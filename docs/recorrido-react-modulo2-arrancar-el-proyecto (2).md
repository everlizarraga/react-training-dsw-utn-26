# 🧭 Recorrido React — Módulo 2

## Arrancar el proyecto: de cero a una app corriendo con tu primer componente

---

### 📍 Sobre este documento

Acá tocás la terminal por primera vez. El objetivo es que termines este módulo con **una app de React corriendo en tu navegador** y entendiendo **qué hace cada comando y cada archivo** — nada de comandos mágicos que copiás sin saber qué pasan.

Le presto atención especial a una cosa: **qué se instala, cuándo y por qué.** Si alguna vez te pasó que "algo se rompió y no sabías por qué" al levantar un proyecto, casi siempre la causa es una de las dos cosas que explico en las secciones 4 y 6. Las vamos a blindar.

**Qué cubre este módulo:**
- Qué necesitás instalado antes de empezar (Node y npm).
- Cómo se crea un proyecto de React, con cada comando explicado.
- La estructura de carpetas: qué es cada cosa.
- El `.gitignore`: qué se sube a un repositorio y qué no (y por qué).
- Cómo instalar dependencias nuevas, cuándo y por qué.
- Cómo levantar la app y verla en el navegador.
- Cómo arranca la app por dentro (la "cadena de booteo").
- La anatomía de un **componente** y el primer contacto con **JSX**.

**Qué NO cubre (viene después):**
- Props, composición de componentes, listas, renderizado condicional → Módulo 3.
- Estado, hooks, eventos → Módulo 4 en adelante.

---

### 🎒 De dónde venís

Asumo que entendiste el Módulo 1 (qué es React, declarativo, Virtual DOM, SPA). Asumo HTML/CSS/JS y que instalaste alguna dependencia con `npm` alguna vez. Sobre eso seguimos.

> Recordá que en el Módulo 1 vimos que React arma una **SPA** que corre en el navegador. Todo lo de este módulo es para poner esa SPA a andar en tu máquina.

---
---

## 1. 🔴 Lo que necesitás antes de empezar: Node y npm

Para trabajar con React necesitás dos cosas instaladas en tu computadora. Probablemente ya las tengas si usaste `npm` antes, pero vamos a verificar.

- **Node.js** es un programa que permite ejecutar JavaScript **fuera del navegador**, directo en tu sistema operativo. Lo necesitás porque todas las herramientas de React (la que crea el proyecto, la que lo levanta, la que lo empaqueta) son programas de JavaScript que corren con Node.
- **npm** (*Node Package Manager*) es el gestor de paquetes que viene **junto con** Node. Es el que descarga e instala las librerías que tu proyecto necesita (React es una de ellas). Esto ya lo conocés de cuando instalaste alguna dependencia.

Para verificar que los tenés, abrí una terminal y escribí:

```bash
node -v    # te muestra la versión de Node, ej: v20.11.0
npm -v     # te muestra la versión de npm, ej: 10.2.4
```

Si cada comando te devuelve un número de versión, estás listo. Si te dice "command not found" o similar, necesitás instalar Node (con eso viene npm). Una versión reciente **LTS** (*Long Term Support*, la versión "estable recomendada") alcanza y sobra.

---

## 2. 🔴 Crear el proyecto

Existen herramientas que te arman un proyecto de React entero con un solo comando: te crean las carpetas, los archivos base y te descargan React ya configurado. La que vamos a usar se llama **Create React App**. Te parás en la carpeta donde querés crear el proyecto y corrés:

```bash
npx create-react-app megasuper
```

Desarmemos el comando, porque cada parte tiene su porqué:

- **`npx`** → es un ayudante que viene con npm. Sirve para **ejecutar una herramienta una sola vez sin instalarla permanentemente** en tu sistema. Como "crear el proyecto" lo hacés una vez y nunca más, no tiene sentido instalar la herramienta para siempre; `npx` la baja, la usa y listo.
- **`create-react-app`** → es la herramienta que arma el esqueleto del proyecto.
- **`megasuper`** → es el nombre de la carpeta del proyecto que se va a crear. Lo elegís vos.

