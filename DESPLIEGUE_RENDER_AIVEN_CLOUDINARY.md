# NexoVisita 1.10: Render + Aiven MySQL + Cloudinary

Actualizado el 21 de octubre de 2026. Este paquete contiene la adaptación; NO implica que tus cuentas ya estén conectadas ni que se hayan trasladado tus datos.

## Qué cambia

El celular se conecta por HTTPS a Render. El servidor consulta MySQL en Aiven con TLS y guarda originales privados en Cloudinary. MySQL conserva el identificador y la huella de cada imagen, nunca una URL pública permanente. El servidor entrega las imágenes después de comprobar sesión y permisos. La app conserva su formato de borradores y cola de sincronización.

Gmail se utiliza mediante su API HTTPS: Render gratuito bloquea SMTP. La contraseña de aplicación de Gmail usada anteriormente NO sirve como refresh token OAuth. Maps continúa configurado únicamente en Android.

## 1. Preparar las cuentas sin tocar el proyecto anterior

En Aiven, abre tu servicio MySQL y crea una base exclusiva llamada `nexovisita`. Conserva la base del proyecto anterior. Copia de Connection information: host, puerto real, usuario y contraseña. Descarga su certificado CA (`ca.pem`). No uses 3306 por suposición: utiliza el puerto mostrado por Aiven.

Si has restringido las IP permitidas en Aiven, autoriza las direcciones de salida que Render muestra para tu servicio y tu IP mientras ejecutes la migración local. No desactives TLS.

En Cloudinary, utiliza tu entorno existente y copia Cloud name, API key y API secret del panel. Escoge un prefijo exclusivo, por ejemplo `nexovisita18`. No cambies los archivos ni los presets del proyecto anterior. Las cargas se firman en el servidor; no necesitas un preset unsigned.

Selecciona siempre los planes Free. Aiven limita recursos y Cloudinary contabiliza almacenamiento, tráfico y transformaciones; revisa sus paneles de consumo. No se ha contratado ningún plan.

## 2. Configurar la copia local para administrar la nube

Extrae el ZIP. En PowerShell, entra en la carpeta `server` de la nueva versión. Ejecuta:

```powershell
py -3 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-cloud.txt
Copy-Item cloud_config.example.json cloud_config.json
notepad cloud_config.json
```

Si ya existe `cloud_config.json`, no vuelvas a copiar el ejemplo encima. Completa MySQL y Cloudinary. En `MYSQL_SSL_CA` usa la ruta absoluta del certificado, con barras `/`, por ejemplo `C:/Users/diego/Documents/NexoPrivado/ca.pem`. En `GMAIL_FROM` coloca el correo dedicado al proyecto. Las demás variables Gmail se completan en el siguiente paso.

`cloud_config.json` contiene secretos: es solo para esta PC. No lo subas a GitHub, no lo envíes al compañero por chat y no lo copies dentro del APK. En Render se utilizarán variables privadas. Para otro administrador, configura su acceso por separado.

## 3. Autorizar Gmail para enviar por HTTPS

1. Abre Google Cloud Console con la cuenta del proyecto. Puedes usar el proyecto donde configuraste Maps.
2. En Biblioteca de APIs habilita **Gmail API**.
3. Configura Google Auth Platform / pantalla de consentimiento: nombre `NexoVisita`, correo de soporte y audiencia externa si utilizas Gmail personal.
4. En acceso a datos añade únicamente `https://www.googleapis.com/auth/gmail.send`.
5. Si está en modo Testing, añade como usuario de prueba el correo remitente del proyecto.
6. En Clientes crea un cliente OAuth de tipo **Aplicación de escritorio** y descarga su JSON a una carpeta privada fuera del repositorio.
7. Desde `server`, ejecuta cambiando la ruta:

```powershell
.\.venv\Scripts\python.exe authorize_gmail.py --client "C:/ruta/privada/cliente_oauth.json"
```

Se abrirá Google en tu navegador. Elige exactamente la cuenta de `GMAIL_FROM` y autoriza el envío. El asistente escucha solamente en localhost, valida state y PKCE, y guarda la autorización en `cloud_config.json` sin imprimirla. No solicita permiso para leer el buzón. Si cancelas o Google bloquea la autorización, se detiene.

