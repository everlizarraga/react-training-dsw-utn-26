# 🧭 Recorrido React — Módulo 1

## ¿Por qué React? El problema que resuelve y cómo piensa

---

### 📍 Sobre este documento

Este es el primer punto del recorrido. Es **puramente conceptual**: acá no instalás nada ni escribís React todavía. El objetivo es que entiendas **por qué React existe** y **cómo piensa**, porque si arrancás escribiendo código sin esa base, terminás copiando recetas sin saber qué estás haciendo — y eso no nos sirve.

**Qué cubre este módulo:**
- El problema de fondo que React viene a resolver.
- El cambio mental clave: programar de forma **imperativa** vs **declarativa**.
- Qué es React, en una definición destilada.
- Dónde corre una app: **SSR vs CSR** (y qué tipo de app vamos a construir).
- **DOM vs Virtual DOM**: el truco técnico que hace que todo funcione.

**Qué NO cubre (viene después):**
- Instalación, comandos, estructura de carpetas → Módulo 2.
- Componentes en código, JSX → Módulo 2 y 3.
- Estado, hooks, eventos → Módulo 4 en adelante.

---

### 🎒 De dónde venís

Asumo que manejás **HTML, CSS y JavaScript**, que alguna vez tocaste el DOM con JS (algo tipo `document.getElementById(...)` para cambiar cosas en la página), y que tenés práctica básica con `npm` (instalar alguna dependencia). Sobre eso construyo.

No asumo nada de React. No asumo TypeScript (todo va a estar en JavaScript puro). Si en algún punto necesito un concepto que quizás no tenés fresco, lo explico ahí mismo.

---

### 🕳️ Una convención: las "madrigueras"

Cada tanto vas a ver un bloque como este:

> 🕳️ **Madriguera — [tema]**
> Una cosa de React que existe pero que se sale de lo que vamos a usar. Te la dejo en 1-3 líneas: qué es y qué hace, lo justo para que sepas que está ahí.
> *Volvé al camino — esto lo profundizás aparte si te interesa, otro día.*

Son señales al costado del camino, no desvíos. Si te tienta, anotala para después; si no, seguís de largo sin culpa. El recorrido principal nunca depende de una madriguera para entenderse.

---
---

## 1. 🔴 El problema de fondo: una UI que tiene que reflejar datos que cambian

Empecemos por el dolor, porque React es la respuesta a un dolor concreto.

Imaginá una página de una tienda — un carrito de compras, digamos. Hay un numerito arriba a la derecha que dice cuántos productos tenés en el carrito. Cuando hacés clic en "Agregar", ese número tiene que pasar de `2` a `3`. Y también tiene que actualizarse el subtotal. Y quizás aparece el producto en una lista lateral. Y el botón "Finalizar compra" que estaba gris ahora se pone activo.

**Un solo dato cambió** (la cantidad de productos), pero **cuatro lugares distintos de la pantalla tienen que reaccionar.**

Con JavaScript "a mano" — el que ya conocés — vos sos responsable de ir a buscar cada uno de esos pedazos del HTML y actualizarlos uno por uno:

```javascript
// Esto es JavaScript del que ya conocés (NO es React).
// Cada vez que agrego un producto, tengo que acordarme de tocar TODO esto:

contador.textContent = cantidad;              // actualizo el numerito
subtotal.textContent = "$" + calcularTotal(); // actualizo el subtotal
listaLateral.appendChild(nuevoItem);          // agrego el item a la lista
botonComprar.disabled = false;                // activo el botón
```

¿Ves el problema? **Vos tenés que recordar, a mano, cada lugar de la pantalla que depende de ese dato.** Si la app tiene 5 pantallas y 40 elementos que dependen de datos cambiantes, mantener todo sincronizado se vuelve un infierno. Te olvidás de actualizar uno, y la pantalla muestra algo inconsistente (el carrito dice 3 pero el subtotal sigue mostrando el de 2). Estos son los bugs clásicos de "la pantalla quedó desincronizada de los datos".

