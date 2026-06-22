# 🧭 Recorrido React — Módulo 8

## Formularios: recibir datos del usuario

---

### 📍 Sobre este documento

En el Módulo 4 aprendiste a controlar **un** input. Pero un formulario real —como el checkout de MegaSuper— tiene **muchos** campos: nombre, apellido, email... Acá aprendés a manejar un formulario completo de forma ordenada: guardar todos los campos juntos, usar **un solo manejador** para todos, **validar** los datos antes de enviar, **deshabilitar** el botón hasta que esté todo bien, y qué hacer **al enviar**.

Los formularios son el corazón de cualquier app que recibe datos del usuario. **Tu TP los va a necesitar sí o sí**, así que este módulo es de los más prácticos del recorrido.

**Qué cubre este módulo:**
- El estado de un formulario como un **objeto** con todos los campos.
- **Un solo manejador** para todos los campos (en vez de uno por campo).
- Enviar el formulario: **`onSubmit`** y **`e.preventDefault()`**.
- **Validación**: chequear los datos antes de enviar.
- **Deshabilitar el botón** según el formulario (`disabled`).
- Qué hacer **después** de enviar (redirigir, limpiar).

**Qué NO cubre (viene después):**
- Vestir el formulario con componentes lindos de una librería visual → Módulo 10.
- Compartir el carrito entre páginas (**Context**) → Módulo 9.

---

### 🎒 De dónde venís

Asumo los Módulos 1 a 7. Para este módulo es clave:
- Del **Módulo 4**: el **input controlado** (`value` + `onChange`), y la idea del estado como única fuente de verdad. Acá lo escalamos a muchos campos.
- Del **Módulo 1**: que una SPA **no se recarga** (lo vamos a defender en la sección 4).
- Del **Módulo 7**: **`useNavigate`** (lo usamos para redirigir después de enviar).

> Recordá del Módulo 4: un input controlado tiene su valor en el estado (`value={texto}`) y se actualiza en cada tecleo (`onChange`). Un formulario es, básicamente, **varios** inputs controlados trabajando juntos.

---
---

## 1. 🔴 El problema: un formulario son muchos inputs

Imaginá el formulario de checkout con cuatro campos: nombre, apellido, email y repetir email. Con lo del Módulo 4, lo "ingenuo" sería crear un estado y un manejador **por cada campo**:

```javascript
// 😩 Repetitivo: un useState y un onChange por campo
const [nombre, setNombre] = useState("");
const [apellido, setApellido] = useState("");
const [email, setEmail] = useState("");
const [repetirEmail, setRepetirEmail] = useState("");
// ...y cuatro onChange casi idénticos
```

Con 4 campos ya cansa; con 10, es insoportable y propenso a errores. Necesitamos una forma **ordenada** de manejar todos los campos juntos. La solución tiene dos partes: guardar todo en **un objeto**, y usar **un solo manejador**.

---

## 2. 🔴 El estado del formulario: un objeto

En vez de un `useState` por campo, usamos **un solo estado** que es un **objeto** con todos los campos adentro:

```javascript
import { useState } from 'react';

const [form, setForm] = useState({
  nombre: "",
  apellido: "",
  email: "",
  repetirEmail: "",
});
```

Ahora todos los campos viven en un solo lugar, `form`. Para leer un campo, accedés a su propiedad: `form.nombre`, `form.email`. Un solo estado, todo el formulario.

Esto, por sí solo, no resuelve lo de los manejadores repetidos. Pero habilita el truco que sigue.

---

## 3. 🔴 Un solo manejador para todos los campos

Acá está la técnica clave. En lugar de un `onChange` por campo, escribimos **uno solo** que sirve para todos. El secreto es darle a cada input un atributo **`name`** que coincida con la clave del objeto, y usar ese `name` para saber qué campo actualizar:

```javascript
function manejarCambio(e) {
  const { name, value } = e.target;   // name = "nombre", value = lo que se tecleó
  setForm({
    ...form,            // 1) copio TODOS los campos actuales
    [name]: value,      // 2) piso SOLO el campo que cambió
  });
}
```

Y todos los inputs usan ese mismo manejador, distinguiéndose por su `name`:

```javascript
<input name="nombre"       value={form.nombre}       onChange={manejarCambio} />
<input name="apellido"     value={form.apellido}     onChange={manejarCambio} />
<input name="email"        value={form.email}        onChange={manejarCambio} />
<input name="repetirEmail" value={form.repetirEmail} onChange={manejarCambio} />
```

Desarmemos el manejador, porque tiene dos sutilezas importantes:

- **`[name]: value`** → eso de las llaves alrededor de `name` se llama **clave computada** (*computed key*) y es JavaScript puro: significa "usá el **valor** de la variable `name` como nombre de la propiedad". Si `name` vale `"email"`, esto es lo mismo que `email: value`. Así, un único manejador actualiza el campo correcto según qué input se tocó.
- **`...form`** → este *spread* (los tres puntos) copia **todos** los campos actuales del objeto antes de pisar el que cambió.

> ⚠️ **Trampa clásica:** si te **olvidás** el `...form` y escribís solo `setForm({ [name]: value })`, **borrás todos los demás campos** y dejás únicamente el que cambió. Síntoma típico: escribís en "email" y se vacían "nombre" y "apellido". El `...form` es obligatorio para conservar el resto.

> 💡 Recordá del Módulo 4 (§5) que cuando el nuevo estado depende del viejo, lo más robusto es la forma de función. Acá aplicaría: `setForm((prev) => ({ ...prev, [name]: value }))`. Para un formulario simple, la versión con `...form` alcanza; tené presente la otra para cuando importe.

> 🎓 **Si te lo preguntan — "¿Cómo manejás un formulario con muchos campos en React?"**
> Se guarda todo el formulario en un único estado que es un objeto con todos los campos. A cada input se le pone un atributo `name` que coincide con la clave, y se usa un solo manejador `onChange` que actualiza el campo correspondiente con una clave computada: `setForm({ ...form, [name]: value })`. El spread conserva los demás campos y la clave computada pisa solo el que cambió.

---

## 4. 🔴 Enviar el formulario: `onSubmit` y `preventDefault`

Para enviar, envolvemos los campos en una etiqueta **`<form>`** (la de HTML, que ya conocés) y le ponemos un manejador en **`onSubmit`**. El botón de enviar lleva `type="submit"`:

```javascript
<form onSubmit={manejarEnvio}>
  {/* ...los inputs... */}
  <button type="submit">Comprar</button>
</form>
```

Pero hay un detalle **crítico**. Por defecto, cuando enviás un `<form>` de HTML, el navegador **recarga la página entera** (es el comportamiento histórico de los formularios web). Y recordá del Módulo 1: en una SPA **no queremos recargar nunca**, porque se pierde el estado y se rompe la fluidez. Para evitarlo, lo primero que hace el manejador es **`e.preventDefault()`**:

```javascript
function manejarEnvio(e) {
  e.preventDefault();   // 👈 frena la recarga por defecto del navegador
  console.log("Datos a enviar:", form);
  // acá: mandar los datos, redirigir, etc.
}
```

`e.preventDefault()` cancela el comportamiento por defecto del evento. Sin esa línea, tu app se recargaría enterita al apretar "Comprar" y todo lo que hicieras después no serviría de nada. **Es la línea que mantiene tu formulario dentro de la SPA.**

> 🎓 **Si te lo preguntan — "¿Por qué se usa `e.preventDefault()` al enviar un formulario?"**
> Porque por defecto el navegador recarga toda la página al enviar un `<form>`, y en una SPA eso rompe la app (se pierde el estado y la fluidez). `e.preventDefault()` cancela esa recarga para que el envío lo maneje React con JavaScript, sin abandonar la página.

---

## 5. 🔴 Validación: chequear antes de enviar

No querés dejar que el usuario envíe datos incompletos o incorrectos (un email vacío, dos emails que no coinciden). **Validar** es simplemente **escribir lógica de JavaScript sobre el estado del formulario** para decidir si está bien. Como `form` es un objeto común, validás con condiciones comunes:

```javascript
const formularioValido =
  form.nombre.trim() !== "" &&             // nombre no vacío
  form.apellido.trim() !== "" &&           // apellido no vacío
  form.email.trim() !== "" &&              // email no vacío
  form.email === form.repetirEmail;        // los dos emails coinciden
```

