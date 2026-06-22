# 🧭 Recorrido React — Módulo 7

## Routing: varias páginas en una SPA

---

### 📍 Sobre este documento

Hasta ahora toda tu app vivió en una sola pantalla. Pero MegaSuper necesita **varias páginas**: el inicio con los productos, la página de detalle de un producto, el checkout. Y necesita que el usuario pueda **navegar** entre ellas, que la URL cambie (`/`, `/productos/5`, `/checkout`), y todo eso **sin recargar** la página. Para eso sirve el **routing**, y lo conseguimos con una librería llamada **React Router**.

Este módulo es bastante **mecánico**: es, sobre todo, conocer las piezas que React Router te da y cómo encajan. Conceptualmente es más tranquilo que el estado o los efectos.

**Qué cubre este módulo:**
- Por qué una SPA necesita una librería de routing.
- Instalar y activar **React Router** (`BrowserRouter`).
- Definir **rutas** (`Routes` / `Route`) y mostrar la página según la URL.
- Navegar sin recargar con **`Link`**.
- Un **layout** común (barra que se mantiene en todas las páginas) con **`Outlet`**.
- Leer **parámetros de la URL** con **`useParams`** (la página de detalle).
- Navegar desde el código con **`useNavigate`**.

**Qué NO cubre (viene después):**
- El **formulario de checkout** (formularios controlados con varios campos) → Módulo 8.
- Compartir el carrito entre páginas lejanas (**Context**) → Módulo 9.

---

### 🎒 De dónde venís

Asumo los Módulos 1 a 6. Para este módulo es clave:
- Del **Módulo 1**: una **SPA** carga **una sola página HTML** y nunca la recarga; el JavaScript reescribe el contenido al vuelo.
- Del **Módulo 3**: las páginas son componentes que reciben datos por **props**.
- Del **Módulo 6**: cómo el padre les pasa datos a las páginas (lifting state up).

> Recordá del Módulo 1: dijimos que una SPA "se siente fluida, como una app de celular, porque no recarga la página en cada clic". Routing es lo que hace posible esa sensación teniendo, a la vez, varias "páginas".

---
---

## 1. 🔴 El problema: una SPA no recarga, pero queremos varias páginas

Acá hay una tensión que resolver. Por un lado, en la web tradicional cada página es un archivo HTML distinto, y para ir de una a otra el navegador **recarga** todo. Por otro lado, recordá del Módulo 1: React arma una **SPA**, **una sola** página HTML que **no se recarga nunca** (esa es justamente su ventaja: fluidez).

Entonces, ¿cómo hacemos para que una app de una sola página **se comporte como si tuviera varias**? Queremos que:
- La URL cambie según dónde estés (`/`, `/productos/5`, `/checkout`).
- Se muestre un componente distinto según esa URL.
- Al navegar, **no** se recargue toda la página (que siga siendo una SPA fluida).

React, por sí solo, **no sabe hacer esto**: no tiene ningún concepto de "páginas" ni de URLs. Es una biblioteca de UI, nada más. Así que sumamos una librería especializada: **React Router**.

> 🎓 **Si te lo preguntan — "¿Por qué React necesita una librería de routing?"**
> Porque React construye una SPA (una sola página que no recarga) y no tiene un concepto propio de páginas ni de URLs. Una librería de routing como React Router agrega la capacidad de asociar URLs a componentes y navegar entre ellos sin recargar, manteniendo la fluidez de la SPA.

---

## 2. 🔴 Instalar React Router

React Router no viene con React: es una librería extra. Recordá del Módulo 2 cómo se sumaban librerías — este es uno de esos momentos. Se instala con:

```bash
npm install react-router-dom
```

Qué hace, recordando el Módulo 2: descarga la librería a `node_modules` y la anota en el `package.json` (para que cualquiera que clone el repo la obtenga con su `npm install`). La librería se llama `react-router-dom` (el "dom" es por el navegador).

Una vez instalada, te da un conjunto de **componentes** (`BrowserRouter`, `Routes`, `Route`, `Link`, `Outlet`) y **hooks** (`useParams`, `useNavigate`) que vas a importar de `'react-router-dom'`. Los vemos uno por uno.

---

## 3. 🔴 Activar el routing: `BrowserRouter`

