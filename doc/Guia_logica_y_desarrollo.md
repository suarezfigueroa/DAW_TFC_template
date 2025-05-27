## Guía elaborar punto sobre Lógica y Codificación del Proyecto

Esta sección detallaré todos los elementos imporantes de mi proyecto respecto a lógica de programación y codificación, indicando casos de uso, trabajo con base de datos, relación de componentes importantes del proyecto, estructura de carpetas, ...

### I. Principales Conceptos de Laravel y su Impacto en el Proyecto

Laravel 11, como framework PHP, proporciona una estructura y un conjunto de herramientas que aceleran y organizan el desarrollo web. En tu proyecto, se han aprovechado sus **conceptos clave** para construir una aplicación sólida y mantenible.

* **Controladores (`Controllers`):** Son el puente entre las peticiones del usuario y la lógica de negocio de tu aplicación. Agrupan la lógica relacionada con una característica específica, haciendo que el código sea más organizado y legible. En tu proyecto, cada funcionalidad importante (como la gestión de productos, usuarios, o procesos de compra) tiene su propio controlador, lo que facilita la comprensión y el mantenimiento. <br>

  Los controladores del proyecto son los siguientes:

   * `SesionContoller`: (encargado de ...)
   *  ...

* **Enrutamiento (`Routing`):** Define cómo las peticiones HTTP (URL) de los usuarios se dirigen a los controladores y a las acciones específicas dentro de ellos. Laravel permite un **enrutamiento basado en acciones**, lo que significa que cada ruta puede apuntar directamente a un método específico dentro de un controlador, optimizando la asignación de responsabilidades y la claridad del flujo de la aplicación. Esto ha permitido crear URLs amigables y una navegación clara en el proyecto.

    Es importante el uso correcto de los verbos http (GET, POST, PUT,...), por eso en las rutas utilizadas se ha usado el verbo más correcto desde un punto de vista funcional y de seguridad.

    Las rutas más importantes del proyecto son:
    
    - `/albums` (GET): Muestra la pantalla de todos los albums de la aplicación.
    - `resto de rutas`: ...

* **Middleware:** Son "capas" de código que se ejecutan antes o después de que una petición llegue a tu controlador. Se utilizan para filtrar peticiones HTTP, manejar la autenticación, la verificación de CORS, o la aplicación de reglas de seguridad. En tu proyecto, se han utilizado middlewares para asegurar que solo los usuarios autenticados o con roles específicos (como administradores) puedan acceder a ciertas rutas, garantizando la seguridad y la coherencia del sistema.




* **Configuración (`Configuration`):** Laravel maneja la configuración de la aplicación de manera centralizada. La **importancia de tener la configuración fuera del código fuente** (por ejemplo, en archivos `.env`) radica en la seguridad y la flexibilidad. Esto evita que información sensible (como credenciales de base de datos) quede expuesta en el repositorio de código y permite **diferentes configuraciones para diferentes entornos** (desarrollo, producción). Esto asegura que la aplicación se comporte de forma distinta y segura cuando se está desarrollando, probando o ya está en producción, sin necesidad de modificar el código principal.
* **Sesiones (`Sessions`):** Las sesiones permiten almacenar información del usuario a través de múltiples peticiones HTTP, lo que es esencial para mantener el estado de un usuario logado o un carrito de compra. En tu proyecto, las sesiones han sido fundamentales para funcionalidades como el Login/Logout y el manejo del Carrito de Compra, manteniendo la experiencia del usuario fluida y persistente.
* **Validación de datos:** 
* **Almacenamiento (`Storage`):** Laravel proporciona una API unificada para interactuar con diferentes sistemas de archivos, tanto locales como en la nube. Esto ha sido crucial para la **gestión de imágenes** y otros archivos, permitiendo un manejo consistente de los recursos multimedia del proyecto.
* **Envío de Emails (`Mail`):** Laravel facilita el envío de emails a través de un API limpia y sencilla. En tu proyecto, se ha utilizado esta funcionalidad para comunicaciones importantes con los usuarios. Además, para el desarrollo y las pruebas, herramientas como **Mailtrap** han sido de gran ayuda, permitiendo interceptar y visualizar los correos electrónicos sin enviarlos realmente, lo que agiliza el proceso de depuración.

### II. Estructura de carpetas

#### Entendiendo la Estructura de Carpetas de Laravel

