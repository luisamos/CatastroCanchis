# Catastro Canchis

Sistema web de gestión catastral desarrollado en **Laravel 9** para administración de usuarios, roles, sectores, manzanas, vías, habilitaciones urbanas, fichas catastrales, reportes, exportación Excel y generación de documentos PDF.

Este despliegue está orientado a **Windows + PostgreSQL**.
.

---

## 1. Tecnologías principales

* PHP 8.0.2 o superior
* Laravel 9
* Composer
* Node.js y npm
* Vite
* PostgreSQL
* PostGIS, recomendado para datos geográficos
* Apache o Nginx
* Livewire
* Laravel Sanctum
* Spatie Laravel Permission
* Laravel Excel
* mPDF
* Laravel Auditing

---

## 2. Requisitos para Windows

Instalar los siguientes componentes:

1. Git para Windows
2. PHP 8.0.2 o superior
3. Composer
4. Node.js LTS
5. PostgreSQL
6. PostGIS, recomendado
7. Apache, Nginx o servidor embebido de Laravel
8. Visual Studio Code u otro editor

En Windows puedes usar XAMPP solo para Apache/PHP, pero la base de datos debe ser PostgreSQL instalado de forma independiente.

---

## 3. Extensiones PHP requeridas

Editar el archivo `php.ini`.

En XAMPP normalmente está en:

```txt
C:\xampp\php\php.ini
```

Habilitar estas extensiones quitando el `;` inicial:

```ini
extension=curl
extension=fileinfo
extension=gd
extension=intl
extension=mbstring
extension=openssl
extension=zip
extension=pdo_pgsql
extension=pgsql
```

También verificar que Composer use el mismo PHP configurado.

Comprobar desde consola:

```bash
php -v
php -m
```

Debe aparecer:

```txt
pdo_pgsql
pgsql
```

Después de modificar `php.ini`, reiniciar Apache o cerrar y volver a abrir la consola.

---

## 4. Clonar el proyecto

Abrir PowerShell, CMD o Git Bash:

```bash
cd C:\xampp\htdocs
git clone https://github.com/JhonatanCastilloSullca/CatastroCanchis.git
cd CatastroCanchis
```

---

## 5. Instalar dependencias PHP

```bash
composer install
```

Si aparece error por extensiones faltantes, revisar nuevamente `php.ini`.

---

## 6. Instalar dependencias Node.js

```bash
npm install
```

Para desarrollo:

```bash
npm run dev
```

Para producción:

```bash
npm run build
```

---

## 7. Crear archivo `.env`

Crear el archivo `.env` en la raíz del proyecto.

Si existe `.env.example`, ejecutar:

```bash
copy .env.example .env
```

Si no existe, crear manualmente el archivo `.env` con esta configuración base:

```env
APP_NAME="Catastro Canchis"
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://127.0.0.1:8000

LOG_CHANNEL=stack
LOG_LEVEL=debug

DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=catastro_canchis
DB_USERNAME=catastro_user
DB_PASSWORD=ClaveSegura123*

DB_SCHEMA=public
DB_SCHEMA2=public

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MAIL_MAILER=smtp
MAIL_HOST=localhost
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="admin@catastrocanchis.local"
MAIL_FROM_NAME="${APP_NAME}"

VITE_APP_NAME="${APP_NAME}"
```

Generar la clave de Laravel:

```bash
php artisan key:generate
```

---

## 8. Crear base de datos PostgreSQL

La base se crea con `psql`, pgAdmin o DBeaver. Se recomienda usar `psql` para instalación técnica.

### 8.1. Ingresar a PostgreSQL

Abrir PowerShell o CMD:

```bash
psql -U postgres
```

Si pide contraseña, ingresar la contraseña definida durante la instalación de PostgreSQL.

---

### 8.2. Crear usuario de la aplicación

```sql
CREATE USER catastro_user WITH PASSWORD 'ClaveSegura123*';
```

---

### 8.3. Crear base de datos

```sql
CREATE DATABASE catastro_canchis
WITH OWNER = catastro_user
ENCODING = 'UTF8'
LC_COLLATE = 'Spanish_Peru.1252'
LC_CTYPE = 'Spanish_Peru.1252'
TEMPLATE = template0;
```

Si el comando anterior genera error por configuración regional, usar esta versión más compatible:

```sql
CREATE DATABASE catastro_canchis
WITH OWNER = catastro_user
ENCODING = 'UTF8'
TEMPLATE = template0;
```

---

### 8.4. Conectarse a la base de datos