Este comando tarda un rato (un par de minutos): está creando archivos **y además descargando React y todas sus piezas de internet.** Cuando termina, tenés una carpeta `megasuper` con un proyecto de React funcional adentro.

> 🕳️ **Madriguera — Create React App está discontinuado; hoy se usa Vite**
> Create React App es la herramienta con la que está hecho el proyecto que vamos a estudiar, así que es la que usamos. Pero te lo digo derecho: el **14 de febrero de 2025 el equipo de React deprecó oficialmente CRA**. Ya no se recomienda para proyectos nuevos; quedó en "modo mantenimiento". La herramienta moderna para crear una SPA como la nuestra se llama **Vite** (más rápida porque no usa Webpack). Si algún día querés armar un proyecto nuevo, el equivalente sería:
>
> ```bash
> npm create vite@latest mi-app -- --template react   # crear
> cd mi-app
> npm install                                          # instalar dependencias
> npm run dev                                           # levantar (en vez de npm start)
> ```
>
> Las **únicas** diferencias prácticas con CRA que vas a notar: se levanta con `npm run dev` (no `npm start`), el puerto por defecto es `5173` (no `3000`), el punto de entrada se llama `main.jsx` (no `index.js`), y Vite **no** te trae las dependencias instaladas hasta que corrés `npm install`. **Todo lo demás del recorrido —componentes, props, estado, hooks, routing, todo— es exactamente igual con Vite.** El React de adentro no cambia; cambia solo el andamio que lo arma.
> *Volvé al camino — seguimos con Create React App porque es lo que usa nuestro proyecto. Si querés el panorama de CRA vs Vite vs Next.js en su lugar, está el anexo del ecosistema.*

> 🕳️ **Madriguera — ¿no había que hacer `npm init` antes?**
> No, y es una confusión común. `npm init -y` crea **solo** un `package.json` casi vacío, para arrancar un proyecto **a mano**. `create-react-app` ya hace ese `init` por vos y mucho más: te genera el `package.json` **ya armado** (con React y los scripts), más toda la estructura. Si corrieras `npm init` antes, ese `package.json` lo terminaría reemplazando `create-react-app` — trabajo al pedo. Regla: **`npm init`** = proyecto a mano desde cero; **`create-react-app`** = proyecto llave en mano (te arma todo, incluido el `package.json`). Y por eso al clonar un repo corrés `npm install` (el `package.json` ya existe), no `npm init`.
> *Volvé al camino — con `create-react-app` solo, alcanza.*

---

## 3. 🔴 Qué se creó: la estructura de carpetas

Entrá a la carpeta del proyecto (`cd megasuper`) y mirá lo que se generó. Esta es la estructura, con lo que importa marcado:

```
megasuper/
├── node_modules/        ← TODAS las librerías descargadas (gigante, no se toca a mano)
├── public/
│   ├── index.html       ← la ÚNICA página HTML real de la SPA
│   ├── favicon.ico      ← el iconito de la pestaña
│   └── manifest.json    ← metadatos de la app (nombre, íconos)
├── src/                 ← ACÁ vivís vos: todo tu código React va acá
│   ├── index.js         ← el punto de entrada (arranca todo)
│   ├── index.css        ← estilos globales
│   ├── App.js           ← el primer componente, la "raíz" de tu app
│   └── App.css          ← estilos del componente App
├── package.json         ← la "ficha técnica" del proyecto (nombre, deps, comandos)
├── package-lock.json    ← versiones exactas de cada dependencia
└── .gitignore           ← qué archivos NO se suben al repositorio
```

Lo que tenés que tener clarísimo:

