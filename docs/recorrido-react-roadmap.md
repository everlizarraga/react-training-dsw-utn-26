# 🗺️ Recorrido React desde cero — Roadmap e índice

**Qué es esto:** el mapa completo del recorrido de React. Una sola hoja para ver de dónde a dónde vas, qué cubre cada módulo, cómo se conectan entre sí y dónde está cada archivo. Útil para ubicarte, para repasar, y para volver a un tema puntual sin releer todo.

---

## 🎯 Las dos fases

```
FASE 1 · Recorrido React desde cero (Módulos 1–10)        ✅ COMPLETA
         Aprender React de cero, ordenado por concepto,
         sin errores, sin referencias a la clase.

FASE 2 · Apuntes maestros de la clase unificada           ⏳ EN CURSO
         La clase (9/10/12) reconstruida sin tropiezos,
         condensada, con el repo real y atada al TP.
```

Este roadmap cubre la **Fase 1**.

---

## 📚 Los 10 módulos

| # | Módulo | Densidad | Archivo |
|---|---|---|---|
| 1 | ¿Por qué React? | 🟢 liviano | `recorrido-react-modulo1-por-que-react.md` |
| 2 | Arrancar el proyecto | 🔴 denso | `recorrido-react-modulo2-arrancar-el-proyecto.md` |
| 3 | Componentes y props | 🟡 medio | `recorrido-react-modulo3-componentes-y-props.md` |
| 4 | El corazón: el estado | 🔴 el más denso | `recorrido-react-modulo4-el-corazon-el-estado.md` |
| 5 | Efectos y datos | 🔴 denso | `recorrido-react-modulo5-efectos-y-datos.md` |
| 6 | Compartir estado (lifting) | 🟡 medio | `recorrido-react-modulo6-compartir-estado-lifting.md` |
| 7 | Routing | 🟡 medio (mecánico) | `recorrido-react-modulo7-routing.md` |
| 8 | Formularios | 🟡 medio | `recorrido-react-modulo8-formularios.md` |
| 9 | Context y custom hooks | 🟡 medio | `recorrido-react-modulo9-context-y-custom-hooks.md` |
| 10 | Estética (CSS-in-JS, MUI) | 🟢 liviano | `recorrido-react-modulo10-estetica.md` |

---

## 🧭 Qué cubre cada módulo

**Módulo 1 — ¿Por qué React?** *(conceptual, sin código)*
El problema que React resuelve (mantener la UI sincronizada con datos que cambian) · imperativo vs declarativo · qué es React · SSR vs CSR vs SPA · DOM vs Virtual DOM y reconciliación.

**Módulo 2 — Arrancar el proyecto** *(acá tocás la terminal)*
Node y npm · crear el proyecto (Create React App) · estructura de carpetas · el `.gitignore` y por qué `node_modules` no se sube · instalar dependencias · levantar la app (`npm start`) · la cadena de booteo (`#root` → `index.js` → `App`) · anatomía de un componente · JSX y sus reglas.

**Módulo 3 — Componentes y props**
Props (pasar datos a un componente) · flujo de datos de padre a hijo · props de solo lectura · `children` · composición · renderizar listas con `map` y la `key` · renderizado condicional (`if` / ternario / `&&`).

**Módulo 4 — El corazón: el estado** *(el más importante)*
Qué es el estado y en qué difiere de las props · por qué una variable normal no alcanza · `useState` · el ciclo de render · eventos (`onClick`, la trampa de los paréntesis) · inputs controlados · las reglas de los hooks.

**Módulo 5 — Efectos y datos**
Qué es un efecto (*side effect*) · `useEffect` · el array de dependencias (sin array / `[]` / `[deps]`) · la capa de servicio (mock) · traer datos al montar · estados de carga ("cargando...") · la función de limpieza.

**Módulo 6 — Compartir estado (lifting state up)**
El estado es privado · elevar el estado al padre común · datos para abajo (props) y eventos para arriba (callbacks) · cómo un hijo le avisa al padre · cuándo elevar y cuándo no (y la antesala del prop drilling).

