## Guía para el Despliegue de Aplicaciones Laravel con Docker Compose

El despliegue de una aplicación es el paso final que la hace accesible a los usuarios. Utilizar **Docker** y **Docker Compose** simplifica enormemente este proceso, garantizando que tu aplicación y sus dependencias (como la base de datos) se ejecuten de manera consistente en cualquier entorno, ya sea en tu máquina local o en un servidor remoto.

Esta documentación mostrará dos escenarios de despliegue comunes utilizando tu configuración de `docker-compose.yml` y `dockerfile`.

### Escenario 1: Despliegue Local con Docker

Este proceso te permitirá levantar tu aplicación en tu propia máquina utilizando Docker. Lo haremos en modo interactivo para que puedas ver los logs y, crucialmente, ejecutar comandos dentro del contenedor de la aplicación para realizar las migraciones de la base de datos.

**Prerrequisitos:**

* **Docker Desktop (o Docker Engine y Docker Compose CLI)** instalado en local.
* El proyecto incluyen en la raíz los archivos `docker-compose.yml` y `dockerfile` proporcionados, y el archivo `.env` configurado.

**Pasos para el Despliegue Local:**

1.  **Clona el repositorio**
    Una vez clonado el repositorio, acceder a la carpeta

    ```bash
    cd /ruta/a/proyecto
    ```

