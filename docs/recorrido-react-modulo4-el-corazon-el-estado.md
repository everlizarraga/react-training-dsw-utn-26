# 🧭 Recorrido React — Módulo 4

## El corazón: el estado

---

### 📍 Sobre este documento

Este es **el módulo más importante de todo el recorrido.** Hasta acá todo lo que mostramos era estático: datos escritos a mano que nunca cambian. Acá las cosas empiezan a **moverse**. Vas a aprender qué es el **estado**, cómo se usa con **`useState`**, qué pasa exactamente cuando el estado cambia (acá se cierra el círculo con el Virtual DOM del Módulo 1), cómo responder a **eventos** (clics, tecleo) y cómo conectar un **input** con tu estado.

Te lo digo derecho: si algo de este recorrido vale la pena leer dos veces, es esto. No porque sea difícil, sino porque es **el** concepto que hace que React sea React. Vamos despacio y con ejemplos mínimos. Si una sección no cierra, frená y la masticamos antes de seguir.

**Qué cubre este módulo:**
- Qué es el **estado** y en qué se diferencia de las props.
- Por qué una variable normal **no** alcanza.
- **`useState`**: la herramienta para tener estado.
- El **ciclo de render**: qué pasa cuando el estado cambia.
- **Eventos**: responder a clics y a lo que el usuario escribe.
- **Inputs controlados**: mantener un campo de texto sincronizado con el estado.
- **Las reglas de los hooks**.

**Qué NO cubre (viene después):**
- Traer datos de un servidor y `useEffect` → Módulo 5.
- Compartir estado entre varios componentes (*lifting state up*) → Módulo 6.

---

### 🎒 De dónde venís

Asumo los Módulos 1, 2 y 3. En particular necesitás tener fresco:
- Del **Módulo 1**: el Virtual DOM y la **reconciliación** (React compara el árbol nuevo con el viejo y toca solo lo que cambió). Lo vamos a reusar fuerte.
- Del **Módulo 3**: las **props** (datos que vienen del padre, de **solo lectura**) y que el componente es una **función que se ejecuta** para producir JSX.

> Recordá cómo cerró el Módulo 3: teníamos una lista de productos en pantalla, pero los datos estaban clavados. Dijimos: "lo único que le falta es que los datos **cambien** y que la pantalla reaccione sola". Eso es lo que resolvemos ahora.

---
---

## 1. 🔴 El problema: datos que cambian

Pensá en cosas que en una app cambian mientras el usuario la usa:

- El **contador del carrito** que sube cuando agregás un producto.
- Lo que el usuario **escribe** en un buscador.
- Si un panel está **abierto o cerrado**.
- La **cantidad** de un producto que elegís antes de agregarlo.

Todos estos son datos que **arrancan con un valor y cambian con el tiempo**, y cada vez que cambian, **la pantalla tiene que reflejar el valor nuevo.**

¿Podemos usar **props** para esto? No. Recordá del Módulo 3: las props vienen del padre y son de **solo lectura** — un componente no puede cambiar sus propias props. Las props sirven para datos que **entran desde afuera**, no para datos que el componente **maneja y modifica por dentro**.

Necesitamos algo nuevo: el **estado**.

> **El estado (o *state*) son los datos internos de un componente que pueden cambiar con el tiempo, y que cuando cambian, hacen que el componente se vuelva a dibujar en pantalla.**

La diferencia con las props, lado a lado:

| | **Props** | **Estado (state)** |
|---|---|---|
| ¿De dónde viene? | Del componente **padre** | Es **interno** del componente |
| ¿Se puede cambiar? | **No** (solo lectura) | **Sí**, con su herramienta |
| ¿Quién lo controla? | El padre | El **propio** componente |
| Al cambiar, ¿redibuja? | (las props cambian si el padre cambia) | **Sí**, cambiarlo redibuja |

