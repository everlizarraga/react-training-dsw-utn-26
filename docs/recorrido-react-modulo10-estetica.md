# 🧭 Recorrido React — Módulo 10

## Estética: que la app se vea bien

---

### 📍 Sobre este documento

Este es el **último módulo del recorrido**, y el más liviano: el moño final. Ya tenés todo el React que hace que una app **funcione**; lo único que falta es que, además, **se vea bien**. Vas a conocer dos caminos para estilar: **CSS-in-JS** (escribir estilos con JavaScript) y las **librerías de componentes** (piezas visuales ya hechas, como Material UI). Y vas a ver cómo todo esto se monta sobre lo que ya sabés, sin cambiar nada de la lógica.

**Qué cubre este módulo:**
- El punto de partida: el CSS común ya funciona en React.
- **CSS-in-JS**: estilos escritos con JavaScript.
- **Librerías de componentes** (Material UI): usar piezas visuales ya hechas.
- Personalizar el aspecto global con **`ThemeProvider`** y **`createTheme`**.
- La conexión que cierra el recorrido: un campo lindo es, por dentro, un input controlado.

**Qué NO cubre:**
- Nada más — este módulo cierra la Fase 1. Lo que viene es otra fase (los apuntes maestros).

---

### 🎒 De dónde venís

Asumo los Módulos 1 a 9 (¡todo el recorrido!). Para este módulo, en particular:
- Del **Módulo 2**: que los componentes usan **`className`** y que podés tener archivos `.css`.
- Del **Módulo 3**: que los componentes reciben **props** para configurarse.
- Del **Módulo 8**: el **input controlado** (`value` + `onChange`) y la promesa de que el `TextField` lindo era, por dentro, lo mismo.
- Del **Módulo 9**: el patrón **Provider** (un componente que provee algo a todo su subárbol).

---
---

## 1. 🟡 El punto de partida: el CSS común ya funciona

Antes de las opciones nuevas, lo básico: **el CSS que ya conocés funciona en React tal cual.** Desde el Módulo 2 venís usando `className` en el JSX y podés tener archivos `.css` con tus estilos:

```javascript
import './ProductCard.css';   // tu archivo de estilos de siempre

function ProductCard({ nombre }) {
  return <div className="card">{nombre}</div>;   // className en vez de class
}
```

```css
/* ProductCard.css */
.card {
  border: 1px solid #ddd;
  padding: 16px;
  border-radius: 8px;
}
```

Esto es CSS normal, el que ya sabés. **Funciona perfecto y es una opción totalmente válida.** Entonces, ¿por qué existen otras formas? Por dos molestias del CSS común cuando la app crece:

1. **Los estilos son globales.** Si en dos archivos `.css` distintos definís una clase `.card`, **chocan** entre sí (la última que carga pisa a la otra). En una app grande, evitar choques de nombres se vuelve un problema.
2. **Son estáticos.** Cambiar un estilo según una prop o el estado (ej: el botón rojo si hay error, verde si está ok) es incómodo con CSS puro.

Las dos opciones que siguen nacen para resolver esto. Ninguna es "obligatoria": son comodidades.

---

## 2. 🔴 CSS-in-JS: estilos escritos con JavaScript

**CSS-in-JS** es un patrón donde escribís los estilos **con JavaScript, dentro de tu código de componentes**, en lugar de en archivos `.css` separados. No es parte de React: lo proveen librerías de terceros (las más conocidas son **emotion** y **styled-components**).

La idea con `styled-components` se ve así: creás un componente que **ya trae sus estilos puestos**:

```javascript
import styled from 'styled-components';

// Creo un componente "botón con estilos". Los estilos van en el `template`.
const BotonComprar = styled.button`
  background: #ff8c00;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
`;

// Y lo uso como cualquier componente de React:
function ProductCard() {
  return <BotonComprar>Comprar</BotonComprar>;
}
```

Qué te da esto sobre el CSS común:

- **Estilos encapsulados.** `styled-components` genera **nombres de clase únicos automáticamente** por detrás, así que **nunca chocan** con los de otro componente. Adiós al problema 1 de la sección anterior.
- **Estilos dinámicos.** Como es JavaScript, podés cambiar los estilos según las props o el estado fácilmente.
- **Todo junto.** El estilo vive al lado del componente, no en un archivo aparte que tenés que ir a buscar.

> 🎓 **Si te lo preguntan — "¿Qué es CSS-in-JS y qué problema resuelve?"**
> Es un patrón donde los estilos se escriben con JavaScript dentro del código de los componentes, en lugar de archivos `.css` separados, usando librerías como emotion o styled-components. Resuelve dos problemas del CSS global: genera nombres de clase únicos (evita choques entre componentes) y permite estilos dinámicos basados en props o estado.