> **La idea central que tenés que llevarte:** el trabajo difícil de una interfaz moderna no es dibujar cosas una vez. Es **mantener lo que se ve sincronizado con datos que cambian todo el tiempo.** Ahí es donde la web hecha "a mano" se rompe a medida que crece.

React nace para resolver exactamente eso. Y la forma en que lo resuelve es cambiándote la manera de pensar el problema. Eso es lo próximo.

---

## 2. 🔴 El cambio mental: imperativo vs declarativo

Este es **el concepto más importante de todo el módulo.** Si entendés esto, el resto de React te va a parecer natural. Si no lo entendés, React te va a parecer magia rara. Así que vamos despacio.

### Imperativo: vos das las instrucciones paso a paso

El código que viste arriba es **imperativo**. "Imperativo" viene de *imperar*, dar órdenes. Vos le decís a la computadora **el CÓMO**, paso por paso:

> "Buscá el elemento del contador. Cambiale el texto. Ahora buscá el subtotal. Calculalo. Cambiale el texto. Ahora creá un nuevo item. Agregalo a la lista. Ahora buscá el botón. Activalo."

Vos sos el que maneja cada paso de la actualización del DOM. Sos responsable de la coreografía completa.

### Declarativo: vos describís el resultado, no los pasos

**Declarativo** es lo opuesto. En vez de dar la lista de pasos, vos **describís cómo tiene que verse la pantalla para un estado de datos dado**, y dejás que otro (React) se ocupe de hacer los cambios necesarios para llegar ahí.

> "Cuando el carrito tiene N productos: el contador muestra N, el subtotal muestra el total de esos productos, la lista muestra esos productos, y el botón está activo si N > 0."

Fijate la diferencia: no dije "buscá esto y cambiá aquello". Dije **cómo se ve la pantalla en función de los datos.** Vos describís el QUÉ; React calcula el CÓMO.

### La analogía del taxi

- **Imperativo** es ir en el asiento de adelante dándole indicaciones al chofer: "doblá a la izquierda acá, seguí derecho tres cuadras, ahora a la derecha, frená en la esquina". Vos manejás cada decisión.
- **Declarativo** es subirte y decir: **"Llevame a Plaza de Mayo."** Te tenés que fiar de que el chofer sabe llegar. No te importa el camino exacto; te importa el destino.

React es el chofer. Vos le decís el destino (cómo tiene que verse la UI para esos datos), y él se encarga del camino (qué tocar en la pantalla para que se vea así).

> 🎓 **Si te lo preguntan — "¿Qué quiere decir que React es declarativo?"**
> Quiere decir que vos no manipulás la pantalla paso a paso. Describís cómo debería verse la interfaz para un estado de datos determinado, y React se encarga de actualizar lo que haga falta para que la pantalla coincida con esa descripción. Programás el *qué*, no el *cómo*.

Esta es una de esas preguntas conceptuales clásicas. Vale la pena que la puedas decir con tus palabras.

> 🕳️ **Madriguera — declarativo vs imperativo no es exclusivo de React**
> Esta distinción aparece en muchos lados de la programación (SQL es declarativo, por ejemplo: pedís "los clientes de Buenos Aires" sin decir cómo recorrer la tabla). React aplica la idea a las interfaces.
> *Volvé al camino — con entenderlo aplicado a la UI nos alcanza.*

---

## 3. 🔴 Entonces, ¿qué es React?

Ahora que tenés el "por qué", la definición te va a caer parada:

> **React es una biblioteca de JavaScript para construir interfaces de usuario de forma declarativa y basada en componentes.**

Desarmemos esa frase, porque cada palabra está elegida:

- **Biblioteca de JavaScript** → es JS, no un lenguaje nuevo. Todo lo que sabés de JavaScript sigue valiendo. React es un conjunto de herramientas que sumás a JS.
- **Para construir interfaces de usuario** → su trabajo es la parte visible, lo que el usuario ve y toca. No es para bases de datos ni para el servidor.
- **De forma declarativa** → lo que acabás de ver en la sección 2. Describís el qué, no el cómo.
- **Basada en componentes** → en vez de una página gigante, armás piezas reutilizables (un botón, una tarjeta de producto, un carrito) y las combinás como bloques de Lego. Esto lo vas a ver en detalle en el Módulo 3; por ahora quedate con la idea de "piezas que se combinan".