- **`src/`** es tu zona de trabajo. **El 99% del tiempo vas a estar acá.** Todo tu código React vive en `src`.
- **`public/index.html`** es la única página HTML de toda la SPA (¿te acordás del Módulo 1, "Single Page Application"?). Acá React va a inyectar todo. Ya la miramos en detalle en la sección 7.
- **`node_modules/`** es donde se guardan las librerías descargadas. Pesa muchísimo (miles de archivos) y **nunca la editás a mano**. Es importante para lo que viene en la sección 4.
- **`package.json`** es la ficha técnica: tiene el nombre del proyecto, la **lista de dependencias** y los **comandos** disponibles. Lo vas a abrir seguido.

> 💡 **Detalle que confunde:** vas a ver archivos terminados en `.js` y otros en `.jsx`. Para React son lo mismo; `.jsx` solo deja explícito "este archivo tiene JSX adentro". Create React App genera `.js`, pero mucha gente los renombra a `.jsx`. No te compliques: si ves `App.js` o `App.jsx`, es la misma idea.

---

## 4. 🔴 El `.gitignore`: qué se sube al repositorio y qué no

Esta es tu pregunta, y es de las cosas que nadie aclara y deja un agujero. **No, no se sube todo el proyecto a un repositorio.**

El `.gitignore` es un archivo que le dice a Git (el sistema de control de versiones) **qué archivos y carpetas ignorar**, es decir, qué NO versionar ni subir. Este es el `.gitignore` real de un proyecto de React, con lo importante:

```bash
# dependencies
/node_modules     ← ¡ESTA es la clave! Las librerías NO se suben.

# production
/build            ← la versión empaquetada para producción, tampoco se sube.

# testing
/coverage         ← reportes de tests, tampoco.

# misc
.env.local        ← archivos con claves/secretos, NUNCA se suben.
```

La línea que resuelve tu duda de fondo es **`/node_modules`**. Las librerías descargadas **no viajan** en el repositorio. ¿Por qué?

- Pesan demasiado (miles de archivos, cientos de megas). Subir eso sería absurdo.
- Son recuperables: no hace falta guardarlas, porque se pueden volver a descargar.

¿Y cómo se recuperan? Acá está la pieza que explica medio mundo de los "se me rompió":

> **El proyecto no viaja con las librerías adentro; viaja con la _lista_ de librerías.** Esa lista es el `package.json`. Cuando alguien clona o descarga un repo de React, lo primero que tiene que correr es **`npm install`**: ese comando lee el `package.json`, ve qué librerías necesita el proyecto, y **las vuelve a descargar todas**, reconstruyendo la carpeta `node_modules` desde cero.

```
Lo que SÍ se versiona/sube:        Lo que NO (lo arma npm install):
  src/  (tu código)                  node_modules/  (las librerías)
  public/                            build/
  package.json  (la lista de deps)
  package-lock.json
  .gitignore
```

> 🎓 **Si te lo preguntan — "¿Por qué `node_modules` no se sube al repo?"**
> Porque pesa demasiado y es recuperable. El repositorio guarda la *lista* de dependencias en el `package.json`, no las dependencias en sí. Al clonar el proyecto se corre `npm install`, que lee esa lista y reconstruye `node_modules` descargando todo de nuevo.

**Por qué esto importa para vos:** si alguna vez descargaste/clonaste un proyecto y "no andaba" o "faltaba algo", lo más probable es que **no se corrió `npm install` primero**, o que se quiso usar una librería que nunca se instaló. Lo vemos en la sección 6.

---

## 5. 🟡 Dependencias: las que vienen y las que sumás

Cuando creaste el proyecto con Create React App, ya te vinieron instaladas las librerías esenciales: **React** (`react`), la pieza que conecta React con el navegador (`react-dom`) y la herramienta interna que levanta y empaqueta el proyecto (`react-scripts`). Con eso ya tenés una app funcionando.

