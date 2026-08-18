# Lista de regalos — Baby shower gemelas

Página de una sola pieza (`index.html`). Los invitados eligen artículos y
cantidad, ven el total, y al **confirmar**, el cupo se descuenta de verdad
para todos — en tiempo real, usando Firebase (gratis).

---

## Parte 1 · Activar el backend compartido (Firebase)

Esto toma ~10 minutos y es gratis para este uso.

1. Ve a **console.firebase.google.com** y entra con una cuenta de Google
   (puede ser la tuya).
2. **Crear un proyecto** → ponle un nombre, por ejemplo `regalos-gemelas` →
   puedes desactivar Google Analytics, no lo necesitas → **Crear proyecto**.
3. En el menú lateral, busca **Compilación → Realtime Database** → **Crear
   base de datos**.
   - Elige la ubicación más cercana (ej. `us-central1` funciona bien para Chile).
   - En "reglas de seguridad", elige **Modo de prueba** por ahora (lo
     ajustamos en el paso 5).
4. En el menú lateral, ve a **⚙️ Configuración del proyecto** → baja hasta
   "Tus apps" → click en el ícono **`</>`** (Web) → nómbrala como quieras →
   **Registrar app**. Te va a mostrar un bloque de código con un objeto
   `firebaseConfig = { apiKey: ..., authDomain: ..., ... }`.
5. Copia esos valores dentro de `index.html`, en la sección `CONFIG.firebase`
   (cerca del inicio del `<script>`, al final del archivo):

```js
firebase: {
  apiKey: "AIza...",
  authDomain: "regalos-gemelas.firebaseapp.com",
  databaseURL: "https://regalos-gemelas-default-rtdb.firebaseio.com",
  projectId: "regalos-gemelas",
  storageBucket: "regalos-gemelas.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
}
```

   `databaseURL` a veces no aparece en ese bloque — lo encuentras arriba de
   la pantalla de Realtime Database, justo bajo el título.

6. Vuelve a **Realtime Database → Reglas** y reemplaza el contenido por
   esto, luego **Publicar**:

```json
{
  "rules": {
    "claimed": {
      ".read": true,
      "$itemId": {
        ".write": "newData.isNumber() && newData.val() >= 0"
      }
    },
    "claims": {
      ".read": true,
      ".write": "newData.hasChildren(['itemCount'])"
    }
  }
}
```

   Esto deja que cualquiera con el link vea y reserve artículos (no hay
   login de invitados — es intencional, para que sea simple), pero evita que
   alguien borre o corrompa los datos. Es un nivel de seguridad razonable
   para un link privado de baby shower, no para datos sensibles.

Con eso, cada vez que alguien confirme su selección, el cupo se descuenta
para todos en tiempo real (verás la barra "Conectando con la lista
compartida…" desaparecer cuando quede bien conectado). Además aparece un
mini "feed" en la página con quién ha confirmado regalos.

---

## Parte 2 · Editar artículos y nombres

Sigue en la sección `CONFIG` e `ITEMS` del mismo `<script>`:

```js
const CONFIG = {
  names: "las gemelas",          // ej: "Emilia y Amanda"
  whatsappNumber: "56900000000", // WhatsApp de los papás, formato 569XXXXXXXX
  firebase: { ... }              // lo que copiaste en la Parte 1
};
```

Cada artículo:

```js
{ id:"panales-rn", name:"Pack pañales talla RN", price:9990, stock:6, category:"Higiene", image:"images/panales.jpg" },
```

- `id`: único, sin espacios ni tildes.
- `stock`: cuántas unidades hay disponibles en total (el sistema descuenta
  de aquí en tiempo real).
- `image`: ruta a la foto — ver Parte 3.

---

## Parte 3 · Subir las fotos de los artículos

**Opción A — más fácil: súbemelas aquí en el chat.** Si me adjuntas las
fotos en esta conversación, te las dejo ya nombradas exactamente como las
espera `index.html` (ej. `images/panales.jpg`), listas para que solo
arrastres la carpeta a GitHub, sin tener que renombrar nada tú.

**Opción B — subirlas tú directamente a GitHub:**

1. Entra a tu repositorio en GitHub.
2. Click en **Add file → Create new file**.
3. En el nombre escribe `images/.gitkeep` (esto crea la carpeta `images`) y
   guarda el commit.
4. Entra a la carpeta `images` que se creó → **Add file → Upload files** →
   arrastra tus fotos.
5. Asegúrate de que el nombre de cada foto coincida con lo que pusiste en
   `image` dentro de `ITEMS` (mismas mayúsculas/minúsculas, mismo formato:
   `.jpg`, `.png`, etc.).

Si un artículo todavía no tiene foto subida, no pasa nada: la tarjeta
muestra automáticamente un ícono de reemplazo en vez de un espacio roto.

---

## Parte 4 · Publicar en GitHub Pages

1. Crea un repositorio (público o privado, ambos funcionan con Pages).
2. Sube `index.html` (y la carpeta `images`).
3. **Settings → Pages** → Source: rama `main`, carpeta `/root` → **Save**.
4. En 1-2 minutos tendrás tu URL pública (`https://tu-usuario.github.io/tu-repo/`)
   para compartir con los invitados.

Cada vez que subas cambios a `index.html`, la página pública se actualiza sola.
