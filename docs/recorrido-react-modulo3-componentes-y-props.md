# 🧭 Recorrido React — Módulo 3

## Componentes y props: piezas que cobran vida y se combinan

---

### 📍 Sobre este documento

En el Módulo 2 creaste tu primer componente, pero era estático: siempre decía lo mismo. Acá le damos vida. Vas a aprender a **pasarle información a un componente** para que la misma pieza muestre datos distintos, a **combinar componentes** chicos en grandes, a **renderizar listas** de datos, y a **mostrar u ocultar cosas** según los datos.

Al terminar este módulo vas a poder armar, por ejemplo, una lista de productos de MegaSuper donde cada producto se dibuja con la misma pieza reutilizable, y donde si no hay productos se muestra un mensaje en su lugar. Todo todavía con datos fijos (escritos a mano); que los datos cambien y vengan de un servidor es de los próximos módulos.

**Qué cubre este módulo:**
- **Props**: cómo pasarle datos a un componente.
- El flujo de datos en React (de padre a hijo).
- **Composición**: combinar componentes para armar interfaces grandes.
- **Renderizar listas** con `map` (y por qué React pide una `key`).
- **Renderizado condicional**: mostrar cosas según condiciones (`if`, ternario, `&&`).

**Qué NO cubre (viene después):**
- **Estado** y `useState` (datos que cambian con el tiempo) → Módulo 4.
- **Eventos** (clics, escribir en un input) → Módulo 4.
- Traer datos de un servidor → Módulo 5.

---

### 🎒 De dónde venís

Asumo que entendiste el Módulo 2: un **componente** es una función que devuelve **JSX**, los nombres de componentes van en mayúscula, y dentro del JSX se mete JavaScript con **llaves `{ }`**. También asumo que de JavaScript ya conocés el método **`.map()`** de los arrays y el **operador ternario** (`condición ? a : b`) — los vamos a reutilizar tal cual.

> Recordá del Módulo 1: React es **declarativo**, vos describís cómo se ve la UI para unos datos dados. Las props son la forma en que esos datos entran a un componente.

---
---

## 1. 🔴 El problema: un componente estático no se reutiliza de verdad

En el Módulo 2 hicimos esto:

```javascript
function Bienvenida() {
  return <h1>Bienvenido a MegaSuper</h1>;
}
```

Funciona, pero está **clavado**: siempre dice exactamente lo mismo. Si quisieras saludar a un usuario por su nombre, necesitarías escribir un componente distinto para cada nombre. Absurdo.

Pensalo con productos. Imaginá una "tarjeta de producto" (`ProductCard`) que muestra el nombre y el precio. Si la tarjeta tiene los datos clavados adentro, solo sirve para **un** producto. Pero vos querés **la misma tarjeta** mostrando la manzana, la banana, la naranja... cada una con sus propios datos.

> **Lo que necesitamos:** una forma de definir la pieza **una vez** y pasarle datos distintos cada vez que la usamos. Esa forma se llama **props**.

---

## 2. 🔴 Props: pasarle información a un componente

**Props** (abreviatura de *properties*, propiedades) son los **datos que un componente padre le pasa a un componente hijo.** Son la entrada de información de un componente.

Ya conocés esta idea de dos lados:

- En **JavaScript**, una función recibe **argumentos**: `sumar(2, 3)`. Las props son los argumentos de un componente.
- En **HTML**, una etiqueta recibe **atributos**: `<img src="logo.png" alt="Logo">`. Las props se escriben igual que los atributos.

### Cómo se pasan las props (desde el padre)

Donde usás el componente, le escribís las props como si fueran atributos:

```javascript
<ProductCard nombre="Manzana" precio={500} />
```

Dos detalles de sintaxis importantes:

- Los **textos** (strings) van entre **comillas**: `nombre="Manzana"`.
- **Cualquier otro valor** de JavaScript (números, booleanos, variables, arrays, objetos) va entre **llaves**: `precio={500}`. Las llaves son las mismas del Módulo 2: "acá adentro va JavaScript".

### Cómo se reciben las props (dentro del hijo)

El componente recibe **todas las props juntas en un solo objeto**, que por convención se llama `props`:

```javascript
function ProductCard(props) {
  return (
    <div className="card">
      <h2>{props.nombre}</h2>      {/* accedo con props.nombre */}
      <p>${props.precio}</p>        {/* y props.precio */}
    </div>
  );
}
```

### La forma que vas a ver en todos lados: desestructurar

Escribir `props.nombre`, `props.precio`, `props.loquesea` se vuelve repetitivo. Por eso casi siempre se **desestructura** el objeto `props` directamente en los paréntesis de la función, sacando solo lo que se usa:

```javascript
// ✅ Esta es la forma estándar. Fijate las llaves en el parámetro.
function ProductCard({ nombre, precio }) {
  return (
    <div className="card">
      <h2>{nombre}</h2>       {/* ahora directamente nombre */}
      <p>${precio}</p>         {/* y precio */}
    </div>
  );
}
```

Esto es JavaScript puro (desestructuración de objetos, que quizás ya viste). Hace exactamente lo mismo que `props.nombre`, pero más limpio. **De acá en adelante uso siempre esta forma**, y es la que vas a encontrar en cualquier proyecto.

> 🎓 **Si te lo preguntan — "¿Qué son las props?"**
> Son los datos que un componente padre le pasa a un componente hijo, como los argumentos de una función o los atributos de una etiqueta HTML. Permiten que un mismo componente se reutilice mostrando información distinta. El hijo las recibe como un objeto y suele desestructurarlas.

---

## 3. 🔴 El pago: un componente realmente reutilizable

Ahora que `ProductCard` recibe props, mirá la magia: **la misma pieza, datos distintos.**

```javascript
function App() {
  return (
    <div>
      <ProductCard nombre="Manzana" precio={500} />
      <ProductCard nombre="Banana"  precio={350} />
      <ProductCard nombre="Naranja" precio={420} />
    </div>
  );
}
```

Definiste `ProductCard` **una sola vez** y la usás tres veces, cada una con sus datos. Tres tarjetas distintas en pantalla, cero código duplicado. Eso es reutilización de verdad, y es el corazón de por qué React arma la UI con componentes.

### Una regla de oro: las props son de solo lectura

Esto es importante y define cómo fluyen los datos en React:

> **Un componente NUNCA modifica sus propias props.** Las recibe, las lee, las muestra. Pero no las cambia. Las props son de **solo lectura**.

¿Por qué? Porque en React los datos fluyen en **una sola dirección: de padre a hijo.** El padre decide qué datos le da al hijo; el hijo los usa pero no los toca. Esto se llama **flujo de datos unidireccional**, y es lo que hace que las apps de React sean predecibles: si algo se ve mal en pantalla, sabés que el dato vino de arriba, no que el hijo lo cambió por su cuenta.

```
        PADRE (App)
           │
           │  pasa datos hacia abajo (props)
           ▼
        HIJO (ProductCard)   ← lee las props, NUNCA las modifica
```

Te lo dejo marcado porque más adelante (Módulo 6) vamos a ver qué pasa cuando un hijo **sí** necesita avisarle algo al padre. La respuesta NO va a ser "modificar las props"; va a ser otra cosa. Por ahora, grabate: **datos para abajo, props de solo lectura.**

---

## 4. 🟡 La prop especial `children`: lo que va "adentro"

Hay una prop con nombre reservado: **`children`**. Es lo que escribís **entre las etiquetas de apertura y cierre** de un componente, igual que el contenido de un `<div>` en HTML.

```javascript
// Defino una "caja" genérica que envuelve lo que sea que le metan adentro:
function Caja({ children }) {
  return <div className="caja-con-borde">{children}</div>;
}

// Y la uso poniendo contenido ENTRE las etiquetas:
function App() {
  return (
    <Caja>
      <h2>Oferta del día</h2>     {/* esto llega a Caja como children */}
      <p>Manzanas a $500</p>
    </Caja>
  );
}
```

`children` es lo que hace posible armar componentes "envoltorio" (un marco, una tarjeta, un panel) que no saben de antemano qué van a contener. Es una herramienta clave para la **composición**, que es justo lo que sigue.

---

## 5. 🔴 Composición: combinar componentes para armar la interfaz

**Componer** es construir componentes grandes combinando componentes chicos. En vez de un archivo gigante con todo, armás piezas pequeñas y las anidás como bloques de Lego.

Pensemos la página de MegaSuper. Podríamos tener:

```javascript
function ProductList() {
  return (
    <div className="lista">
      <ProductCard nombre="Manzana" precio={500} />
      <ProductCard nombre="Banana"  precio={350} />
      <ProductCard nombre="Naranja" precio={420} />
    </div>
  );
}

function App() {
  return (
    <div>
      <Bienvenida />
      <ProductList />     {/* App contiene ProductList, que contiene ProductCards */}
    </div>
  );
}
```

Acá `App` **compone** a `Bienvenida` y `ProductList`; y `ProductList` **compone** varios `ProductCard`. Se forma una jerarquía de componentes anidados — un **árbol**:

```
            App
           /   \
   Bienvenida   ProductList
                 /   |   \
          Product  Product  Product
           Card     Card     Card
```

> Recordá del Módulo 1: React arma un Virtual DOM a partir de todo esto. Ese árbol de componentes es, justamente, lo que React recorre para construir la interfaz. Componer componentes = armar ese árbol.

Cada componente es independiente, reutilizable y fácil de entender por separado. Si mañana querés cambiar cómo se ve una tarjeta, tocás solo `ProductCard` y todas las tarjetas de toda la app se actualizan. Eso es el poder de componer.

---

## 6. 🔴 Renderizar listas con `map`

En el ejemplo anterior escribí los tres `<ProductCard>` a mano. Pero en la realidad los productos vienen en un **array de datos**, y pueden ser 3 o 300. No vas a escribir 300 tarjetas a mano. Acá entra una herramienta que **ya conocés de JavaScript**: `.map()`.

Recordá qué hace `.map()` en JS puro: recorre un array y **transforma cada elemento** en otra cosa, devolviendo un array nuevo.

```javascript
const numeros = [1, 2, 3];
const dobles = numeros.map((n) => n * 2);   // [2, 4, 6]
```

En React usamos exactamente eso, pero transformamos **cada dato en un componente**. Primero, los datos (un array de objetos JavaScript comunes):

```javascript
// Cada producto es un objeto. Fijate que cada uno tiene un "id" único.
const productos = [
  { id: 1, nombre: "Manzana", precio: 500 },
  { id: 2, nombre: "Banana",  precio: 350 },
  { id: 3, nombre: "Naranja", precio: 420 },
];
```

Ahora, dentro del JSX, mapeamos cada producto a una `<ProductCard>`:

```javascript
function ProductList({ productos }) {
  return (
    <div className="lista">
      {productos.map((producto) => (
        <ProductCard
          key={producto.id}                 // 👈 la "key" (lo explico abajo)
          nombre={producto.nombre}
          precio={producto.precio}
        />
      ))}
    </div>
  );
}
```

Tres cosas para entender bien:

1. El `.map()` va **entre llaves `{ }}`** porque es JavaScript dentro del JSX.
2. Por cada `producto` del array, devolvemos una `<ProductCard>` con las props sacadas de ese producto. Si el array tiene 300 productos, se generan 300 tarjetas. Si tiene 3, tres. **Cero tarjetas escritas a mano.**
3. Aparece una prop nueva, **`key`**, que no estaba en `ProductCard`. Esa es especial.

### Por qué React pide una `key`

Cuando renderizás una lista, React necesita **identificar cada elemento de forma única**, para saber cuál es cuál cuando la lista cambia (se agrega, se borra o se reordena un producto). La `key` es ese identificador único.

> Recordá del Módulo 1 el proceso de **reconciliación**: React compara el Virtual DOM nuevo con el viejo para tocar solo lo que cambió. En una lista, las `key` son las que le permiten a React decir "este producto es el mismo de antes, no lo redibujo; este otro es nuevo, lo agrego". Sin `key`, React no puede emparejar elementos entre un render y el siguiente, y termina haciendo trabajo de más o cometiendo errores visuales.

La `key` tiene que ser **única y estable** entre los elementos de la lista. Lo ideal es un identificador propio del dato, como el `id` que viene de la base de datos (`key={producto.id}`).

> ⚠️ **Trampa común:** usar la **posición** del elemento como key (`key={index}`) parece funcionar pero da problemas cuando la lista se reordena o se borran elementos del medio, porque las posiciones se corren. Usá un `id` real siempre que puedas.

