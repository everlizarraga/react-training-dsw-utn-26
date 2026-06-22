# 🧭 Recorrido React — Módulo 5

## Efectos y datos: traer información de afuera

---

### 📍 Sobre este documento

En el Módulo 4 tu app se volvió interactiva, pero los datos seguían escritos a mano en el código. En una app real, los datos **viven en otro lado** (un servidor) y hay que **ir a buscarlos**. Acá aprendés a hacer eso: qué es un **efecto**, cómo usar **`useEffect`** para ejecutar código en el momento justo, cómo controlar *cuándo* se ejecuta con el **array de dependencias**, cómo separar la obtención de datos en una **capa de servicio**, y cómo mostrar un **"cargando..."** mientras los datos llegan.

**Qué cubre este módulo:**
- Qué es un **efecto** (*side effect*) y por qué no va dentro del render.
- **`useEffect`**: anatomía y cómo se usa.
- El **array de dependencias**: las tres formas y qué hace cada una.
- La **capa de servicio**: separar el "cómo se obtienen los datos".
- **Traer datos** cuando el componente aparece, y guardarlos en el estado.
- **Estados de carga**: mostrar "cargando..." mientras los datos llegan.
- La **función de limpieza** de un efecto.

**Qué NO cubre (viene después):**
- Compartir estado entre componentes separados (*lifting state up*) → Módulo 6.
- Navegar entre pantallas (*routing*) → Módulo 7.

---

### 🎒 De dónde venís

Asumo los Módulos 1 a 4. En particular necesitás tener fresco del **Módulo 4**: el **estado** y `useState`, el **ciclo de render** (cambiar el estado redibuja), y las **reglas de los hooks** (van arriba de todo, siempre se ejecutan). `useEffect` es otro hook, así que esas reglas también le aplican.

> También vas a reusar de **JavaScript** las **Promesas** y `async`/`await`. Si están un poco oxidadas: una **Promesa** es un valor que va a llegar *más tarde* (típico de cosas que tardan, como pedir datos por la red), y **`await`** es "esperá a que ese valor llegue antes de seguir". Con eso alcanza para este módulo.

---
---

## 1. 🔴 El problema: los datos vienen de afuera

Hasta ahora nuestros productos estaban acá, clavados en el código:

```javascript
const productos = [
  { id: 1, nombre: "Manzana" },
  // ...
];
```

Pero en MegaSuper de verdad, los productos están en una **base de datos en un servidor**, y la app tiene que **pedirlos** cuando arranca. Eso plantea una pregunta nueva: *¿en qué momento, y cómo, le digo a mi componente "andá a buscar los productos"?*

No podés simplemente poner el pedido en medio del componente, así:

```javascript
function ProductList() {
  const data = irABuscarProductosAlServidor();   // ❌ NO se hace así
  return <div>...</div>;
}
```

¿Por qué no? Porque eso se ejecutaría **en cada render**, una y otra vez (recordá del Módulo 4: el componente es una función que React ejecuta muchas veces). Pedirías los datos al servidor decenas de veces por segundo. Un desastre.

Lo que necesitás es ejecutar ese pedido en un **momento controlado** — por ejemplo, **una sola vez, cuando el componente aparece en pantalla**. Para eso existe `useEffect`. Pero antes, entendamos qué es un "efecto".

---

## 2. 🔴 Qué es un "efecto"

Recordá del Módulo 4 que la función de un componente tiene **un trabajo principal**: a partir del estado y las props, **calcular el JSX** (la descripción de la UI). Eso es lo que se llama un render "puro": entran datos, sale interfaz, sin meterse con nada más.

Pero a veces tu componente necesita hacer cosas que **no** son "calcular JSX": pedir datos a un servidor, arrancar un temporizador, suscribirse a algo, cambiar el título de la pestaña del navegador. Todas estas tienen algo en común: **tocan algo que está afuera del componente.** A eso se le llama un **efecto** (o *side effect*, efecto secundario).

> Un **efecto** es código que conecta tu componente con el mundo exterior (un servidor, un temporizador, el navegador). Como no es parte de "calcular la UI", **no debe ejecutarse durante el render**.