React se apoya en tres ideas grandes que vas a escuchar todo el tiempo:

| Idea | Qué es (en una línea) | Dónde lo vemos |
|---|---|---|
| **Componentes** | Piezas reutilizables que arman la UI | Módulo 3 |
| **Declarativo** | Describís el resultado, React hace los cambios | Ya lo viste (sección 2) |
| **Virtual DOM** | El mecanismo que hace todo eficiente | Sección 5 de este módulo |

> 🕳️ **Madriguera — "biblioteca" vs "framework"**
> Vas a escuchar a gente discutir si React es una "biblioteca" o un "framework". La distinción es más filosófica que práctica: React oficialmente se llama a sí mismo biblioteca porque resuelve una cosa (la UI) y te deja elegir el resto de las herramientas. Un framework (como Angular) te impone más decisiones.
> *Volvé al camino — para vos hoy, "biblioteca" alcanza y sobra.*

---

## 4. 🟡 ¿Dónde corre la app? SSR vs CSR

Antes de seguir necesitás ubicar **dónde se arma la página que ve el usuario**, porque define qué tipo de app vamos a construir. Hay dos modelos.

### SSR — Server-Side Rendering (renderizado del lado del servidor)

El **servidor** arma el HTML ya terminado y se lo manda al navegador listo para mostrar. Es el modelo clásico de la web "de toda la vida": cada vez que pedís una página, el servidor te devuelve HTML cocinado.

### CSR — Client-Side Rendering (renderizado del lado del cliente)

El servidor te manda un HTML casi vacío más un paquete de JavaScript. Es el **navegador** (el cliente) el que ejecuta ese JavaScript y arma la página ahí mismo, en tu máquina. La primera vez tarda un poco más (tiene que bajar y ejecutar todo el JS), pero después las interacciones son muy fluidas porque ya está todo cargado y no necesita volver a pedirle páginas enteras al servidor.

Acá va la comparación directa:

| Aspecto | SSR (servidor) | CSR (cliente) |
|---|---|---|
| **Quién arma la vista** | El servidor | El navegador |
| **Qué viaja por la red** | HTML ya terminado | Un HTML mínimo + JavaScript |
| **Carga inicial** | Rápida (el HTML ya viene hecho) | Más lenta (espera a que baje y corra el JS) |
| **Después de cargar** | Cada navegación pide al servidor | Fluido, sin recargar la página entera |
| **Dónde está el trabajo** | Más carga en el servidor | Más carga en el navegador |

**¿Qué vamos a usar nosotros?** **CSR.** La forma estándar en la que vamos a crear nuestra app de React produce lo que se llama una **SPA**.

### SPA — Single Page Application (aplicación de una sola página)

El nombre asusta pero es simple. Una SPA es una app que carga **una sola página HTML de verdad**, y a partir de ahí, cuando navegás entre "secciones" (inicio, detalle de producto, checkout), **no se recarga la página completa**: el JavaScript reescribe el contenido al vuelo. Por eso se siente tan fluido, como una app de celular y no como una web que parpadea en cada clic.

Cuando más adelante naveguemos entre pantallas en nuestra app y veas que la transición es instantánea y sin parpadeo, eso es la SPA en acción.

> 🕳️ **Madriguera — React también puede hacer SSR**
> React no está casado con CSR. Existen herramientas (la más conocida se llama **Next.js**) que le permiten a React hacer SSR, justamente para acelerar la carga inicial y mejorar el posicionamiento en buscadores. Es un mundo aparte.
> *Volvé al camino — nosotros vamos con CSR / SPA, que es lo que necesitamos.*

> 🎓 **Si te lo preguntan — "Diferencia entre SSR y CSR"**
> En SSR el servidor arma el HTML terminado y lo manda listo; la carga inicial es rápida pero cada navegación vuelve al servidor. En CSR el servidor manda un HTML mínimo más JavaScript, y el navegador arma la página; la carga inicial es más lenta pero después la app se siente fluida porque no recarga páginas enteras. React, en su forma estándar, construye apps CSR conocidas como SPA.