Laravel sigue una estructura de directorios limpia y organizada por defecto, lo que facilita encontrar y entender dónde va cada tipo de código. Esta estructura está diseñada para ser intuitiva y escalable, permitiendo a los desarrolladores centrarse en la lógica de su aplicación en lugar de preocuparse por la organización de archivos.

A continuación, exploraremos las carpetas más importantes y su propósito dentro de un proyecto Laravel.

```text
.
├── app/
├── bootstrap/
├── config/
├── database/
├── public/
├── resources/
├── routes/
├── storage/
├── tests/
├── vendor/
├── .env
├── .gitattributes
├── .gitignore
├── artisan
├── composer.json
├── composer.lock
├── package.json
├── phpunit.xml
├── postcss.config.js
├── readme.md
├── tailwind.config.js
└── vite.config.js
``` 
Aquí se detallan las carpetas clave para el proyecto:

#### `app/`

Esta es la joya de la corona, el corazón de tu aplicación. Contiene el código fuente principal de tu proyecto. La mayoría de los archivos específicos de tu aplicación residirán aquí.

* **`app/Http/`**: Contiene los archivos relacionados con la interacción web (HTTP).
    * **`Controllers/`**: Aquí residen tus **controladores**. Son los que manejan las peticiones entrantes, procesan los datos y devuelven una respuesta. Son el intermediario entre la petición del usuario y la lógica de tu aplicación.
    * **`Middleware/`**: Contiene los **middlewares**, que son filtros HTTP que se ejecutan antes o después de que una petición llegue a tus controladores. Se usan para autenticación, verificación de roles, CORS, etc.
* **`app/Models/`**: Aquí se define tus **modelos de Eloquent**. Cada archivo en esta carpeta generalmente corresponde a una tabla en tu base de datos y actúa como la interfaz principal para interactuar con esa tabla. Aquí se definen también las relaciones entre tus tablas.
* **`app/Providers/`**: Contiene los **proveedores de servicios**. Estos son el lugar central para configurar e inicializar los servicios de tu aplicación (como la autenticación, las configuraciones de la base de datos, o la carga de componentes).
* **`app/Console/`**: Aquí puedes definir tus **comandos de Artisan** personalizados.
* **`app/Exceptions/`**: Contiene el `Handler.php`, que es donde se gestionan todas las **excepciones** no capturadas de tu aplicación. Puedes personalizar cómo se reportan y se renderizan los errores.
* **`app/Mail/`**: Si tu aplicación envía correos electrónicos, los Mailable classes (plantillas de correo electrónico) se encuentran aquí.

#### `config/`

Esta carpeta alberga todos los archivos de **configuración** de tu aplicación. Cada archivo corresponde a un aspecto específico de la configuración (base de datos, caché, servicios, etc.).

La configuración se almacena fuera del código fuente en los ficheros `.env` y `.env.production`.


#### `database/`

Contiene todos los archivos relacionados con la base de datos de tu aplicación.

* **`migrations/`**: Aquí se encuentran las **migraciones de la base de datos**. Las migraciones son como el control de versiones para tu base de datos, permitiéndote definir y modificar el esquema de la base de datos de forma programática.
* **`seeders/`**: Contiene los *seeders*, que son clases para poblar tu base de datos con datos de prueba o iniciales.
* **`factories/`**: Contiene las fábricas de modelos, utilizadas para generar datos de prueba de manera automatizada.

#### `public/`

Este es el directorio raíz de acceso público de tu aplicación web. Es el único directorio al que el servidor web (Apache, Nginx) debe tener acceso directo.

* Contiene tu archivo `index.php` (el único punto de entrada a tu aplicación) y los **assets públicos** como CSS compilado, JavaScript, imágenes, y otros archivos estáticos.
* Todos los enlaces a tus assets deben apuntar a esta carpeta.

#### `resources/`

Aquí se almacenan los **recursos no compilados** de tu aplicación.

* **`views/`**: Contiene tus **vistas de Blade**. Blade es el potente motor de plantillas de Laravel. Aquí se definen las interfaces de usuario (HTML) de tu aplicación.
* **`css/`**: Contiene tus archivos CSS fuente (por ejemplo, los archivos de Tailwind CSS antes de ser compilados).
* **`js/`**: Contiene tus archivos JavaScript fuente (por ejemplo, los archivos de Alpine.js antes de ser compilados).
* **`lang/`**: Si tu aplicación es multilingüe, aquí se almacenan los archivos de traducción.

#### `routes/`

Define todas las rutas de tu aplicación.