Pero a medida que tu app crece, vas a necesitar **librerías extra** que no vienen por defecto: una para navegar entre pantallas, una para componentes visuales lindos, una para íconos, etc. Esas las instalás vos, cuando las necesitás, con:

```bash
npm install nombre-de-la-libreria
# ejemplo genérico (no lo corras todavía):
# npm install alguna-libreria
```

Qué hace `npm install <libreria>`:
1. Descarga la librería a `node_modules`.
2. **La anota en el `package.json`** (en la lista de dependencias), para que quede registrada y cualquiera que clone el repo la obtenga con su `npm install`.

> **La regla de oro contra los "se rompió":** si vas a usar una librería, primero la instalás con `npm install`. Si la usás en el código sin instalarla, la app rompe con un error tipo "no se encontró el módulo". No es un misterio: es que falta el `npm install`.

En este recorrido, **cada vez que necesitemos una librería nueva, te voy a dar el comando exacto en el momento, con la explicación de qué problema resuelve.** Así nunca te aparece una dependencia de la nada. (Por ejemplo: la de navegación entre pantallas la instalamos en el Módulo 6; la de componentes visuales, en el Módulo 8.)

---

## 6. 🔴 Levantar la app y verla corriendo

Llegó el momento. Parado en la carpeta del proyecto, corré:

```bash
npm start
```

Esto enciende un **servidor de desarrollo**: un programa que compila tu app y la sirve localmente. En unos segundos se te abre el navegador en **`http://localhost:3000`** y ves la app corriendo (Create React App trae una pantalla de bienvenida por defecto).

- **`localhost`** significa "tu propia máquina". **`3000`** es el puerto donde quedó escuchando el servidor de desarrollo.
- La gracia: **hot reload** (recarga en caliente). Mientras `npm start` está corriendo, cada vez que guardás un cambio en tu código, el navegador se actualiza **solo**, al instante. No tenés que recargar a mano. Para frenar el servidor, en la terminal hacés `Ctrl + C`.

Estos son los comandos del proyecto (están definidos en el `package.json`):

| Comando | Qué hace | Marca |
|---|---|---|
| `npm start` | Levanta el servidor de desarrollo (el que usás todo el tiempo) | 🔴 |
| `npm run build` | Empaqueta la app optimizada para producción (en la carpeta `build/`) | 🟢 |
| `npm test` | Corre los tests del proyecto | 🟢 |

**Y el ritual más importante de todos, el que evita el 90% de los dolores de cabeza:**

```bash
# Cuando clonás/descargás un proyecto de React por primera vez:
npm install     # 1) reconstruye node_modules desde el package.json
npm start       # 2) recién ahora levantás la app
```

Si saltás el `npm install`, la app no tiene las librerías (porque, recordá, `node_modules` no viaja en el repo) y rompe. **Primero `install`, después `start`.** Grabate esa secuencia.

> 🕳️ **Madriguera — `npm test` y `npm run eject`**
> `npm test` corre la batería de tests automáticos. `npm run eject` es un comando de una sola vía que "abre el capó" del proyecto para configurar todo a mano — **no lo corras nunca por accidente, no tiene vuelta atrás.**
> *Volvé al camino — con `start` te alcanza para todo el recorrido.*

---

## 7. 🔴 Cómo arranca la app por dentro: la cadena de booteo

Esto es clave para que React no te parezca magia. ¿Cómo hace tu código React para terminar dibujado en la pantalla? Hay una cadena de tres eslabones. Vamos a verla con los archivos reales.

**Eslabón 1 — `public/index.html`.** La única página HTML. Si la mirás, casi no tiene nada. La línea que importa es una sola:

```html
<body>
  <div id="root"></div>   <!-- 👈 acá React va a inyectar TODA la app -->
</body>
```

Ese `<div id="root">` está **vacío**. Es un cajón vacío esperando que React lo llene. Toda tu aplicación va a vivir dentro de ese div.