Esto es solo JavaScript:
- `.trim()` saca los espacios de los extremos (para que "   " no cuente como lleno).
- `!== ""` chequea que no esté vacío.
- `form.email === form.repetirEmail` chequea que los dos emails sean iguales.

`formularioValido` es un booleano (`true` o `false`) que se **recalcula en cada render** (recordá del Módulo 4: la función del componente corre de nuevo en cada cambio de estado). Así, a medida que el usuario escribe, esta variable refleja al instante si el formulario está listo o no. Ahora la usamos.

> Esto es validación "a mano", la más simple. Para reglas más finas (formato de email correcto, longitud mínima de contraseña, etc.) usarías más condiciones o expresiones regulares, pero la idea es la misma: **lógica de JS sobre el estado del formulario.**

---

## 6. 🔴 Deshabilitar el botón con `disabled`

Ahora conectamos la validación con el botón. Usamos el atributo **`disabled`** (que ya conocés de HTML), pero manejado por nuestra variable de validación: el botón está deshabilitado **mientras el formulario no sea válido**.

```javascript
<button type="submit" disabled={!formularioValido}>
  Comprar
</button>
```

Leé `disabled={!formularioValido}` así: "deshabilitado cuando el formulario **no** es válido". Mientras falten campos o los emails no coincidan, `formularioValido` es `false`, `!formularioValido` es `true`, y el botón está gris e inutilizable. En cuanto el usuario completa todo bien, se habilita solo.

Esto es una UX muy linda y muy común: el usuario no puede ni intentar enviar datos incompletos, y ve en tiempo real cuándo el formulario quedó listo. Y fijate cómo se combinan los módulos: el **estado** del formulario (Módulo 4) alimenta una **validación** (JS), que controla el **`disabled`** del botón, que se actualiza en cada **render**. Todo el ciclo de React trabajando junto.

---

## 7. 🟡 Después de enviar: redirigir y limpiar

Cuando el envío sale bien, lo habitual es **llevar al usuario a otro lado** y, a veces, **limpiar** el formulario. Para redirigir usamos `useNavigate` del Módulo 7:

```javascript
import { useNavigate } from 'react-router-dom';

function Checkout() {
  const navigate = useNavigate();
  const [form, setForm] = useState({ /* ...campos... */ });

  function manejarEnvio(e) {
    e.preventDefault();

    // (acá iría el envío real de los datos al servidor)
    alert(`¡Gracias por tu compra, ${form.nombre}!`);

    setForm({ nombre: "", apellido: "", email: "", repetirEmail: "" }); // limpiar
    navigate("/");   // volver al inicio (Módulo 7)
  }
  // ...
}
```

Para limpiar el formulario, simplemente volvés a poner el estado en su valor inicial (todos los campos vacíos). Y `navigate("/")` lleva al usuario de vuelta a la home, sin recargar.

---

## 8. 🟢 Cómo encaja todo junto

El formulario de checkout completo, con todo lo del módulo. Plano y con `<input>` común (el "vestido" lindo viene en el Módulo 10):

```javascript
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';

function Checkout() {
  const navigate = useNavigate();

  // 1) Estado del formulario: un objeto con todos los campos (Módulo 8 §2)
  const [form, setForm] = useState({
    nombre: "",
    apellido: "",
    email: "",
    repetirEmail: "",
  });

  // 2) Un solo manejador para todos los campos (§3)
  function manejarCambio(e) {
    const { name, value } = e.target;
    setForm({ ...form, [name]: value });
  }

  // 3) Validación: ¿está todo bien? (§5)
  const formularioValido =
    form.nombre.trim() !== "" &&
    form.apellido.trim() !== "" &&
    form.email.trim() !== "" &&
    form.email === form.repetirEmail;

  // 4) Envío (§4 y §7)
  function manejarEnvio(e) {
    e.preventDefault();                       // no recargar (SPA, Módulo 1)
    alert(`¡Gracias, ${form.nombre}!`);
    navigate("/");                            // redirigir (Módulo 7)
  }

  return (
    <form onSubmit={manejarEnvio}>
      <input name="nombre"       value={form.nombre}       onChange={manejarCambio} placeholder="Nombre" />
      <input name="apellido"     value={form.apellido}     onChange={manejarCambio} placeholder="Apellido" />
      <input name="email"        value={form.email}        onChange={manejarCambio} placeholder="Email" type="email" />
      <input name="repetirEmail" value={form.repetirEmail} onChange={manejarCambio} placeholder="Repetir email" type="email" />

      {/* 5) Botón deshabilitado hasta que el formulario sea válido (§6) */}
      <button type="submit" disabled={!formularioValido}>
        Comprar
      </button>
    </form>
  );
}
```

