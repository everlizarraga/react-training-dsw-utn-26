# 🧰 Anexo del recorrido — El ecosistema: CRA, Vite y Next.js

## Cómo se crea y se levanta una app de React (y por qué hay varias formas)

---

> **Por qué este anexo.** En el Módulo 2 creamos el proyecto con **Create React App (CRA)**, que es lo que usa la cátedra. Pero CRA está discontinuado, y existen alternativas (**Vite**, **Next.js**) que conviene ubicar para que el recorrido no te quede desactualizado. Este anexo pone las tres en su lugar. Es **complementario**: el recorrido se entiende perfecto sin esto. Acá venís para tener el panorama.

---

## 1. La idea que ordena todo: esto NO es "tipos de React"

Lo primero y más importante, para que no te confundas:

> CRA, Vite y Next.js **no son versiones ni sabores de React.** Son **herramientas para armar y hacer correr** un proyecto de React. El React que escribís adentro —componentes, props, estado, hooks, routing, context— es **el mismo** con las tres.

Por eso **el 95% de este recorrido no depende de cuál uses.** Cambiás la herramienta que crea y levanta el proyecto; no cambiás cómo programás React. Tenelo presente: lo que aprendiste no se vuelve obsoleto porque cambie la herramienta de andamiaje.

```
        ┌──────────────────────────────────────────────┐
        │   TU CÓDIGO REACT (lo que aprendiste)         │
        │   componentes · props · estado · hooks ·      │   ← esto NO cambia
        │   efectos · routing · context · formularios   │
        └──────────────────────────────────────────────┘
                          ▲
                          │  lo arma y lo levanta...
        ┌─────────────────┴─────────────────────────────┐
        │   LA HERRAMIENTA   →   CRA / Vite / Next.js    │   ← esto sí varía
        └────────────────────────────────────────────────┘
```

---

## 2. Las dos categorías (esta es la distinción clave)

No son tres cosas equivalentes. Son **dos categorías distintas**:

```
   CATEGORÍA A — "Build tools" (arman una SPA)
   ┌─────────────────────────────────────────────┐
   │   CRA  ·  Vite                              │
   │   Crean una SPA / CSR (Módulo 1):           │
   │   una sola página, renderizada en el        │
   │   navegador. Vos le ponés el routing,       │
   │   los datos, etc. (lo que hicimos).         │
   └─────────────────────────────────────────────┘

   CATEGORÍA B — "Frameworks" (mucho más que armar)
   ┌─────────────────────────────────────────────┐
   │   Next.js                                   │
   │   Un framework completo sobre React:        │
   │   trae SSR, routing por archivos, carga     │
   │   de datos, etc., ya resueltos y con su     │
   │   forma de hacer las cosas.                 │
   └─────────────────────────────────────────────┘
```

- **CRA y Vite son la MISMA categoría**: arman una **SPA** (lo del Módulo 1, todo en el cliente). Son **intercambiables** — cambia el comando de creación y un par de detalles del arranque, nada más.
- **Next.js es OTRA categoría**: es un **framework**, que es "el siguiente nivel". No es "otra forma de crear una SPA": cambia el modelo de cómo corre tu app (puede renderizar en el **servidor**, ¿te acordás de SSR vs CSR del Módulo 1?).

Por eso compararlos "en paralelo" como si fueran lo mismo confunde. CRA ↔ Vite, sí. Next.js juega en otra liga.

---

## 3. Las tres, una por una

### 🟡 Create React App (CRA) — *lo que usa la cátedra, ya discontinuado*

La herramienta clásica (2016) para armar una SPA sin configurar nada. **El equipo de React la deprecó oficialmente en febrero de 2025**: sigue funcionando en "modo mantenimiento" (de hecho soporta React 19), pero ya **no se recomienda para proyectos nuevos**. Es lenta arrancando porque usa Webpack por debajo.

```bash
npx create-react-app megasuper
npm start          # levanta en localhost:3000
```

**Por qué la usamos igual:** es lo que tiene el repo de la cátedra. Para aprender React da exactamente lo mismo. Pero sabé que, fuera de la materia, hoy elegirías otra.

### 🟢 Vite — *el reemplazo moderno de CRA (misma categoría)*

Es la herramienta **estándar de hoy** para armar una SPA de React. Hace **lo mismo** que CRA (te arma el proyecto y lo levanta), pero mucho **más rápido** (usa esbuild/Rollup en vez de Webpack). Es, literalmente, "CRA pero moderno". Es el **equivalente más cercano a CRA** que recomienda el propio equipo de React.