**Eslabón 2 — `src/index.js`.** Es el **punto de entrada**: el primer código que se ejecuta. Su trabajo es agarrar tu app y meterla en ese cajón vacío:

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';        // 👈 importa tu componente raíz

// 1) Busca el cajón vacío (#root) en el HTML
const root = ReactDOM.createRoot(document.getElementById('root'));

// 2) Dibuja tu app <App /> dentro de ese cajón
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

En palabras: *"buscá el div con id `root`, y renderizá ahí adentro el componente `App`."* Eso es lo que conecta tu código React con la página real.

> `<React.StrictMode>` es un envoltorio opcional que React te pone por defecto: activa chequeos extra durante el desarrollo para avisarte de problemas potenciales. No cambia lo que ves; solo te ayuda a programar mejor. Por ahora ignoralo, está ahí cuidándote.

**Eslabón 3 — `src/App.js`.** Es tu **componente raíz**, el primero de tu app. Todo lo que construyas va a colgar de `App`. Lo vemos en la próxima sección.

La cadena completa, en un diagrama:

```
  public/index.html              src/index.js                  src/App.js
 ┌────────────────────┐        ┌──────────────────┐         ┌──────────────┐
 │  <div id="root">   │  ◄───  │ createRoot(#root)│  ◄───   │  componente  │
 │     (vacío)        │        │   .render(<App/>)│         │     App      │
 └────────────────────┘        └──────────────────┘         └──────────────┘
      el cajón                  busca el cajón y              lo que se
      en el HTML                mete la app adentro           dibuja
```

> 🎓 **Si te lo preguntan — "¿Cómo termina una app de React en la pantalla?"**
> El HTML tiene un único div vacío (`#root`). El archivo de entrada (`index.js`) usa `createRoot` para apuntar a ese div y `render` para dibujar el componente raíz (`App`) adentro. A partir de ahí, React maneja todo lo que pasa dentro de ese div.

---

## 8. 🔴 La anatomía de un componente

Ya viste la palabra "componente" varias veces. Ahora la concretamos. En el Módulo 1 dijimos que React arma la UI con **piezas reutilizables**: esas piezas son los componentes.

> **Un componente es una función de JavaScript que devuelve la descripción de un pedazo de interfaz.**

Sí: es una función común de JS. Lo único especial es **qué devuelve**: en vez de devolver un número o un texto, devuelve *marcado* (algo que se parece a HTML). Mirá el componente más simple posible, con sabor a tienda:

```javascript
// Bienvenida.js

// 1) Es una función. Su nombre empieza con MAYÚSCULA (regla de React).
function Bienvenida() {
  // 2) Devuelve "markup": esto que parece HTML se llama JSX (sección 9).
  return <h1>Bienvenido a MegaSuper</h1>;
}

// 3) Se exporta para poder usarlo desde otros archivos.
export default Bienvenida;
```

Tres cosas para fijar:

1. **Es una función** que devuelve marcado. Nada más.
2. **El nombre empieza con mayúscula.** `Bienvenida`, no `bienvenida`. Esto es **obligatorio** en React: así distingue tus componentes (mayúscula) de las etiquetas HTML normales (minúscula, como `<h1>`). Si lo escribís en minúscula, React no lo trata como componente.
3. **Se exporta** con `export default` para poder importarlo y usarlo en otro lado.

### Cómo se usa un componente

Una vez creado, lo usás **como si fuera una etiqueta HTML nueva**, escribiendo su nombre entre `< >`:

```javascript
// App.js
import Bienvenida from './Bienvenida';   // lo traigo

function App() {
  return (
    <div>
      <Bienvenida />     {/* 👈 lo uso como si fuera una etiqueta */}
      <Bienvenida />     {/* 👈 y lo puedo REUSAR las veces que quiera */}
    </div>
  );
}

export default App;
```

Eso es la idea de "piezas reutilizables": definís `Bienvenida` una vez y la usás todas las veces que quieras, en cualquier parte. Acá es donde empieza a tener sentido lo de armar la UI con bloques tipo Lego.