* **`web.php`**: Contiene las rutas para tu interfaz web (`web routes`), incluyendo las rutas para las vistas, formularios, etc. Es donde la mayoría de tus rutas de usuario final se definirán.
* **`api.php`**: Contiene las rutas para tu API (`API routes`), que suelen ser sin estado y autenticadas mediante tokens.
* **`console.php`**: Aquí puedes definir cierres de consola para comandos de Artisan.
* **`channels.php`**: Si utilizas Broadcasting de Laravel, aquí se definen los canales de evento.

#### `storage/`

Este directorio contiene los archivos generados por tu aplicación que no son accesibles públicamente (por defecto).

* **`app/`**: Aquí puedes almacenar archivos generados por tu aplicación, como uploads de usuarios que no deben ser directamente accesibles desde el navegador. La librería Spatie Media Library a menudo almacena sus archivos aquí.
* **`framework/`**: Contiene archivos de caché, sesiones y vistas compiladas de Laravel.
* **`logs/`**: Es donde se almacenan los **archivos de log** de tu aplicación. Laravel utiliza Monolog para el logging, y aquí encontrarás los registros de errores, advertencias e información de tu aplicación, a menudo con rotación diaria para evitar archivos excesivamente grandes.

#### `vendor/`

Este directorio es gestionado por Composer y contiene todas las **dependencias de Composer** de tu proyecto (librerías de terceros). Nunca debes modificar archivos directamente aquí o añadirlos a tu control de versiones.

### II. Control de acceso

Laravel ofrece un sistema de autenticación y autorización robusto y flexible que se integra directamente con el sistema de **rutas**. Esto permite controlar quién puede acceder a qué partes de tu aplicación de manera declarativa y sencilla.

El control de acceso en Laravel se basa principalmente en dos conceptos:

1.  **Autenticación:** Verificar la identidad de un usuario (¿quién eres?). Esto se hace generalmente mediante el login y el uso de sesiones.
2.  **Autorización:** Determinar si un usuario autenticado tiene permiso para realizar una acción o acceder a un recurso específico (¿qué puedes hacer?).

> (detallar especificamente el proceso de tú proyecto)

Vamos a desglosar qué significa cada una:

- `Route::get('/register', [RegisteredUserController::class, 'create'])->name('register')`;

    - Route::get('/register', ...): Define una ruta que responde a las peticiones GET a la URL /register. Esto significa que cuando un usuario navega a tuapp.com/register, esta ruta se activará.
    - `[RegisteredUserController::class, 'create']`: Indica que cuando esta ruta se active, Laravel debe llamar al método create de la clase RegisteredUserController. Este método es típicamente el encargado de mostrar el formulario de registro al usuario.
    - `->name('register')`: Asigna un nombre único a esta ruta. Esto es una buena práctica en Laravel porque te permite referenciar la ruta por su nombre (route('register')) en lugar de por su URL (/register). Si en el futuro cambias la URL, no tendrás que actualizar todas las referencias en tu código, solo la definición de la ruta.


- `(indicar el resto de rutas de acceso)`


### II. Trabajo con la Base de Datos

El acceso y manipulación de datos son pilares de cualquier aplicación web. En este proyecto, se ha adoptado una estrategia robusta y eficiente:

* **ORM (Object-Relational Mapper) a través de Eloquent:** En lugar de escribir consultas SQL directamente, se utiliza **Eloquent**, el ORM de Laravel. Eloquent permite interactuar con la base de datos utilizando **clases y objetos PHP**, haciendo que el código sea más intuitivo y fácil de mantener. Esto **hace transparente el uso de conceptos propios de bases de datos relacionales**, ya que te enfocas en los objetos de tu aplicación (ej. `Producto`, `Usuario`) y Eloquent se encarga de traducir esas operaciones a consultas SQL.
* **Modelos (`Models`):** Cada tabla de la base de datos se representa con un modelo Eloquent. Estos modelos no solo encapsulan los datos, sino también la lógica de negocio relacionada con esa tabla.

    Los modelos del proyecto son:

    - `User`: Tabla users. Almacena y gestiona los usuarios de la aplicación.
    - `otros modelos`: ...