> 🎓 **Si te lo preguntan — "¿Qué es el estado y en qué se diferencia de las props?"**
> El estado son los datos internos de un componente que pueden cambiar con el tiempo; cuando cambian, el componente se vuelve a renderizar. Las props vienen de afuera (del padre) y son de solo lectura. La diferencia clave: las props las controla el padre y el componente no las modifica; el estado lo controla y modifica el propio componente.

---

## 2. 🔴 Por qué una variable normal no alcanza (el "aha")

Acá está el malentendido que hace tropezar a casi todos. Es tentador pensar: *"si quiero un contador, uso una variable común y la voy sumando".* Probémoslo y veamos por qué **no funciona**:

```javascript
function Contador() {
  let count = 0;   // una variable normal

  return (
    <button onClick={() => count++}>
      Clics: {count}
    </button>
  );
}
```

Hacés clic en el botón... y **el número en pantalla nunca cambia.** Sigue en 0 para siempre. ¿Por qué? Por **dos razones**, y entender ambas es entender React:

**Razón 1 — React no se entera.** Cuando hacés `count++`, cambiás la variable en memoria, pero **React no tiene forma de saber que algo cambió**, así que no vuelve a dibujar nada. Recordá del Módulo 1: React es declarativo, redibuja cuando cambian los datos. Pero una variable común no le avisa a React. Es un cambio "a escondidas" que la pantalla nunca ve.

**Razón 2 — la variable se reinicia.** Recordá del Módulo 3 que un componente es una **función que se ejecuta**. Cada vez que React dibuja el componente, **ejecuta la función de nuevo de arriba a abajo**. Y cada vez que la ejecuta, vuelve a correr `let count = 0`, **reseteando la variable a 0**. Una variable común no "sobrevive" entre dibujados.

Entonces necesitamos una herramienta que haga **dos cosas** que una variable normal no puede:

1. **Recordar** el valor entre un dibujado y el siguiente (que no se reinicie).
2. **Avisarle a React** cuando el valor cambia, para que vuelva a dibujar.

Esa herramienta es **`useState`**.

> 🎓 **Si te lo preguntan — "¿Por qué no podés usar una variable normal en lugar de `useState`?"**
> Por dos razones: una variable normal no le avisa a React cuando cambia, así que la pantalla no se actualiza; y como el componente es una función que se ejecuta en cada render, la variable se reinicia a su valor inicial cada vez. `useState` resuelve ambas: conserva el valor entre renders y dispara un re-render cuando cambia.

---

## 3. 🔴 `useState`: la herramienta

`useState` es una función que React te da para agregarle estado a un componente. (Es un **hook**; qué significa eso lo vemos en la sección 6, por ahora usémoslo.)

Primero hay que **importarlo** desde React:

```javascript
import { useState } from 'react';
```

Y se usa así:

```javascript
const [count, setCount] = useState(0);
```

Esa línea es densa. Vamos parte por parte:

- **`useState(0)`** → crea una pieza de estado con **valor inicial `0`**. Ese `0` es el valor que tiene la primera vez.
- `useState` **devuelve un array de dos elementos**, y nosotros lo **desestructuramos** (esto es JS común, lo viste con las props):
  - **`count`** → el **valor actual** del estado. Lo leés para mostrarlo.
  - **`setCount`** → una **función para cambiar** ese estado. Es la única forma válida de cambiarlo.
- La **convención de nombres** es `[cosa, setCosa]`: el valor con su nombre, y el setter con `set` adelante. `[count, setCount]`, `[nombre, setNombre]`, `[abierto, setAbierto]`.

Ahora sí, el contador que **funciona**:

```javascript
import { useState } from 'react';

function Contador() {
  const [count, setCount] = useState(0);   // estado, arranca en 0

  return (
    <button onClick={() => setCount(count + 1)}>
      Clics: {count}
    </button>
  );
}
```

Hacés clic → el número sube → **y la pantalla se actualiza.** La diferencia con la versión rota es una sola línea: en vez de `count++` usamos **`setCount(count + 1)`**. Ese setter es el que le avisa a React.

### Las dos reglas de oro del estado

