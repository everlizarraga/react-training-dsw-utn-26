# 🧭 Recorrido React — Módulo 9

## Estado global: Context y custom hooks

---

### 📍 Sobre este documento

Este módulo cierra el **modelo de datos** de React. Veníamos arrastrando un dolor desde el Módulo 6: el **carrito** lo necesitan componentes muy lejanos entre sí (la navbar, el detalle, el checkout), y pasarlo por props a través de todo el árbol es tedioso — el **prop drilling**. Acá lo resolvemos de raíz con el **Context API**. Y de paso aprendés a empaquetar lógica reutilizable en tus propios **custom hooks**.

**Qué cubre este módulo:**
- El **prop drilling** del carrito, en su peor versión.
- **Context API**: dar acceso a un dato sin pasarlo por props por todo el árbol.
- Los **tres pasos** del context: crear, proveer, usar.
- **Custom hooks**: empaquetar lógica reutilizable en tu propia herramienta.
- Cuándo conviene context y cuándo no.

**Qué NO cubre (viene después):**
- Vestir la app con una librería visual (CSS-in-JS, Material UI) → Módulo 10.

---

### 🎒 De dónde venís

Asumo los Módulos 1 a 8. Para este módulo es clave tener fresco:
- Del **Módulo 6**: **lifting state up** (elevar el estado al padre común) y el **prop drilling** (pasar props por componentes intermedios que no las usan).
- Del **Módulo 3**: las props y la prop especial **`children`**.
- Del **Módulo 4**: `useState` y la forma de función `setX(prev => ...)`.
- Del **Módulo 5**: `useEffect` y su **función de limpieza** (que acá, por fin, va a brillar).

> Recordá lo que vengo marcando desde el Módulo 6: el carrito es el ejemplo perfecto de un estado que "necesitan muchísimos componentes lejanos", y dije que lifting + props se volvía insoportable, y que la solución se llamaba **Context**. Llegamos.

---
---

## 1. 🔴 El problema en su peor versión: el prop drilling del carrito

Pongamos el dolor sobre la mesa. El carrito de MegaSuper lo necesitan tres componentes que están en lugares **muy distintos** del árbol:

- La **Navbar** (para mostrar el contador de productos).
- La página de **Detalle** (para agregar un producto).
- El **Checkout** (para listar lo comprado y vaciarlo).

Con lo que sabés del Módulo 6, el estado del carrito iría en lo más alto (en `App`), y de ahí habría que **pasarlo por props** hacia abajo, atravesando todos los componentes intermedios — aunque esos intermedios **no usen** el carrito, tienen que recibirlo y volver a pasarlo solo para que llegue al de abajo:

```
   App  [carrito]                    ← el estado vive acá
    │  pasa carrito por props ↓
   Layout  (no usa el carrito,       ← pero igual tiene que recibirlo
    │       solo lo pasa para abajo)     y pasarlo a Navbar...
    ├──► Navbar  (acá SÍ se usa)
    │
   Outlet ↓ (y para que llegue al detalle, hay que seguir pasándolo...)
    └──► ProductDetailPage  (acá SÍ se usa)
```

Eso de ir **enhebrando** la prop por niveles que no la necesitan, solo para que llegue a destino, es el **prop drilling** (perforación de props). Es verboso, frágil (te olvidás de pasarla en un nivel y se rompe) y ensucia componentes intermedios con props que no les importan. Cuantos más niveles y más lugares lejanos, peor.

Necesitamos una forma de que un componente acceda al carrito **directamente**, sin importar qué tan abajo esté ni cuántos intermedios haya en el medio. Eso es el **Context**.

---

## 2. 🔴 La idea: Context

> **El Context API permite que un componente "padre" ponga un dato a disposición de _todo su subárbol_, y que cualquier componente descendiente lo lea _directamente_, sin que haya que pasarlo por props nivel por nivel.**

La analogía: en vez de pasar un mensaje de mano en mano por toda la fila (props), el padre lo **transmite por altavoz** a toda la sala, y cualquiera que esté en la sala lo escucha directo, sin importar dónde esté sentado.

