# Versión 1.10 — 21 de octubre de 2026

- Acceso, activación y recuperación sin el campo Dirección del servidor.
- Dirección interna fija: https://nexovisita-api.onrender.com.
- Las sesiones de otro servidor no se reutilizan en la nube. Se conservan los datos locales existentes.
- Versión Android 1.10, código 12. Servidor 1.10, API 4 compatible con 1.9.
- Configuración de Maps conservada únicamente en la copia local y APK del usuario; fuera del paquete público.

# Versión 1.9 — 20 de octubre de 2026

- versionName 1.9, versionCode 11, contrato del servidor API 4.
- Correo de asignación/reprogramación con cliente, dirección, fecha, hora, trabajo y enlace a Google Maps.
- Navegación externa para caminar, conducir o usar transporte público.
- Traslado compartido voluntario con servicio de ubicación en primer plano, notificación y botón Detener.
- Consulta del coordinador por visita, trazado por segmentos, marcador animado, precisión, antigüedad y estados de señal.
- Vista satélite, seguimiento de cámara, encuadre del destino y reproducción histórica.
- Reintento automático mientras el traslado está activo y copia cifrada pendiente por cuenta/servidor/visita.
- Reenvío manual del último traslado pendiente después de detenerse o si Android cerró el proceso.
- Permisos de servidor, secuencias contra envíos fuera de orden y auditoría de inicio/fin.
- Se mantienen fotografías antes/después, checklist, confirmación del cliente y cierre con sincronización.
- Migración aditiva de journeys en SQLite/MySQL; no se borra la base anterior.
- Nuevo acceso por defecto al servidor HTTPS de Render; las instalaciones anteriores conservan su dirección guardada.

# NexoVisita 1.8

- Versión visible 1.8; versionCode 10; API 3 compatible con el contrato anterior.
- Adaptador MySQL con TLS validado, esquema InnoDB y transacciones serializadas para la demostración.
- Evidencias privadas en Cloudinary, comprobación de integridad y entrega autorizada por la API.
- Migración SQLite -> MySQL sobre destino vacío, preservando cuentas, contraseñas protegidas e historial.
- Gmail API HTTPS y asistente local de autorización OAuth con PKCE.
- Configuración Render Free, comandos privados de administración y guía de despliegue.
- Mayor tiempo de espera Android y mensajes para el arranque de Render.
- Los servicios externos no están configurados ni probados con las cuentas reales todavía.

## Versión anterior

# NexoVisita 1.7

- Versión visible 1.7; versionCode interno 9; API 3. El contador interno no se muestra.
- Migración aditiva compatible con cuentas, clientes, visitas y rutas anteriores.
- Evidencias antes/después, tareas y confirmación sencilla, con historial de atención.
- Reprogramación con motivo, prioridad, duración, agenda y conflictos horarios.
- Cola local cifrada de resultados con identificadores idempotentes y control de versión.
- Indicadores y exportación CSV; revocación de sesiones y herramienta de retención.
- Recuperación cifrada de recorridos en pausa; formularios adaptados al teclado y ancho disponible.
- Adaptador Waitress/WSGI y documentación para HTTPS. Despliegue público y ensayo en teléfonos pendientes.

## Historial anterior

# Historial de NexoVisita

## 1.6 - Acceso e invitaciones (2026-10-02)

- Campo de acceso titulado únicamente Usuario.
- Recuperación e invitación como acciones secundarias sin fondo, después de Iniciar sesión.
- Enlace de activación: ¿Recibiste una invitación? Activa tu cuenta.
- Creación de técnicos denominada Invitar técnico en el panel del coordinador.
- Versión visible 1.6; versionCode interno 8. Se mantiene la compatibilidad de cuentas anteriores.

## 1.5 — Correo y acceso por usuario (2026-10-02)

- Invitaciones a técnicos con código enviado por SMTP TLS; activación con usuario único y contraseña propia.
- Recuperación por correo verificado con códigos de un uso, caducidad, límites de intentos y revocación de sesiones.
- Avisos de visitas asignadas mediante cola persistente, estados visibles y reintento de envíos fallidos.
- Migración aditiva desde 1.4; las cuentas anteriores conservan acceso por correo hasta completar la verificación.
- Configuración para Gmail mediante contraseña de aplicación temporal en el entorno del servidor.
- Versión visible 1.5; versionCode interno 7. Sin contador adicional en pantalla.

## 1.4 — NexoVisita (2026-10-01)

- Nuevo enfoque: gestión de visitas técnicas para pequeñas empresas de mantenimiento.
- Nombre NexoVisita e icono vectorial original: ubicación, herramienta y tarea completada.
- Acceso por cuentas; coordinador y técnico con permisos comprobados por la API.
- Clientes con ubicación, equipo técnico y asignación de visitas por fecha.
- Llegada GPS, observaciones, cierre completado/no realizado y resumen compartible.
- Recorridos locales por cuenta y adjuntos al servidor visibles para el coordinador.
- Servidor Python/SQLite para demostración local, caché de consulta y borradores de observaciones.
- Validación de versiones concurrentes y registro de auditoría de acciones.
- Se mantienen el identificador Android, el almacenamiento anterior y el motor de distancia.
- Nueva interfaz de gestión en español; localizaciones anteriores preservadas para recorridos.
- Se incluyen guía de configuración, guion de presentación y pruebas de API.

## 1.3

- Nuevo nombre RubRabbit y logo de conejo en Inicio e ícono de Android.
- Perfil muestra únicamente Versión 1.3 en los tres idiomas.
- Versionado visible mayor.menor; código interno de Android creciente.
- Registro unificado a pie y en transporte terrestre, con techo de plausibilidad de 216 km/h y validación respecto a la última muestra válida.
- Solicitud de GPS cada segundo y avisos de señal interrumpida o trazado dividido.
- El tiempo incluye esperas en tráfico mientras el registro está activo. Comparación manual en Historial.
- Se mantienen datos, identificador de aplicación, idiomas y funciones en segundo plano.
- 14 pruebas unitarias del motor aprobadas. Falta validar la medición real en teléfono.

## 1.2

- Versión recibida del proyecto Paso a Paso. El filtro de caminata rechazaba velocidades superiores a 16,2 km/h.
