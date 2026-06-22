# 🧭 Recorrido React — Módulo 6

## Compartir estado entre componentes (lifting state up)

---

### 📍 Sobre este documento

Hasta acá, cuando un estado lo necesitaba todo un componente, lo poníamos adentro de ese componente y listo. Pero, ¿qué pasa cuando **dos componentes separados** necesitan el **mismo** dato? Ahí aparece uno de los patrones más importantes de React: **elevar el estado** (*lifting state up*). De paso, vas a aprender cómo un componente hijo le **avisa algo a su padre** — la pregunta que dejé abierta en el Módulo 3.

Este módulo es **conceptualmente más liviano** que los dos anteriores (no hay un mecanismo nuevo gigante como el estado o los efectos), pero el patrón que enseña es la base de cómo se organizan los datos en cualquier app de React. Vale la pena que quede claro.

**Qué cubre este módulo:**
- Por qué el estado es **privado** de cada componente.
- **Lifting state up**: mover el estado al padre común.
- Datos para **abajo** (props) y eventos para **arriba** (callbacks).
- Cómo un **hijo le comunica algo al padre** sin tocar las props.
- Cuándo conviene elevar el estado y cuándo no.

**Qué NO cubre (viene después):**
- Tener varias páginas y navegar entre ellas (**routing**) → Módulo 7.
- Estado que necesitan **muchísimos** componentes lejanos (**Context**) → Módulo 8.

---

### 🎒 De dónde venís

Asumo los Módulos 1 a 5. Para este módulo es clave tener fresco:
- Del **Módulo 3**: las **props** fluyen de **padre a hijo** y son de **solo lectura**. Y la pregunta que dejé abierta: *"¿qué pasa cuando un hijo necesita avisarle algo al padre? La respuesta NO es modificar las props"*.
- Del **Módulo 4**: el **estado** (`useState`), los **eventos**, y el **input controlado** (`value` + `onChange`). El ejemplo del buscador que filtraba la lista — **todo en un solo componente**.

> Recordá cómo cerró el Módulo 4: armamos un buscador que filtraba productos, y aclaré que funcionaba porque el buscador y la lista estaban **en el mismo componente**, compartiendo el estado `texto`. Y dije: "cuando quieras que sean componentes **separados**, vas a necesitar *lifting state up*". Llegó el momento.

---
---

## 1. 🔴 El problema: el estado es privado

Acá está la traba. **El estado de un componente es privado: solo ese componente lo ve.** Dos componentes distintos, aunque sean hermanos, no pueden ver el estado del otro.

Volvamos al buscador del Módulo 4, pero ahora separado en dos componentes, como sería en una app real:

- Un `SearchBar` (la barra de búsqueda).
- Un `ProductList` (la lista de productos).

El problema: el texto que el usuario escribe lo maneja `SearchBar`, pero quien necesita ese texto para **filtrar** es `ProductList`. Si cada uno tiene su propio estado, no hay forma de que se vean:

```javascript
function SearchBar() {
  const [texto, setTexto] = useState("");   // este texto vive ACÁ, encerrado
  // ...
}

function ProductList() {
  // ...pero ProductList no tiene forma de ver el 'texto' de SearchBar. ❌
}
```

`SearchBar` sabe lo que se escribió, pero `ProductList` no puede acceder a eso. El estado está **atrapado** dentro de `SearchBar`. Necesitamos que ese dato sea **compartido**. ¿Cómo?

---

## 2. 🔴 La idea: elevar el estado (lifting state up)

La solución tiene un nombre y es elegante:

> **Lifting state up**: cuando dos componentes necesitan compartir un estado, ese estado se **mueve hacia arriba**, al **componente padre común más cercano** de ambos. El padre se vuelve el **dueño** del estado.

En vez de que `SearchBar` o `ProductList` tengan el estado, lo tiene el **padre** que los contiene a los dos (llamémoslo `Home`). El padre es ahora la **única fuente de verdad** de ese dato, y se lo reparte a sus hijos.

Visualmente, el cambio es este:

```
   ❌ ANTES: el estado atrapado en un hijo
        Home
        /   \
   SearchBar  ProductList
   [texto]      (no lo ve)


   ✅ DESPUÉS: el estado elevado al padre común
        Home  [texto]   ← el padre es el dueño
        /   \
       ▼     ▼   (les reparte lo que cada uno necesita)
   SearchBar  ProductList
```