---

## 5. 🔴 DOM vs Virtual DOM: el truco que hace todo posible

Llegamos al corazón técnico del módulo. Esto explica **cómo** React logra ser declarativo sin ser lento. Vamos por partes.

### Primero, ¿qué es el DOM? (repaso)

El **DOM** (*Document Object Model*) es la representación de tu página HTML como un **árbol de objetos** que el navegador mantiene en memoria. Cada etiqueta HTML es un nodo de ese árbol, con sus hijos colgando debajo:

```
documento
   └── <html>
         └── <body>
               ├── <h1>          "Tienda"
               └── <ul>
                     ├── <li>    "Manzana"
                     ├── <li>    "Banana"
                     └── <li>    "Naranja"
```

Cuando en JavaScript hacés `document.getElementById(...)` y cambiás algo, estás tocando este árbol. El navegador, cada vez que el DOM cambia, tiene que **recalcular cómo se ve la página y volver a dibujarla**. Y acá está el detalle clave: **tocar el DOM real es caro.** Cada cambio puede disparar recálculos de posiciones, tamaños y un re-dibujado. Si hacés muchos cambios chiquitos al DOM real, uno tras otro, la página se pone lenta.

### El problema concreto

En una app declarativa, cuando los datos cambian, la forma "ingenua" de actualizar sería: *"borrá toda la pantalla y volvé a dibujarla desde cero con los datos nuevos".* Eso sería simple de programar... pero un desastre de rendimiento, porque estarías re-tocando el DOM real entero por cada cambio, aunque solo haya cambiado un numerito.

React necesitaba una forma de ser declarativo (vos describís toda la pantalla) **sin** pagar el costo de redibujar todo el DOM real cada vez. La solución es el Virtual DOM.

### El Virtual DOM

El **Virtual DOM (VDOM)** es una **copia liviana del DOM, hecha de objetos JavaScript comunes, que vive solo en memoria.** No se dibuja en pantalla. Es como un "borrador" del DOM real.

La gracia es que comparar y modificar objetos JavaScript en memoria es **muchísimo más barato** que tocar el DOM real. Entonces React usa ese borrador para hacer todas las cuentas, y recién toca el DOM real **lo mínimo indispensable.**

### Cómo funciona, paso a paso

Cuando los datos de tu app cambian, React hace esto:

```
   ┌──────────────────────────────────────────────────────────┐
   │  1. Cambian los datos                                     │
   │     React arma un VDOM NUEVO: cómo debería verse          │
   │     la pantalla ahora, completa, en memoria.              │
   └──────────────────────────────────────────────────────────┘
                              │
                              ▼
   ┌──────────────────────────────────────────────────────────┐
   │  2. RECONCILIACIÓN (el "diffing")                         │
   │     Compara el VDOM nuevo con el VDOM anterior y          │
   │     encuentra SOLO las diferencias.                       │
   │     Ej: "lo único que cambió fue el texto de este nodo".  │
   └──────────────────────────────────────────────────────────┘
                              │
                              ▼
   ┌──────────────────────────────────────────────────────────┐
   │  3. PATCH al DOM real                                     │
   │     Aplica al DOM real SOLO esos cambios mínimos.         │
   │     No redibuja todo: toca el único nodo que cambió.      │
   └──────────────────────────────────────────────────────────┘
```

Ese paso 2 — comparar el árbol nuevo con el viejo para sacar la lista mínima de cambios — se llama **reconciliación**. Es el concepto técnico estrella de React.

### Volvamos al carrito

¿Te acordás del numerito del carrito que pasaba de 2 a 3? Con React vos no escribís "buscá el contador y cambiale el texto". Vos describís toda la pantalla en función del dato. Cuando el dato pasa de 2 a 3, React arma el VDOM nuevo, lo compara con el anterior, **se da cuenta solo de que lo único que cambió es ese texto**, y toca únicamente ese nodo del DOM real. El resto de la pantalla ni se entera.

Así conseguís lo mejor de los dos mundos: **escribís de forma declarativa y simple** (describís todo), pero **por debajo se actualiza de forma eficiente** (solo lo que cambió). Ese es, en una frase, el aporte de React.