* **Migraciones (`Migrations`):** Permiten gestionar el esquema de la base de datos de manera controlada y versionada, como si fuera código. Esto ha garantizado que la estructura de la base de datos del proyecto pueda ser replicada fácilmente en diferentes entornos de desarrollo y producción.

    Estas son todas las migraciones del proyecto:  (Utilizar comando `php artisan migrate:status`)

    ```text
    Migration name .................................................................................................................... Batch / Status  
    0001_01_01_000000_create_users_table ..................................................................................................... [1] Ran  
    0001_01_01_000001_create_cache_table ..................................................................................................... [1] Ran  
    0001_01_01_000002_create_jobs_table ...................................................................................................... [1] Ran
    2025_02_05_163542_create_artists_table ................................................................................................... [1] Ran
    2025_02_05_163621_create_albums_table .................................................................................................... [1] Ran
    2025_02_05_163709_create_tracks_table .................................................................................................... [1] Ran
    2025_02_05_163743_create_playlists_table ................................................................................................. [2] Ran
    2025_02_18_105936_create_personal_access_tokens_table .................................................................................... [2] Ran
    2025_02_22_193250_create_shows_table ..................................................................................................... [2] Ran
    2025_02_25_091931_create_user_show_table ................................................................................................. [2] Ran
    ```

    Las migraciones propias del proyecto son las siguientes:

    ```text
    Migration name .................................................................................................................... Batch / Status  
    2025_02_05_163542_create_artists_table ................................................................................................... [1] Ran
    2025_02_05_163621_create_albums_table .................................................................................................... [1] Ran
    2025_02_05_163709_create_tracks_table .................................................................................................... [1] Ran
    2025_02_05_163743_create_playlists_table ................................................................................................. [2] Ran
    2025_02_22_193250_create_shows_table ..................................................................................................... [2] Ran
    2025_02_25_091931_create_user_show_table ................................................................................................. [2] Ran
    ```

    El nombre de cada una es autoexplicativo, por lo que me centraré en mostrar una de ellas, y cómo Laravel permite trabajar con DDL a través de código PHP:

    (elegir una: indicar nombre)
    ```php
    return new class extends Migration {
        public function up(): void {
            
            // Crea la tabla artists y la tabla intermedia artists_user
            // que relaciona artistas con usuarios
            Schema::create('artists', function (Blueprint $table) {
            $table->string('id')->primary();
            $table->string('code', 50)->unique();
            $table->string('name', 100);
            $table->string('type', 50)->default('artist');
            $table->string('genre', 50);
            $table->string('imagedUrl')->default('');
            $table->date('releaseDate')->nullable();
            $table->integer('popularity')->default(0);
            $table->timestamps();
            });

            Schema::create('artist_user', function (Blueprint $table) {
            $table->foreignIdFor(Artist::class)->constrained()->cascadeOnDelete();
            $table->foreignIdFor(User::class)->constrained()->cascadeOnDelete();
            $table->timestamps();

            $table->primary([
                (new Artist)->getForeignKey(),
                (new User())->getForeignKey(),
            ]);
            });


        }

        /* * Borra las tablas artists y artists_user
        *
        * @return void
        */
        public function down(): void {
            Schema::dropIfExists('artists_user');
            Schema::dropIfExists('artists');
        }
    };
    ``` 

* **Relaciones (`Relationships`):** Eloquent facilita la definición y el manejo de las relaciones entre las tablas (uno a uno, uno a muchos, muchos a muchos), lo que ha permitido trabajar con datos relacionados de forma muy eficiente, por ejemplo, obtener los pedidos de un usuario o los productos de una categoría.

    Las relaciones permiten navegar desde un modelo a otro, y modelan todos los tipos de relaciones que se encuentran en un modelo relacional, uno a uno, uno a muchos, muchos a muchos, etc...

    Por ejemplo, el model usuario presenta la siguientes relaciones:

    ```php
    class User {
        ...

        // Obtiene los albums que este usuario ha agregado al catálogo (tablar intermediia albums_user)
        public function albums(): BelongsToMany {
            return $this->belongsToMany(Album::class);
        }

        // Obtiene los artistas que este usuario ha agregado al catálogo (tabla intermedia artists_user)
        public function artists(): BelongsToMany {
            return $this->belongsToMany(Artist::class);
        }
        
        public function shows(): BelongsToMany {
            return $this->belongsToMany(Show::class);
        }

        public function artistFollowed(): BelongsToMany {
            return $this->belongsToMany(Artist::class);
        }

        // Obtiene las listas de reproducción que este usuario ha creado
        public function playlists(): HasMany {
            return $this->hasMany(Playlist::class);
        }

        ...
    }
    ```
---

