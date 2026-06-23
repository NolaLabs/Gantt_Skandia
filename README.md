# Gantt Oficial — Piloto SkandIA (NOLA LABS)

Tablero interactivo de seguimiento del piloto de IA de SkandIA: metodología ágil por **sprints**, 10 semanas (22 jun – 28 ago 2026), con checkboxes, edición de tareas, % de avance, vista Gantt y seguimiento por champion.

Sincroniza el avance en la nube (Firebase Firestore) para que esté **siempre actualizado en cualquier dispositivo** (computador, celular, tablet).

---

## Archivos del repo

| Archivo | Qué es |
|---|---|
| `index.html` | La aplicación completa (un solo archivo). Es lo que sirve GitHub Pages. |
| `GUIA_Backend_Sync.md` | Guía paso a paso para conectar el backend de Firebase (sincronización). |
| `README.md` | Este documento. |

---

## Cómo publicarlo en GitHub Pages (desde la web, sin terminal)

1. Crea un **repositorio nuevo** en GitHub (botón **New**). Ponle un nombre, ej. `gantt-skandia`.
   - **Lee antes la nota de confidencialidad de más abajo** para decidir entre repo público o privado.
2. Entra al repo → **Add file → Upload files** → arrastra `index.html`, `GUIA_Backend_Sync.md` y `README.md` → **Commit changes**.
3. **Settings → Pages**:
   - **Source:** `Deploy from a branch`
   - **Branch:** `main` y carpeta `/ (root)` → **Save**
4. Espera 1–2 minutos. Tu tablero quedará en:
   `https://TU-USUARIO.github.io/gantt-skandia/`
5. Abre esa URL en el celular y guárdala en la pantalla de inicio.

> Al subir cambios al `index.html` (ej. renombrar procesos), GitHub Pages se actualiza solo en 1–2 min.

---

## Activar la sincronización (Firebase)

Sin esto, el tablero funciona pero **en modo local** (cada dispositivo guarda su propia copia). Para que se sincronice entre dispositivos:

1. Sigue **`GUIA_Backend_Sync.md`** (crear proyecto Firebase, Firestore, login anónimo, reglas).
2. Pega tu config en el bloque `FIREBASE_CONFIG` que está al inicio del `<script>` dentro de `index.html`.
3. **Importante para GitHub Pages:** agrega `TU-USUARIO.github.io` en
   **Firebase Console → Authentication → Settings → Authorized domains**.
   Si no lo haces, el login anónimo se bloquea y verás "Sin conexión / Error".
4. Abre la URL: arriba debe decir **"● Sincronizado en la nube"** (verde).

---

## ⚠️ Confidencialidad (léelo antes de publicar)

Este tablero contiene información de cliente (nombres de champions, procesos y el plan de SkandIA), que está dentro de `index.html` como datos por defecto.

- **GitHub Pages gratis sirve desde un repo PÚBLICO** → cualquiera con la URL (o que encuentre el repo) podría ver ese contenido.
- Recomendaciones, de más a menos segura:
  1. **Repo privado + GitHub Pages** → requiere un plan **GitHub Pro/Team** (de pago). Mantiene todo privado.
  2. **Repo público pero genérico** → puedo entregarte una versión donde los nombres reales NO van en el código (quedan solo en tu Firestore privado, que sí está protegido). Dime y te la preparo.
  3. **Repo público tal cual** → solo si te es indiferente que el plan sea visible.
- Sobre la **`apiKey` de Firebase**: en apps web no es un secreto crítico; la seguridad real la dan las **reglas de Firestore** (en la guía) que limitan el acceso a sesiones autenticadas. Aun así, conviene restringir la API key en Google Cloud Console (HTTP referrers) si el repo es público.

---

## Uso rápido del tablero

- Marca el círculo para completar una tarea; mueve el deslizador para el % de avance.
- Clic en el texto para editar; el engranaje (⚙) abre fechas y notas; la ✕ elimina.
- Cambia entre **Tablero de sprints** y **Vista Gantt** arriba.
- Filtra por champion con los chips de colores.
- **Exportar / Importar** (JSON) para respaldos manuales.

Hecho por NOLA LABS.