> 💡 Fijate que `<Bienvenida />` se cierra solo con `/>`. Cuando un componente (o etiqueta) no tiene nada adentro, se cierra así. Es una de las reglas de JSX, que vemos ahora.

---

## 9. 🔴 JSX: el "HTML dentro de JavaScript"

Eso que tus componentes devuelven —lo que parece HTML pero está adentro de un archivo `.js`— se llama **JSX** (*JavaScript XML*). Es una sintaxis que **te deja escribir marcado parecido a HTML directamente en tu código JavaScript.** No es HTML de verdad ni es un string: es una forma cómoda de describir interfaz que después React transforma en instrucciones reales.

Se parece tanto a HTML que casi todo lo que sabés se traslada igual. Pero tiene **un puñado de reglas propias** que muerden a todos los principiantes. Estas cuatro son las que te vas a cruzar enseguida:

### Regla 1 — Tenés que devolver UN solo elemento padre

Un componente no puede devolver dos elementos "sueltos" lado a lado. Esto **rompe**:

```javascript
// ❌ MAL: dos elementos hermanos sueltos
return (
  <h1>MegaSuper</h1>
  <p>La mejor tienda</p>
);
```

Tenés que envolverlos en **un** contenedor. Puede ser un `<div>`:

```javascript
// ✅ BIEN: todo dentro de un único padre
return (
  <div>
    <h1>MegaSuper</h1>
    <p>La mejor tienda</p>
  </div>
);
```

Si no querés agregar un `<div>` de más al HTML, React te da un envoltorio invisible llamado **Fragment**, que se escribe con etiquetas vacías `<>...</>`:

```javascript
// ✅ BIEN: Fragment, un padre que no deja rastro en el HTML
return (
  <>
    <h1>MegaSuper</h1>
    <p>La mejor tienda</p>
  </>
);
```

### Regla 2 — Se escribe `className`, no `class`

En HTML usás `class="..."` para los estilos. En JSX, como estás dentro de JavaScript y `class` es una palabra reservada de JS, se usa **`className`**:

```javascript
// HTML normal:   <h1 class="titulo">...</h1>
// En JSX:        <h1 className="titulo">...</h1>
return <h1 className="titulo">Bienvenido a MegaSuper</h1>;
```

### Regla 3 — Para meter JavaScript, usás llaves `{ }`

Esta es la que le da poder al JSX. Si querés insertar **un valor de JavaScript** (una variable, una cuenta, lo que sea) en medio del marcado, lo ponés entre **llaves**:

```javascript
function Bienvenida() {
  const nombreTienda = "MegaSuper";
  const productos = 12;

  return (
    <div>
      <h1>Bienvenido a {nombreTienda}</h1>      {/* mete la variable */}
      <p>Tenemos {productos} productos</p>       {/* mete otra variable */}
      <p>El doble sería {productos * 2}</p>      {/* hasta una cuenta */}
    </div>
  );
}
```

Todo lo que va entre `{ }` es JavaScript de verdad ejecutándose ahí. Esto es lo que hace que el marcado sea **dinámico**: en vez de texto fijo, mostrás valores que vienen de tu código. (En el Módulo 1 hablamos de esto: la UI que refleja datos. Las llaves son el puente.)

### Regla 4 — Las etiquetas sin contenido se cierran solas

En HTML podés escribir `<img>` o `<br>` sin cierre. En JSX, **toda etiqueta tiene que cerrarse**. Las que no tienen contenido se cierran con `/>`:

```javascript
// HTML:   <img src="logo.png">      <br>
// JSX:    <img src="logo.png" />    <br />
```

Por eso tus componentes sin contenido también se escriben `<Bienvenida />` (lo viste en la sección 8).

