# Guía — Backend de sincronización del Gantt SkandIA

Esta guía conecta tu tablero `Gantt_Skandia_Sync.html` a un backend en la nube (**Firebase Firestore**) para que el avance esté **siempre actualizado en cualquier dispositivo** (computador, celular, tablet) en tiempo real.

- **Gratis** (plan Spark de Firebase, de sobra para este uso).
- **Sin servidor que mantener** — Google se encarga de la infraestructura.
- **Privado** — solo accede quien tenga el archivo configurado.
- Mientras no configures nada, el tablero funciona en **modo local** (se guarda solo en ese navegador).

Tiempo estimado: **10–15 minutos**, una sola vez.

---

## Resumen de lo que vas a hacer

1. Crear un proyecto en Firebase (cuenta de Google).
2. Crear una "app web" y copiar su configuración.
3. Pegar esa configuración dentro del HTML.
4. Activar la base de datos (Firestore) y el inicio de sesión anónimo.
5. Poner las reglas de seguridad.
6. (Para abrir desde el celular) publicar el archivo en una URL.

---

## Paso 1 — Crear el proyecto en Firebase

1. Entra a **https://console.firebase.google.com** e inicia sesión con tu cuenta de Google.
2. Clic en **"Agregar proyecto" / "Add project"**.
3. Nómbralo, por ejemplo, `skandia-gantt`. Acepta los pasos (puedes desactivar Google Analytics, no hace falta).
4. Espera a que se cree y entra al proyecto.

## Paso 2 — Crear la "app web" y copiar la config

1. En la pantalla del proyecto, clic en el ícono **`</>`** (Web) para registrar una app web.
2. Ponle un apodo (ej. `gantt`) y clic en **"Registrar app"**. (No necesitas marcar Firebase Hosting aquí.)
3. Firebase te mostrará un bloque parecido a esto — **copia los valores**:

```js
const firebaseConfig = {
  apiKey: "AIzaSyXXXXXXXXXXXXXXXXXXXXXXXX",
  authDomain: "skandia-gantt.firebaseapp.com",
  projectId: "skandia-gantt",
  storageBucket: "skandia-gantt.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef123456"
};
```

## Paso 3 — Pegar la config en el HTML

1. Abre `Gantt_Skandia_Sync.html` con un editor de texto (Bloc de notas, TextEdit en modo texto plano, VS Code…).
2. Busca cerca del inicio del `<script>` este bloque:

```js
const FIREBASE_CONFIG = {
  apiKey: "",
  authDomain: "",
  projectId: "",
  appId: ""
};
const BOARD_ID = "skandia-piloto";
```

3. Rellena `apiKey`, `authDomain`, `projectId` y `appId` con tus valores del Paso 2. (Los demás campos no son obligatorios para esto.)
4. **`BOARD_ID`**: déjalo igual (`skandia-piloto`). Es la "llave" del tablero compartido: todos los dispositivos que usen el **mismo `BOARD_ID`** verán y editarán el **mismo tablero**.
5. Guarda el archivo.

> Nota: el `apiKey` de Firebase Web **no es un secreto** que comprometa tu cuenta; la seguridad real la dan las reglas del Paso 5.

## Paso 4 — Activar Firestore y el inicio de sesión anónimo

**Base de datos (Firestore):**
1. En el menú izquierdo: **Build → Firestore Database**.
2. Clic en **"Crear base de datos" / "Create database"**.
3. Elige una ubicación (ej. `nam5` o la más cercana) y créala. Si te pregunta por el modo, elige **producción** (luego pondremos las reglas en el Paso 5).

**Inicio de sesión anónimo (para que solo la app entre a los datos):**
1. En el menú izquierdo: **Build → Authentication → "Comenzar" / "Get started"**.
2. Pestaña **"Sign-in method"** → en la lista activa **"Anónimo / Anonymous"** → **Guardar**.

## Paso 5 — Reglas de seguridad