2.  **Archivo configuración `.env`:**
    Este archivo almacena la configuración de la base de datos, servidor, nombre y datos de acceso.
    Como Laravel también usa este tipo de ficheros de configuración, podemos **usarlos** para no tener que repetir la configuración dentro del fichero `docker-compose`.
    
    Las claves utilizadas son las siguientes: `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, y `DB_PASSWORD`.

3.  **Docker Compose:**
    
    Este es el fichero fundamental, que contiene toda la información de la creación del escenario.  
    
    (aquí poner el contenido de vuestro docker-compose)
    ```docker
    version: '3.8'
    name: {nombre-del-escenario}
    services:

        # Application
        app:
            container_name: musify-app
            build:
                context: .
                dockerfile: dockerfile
            volumes:
                - .env:/app/.env
            ports:
                -   "9000:8000"
            depends_on:
                - "database"

        ...
    ```
    El dockerfile es la receta para construir la imagen que contendrá la aplicación. Los pasos se ejecutan en orden, y el último paso es donde se ejecuta el proceso que estará funcionando, en este proyecto, el servidor "octane".

    ```docker
    FROM elrincondeisma/php-for-laravel:8.3.7
    WORKDIR /app
    
    # Copiar la app Laravel
    COPY . .
    
    # Instalar Composer dependencias (sin desarrollo)
    RUN composer install --no-dev --optimize-autoloader
    RUN composer require laravel/octane
    
    # Crear carpeta de logs
    RUN mkdir -p /app/storage/logs
    
    # Instalar Octane con Swoole
    RUN php artisan octane:install --server="swoole"
    
    # Exponer el puerto de Octane
    EXPOSE 8000
    
    # Comando de arranque
    ENTRYPOINT ["php", "artisan", "octane:start", "--server=swoole", "--host=0.0.0.0"]
    ```

    **Servicios Definidos**

    #### **1. `app` (Aplicación)**

    Este servicio es el corazón de tu aplicación, donde se ejecuta el código Laravel.

    * **`container_name: musify-app`**: Asigna un nombre específico al contenedor para una fácil referencia.
    * **`build:`**: Docker construirá la imagen para este servicio a partir de un `Dockerfile`.
        * **`context: .`**: Indica que el contexto de construcción es el directorio actual donde se encuentra `docker-compose.yml`.
        * **`dockerfile: dockerfile`**: Especifica el nombre del archivo Dockerfile a utilizar para construir la imagen.
    * **`volumes:`**: Mapea un archivo del host al contenedor para permitir acceso al `.env` dinámicamente.
        * **`- .env:/app/.env`**: Sincroniza el archivo `.env` de tu máquina local con el archivo `.env` dentro del contenedor.
    * **`ports:`**: Publica los puertos del contenedor al host para acceder a la aplicación.
        * **`- "9000:8000"`**: Mapea el puerto `8000` del contenedor (donde Octane escucha) al puerto `9000` de tu máquina local.
    * **`depends_on:`**: Asegura que el servicio `database` se inicie antes que la aplicación.
        * **`- "database"`**: Establece una dependencia: el contenedor `app` solo se iniciará una vez que el contenedor `database` esté en marcha.

    #### **2. `database` (Base de Datos MySQL)**

    Este servicio es el contenedor que aloja tu base de datos MySQL para la aplicación.

    * **`image: mysql:8.0`**: Utiliza la imagen oficial de MySQL versión 8.0 desde Docker Hub.
    * **`container_name: musify-db`**: Asigna un nombre específico al contenedor de la base de datos.
    * **`volumes:`**: Persiste los datos de la base de datos y ejecuta scripts de inicialización.
        * **`- dbdata:/var/lib/mysql`**: Monta un volumen de Docker llamado `dbdata` para asegurar que los datos de la base de datos persistan incluso si el contenedor se detiene o se elimina.
        * **`- ./_res/database/init:/docker-entrypoint-initdb.d`**: Monta un directorio local con scripts SQL que se ejecutarán automáticamente al iniciar el contenedor de la base de datos por primera vez.
    * **`environment:`**: Define variables de entorno dentro del contenedor de la base de datos.
        * **`MYSQL_DATABASE: ${DB_DATABASE}`**: Establece el nombre de la base de datos, obteniéndolo del `.env` del host.
        * **`MYSQL_ROOT_PASSWORD: p@ssw0rd`**: Define la contraseña para el usuario `root` de MySQL (¡solo para desarrollo!).
        * **`MYSQL_PASSWORD: ${DB_PASSWORD}`**: Define la contraseña para el usuario de la base de datos especificado por `MYSQL_USER`, obteniéndola del `.env` del host.
        * **`MYSQL_USER: ${DB_USERNAME}`**: Define el nombre de usuario de la base de datos, obteniéndolo del `.env` del host.
    * **`ports:`**: Publica el puerto de la base de datos del contenedor al host.
        * **`- "33061:3306"`**: Mapea el puerto `3306` (por defecto de MySQL) del contenedor al puerto `33061` de tu máquina local.
    * **`env_file:`**: Carga variables de entorno para el servicio desde un archivo.
        * **`- .env`**: Carga las variables definidas en tu archivo `.env` del host para el servicio de base de datos.

    #### **3. `pma` (phpMyAdmin)**

    Este servicio proporciona una interfaz web para gestionar tu base de datos MySQL.

    * **`image: phpmyadmin:5.1`**: Utiliza la imagen oficial de phpMyAdmin versión 5.1.
    * **`container_name: musify-pma`**: Asigna un nombre específico al contenedor de phpMyAdmin.
    * **`environment:`**: Configura variables de entorno para phpMyAdmin.
        * **`- PMA_ARBITRARY=1`**: Permite conectar a cualquier host de MySQL, no solo al predefinido.
        * **`- PMA_HOST=${DB_HOST}`**: Especifica el host de la base de datos a la que phpMyAdmin intentará conectar, obteniéndolo del `.env` del host.
        * **`- PMA_USER=${DB_USERNAME}`**: Define el nombre de usuario para la conexión de phpMyAdmin, desde el `.env` del host.
        * **`- PMA_PASSWORD=${DB_PASSWORD}`**: Define la contraseña para la conexión de phpMyAdmin, desde el `.env` del host.
        * **`- PMA_PORT=${DB_PORT}`**: Especifica el puerto de la base de datos, desde el `.env` del host.
    * **`depends_on:`**: Asegura que la base de datos esté lista antes de iniciar phpMyAdmin.
        * **`- database`**: El contenedor `pma` solo se iniciará después de que el contenedor `database` esté en marcha.
    * **`ports:`**: Publica el puerto de phpMyAdmin del contenedor al host.
        * **`- "8888:80"`**: Mapea el puerto `80` (por defecto de phpMyAdmin) del contenedor al puerto `8888` de tu máquina local.
    * **`env_file:`**: Carga variables de entorno para el servicio desde un archivo.
        * **`- .env`**: Carga las variables definidas en tu archivo `.env` del host para el servicio de phpMyAdmin.

5.  **Levantar el escenario**

    La primera vez, como la imagen no está construida, se construirá. Este proceso es automático, pero si necesitaramos construir la imagen previamente:

    ```bash
    docker compose up --build
    ```
    
    * **`docker compose up`**: Inicia los contenedores.
    * **`--build`**: Fuerza la reconstrucción de las imágenes, lo que es útil si has hecho cambios en tu `Dockerfile` o en tu código base.

4.  **Ejecutar las migraciones de la base de datos:**
    Si la BD está vacia, es necesario ejecutar el script de construcción.

    Ejecuta el comando de migración (ejecutar todas las migraciones no ejecutadas) dentro del contenedor "app".
    
    ```bash
    docker compose exec app php artisan migrate
    ```
    **Importante:** Si tus migraciones necesitan datos iniciales (seeders), puedes ejecutarlos después de las migraciones:

    ```bash
    docker compose exec app php artisan db:seed
    ```

5.  **Acceder a al aplicacición:**

    Para acceder a la aplicación, simplemente desde el navegador `http://localhost:9000`.


