# NexoVisita 1.10 — Asignación, traslado y evidencias

Proyecto académico de Diego Arriaga. Actualizado el 21 de octubre de 2026.

## Flujo para presentar con dos cuentas

1. El coordinador registra un cliente con dirección y ubicación en el mapa. El punto elegido debe corresponder a la dirección real.
2. Invita a un técnico y espera a que active su cuenta. Asigna la visita indicando fecha, hora, trabajo y prioridad.
3. La visita aparece al actualizar la agenda del técnico. El correo incluye cliente, dirección, fecha, hora, trabajo y enlace de indicaciones. El correo se envía mediante la cola del servidor; puede tardar y debe estar configurado Gmail API.
4. El técnico abre la visita y pulsa **Cómo llegar y compartir traslado**.
5. Pulsa **Iniciar traslado compartido**, lee la confirmación y concede ubicación precisa. Se inicia una notificación con botón **Detener**. La transmisión se limita a esta visita y a la cuenta asignada.
6. Pulsa **Cómo llegar · Google Maps**, elige caminando, vehículo o transporte público. Google Maps calcula y muestra las indicaciones. La disponibilidad de navegación depende de Google Maps y del modo de transporte.
7. El coordinador abre la misma visita y pulsa **Seguimiento del traslado**. Puede ver destino, puntos recibidos, segmentos GPS, distancia aproximada, duración, precisión y antigüedad. El marcador se anima entre posiciones recibidas. Puede mover el mapa, centrarlo o cambiar a satélite.
8. El técnico puede detener el traslado desde la pantalla o notificación. Registrar la llegada con GPS también solicita detenerlo. El servidor deja de admitir actualizaciones activas después de la llegada o del cierre.
9. El técnico adjunta fotografías antes/después, marca tareas y documenta el resultado. Al completar la visita se envían las evidencias; el coordinador las consulta desde el detalle. No es un chat de imágenes ni un envío instantáneo de fotos mientras se redacta el borrador.
10. En un traslado terminado, **Reproducir recorrido terminado** mueve el marcador sobre una muestra de los puntos históricos. Es una animación ilustrativa, no una reproducción con los tiempos originales ni la posición actual.

## Qué puede comprobar el coordinador

Puede revisar visualmente por dónde pasó el técnico y compararlo con el destino. La app no declara automáticamente que una ruta sea correcta, ni acusa desvíos: tráfico, cierres, transbordos y pérdida de señal pueden explicar variaciones.

Las indicaciones de Google Maps no se importan como ruta prevista dentro de NexoVisita. Esta opción evita añadir Routes API. El mapa interno sigue necesitando Maps SDK for Android y su clave válida. Un trazado GPS no prueba por sí solo que se realizó el mantenimiento.

## Conexión y límites

- Se intenta enviar una instantánea cada 15 segundos después de terminar la solicitud anterior. El coordinador consulta cada 15 segundos mientras su pantalla está visible. No es seguimiento instantáneo y el tiempo aumenta con una mala conexión o el despertar de Render.
- Una entrega o una señal GPS de más de 90 segundos se muestra como no reciente. La precisión es estimada; no garantiza una ubicación exacta.
- Para iniciar un traslado nuevo se necesita conexión. Durante uno activo, si se pierde Internet, se mantiene el registro en memoria y una copia cifrada periódica. Al volver la red, el siguiente envío incluye los puntos acumulados.
- Si se detiene sin conexión o Android termina el proceso, se puede usar **Enviar traslado pendiente** desde la misma visita, cuenta y servidor. Los segundos posteriores a la última copia pueden perderse si el proceso se cierra abruptamente. No se reinicia el GPS en secreto ni se reconstruyen posiciones que no se midieron.
- Los borradores de resultados tienen su propia cola automática. El traslado detenido pendiente se reenvía manualmente; son dos mecanismos diferentes.
- Una sesión vencida o acceso revocado detiene el envío. Inicia sesión de nuevo con la misma cuenta para resolver sus pendientes.
- Solo se mantiene un traslado activo por proceso del teléfono. Otro inicio para la misma visita se rechaza mientras el servidor haya recibido datos en los últimos dos minutos.
- Un nuevo traslado para una visita reemplaza su trazado compartido anterior, previa advertencia en la confirmación. Los recorridos guardados manualmente y adjuntos siguen siendo independientes. Inicios y detenciones quedan en el historial.
- El máximo por traslado es 30 000 puntos. Se corta la captura al alcanzar ese límite.
- El GPS en segundo plano depende de los permisos y de las restricciones de batería del teléfono. Debe probarse con pantalla bloqueada en dispositivos reales.

