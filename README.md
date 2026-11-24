# Dashboard BMR · Business Monthly Review

Dashboard de una sola página (`index.html`) conectado en línea al tablero de Monday
`https://ibm.monday.com/boards/18439845138` (tablero **BMR**).

## Qué hace

- **Captura** – una tarjeta por área (Gastos de viaje, Cuentas por pagar, EMEA, Master Data, Activos fijos, Tesorería, Consolidación, Contabilidad, Collections, Cash App, Otros negocios, Billing), agrupadas por P2P/T&E, R2R y O2C. Los gerentes editan `Status`, `Mes`, **Highlights**, **Challenges**, **Operating Results** y **Solutions & Improvements**; cada cambio se guarda en Monday al salir del campo.
- **Diapositiva gerencial** – una lámina 16:9 de 4 columnas (01 Highlights, 02 Challenges, 03 Operating results, 04 Solutions & improvements) que se arma sola con lo capturado, con el filtro de mes. Se puede **descargar en PowerPoint** o imprimir a PDF.
- **Logos IBM y CEMEX** – aparecen arriba a la derecha en las dos láminas, en pantalla, PDF y PowerPoint. Van incrustados (base64) en `index.html`, sin archivos de imagen aparte. Se extrajeron de las imágenes de la página FARO; si tienes los logos oficiales en PNG, reemplaza las constantes `LOGO_IBM` y `LOGO_CEMEX` al inicio del `<script>` (usa data URI `data:image/png;base64,...`).
- **Lámina de indicadores** – segunda lámina con 5 KPIs y 4 gráficas que salen de los datos del tablero: estado por frente (P2P/R2R/O2C), distribución por estado, % de campos diligenciados y estado por mes. En el PowerPoint descargado las gráficas son **nativas** (clic derecho → Editar datos), no imágenes.
- **Columnas numéricas** – si agregan al tablero una columna de tipo *Numbers* (por ejemplo "Payment applications" o "SLA %"), la primera se grafica automáticamente por área en lugar de "Estado por mes". Sin cambios de código.
- **Revisión de redacción** – cada campo de la pestaña Captura tiene un botón **✓ Revisar redacción**. Detecta el idioma (español o inglés) y marca errores de ortografía, gramática y puntuación con sugerencias. El gerente aplica una sugerencia o **todas**; la corrección se guarda en Monday por el mismo autoguardado y el campo se vuelve a revisar. Usa [LanguageTool](https://languagetool.org): por defecto el servicio público (`api.languagetool.org`, con límites de uso). **El texto del campo se envía a ese servicio**, por eso se pide confirmación la primera vez; para información sensible, apunta en *Configuración* a un servidor LanguageTool propio (campo "URL del corrector").
- **Redactar con IA** – cada campo tiene un botón **✨ Redactar con IA**. El gerente escribe unas notas sueltas, pulsa el botón y el dashboard **copia al portapapeles un prompt ya armado** (campo, área, mes, idioma activo y las notas, con la instrucción de no inventar datos). La respuesta se pega de vuelta en el campo y luego se puede pasar el corrector de ortografía. Es un paso manual: no hay API para conectarlo en automático, y el dashboard no envía el texto a ningún servicio.
  - El asistente se elige en *Configuración → Asistente de redacción* (se guarda por navegador): **Solo copiar el prompt** (por defecto, no abre ninguna página, así funciona para quien no tenga acceso a ICA), **Lumini (IBM Consulting Advantage)**, **Microsoft Copilot** u **Otro enlace** con una URL propia.
  - Para Lumini la URL por defecto sale de la página FARO y se puede cambiar en el campo de URL que aparece al elegirlo. Si da "Unauthorized", la persona no tiene sesión o acceso a ICA: debe elegir otro asistente.
- **En línea** – relee Monday cada 60 s (si nadie está escribiendo), así varios gerentes ven los cambios de los demás.
- **Español / English** – el botón **EN / ES** del encabezado cambia la interfaz, los títulos y las gráficas de la diapositiva y del PowerPoint. El texto que escriben los gerentes (nombres de área, Highlights, Challenges, etc.) se traduce **en ambos sentidos (ES ⇄ EN) solo para mostrarlo**: se detecta el idioma de cada texto y solo se traduce si es distinto al de la interfaz. Usa el Translator API y el Language Detector integrados del navegador (Chrome/Edge recientes; el texto no sale del equipo). Si el navegador no tiene el detector, se usa una heurística simple por palabras. La traducción nunca se guarda en Monday. En **Captura** se muestra en un recuadro azul de solo lectura (🌐) debajo de cada campo, sin tocar el texto que editas. La primera vez, el botón **🌐 Activar traducción** del encabezado descarga el idioma en el navegador. El idioma elegido se recuerda en el navegador.

## Uso

1. Abre `index.html` (doble clic, o publícalo en GitHub Pages / servidor interno).
2. En la primera carga pega tu **token personal de Monday** (Avatar → Developers → My access tokens). Se guarda solo en tu navegador y respeta tus permisos sobre el tablero. Cada gerente usa su propio token, y Monday registra quién editó.
3. Elige el mes, completa los campos en **Captura** y pasa a **Diapositiva gerencial**.

## Notas

- Los gerentes deben tener permiso de edición en el tablero de Monday.
- Las columnas se detectan por su título (`Status`, `Mes`, `Person`, `Highlights`, `Challenges`, `Operating Results`, `Solutions & Improvements`). Si se renombran en Monday, hay que ajustar `FIELDS` en `index.html`.
- Cada línea del texto (Enter) se muestra como una línea en la diapositiva.
- La descarga de PowerPoint usa la librería PptxGenJS 3.12.0, incluida en el repositorio como `pptxgen.bundle.js` (no depende de ningún CDN). **Hay que subir ese archivo junto a `index.html`**, o el botón de PowerPoint no funcionará.

## Seguridad

- **Sin secretos en el código.** No hay tokens ni claves en `index.html`. El token de Monday lo pega cada persona y queda solo en el `localStorage` de su navegador.
- **Política de contenido (CSP)** en el `<head>`: solo se permiten scripts propios (sin dominios externos) y conexiones a `api.monday.com` y `api.languagetool.org`. Si usas un LanguageTool propio, agrega su dominio en `connect-src`.
- **Librería local.** PptxGenJS 3.12.0 viaja en el repositorio (`pptxgen.bundle.js`), sin CDN. No lleva atributo `integrity` porque el navegador lo bloquea al abrir el archivo con doble clic (`file://`), y al ser del mismo repositorio no aporta protección adicional. Su huella SHA-384 es `Cck14aA9cifjYolcnjebXRfWGkz5ltHMBiG4px/j8GS+xQcb7OhNQWZYyWjQ+UwQ` por si seguridad quiere verificar que coincide con la versión oficial 3.12.0.
- **Texto escapado.** Todo valor que viene de Monday se escapa antes de pintarse en pantalla (`esc()`), y el estado solo se usa como clase si es uno de los valores permitidos.
- **Envío de texto a terceros.** LanguageTool público recibe el texto del campo, y por eso se pide confirmación. Para información confidencial, usa un servidor propio. La redacción con IA no envía nada: solo copia el prompt al portapapeles.
- **Traducción local.** Usa el traductor integrado del navegador; el texto no sale del equipo.
- **Pendiente de revisión por IBM.** Las normas internas de IBM (por ejemplo, clasificación de datos, uso de CDN externos o de servicios de terceros) deben validarse con el equipo de seguridad del proyecto. Este documento no las sustituye.