1. Ve a **Firestore Database → pestaña "Reglas" / "Rules"**.
2. Borra lo que haya y pega esto:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /boards/{doc} {
      allow read, write: if request.auth != null;
    }
  }
}
```

3. Clic en **"Publicar" / "Publish"**.

Esto permite leer/escribir el tablero **solo** a sesiones autenticadas (la app inicia sesión anónima automáticamente) y solo en la colección `boards`. Nadie sin tu archivo configurado puede entrar a los datos.

> ¿Solo quieres probar rápido sin auth? Puedes usar reglas abiertas temporales (`allow read, write: if true;`), pero **no las dejes así** para datos de cliente. Mejor usa las de arriba.

## Paso 6 — Probar

1. Abre el `Gantt_Skandia_Sync.html` (doble clic).
2. Arriba a la derecha, junto a los botones, debe aparecer **"● Sincronizado en la nube"** en verde.
   - Si dice **"Modo local"** (naranja): la config quedó vacía o mal pegada → revisa el Paso 3.
   - Si dice **"Sin conexión / Error"** (rojo): revisa que activaste Firestore (Paso 4) y publicaste las reglas (Paso 5).
3. Marca una tarea como hecha. Abre el mismo archivo en otra pestaña o dispositivo configurado igual: el cambio aparece en segundos.

---

## Para abrirlo desde el celular (publicar en una URL)

El archivo en tu computador no es accesible desde el teléfono. Necesitas una URL.

> **⚠️ Paso obligatorio cuando publicas en una URL (cualquier opción):** agrega tu dominio a Firebase para que el login anónimo funcione.
> Firebase Console → **Authentication → Settings → Authorized domains → "Add domain"** y agrega tu dominio (ej. `tu-usuario.github.io`, `nombre.netlify.app`, o `skandia-gantt.web.app`).
> Si no lo haces, en el celular verás "Sin conexión / Error" porque la auth se bloquea.

### Opción C — GitHub Pages (lo que vas a usar)
1. Sube los archivos a tu repo (ver `README.md`).
2. En el repo: **Settings → Pages → Source: "Deploy from a branch" → rama `main` / carpeta `/ (root)` → Save**.
3. En 1–2 min tendrás la URL `https://tu-usuario.github.io/nombre-del-repo/`.
4. **No olvides** agregar `tu-usuario.github.io` a los Authorized domains de Firebase (ver aviso de arriba).

### Opción A — Netlify Drop (la más fácil, sin instalar nada)
1. Entra a **https://app.netlify.com/drop**.
2. Arrastra el archivo `Gantt_Skandia_Sync.html` a la página.
3. Te da una URL (ej. `https://nombre-aleatorio.netlify.app`). Ábrela en el celular y guárdala como favorito / en la pantalla de inicio.
4. Cuando edites el HTML (ej. cambiar nombres de procesos), vuelve a arrastrarlo para actualizar, o crea una cuenta gratis para administrarlo.

> La URL es aleatoria y no está indexada, pero es accesible para quien la tenga. Como los **datos** viven en tu Firestore privado (protegido por las reglas), lo que se expone es solo la interfaz. Para una capa extra, Netlify permite proteger el sitio con contraseña en su plan pago.

### Opción B — Firebase Hosting (todo en Firebase, algo más técnico)
Requiere Node.js instalado. En una terminal, en la carpeta del archivo:

```bash
npm install -g firebase-tools
firebase login
firebase init hosting        # elige tu proyecto; carpeta pública: . ; single-page: No
# renombra el archivo a index.html o cópialo como index.html
firebase deploy
```

Te dará una URL `https://skandia-gantt.web.app`.

---

## Cómo funciona la sincronización (para que sepas qué esperar)

- El estado completo del tablero se guarda como **un documento** en Firestore (`boards/skandia-piloto`).
- Cada cambio se escribe a la nube (con un pequeño retardo de ~0.6s para no saturar) y se refleja en los demás dispositivos en **tiempo real**.
- Si dos personas editan **a la vez**, gana el último cambio guardado (last-write-wins). Para un PM o un equipo pequeño y coordinado es suficiente; no es un editor colaborativo tipo Google Docs celda por celda.
- Si te quedas sin internet, sigues editando (queda en local) y se sincroniza al volver la conexión.
- El botón **Exportar** sigue sirviendo para respaldos puntuales (JSON).

## Preguntas frecuentes

- **¿Cuánto cuesta?** El plan gratuito de Firebase cubre de sobra este uso (lecturas/escrituras mínimas).
- **¿Y la confidencialidad del cliente?** Los datos quedan en tu proyecto privado de Firebase, accesibles solo con las reglas de auth. No se publican en GitHub ni en ningún repo.
- **¿Puedo tener varios tableros?** Sí: cambia `BOARD_ID` (ej. `skandia-fase2`) y tendrás un tablero independiente.
- **¿Cómo sumo a los champions en julio?** Comparte la URL (Paso "celular") con quienes deban actualizar. Todos verán y editarán el mismo tablero.

---

¿Te trabas en algún paso? Dime en qué paso vas y te guío puntual.
