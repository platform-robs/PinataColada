# 🎉 Piñata Colada — Landing Page (Cotizaciones + Google Sheets + WhatsApp)

Landing page moderna y responsiva para **Piñata Colada**, con diseño premium (UI/UX), hero con logo animado tipo piñata, sección de tamaños, galería con carruseles infinitos, FAQ y formulario de cotización con:

✅ Guardado automático en **Google Sheets**  
✅ Generación de folio incremental tipo **COT-0001**  
✅ Envío de solicitud a **WhatsApp** con mensaje prellenado  
✅ Modal/popup con validaciones y experiencia fluida  

---

## 🚀 Demo (Local)

Solo abre `index.html` en tu navegador.

> Recomendación: usar **VS Code + Live Server** para evitar problemas con rutas.

---

## 🌐 Deploy en GitHub Pages

1. Ve a **Settings → Pages**
2. En **Branch**, selecciona:
   - `main`
   - carpeta `/root`
3. Guarda

GitHub te dará un link tipo:

`https://tuusuario.github.io/pinata-colada-landing/`

---

## 📁 Estructura del proyecto

```txt
/pinata-colada-landing
│── index.html
│
└── assets/
    └── logo.png
```
---


📌 **IMPORTANTE:**  
Tu logo debe llamarse exactamente:

assets/logo.png


---

## 🎨 Características principales

### ⭐ Hero con logo animado
- Logo centrado
- Sombra profesional
- Animación sutil tipo piñata colgando
- “Golpecito” cada ~3 segundos (muy sutil)

### 📦 Tamaños (cards)
- Chica / Mediana / Grande
- Cada card incluye ícono de piñata con tamaño distinto
- Al hacer clic se despliegan precios (editables en el HTML)

### 🖼️ Galería
- 2 carruseles infinitos:
  - Uno hacia la izquierda
  - Uno hacia la derecha

### 🧾 Cotización (Modal)
Formulario popup con:

- Nombre del cliente
- Tipo de piñata (chica, mediana, grande, 100% personalizada)
- Tema (¿de qué será tu piñata?)
- Fecha requerida (input tipo calendario)
- Tipo de evento
- Teléfono (opcional)
- Detalles extra (opcional)

Al enviar:
- Se guarda en Google Sheets
- Se genera folio tipo **COT-0001**
- Se abre WhatsApp al número: **4465165882**
- Se bloquea el botón y aparece mensaje de confirmación

### ❓ Preguntas frecuentes (FAQ)
- Acordeones con `<details>` modernos y responsive

### 🔒 Footer con popups legales
- Redes sociales
- Aviso de privacidad (popup)
- Términos y condiciones (popup)

---

## 🔧 Configuración del Google Sheets + Apps Script

El proyecto usa esta hoja:

📄 Google Sheets:
`1YoPCt6VqYuF0F0d7IG2NWtHHiNpW3R0DQOZ1Dmz2udM`

Hoja:
`COTIZACIONES`

## 🧠 Encabezados recomendados en la hoja

En la hoja `COTIZACIONES`, crea estos encabezados en la fila 1:

- A = Folio  
- B = Timestamp  
- C = Nombre  
- D = Tamaño  
- E = Tema  
- F = Fecha  
- G = Evento  
- H = Teléfono  
- I = Detalles  

📌 El sistema empieza a guardar desde la fila 2.


---

## 🧩 Apps Script (Code.gs)

En el proyecto real se debe incluir un `doPost(e)` que:

- reciba la información del formulario
- genere un folio incremental tipo `COT-0001`
- guarde la información en la hoja `COTIZACIONES`
- devuelva: `{ ok: true, folio: "COT-000X" }`


```js
const SPREADSHEET_ID = "1YoPCt6VqYuF0F0d7IG2NWtHHiNpW3R0DQOZ1Dmz2udM";
const SHEET_NAME = "COTIZACIONES";

function doPost(e){
  try{
    const body = JSON.parse(e.postData.contents);

    const ss = SpreadsheetApp.openById(SPREADSHEET_ID);
    const sh = ss.getSheetByName(SHEET_NAME);

    if(!sh) throw new Error("No existe la hoja: " + SHEET_NAME);

    // Encabezados recomendados:
    // A: Folio
    // B: Timestamp
    // C: Nombre
    // D: Tamaño
    // E: Tema
    // F: Fecha
    // G: Evento
    // H: Teléfono
    // I: Detalles

    const lastRow = sh.getLastRow();
    let nextNumber = 1;

    if(lastRow >= 2){
      const lastFolio = sh.getRange(lastRow, 1).getValue();
      const m = String(lastFolio).match(/COT-(\d+)/);
      if(m) nextNumber = Number(m[1]) + 1;
    }

    const folio = "COT-" + String(nextNumber).padStart(4, "0");

    const row = [
      folio,
      new Date(),
      body.nombre || "",
      body.tamano || "",
      body.tema || "",
      body.fecha || "",
      body.evento || "",
      body.telefono || "",
      body.detalles || ""
    ];

    sh.appendRow(row);

    return ContentService
      .createTextOutput(JSON.stringify({ ok: true, folio }))
      .setMimeType(ContentService.MimeType.JSON);

  }catch(err){
    return ContentService
      .createTextOutput(JSON.stringify({ ok: false, error: String(err) }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

---

## 🚀 Deploy del Apps Script como Web App

1. En Apps Script, ve a:  
   **Deploy → New deployment**
2. Selecciona:  
   **Web app**
3. Configura:
   - **Execute as:** Me
   - **Who has access:** Anyone
4. Da clic en **Deploy**
5. Copia la URL del Web App (termina en `/exec`)

Ejemplo:

`https://script.google.com/macros/s/XXXXXXX/exec`

---

## 🔌 Conectar la URL del Web App en la landing

En tu `index.html`, busca esta línea:

```js
const APPS_SCRIPT_WEBAPP_URL = "";
```
const APPS_SCRIPT_WEBAPP_URL = "https://script.google.com/macros/s/XXXXXXX/exec";

---


---

## 16) WhatsApp (mensaje automático)

```md
## 📲 WhatsApp (mensaje automático)

El proyecto envía la solicitud al WhatsApp:

📌 `4465165882`

En `index.html` puedes modificarlo aquí:

```js
const WHATSAPP_NUMBER = "4465165882";
```
📌 Para México, el sistema agrega 52 automáticamente.


---

## 17) Nota del folio

```md
## 🧾 Nota importante sobre el folio COT-0001

El folio se calcula con base en el último registro en la hoja.

Para evitar errores:

- No borres filas manualmente (o se puede repetir folio)
- No modifiques la columna A (folio)
- Mantén encabezados en fila 1
```

---

## 🎨 Personalización rápida

### Cambiar precios
En `index.html`, dentro de cada tarjeta, edita el bloque de precios.

### Cambiar textos (info, FAQ, etc.)
Todo es editable directamente en el HTML.

### Cambiar imágenes del carrusel
Puedes reemplazar los placeholders por imágenes reales.

---

## 🛡️ Licencia

Proyecto desarrollado para uso comercial de **Piñata Colada**.  
Puedes adaptarlo y modificarlo para el cliente.

---

## 💖 Hecho para convertir

Diseño pensado para:

- verse increíble
- ser rápido
- ser fácil de navegar
- generar cotizaciones con pocos clics
- funcionar perfecto en celular y PC