```javascript
// ✅ LEER: usás la variable directamente
<p>{count}</p>

// ✅ CAMBIAR: SIEMPRE con el setter
setCount(count + 1);

// ❌ NUNCA cambiar la variable a mano. Esto NO funciona:
count = count + 1;    // React no se entera → no redibuja
count++;              // lo mismo
```

Grabate esto: **el valor se lee con la variable, pero solo se cambia con el setter.** El setter es lo que dispara todo el mecanismo de actualización. Es justo lo que vemos ahora.

---

## 4. 🔴 El ciclo de render: qué pasa cuando llamás al setter

Acá se cierra el círculo del Módulo 1. Esto es lo que pasa, paso a paso, cuando hacés `setCount(count + 1)`:

```
   Usuario hace clic
         │
         ▼
   setCount(count + 1)          ← le pedís a React que cambie el estado
         │
         ▼
   React vuelve a EJECUTAR la función del componente   ← esto es "renderizar"
         │                       (ahora useState te devuelve el valor NUEVO)
         ▼
   La función produce JSX nuevo  →  React arma un Virtual DOM nuevo
         │
         ▼
   RECONCILIACIÓN: compara el Virtual DOM nuevo con el anterior
         │           (exactamente lo del Módulo 1)
         ▼
   Actualiza en el DOM real SOLO lo que cambió (el número)
         │
         ▼
   Ves el número nuevo en pantalla
```

Dos ideas que tenés que sacar de acá:

**"Renderizar" = React ejecuta la función de tu componente** para obtener la descripción actual de la UI. No es magia: es literalmente volver a correr tu función. Cada cambio de estado dispara un nuevo render (una nueva ejecución).

**El estado sobrevive a los renders, las variables no.** Tu función se ejecuta muchísimas veces (una por render). En cada ejecución, `useState` te devuelve **el valor más reciente** del estado, que React guarda **por fuera** de tu función. Por eso `count` no se reinicia: no vive dentro de la función, vive en React. Eso es exactamente lo que la variable común (sección 2) no podía hacer.

> 🎓 **Si te lo preguntan — "¿Qué pasa cuando llamás a un setter de estado?"**
> React marca el componente para re-renderizar: vuelve a ejecutar su función, que produce un JSX nuevo con el valor actualizado. Con eso arma un nuevo Virtual DOM, lo compara con el anterior (reconciliación) y aplica al DOM real solo los cambios necesarios. El resultado es que la pantalla refleja el nuevo estado.

---

## 5. 🟡 Dos sutilezas del estado (que evitan bugs)

Estas dos cosas no son urgentes para tu primer contador, pero te van a ahorrar dolores de cabeza. Si en una primera lectura te marean, anotalas y volvé después.

### El estado cambia para el PRÓXIMO render, no al instante

Cuando llamás al setter, el valor **no** cambia inmediatamente en la línea siguiente. El cambio se aplica recién en el **próximo render**. Mirá:

```javascript
function Contador() {
  const [count, setCount] = useState(0);

  function manejarClic() {
    setCount(count + 1);
    console.log(count);   // ⚠️ todavía imprime el valor VIEJO, no el nuevo
  }
  // ...
}
```

`count` mantiene su valor durante toda esta ejecución. El valor nuevo lo vas a ver en la próxima ejecución de la función (el próximo render). No es un bug, es cómo funciona: dentro de un render, el estado es una "foto fija".

### Si el valor nuevo depende del viejo, usá la forma de función

Por lo anterior, cuando el nuevo valor **depende del valor anterior**, la forma más segura de actualizar es pasarle al setter **una función** que recibe el valor previo y devuelve el nuevo:

```javascript
// En vez de esto:
setCount(count + 1);

// Forma robusta (sobre todo si hacés varias actualizaciones seguidas):
setCount((prev) => prev + 1);
```

Le pasás una función `(prev) => prev + 1`: React la llama con el **valor más actual** (`prev`) y usa lo que devuelvas. Esto garantiza que sumes sobre el valor correcto aunque haya varias actualizaciones encoladas. Es el patrón recomendado para "incrementar", "agregar a una lista", "alternar", etc.

