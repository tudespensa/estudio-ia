# Estudio IA — estudio móvil para mini series

Prototipo funcional para organizar una mini serie desde móvil o computadora: biblia, personajes, episodios, escenas, prompts y exportación del brief. La interfaz es responsive y está preparada como PWA instalable. La instalación en pantalla de inicio requiere publicar el paquete bajo HTTPS.

## Qué funciona ahora

- Diseño adaptable a teléfono y escritorio, con navegación móvil.
- Biblia de serie, personajes, episodios, escenas y prompts con guardado en el almacenamiento local del navegador.
- Exportación del brief y preferencias de proveedor.
- Dos ayudas de texto mediante Gemini (con Buffed como respaldo opcional): propuesta de premisa y propuesta de sinopsis.
- Narración de voz por escena mediante ElevenLabs: botón Generar voz en el prompt de cada escena, con el diálogo o narración de la escena.
- Backend pequeño con la API key en el servidor, nunca en HTML ni en almacenamiento del navegador.
- Acceso opcional con contraseña para una instalación publicada.

## Qué no está conectado todavía

No genera imágenes ni video, y no monta/edita clips. La app tampoco sincroniza proyectos entre dispositivos: los datos siguen guardados en el navegador donde se crean. La integración de Buffed (respaldo opcional) se probó con un proveedor simulado, no con la cuenta real; hay que validarla con una clave nueva y una petición pequeña.

## Seguridad antes de probar

La API key apareció legible en una captura anterior. Revócala y crea otra en Buffed antes de conectarla. No la envíes por chat ni la pongas en `index.html`, capturas o archivos que publiques. Las peticiones de texto envían el contexto del proyecto a Buffed; no incluyas datos confidenciales de clientes sin revisar antes sus condiciones y privacidad.

## Ejecutar en una computadora

Requisitos: Node.js 20 o posterior; no hace falta instalar paquetes.

1. Duplica `.env.example` como `.env` en esta carpeta.
2. Edita `.env` en tu equipo:
   - `GEMINI_API_KEY`: pega ahí tu clave de Google AI Studio.
   - `ELEVENLABS_API_KEY`: pega ahí tu clave de ElevenLabs.
   - `APP_PASSWORD`: reemplaza el ejemplo por una contraseña privada, única y de al menos 14 caracteres. Para uso local se puede dejar vacío, pero no publiques la app sin establecerla.
3. En una terminal, desde esta carpeta, ejecuta `node server.mjs`.
4. En el mismo equipo abre `http://127.0.0.1:3000`. Si configuraste `APP_PASSWORD`, inicia sesión con esa contraseña.
5. Prueba **Proponer con IA** con contenido no sensible y el botón **Generar voz** en el prompt de una escena.

No compartas el archivo `.env`. Está excluido por `.gitignore` y `.dockerignore`.

## Uso desde el teléfono

Para usarla en el teléfono estando fuera de la computadora, primero hay que desplegar el servidor en un servicio HTTPS de confianza. Un móvil y una computadora en la misma red local no convierten `127.0.0.1` en una dirección accesible entre ambos. Para publicarla:

- Despliega como un único proceso Node o contenedor persistente, no como hosting de archivos estáticos o función serverless.
- Configura en el panel de secretos del hosting `GEMINI_API_KEY`, `GEMINI_MODEL`, `ELEVENLABS_API_KEY`, `ELEVENLABS_VOICE_ID` y `APP_PASSWORD` (única, larga y privada).
- Configura `NODE_ENV=production` y `HOST=0.0.0.0`. El hosting debe proporcionar `PORT` y terminar HTTPS delante del proceso.
- No publiques un `.env` ni escribas secretos en el repositorio, Dockerfile o variables visibles al navegador.
- Cuando el dominio HTTPS esté activo, abre la URL en el navegador del teléfono y usa **Añadir a pantalla de inicio / Instalar aplicación** si el navegador lo ofrece.

Las sesiones actuales viven en memoria del proceso; reiniciar el servidor puede cerrar las sesiones. Usa una sola instancia. La autenticación incluida es una protección básica de acceso, no reemplaza un sistema multiusuario ni una revisión de seguridad para clientes.

## Variables

| Variable | Uso |
| --- | --- |
| `GEMINI_API_KEY` | Clave privada de Gemini, solo servidor |
| `GEMINI_MODEL` | Modelo de texto de Gemini; valor de ejemplo `gemini-3.5-flash` |
| `ELEVENLABS_API_KEY` | Clave privada de ElevenLabs, solo servidor |
| `ELEVENLABS_VOICE_ID` | Voz por defecto para narrar; valor de ejemplo George (narrador) |
| `ELEVENLABS_MODEL` | Modelo de voz; valor de ejemplo `eleven_multilingual_v2` |
| `BUFFED_API_KEY` | Respaldo opcional para textos, solo si no hay clave de Gemini |
| `BUFFED_BASE_URL` | Base compatible con OpenAI; valor de ejemplo `https://api.buffed.cc/v1` |
| `BUFFED_MODEL` | Modelo de texto habilitado en tu cuenta de Buffed |
| `APP_PASSWORD` | Contraseña de entrada; obligatoria en `NODE_ENV=production` y de 14+ caracteres |
| `NODE_ENV` | Usa `production` en el hosting |
| `HOST` | `127.0.0.1` local; `0.0.0.0` en un contenedor publicado |
| `PORT` | Puerto del servidor; el hosting puede asignarlo automáticamente |

## Archivos principales

- `index.html`: interfaz y datos del proyecto en el navegador.
- `server.mjs`: servidor, login y proxy limitado a dos tareas de texto.
- `login.html`: pantalla de acceso.
- `manifest.webmanifest`, iconos y `sw.js`: instalación PWA; el service worker solo conserva recursos públicos, nunca la pantalla privada ni respuestas de la API.
- `Dockerfile`: imagen de contenedor para un host Node persistente.

Este paquete no está publicado en internet. Para ponerlo en línea hace falta elegir y autorizar una cuenta de hosting; no envíes la API key por chat para hacer ese paso.