```
   ❌ Prop drilling: el dato se pasa de mano en mano
   App → Layout → ... → ... → componente que lo necesita

   ✅ Context: el dato se "transmite" a todo el subárbol
   App  📡 (provee el carrito)
    │
    ├── Layout
    │     └── Navbar  ◄── lo lee directo
    └── ProductDetailPage  ◄── lo lee directo
        (sin pasar por los intermedios)
```

El Context es ideal para datos que **muchos** componentes lejanos necesitan: el carrito, el usuario logueado, el tema visual (modo claro/oscuro), el idioma. Para esos casos reemplaza al prop drilling.

> 🎓 **Si te lo preguntan — "¿Qué problema resuelve el Context?"**
> Resuelve el prop drilling: la necesidad de pasar una prop por muchos componentes intermedios que no la usan, solo para que llegue a un componente profundo. El Context permite que un componente provea un dato a todo su subárbol y que cualquier descendiente lo lea directamente, sin pasarlo nivel por nivel. Se usa para datos que muchos componentes distantes necesitan.

---

## 3. 🔴 Los tres pasos del Context

Usar Context son siempre **tres pasos**: crear, proveer, usar. Veámoslos con el carrito.

### Paso 1 — Crear el contexto

Creás el contexto con `createContext`. Es como declarar el "canal" por el que va a viajar el dato:

```javascript
import { createContext } from 'react';

const CartContext = createContext();
```

### Paso 2 — Proveer el contexto

El componente que tiene el dato lo **provee** a su subárbol envolviéndolo en `<CartContext.Provider>`. Lo que pongas en la prop **`value`** es lo que los descendientes van a poder leer:

```javascript
<CartContext.Provider value={elCarrito}>
  {/* todo lo que esté acá adentro puede leer 'elCarrito' */}
</CartContext.Provider>
```

### Paso 3 — Usar el contexto

Cualquier componente descendiente lee el dato con el hook **`useContext`**, pasándole el contexto:

```javascript
import { useContext } from 'react';

function Navbar() {
  const carrito = useContext(CartContext);   // lee el dato directo, sin props
  // ...
}
```

Eso es el esqueleto. Ahora lo armamos completo y bien, que es como lo vas a ver en la práctica.

> 🎓 **Si te lo preguntan — "¿Cuáles son los tres pasos para usar Context?"**
> Crear el contexto con `createContext()`. Proveerlo desde un componente padre envolviendo el subárbol en `<Contexto.Provider value={...}>`, donde `value` es el dato a compartir. Y usarlo desde cualquier descendiente con `useContext(Contexto)`, que devuelve ese `value`.

---

## 4. 🔴 El patrón completo: un Provider que tiene el estado

En la práctica no se provee un dato suelto: se arma un **componente Provider** que **tiene el estado** (con `useState`) y expone tanto el dato como las funciones para modificarlo. Así todo lo del carrito queda en un solo lugar prolijo:

```javascript
// context/CartContext.jsx
import { createContext, useContext, useState } from 'react';

// Paso 1 — crear el contexto
const CartContext = createContext();

// El Provider: tiene el estado del carrito y lo provee a su subárbol
export function CartProvider({ children }) {
  const [carrito, setCarrito] = useState([]);

  // forma de función (Módulo 4 §5): el nuevo estado depende del viejo
  const actualizarCarrito = (producto) => {
    setCarrito((prev) => [...prev, producto]);
  };

  const limpiarCarrito = () => setCarrito([]);

  // Paso 2 — proveer: expone el dato Y las funciones por 'value'
  return (
    <CartContext.Provider value={{ carrito, actualizarCarrito, limpiarCarrito }}>
      {children}
    </CartContext.Provider>
  );
}
```

Fijate cómo se juntan módulos anteriores:
- `useState` (Módulo 4) guarda el carrito **dentro del Provider** — ese es ahora su dueño.
- `actualizarCarrito` usa la **forma de función** `setCarrito(prev => ...)` (Módulo 4 §5), que es la correcta porque el nuevo carrito depende del anterior.
- **`children`** (Módulo 3) es todo lo que el Provider envuelve.
- En `value` se exponen tres cosas: el dato (`carrito`) y las dos funciones para modificarlo. Cualquier descendiente va a poder usar las tres.

Después, en `App`, envolvés toda la app con ese Provider (una sola vez):