> 🕳️ **Madriguera — otras formas de estilar**
> Hay más opciones populares: **Tailwind CSS** (clases utilitarias pre-hechas que ponés directo en el `className`), **CSS Modules** (archivos `.css` con alcance local automático). Cada una tiene su filosofía; todas resuelven, a su manera, el problema del CSS global.
> *Volvé al camino — con conocer el concepto de CSS-in-JS alcanza para entender lo que viene.*

---

## 3. 🔴 Librerías de componentes: piezas ya hechas (Material UI)

Acá está la otra gran comodidad, y la que más vas a usar. En vez de construir cada botón, tarjeta o campo **desde cero** con tu propio CSS, usás una **librería de componentes**: una colección de piezas visuales **ya hechas, pulidas y listas para usar.**

La que usa nuestro proyecto se llama **Material UI** (o **MUI**). Se instala como cualquier librería (recordá el Módulo 2: `npm install @mui/material`) y te da componentes que importás y usás:

```javascript
import { Button, Card, TextField } from '@mui/material';

function Ejemplo() {
  return (
    <Card>
      <TextField label="Nombre" />
      <Button variant="contained">Comprar</Button>
    </Card>
  );
}
```

Eso te da un botón, una tarjeta y un campo **profesionales y lindos**, sin escribir una línea de CSS. Y fijate algo importante: **estos son componentes de React comunes** — los configurás con **props** (Módulo 3). Por ejemplo, `variant="contained"` le dice al botón que sea relleno; `variant="outlined"` lo haría con solo el borde. Toda la personalización pasa por props, exactamente como aprendiste.

¿Por qué usar una librería así?
- **No reinventás la rueda.** Botones, formularios, diálogos, menús: ya están hechos.
- **Consistencia.** Toda la app se ve coherente porque las piezas vienen del mismo set.
- **Accesibilidad y detalles** (foco, teclado, responsive) ya resueltos por la librería.

> 🎓 **Si te lo preguntan — "¿Por qué usar una librería de componentes?"**
> Porque provee componentes visuales ya hechos, pulidos y consistentes (botones, tarjetas, campos, diálogos), lo que evita construir y estilar todo desde cero. Aseguran consistencia visual y resuelven detalles de accesibilidad y comportamiento. Se usan como componentes de React normales, configurados por props.

> 🕳️ **Madriguera — otras librerías de componentes**
> Material UI no es la única. Están también **React Bootstrap** (la versión React del Bootstrap clásico), **Ant Design**, **Chakra UI**, entre otras. Todas dan la misma idea (componentes listos), con estéticas distintas. De hecho, un proyecto puede usar más de una.
> *Volvé al camino — entendiendo MUI, las demás se aprenden rápido porque siguen la misma lógica.*

---

## 4. 🟡 Personalizar el aspecto: `ThemeProvider` y `createTheme`

Las librerías como MUI vienen con colores por defecto (un azul típico). Pero MegaSuper tiene su identidad — pongamos, un naranja. ¿Cómo hacés que **todos** los componentes de MUI usen tus colores y tu fuente, sin configurarlos uno por uno?

Definís un **tema** con `createTheme` y envolvés la app con `ThemeProvider`:

```javascript
import { createTheme, ThemeProvider } from '@mui/material';

const theme = createTheme({
  palette: {
    primary: { main: '#ff8c00' },          // el naranja de MegaSuper
  },
  typography: {
    fontFamily: "'Poppins', sans-serif",   // la fuente de la marca
  },
});

function App() {
  return (
    <ThemeProvider theme={theme}>    {/* 👈 todos los componentes MUI usan este tema */}
      {/* ...toda la app... */}
    </ThemeProvider>
  );
}
```

A partir de ahí, cada `Button`, `TextField`, etc. de MUI usa tu naranja y tu fuente. Un solo lugar define el aspecto de toda la app — **única fuente de verdad** para el estilo.

> ¿Te suena el patrón? `ThemeProvider` envuelve la app y le provee el tema a todos los componentes de adentro. Es **exactamente la idea del Provider del Módulo 9**: un componente que provee algo (en este caso, el tema) a todo su subárbol. MUI usa Context por debajo para esto. Otra pieza del recorrido que se conecta.

---

## 5. 🔴 La conexión que cierra todo: un campo lindo es un input controlado

Y acá está la promesa que te hice en el Módulo 8, cumplida. Cuando en el formulario de checkout cambiás el `<input>` pelado por un `<TextField>` de MUI, mirá lo que pasa:

```javascript
// Lo que aprendiste en el Módulo 8 (input controlado):
<input name="nombre" value={form.nombre} onChange={manejarCambio} />

// La versión "linda" con MUI:
<TextField name="nombre" value={form.nombre} onChange={manejarCambio} label="Nombre" />
```