La regla mental clave:

> **React renderiza primero, y ejecuta los efectos después.** Primero calcula y dibuja la UI; recién cuando terminó, corre los efectos.

`useEffect` es el hook donde ponés ese tipo de código, para que React lo ejecute en el momento correcto (después del render) y la cantidad de veces que vos decidas.

> 🎓 **Si te lo preguntan — "¿Para qué sirve `useEffect`?"**
> Sirve para ejecutar efectos secundarios en un componente: código que interactúa con el mundo exterior (pedir datos, temporizadores, suscripciones) y que no forma parte del cálculo puro de la UI. React lo ejecuta *después* de renderizar, y permite controlar cuándo se ejecuta mediante el array de dependencias.

---

## 3. 🔴 `useEffect`: anatomía

Como todo hook, primero se importa:

```javascript
import { useEffect } from 'react';
```

Y su forma es siempre esta: recibe **dos argumentos**, una función y un array.

```javascript
useEffect(() => {
  // 1) La FUNCIÓN del efecto: el código que querés ejecutar.
}, [/* 2) El ARRAY DE DEPENDENCIAS: controla CUÁNDO se ejecuta. */]);
```

- **Primer argumento — la función del efecto.** Acá adentro va el código que toca el mundo exterior (pedir datos, etc.). React la ejecuta **después** del render.
- **Segundo argumento — el array de dependencias.** Es lo que decide **cuándo** se vuelve a ejecutar la función. Es la parte que hay que entender bien, y es lo que sigue.

---

## 4. 🔴 El array de dependencias: las tres formas

Esta es la pieza central del módulo. El array de dependencias tiene **tres variantes**, y cada una hace que el efecto se ejecute en momentos distintos. Antes, una palabra que vas a escuchar:

> **Montar** (*mount*) = el momento en que el componente **aparece por primera vez** en la pantalla. Es su "nacimiento".

Las tres formas:

```javascript
// FORMA 1 — Sin array → se ejecuta DESPUÉS DE CADA render.
useEffect(() => {
  console.log("Corro en todos los renders");
});

// FORMA 2 — Array vacío [] → se ejecuta UNA SOLA VEZ, al montar.
useEffect(() => {
  console.log("Corro solo cuando el componente aparece");
}, []);

// FORMA 3 — Con dependencias [x] → al montar Y cada vez que x cambia.
useEffect(() => {
  console.log("Corro al aparecer y cuando cambia 'filtro'");
}, [filtro]);
```

En tabla, que es como conviene tenerlo:

| Forma | Cuándo se ejecuta el efecto | Uso típico |
|---|---|---|
| `useEffect(fn)` *(sin array)* | Después de **cada** render | Raro; casi nunca lo querés |
| `useEffect(fn, [])` | **Una vez**, al montar | **Traer datos al cargar** (lo más común) |
| `useEffect(fn, [dep])` | Al montar **y** cuando `dep` cambia | Reaccionar a un cambio (ej: refiltrar cuando cambia algo) |

La que vas a usar el 90% del tiempo es la **Forma 2** (`[]`): "hacé esto una sola vez, cuando el componente aparece". Es justo lo que necesitamos para pedir los productos al arrancar.

> ⚠️ **Trampa clásica — el bucle infinito:** si **omitís** el array (Forma 1) y dentro del efecto cambiás un estado con un setter, pasa esto: el efecto cambia el estado → el estado dispara un render → después del render se ejecuta el efecto de nuevo → cambia el estado de nuevo → ... bucle infinito. Por eso, cuando un efecto actualiza estado, casi siempre va con `[]` o con dependencias bien elegidas, **nunca sin array.**

> 🎓 **Si te lo preguntan — "¿Qué hacen las tres formas del array de dependencias de `useEffect`?"**
> Sin array, el efecto corre después de cada render. Con array vacío `[]`, corre una sola vez cuando el componente se monta. Con dependencias `[x]`, corre al montar y cada vez que alguna dependencia cambia. El array le dice a React de qué valores depende el efecto para decidir cuándo re-ejecutarlo.