```bash
npm create vite@latest megasuper -- --template react
cd megasuper
npm install
npm run dev        # levanta en localhost:5173
```

**Diferencias prácticas con CRA** (mínimas):

| | CRA | Vite |
|---|---|---|
| Crear | `npx create-react-app x` | `npm create vite@latest x -- --template react` |
| Levantar | `npm start` | `npm run dev` |
| Puerto | 3000 | 5173 |
| Entrada | `src/index.js` | `src/main.jsx` |
| Dependencias | ya instaladas | corrés `npm install` |

**Todo lo demás del recorrido es idéntico.** Si mañana migrás MegaSuper de CRA a Vite, tu carpeta `src` (componentes, estado, routing, context) pasa casi tal cual.

### 🔵 Next.js — *un framework completo (otra categoría)*

Es un **framework construido sobre React**, "el siguiente nivel". No solo arma el proyecto: trae resueltas cosas que en una SPA tenés que armar vos, y habilita un modelo distinto:

- **SSR (renderizado en el servidor):** puede generar el HTML en el servidor (mejor carga inicial y SEO). Recordá del Módulo 1 que React por defecto hace CSR; Next.js suma SSR.
- **Routing por archivos:** en vez de configurar rutas con React Router (Módulo 7), creás archivos/carpetas y eso **es** la ruta.
- Carga de datos, optimizaciones, y más, ya integrados.

```bash
npx create-next-app@latest megasuper    # crea un proyecto Next.js
```

**Lo honesto sobre Next.js:** es **bastante más que React** — tiene su propia curva, sus propias convenciones, su propia forma de hacer las cosas. Es excelente para apps de producción que necesitan SEO o un backend integrado. **Pero para aprender React y para tu TP, NO lo necesitás.** Es un mundo aparte que conviene encarar *después* de tener React sólido (que es justo lo que estás construyendo). Tenelo en el radar como "a dónde podría ir esto más adelante", no como algo a estudiar ahora.

---

## 4. Tabla comparativa

| | **CRA** | **Vite** | **Next.js** |
|---|---|---|---|
| Categoría | Build tool (SPA) | Build tool (SPA) | Framework |
| Qué arma | SPA / CSR | SPA / CSR | App con SSR + CSR |
| Routing | lo ponés vos (React Router) | lo ponés vos (React Router) | por archivos, integrado |
| Velocidad | lenta (Webpack) | muy rápida (esbuild) | rápida |
| Estado hoy | **discontinuada** | **estándar para SPAs** | **framework recomendado** |
| Curva extra sobre React | ninguna | casi ninguna | **sí, bastante** |
| ¿Para tu TP? | sí (es lo del repo) | sí (equivalente) | no hace falta |

---

## 5. ¿Cuál usarías y cuándo? (resumen)

```
   ¿Querés aprender React / hacer una SPA simple?
        ├─ Es lo de la cátedra → CRA   (lo que usamos; anda perfecto para aprender)
        └─ Proyecto nuevo tuyo → Vite  (lo mismo, moderno y rápido)

   ¿Necesitás SSR, SEO, routing por archivos, una app de producción grande?
        └─ Next.js   (otra liga; cuando ya domines React)
```

Para tu situación concreta —aprender React y encarar el TP— **CRA (lo del repo) o Vite alcanzan y sobran.** Next.js es para tener en el horizonte, no para ahora.

---

## 6. El mensaje para llevarte

> El recorrido que hiciste **no caduca** porque CRA esté discontinuada. Aprendiste **React**, no "React-con-CRA". Componentes, props, estado, hooks, efectos, lifting, routing, context, formularios: todo eso es idéntico con CRA, con Vite o con Next.js. La herramienta de andamiaje es la parte intercambiable y perecedera; lo que aprendiste es la parte que perdura.

Si algún día CRA te da problemas o querés arrancar un proyecto nuevo, ya sabés: **Vite**, mismo React adentro, y seguís de largo.

> 🔎 La diferencia SSR vs CSR (clave para entender por qué Next.js es otra categoría) está en el **Módulo 1** del recorrido. La instalación con CRA, en el **Módulo 2**.

---

**FIN DEL ANEXO — ECOSISTEMA CRA / VITE / NEXT.JS**
