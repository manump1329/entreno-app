# 💪 Cuaderno de entreno — Manu

App web PWA para planificación y seguimiento de entrenamientos de fuerza y running.

---

## Estructura de archivos

```
entreno-app/
├── index.html              ← App completa (1 sola página)
├── manifest.json           ← PWA manifest
├── sw.js                   ← Service Worker (offline)
└── icons/
    ├── icon-192.png
    ├── icon-512.png
    └── apple-touch-icon.png
```

---

## PASO 1 — Crear repositorio en GitHub

1. Ve a **github.com** → botón verde **New** → crea un repo llamado `entreno-app`
2. Márcalo como **Public** (necesario para GitHub Pages gratis)
3. **No** inicialices con README

---

## PASO 2 — Subir los archivos

### Opción A: Desde la web de GitHub (más fácil)

1. En tu nuevo repositorio vacío, haz clic en **"uploading an existing file"**
2. Arrastra todos los archivos (index.html, manifest.json, sw.js) y la carpeta `icons/`
3. Haz clic en **Commit changes**

### Opción B: Con Git (terminal)

```bash
cd entreno-app
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/entreno-app.git
git push -u origin main
```

---

## PASO 3 — Activar GitHub Pages

1. En tu repositorio → **Settings** → **Pages** (menú lateral izquierdo)
2. En *Source* selecciona **Deploy from a branch**
3. Branch: **main** / folder: **/ (root)**
4. Haz clic en **Save**
5. En 1-2 minutos tu app estará en: `https://TU_USUARIO.github.io/entreno-app/`

---

## PASO 4 — Configurar Firebase (para sincronización entre dispositivos)

### 4.1 Crear proyecto Firebase

1. Ve a **console.firebase.google.com**
2. Haz clic en **Add project** → ponle nombre (ej. "entreno-manu")
3. Desactiva Google Analytics si quieres → **Create project**

### 4.2 Activar Authentication (Google)

1. En el panel de Firebase → **Authentication** → **Get started**
2. En la pestaña *Sign-in method* → **Google** → actívalo
3. Pon tu email en *Project support email* → **Save**

### 4.3 Activar Firestore

1. **Firestore Database** → **Create database**
2. Selecciona **Start in production mode** → elige región (europe-west1 es buena opción)
3. **Done**

### 4.4 Añadir reglas de Firestore

Ve a **Firestore → Rules** y pega esto:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

Haz clic en **Publish**.

### 4.5 Obtener la configuración de Firebase

1. En Firebase → icono ⚙️ → **Project settings**
2. En *Your apps* → haz clic en **</>** (web)
3. Registra la app con el nombre "entreno-web" → **Register app**
4. Copia el objeto `firebaseConfig` que aparece

### 4.6 Añadir dominio de GitHub Pages a Firebase Auth

1. Firebase → **Authentication** → **Settings** → **Authorized domains**
2. Haz clic en **Add domain**
3. Añade: `TU_USUARIO.github.io`

### 4.7 Editar index.html con tu configuración

Abre `index.html` y busca este bloque (está cerca del principio del `<script>`):

```javascript
const FIREBASE_CONFIG = {
  apiKey: "TU_API_KEY",
  authDomain: "TU_PROYECTO.firebaseapp.com",
  projectId: "TU_PROYECTO_ID",
  storageBucket: "TU_PROYECTO.appspot.com",
  messagingSenderId: "TU_MESSAGING_SENDER_ID",
  appId: "TU_APP_ID"
};
```

Sustituye cada valor con los datos que copiaste de Firebase. Luego sube el archivo actualizado a GitHub (arrastra y suelta otra vez, o usa `git push`).

---

## PASO 5 — Instalar en el iPhone como app

1. Abre Safari en el iPhone
2. Ve a tu URL: `https://TU_USUARIO.github.io/entreno-app/`
3. Toca el botón **Compartir** (cuadrado con flecha hacia arriba)
4. Toca **Añadir a pantalla de inicio**
5. Ponle el nombre que quieras → **Añadir**

¡Listo! Verás el icono de la kettlebell en tu pantalla de inicio y la app se abrirá en modo pantalla completa.

---

## Funcionalidades

| Pestaña | Qué hace |
|---------|----------|
| **Plan** | Semana actual con checkboxes por ejercicio · navegador entre semanas pasadas |
| **Ejercicios** | Biblioteca completa · vídeo YouTube por ejercicio · diagrama de músculos |
| **Fuerza** | Registro de pesos por ejercicio · gráfica de progresión · historial |
| **Running** | Registro de carreras · ritmo calculado automáticamente · evolución |
| **Cuerpo** | Registro de peso · IMC automático (altura 178 cm) · gráfica de evolución · recordatorio cada 15 días |
| **Objetivos** | Notas libres · objetivos mensuales/trimestrales/anuales |

---

## Automatización semanal (Claude)

Cada domingo a las 17:00 (hora España) Claude analiza la semana, guarda los datos y publica el plan de la semana siguiente automáticamente. No tienes que hacer nada.

---

## Preguntas frecuentes

**¿Mis datos están seguros?**
Sí — cada usuario solo puede leer y escribir sus propios datos en Firestore (lo garantizan las reglas del paso 4.4).

**¿Funciona sin conexión?**
Sí, el Service Worker cachea la app. Sin conexión puedes ver los datos guardados pero no sincronizarán hasta que vuelvas a tener internet.

**¿Puedo usarlo desde el ordenador?**
Sí — la misma URL funciona en cualquier navegador en cualquier dispositivo, y los datos se sincronizan automáticamente gracias a Firebase.