---

## 5. 🔴 La capa de servicio: separar el "cómo se obtienen los datos"

Antes de traer los productos, una decisión de orden. **El componente no debería ocuparse de los detalles de cómo se piden los datos** (a qué dirección, con qué protocolo, etc.). Su trabajo es mostrar la UI. Los detalles de "ir a buscar datos" se separan en un archivo aparte que se llama, por convención, una **capa de servicio** (*service*).

La idea: el componente le dice al servicio *"dame los productos"*, y el servicio se encarga del cómo. Si mañana cambia de dónde vienen los datos, tocás solo el servicio y el componente ni se entera. Esto es **separación de responsabilidades**, y hace tu código mucho más ordenado.

Por ahora todavía no tenemos un servidor real, así que vamos a **simularlo** (un *mock*). Este servicio devuelve los productos, pero con un retraso artificial para imitar lo que tardaría una llamada de red real:

```javascript
// service/productsService.js
import { productos } from '../mockdata/productos';

// Simula pedir los productos a un servidor.
// Devuelve una Promesa que se resuelve con los datos después de 1 segundo.
export function obtenerProductos() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(productos);   // "acá están tus productos" (después de esperar)
    }, 1000);
  });
}
```

Qué hace, en palabras:
- Devuelve una **Promesa**: un valor que va a estar listo *más tarde*, no ya.
- El `setTimeout(..., 1000)` espera **1 segundo** antes de resolver, fingiendo la demora de la red.
- Cuando pasa ese segundo, `resolve(productos)` entrega los datos.

Lo lindo es que, desde el componente, **pedir datos a este mock simulado se hace igual que pedirlos a un servidor real**: llamás a `obtenerProductos()` y esperás la Promesa. El día que conectes un backend de verdad, cambiás el adentro del servicio y el componente sigue igual.

> 🕳️ **Madriguera — pedir datos a un servidor real**
> Cuando haya un backend de verdad, en lugar del `setTimeout` el servicio usará `fetch` (que viene en el navegador) o una librería como **axios** para hacer la llamada HTTP real. El mecanismo desde el componente no cambia: seguís llamando al servicio y esperando la Promesa. Conectar con el backend es un tema en sí mismo.
> *Volvé al camino — con el mock aprendés el patrón completo, que es lo que importa.*

---

## 6. 🔴 Traer datos cuando el componente aparece

Ahora juntamos las piezas: estado (Módulo 4) para guardar los productos + `useEffect` con `[]` para pedirlos al montar + la capa de servicio.

```javascript
import { useState, useEffect } from 'react';
import { obtenerProductos } from './service/productsService';

function ProductList() {
  // 1) Estado donde vamos a guardar los productos. Arranca como lista vacía.
  const [productos, setProductos] = useState([]);

  // 2) Efecto: pedir los productos UNA VEZ, al montar (array vacío []).
  useEffect(() => {
    async function cargar() {
      const data = await obtenerProductos();   // espero a que lleguen
      setProductos(data);                       // los guardo en el estado
    }
    cargar();
  }, []);

  // 3) Render: muestro los productos (vacío al principio, llenos después)
  return (
    <ul>
      {productos.map((p) => <li key={p.id}>{p.nombre}</li>)}
    </ul>
  );
}
```

### Un detalle que confunde: la función `async` adentro

Quizás te preguntás por qué definí una función `cargar` adentro en vez de hacer el efecto `async` directamente. La razón:

```javascript
// ❌ NO se puede: el efecto no puede ser async directamente.
useEffect(async () => { ... }, []);
//        React espera que el efecto devuelva o nada o una función de limpieza
//        (sección 8), pero una función async siempre devuelve una Promesa.

// ✅ Solución: definís una función async ADENTRO y la llamás.
useEffect(() => {
  async function cargar() {
    const data = await obtenerProductos();
    setProductos(data);
  }
  cargar();
}, []);
```

Es un patrón que vas a escribir muchas veces. No te compliques con el porqué profundo: la regla práctica es **"función async adentro del efecto, y la llamás".**

### Por qué se ve primero vacío y después lleno