```javascript
import { CartProvider } from './context/CartContext';

function App() {
  return (
    <CartProvider>      {/* 👈 ahora TODA la app puede acceder al carrito */}
      <Routes>
        {/* ...las rutas del Módulo 7... */}
      </Routes>
    </CartProvider>
  );
}
```

Y listo: ya no hay que pasar el carrito por props por ningún lado. Cualquier componente, por más abajo que esté, lo lee directo. **El prop drilling desapareció.**

---

## 5. 🔴 Custom hooks: empaquetar lógica reutilizable

Para **usar** el carrito, un componente haría `useContext(CartContext)`, importando `useContext` de React y `CartContext` del archivo del contexto. Eso es plomería que se repite en cada componente. Acá entran los **custom hooks**.

> Un **custom hook** (hook personalizado) es simplemente **una función propia cuyo nombre empieza con `use` y que usa otros hooks adentro.** Sirve para empaquetar y reutilizar lógica.

El custom hook más común de todos es justo este: envolver el `useContext` en una función linda llamada `useCart`:

```javascript
// (en el mismo archivo context/CartContext.jsx)
export const useCart = () => useContext(CartContext);
```

Eso es todo: `useCart` es una función que por dentro llama a `useContext(CartContext)`. Ahora, **cualquier componente** que necesite el carrito hace simplemente:

```javascript
import { useCart } from '../../context/CartContext';

function Navbar() {
  const { carrito } = useCart();          // ¡directo y limpio!
  // ...
}

function ProductDetailPage() {
  const { carrito, actualizarCarrito } = useCart();   // saca lo que necesite
  // ...
}
```

El componente ya no importa `useContext` ni `CartContext` ni sabe de la plomería: solo dice `useCart()`. El código pasa a **expresar la intención** ("dame el carrito") en lugar de la **implementación** (cómo se accede por dentro). Eso es lo que un buen custom hook te da: esconde lo complejo detrás de un nombre claro.

> 🎓 **Si te lo preguntan — "¿Qué es un custom hook?"**
> Es una función propia cuyo nombre empieza con `use` y que utiliza otros hooks por dentro. Sirve para extraer y reutilizar lógica con estado entre componentes, escondiendo los detalles de implementación detrás de un nombre claro. El ejemplo típico es envolver `useContext` en un hook como `useCart`.

---

## 6. 🟡 Custom hooks más allá del context

Los custom hooks no son solo para el context: sirven para empaquetar **cualquier** lógica con estado reutilizable. Acá es donde, por fin, **brilla la función de limpieza** que viste en el Módulo 5 §8 (te lo prometí).

Imaginá que en varios lugares de la app querés saber si el usuario tiene conexión a internet. Esa lógica (escuchar los eventos del navegador `online`/`offline`) la empaquetás en un custom hook:

```javascript
import { useState, useEffect } from 'react';

function useOnlineStatus() {
  const [online, setOnline] = useState(true);

  useEffect(() => {
    function avisarOnline()  { setOnline(true); }
    function avisarOffline() { setOnline(false); }

    // me suscribo a los eventos del navegador
    window.addEventListener('online', avisarOnline);
    window.addEventListener('offline', avisarOffline);

    // 👇 función de limpieza (Módulo 5): me desuscribo al desmontar
    return () => {
      window.removeEventListener('online', avisarOnline);
      window.removeEventListener('offline', avisarOffline);
    };
  }, []);

  return online;   // el hook devuelve el dato útil
}
```

Y cualquier componente lo usa con una sola línea, sin saber nada de los `addEventListener`:

```javascript
function BarraDeEstado() {
  const online = useOnlineStatus();
  return <p>{online ? "✅ Conectado" : "❌ Sin conexión"}</p>;
}
```

Mirá lo que conseguiste: la lógica complicada (suscribirse, limpiar) vive **una vez** dentro del hook, y los componentes la usan limpia. Acá ves por qué la **limpieza del Módulo 5** importaba: el hook enciende suscripciones y su limpieza las apaga al desmontar, sin fugas.

> ⚠️ **Punto clave:** los custom hooks comparten **lógica, no estado**. Si dos componentes distintos llaman a `useOnlineStatus()`, **cada uno tiene su propio estado independiente** — no comparten el mismo `online`. Comparten la *receta*, no los *datos*. (Si querés que compartan los **datos**, eso es justamente lo que hace el Context de las secciones anteriores.)