## Actualizar conservando la configuración

1. Conserva una copia de la versión anterior y de sus archivos privados. No borres la base Aiven, el servicio Render ni las variables ya configuradas.
2. Abre el proyecto completo 1.10 en Android Studio. Usa JDK 21, SDK 36 y deja que Android Studio configure su SDK local.
3. Copia tu `secrets.properties` de Maps a esta copia local. No lo publiques. Para actualizar la app instalada, usa la misma firma; no desinstales si deseas conservar borradores y recorridos.
4. Publica el servidor 1.10 del paquete limpio. El arranque crea la tabla `journeys` si falta y conserva las tablas existentes. Haz respaldo antes de actualizar una base con datos reales.
5. La app 1.10 exige API 4. `/health` debe mostrar `version: 1.10` y `api: 4`.
6. La app utiliza internamente `https://nexovisita-api.onrender.com`. Ya no existe el campo de dirección en acceso, activación ni recuperación. Al actualizar desde un servidor distinto se invalida solo la sesión local; inicia sesión con una cuenta de la base de Aiven. Los borradores y recorridos anteriores no se borran ni se migran automáticamente.
7. No se copiaron contraseñas ni bases reales en estos paquetes. El servidor todavía debe desplegarse y validarse con tus cuentas de proveedores.

## Publicar el paquete limpio en GitHub

Utiliza `NexoVisita_1.10_Servidor_Render.zip`, extráelo y sube su contenido a la raíz del repositorio, no el ZIP. Deben verse `server/cloud.py`, `server/requirements-cloud.txt` y `render.yaml`.

Render: Root Directory `server`; Build Command `pip install -r requirements-cloud.txt`; Start Command `python cloud.py`; Health Check `/health`. Conserva MySQL, Cloudinary, Gmail y el archivo secreto `ca.pem` configurados. No cambies el prefijo de Cloudinary si ya guardaste imágenes.

El paquete del servidor tiene ejemplos vacíos, `.gitignore`, fuentes y pruebas. No incluye `cloud_config.json`, `mail_config.json`, el JSON OAuth descargado, `ca.pem`, bases de datos, contraseñas, APK, firma ni carpetas de compilación. El ZIP completo Android también se entrega sin estos archivos privados.

La limpieza del ZIP no elimina secretos que alguien haya publicado antes en un repositorio. En este trabajo no se publicó ningún commit ni se modificó Render.

## Prueba manual antes de presentar

- Dos cuentas diferentes: el técnico B no debe acceder al traslado del técnico A.
- Asignar y recibir un correo con dirección y trabajo correctos.
- Abrir el modo de transporte en Google Maps y comprobar que el destino sea el elegido.
- Iniciar traslado, caminar una ruta conocida y observar desde el coordinador.
- Bloquear la pantalla y abrir Google Maps; confirmar que continúa la notificación.
- Quitar Internet durante el traslado: el coordinador debe ver datos antiguos, nunca una ubicación inventada. Reconectar y comprobar recuperación.
- Detener sin conexión, reconectar y reenviar el pendiente.
- Registrar llegada y comprobar que cesa el seguimiento activo.
- Adjuntar fotos antes/después, completar y verlas con el coordinador.
- Probar pantalla pequeña y texto grande; los controles del mapa se desplazan verticalmente.

Referencias: [Maps URLs](https://developers.google.com/maps/documentation/urls/get-started) y [servicios de ubicación en primer plano](https://developer.android.com/develop/background-work/services/fgs/restrictions-bg-start).