Una vez que el estado vive en el padre, el padre puede pasárselo a **los dos** hijos. Pero acá hay dos direcciones distintas que hay que entender por separado: los datos bajan, y los eventos suben. Vamos una por una.

---

## 3. 🔴 Datos para abajo: el padre pasa el estado por props

Esta dirección ya la conocés del Módulo 3: el padre le pasa datos a los hijos por **props**. Como ahora el estado vive en `Home`, `Home` se lo pasa a quien lo necesite:

```javascript
function Home() {
  const [texto, setTexto] = useState("");   // el estado vive en el padre

  const productosFiltrados = productos.filter((p) =>
    p.nombre.toLowerCase().includes(texto.toLowerCase())
  );

  return (
    <div>
      {/* le paso el texto a la barra para que lo muestre */}
      <SearchBar texto={texto} ... />

      {/* le paso la lista ya filtrada a ProductList */}
      <ProductList productos={productosFiltrados} />
    </div>
  );
}
```

`ProductList` recibe los productos ya filtrados por props y simplemente los muestra — no sabe nada del buscador, solo recibe una lista. Esto es coherente con todo lo del Módulo 3: **los datos fluyen hacia abajo.** Hasta acá, nada nuevo.

Lo nuevo es la otra dirección: ¿cómo hace `SearchBar` para **cambiar** el `texto`, si el `texto` ahora vive en el padre y las props son de solo lectura?

---

## 4. 🔴 Eventos para arriba: la comunicación con callbacks

Acá respondemos la pregunta que quedó abierta desde el Módulo 3: **¿cómo le avisa un hijo algo a su padre?**

La respuesta: **el padre le pasa al hijo una _función_ como prop. El hijo, cuando pasa algo, _llama_ a esa función.** Esa función se ejecuta en el padre y actualiza el estado del padre. El hijo nunca toca el estado ni las props directamente: solo **invoca la función que le dieron.**

A esa función pasada como prop se la llama **callback** (devolución de llamada). Mirá:

```javascript
function Home() {
  const [texto, setTexto] = useState("");

  return (
    <div>
      {/* le paso a SearchBar la función para cambiar el texto */}
      <SearchBar texto={texto} onBuscar={setTexto} />
      {/* ... */}
    </div>
  );
}

function SearchBar({ texto, onBuscar }) {
  return (
    <input
      value={texto}                                // dato que baja (Módulo 4)
      onChange={(e) => onBuscar(e.target.value)}   // ¡llama a la función del padre!
    />
  );
}
```

Seguí el viaje de una letra que el usuario teclea:

```
   Usuario escribe en el input (que vive en SearchBar)
         │
         ▼
   onChange dispara → onBuscar(e.target.value)     ← el hijo LLAMA la función...
         │
         ▼
   ...que en realidad es setTexto del padre        ← ...y se ejecuta en el PADRE
         │
         ▼
   Cambia el estado 'texto' en Home → Home re-renderiza
         │
         ▼
   Home recalcula productosFiltrados y se los pasa a ProductList
         │
         ▼
   La lista se actualiza. SearchBar muestra el texto nuevo (value={texto}).
```

La clave para tu cabeza: **`onBuscar` es `setTexto` viajando hacia abajo como prop.** El hijo no sabe qué hace esa función ni le importa; solo la llama con el dato nuevo. El padre, que es el dueño, decide qué pasa. El hijo "le avisa" al padre llamando a la función que el padre le confió.

> **El patrón completo, en una frase:** los **datos** fluyen hacia **abajo** (props), y los **eventos** fluyen hacia **arriba** (callbacks). El padre manda datos a los hijos y los hijos le avisan al padre llamando a funciones que el padre les dio. Esto es el **flujo de datos unidireccional** del Módulo 3, ahora completo en sus dos sentidos.

> 🎓 **Si te lo preguntan — "¿Cómo se comunica un hijo con su padre en React?"**
> El padre le pasa una función como prop (un callback). El hijo, cuando ocurre un evento, llama a esa función pasándole el dato. La función se ejecuta en el padre y actualiza su estado. El hijo nunca modifica las props ni el estado del padre directamente: solo invoca la función que recibió. Así los datos bajan por props y los eventos suben por callbacks.