> 🎓 **Si te lo preguntan — "¿Qué es el Virtual DOM y por qué React lo usa?"**
> El Virtual DOM es una copia liviana del DOM real, hecha de objetos JavaScript en memoria. Cuando los datos cambian, React arma un nuevo Virtual DOM, lo compara con el anterior (proceso llamado reconciliación) para detectar solo las diferencias, y aplica al DOM real únicamente esos cambios mínimos. Lo usa porque tocar el DOM real es costoso: comparar en memoria y actualizar solo lo necesario es mucho más eficiente que redibujar toda la página.

> 🕳️ **Madriguera — cómo hace el diffing por dentro**
> El algoritmo de reconciliación tiene reglas internas (compara nodo por nodo, usa "keys" para identificar elementos de una lista, etc.) y un motor moderno llamado *Fiber*. Son las tripas de React.
> *Volvé al camino — saber que existe la reconciliación y qué hace es suficiente; cómo está implementada por dentro es para mucho más adelante.*

---

## 6. 🟢 Cómo encaja todo junto

Cerremos atando los hilos en un solo párrafo, para que tengas la foto completa:

> Construir interfaces modernas es difícil porque hay que **mantener la pantalla sincronizada con datos que cambian** (sección 1). React resuelve eso dejándote programar de forma **declarativa**: describís cómo se ve la UI para cada estado de los datos, en vez de manipular la pantalla paso a paso (sección 2). React es una **biblioteca de JavaScript** que arma la UI con **componentes** reutilizables (sección 3). La app corre en el **navegador** (CSR) como una **SPA** de una sola página que no recarga (sección 4). Y para que ese estilo declarativo no sea lento, usa un **Virtual DOM**: un borrador en memoria que compara contra el anterior (**reconciliación**) y toca el DOM real solo donde hubo cambios (sección 5).

Si podés explicar ese párrafo con tus palabras, este módulo está aprobado.

---
---

## ✅ Checkpoint — ¿lo agarraste?

Respondé con tus palabras, sin mirar el documento. No te doy las respuestas acá a propósito (se resuelven en el complemento del recorrido, o me preguntás directamente):

1. ¿Por qué la web hecha "a mano" con JavaScript se complica a medida que la app crece? ¿Cuál es el dolor concreto?
2. Explicá con tus palabras la diferencia entre programar **imperativo** y **declarativo**. Dame un ejemplo de cada uno (puede ser fuera de la programación, tipo el taxi).
3. ¿En qué categoría —imperativo o declarativo— cae React, y por qué?
4. Definí React en una frase, y desarmá esa frase explicando cada parte.
5. ¿Qué diferencia hay entre **SSR** y **CSR**? ¿Cuál usamos nosotros?
6. ¿Qué es una **SPA** y por qué se siente más fluida que una web tradicional?
7. ¿Qué es el **DOM**? ¿Por qué se dice que tocarlo es "caro"?
8. ¿Qué es el **Virtual DOM** y qué problema resuelve?
9. ¿Qué es la **reconciliación**? Contá los tres pasos que da React cuando cambian los datos.
10. Atando todo: ¿cómo logra React que vos escribas declarativo (simple) pero que la pantalla se actualice eficiente (rápido)?

---

## 👉 Qué viene en el Módulo 2

Ya tenés el **porqué** y el **cómo piensa** React. En el Módulo 2 nos ensuciamos las manos por primera vez:

- Cómo se **crea un proyecto de React** desde cero, paso a paso, con cada comando explicado.
- Qué dependencias se instalan, **cuándo** y **por qué** (nada de comandos mágicos sin explicación).
- La **estructura de carpetas** de un proyecto: qué es cada cosa.
- Qué se sube a un repositorio y qué no (el famoso `.gitignore` y por qué `node_modules` no viaja).
- Cómo **levantar la app** y verla corriendo en el navegador.
- La **anatomía de un componente** y la primera mirada a **JSX**.

A partir del Módulo 2 vas a tener algo corriendo en pantalla. Este módulo era el cimiento; ahora empezamos a levantar la pared.

---

**FIN DEL MÓDULO 1**