Lo primero es **envolver toda tu app** con un componente llamado `BrowserRouter`. Esto "enciende" el routing para todo lo que esté adentro. Va en el punto de entrada (`index.js`, el del Módulo 2):

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';   // 👈 lo importo
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <BrowserRouter>      {/* 👈 envuelve toda la app */}
    <App />
  </BrowserRouter>
);
```

A partir de acá, cualquier componente dentro de `<App />` puede usar las herramientas de routing. `BrowserRouter` es el que está atento a la URL del navegador y coordina todo. Lo ponés una sola vez y te olvidás.

---

## 4. 🔴 Definir las rutas: `Routes` y `Route`

Ahora, dentro de tu app, declarás **qué componente se muestra para cada URL**. Eso se hace con dos componentes: `Routes` (el contenedor) y `Route` (cada regla).

```javascript
import { Routes, Route } from 'react-router-dom';
import Home from './features/home/Home';
import Checkout from './features/checkout/Checkout';

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/checkout" element={<Checkout />} />
    </Routes>
  );
}
```

Leé cada `Route` así: *"cuando la URL sea `path`, mostrá el componente de `element`"*.
- En `/` se muestra `<Home />`.
- En `/checkout` se muestra `<Checkout />`.

`React Router` mira la URL actual y, entre todas las `Route` dentro de `Routes`, **muestra la que coincide**. Cambiás de URL, cambia el componente. Eso es el corazón del routing.

> Fijate la sintaxis `element={<Home />}`: le pasás el componente **ya escrito como JSX** (con sus `< >`), no solo el nombre. Es la forma actual de React Router.

---

## 5. 🔴 Navegar sin recargar: `Link`

¿Cómo movés al usuario de una ruta a otra? Tu instinto de HTML diría "con un `<a href>`". **Pero no**, y la razón es importante:

```javascript
// ❌ MAL: un <a> recarga TODA la página → mata la SPA
<a href="/checkout">Ir al checkout</a>
```

Un `<a href>` hace que el navegador **recargue la página entera** — vuelve a bajar todo el JavaScript, se pierde el estado, se rompe la fluidez. Es exactamente lo que una SPA quiere evitar (Módulo 1).

En su lugar, React Router te da el componente **`Link`**, que cambia la URL y el componente **sin recargar**:

```javascript
import { Link } from 'react-router-dom';

// ✅ BIEN: <Link> navega sin recargar, mantiene la SPA fluida
<Link to="/checkout">Ir al checkout</Link>
```

Usás `to` (en vez de `href`) para indicar a dónde. Por dentro, `Link` intercepta el clic, evita la recarga, cambia la URL y le dice a React Router que muestre el componente nuevo. Visualmente para el usuario es un link común; por debajo, es navegación de SPA.

> 🎓 **Si te lo preguntan — "¿Qué diferencia hay entre `<a href>` y `<Link>`?"**
> El `<a href>` recarga toda la página: vuelve a pedir y ejecutar todo, y se pierde el estado de la app. El `<Link to>` de React Router cambia la URL y el componente mostrado **sin recargar**, manteniendo la SPA fluida y el estado intacto. En una SPA siempre se navega con `Link` (o navegación programática), no con `<a>`.

---

## 6. 🔴 Un layout común: rutas anidadas y `Outlet`

Surge un problema práctico: la **barra de navegación** (con el logo y el carrito) tiene que aparecer en **todas** las páginas. No querés copiar y pegar la navbar en `Home`, en el detalle, en el checkout. Querés escribirla **una vez** y que envuelva a todas las páginas.

La solución es un componente **`Layout`**: contiene lo que se repite (la navbar) más un hueco especial donde se va a dibujar la página actual. Ese hueco es **`Outlet`**.

```javascript
import { Outlet } from 'react-router-dom';
import Navbar from '../../components/headers/Navbar';

function Layout() {
  return (
    <>
      <Navbar />        {/* esto aparece en TODAS las páginas */}
      <Outlet />        {/* 👈 acá se dibuja la página que coincida con la URL */}
    </>
  );
}
```

`<Outlet />` es un marcador de posición: "acá va la página de turno". Para conectarlo, usás **rutas anidadas**: una `Route` "padre" con el `Layout`, y adentro las `Route` "hijas" de cada página:

```javascript
import { Routes, Route } from 'react-router-dom';