6.  **Verificar el estado de la aplicación:**

    Para verificar si los contenedores están funcionando bien, lo mejor es revisar los logs, y el estado de ejecución de los contenedores.

    Ver el estado de los contenedores:

    ```bash
    docker ps
    ```
    Y ver los logs de un servicio específico:

    ```bash
    docker compose logs app -f # -f para seguir los logs en tiempo real
    ```

6.  **Detener la aplicación:**

    Si queremos finalizar la ejecución del escenario (todos los contenedores), ejecutar comando:
    
    > Si ademas se necesita borrar todo rastro, incluir el comando `-v`.

    ```bash
    docker compose down
    ```

---

### Escenario 2: Despliegue Remoto en un Servidor Linux (Digital Ocean)

El despliegue en un servidor remoto implica mover tu código y tus configuraciones de Docker a la nube y levantar los servicios allí. Digital Ocean es una excelente opción por su simplicidad.

**Prerrequisitos:**

* Una cuenta en **Digital Ocean** (o cualquier proveedor de VPS con Linux, como Ubuntu).
* Un **Droplet (servidor virtual)** con **Ubuntu (recomendado)**.
* **Acceso SSH** a tu Droplet.
* **Docker y Docker Compose** instalados en tu Droplet. (En Ubuntu, puedes instalarlos siguiendo la documentación oficial de Docker. Es recomendable añadir tu usuario al grupo `docker` para evitar usar `sudo` constantemente, con `sudo usermod -aG docker $USER && newgrp docker`).

**Pasos para el Despliegue Remoto:**

1.  **Conectar al servidor remoto vía SSH:**
2.  **Clonar repositorio github en servidor**

    > Para este paso será necesario, generar un nuevo par de claves SSH, y subir la clave pública generada a github.
    
    ```bash
    git clone ...
    ```

3.  **Generar imagen y levantar contenedores**

    A partir de este paso, el despliegue en un servidor remoto o en local son iguales, ya que lo que resta sería, ejecutar el comando `docker compose up --build`, y si todo funciona bien, ya estará nueva aplicación lista para su uso.

6.  **Acceder a la aplicación desde el exterior**
    
    Para acceder desde el exterior, será necesario conocer la IP externa asociada al servidor. Esta ip (depediendo del proveedor y de las opciones configuradas) puede cambiar cada vez que "encedamos" el servidor, o puede ser fija. Lo idea es que sea fija, pero eso podría tener un coste adicional.
    También es posible asignar un dominio a esta IP, un dominio que previamente hayamos adquirido.

    Ruta acceso aplicación: `http://{ip-servidor}`


8.  **Configurar el firewall (UFW en Ubuntu):**
    En principio hay que asegurar que no haya un firewall activado, y si fuese el caso, o nos gustaría tener un firewall activo para mayor seguridad, sería necesario abrir los puerots. necesarios (80 para HTTP, 443 para HTTPS).

    ```bash
    sudo ufw allow 'Nginx HTTP' # O 'Nginx HTTPS' si configuras SSL
    sudo ufw enable # Si no está habilitado
    sudo ufw status # Para verificar
    ```