### Convención de nombres

Las props que son callbacks suelen nombrarse con **`on`** adelante (`onBuscar`, `onAgregar`, `onClick`), igual que los eventos. Y la función que las maneja en el padre suele llamarse con **`manejar`** o `handle` adelante (`manejarBusqueda`, `handleAgregar`). No es obligatorio, pero es lo que vas a ver en todos lados.

---

## 5. 🔴 El ejemplo completo

Juntemos todo y resolvamos de una vez el cabo suelto del Módulo 4: el buscador y la lista como **componentes separados** que comparten estado. Tres componentes, cada uno con un rol claro:

```javascript
import { useState } from 'react';

const productos = [
  { id: 1, nombre: "Manzana" },
  { id: 2, nombre: "Banana"  },
  { id: 3, nombre: "Naranja" },
];

// HIJO 1 — la barra de búsqueda. No tiene estado propio.
function SearchBar({ texto, onBuscar }) {
  return (
    <input
      value={texto}
      onChange={(e) => onBuscar(e.target.value)}
      placeholder="Buscar..."
    />
  );
}

// HIJO 2 — la lista. Solo recibe productos y los muestra.
function ProductList({ productos }) {
  if (productos.length === 0) {
    return <p>No se encontraron productos.</p>;
  }
  return (
    <ul>
      {productos.map((p) => <li key={p.id}>{p.nombre}</li>)}
    </ul>
  );
}

// PADRE — dueño del estado. Orquesta a los dos hijos.
function Home() {
  const [texto, setTexto] = useState("");          // 👈 el estado vive ACÁ

  const filtrados = productos.filter((p) =>
    p.nombre.toLowerCase().includes(texto.toLowerCase())
  );

  return (
    <div>
      <SearchBar texto={texto} onBuscar={setTexto} />  {/* dato abajo + callback */}
      <ProductList productos={filtrados} />            {/* dato abajo */}
    </div>
  );
}
```

Mirá lo que conseguimos. `SearchBar` y `ProductList` son **independientes y reutilizables** (ninguno sabe del otro), pero comparten el dato porque el padre `Home` es el dueño del estado y lo reparte. `SearchBar` le avisa a `Home` cuando se escribe algo (callback), `Home` recalcula la lista, y `ProductList` recibe el resultado. Cada pieza hace una sola cosa y la hace bien.

Comparalo con el Módulo 4: ahí todo estaba apelotonado en un componente. Ahora está repartido en piezas limpias. Eso es lo que te permite *lifting state up*: **separar la UI en componentes chicos sin perder la capacidad de compartir datos.**

---

## 6. 🟡 Cuándo elevar el estado (y cuándo no)

Una advertencia importante para no pasarte de rosca: **no eleves todo el estado al componente más alto "por las dudas".** Elevar tiene un costo, y la regla es:

> Poné cada estado **lo más abajo posible** (en el componente que de verdad lo usa). Elevalo **solo** cuando dos o más componentes necesitan compartirlo, y solo hasta el **padre común más cercano** — ni un nivel más arriba.

¿Por qué importa? Porque si elevás un estado muy arriba y los componentes que lo necesitan están muy abajo, tenés que ir **pasando la prop por todos los componentes intermedios**, uno por uno, aunque esos del medio no la usen. Eso se vuelve tedioso y se llama **"prop drilling"** (perforación de props).

El ejemplo clásico en MegaSuper es el **carrito**: el estado del carrito lo necesitan la barra de navegación (para el contador), la página de producto (para agregar), el checkout (para listar)... componentes que están en lugares muy distintos y lejanos del árbol. Si lo elevás hasta arriba de todo y lo vas pasando por props hasta cada rincón, el prop drilling se vuelve un dolor.

> Cuando el estado lo necesitan **muchísimos** componentes lejanos y el prop drilling se hace insoportable, hay una herramienta mejor que elevar-y-perforar: el **Context**. Es exactamente el tema del Módulo 8. Por ahora quedate con: *lifting state up* es la herramienta para compartir entre **pocos** componentes cercanos; cuando son muchos y lejanos, te va a faltar algo más, y ya sabés cómo se llama.