> 🎓 **Si te lo preguntan — "¿Por qué React pide una `key` al renderizar listas?"**
> Para identificar de forma única cada elemento de la lista entre un render y el siguiente. Durante la reconciliación, las keys le permiten a React saber qué elementos se mantuvieron, cuáles se agregaron y cuáles se eliminaron, y así actualizar el DOM de forma eficiente y correcta. La key debe ser única y estable, idealmente un id propio del dato.

---

## 7. 🔴 Renderizado condicional: mostrar cosas según los datos

Muchas veces querés mostrar algo **solo si** se cumple una condición: un mensaje de "no hay productos" si la lista está vacía, un cartel de "sin stock" si el producto se agotó, etc. A esto se le llama **renderizado condicional**, y hay tres formas, cada una buena para un caso distinto.

### Forma A — `if` para decisiones grandes (al principio del componente)

Cuando la decisión afecta a **todo lo que devuelve** el componente, usás un `if` arriba de todo y devolvés JSX distinto en cada caso. Esto se llama *early return* (retorno temprano):

```javascript
function ProductList({ productos }) {
  // Si no hay productos, devuelvo otra cosa y corto acá.
  if (productos.length === 0) {
    return <p>No hay productos disponibles.</p>;
  }

  // Si llegué hasta acá, es porque sí hay productos: devuelvo la lista.
  return (
    <div className="lista">
      {productos.map((producto) => (
        <ProductCard key={producto.id} nombre={producto.nombre} precio={producto.precio} />
      ))}
    </div>
  );
}
```

Usá `if` cuando el componente entero se ve distinto según la condición.

### Forma B — ternario `? :` para elegir entre dos cosas (dentro del JSX)

Cuando dentro del marcado querés mostrar **una cosa u otra**, usás el operador ternario (el que ya conocés de JS) entre llaves:

```javascript
function ProductCard({ nombre, precio, stock }) {
  return (
    <div className="card">
      <h2>{nombre}</h2>
      <p>${precio}</p>
      {/* Si hay stock muestro un texto; si no, otro */}
      {stock > 0 ? <span>En stock</span> : <span>Sin stock</span>}
    </div>
  );
}
```

Usá el ternario para "esto **o** aquello" en un pedacito de la UI.

### Forma C — `&&` para mostrar algo o nada (dentro del JSX)

Cuando querés mostrar algo **solo si** se cumple la condición, y **nada** si no, usás el operador `&&`:

```javascript
function ProductCard({ nombre, precio, stock }) {
  return (
    <div className="card">
      <h2>{nombre}</h2>
      <p>${precio}</p>
      {/* Si stock es 0, muestro el cartel. Si no, no aparece nada. */}
      {stock === 0 && <span className="sin-stock">¡Agotado!</span>}
    </div>
  );
}
```

Cómo funciona: en JavaScript, `condición && valor` devuelve el `valor` solo si la condición es verdadera; si es falsa, se corta y no devuelve nada visible. Usá `&&` para "mostrar **si**; si no, nada".

> ⚠️ **Trampa clásica con `&&`:** si la condición de la izquierda es el número `0`, React **dibuja el 0** en pantalla en vez de "nada". Por ejemplo `{productos.length && <Lista/>}` muestra un `0` cuando la lista está vacía. Por eso, con números, es más seguro comparar explícitamente: `{productos.length > 0 && <Lista/>}`. Te lo dejo marcado porque le pasa a todo el mundo al menos una vez.

### Resumen de cuál usar

| Situación | Herramienta |
|---|---|
| El componente entero cambia según la condición | `if` + early return (Forma A) |
| Mostrar una cosa **u** otra en un pedacito | ternario `? :` (Forma B) |
| Mostrar algo **o nada** | `&&` (Forma C) |

---

## 8. 🟢 Cómo encaja todo junto

Juntemos las cuatro herramientas del módulo en un solo ejemplo de MegaSuper. Esto combina props, composición, `map` con `key`, y los tres tipos de renderizado condicional:

```javascript
// Datos (fijos por ahora; en el Módulo 5 vendrán de un servidor)
const productos = [
  { id: 1, nombre: "Manzana", precio: 500, stock: 12 },
  { id: 2, nombre: "Banana",  precio: 350, stock: 0  },
  { id: 3, nombre: "Naranja", precio: 420, stock: 8  },
];

// Pieza chica reutilizable: recibe props, no las modifica
function ProductCard({ nombre, precio, stock }) {
  return (
    <div className="card">
      <h2>{nombre}</h2>
      <p>${precio}</p>
      {/* condicional con && : el cartel aparece solo si está agotado */}
      {stock === 0 && <span className="sin-stock">¡Agotado!</span>}
    </div>
  );
}

// Pieza grande: compone muchas ProductCard mapeando el array
function ProductList({ productos }) {
  // condicional con if : si está vacío, corto acá
  if (productos.length === 0) {
    return <p>No hay productos disponibles.</p>;
  }
  return (
    <div className="lista">
      {productos.map((producto) => (
        <ProductCard
          key={producto.id}
          nombre={producto.nombre}
          precio={producto.precio}
          stock={producto.stock}
        />
      ))}
    </div>
  );
}

// La raíz compone todo
function App() {
  return (
    <div>
      <h1>MegaSuper</h1>
      <ProductList productos={productos} />
    </div>
  );
}
```

Leelo de arriba a abajo y fijate cómo cada concepto del módulo está ahí: `App` **compone** `ProductList`, que recibe los productos por **props**, **mapea** el array a `ProductCard` con su **key**, maneja el caso vacío con **`if`**, y cada `ProductCard` muestra el cartel de agotado con **`&&`**. Esto es, en miniatura, exactamente cómo se arma una pantalla real de productos.

> 🕳️ **Madriguera — pasar el objeto entero como una sola prop**
> En vez de pasar `nombre`, `precio` y `stock` por separado, podrías pasar el objeto completo: `<ProductCard producto={producto} />` y adentro recibir `function ProductCard({ producto })`. Es más corto y muy común. Lo dejé separado acá para que se vea claro qué es cada prop.
> *Volvé al camino — las dos formas son válidas; elegís según el caso.*

> El último párrafo del Módulo 1 hablaba de "la UI que refleja datos". Bueno: este ejemplo **es** una UI que refleja un array de datos. Lo único que todavía le falta es que esos datos **cambien** (que agregues un producto, que se agote uno) y que la pantalla reaccione sola. Eso es el estado, y es el próximo módulo.

---
---

## ✅ Checkpoint — ¿lo agarraste?

Sin mirar, con tus palabras (las respuestas se resuelven en el complemento del recorrido, o me preguntás):

1. ¿Qué problema resuelven las **props**? Dalo con el ejemplo de la tarjeta de producto.
2. ¿Cómo se **pasa** una prop desde el padre? ¿Cómo se **recibe** en el hijo? ¿Qué es desestructurar las props?
3. Al pasar una prop, ¿cuándo usás comillas y cuándo usás llaves `{ }`?
4. ¿Qué quiere decir que las props son de **solo lectura**? ¿En qué dirección fluyen los datos en React?
5. ¿Qué es la prop **`children`** y para qué sirve?
6. ¿Qué es **componer** componentes? Dibujá (mental o en papel) el árbol de `App` → `ProductList` → `ProductCard`.
7. ¿Cómo renderizás una **lista** de datos en React? ¿Qué método de JavaScript usás?
8. ¿Qué es la **`key`** y por qué React la pide? Conectalo con la reconciliación del Módulo 1.
9. ¿Por qué usar el **índice** como key puede traer problemas?
10. Nombrá las **tres formas** de renderizado condicional y en qué situación conviene cada una. ¿Cuál es la trampa del `&&` con números?

---

## 👉 Qué viene en el Módulo 4

Hasta acá todo lo que mostramos es **estático**: los datos están escritos a mano y nunca cambian. En el Módulo 4 —el corazón de React— las cosas empiezan a **moverse**:

- **Estado**: qué es y por qué es distinto de las props.
- **`useState`**: el hook para que un componente "recuerde" datos que cambian.
- El **ciclo de render**: cómo React redibuja cuando el estado cambia (acá se cierra el círculo con el Virtual DOM del Módulo 1).
- **Eventos**: responder a clics y a lo que el usuario escribe.
- **Inputs controlados**: que un campo de texto y tu estado estén siempre sincronizados.

Es el módulo más importante de todos, y todo lo que viste hasta acá es la base que lo hace posible. Te espero en el Módulo 4.

---

**FIN DEL MÓDULO 3**