> Te dejo esto marcado ahora porque cuando lleguemos a cosas como "agregar un producto al carrito", la forma de función va a ser la correcta. Por ahora, quedate con: **si el nuevo estado se calcula a partir del viejo, usá `setX(prev => ...)`.**

---

## 6. 🟡 `useState` es un *hook*: las reglas de los hooks

`useState` pertenece a una familia de funciones especiales de React llamadas **hooks**. Un **hook** es una función (su nombre siempre empieza con **`use`**) que le permite a un componente "engancharse" a una característica de React — en el caso de `useState`, la característica es **tener memoria**. En el próximo módulo vas a conocer otro hook (`useEffect`); por eso conviene entender desde ya que los hooks tienen **reglas**.

Son tres, y la primera es la que importa de verdad:

1. **Solo se llaman en el nivel superior del componente.** Es decir, arriba de todo, directamente en el cuerpo de la función. **Nunca** dentro de un `if`, un bucle (`for`), o una función anidada.
2. **Solo se llaman dentro de componentes de React** (o dentro de otros hooks, que vas a ver más adelante). No en una función de JavaScript cualquiera.
3. **Se llaman siempre, en el mismo orden, en cada render.** (Esta es la razón técnica detrás de la regla 1.)

```javascript
// ✅ BIEN: el hook arriba de todo, siempre se ejecuta
function Componente() {
  const [count, setCount] = useState(0);
  // ...
}

// ❌ MAL: el hook adentro de un if
function Componente() {
  if (algo) {
    const [count, setCount] = useState(0);   // ROMPE
  }
}
```

**¿Por qué la regla del `if`?** Porque React identifica cada estado **por el orden en que se llaman los hooks**, no por su nombre. Si un render llama a `useState` y el siguiente no (porque estaba dentro de un `if` que dio falso), el orden se desordena y React mezcla los estados. La regla simple para no equivocarte nunca: **los hooks van siempre arriba, siempre se ejecutan, sin condiciones.**

---

## 7. 🔴 Eventos: responder a la interacción

Ya usaste `onClick` en el contador. Formalicemos cómo se responde a la interacción del usuario, porque es donde se disparan los cambios de estado.

En React, a los elementos les pasás **manejadores de eventos** (*event handlers*): funciones que React ejecuta cuando ocurre algo. Se escriben como props en **camelCase**: `onClick`, `onChange`, `onSubmit` (no `onclick` en minúscula como en HTML).

```javascript
function Boton() {
  // El manejador es una función
  function manejarClic() {
    console.log("¡Me hiciste clic!");
  }

  return <button onClick={manejarClic}>Tocame</button>;
  //                      ▲ le PASO la función (sin paréntesis)
}
```

### La trampa que le pasa a TODOS al principio

Fijate que escribí `onClick={manejarClic}` **sin paréntesis**. Esto es crucial:

```javascript
// ✅ BIEN: le pasás la función. React la llama cuando hay clic.
<button onClick={manejarClic}>Tocame</button>

// ❌ MAL: con paréntesis la EJECUTÁS vos al renderizar, no en el clic.
<button onClick={manejarClic()}>Tocame</button>
```

Con paréntesis, `manejarClic()` se ejecuta **inmediatamente cuando se dibuja el componente**, no cuando el usuario hace clic. Es un error clásico. La regla: en el `onClick` va el **nombre** de la función (una referencia), no la llamada.

### Manejador separado vs flecha en línea

Tenés dos formas, ambas válidas:

```javascript
// Forma 1: función aparte (útil cuando el manejador hace varias cosas)
function manejarClic() {
  setCount(count + 1);
}
<button onClick={manejarClic}>+1</button>

// Forma 2: flecha en línea (útil para algo corto, o para pasar argumentos)
<button onClick={() => setCount(count + 1)}>+1</button>
```

La flecha en línea `() => setCount(count + 1)` es una función que se define ahí mismo y se ejecuta al hacer clic. Es la que usamos en el contador.