### III. Gestión de Contenido Multimedia: Imágenes

Las imágenes son un elemento crucial en cualquier proyecto web, especialmente en aquellos con un fuerte componente visual como tiendas online o catálogos. En este proyecto, se ha prestado especial atención a su desarrollo:

* **Almacenamiento Directo en "Storage":** Para imágenes que no requieren procesamiento avanzado o una gestión más compleja, se han almacenado directamente en el sistema de archivos configurado por Laravel (`storage`), aprovechando las facilidades del framework para su subida y acceso.
* **Librerías Externas (Spatie Media Library):** Para una gestión más profesional y versátil de archivos multimedia, se ha integrado la librería **Spatie Media Library**. Esta librería va más allá del simple almacenamiento, permitiendo:
    * Asociar múltiples archivos multimedia a cualquier modelo de Eloquent.
    * Generar diferentes colecciones de medios (ej. "imágenes de producto", "fotos de perfil").
    * Realizar transformaciones de imágenes al vuelo (redimensionado, recorte, etc.).
    * Gestionar miniaturas y conversiones de formato.
    
    La integración de Spatie Media Library ha simplificado enormemente el manejo de imágenes en el proyecto, ofreciendo una solución robusta para la gestión de activos.



    > (explicar aquí cómo habéis realizar el tema de las imágenes en el proyecto)
---

### IV. Gestión de Errores y Seguimiento

Una aplicación robusta necesita una buena gestión de errores y un sistema de seguimiento eficiente.

* **Excepciones (`Exceptions`):** La **gestión y emisión de excepciones es un concepto clave** para manejar situaciones inesperadas o errores controlados en la aplicación. En tu proyecto, se han utilizado excepciones para los **procesos más importantes** y críticos, como validaciones de datos, operaciones con la base de datos o interacciones con servicios externos. Esto asegura que, ante un fallo, la aplicación no solo lo notifique de manera clara, sino que también permita una recuperación o un manejo adecuado del error.
* **Logging (`Logging`):** El *logging* es un proceso **vital**, no solo durante el desarrollo sino también cuando el proyecto ya está desplegado en producción. Permite registrar eventos, errores y el comportamiento de la aplicación. En este proyecto, se ha utilizado la característica de *logging* de Laravel:
    * **Rotación Diaria:** Los logs se rotan diariamente, lo que evita que los archivos de log crezcan excesivamente y facilita su gestión.
    * **Niveles de Log:** Se han generado logs de diferentes niveles para categorizar la información:
        * **`ERROR`:** Para errores críticos que necesitan atención inmediata.
        * **`WARNING`:** Para situaciones que podrían indicar un problema futuro o un comportamiento inesperado.
        * **`INFO`:** Para eventos normales del sistema que son útiles para el seguimiento (ej. "usuario logado", "pedido creado").
        * **`DEBUG`:** Para información detallada útil durante el desarrollo y la depuración.
    
    Esto proporciona una trazabilidad completa de lo que ocurre en la aplicación, siendo una herramienta invaluable para la depuración y el monitoreo en producción.

    > (Indicar en qué partes de la aplicación utilizáis el loggin y las excepciones (si es el caso)).

---

### V. Casos de Uso del Proyecto

Aquí se describen las funcionalidades clave del proyecto, destacando cómo las tecnologías de Laravel han sido aplicadas para su implementación.

* **Login/Logout:** La autenticación de usuarios es un pilar. Se ha utilizado **Laravel Breeze** para la base de autenticación, y las **sesiones/cookies de PHP** gestionadas por Laravel han sido esenciales para mantener el estado del usuario (`logado` o `no logado`) a través de las diferentes peticiones, garantizando un acceso seguro y una experiencia de usuario fluida.

    > (explicar aquí como se ha desarrollado).

    En SessionController (o en vuestro caso...)

    ```php
    /**
    * Controlador para manejar las sesiones de usuario (login/logout).
    */
    class SessionController extends Controller
    {
        /**
        * Muestra el formulario de inicio de sesión.
        */
        public function create()
        {
            return view('auth.login');
        }


    /**
    * Guarda nueva sesión (login).
    */
    public function store()
        {
            // Valida los datos de entrada del formulario de inicio de sesión
            $attributes = request()->validate([
                'email' => ['required', 'email'],
                'password' => ['required']
            ]);

            // Intenta autenticar al usuario con las credenciales proporcionadas
            if (!Auth::attempt($attributes)) {
                throw ValidationException::withMessages([
                    'email' => 'Sorry, those credentials do not match.'
                ]);
            }
            
            // Regenera la sesión
            request()->session()->regenerate();

            // Si la autenticación es exitosa, redirige al usuario a la página de inicio
            return redirect()->route('dashboard.index');
        }

        public function destroy()
        {
            Auth::logout();

            return redirect(route('dashboard.index'));
        }
    }
    ```

