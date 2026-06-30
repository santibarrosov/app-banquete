# 📦 Mi Inventario — App de Alquiler de Materiales

App móvil para gestionar stock y reservas de materiales de banquetes.  
Sincronización en tiempo real entre múltiples dispositivos vía Firebase.

---

## 🚀 Cómo subir la app en 3 pasos

### PASO 1 — Crear la base de datos en Firebase (gratis)

1. Entrá a [console.firebase.google.com](https://console.firebase.google.com)
2. Hacé clic en **"Crear un proyecto"** → ponele un nombre (ej: `alquiler-materiales`)
3. Desactivá Google Analytics (no es necesario) → **Crear proyecto**
4. En el menú izquierdo → **Realtime Database** → **Crear base de datos**
   - Elegí una ubicación (ej: `us-central1`)
   - Seleccioná **"Empezar en modo de prueba"** → Habilitar
5. En el menú izquierdo → **Configuración del proyecto** (ícono ⚙️)
6. Bajá hasta **"Tus apps"** → hacé clic en el ícono `</>`  (Web)
7. Registrá la app con cualquier nombre → **Registrar app**
8. Copiá el objeto `firebaseConfig` que aparece. Se ve así:

```js
const firebaseConfig = {
  apiKey:            "AIzaSy...",
  authDomain:        "tu-proyecto.firebaseapp.com",
  databaseURL:       "https://tu-proyecto-default-rtdb.firebaseio.com",
  projectId:         "tu-proyecto",
  storageBucket:     "tu-proyecto.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123:web:abc..."
};
```

9. Abrí el archivo `index.html` con cualquier editor de texto
10. Buscá el bloque `firebaseConfig` cerca del principio y **reemplazá los valores** con los tuyos

---

### PASO 2 — Subir a GitHub

1. Creá una cuenta en [github.com](https://github.com) si no tenés
2. Hacé clic en **"New repository"** (botón verde)
   - Nombre: `alquiler-materiales` (o el que quieras)
   - Visibilidad: **Public**
   - ✅ Tildar "Add a README file"
   - Crear repositorio
3. En el repositorio, hacé clic en **"Add file" → "Upload files"**
4. Arrastrá el archivo `index.html` → **Commit changes**

---

### PASO 3 — Activar GitHub Pages (para que sea una URL pública)

1. En tu repositorio → **Settings** (arriba a la derecha)
2. En el menú izquierdo → **Pages**
3. En "Source" seleccioná **"Deploy from a branch"**
4. Branch: **main** → carpeta: **/ (root)** → **Save**
5. Esperá 1-2 minutos
6. GitHub te da una URL del tipo:  
   `https://TU-USUARIO.github.io/alquiler-materiales/`

**¡Esa URL es la app!** Guardala en los 3 teléfonos como acceso directo.

---

## 📱 Agregar a la pantalla de inicio del celular

**iPhone (Safari):**  
Abrí la URL → Compartir → "Agregar a pantalla de inicio"

**Android (Chrome):**  
Abrí la URL → Menú ⋮ → "Agregar a pantalla de inicio"

---

## 🔄 Cómo funciona la sincronización

- Los datos se guardan en Firebase Realtime Database
- Cuando cualquier teléfono hace un cambio, los otros lo ven **en menos de 1 segundo**
- Si no hay internet, la app guarda los cambios localmente y los sube cuando vuelve la conexión

---

## 📂 Estructura del proyecto

```
alquiler-materiales/
└── index.html    ← toda la app en un solo archivo
```

---

## 📧 Configurar el resumen mensual por email (EmailJS)

Cada día 1° del mes, la app intenta enviar automáticamente un resumen del mes anterior (reservas, presupuestado, cobrado, gastos y balance) al mail configurado en `EMAIL_DESTINO` dentro de `index.html`. Para que funcione hay que crear una cuenta gratuita en EmailJS:

1. Entrá a [emailjs.com](https://www.emailjs.com) → **Sign Up** (plan gratis: 200 emails/mes)
2. **Email Services** → **Add New Service** → elegí tu proveedor (ej. Gmail) y conectá tu cuenta → copiá el **Service ID**
3. **Email Templates** → **Create New Template** → pegá este contenido:

   - **Asunto:** `Resumen {{month_name}} {{year}} — El Rincón`
   - **Cuerpo:**
     ```
     Resumen de {{month_name}} {{year}}

     Reservas del mes: {{reservas_count}}
     Presupuestado: {{total_presupuestado}}
     Cobrado: {{total_cobrado}}
     Gastos: {{total_gastos}}
     Balance: {{balance}}

     Detalle de reservas:
     {{detalle}}

     Detalle de gastos:
     {{detalle_gastos}}
     ```
   - En el campo **"To Email"** de la plantilla, poné `{{to_email}}`
   - Guardá y copiá el **Template ID**

4. **Account** → **General** → copiá tu **Public Key**
5. Abrí `index.html`, buscá el bloque cerca del principio:
   ```js
   const EMAILJS_SERVICE_ID  = 'TU_SERVICE_ID';
   const EMAILJS_TEMPLATE_ID = 'TU_TEMPLATE_ID';
   const EMAILJS_PUBLIC_KEY  = 'TU_PUBLIC_KEY';
   ```
   y reemplazá los tres valores con los que copiaste.

Una vez que tengas esos tres datos, pasámelos y te los dejo cargados directamente en el código.

---

## 🔒 Asegurar las reglas de Firebase

El modo "prueba" del PASO 1 deja la base de datos abierta a **cualquiera** que tenga la URL del proyecto (no hace falta conocer la app). Para cerrarla sin agregar login:

1. En Firebase Console → **Realtime Database** → pestaña **Reglas**
2. Reemplazá el contenido por el de `firebase-rules.json` (incluido en este proyecto)
3. **Publicar**

Estas reglas:
- Bloquean cualquier ruta que no sea `stock`, `reservas`, `gastos`, `nextId` o `lastEmailSent` (antes, en modo prueba, se podía leer/escribir cualquier cosa)
- Validan que cada registro tenga la forma mínima esperada (evita que un escritor accidental o malicioso rompa la base con datos corruptos)

⚠️ Importante: como la app no tiene login, esto **no impide** que alguien con la URL del proyecto siga leyendo o escribiendo datos válidos — solo evita que la base quede totalmente abierta a cualquier estructura. Si en el futuro se necesita bloquear el acceso por completo, el siguiente paso sería agregar autenticación (PIN o usuario/contraseña) con Firebase Auth.

---

## 🛟 Soporte

Si algo no funciona, revisá:
- Que el `databaseURL` en el `firebaseConfig` esté copiado exactamente
- Que las reglas de Firebase estén en modo "prueba" (permiten lectura/escritura) o que hayas aplicado `firebase-rules.json`
- Que la URL de GitHub Pages esté bien formada
