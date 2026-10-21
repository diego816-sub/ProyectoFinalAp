# Validación de NexoVisita 1.10

Fecha: 21 de octubre de 2026. Versión visible 1.10, código 12, API 4.

## Verificaciones completadas

- Se retiraron los campos de dirección del servidor del acceso, activación y recuperación. Todas las solicitudes utilizan https://nexovisita-api.onrender.com. Al cambiar de servidor se invalidan las credenciales locales anteriores, sin migrar cuentas ni borrar borradores.

- **Servidor: 74 pruebas aprobadas** con unittest. Incluyen las pruebas existentes de cuentas, roles, cierre, agenda, evidencias y migración, más 8 pruebas del traslado compartido.
- Traslados: acceso de otro técnico rechazado, actualización del coordinador rechazada, entrega duplicada sin sobrescritura, sesión antigua rechazada, llegada que desactiva seguimiento, coordenadas inválidas rechazadas, caducidad de señal y cuenta revocada.
- El correo generado incluye dirección, tarea y enlace al destino. En las pruebas el envío real está simulado.
- **Android: 17 pruebas de TrackingSession aprobadas**, sin fallos ni errores.
- **Compilación limpia y generación de APK debug completadas** con JDK 21, Gradle 9.6.0, SDK 36 y dependencias locales.
- **Lint debug completado: 0 errores y 48 advertencias**. Hay advertencias de internacionalización y recomendaciones de estilo; las pantallas nuevas están en español.
- Se utilizó compilación Kotlin no incremental y en proceso para resolver un fallo de caché/daemon de este entorno. No se deshabilitaron reglas de Lint para aprobar la revisión.
- El empaquetado excluye credenciales, bases, certificados, firmas, APK y carpetas de compilación. Se comprueban nombres privados y patrones de claves antes de entregar.

## No verificado en servicios o dispositivos reales

- No se ejecutó este servidor contra las cuentas reales de Aiven, Cloudinary, Gmail o Render. Las integraciones de nube usan dobles de prueba. Las pruebas de base de datos de esta ejecución utilizan SQLite.
- No se publicó ningún commit ni se desplegó esta versión en Render.
- No se verificó en un teléfono la duración de batería, GPS con pantalla bloqueada, comportamiento del fabricante, navegación externa, accesibilidad visual o recepción real de fotografías y correos.
- El APK de prueba entregado utiliza la configuración Maps existente y la firma debug habitual del usuario. No se ha instalado ni probado en su teléfono. El código fuente y los paquetes GitHub excluyen esa configuración privada y la firma.

## Comandos usados

Servidor (con dependencias de requirements-cloud.txt disponibles):

```text
python -m unittest discover -s server -v
```

Android (Gradle del proyecto, JDK 21):

```text
gradlew clean :app:testDebugUnitTest :app:assembleDebug :app:lintDebug --max-workers=2 -Pkotlin.incremental=false -Pkotlin.compiler.execution.strategy=in-process
```

En PowerShell, encierra los argumentos `-P...` entre comillas simples. El entorno de verificación utilizó además la firma debug habitual del usuario, rutas locales del SDK/JDK y el modo offline.

Antes de presentar sigue las pruebas de dos cuentas, desconexión y pantalla bloqueada de GUIA_1.10.md. La compilación y las pruebas automatizadas no sustituyen esa validación.
