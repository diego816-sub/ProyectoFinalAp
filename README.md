# NexoVisita 1.10 — Servidor

Repositorio preparado para Render, Aiven MySQL, Cloudinary y Gmail API.
Aplicación académica de gestión de visitas técnicas de Diego Arriaga.

## Render

- Tipo: Web Service, Python 3, plan Free.
- Root Directory: `server`.
- Build Command: `pip install -r requirements-cloud.txt`.
- Start Command: `python cloud.py`.
- Health Check: `/health` (versión 1.10, API 4).

Conserva las variables privadas y el archivo secreto `ca.pem` configurados en Render. No subas sus valores a este repositorio.
El código crea tablas al iniciar; el coordinador se administra mediante `manage_cloud.py init` con configuración privada, según la guía.

Lee DESPLIEGUE_RENDER_AIVEN_CLOUDINARY.md para configurar las conexiones y GUIA_1.10.md para probar asignaciones, traslado y fotografías. VALIDACION.md distingue pruebas automatizadas de servicios reales.

Sube todo el contenido de esta carpeta a la raíz de GitHub. No subas el ZIP como único archivo: Render debe encontrar `server/cloud.py`.
La app Android está en el paquete completo NexoVisita_1.10.zip.

No incluye credenciales ni datos reales. No agregues cloud_config.json, mail_config.json, el JSON de Google, certificados, contraseñas ni bases de datos.