> 🕳️ **Madriguera — hay más reglas de JSX**
> Existen otras diferencias menores (por ejemplo, `for` de los `<label>` se escribe `htmlFor`, y los atributos van en *camelCase* como `onClick`). Te las voy a ir señalando cuando aparezcan, así no te abruman todas juntas.
> *Volvé al camino — con estas cuatro reglas arrancás sin problemas.*

> 🎓 **Si te lo preguntan — "¿Qué es JSX?"**
> Es una sintaxis que permite escribir marcado parecido a HTML directamente dentro del código JavaScript de React. No es HTML real ni un string: React lo transforma en instrucciones para construir la interfaz. Permite insertar valores de JavaScript usando llaves `{ }`, lo que hace que el marcado sea dinámico.

---

## 10. 🟢 Cómo encaja todo junto

La foto completa del módulo, en un párrafo:

> Para trabajar con React necesitás **Node y npm** (sección 1). Con **Create React App** creás el proyecto, que te arma las carpetas y descarga React (sección 2). Tu código vive en **`src/`**; las librerías descargadas viven en **`node_modules`**, que **no se sube al repo** porque pesa mucho y se reconstruye con **`npm install`** leyendo el `package.json` (secciones 3 y 4). Las librerías extra las sumás con `npm install <libreria>` cuando las necesitás (sección 5). Levantás la app con **`npm start`** y la ves en `localhost:3000` con recarga automática (sección 6). Por dentro, el HTML tiene un div vacío **`#root`**, y **`index.js`** mete ahí tu componente **`App`** (sección 7). Un **componente** es una función que devuelve **JSX** (secciones 8 y 9), y JSX es marcado parecido a HTML, con reglas propias, donde insertás JavaScript con llaves `{ }`.

Si podés contar ese párrafo con tus palabras, el módulo está aprobado.

---
---

## ✅ Checkpoint — ¿lo agarraste?

Sin mirar el documento, con tus palabras (las respuestas se resuelven en el complemento del recorrido, o me preguntás):

1. ¿Qué son **Node** y **npm**, y por qué los necesitás para React? ¿Cómo verificás que los tenés?
2. ¿Qué hace `npx create-react-app megasuper`? ¿Por qué `npx` y no `npm install`?
3. En la estructura del proyecto, ¿qué hay en `src/`? ¿Y en `public/`? ¿Para qué sirve el `package.json`?
4. ¿Por qué `node_modules` **no** se sube a un repositorio? Si no se sube, ¿cómo lo recupera otra persona que clona el proyecto?
5. Tu compañero clonó el repo y "no le anda nada". ¿Cuál es la primera cosa que le preguntás/decís que haga?
6. ¿Qué comando levanta la app para desarrollo? ¿En qué dirección la ves? ¿Qué es el "hot reload"?
7. Contá la **cadena de booteo**: ¿cómo termina tu componente `App` dibujado en la página? Nombrá los tres eslabones.
8. ¿Qué es un **componente** en React? ¿Por qué su nombre tiene que empezar con mayúscula?
9. ¿Qué es **JSX**? ¿Cómo metés el valor de una variable de JavaScript dentro del marcado?
10. Nombrá al menos tres reglas de JSX que lo diferencian del HTML normal.

---

## 👉 Qué viene en el Módulo 3

Ya tenés una app corriendo y sabés qué es un componente y qué es JSX. En el Módulo 3 le damos profundidad a los componentes para que dejen de ser piezas estáticas:

- **Props**: cómo pasarle información a un componente para que sea de verdad reutilizable (la misma tarjeta de producto mostrando productos distintos).
- **Composición**: cómo combinar componentes chicos para armar componentes grandes.
- **Renderizar listas** con `map` (mostrar 20 productos sin escribir 20 veces lo mismo).
- **Renderizado condicional**: mostrar u ocultar cosas según los datos (`if`, ternario, `&&`).

Ahí los componentes empiezan a cobrar vida. Te espero en el Módulo 3.

---

**FIN DEL MÓDULO 2**