```sql
\c catastro_canchis
```

---

### 8.5. Habilitar PostGIS

Si el proyecto va a manejar geometrías o datos espaciales, habilitar PostGIS:

```sql
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS postgis_topology;
```

Verificar:

```sql
SELECT postgis_full_version();
```

---

### 8.6. Dar permisos al usuario

```sql
GRANT ALL PRIVILEGES ON DATABASE catastro_canchis TO catastro_user;
GRANT ALL ON SCHEMA public TO catastro_user;
ALTER SCHEMA public OWNER TO catastro_user;
```

Salir de PostgreSQL:

```sql
\q
```

---

## 9. Probar conexión a PostgreSQL

Desde la raíz del proyecto:

```bash
php artisan tinker
```

Ejecutar:

```php
DB::connection()->getPdo();
```

Si no aparece error, Laravel está conectando correctamente con PostgreSQL.

Salir:

```php
exit
```

---

## 10. Ejecutar migraciones y seeders

Crear tablas y cargar datos iniciales:

```bash
php artisan migrate --seed
```

Si es una instalación local nueva y se desea reconstruir todo:

```bash
php artisan migrate:fresh --seed
```

Advertencia: `migrate:fresh --seed` elimina todas las tablas existentes y vuelve a crearlas.

---

## 11. Usuario inicial

El seeder crea un usuario administrador inicial.

```txt
Usuario: admin
```

Si no conoces la contraseña o necesitas cambiarla, ejecutar:

```bash
php artisan tinker
```

Luego:

```php
\App\Models\User::where('usuario', 'admin')->update([
    'password' => bcrypt('Admin123*')
]);
```

Salir:

```php
exit
```

Credenciales actualizadas:

```txt
Usuario: admin
Contraseña: Admin123*
```

Cambiar esta contraseña después del primer ingreso.

---

## 12. Crear enlace de almacenamiento

```bash
php artisan storage:link
```

---

## 13. Levantar el proyecto en desarrollo

En una consola:

```bash
php artisan serve --host=127.0.0.1 --port=8000
```

En otra consola, si se trabaja en desarrollo frontend:

```bash
npm run dev
```

Abrir:

```txt
http://127.0.0.1:8000
```

---

## 14. Configurar Apache en Windows

Laravel debe publicarse desde la carpeta `public`, no desde la raíz del proyecto.

Editar:

```txt
C:\xampp\apache\conf\extra\httpd-vhosts.conf
```

Agregar:

```apache
<VirtualHost *:80>
    ServerName catastro-canchis.local
    DocumentRoot "C:/xampp/htdocs/CatastroCanchis/public"

    <Directory "C:/xampp/htdocs/CatastroCanchis/public">
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog "logs/catastro-canchis-error.log"
    CustomLog "logs/catastro-canchis-access.log" common
</VirtualHost>
```

Verificar que Apache tenga habilitado `mod_rewrite` en:

```txt
C:\xampp\apache\conf\httpd.conf
```

Debe existir esta línea sin comentario:

```apache
LoadModule rewrite_module modules/mod_rewrite.so
```

También verificar:

```apache
Include conf/extra/httpd-vhosts.conf
```

Editar el archivo `hosts` de Windows como administrador:

```txt
C:\Windows\System32\drivers\etc\hosts
```

Agregar:

```txt
127.0.0.1 catastro-canchis.local
```

Reiniciar Apache.

Abrir:

```txt
http://catastro-canchis.local
```

---

## 15. Configuración de PostgreSQL en Windows

Si Laravel no conecta a PostgreSQL, revisar el archivo:

```txt
C:\Program Files\PostgreSQL\16\data\pg_hba.conf
```

La ruta puede cambiar según la versión instalada.

Debe permitir conexión local. Ejemplo:

```txt
host    all             all             127.0.0.1/32            scram-sha-256
host    all             all             ::1/128                 scram-sha-256
```

También revisar:

```txt
C:\Program Files\PostgreSQL\16\data\postgresql.conf
```

Verificar:

```txt
listen_addresses = 'localhost'
port = 5432
```

Después de modificar archivos de PostgreSQL, reiniciar el servicio:

```bash
net stop postgresql-x64-16
net start postgresql-x64-16
```

El nombre del servicio puede variar según la versión.

---

## 16. Comandos útiles

Limpiar caché general:

```bash
php artisan optimize:clear
```

Limpiar configuración:

```bash
php artisan config:clear
```

Limpiar rutas:

```bash
php artisan route:clear
```

Limpiar vistas:

```bash
php artisan view:clear
```