**Es el mismo `value`, el mismo `onChange`, el mismo estado, la misma lógica.** Lo único que cambió es que `TextField` se ve profesional (con su label flotante, sus bordes, sus animaciones) en lugar de un input gris. Por dentro, `TextField` **es** un input controlado, manejado exactamente como el del Módulo 8.

Esta es la idea más importante para llevarte de todo el módulo:

> **Las librerías de componentes y el CSS-in-JS son maquillaje sobre el React que ya sabés.** No cambian cómo funciona el estado, las props, los eventos, los formularios ni nada de lo que aprendiste. Le ponen un vestido lindo a la misma lógica. Por eso este es el módulo más liviano: no hay un concepto nuevo de fondo, hay una capa estética encima de cimientos que ya tenés sólidos.

---

## 6. 🟢 Cierre del recorrido

Mirá para atrás un segundo. Arrancaste el Módulo 1 sin saber qué era React y por qué existía. Ahora tenés el **panorama completo**:

| Módulo | Lo que dominás |
|---|---|
| 1 | Por qué React: declarativo, Virtual DOM, SPA |
| 2 | Crear un proyecto, estructura, levantarlo, componentes y JSX |
| 3 | Props, composición, listas (`map`), renderizado condicional |
| 4 | Estado, `useState`, ciclo de render, eventos, inputs controlados |
| 5 | `useEffect`, datos de un servicio, estados de carga |
| 6 | Compartir estado: lifting state up y comunicación hijo→padre |
| 7 | Routing: varias páginas, navegación, parámetros de URL |
| 8 | Formularios controlados con validación |
| 9 | Estado global: Context y custom hooks |
| 10 | Estética: CSS-in-JS y librerías de componentes |

Eso es una app de React **completa**. Podés leer el código de una, entender por qué cada pieza está donde está, y construir las tuyas. No memorizaste recetas: entendés el **sistema** — por qué React redibuja, cómo fluyen los datos, qué herramienta usar para cada problema. Que es exactamente lo que hace la diferencia entre copiar código y programar de verdad.

> Lo que dijimos al final del Módulo 1: *"construir interfaces modernas es difícil porque hay que mantener la pantalla sincronizada con datos que cambian"*. Recorriste, módulo a módulo, todas las herramientas que React te da para hacer justo eso. El círculo está cerrado.

Y un reconocimiento honesto, porque te lo ganaste: aprender esto de cero, ordenado y entendiéndolo, después de una primera experiencia frustrante, no es poca cosa. Lo hiciste.

---
---

## ✅ Checkpoint — ¿lo agarraste?

Sin mirar, con tus palabras (las respuestas se resuelven en el complemento del recorrido, o me preguntás):

1. ¿El CSS común funciona en React? ¿Cómo se aplica una clase a un elemento?
2. ¿Cuáles son las dos molestias del CSS global que motivan las otras opciones?
3. ¿Qué es **CSS-in-JS**? Nombrá dos ventajas sobre el CSS común y dos librerías que lo implementan.
4. ¿Qué es una **librería de componentes**? Dá tres razones para usar una.
5. ¿Cómo se personalizan los componentes de MUI (ej: que un botón sea relleno o con borde)?
6. ¿Para qué sirven `createTheme` y `ThemeProvider`? ¿Qué patrón ya conocido te recuerdan?
7. ¿Qué diferencia hay, en la lógica, entre un `<input>` controlado y un `<TextField>` de MUI?
8. Completá: "las librerías de componentes y el CSS-in-JS son ______ sobre el React que ya sabés".
9. ¿Por qué este es el módulo más liviano del recorrido?
10. (Repaso final) Sin mirar la tabla: nombrá al menos 6 de los 10 temas que cubriste en el recorrido.

---

## 🎉 Cerraste la Fase 1

Terminaste el **recorrido de React desde cero**. Tenés el modelo mental completo y sólido.

**Qué viene ahora (Fase 2):** los **apuntes maestros** de la clase unificada. Con el piso firme que armaste, esos apuntes van a ser una reconstrucción ordenada y sin errores de lo que se dio en las clases — ya no contenido nuevo que te cuesta, sino la consolidación de algo que ahora entendés. Va a ser, como te dije al principio, "la clase dada sin tropiezos", y vas a poder seguirla tranquilo.

Cuando quieras, arrancamos la Fase 2. Y si antes querés repasar algún módulo, resolver los checkpoints, o que aclaremos cualquier duda que te haya quedado, este es buen momento.

---

**FIN DEL MÓDULO 10 — FIN DEL RECORRIDO REACT DESDE CERO**