> 🎓 **Si te lo preguntan — "¿Qué es lifting state up?"**
> Es mover un estado compartido al componente padre común más cercano de los componentes que lo necesitan. El padre pasa a manejar ese estado como única fuente de verdad: distribuye los datos a los hijos por props y recibe los cambios mediante callbacks. Se usa cuando dos o más componentes necesitan compartir el mismo dato.

---

## 7. 🟢 Cómo encaja todo junto

La foto del módulo, que en realidad es la foto de **cómo fluyen los datos en toda app de React**:

```
                    ┌─────────────────────────┐
                    │   PADRE (dueño del       │
                    │   estado compartido)     │
                    └───────────┬─────────────┘
            datos ↓ (props)     │     ↑ eventos (callbacks)
            ┌──────────────────┐│┌──────────────────┐
            ▼                  ││▼                   
     ┌────────────┐      el padre reparte      ┌────────────┐
     │   HIJO A   │      datos y recibe        │   HIJO B   │
     │ (muestra)  │      avisos                │ (avisa)    │
     └────────────┘                            └────────────┘
```

En una frase para llevar: **el estado vive en el padre común; los datos bajan por props; los eventos suben por callbacks.** Cuando entendés esto, entendés cómo está organizada cualquier app de React por dentro. Todo lo demás (routing, context, lo que venga) se monta sobre esta misma idea.

> 🕳️ **Madriguera — librerías de manejo de estado global**
> Cuando una app crece mucho y compartir estado se vuelve complejo, existen librerías dedicadas (las más conocidas son **Redux** y **Zustand**) que centralizan el estado de toda la app en un lugar. Son una alternativa más potente que combinar lifting + context, para casos grandes.
> *Volvé al camino — con lifting state up (este módulo) y context (Módulo 8) cubrís lo que necesitás.*

---
---

## ✅ Checkpoint — ¿lo agarraste?

Sin mirar, con tus palabras (las respuestas se resuelven en el complemento del recorrido, o me preguntás):

1. ¿Por qué se dice que el estado de un componente es **privado**? ¿Qué problema causa eso cuando dos componentes hermanos necesitan el mismo dato?
2. ¿Qué es **lifting state up**? ¿A dónde se mueve el estado?
3. Una vez elevado el estado, ¿cómo le llega el dato a los hijos? (¿en qué dirección y con qué herramienta?)
4. La pregunta del Módulo 3: ¿cómo hace un hijo para **avisarle algo al padre**? ¿Qué es un **callback**?
5. En `<SearchBar texto={texto} onBuscar={setTexto} />`, ¿qué es `onBuscar` realmente? ¿Quién la ejecuta y dónde corre?
6. ¿Por qué el hijo **no** rompe la regla de "las props son de solo lectura" al llamar a `onBuscar`?
7. Completá la frase: "los datos fluyen hacia ____ por ____, y los eventos fluyen hacia ____ por ____".
8. ¿Cuál es la regla sobre **dónde** poner cada estado? ¿Por qué no conviene elevar todo al tope?
9. ¿Qué es el **prop drilling** y cuándo aparece?
10. En el ejemplo del carrito de MegaSuper, ¿por qué elevar el estado hasta arriba y pasarlo por props se vuelve incómodo? ¿Qué herramienta lo va a resolver?

---

## 👉 Qué viene en el Módulo 7

Ya sabés compartir estado entre componentes. Pero toda tu app sigue viviendo en **una sola pantalla**. En el Módulo 7 le damos **varias páginas**:

- **Routing**: instalar la herramienta de navegación y entender por qué React la necesita.
- Definir **rutas** y navegar entre pantallas (inicio, detalle, checkout) **sin recargar** la página.
- **Links** para moverse, y un **layout** común (barra de navegación que se mantiene en todas las pantallas).
- Leer **parámetros de la URL** para mostrar la página de detalle del producto correcto.
- El **formulario de checkout** (aplicando los inputs controlados del Módulo 4 a un formulario con varios campos).

Ahí MegaSuper deja de ser una pantalla y se vuelve una aplicación de verdad, con su navegación. Te espero en el Módulo 7.

---

**FIN DEL MÓDULO 6**