**Módulo 7 — Routing**
Por qué una SPA necesita routing · `BrowserRouter` · `Routes` / `Route` · navegar sin recargar con `Link` · layout común con `Outlet` y rutas anidadas · ruta `index` · parámetros de URL con `useParams` · navegación programática con `useNavigate`.

**Módulo 8 — Formularios**
El estado del formulario como objeto · un solo manejador con clave computada · `onSubmit` y `e.preventDefault()` · validación · deshabilitar el botón con `disabled` · qué hacer después de enviar.

**Módulo 9 — Context y custom hooks**
El prop drilling en su peor versión · el Context API · los tres pasos (crear, proveer, usar) · el patrón Provider con estado · custom hooks (`useCart` y un hook general con limpieza) · cuándo usar context y cuándo no.

**Módulo 10 — Estética**
El CSS común funciona · CSS-in-JS (emotion, styled-components) · librerías de componentes (Material UI) · `createTheme` y `ThemeProvider` · la conexión final: un `TextField` es un input controlado.

---

## 🔗 Los hilos que se cierran

Una de las cosas que hace al recorrido un sistema y no una lista suelta: varios cabos quedan abiertos a propósito y se cierran módulos después. Este es el mapa de esas conexiones:

```
M1  declarativo / Virtual DOM ─────────► se cierra en M4 (ciclo de render)
                                          y reaparece en M7 (la SPA no recarga)

M3  "¿cómo avisa un hijo al padre?" ───► se resuelve en M6 (callbacks)

M4  "buscador + lista separados" ──────► se resuelve en M6 (lifting state up)

M5  función de limpieza ───────────────► brilla en M9 (custom hook useOnlineStatus)

M6  prop drilling (el carrito) ────────► se resuelve en M9 (Context)
    (el dolor se siente otra vez en M7 y M8)

M8  "un TextField es un input controlado" ─► se confirma en M10
```

Si seguís estas flechas, ves cómo cada herramienta nueva resuelve un dolor concreto que ya sentiste antes. Esa es la lógica del recorrido.

---

## 📈 El orden y por qué

El recorrido **no** sigue el orden de la clase: sigue el orden de **dependencia de conceptos**. Cada módulo se apoya en los anteriores. El más importante es el **Módulo 4** (estado): todo lo de antes es la base que lo hace posible, y todo lo de después se monta sobre él.

```
M1 ─► M2 ─► M3 ─► [ M4 ] ─► M5 ─► M6 ─► M7 ─► M8 ─► M9 ─► M10
porqué  setup  props  ESTADO  datos  lifting routing forms context estética
                       ▲
              el corazón: si esto está firme,
              todo lo demás se apoya tranquilo
```

---

## 🏷️ Leyenda de marcas

Dentro de cada módulo, las secciones están marcadas por importancia:

| Marca | Significado |
|---|---|
| 🔴 | **Central.** Concepto fundamental. Detenerse y entender bien. |
| 🟡 | **Importante pero secundario.** Complementa lo central. |
| 🟢 | **Mencionado de pasada.** Saber que existe. |

Otros bloques que vas a encontrar:

| Bloque | Para qué |
|---|---|
| 🎓 **Si te lo preguntan** | Respuesta modelo, concisa, para consolidar un concepto clave. |
| 🕳️ **Madriguera** | Algo de React que existe pero queda fuera del camino. Lo justo para saber que está; profundizás aparte. |
| ⚠️ | Una trampa común que le pasa a casi todos. Ojo. |
| ✅ **Checkpoint** | Al final de cada módulo: preguntas de autoevaluación sin respuestas. |

---

## 🎒 Cómo usar este recorrido

1. **Leelo en orden.** Cada módulo asume el anterior.
2. **No apures los densos (2, 4, 5).** Si un módulo no cierra, frená antes de seguir.
3. **Hacé los checkpoints de verdad.** Si podés responder con tus palabras, avanzás tranquilo. Si no, volvé a esa sección.
4. **Las madrigueras son opcionales.** Si te tientan, anotalas para otro día; el camino principal nunca depende de ellas.
5. **Lo que aprendés es JavaScript + React, todo en JS** (sin TypeScript). El ejemplo que hila todo es una tienda, MegaSuper.

---

**FIN DEL ROADMAP — RECORRIDO REACT DESDE CERO**