Leelo de arriba a abajo y reconocé el módulo entero: **un objeto** de estado guarda todos los campos, **un manejador** los actualiza a todos, una **validación** decide si está listo, el botón se **deshabilita** según eso, y al **enviar** se frena la recarga y se redirige. Esto es, en esencia, cualquier formulario que vayas a escribir — el de checkout, el de registro de un paciente, el de cargar un turno. El patrón no cambia.

> 🕳️ **Madriguera — componentes de formulario de una librería visual**
> En una app real, en vez de `<input>` pelado vas a usar componentes lindos de una librería (como el `TextField` de Material UI). La **lógica no cambia en absoluto**: ese `TextField` también recibe `value` y `onChange` igual que un input. Lo único distinto es el aspecto visual. Eso es el Módulo 10.
> *Volvé al camino — el patrón que aprendiste acá es el que vale; el resto es maquillaje.*

> 🕳️ **Madriguera — librerías de formularios**
> Para formularios grandes y complejos (muchísimos campos, validaciones elaboradas, mensajes de error por campo) existen librerías dedicadas como **React Hook Form** o **Formik**, que reducen el código repetitivo y manejan errores por vos.
> *Volvé al camino — entender el formulario "a mano" es lo que te deja usar esas librerías con criterio.*

> Un detalle honesto de alcance: este ejemplo muestra la validación como un botón deshabilitado, que es lo más simple. Mostrar **mensajes de error específicos debajo de cada campo** ("el email no es válido") es un refinamiento que se agrega con más estado; el esqueleto que aprendiste es la base correcta. Y en el checkout real, el formulario también recibe el **carrito** por props para mostrarlo y vaciarlo al comprar — eso conecta con el Módulo 6 (lifting) y con el Context que viene ahora.

---
---

## ✅ Checkpoint — ¿lo agarraste?

Sin mirar, con tus palabras (las respuestas se resuelven en el complemento del recorrido, o me preguntás):

1. ¿Por qué conviene guardar un formulario en **un objeto** en vez de un `useState` por campo?
2. ¿Cómo hace **un solo manejador** para saber qué campo actualizar? ¿Qué papel juega el atributo `name` del input?
3. ¿Qué es una **clave computada** (`[name]: value`)? ¿Qué hace?
4. ¿Para qué sirve el `...form` en `setForm({ ...form, [name]: value })`? ¿Qué pasa si te lo olvidás?
5. ¿Qué pasa **por defecto** cuando se envía un `<form>` de HTML? ¿Por qué eso es un problema en una SPA?
6. ¿Qué hace **`e.preventDefault()`** y por qué es la primera línea del manejador de envío?
7. ¿Qué es **validar** un formulario en React? ¿Sobre qué se escribe la lógica de validación?
8. ¿Cómo se conecta la validación con el botón? ¿Qué hace `disabled={!formularioValido}`?
9. ¿Por qué `formularioValido` se actualiza solo mientras el usuario escribe? (pensá en el ciclo de render del Módulo 4)
10. Después de un envío exitoso, ¿qué dos cosas se suelen hacer y con qué herramientas?

---

## 👉 Qué viene en el Módulo 9

Ya tenés páginas, navegación y formularios. Queda un dolor pendiente que vengo sembrando desde el Módulo 6: el **carrito** que necesitan la navbar, el detalle y el checkout — componentes lejanos en el árbol. Pasarlo por props a través de todo es el **prop drilling**, y es tedioso. El Módulo 9 lo resuelve de raíz:

- El **problema del prop drilling**, ahora visto en su peor versión (el carrito).
- **Context API**: cómo darle a un componente acceso a un dato sin pasarlo por props por todo el árbol.
- Los **tres pasos** del context (crear, proveer, usar).
- **Custom hooks**: empaquetar lógica reutilizable (como el acceso al carrito) en tu propia herramienta.

Ahí cerramos el modelo de datos de React. Te espero en el Módulo 9.

---

**FIN DEL MÓDULO 8**