---

## 7. 🟢 Cómo encaja todo junto

Con esto, el **modelo de datos completo de React** queda en tu cabeza. Tenés tres herramientas, cada una para su caso:

| Herramienta | Para qué | Módulo |
|---|---|---|
| **Props + callbacks** | Compartir entre **pocos** componentes cercanos (padre↔hijo) | 3 y 6 |
| **Context** | Compartir un dato con **muchos** componentes lejanos | 9 (este) |
| **Custom hooks** | Empaquetar **lógica** reutilizable (incluido el acceso a un context) | 9 (este) |

Y el carrito de MegaSuper, que era el dolor de cabeza, ahora es limpio: el `CartProvider` tiene el estado, envuelve toda la app, y la Navbar, el Detalle y el Checkout lo leen con `useCart()` — **sin una sola prop drilleada.** El problema que sentiste tres veces (Módulos 6, 7 y 8) quedó resuelto.

> 🕳️ **Madriguera — el valor por defecto del context**
> Si creás el contexto sin valor por defecto (`createContext()`) y un componente usa `useCart()` **fuera** del Provider, va a recibir `undefined` y el destructuring va a romper. En la práctica no pasa porque el Provider envuelve toda la app, pero es bueno saberlo. Se puede pasar un valor por defecto a `createContext(valorInicial)` para esos casos.
> *Volvé al camino — con el Provider envolviendo todo, estás cubierto.*

> 🕳️ **Madriguera — cuándo NO abusar del context**
> El context es genial para datos "globales" (carrito, usuario, tema), pero no es para todo. Si un dato lo usan solo dos componentes cercanos, props + lifting (Módulo 6) es más simple y explícito. Meter todo en context hace que el flujo de datos sea menos visible. Regla: context para lo realmente compartido por muchos; props para lo local.
> *Volvé al camino — usá la herramienta según la distancia y la cantidad de componentes.*

---
---

## ✅ Checkpoint — ¿lo agarraste?

Sin mirar, con tus palabras (las respuestas se resuelven en el complemento del recorrido, o me preguntás):

1. ¿Qué es el **prop drilling**? Explicalo con el ejemplo del carrito de MegaSuper.
2. ¿Qué problema resuelve el **Context** y con qué analogía lo entendés?
3. Nombrá los **tres pasos** del Context y qué hace cada uno.
4. En `<CartContext.Provider value={...}>`, ¿qué representa el `value`? ¿Quién puede leerlo?
5. En el patrón del `CartProvider`, ¿dónde vive el estado del carrito ahora? ¿Qué se expone en `value`?
6. ¿Qué tiene que ver `children` (Módulo 3) con el Provider?
7. ¿Qué es un **custom hook**? ¿Por qué `useCart = () => useContext(CartContext)` es uno?
8. ¿Qué ventaja da `useCart()` frente a usar `useContext(CartContext)` directo en cada componente?
9. En el hook `useOnlineStatus`, ¿para qué sirve la **función de limpieza** (el `return`)? (conectá con el Módulo 5)
10. Los custom hooks, ¿comparten **lógica** o **estado**? Si dos componentes llaman al mismo custom hook, ¿comparten los datos? ¿Qué herramienta sí los compartiría?

---

## 👉 Qué viene en el Módulo 10 (el último)

Ya tenés **todo el React funcional** que necesitás: componentes, props, estado, efectos, datos, compartir estado, routing, formularios y estado global. Lo único que falta es lo estético — que la app, además de funcionar, se vea bien. El Módulo 10 cierra el recorrido:

- **CSS-in-JS**: escribir estilos con JavaScript en lugar de archivos `.css` aparte.
- **Librerías de componentes** (Material UI): usar piezas visuales ya hechas y lindas (botones, tarjetas, campos de formulario) en vez de armar todo desde cero.
- Cómo eso se conecta con lo que ya sabés (el `TextField` que mencioné en el Módulo 8 es, por dentro, un input controlado).

Es el módulo más liviano de todos, el "moño" final. Te espero en el Módulo 10.

---

**FIN DEL MÓDULO 9**