En modo Testing los refresh tokens de estos permisos normalmente caducan en 7 días. Para una presentación próxima puedes reautorizar; para mantener el servicio debes revisar el estado de publicación y los requisitos de verificación de Google. Publicar el consentimiento no garantiza que una autorización nunca pueda revocarse. Después de reautorizar, actualiza también las variables en Render.

Comprueba la configuración y envía una prueba a tu propio correo:

```powershell
.\.venv\Scripts\python.exe manage_cloud.py check
.\.venv\Scripts\python.exe manage_cloud.py mail-test --recipient "TU_CORREO"
```

`check` comprueba MySQL y credenciales Cloudinary; la aceptación por Gmail se comprueba con `mail-test`. Comprueba la bandeja de entrada y spam.

## 4. Elegir: conservar datos o empezar vacío

Antes de instalar la versión 1.10 en los celulares, sincroniza todos los borradores pendientes con el servidor antiguo y guarda los recorridos en curso. Los borradores cifrados se separan por servidor: no se reasignan automáticamente a una dirección nueva.

### Conservar tus cuentas y visitas

Detén el servidor antiguo y conserva una copia independiente de `nexovisita.db`. Indica su ruta real. Primero simula:

```powershell
.\.venv\Scripts\python.exe manage_cloud.py migrate --source "C:/ruta/servidor-anterior/nexovisita.db"
```

Revisa las cantidades. Para efectuar la copia:

```powershell
.\.venv\Scripts\python.exe manage_cloud.py migrate --source "C:/ruta/servidor-anterior/nexovisita.db" --apply
```

La herramienta lee la base original sin modificarla, migra una copia temporal y exige que todas las tablas de destino estén vacías. Mantiene identificadores, usuarios, hashes de contraseñas, verificaciones, roles, clientes, visitas, rutas, auditoría y operaciones. Mueve las fotos incluidas en informes a Cloudinary. No copia sesiones ni códigos temporales: tendrás que iniciar sesión otra vez. La cola de avisos se conserva; los avisos pendientes pueden enviarse cuando Render arranque.

Si una copia falla, la transacción MySQL se revierte. Las imágenes ya subidas pueden quedar huérfanas en el prefijo exclusivo de Cloudinary; un reintento usa los mismos identificadores y las reutiliza. No hay transacción distribuida entre MySQL y Cloudinary. No borres el prefijo completo si ya existen visitas nuevas guardadas.

### Empezar sin datos anteriores

Solo si decidiste empezar vacío, usa:

```powershell
.\.venv\Scripts\python.exe manage_cloud.py init
```

Se solicitan nombre, correo y contraseña del coordinador. Al escribir la contraseña no aparecen caracteres: es normal. Si ya existe coordinador, el comando se detiene. No ejecutes `init` antes de una migración: dejaría el destino ocupado. El acceso inicial del coordinador usa su correo hasta activar/verificar y elegir usuario.

## 5. Preparar GitHub

Repositorio indicado: https://github.com/diego816-sub/ProyectoFinalAp

Si es privado, mantenlo privado y autoriza a Render a acceder específicamente a ese repositorio. No hace falta hacerlo público.

El paquete `NexoVisita_1.10_Servidor_Render.zip` incluye solo `server/`, `render.yaml`, `.gitignore` y esta guía. Extrae y coloca esos elementos en la raíz del repositorio, conservando su historial. No subas el ZIP como único archivo: Render necesita encontrar `server/cloud.py` y `server/requirements-cloud.txt`.

Si el repositorio ya tiene código, trabaja en una rama nueva y revisa los cambios antes de integrarlos. No reemplaces archivos ajenos al proyecto ni uses force push.

Antes del commit comprueba que no incluyas `.venv`, `cloud_config.json`, credenciales OAuth, bases de datos, `mail_config.json`, `secrets.properties`, certificados privados o contraseñas. `.gitignore` evita añadirlos desde esta versión, pero no elimina archivos ya registrados en Git.

## 6. Crear el servicio en Render

En Render selecciona **New > Web Service**, conecta GitHub y selecciona ProyectoFinalAp y la rama que contiene la versión 1.10. Configura:

| Campo | Valor |
|---|---|
| Language / Runtime | Python |
| Root Directory | `server` |
| Build Command | `pip install -r requirements-cloud.txt` |
| Start Command | `python cloud.py` |
| Instance Type | **Free** |
| Health Check Path | `/health` |

También se incluye `render.yaml` para Blueprint. La configuración manual anterior permite añadir todas las variables y el certificado antes del primer arranque.

En Environment añade todas las claves de `cloud_config.json` con sus valores, salvo `MYSQL_SSL_CA`: en Render debe valer `/etc/secrets/ca.pem`. No subas el JSON completo como archivo público. Añade además `HOST=0.0.0.0` y `PYTHON_VERSION=3.13.9`. Render proporciona `PORT` automáticamente.

En **Secret Files** crea `ca.pem` y pega el contenido del certificado CA descargado de Aiven. El servidor verifica tanto la CA como el nombre del servidor MySQL.

Comprueba especialmente:

```text
NEXOVISITA_DB_DRIVER=mysql
NEXOVISITA_MEDIA=cloudinary
NEXOVISITA_MAIL=gmail_api
MYSQL_SSL_CA=/etc/secrets/ca.pem
HOST=0.0.0.0
```

El arranque se detiene si intenta usar SQLite en Render o falta la configuración obligatoria. No crea cuentas públicas ni contraseñas predeterminadas.

## 7. Conectar el celular y comprobar de verdad

Cuando Render indique Live, abre la URL que Render te entregue seguida de `/health`. Debe responder JSON con `app=NexoVisita`, `version=1.10` y `api=4`.

En Android Studio abre el proyecto completo 1.10. Conserva tu `secrets.properties` de Maps de forma privada y utiliza la misma firma que tu instalación anterior si quieres actualizar sin desinstalar. En la app escribe únicamente `https://NOMBRE-REAL.onrender.com`, sin `/health`, sin puerto 8765 y sin 127.0.0.1.

Prueba con dos cuentas:

1. Inicia sesión como coordinador y comprueba las visitas importadas.
2. Invita un técnico y comprueba que Gmail entregue el código.
3. Activa el técnico, asigna una visita y registra llegada.
4. Adjunta una foto antes/después, completa tareas y cierra la visita.
5. Abre la evidencia como coordinador. Un técnico ajeno no debe verla.
6. Desconecta datos después de registrar llegada, prepara un resultado y reconecta. Debe enviarse una sola vez.
7. Reinicia el servicio Render y comprueba que cuentas, visitas y fotos permanezcan.
8. Apaga la PC y prueba desde datos móviles. Este es el criterio para confirmar independencia de la PC.

Render gratuito puede tardar alrededor de un minuto en despertar después de 15 minutos inactivo. Android 1.10 amplía la espera y muestra un mensaje; no repite automáticamente operaciones de creación que podrían duplicarse. Los resultados offline conservan sus identificadores de reintento.

## Límites y operación

- El servidor utiliza un bloqueo de transacciones por base para conservar las reglas de concurrencia del proyecto. Es apropiado para pocas cuentas de una demostración; las cargas simultáneas grandes requieren optimización.
- Los avisos en cola se procesan mientras Render está despierto. Su suspensión retrasa reintentos; no se promete envío en una hora exacta.
- La API no guarda datos duraderos en el disco de Render. MySQL y Cloudinary deben seguir disponibles.
- La política de retención automática en MySQL no está implementada. `retention.py` bloquea su modo MySQL para no aparentar respaldos ni borrar fotos sin una política revisada. Organiza respaldos privados y retención para ambos servicios antes de uso real.
- No se han contratado servicios ni cambiado tus cuentas por generar este paquete. El despliegue real requiere tus valores privados y pruebas de extremo a extremo.

## Fuentes de configuración

- https://aiven.io/docs/products/mysql/howto/connect-with-python
- https://aiven.io/docs/products/mysql/concepts/mysql-free-tier
- https://cloudinary.com/documentation/control_access_to_media
- https://render.com/docs/free
- https://render.com/docs/configure-environment-variables
- https://developers.google.com/identity/protocols/oauth2/native-app
- https://developers.google.com/identity/protocols/oauth2