function App() {
  return (
    <Routes>
      {/* Ruta padre: el Layout (con la navbar y el Outlet) */}
      <Route path="/" element={<Layout />}>
        {/* Rutas hijas: se dibujan dentro del <Outlet/> del Layout */}
        <Route index element={<Home />} />               {/* URL: "/" */}
        <Route path="productos/:id" element={<ProductDetailPage />} />
        <Route path="checkout" element={<Checkout />} />  {/* URL: "/checkout" */}
      </Route>
    </Routes>
  );
}
```

Dos cosas para entender:

- **`<Route index element={<Home/>} />`** → la ruta **`index`** es la página por defecto del padre. Como el padre es `path="/"`, la `index` es lo que se muestra en `/` (la home). "index" = "la hija por defecto cuando estás justo en la ruta del padre".
- Cuando navegás, el `Layout` **siempre** se dibuja (con su navbar), y dentro de su `<Outlet />` se dibuja la hija que coincide con la URL. Así la navbar queda fija y solo cambia el contenido de abajo. Justo lo que queríamos.

```
   Layout (navbar SIEMPRE visible)
   ┌──────────────────────────────────┐
   │  [ Navbar: logo · carrito ]      │  ← fijo en todas las páginas
   ├──────────────────────────────────┤
   │                                  │
   │   <Outlet/>: acá cambia según    │  ← Home / Detalle / Checkout
   │   la URL                         │
   │                                  │
   └──────────────────────────────────┘
```

---

## 7. 🔴 Parámetros de URL: `useParams` (la página de detalle)

La página de detalle tiene un desafío: **¿cómo sabe qué producto mostrar?** Hay un producto distinto por cada uno, no vas a crear una ruta a mano para cada producto. La solución: la URL **lleva el dato** del producto. Por ejemplo, `/productos/5` es el detalle del producto con id 5.

Para eso se usa un **parámetro de ruta**, que se escribe con **dos puntos** en el `path`:

```javascript
<Route path="productos/:id" element={<ProductDetailPage />} />
//                    ▲ ":id" es un comodín: matchea /productos/5, /productos/42, etc.
```

El `:id` es una parte variable de la URL. Dentro del componente, lo leés con el hook **`useParams`**:

```javascript
import { useParams } from 'react-router-dom';
import { productos } from '../../mockdata/Productos';

function ProductDetailPage() {
  const { id } = useParams();          // si la URL es /productos/5 → id vale "5"

  // ⚠️ useParams devuelve STRINGS. Si los id del dato son números,
  // convertí con parseInt para comparar bien.
  const producto = productos.find((p) => p.id === parseInt(id));

  // Buena práctica: contemplar el caso de que no exista
  if (!producto) {
    return <p>Producto no encontrado.</p>;
  }

  return <h1>{producto.nombre}</h1>;
}
```

El flujo completo:
1. En la lista, cada producto es un `Link` a su detalle: `<Link to={`/productos/${producto.id}`}>`.
2. El usuario hace clic → la URL pasa a `/productos/5` → se muestra `ProductDetailPage`.
3. Adentro, `useParams()` lee `id = "5"` de la URL.
4. Buscamos ese producto y lo mostramos.

> ⚠️ El detalle del `parseInt`: `useParams` siempre te da **texto** (la URL es texto). Si comparás `"5" === 5` da falso. Por eso, si tus id son números, convertís con `parseInt(id)` antes de comparar. Es un error sutil que confunde un rato.

> 🎓 **Si te lo preguntan — "¿Cómo lee un componente un parámetro de la URL?"**
> Se define la ruta con un parámetro usando dos puntos (`path="productos/:id"`). Dentro del componente, el hook `useParams()` devuelve un objeto con esos parámetros (`const { id } = useParams()`). Hay que recordar que vienen como string, así que si se comparan con números hay que convertirlos.

---

## 8. 🟡 Navegar desde el código: `useNavigate`

`Link` sirve cuando el usuario hace clic en algo para navegar. Pero a veces querés navegar **desde el código**, como resultado de una acción: "después de agregar al carrito, llevame al inicio" o "al tocar el botón del carrito, andá al checkout". Para eso está el hook **`useNavigate`**:

```javascript
import { useNavigate } from 'react-router-dom';

function Navbar() {
  const navigate = useNavigate();   // me da una función para navegar

  const irAlCheckout = () => {
    navigate('/checkout');          // navego por código, no por un Link
  };

  return (
    <button onClick={irAlCheckout}>🛒 Ver carrito</button>
  );
}
```

La diferencia con `Link`:
- **`Link`** → un elemento clickeable en la pantalla (un "link"). El usuario lo ve y lo toca.
- **`useNavigate`** → una función que llamás **desde tu lógica**, típicamente dentro de un manejador de evento, después de hacer algo. No es un elemento visible.

Ejemplo típico: en la página de detalle, después de `agregarAlCarrito()`, hacés `navigate('/')` para volver al inicio. El usuario no tocó un link de "volver"; tu código lo llevó.

---

## 9. 🟢 Cómo encaja todo junto

La estructura completa del routing de MegaSuper, de afuera hacia adentro:

```
<BrowserRouter>                                    ← enciende el routing (en index.js)
  └── <Routes>                                     ← contenedor de reglas
        └── <Route path="/" element={<Layout/>}>   ← navbar fija + <Outlet/>
              ├── <Route index element={<Home/>} />              → URL  /
              ├── <Route path="productos/:id" element={<Detail/>}/> → /productos/5
              └── <Route path="checkout" element={<Checkout/>} />   → /checkout