* **Carrito de Compra:** Para gestionar el carrito de compra de los usuarios, **se han utilizado las sesiones para almacenar los productos** que el usuario desea adquirir. Esto permite que el contenido del carrito persista mientras el usuario navega por la tienda, ofreciendo una experiencia de compra continua.

    > (explicar proceso y clases/recursos utilizados)

* **Finalizar Compra:** Por motivos de tiempo y alcance del proyecto, **se ha obviado el proceso completo de pago y se ha enfocado en la generación del pedido**. Al finalizar la compra, se crea un registro del pedido en la base de datos, asociándolo al usuario y a los productos seleccionados en el carrito.

    > (explicar proceso)

* **(Resto de Casos de Uso):** Aquí deberás listar y describir brevemente cada una de las demás funcionalidades principales de tu proyecto (ej. "Gestión de Productos", "Visualización de Detalles de Apartamento", "Búsqueda de Elementos", etc.), explicando cómo se han implementado usando los conceptos de Laravel.

---

### VI. Panel Administrativo

La existencia de un **panel administrativo es fundamental en un sistema completo**, ya que proporciona una interfaz segura y centralizada para que los usuarios autorizados (administradores) gestionen los datos y la configuración de la aplicación sin necesidad de acceder directamente a la base de datos o al código. Es el cerebro operativo del proyecto.

* **Generado con FilamentPHP:** El panel administrativo de este proyecto ha sido construido utilizando **FilamentPHP**. Esta librería ha permitido crear rápidamente una interfaz de administración robusta y personalizable con poco código, integrándose a la perfección con Laravel y sus modelos Eloquent.
* **Acceso Restringido:** Se accede al panel a través de una opción específica para el usuario logado, y solo si este cuenta con el rol de **administrador**. Esto se ha logrado mediante middlewares y la lógica de autenticación de Laravel, asegurando que el acceso sea exclusivo para personal autorizado.
* **Conceptos en el Panel Administrativo:** Dentro del panel de FilamentPHP, se han implementado los siguientes conceptos:
    * **Recursos (`Resources`):** Para cada modelo principal de tu aplicación (por ejemplo, `Producto`, `Apartamento`, `Usuario`, `Pedido`), Filament crea un "recurso". Cada recurso proporciona una interfaz completa para:
        * **Visualizar (`View`):** Mostrar los detalles de un registro individual.
        * **Listar (`List`):** Presentar una tabla de todos los registros, con opciones de paginación, búsqueda y filtrado.
        * **Crear (`Create`):** Formularios para añadir nuevos registros.
        * **Editar (`Edit`):** Formularios para modificar registros existentes.
        * **Eliminar (`Delete`):** Opciones para borrar registros.
    * **Campos de Formulario (`Form Fields`):** Filament provee una amplia gama de campos de formulario (texto, números, selectores, cargas de archivos, etc.) que se han utilizado para construir formularios de creación y edición intuitivos y validados.
    * **Tablas (`Tables`):** Las tablas de Filament permiten visualizar y gestionar colecciones de datos, con funcionalidades como búsqueda global, filtros avanzados, acciones por fila y ordenación.
    * **Relaciones (`Relations`):** El panel también permite gestionar las relaciones entre los recursos, por ejemplo, ver los productos asociados a una categoría o los pedidos de un usuario específico directamente desde sus respectivos paneles.
    * **(Otros elementos si los usaste):** Si utilizaste otras características de Filament como `Widgets`, `Pages`, `Custom Fields`, `Notifications`, etc., menciónalas aquí y explica brevemente su función.
    <br>

    > EXPLICA EN QUÉ PARTE DEL PROYECTO (Carpetas) ESTÁ FILAMENT, Y CÓMO SE INTEGRA CON EL RESTO DE LA APLICACIÓN<br>
    > INDICA EL PUNTO DE INICIO QUE MUESTRA EL PANEL<br>
    > INDICA TAMBIÉN ALGÚN RECURSO UTILIZADO (Ej: Producto) y qué clases lo conforman...<br>