### El objeto del evento

Cuando React llama a tu manejador, le pasa un **objeto evento** con información de lo que pasó. Lo recibís como parámetro (por convención `e` o `event`). El uso más común es leer lo que el usuario escribió en un input, con **`e.target.value`** — y eso nos lleva justo al último tema.

---

## 8. 🔴 Inputs controlados: el input y el estado, sincronizados

Este es el cierre del módulo y junta todo: estado + eventos.

El problema: un `<input>` de HTML tiene su **propio** valor interno, que maneja el navegador. Vos escribís y el navegador guarda el texto. Pero entonces ese texto vive "atrapado" en el DOM, y React no lo conoce. Si querés **usar** ese texto (filtrar productos, validar, mandarlo a un servidor), lo necesitás en tu **estado**.

La solución es un **input controlado**: un input cuyo valor está **manejado por el estado de React**. Se arma con dos piezas:

```javascript
import { useState } from 'react';

function Buscador() {
  const [texto, setTexto] = useState("");   // estado para el texto, arranca vacío

  return (
    <input
      value={texto}                               // 1) el input MUESTRA el estado
      onChange={(e) => setTexto(e.target.value)}  // 2) al escribir, ACTUALIZA el estado
      placeholder="Buscar productos..."
    />
  );
}
```

Las dos piezas que lo hacen "controlado":

1. **`value={texto}`** → el input siempre muestra lo que hay en el estado. El estado manda.
2. **`onChange={(e) => setTexto(e.target.value)}`** → cada vez que el usuario teclea, se dispara `onChange`, leemos lo nuevo con `e.target.value` y lo guardamos en el estado con `setTexto`.

El ciclo, que ya te va a sonar:

```
   Usuario teclea una letra
         │
         ▼
   onChange se dispara → setTexto(e.target.value)
         │
         ▼
   Cambia el estado → React re-renderiza
         │
         ▼
   El input muestra value={texto} con el texto nuevo
```

El estado y el input quedan **siempre sincronizados**. Se dice que el estado es la **"única fuente de verdad"** (*single source of truth*): el texto verdadero está en el estado, y el input solo lo refleja. La ventaja es enorme: ahora ese texto está en una variable de React y podés usarlo donde quieras.

> 🎓 **Si te lo preguntan — "¿Qué es un input controlado?"**
> Es un input cuyo valor está gobernado por el estado de React: su `value` se lee del estado, y su `onChange` actualiza el estado en cada tecleo. Así el estado es la única fuente de verdad y siempre está sincronizado con lo que se ve. La alternativa (input no controlado) deja el valor en el DOM, fuera del alcance de React.

> 🕳️ **Madriguera — inputs no controlados**
> Existe la forma opuesta: dejar que el input maneje su propio valor y leerlo solo cuando hace falta (con algo llamado `useRef`). Se usa en casos puntuales. El controlado es el camino por defecto y el que vas a usar el 95% del tiempo.
> *Volvé al camino — nosotros usamos inputs controlados.*

---

## 9. 🟢 Cómo encaja todo junto

Cerremos con un ejemplo que combina **todo** el módulo, y de paso reusa el `map` y el condicional del Módulo 3. Es un buscador de MegaSuper que **filtra la lista mientras escribís** — y notá que todo cambia en pantalla sin que vos toques el DOM a mano:

```javascript
import { useState } from 'react';

const productos = [
  { id: 1, nombre: "Manzana" },
  { id: 2, nombre: "Banana"  },
  { id: 3, nombre: "Naranja" },
];

function Buscador() {
  // ESTADO: el texto del buscador (Módulo 4)
  const [texto, setTexto] = useState("");

  // Filtramos la lista según el texto. Esto es JS común corriendo en cada render.
  const filtrados = productos.filter((p) =>
    p.nombre.toLowerCase().includes(texto.toLowerCase())
  );

  return (
    <div>
      {/* INPUT CONTROLADO (Módulo 4): value + onChange */}
      <input
        value={texto}
        onChange={(e) => setTexto(e.target.value)}
        placeholder="Buscar..."
      />

      {/* CONDICIONAL (Módulo 3): si no hay resultados, mensaje */}
      {filtrados.length === 0 ? (
        <p>No se encontraron productos.</p>
      ) : (
        // LISTA con map y key (Módulo 3)
        <ul>
          {filtrados.map((p) => (
            <li key={p.id}>{p.nombre}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

Pará un segundo a apreciar lo que pasa acá, porque es React entero en miniatura: vos **escribís** una letra → cambia el **estado** (`texto`) → React **re-renderiza** → la función corre de nuevo → el `filter` recalcula `filtrados` con el texto nuevo → el `map` produce la lista filtrada → la **reconciliación** actualiza solo lo que cambió en pantalla. Vos **nunca** dijiste "borrá este `<li>`, agregá este otro". Solo describiste *cómo se ve la UI para un texto dado*, y dejaste que el estado y React hagan el resto. **Eso es lo declarativo del Módulo 1, ahora funcionando de verdad.**

> 🕳️ **Madriguera — `useReducer`**
> Cuando un componente tiene estado más complejo (varios valores que cambian juntos con lógica), existe otro hook, `useReducer`, que organiza esos cambios de forma más ordenada que muchos `useState` sueltos. Es el hermano mayor de `useState`.
> *Volvé al camino — con `useState` cubrís la enorme mayoría de los casos.*

Una aclaración honesta de alcance: en este ejemplo el buscador y la lista están **en el mismo componente**, por eso pueden compartir el estado `texto` fácilmente. Cuando quieras que el buscador y la lista sean **componentes separados** que comparten un estado, vas a necesitar una técnica llamada *lifting state up* (elevar el estado). Eso es el Módulo 6. Por ahora, con todo junto en un componente, ya tenés una app **interactiva** de verdad.

---
---

## ✅ Checkpoint — ¿lo agarraste?

Sin mirar, con tus palabras (las respuestas se resuelven en el complemento del recorrido, o me preguntás):

1. ¿Qué es el **estado**? Nombrá al menos dos diferencias con las props.
2. Tenés un contador con `let count = 0` y hacés `count++` en el clic. ¿Por qué el número **no** se actualiza en pantalla? Dá las **dos** razones.
3. Explicá la línea `const [count, setCount] = useState(0)`: ¿qué es cada parte?
4. ¿Cuál es la única forma válida de cambiar el estado? ¿Por qué `count = count + 1` no sirve?
5. Contá el **ciclo de render**: ¿qué pasa, paso a paso, desde que llamás al setter hasta que ves el cambio? Conectalo con la reconciliación del Módulo 1.
6. ¿Por qué se dice que "renderizar" es ejecutar la función del componente? ¿Por qué el estado no se reinicia pero una variable común sí?
7. ¿Cuándo conviene usar `setCount(prev => prev + 1)` en vez de `setCount(count + 1)`?
8. ¿Qué es un **hook**? Nombrá la regla más importante de los hooks y por qué existe.
9. En un `onClick`, ¿cuál es la diferencia entre `{manejar}` y `{manejar()}`? ¿Cuál está bien?
10. ¿Qué es un **input controlado**? ¿Cuáles son las dos piezas (`value` y `onChange`) y qué hace cada una?

---

## 👉 Qué viene en el Módulo 5

Ya tenés estado y la app reacciona a la interacción. Pero los datos siguen escritos a mano en el código. En el Módulo 5 los traemos **de afuera**:

- **`useEffect`**: el hook para ejecutar código en momentos clave (como cuando el componente aparece en pantalla).
- El **array de dependencias**: cómo controlar *cuándo* se ejecuta un efecto.
- **Traer datos** de un servicio (simulado por ahora) y guardarlos en el estado.
- Manejar el momento de **"cargando..."** mientras los datos llegan.

Ahí tu app deja de tener datos inventados y empieza a parecerse a una real. Te espero en el Módulo 5.

---

**FIN DEL MÓDULO 4**