Regenerar autoload:

```bash
composer dump-autoload
```

Compilar assets:

```bash
npm run build
```

Optimizar para producción:

```bash
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

---

## 17. Configuración para producción

En producción cambiar `.env`:

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://tu-dominio.gob.pe
```

También ejecutar:

```bash
npm run build
php artisan optimize:clear
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

Recomendaciones:

1. Usar HTTPS.
2. No exponer la raíz del proyecto.
3. El servidor web debe apuntar a la carpeta `public`.
4. Cambiar la contraseña del usuario administrador.
5. Crear backups automáticos de PostgreSQL.
6. No subir `.env`.
7. No subir `vendor`.
8. No subir `node_modules`.
9. Restringir permisos de escritura solo a `storage` y `bootstrap/cache`.
10. Verificar que `APP_DEBUG=false` en producción.

---

## 18. Backup de PostgreSQL

Crear backup:

```bash
pg_dump -U catastro_user -h 127.0.0.1 -p 5432 -F c -b -v -f C:\backups\catastro_canchis.backup catastro_canchis
```

Restaurar backup:

```bash
pg_restore -U catastro_user -h 127.0.0.1 -p 5432 -d catastro_canchis -v C:\backups\catastro_canchis.backup
```

Backup SQL plano:

```bash
pg_dump -U catastro_user -h 127.0.0.1 -p 5432 -f C:\backups\catastro_canchis.sql catastro_canchis
```

Restaurar SQL plano:

```bash
psql -U catastro_user -h 127.0.0.1 -p 5432 -d catastro_canchis -f C:\backups\catastro_canchis.sql
```

---

## 19. Errores comunes

### Error: `could not find driver`

Falta habilitar PostgreSQL en PHP.

Revisar `php.ini`:

```ini
extension=pdo_pgsql
extension=pgsql
```

Luego reiniciar Apache o la consola.

---

### Error: `SQLSTATE[08006] could not connect to server`

Verificar que PostgreSQL esté iniciado:

```bash
net start
```

Buscar servicio PostgreSQL o iniciarlo:

```bash
net start postgresql-x64-16
```

También verificar en `.env`:

```env
DB_HOST=127.0.0.1
DB_PORT=5432
```

---

### Error: contraseña inválida

Probar conexión directa:

```bash
psql -U catastro_user -h 127.0.0.1 -d catastro_canchis
```

Si falla, cambiar contraseña desde usuario `postgres`:

```sql
ALTER USER catastro_user WITH PASSWORD 'NuevaClaveSegura123*';
```

Actualizar `.env`:

```env
DB_PASSWORD=NuevaClaveSegura123*
```

Limpiar caché:

```bash
php artisan config:clear
```

---

### Error: `No application encryption key has been specified`

Ejecutar:

```bash
php artisan key:generate
php artisan config:clear
```

---

### Error: `Vite manifest not found`

Ejecutar:

```bash
npm install
npm run build
```

---

### Error 404 en rutas internas

Verificar:

1. Apache tiene `mod_rewrite` habilitado.
2. El VirtualHost apunta a la carpeta `public`.
3. Existe el archivo `public/.htaccess`.

---

### Error de permisos en `storage`

Verificar que estas carpetas tengan permisos de escritura:

```txt
storage
bootstrap/cache
```

Limpiar caché:

```bash
php artisan optimize:clear
```

---

## 20. Instalación rápida

```bash
cd C:\xampp\htdocs
git clone https://github.com/JhonatanCastilloSullca/CatastroCanchis.git
cd CatastroCanchis

composer install
npm install

copy .env.example .env
php artisan key:generate
php artisan migrate --seed
php artisan storage:link

npm run build
php artisan serve --host=127.0.0.1 --port=8000
```

Si no existe `.env.example`, crear manualmente el `.env` usando la plantilla PostgreSQL de este README.

Abrir:

```txt
http://127.0.0.1:8000
```

---

## 21. Resumen de configuración obligatoria

```env
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=catastro_canchis
DB_USERNAME=catastro_user
DB_PASSWORD=ClaveSegura123*
DB_SCHEMA=public
DB_SCHEMA2=public
```

---

## 22. Nota final

Este proyecto debe desplegarse con PostgreSQL.
No se requiere phpMyAdmin porque phpMyAdmin es una herramienta para MySQL/MariaDB, no para PostgreSQL.

Para administrar PostgreSQL en Windows se puede usar:

```txt
psql
pgAdmin
DBeaver
DataGrip
```

La opción recomendada para instalación técnica es `psql`.