Seguí el flujo, porque acá se entiende cómo se sienten los datos al cargar:

```
   Render 1:  productos = []  (valor inicial)  →  pantalla: lista vacía
        │
        ▼  (después del render, corre el efecto por el [])
   El efecto llama al servicio... y espera 1 segundo...
        │
        ▼  (llegan los datos)
   setProductos(data)  →  cambió el estado  →  React re-renderiza
        │
        ▼
   Render 2:  productos = [...]  →  pantalla: la lista con los productos
```

Hay **dos renders**: uno con la lista vacía (mientras los datos viajan) y otro con la lista llena (cuando llegaron). Ese primer momento "vacío" es el que vamos a manejar ahora, para no mostrarle al usuario una pantalla en blanco.

---

## 7. 🔴 Estados de carga: el "cargando..."

Durante ese primer render (mientras los datos viajan), no querés mostrar una pantalla vacía y rara. Querés mostrar un **"cargando..."** (un *spinner*, un texto, lo que sea). Para eso usás **otro estado**, un booleano que indica si todavía estás esperando:

```javascript
import { useState, useEffect } from 'react';
import { obtenerProductos } from './service/productsService';

function ProductList() {
  const [productos, setProductos] = useState([]);
  const [cargando, setCargando] = useState(true);   // arranca en "true": esperando

  useEffect(() => {
    async function cargar() {
      const data = await obtenerProductos();
      setProductos(data);
      setCargando(false);          // ya llegaron: dejamos de esperar
    }
    cargar();
  }, []);

  // Renderizado condicional (Módulo 3): mostramos una cosa u otra según 'cargando'
  if (cargando) {
    return <p>Cargando productos...</p>;
  }

  return (
    <ul>
      {productos.map((p) => <li key={p.id}>{p.nombre}</li>)}
    </ul>
  );
}
```

El flujo ahora:
- **Render 1:** `cargando = true` → se muestra "Cargando productos...".
- Llegan los datos → `setProductos(data)` y `setCargando(false)`.
- **Render 2:** `cargando = false` → se muestra la lista.

Fijate cómo se combinan los módulos: el **estado** (Módulo 4) `cargando` decide, vía **renderizado condicional** (Módulo 3), qué se muestra. Esto es lo que en una app real es el típico *spinner* o *skeleton* (esos recuadros grises que parpadean mientras carga el contenido): exactamente este patrón, con un componente más lindo en lugar del texto.

---

## 8. 🟡 La función de limpieza de un efecto

Esto completa la anatomía de `useEffect`. Para traer datos no hace falta, pero te lo muestro para que tengas la figura entera (y porque lo vas a necesitar en el Módulo 9).

Algunos efectos **dejan algo "prendido"** que después hay que **apagar**: un temporizador que hay que frenar, una suscripción que hay que cancelar. Para eso, la función del efecto puede **devolver otra función**, llamada **función de limpieza** (*cleanup*), que React ejecuta cuando el componente **desaparece** (se desmonta) o antes de volver a correr el efecto.

```javascript
useEffect(() => {
  // Arranco un temporizador (algo que queda "prendido")
  const id = setInterval(() => {
    console.log("tic");
  }, 1000);

  // Devuelvo la limpieza: React la llama al desmontar para apagarlo
  return () => {
    clearInterval(id);
  };
}, []);
```

Sin esa limpieza, el temporizador seguiría corriendo aunque el componente ya no exista (una "fuga"). La regla mental: **si un efecto enciende algo, su limpieza lo apaga.**

> No te preocupes si esto se siente abstracto ahora: para pedir datos no lo vas a usar. Lo vas a ver brillar en el Módulo 9, cuando armemos un hook que escucha eventos del navegador.

---

## 9. 🟢 Cómo encaja todo junto

El patrón completo de "cargar datos en una pantalla", que es de las cosas más comunes que vas a escribir en React. Junta el servicio, el efecto al montar, el estado de carga y el render:

```javascript
// --- service/productsService.js ---
import { productos } from '../mockdata/productos';

export function obtenerProductos() {
  return new Promise((resolve) => {
    setTimeout(() => resolve(productos), 1000);   // simula la red
  });
}

// --- ProductList.jsx ---
import { useState, useEffect } from 'react';
import { obtenerProductos } from './service/productsService';

function ProductList() {
  const [productos, setProductos] = useState([]);   // datos
  const [cargando, setCargando] = useState(true);   // estado de carga

  useEffect(() => {
    async function cargar() {
      const data = await obtenerProductos();
      setProductos(data);
      setCargando(false);
    }
    cargar();
  }, []);   // [] → una sola vez, al montar

  if (cargando) {
    return <p>Cargando productos...</p>;
  }

  if (productos.length === 0) {
    return <p>No hay productos disponibles.</p>;
  }

  return (
    <ul>
      {productos.map((p) => <li key={p.id}>{p.nombre}</li>)}
    </ul>
  );
}
```

Leelo de arriba a abajo y reconocé cada módulo trabajando junto: la **capa de servicio** simula la red; el **`useEffect` con `[]`** pide los datos una vez al aparecer; el **estado** (Módulo 4) guarda productos y el flag de carga; el **renderizado condicional** (Módulo 3) elige entre "cargando", "no hay nada" y "la lista"; y el **`map` con `key`** (Módulo 3) dibuja los resultados. Esto es, literalmente, cómo se carga una pantalla de productos en una app de verdad.

> 🕳️ **Madriguera — librerías de manejo de datos**
> Existen librerías (la más conocida se llama **React Query** / TanStack Query) que se encargan solas de todo este baile: el estado de carga, los errores, reintentos y caché. En proyectos grandes se usan muchísimo para no escribir el patrón a mano cada vez.
> *Volvé al camino — entender el patrón "a mano" es lo que te deja después usar esas librerías con criterio.*

> Un detalle honesto que dejamos para más adelante: este patrón no maneja el caso de **error** (qué pasa si el servidor falla) ni el de cancelar un pedido si el componente desaparece a mitad de camino. Son refinamientos que se agregan cuando hace falta; el esqueleto que aprendiste es el correcto.

---
---

## ✅ Checkpoint — ¿lo agarraste?

Sin mirar, con tus palabras (las respuestas se resuelven en el complemento del recorrido, o me preguntás):

1. ¿Qué es un **efecto** (*side effect*)? Dá dos ejemplos. ¿Por qué no va dentro del render?
2. ¿Qué quiere decir "React renderiza primero y ejecuta los efectos después"?
3. ¿Por qué está **mal** pedir los datos directamente en el cuerpo del componente (sin `useEffect`)?
4. Explicá las **tres formas** del array de dependencias y cuándo se ejecuta el efecto en cada una. ¿Cuál usás para "traer datos al cargar"?
5. ¿Qué significa que un componente se **monta**?
6. ¿Qué es la **trampa del bucle infinito** y cómo se evita?
7. ¿Qué es una **capa de servicio** y por qué conviene separarla del componente?
8. En el patrón de carga, ¿por qué la pantalla se ve primero "vacía/cargando" y después con datos? Contá los dos renders.
9. ¿Por qué no se puede hacer `useEffect(async () => {...}, [])` y cuál es la solución?
10. ¿Para qué sirve la **función de limpieza** de un efecto? ¿Cuándo la ejecuta React?

---

## 👉 Qué viene en el Módulo 6

Ya tenés componentes interactivos que cargan datos reales. Pero todo vive en una sola pantalla y, cuando un estado lo necesitan dos componentes **separados**, todavía no sabés cómo compartirlo. El Módulo 6 resuelve eso:

- **Lifting state up** (elevar el estado): cómo dos componentes hermanos comparten un estado (acá se resuelve el cabo suelto del buscador del Módulo 4).
- Cómo un hijo le "avisa" algo al padre (sin tocar las props).

Y a partir de ahí, MegaSuper deja de ser una pantalla y se vuelve una app de verdad: **routing** y varias páginas (Módulo 7), **formularios** (Módulo 8) y **estado global** con Context (Módulo 9). Te espero en el Módulo 6.

---

**FIN DEL MÓDULO 5**