```

Y cómo se conecta el viaje del usuario, juntando todo el módulo:

1. En la **Home**, cada producto es un **`Link`** a `/productos/su-id`.
2. El usuario hace clic → **sin recargar**, la URL cambia y React Router busca la `Route` que coincide.
3. El **`Layout`** sigue ahí (navbar fija); dentro de su **`Outlet`** se dibuja la página de detalle.
4. La página de detalle usa **`useParams`** para saber qué producto mostrar.
5. Cuando el usuario agrega al carrito, **`useNavigate`** lo lleva de vuelta al inicio.

Todo eso ocurriendo en **una sola página HTML** que **nunca se recarga** — la SPA del Módulo 1, ahora con varias "páginas" de verdad.

> Un apunte honesto que conecta con lo que viene: en una app real, estas páginas necesitan **compartir datos** entre sí — por ejemplo, el **carrito**: el detalle lo modifica, la navbar muestra el contador, el checkout lo lista. Con lo que sabés (Módulo 6, lifting state up), eso se haría poniendo el carrito en lo más alto y **pasándolo por props** a través de las rutas hasta cada página. Funciona... pero es justo el **prop drilling** del que hablamos: tedioso y repetido. Esa molestia es la que el **Context** (Módulo 9) viene a eliminar. Por ahora, quedate con la mecánica de la navegación.

> 🕳️ **Madriguera — otras piezas de React Router**
> Existen más herramientas: `NavLink` (un `Link` que sabe si está "activo", útil para resaltar la sección actual), `useSearchParams` (para leer parámetros de búsqueda tipo `?orden=precio`), y una API alternativa basada en configuración (`createBrowserRouter`) con carga de datos integrada. Son extensiones del mismo modelo.
> *Volvé al camino — con lo de este módulo manejás la navegación de una app completa.*

---
---

## ✅ Checkpoint — ¿lo agarraste?

Sin mirar, con tus palabras (las respuestas se resuelven en el complemento del recorrido, o me preguntás):

1. ¿Por qué una SPA necesita una librería de routing? ¿React trae routing por sí solo?
2. ¿Qué comando instala React Router? ¿Qué hace ese comando (recordá el Módulo 2)?
3. ¿Para qué sirve `BrowserRouter` y dónde se pone?
4. ¿Qué hacen `Routes` y `Route`? Escribí una ruta que muestre `<Checkout/>` en la URL `/checkout`.
5. ¿Por qué **no** se navega con `<a href>` en una SPA? ¿Qué se usa en su lugar y por qué?
6. ¿Qué problema resuelve el **`Layout`** con **`Outlet`**? ¿Qué se dibuja en el `Outlet`?
7. ¿Qué es una **ruta `index`**?
8. ¿Cómo hacés que una ruta tenga un parámetro variable (como el id de un producto)? ¿Con qué hook lo leés adentro del componente?
9. ¿Por qué hay que usar `parseInt` con lo que devuelve `useParams` al comparar con id numéricos?
10. ¿Cuál es la diferencia entre `Link` y `useNavigate`? Dá un caso de uso de cada uno.

---

## 👉 Qué viene en el Módulo 8

Ya tenés una app con varias páginas y navegación. Una de esas páginas es el **checkout**, que tiene un **formulario**. En el Módulo 8 nos metemos de lleno con los formularios:

- Un **formulario controlado** con **varios campos** (nombre, dirección, etc.), extendiendo el input controlado del Módulo 4.
- Manejar todos los campos de forma ordenada (estado de formulario).
- **Validación**: chequear que los datos estén bien antes de enviar.
- Habilitar/deshabilitar el botón de envío según el formulario (`disabled`).
- Qué hacer al **enviar** el formulario.

Los formularios son el corazón de cualquier app que recibe datos del usuario — y tu TP los va a necesitar sí o sí. Te espero en el Módulo 8.

---

**FIN DEL MÓDULO 7**
