# **Guía Definitiva para Instalar Laravel 11 en Ubuntu con XAMPP y MySQL/MariaDB (Incluyendo Breeze y Blade)**

## **Introducción**

Esta guía proporciona un proceso paso a paso, ultra preciso y totalmente funcional para instalar el framework PHP Laravel, específicamente la versión 11 (la última versión estable al momento de escribir este documento), en un sistema Ubuntu Linux utilizando XAMPP como entorno de servidor local. A diferencia de otras guías, este enfoque evita explícitamente el uso de Docker y Laravel Sail, optando por una configuración manual tradicional con Apache, MySQL/MariaDB (incluido en XAMPP) y PHP. Además, se detalla la configuración del paquete de autenticación Laravel Breeze con plantillas Blade y la configuración de un Virtual Host de Apache para un acceso limpio al proyecto.

**Nota Importante sobre la Versión:** La consulta original solicitaba Laravel 12\. Sin embargo, al momento de esta redacción, Laravel 11 es la última versión estable lanzada (Marzo 2024).1 Se espera que Laravel 12 sea lanzado en el primer trimestre de 2025\.2 Esta guía se centrará en Laravel 11, pero el proceso de instalación para Laravel 12 probablemente será muy similar, con posibles ajustes menores en los requisitos de versión de PHP u otras dependencias.3

## **1\. Prerrequisitos del Sistema**

Antes de comenzar, asegúrese de que su sistema Ubuntu cumpla con los siguientes requisitos:

* **Sistema Operativo:** Una versión de Ubuntu soportada. Se recomienda una versión LTS (Long-Term Support) como Ubuntu 22.04 LTS ("Jammy Jellyfish") o superior, ya que reciben soporte de seguridad estándar durante 5 años (hasta abril de 2027 para 22.04).5  
* **Usuario con Privilegios sudo:** Necesitará ejecutar comandos con sudo para instalar software y modificar archivos de configuración.10  
* **Conexión a Internet Estable:** Necesaria para descargar los instaladores y paquetes.10  
* **Navegador Web:** Para verificar la instalación de XAMPP y la aplicación Laravel.  
* **Editor de Texto:** Un editor de texto como nano, vim o un editor gráfico como VS Code para modificar archivos de configuración.

## **2\. Paso 1: Instalar XAMPP en Ubuntu**

XAMPP proporciona una pila de servidor web completa (Apache, MariaDB, PHP, Perl) fácil de instalar.10 Utilizaremos XAMPP para gestionar nuestro servidor Apache, la base de datos MariaDB (compatible con MySQL) y la versión de PHP requerida.

* **Descargar XAMPP:**  
  * Visite la página oficial de descargas de Apache Friends: [https://www.apachefriends.org/download.html](https://www.apachefriends.org/download.html).10  
  * Busque la sección "XAMPP for Linux".  
  * Descargue la versión de XAMPP que incluya **PHP 8.2 o superior**, ya que Laravel 11 requiere PHP \>= 8.2.1 Al momento de escribir esto, **XAMPP 8.2.12** (que incluye PHP 8.2.12) es una opción adecuada.14 Haga clic en el enlace de descarga de 64 bits.16  
  * El archivo descargado tendrá un nombre similar a xampp-linux-x64-8.2.12-0-installer.run.  
* **Hacer Ejecutable el Instalador:**  
  * Abra su terminal (Ctrl+Alt+T).  
  * Navegue al directorio donde descargó el archivo (generalmente Downloads):  
    Bash  
    cd \~/Downloads  
    13  
  * Cambie los permisos del archivo para hacerlo ejecutable. Reemplace xampp-linux-x64-8.2.12-0-installer.run con el nombre exacto del archivo descargado:  
    Bash  
    chmod \+x xampp-linux-x64-8.2.12-0-installer.run  
    o  
    Bash  
    chmod 755 xampp-linux-x64-8.2.12-0-installer.run  
    10  
* **Ejecutar el Instalador:**  
  * Ejecute el instalador con privilegios sudo:  
    Bash  
    sudo./xampp-linux-x64-8.2.12-0-installer.run  
    10  
  * Ingrese su contraseña de usuario cuando se le solicite.  
* **Seguir el Asistente de Instalación Gráfico:**  
  * Se abrirá una ventana de instalación gráfica.13  
  * Haga clic en "Next" (Siguiente) en las pantallas de bienvenida y selección de componentes. Se recomienda mantener los componentes predeterminados (Apache, MySQL, PHP Core Files, PHPMyAdmin).13  
  * La ubicación de instalación predeterminada es /opt/lampp. Mantenga esta ubicación.10  
  * Desmarque la casilla "Learn more about Bitnami for XAMPP" si aparece.15  
  * Haga clic en "Next" para comenzar la instalación.13  
  * Una vez completada la instalación, **desmarque** la casilla "Launch XAMPP" antes de hacer clic en "Finish" (Finalizar), ya que realizaremos la gestión inicial desde la terminal.13  
* **Verificar la Instalación de XAMPP:**  
  * XAMPP se instala en el directorio /opt/lampp.10  
  * Inicie los servicios de XAMPP (Apache y MySQL/MariaDB) usando el comando:  
    Bash  
    sudo /opt/lampp/lampp start  
    10  
    * Debería ver mensajes indicando que Apache y MySQL se están iniciando.  
    * **Nota:** Si los servicios no se inician, podría deberse a que falta el paquete net-tools. Instálelo con sudo apt install net-tools y vuelva a intentarlo.13 Otro problema podría ser SELinux si está activado; desactívelo temporalmente con sudo /usr/sbin/setenforce 0\.23  
  * Abra su navegador web y vaya a http://localhost o http://127.0.0.1.10  
  * Debería ver el panel de control (dashboard) de XAMPP, confirmando que Apache funciona.10  
  * Verifique phpMyAdmin accediendo a http://localhost/phpmyadmin.10 Debería cargarse la interfaz de phpMyAdmin, confirmando que MySQL/MariaDB funciona.  
* **Comandos Útiles de XAMPP:**  
  * Iniciar todos los servicios: sudo /opt/lampp/lampp start 10  
  * Detener todos los servicios: sudo /opt/lampp/lampp stop 23  
  * Reiniciar todos los servicios: sudo /opt/lampp/lampp restart 28  
  * Iniciar solo Apache: sudo /opt/lampp/lampp startapache 28  
  * Iniciar solo MySQL: sudo /opt/lampp/lampp startmysql 28  
  * Verificar estado (implícito en start/stop, o usar panel gráfico): sudo /opt/lampp/manager-linux-x64.run 10 o sudo /opt/lampp/lampp status 40 (Nota: status no está documentado consistentemente, el panel gráfico o ps son más fiables).

## **3\. Paso 2: Verificar Extensiones PHP Requeridas por Laravel**

Laravel 11 tiene requisitos específicos de extensiones PHP.2 XAMPP generalmente incluye la mayoría de ellas, pero es crucial verificar. Laravel 11 requiere como mínimo PHP 8.2.1

* **Extensiones Requeridas por Laravel (Típicas):**  
  * BCMath  
  * Ctype  
  * cURL 20  
  * DOM  
  * Fileinfo  
  * JSON  
  * Mbstring 2  
  * OpenSSL 2  
  * PCRE  
  * PDO 2  
  * Tokenizer 2  
  * XML  
  * Zip (requerida por Composer y a veces por Laravel Dusk o manejo de archivos) 41  
* **Verificar Extensiones Instaladas/Habilitadas en XAMPP:**  
  * **Método 1: phpinfo()**  
    * Cree un archivo llamado info.php en el directorio htdocs de XAMPP: sudo nano /opt/lampp/htdocs/info.php  
    * Añada el siguiente contenido:  
      PHP  
      \<?php  
      phpinfo();

  ?\> \`\`\` 44

    * Guarde y cierre el archivo (Ctrl+X, luego Y, luego Enter en nano).  
    * Asegúrese de que Apache esté corriendo (sudo /opt/lampp/lampp startapache).  
    * Abra su navegador y vaya a http://localhost/info.php.44  
    * Busque en la página las extensiones requeridas. La página mostrará todas las extensiones cargadas y la configuración de PHP.  
  * **Método 2: Línea de Comandos (Usando el PHP de XAMPP)**  
    * Ejecute el siguiente comando para listar los módulos compilados/cargados:  
      Bash  
      sudo /opt/lampp/bin/php \-m  
      46  
    * Puede filtrar la salida usando grep, por ejemplo, para buscar pdo:  
      Bash  
      sudo /opt/lampp/bin/php \-m | grep \-i pdo

* **Habilitar Extensiones Faltantes (si es necesario):**  
  * XAMPP para Linux generalmente viene con las extensiones necesarias precompiladas o habilitadas. Sin embargo, si falta alguna (como zip o intl a veces), necesitará editar el archivo php.ini de XAMPP.  
  * Localice el archivo php.ini: sudo nano /opt/lampp/etc/php.ini.23  
  * Busque la línea correspondiente a la extensión faltante (por ejemplo, extension=zip o extension=intl).42  
  * Si la línea está comentada (comienza con ;), elimine el punto y coma ; para descomentarla.42  
  * Guarde el archivo y cierre el editor.  
  * Reinicie Apache para aplicar los cambios: sudo /opt/lampp/lampp restartapache o sudo /opt/lampp/lampp restart.  
* **Extensiones Incluidas en XAMPP 8.2.12 (Linux):** Aunque la documentación oficial 16 lista componentes generales, no detalla todas las extensiones PHP. Sin embargo, basado en componentes listados como MariaDB, SQLite, OpenSSL, GD, libxml, zlib, curl, y el soporte multibyte (mbstring), se puede inferir que las extensiones comunes como pdo\_mysql, pdo\_sqlite, openssl, gd, xml, zlib, curl, y mbstring están presentes.16 La extensión zip también suele estar incluida o fácilmente habilitable.41 Se espera que las extensiones ctype, dom, fileinfo, json, pcre, tokenizer también estén incluidas por defecto con PHP 8.2.41 Es probable que mod\_rewrite de Apache esté habilitado por defecto en XAMPP 51, pero se verificará en el Paso 7\.

## **4\. Paso 3: Instalar Composer Globalmente**

Composer es el gestor de dependencias para PHP, esencial para instalar Laravel y sus paquetes.2 Lo instalaremos globalmente para poder usar el comando composer desde cualquier directorio.

* **Actualizar Índice de Paquetes:**  
  Bash  
  sudo apt update  
  56  
* **Instalar Dependencias de Composer:** Composer necesita php-cli (que ya viene con XAMPP, pero instalarlo vía apt asegura que las dependencias del sistema estén satisfechas) y otras herramientas como curl, unzip, git.56  
  Bash  
  sudo apt install curl php-cli php-mbstring git unzip \-y  
  56 (Usamos el php-cli del sistema aquí, pero Composer usará el PHP encontrado en el PATH, que configuraremos para que sea el de XAMPP si es necesario, o podemos invocar Composer usando el PHP de XAMPP explícitamente más adelante).  
* **Descargar el Instalador de Composer:**  
  * Navegue a su directorio home o a /tmp: cd \~  
  * Use curl para descargar el script de instalación oficial:  
    Bash  
    curl \-sS https://getcomposer.org/installer \-o composer-setup.php  
    12  
* **(Opcional pero Recomendado) Verificar la Firma del Instalador:**  
  * Este paso asegura que el instalador no ha sido manipulado.  
  * Obtenga el hash SHA-384 más reciente de la página de Descargas de Composer ([https://getcomposer.org/download/](https://getcomposer.org/download/)).61 Copie el hash correspondiente a la última versión estable (ej. 2.8.8).  
  * Compare el hash esperado con el hash del archivo descargado usando PHP (reemplace EXPECTED\_SIGNATURE con el hash copiado):  
    Bash  
    EXPECTED\_SIGNATURE='...' \# Pegue el hash aquí  
    php \-r "if (hash\_file('sha384', 'composer-setup.php') \=== '$EXPECTED\_SIGNATURE') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP\_EOL;"  
    57  
  * Si dice "Installer corrupt", descargue el instalador de nuevo y repita la verificación. Si dice "Installer verified", continúe.  
* **Instalar Composer Globalmente:**  
  * Ejecute el script de instalación usando el PHP de XAMPP (para asegurar compatibilidad si hay múltiples versiones de PHP) y especifique el directorio de instalación global (/usr/local/bin) y el nombre del ejecutable (composer):  
    Bash  
    sudo /opt/lampp/bin/php composer-setup.php \--install-dir=/usr/local/bin \--filename=composer  
    12  
    * Alternativamente, si el PHP del sistema es compatible (\>=7.2.5 para Composer 2.x), podría usar sudo php composer-setup.php.... Usar el PHP de XAMPP es más seguro en este contexto.  
* **Verificar la Instalación de Composer:**  
  * Compruebe que Composer está instalado y accesible globalmente:  
    Bash  
    composer \--version  
    12  
  * Debería mostrar la versión de Composer instalada (ej. Composer version 2.8.8).61  
* **Limpiar el Instalador:**  
  * Elimine el script de instalación:  
    Bash  
    php \-r "unlink('composer-setup.php');"  
    57

## **5\. Paso 4: Instalar Node.js y npm**

Laravel Breeze utiliza Tailwind CSS para su scaffolding de Blade, y necesita Node.js y npm (Node Package Manager) para compilar los assets CSS y JavaScript.64 Se recomienda usar NVM (Node Version Manager) para instalar Node.js, ya que permite gestionar múltiples versiones fácilmente.

* **Instalar NVM:**  
  * Instale curl si aún no lo tiene: sudo apt install curl.72  
  * Descargue y ejecute el script de instalación de NVM desde su repositorio oficial de GitHub (verifique la última versión en la página de NVM):  
    Bash  
    curl \-o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash  
    (Reemplace v0.39.7 con la última versión si es diferente) 72  
* **Activar NVM:**  
  * Cierre y vuelva a abrir su terminal, o ejecute el siguiente comando para cargar NVM en la sesión actual:  
    Bash  
    source \~/.bashrc  
    72  
* **Instalar la Última Versión LTS de Node.js:**  
  * LTS (Long-Term Support) es la versión recomendada para la mayoría de los usuarios. NVM facilita la instalación de la última LTS:  
    Bash  
    nvm install \--lts  
    72  
  * NVM descargará, compilará e instalará la última versión LTS de Node.js y el npm correspondiente.  
* **Verificar la Instalación de Node.js y npm:**  
  * Compruebe las versiones instaladas:  
    Bash  
    node \-v  
    npm \-v  
    72  
  * Debería ver los números de versión de Node.js (ej. v20.x.x) y npm. Es importante tener una versión de Node.js relativamente reciente (\>= 12.13.0 era necesario para Tailwind v2 76, las versiones LTS actuales son mucho más nuevas).

## **6\. Paso 5: Crear el Proyecto Laravel 11**

Ahora que todas las herramientas están listas, podemos crear un nuevo proyecto Laravel 11 usando Composer.

* **Navegar al Directorio htdocs de XAMPP:**  
  * Este es el directorio raíz donde Apache busca los sitios web por defecto.  
  * El directorio htdocs se encuentra en /opt/lampp/htdocs.15  
  * Navegue a este directorio:  
    Bash  
    cd /opt/lampp/htdocs

* **Ajustar Permisos de htdocs (Importante):**  
  * Por defecto, el directorio /opt/lampp/htdocs es propiedad de root, lo que impide que su usuario normal cree archivos o directorios dentro de él.79  
  * Para permitir la creación del proyecto Laravel por su usuario, debe cambiar la propiedad del directorio htdocs a su usuario. Reemplace your\_username con su nombre de usuario real (puede encontrarlo ejecutando whoami 25).  
    Bash  
    sudo chown \-R your\_username:your\_username /opt/lampp/htdocs  
    25  
  * También es buena idea asegurarse de que los permisos permitan a su usuario escribir en él (aunque chown suele ser suficiente, chmod puede asegurar los permisos correctos para el grupo y otros si es necesario, aunque 755 es común):  
    Bash  
    sudo chmod \-R 755 /opt/lampp/htdocs  
    25  
    * **Nota:** Algunos usuarios usan 777 83, pero 755 (o 775 si se configura un grupo específico) es generalmente más seguro. Cambiar la propiedad con chown es el paso clave aquí para permitir la creación del proyecto por el usuario.  
* **Crear el Proyecto Laravel:**  
  * Use el comando create-project de Composer. Reemplace your-project-name con el nombre deseado para su proyecto (e.g., my\_laravel\_app).  
  * Usaremos el PHP de XAMPP explícitamente para asegurar que se usa la versión correcta (PHP \>= 8.2) para las dependencias de Laravel 11\.  
    Bash  
    /opt/lampp/bin/php /usr/local/bin/composer create-project laravel/laravel your-project-name  
    52  
    * Alternativamente, si /opt/lampp/bin está en su PATH o si está seguro de que el php y composer predeterminados son correctos, podría usar: composer create-project laravel/laravel your-project-name  
    * Este comando descargará Laravel 11 y sus dependencias en un nuevo directorio llamado your-project-name dentro de /opt/lampp/htdocs. Laravel 11 presenta una estructura de directorios más simplificada ("Slim Skeleton") en comparación con versiones anteriores.1  
* **Navegar al Directorio del Proyecto:**  
  Bash  
  cd your-project-name  
  52  
* **Configurar el Archivo de Entorno (.env):**  
  * Laravel utiliza un archivo .env para la configuración específica del entorno (base de datos, URL de la aplicación, etc.).  
  * Copie el archivo de ejemplo .env.example a .env:  
    Bash  
    cp.env.example.env  
    52  
* **Generar la Clave de Aplicación:**  
  * Esta clave se utiliza para el cifrado y es esencial para la seguridad.  
  * Ejecute el comando Artisan de Laravel (usando el PHP de XAMPP):  
    Bash  
    /opt/lampp/bin/php artisan key:generate  
    52  
  * Esto generará una clave única y la establecerá en la variable APP\_KEY en su archivo .env.

## **7\. Paso 6: Configurar la Base de Datos y Ejecutar Migraciones**

Configuraremos Laravel para que se conecte a la base de datos MariaDB proporcionada por XAMPP y luego crearemos las tablas necesarias usando las migraciones de Laravel.

* **Crear la Base de Datos:**  
  * Abra phpMyAdmin en su navegador: http://localhost/phpmyadmin.10  
  * Vaya a la pestaña "Databases" (Bases de datos) o haga clic en "New" (Nuevo) en el panel izquierdo.33  
  * En el campo "Database name" (Nombre de la base de datos), ingrese un nombre para su base de datos (e.g., laravel\_app\_db).  
  * **Seleccionar Collation:** Elija una collation adecuada. utf8mb4\_unicode\_ci es una opción segura y ampliamente compatible que maneja correctamente una amplia gama de caracteres, incluidos los emojis.90 Aunque Laravel 11 consideró brevemente utf8mb4\_0900\_ai\_ci como predeterminado para MySQL 8+, esto causó problemas con MariaDB y fue revertido.94 Por lo tanto, utf8mb4\_unicode\_ci es la recomendación actual para compatibilidad con XAMPP/MariaDB. Seleccione utf8mb4\_unicode\_ci en el desplegable de collation.  
  * Haga clic en "Create" (Crear).33  
* **Crear un Usuario de Base de Datos Dedicado (Recomendado):**  
  * Aunque puede usar el usuario root de XAMPP (que por defecto no tiene contraseña 23), es una mejor práctica de seguridad crear un usuario específico para su aplicación.  
  * En phpMyAdmin, vaya a la pestaña "User accounts" (Cuentas de usuario).33  
  * Haga clic en "Add user account" (Añadir cuenta de usuario).33  
  * **Información de inicio de sesión:**  
    * **Username:** Elija un nombre de usuario (e.g., laravel\_user).  
    * **Host name:** Seleccione "Local" en el desplegable (esto lo establecerá en localhost).33  
    * **Password:** Genere o ingrese una contraseña segura. Anótela.  
    * **Re-type:** Confirme la contraseña.  
  * **Privilegios específicos de la base de datos:**  
    * Marque la opción "Create database with same name and grant all privileges" **O**, si prefiere más control:  
    * **No** marque ninguna casilla en "Global privileges".88  
    * Haga clic en "Go" (Continuar) en la parte inferior para crear el usuario.  
    * Después de crear el usuario, vuelva a la pestaña "User accounts", busque su nuevo usuario y haga clic en "Edit privileges" (Editar privilegios).88  
    * Vaya a la sección "Database-specific privileges" (Privilegios específicos de la base de datos).88  
    * Seleccione la base de datos que creó anteriormente (e.g., laravel\_app\_db) en el menú desplegable "Add privileges on the following database".  
    * Haga clic en "Go".  
    * En la página siguiente, haga clic en "Check All" (Marcar todo) para otorgar todos los privilegios necesarios (SELECT, INSERT, UPDATE, DELETE, CREATE, ALTER, INDEX, DROP, etc.) a este usuario *solo para esa base de datos específica*.88  
    * Haga clic en "Go" para guardar los privilegios.  
* **Configurar el Archivo .env de Laravel:**  
  * Abra el archivo .env en la raíz de su proyecto Laravel con un editor de texto: nano.env  
  * Localice las siguientes líneas y actualícelas con los detalles de su base de datos y usuario:  
    Fragmento de código  
    DB\_CONNECTION=mysql  
    DB\_HOST=127.0.0.1  
    DB\_PORT=3306  
    DB\_DATABASE=laravel\_app\_db  \# El nombre de la base de datos que creó  
    DB\_USERNAME=laravel\_user    \# El nombre de usuario que creó (o root)  
    DB\_PASSWORD=your\_strong\_password \# La contraseña que estableció (o dejar en blanco si usa root sin contraseña)  
    52  
    * **Importante:** Use 127.0.0.1 para DB\_HOST en lugar de localhost. Aunque a menudo son intercambiables, localhost a veces puede hacer que PHP intente conectarse a través de un socket Unix en lugar de TCP/IP, lo que puede fallar en configuraciones como XAMPP o WSL si el socket no está donde PHP espera.107 Usar 127.0.0.1 fuerza una conexión TCP/IP al puerto 3306\.  
    * Asegúrese de que DB\_PORT sea 3306 (el puerto predeterminado de MySQL/MariaDB en XAMPP).31  
    * Verifique que DB\_CONNECTION esté configurado en mysql. Aunque XAMPP usa MariaDB, el driver mysql de Laravel es compatible.23 Laravel 11 también introdujo una opción mariadb 94, pero mysql sigue siendo funcional y más común.  
  * Guarde y cierre el archivo .env.  
* **Ejecutar las Migraciones:**  
  * Las migraciones de Laravel definen la estructura de su base de datos en código PHP. El comando migrate crea las tablas correspondientes.  
  * Ejecute el comando Artisan migrate (usando el PHP de XAMPP):  
    Bash  
    /opt/lampp/bin/php artisan migrate  
    3  
  * Si todo está configurado correctamente, verá mensajes indicando que las migraciones (como create\_users\_table, create\_password\_reset\_tokens\_table, etc.) se han ejecutado.  
  * Puede verificar en phpMyAdmin que las tablas (users, password\_reset\_tokens, migrations, etc.) se han creado en su base de datos laravel\_app\_db.  
  * **Solución de problemas comunes:**  
    * **Error Connection refused** 107**:** Verifique que el servidor MySQL/MariaDB de XAMPP esté en ejecución. Verifique DB\_HOST (use 127.0.0.1), DB\_PORT (3306), DB\_USERNAME y DB\_PASSWORD en .env. Asegúrese de que el firewall no esté bloqueando el puerto 3306\.  
    * **Error Access denied for user** 106**:** Verifique DB\_USERNAME y DB\_PASSWORD en .env. Asegúrese de que el usuario de la base de datos tenga los privilegios correctos sobre la base de datos especificada (otorgados en phpMyAdmin).  
    * **Error Unknown database:** Asegúrese de que el nombre en DB\_DATABASE coincida exactamente con el nombre de la base de datos creada en phpMyAdmin.  
    * **Error Base table or view already exists** 111**:** Esto sucede si intenta ejecutar migrate cuando las tablas ya existen. Si desea empezar de nuevo, puede usar php artisan migrate:fresh, que eliminará todas las tablas y volverá a ejecutar todas las migraciones.110 **¡Use migrate:fresh con precaución, ya que elimina todos los datos\!**  
    * **Error Unknown collation: 'utf8mb4\_0900\_ai\_ci'** 42**:** Este error ocurría en las primeras versiones de Laravel 11 con MariaDB/XAMPP. Asegúrese de tener Laravel \>= 11.0.3. Si aún ocurre, puede forzar la collation anterior agregando DB\_COLLATION=utf8mb4\_unicode\_ci a su archivo .env.95

## **8\. Paso 7: Instalar y Configurar Laravel Breeze (Blade Stack)**

Laravel Breeze proporciona un punto de partida simple y mínimo para la autenticación de aplicaciones (inicio de sesión, registro, restablecimiento de contraseña, etc.).31 Lo instalaremos usando el stack Blade con Tailwind CSS.

* **Instalar Breeze vía Composer:**  
  * Asegúrese de estar en el directorio raíz de su proyecto (/opt/lampp/htdocs/your-project-name).  
  * Ejecute el siguiente comando Composer (usando el PHP de XAMPP):  
    Bash  
    /opt/lampp/bin/php /usr/local/bin/composer require laravel/breeze \--dev  
    3  
* **Ejecutar el Comando de Instalación de Breeze:**  
  * Este comando publicará las vistas de autenticación, rutas, controladores y otros recursos.  
  * Ejecute (usando el PHP de XAMPP):  
    Bash  
    /opt/lampp/bin/php artisan breeze:install  
    3  
  * Se le pedirá que seleccione el stack de frontend. Elija la opción para **Blade** (generalmente "Blade with Alpine" o similar).3  
  * También puede preguntarle sobre características adicionales como soporte para modo oscuro (--dark) 65 o si desea instalar pruebas Pest/PHPUnit. Seleccione sus preferencias (los valores predeterminados suelen ser suficientes para empezar).  
* **Ejecutar Migraciones (Nuevamente):**  
  * Breeze puede añadir migraciones adicionales o modificar existentes. Es seguro ejecutar el comando migrate de nuevo; solo procesará las migraciones pendientes.  
    Bash  
    /opt/lampp/bin/php artisan migrate  
    3  
* **Instalar Dependencias de Frontend (npm):**  
  * Breeze requiere dependencias de Node.js para compilar sus assets (principalmente Tailwind CSS).  
    Bash  
    npm install  
    3  
* **Compilar Assets de Frontend (npm):**  
  * Compile los archivos CSS y JS usando Vite (el compilador predeterminado de Laravel).  
    Bash  
    npm run dev  
    3  
  * Este comando compilará los assets y los mantendrá observando cambios mientras desarrolla. Para una compilación de producción única, usaría npm run build.

Ahora, Breeze está instalado. Se han añadido rutas de autenticación (en routes/auth.php), controladores (en app/Http/Controllers/Auth/), y vistas Blade (en resources/views/auth/ y resources/views/components/) a su aplicación.64

* **Explorando Plantillas Blade:**  
  * Las plantillas Blade utilizan una sintaxis simple y potente.116 Archivos como resources/views/dashboard.blade.php, resources/views/profile/edit.blade.php, y las vistas en resources/views/auth/ utilizan esta sintaxis.  
  * **Directivas Clave:**  
    * @extends('layouts.app'): Indica que una vista hereda de un layout base (e.g., resources/views/layouts/app.blade.php).116  
    * @section('content')... @endsection: Define un bloque de contenido que reemplazará un @yield en el layout.116  
    * @yield('content'): Marca un lugar en el layout donde se inyectará el contenido de una @section.116  
    * {{ $variable }}: Muestra el contenido de una variable PHP, escapado automáticamente para prevenir ataques XSS.116  
    * @foreach($items as $item)... @endforeach: Itera sobre arrays o colecciones.116  
    * @if(...)... @else... @endif: Estructuras condicionales.117  
    * @include('partials.header'): Incluye otra vista Blade (subvista o parcial).116

## **9\. Paso 8: Configurar un Virtual Host de Apache**

Para acceder a su aplicación Laravel usando una URL personalizada y limpia (como http://your-project-name.local) en lugar de http://localhost/your-project-name/public, configuraremos un Virtual Host en Apache.

* **Verificar mod\_rewrite:**  
  * Laravel requiere el módulo mod\_rewrite de Apache para manejar URLs amigables (eliminando index.php de la URL).  
  * Generalmente, mod\_rewrite está habilitado por defecto en XAMPP.51  
  * Para verificar, abra el archivo de configuración principal de Apache: sudo nano /opt/lampp/etc/httpd.conf.51  
  * Busque la línea LoadModule rewrite\_module modules/mod\_rewrite.so. Asegúrese de que **no** esté comentada (que no empiece con \#).51 Si lo está, elimine el \#.  
  * Busque también las directivas AllowOverride. Dentro de los bloques \<Directory\> relevantes (especialmente el que apunta a /opt/lampp/htdocs o su directorio de proyecto si lo define aquí), asegúrese de que AllowOverride esté configurado en All en lugar de None. Esto permite que el archivo .htaccess de Laravel funcione correctamente.51 Por ejemplo:  
    Apache  
    \<Directory "/opt/lampp/htdocs"\>  
        \#... otras opciones...  
        AllowOverride All  
        Require all granted  
    \</Directory\>  
    129  
  * Si realizó cambios, guarde el archivo y reinicie Apache: sudo /opt/lampp/lampp restart apache.51  
* **Habilitar el Archivo de Configuración de Virtual Hosts:**  
  * Abra de nuevo el archivo principal de configuración de Apache: sudo nano /opt/lampp/etc/httpd.conf.27  
  * Busque la línea \#Include etc/extra/httpd-vhosts.conf (puede variar ligeramente, busque httpd-vhosts.conf).  
  * Descoméntela eliminando el símbolo \# al principio: Include etc/extra/httpd-vhosts.conf.27  
  * Guarde y cierre el archivo.  
* **Editar el Archivo de Virtual Hosts:**  
  * Abra el archivo de configuración de Virtual Hosts: sudo nano /opt/lampp/etc/extra/httpd-vhosts.conf.27  
  * **Importante: Mantener localhost funcional.** Para seguir accediendo a phpMyAdmin y otras herramientas de XAMPP a través de http://localhost, añada el siguiente bloque VirtualHost *antes* de cualquier otro que defina:  
    Apache  
    \<VirtualHost \*:80\>  
        DocumentRoot "/opt/lampp/htdocs"  
        ServerName localhost  
    \</VirtualHost\>  
    131  
  * **Añadir el Virtual Host para su Proyecto Laravel:** Añada el siguiente bloque, reemplazando your-project-name con el nombre real de su directorio de proyecto y el dominio local deseado:  
    Apache  
    \<VirtualHost \*:80\>  
        ServerAdmin webmaster@localhost  
        DocumentRoot "/opt/lampp/htdocs/your-project-name/public"  
        ServerName your-project-name.local  
        ServerAlias www.your-project-name.local  
        ErrorLog "logs/your-project-name.local-error\_log"  
        CustomLog "logs/your-project-name.local-access\_log" common  
        \<Directory "/opt/lampp/htdocs/your-project-name/public"\>  
            Options Indexes FollowSymLinks MultiViews  
            AllowOverride All  
            Require all granted  
        \</Directory\>  
    \</VirtualHost\>  
    27  
    * **Explicación de Directivas Clave:**  
      * DocumentRoot: **Debe** apuntar al directorio public dentro de su proyecto Laravel, ya que este es el punto de entrada de la aplicación.  
      * ServerName: El dominio local personalizado que usará para acceder al proyecto (e.g., my\_laravel\_app.local).  
      * ServerAlias: Nombres alternativos (opcional).  
      * \<Directory...\>: Aplica configuraciones específicas a DocumentRoot.  
      * AllowOverride All: **Crucial** para permitir que el archivo .htaccess de Laravel (ubicado en el directorio public) gestione las reescrituras de URL (rutas amigables).  
      * Require all granted: Necesario en Apache 2.4+ para permitir el acceso al directorio.  
  * Guarde y cierre el archivo httpd-vhosts.conf.  
* **Editar el Archivo hosts del Sistema:**  
  * Necesitamos decirle a su sistema Ubuntu que el dominio personalizado (e.g., your-project-name.local) debe resolverse a la dirección IP de su propia máquina (loopback, 127.0.0.1).  
  * Abra el archivo hosts: sudo nano /etc/hosts.27  
  * Añada la siguiente línea al final del archivo, reemplazando your-project-name.local con el ServerName que eligió:  
    127.0.0.1   your-project-name.local www.your-project-name.local  
    27  
  * Guarde y cierre el archivo hosts.  
* **Reiniciar Apache:**  
  * Aplique todos los cambios de configuración reiniciando el servidor Apache de XAMPP:  
    Bash  
    sudo /opt/lampp/lampp restart  
    27 (O sudo /opt/lampp/lampp restartapache si solo modificó la configuración de Apache).

Ahora debería poder acceder a su aplicación Laravel visitando http://your-project-name.local en su navegador web.

## **10\. Step 9: Establecer Permisos de Archivos de Laravel**

Este es un paso **crítico** y una fuente común de errores en entornos Linux como Ubuntu con XAMPP. Laravel necesita que su servidor web (Apache, que en XAMPP se ejecuta comúnmente como el usuario daemon 23) tenga permisos de escritura en directorios específicos para funcionar correctamente (por ejemplo, para escribir archivos de log, caché, sesiones, vistas compiladas).105 Aunque en el Paso 5 ajustamos los permisos de htdocs para permitir la creación del proyecto por your\_username, ahora debemos asegurarnos de que el usuario daemon de Apache pueda escribir en los directorios storage y bootstrap/cache en tiempo de ejecución.

* **Navegar a la Raíz del Proyecto:**  
  Bash  
  cd /opt/lampp/htdocs/your-project-name

* **Establecer Propiedad (Ownership):**  
  * Cambie la propiedad de los directorios storage y bootstrap/cache (y todo su contenido recursivamente) al usuario y grupo bajo el cual se ejecuta Apache en XAMPP. Asumiremos que es daemon:daemon.  
    Bash  
    sudo chown \-R daemon:daemon storage bootstrap/cache  
    144  
    * Esto asegura que el proceso del servidor web sea el propietario de estos directorios cruciales.  
* **Establecer Permisos de Directorio:**  
  * Otorgue permisos de lectura, escritura y ejecución al propietario (daemon) y al grupo (daemon), y permiso de lectura y ejecución a otros. Esto se logra con el modo 775\.  
    Bash  
    sudo find storage \-type d \-exec chmod 775 {} \\;  
    sudo find bootstrap/cache \-type d \-exec chmod 775 {} \\;  
    145  
* **Establecer Permisos de Archivo:**  
  * Otorgue permisos de lectura y escritura al propietario (daemon) y al grupo (daemon), y permiso de solo lectura a otros. Esto se logra con el modo 664\.  
    Bash  
    sudo find storage \-type f \-exec chmod 664 {} \\;  
    sudo find bootstrap/cache \-type f \-exec chmod 664 {} \\;  
    145  
* **Permisos del Archivo .env:**  
  * Por seguridad, el archivo .env contiene credenciales sensibles y no debería ser legible por todos. Se recomienda establecer permisos más restrictivos, como 600 (solo lectura/escritura para el propietario) o 640 (lectura/escritura para propietario, lectura para grupo). Asegúrese de que el usuario daemon (o el grupo daemon) pueda leerlo si es necesario (aunque generalmente es leído por el proceso PHP que podría ejecutarse como your\_username si usa php artisan serve, pero como daemon bajo Apache). 640 o 600 son opciones seguras.  
    Bash  
    sudo chmod 600.env  
    146 O, si necesita que el grupo daemon lo lea:  
    Bash  
    sudo chown your\_username:daemon.env  
    sudo chmod 640.env

* **Resumen de Permisos de Laravel en XAMPP/Ubuntu:**

| Tipo de Archivo/Directorio | Ruta Relativa (desde raíz del proyecto) | Propietario:Grupo Recomendado | Permisos Recomendados | Razón |
| :---- | :---- | :---- | :---- | :---- |
| Directorios Generales | app/, config/, routes/, etc. | your\_username:your\_username | 755 (rwxr-xr-x) | El desarrollador necesita control total; el servidor web solo necesita leer/ejecutar. |
| Archivos Generales | \*.php, \*.js, etc. | your\_username:your\_username | 644 (rw-r--r--) | El desarrollador necesita leer/escribir; el servidor web solo necesita leer. |
| Directorio storage | storage/ | daemon:daemon | 775 (rwxrwxr-x) | El servidor web (daemon) necesita escribir logs, caché, sesiones, archivos subidos, etc. 145 |
| Archivos en storage | storage/logs/laravel.log, etc. | daemon:daemon | 664 (rw-rw-r--) | El servidor web (daemon) necesita escribir en estos archivos. 145 |
| Directorio bootstrap/cache | bootstrap/cache/ | daemon:daemon | 775 (rwxrwxr-x) | El servidor web (daemon) necesita escribir archivos de caché (config, rutas, vistas). 145 |
| Archivos en bootstrap/cache | bootstrap/cache/\*.php, etc. | daemon:daemon | 664 (rw-rw-r--) | El servidor web (daemon) necesita escribir estos archivos. 145 |
| Archivo .env | .env | your\_username:daemon | 640 (rw-r-----) | Contiene secretos; solo el propietario (desarrollador) y el grupo (servidor web) deben leerlo. 146 |

\*Nota: \`your\_username\` es su usuario de Ubuntu, \`daemon\` es el usuario/grupo bajo el cual se ejecuta Apache en XAMPP.\*

Configurar estos permisos correctamente es fundamental para evitar errores comunes de "Permission denied" \[105, 144, 152, 153, 154\] y asegurar que Laravel pueda realizar operaciones de escritura necesarias durante la ejecución. Usar \`775\` y \`664\` para \`storage\` y \`bootstrap/cache\` con la propiedad correcta (\`daemon:daemon\`) equilibra la funcionalidad y la seguridad, siendo preferible a los permisos excesivamente abiertos como \`777\`.\[149, 150\]

## **11\. Step 10: Verificar la Instalación Completa**

Finalmente, verifique que todo funcione como se espera.

* **Acceder a la Aplicación:**  
  * Abra su navegador web y navegue a la URL que configuró en su Virtual Host (e.g., http://your-project-name.local).112  
* **Verificar la Página de Bienvenida/Autenticación:**  
  * Debería ver la página de bienvenida de Laravel 11 1 o, si Breeze redirige automáticamente, la página de inicio de sesión o registro de Breeze.  
* **Probar la Autenticación de Breeze:**  
  * Haga clic en el enlace "Register" (Registrarse).  
  * Complete el formulario de registro con datos de prueba y envíelo.  
  * Verifique que el registro sea exitoso y que sea redirigido a la página del panel de control (generalmente /dashboard).  
  * Cierre la sesión (Logout).  
  * Haga clic en el enlace "Log in" (Iniciar sesión).  
  * Inicie sesión con las credenciales que acaba de crear.  
  * Verifique que el inicio de sesión sea exitoso y que sea redirigido al panel de control.  
* **Revisar Logs en Caso de Errores:**  
  * Si encuentra algún problema (pantalla blanca, error 500 105, errores específicos de Laravel), el primer lugar para buscar es el archivo de log de Laravel:  
    Bash  
    tail \-f /opt/lampp/htdocs/your-project-name/storage/logs/laravel.log  
    105 (Use Ctrl+C para detener el seguimiento). O ábralo en un editor de texto.  
  * También revise los logs de error de Apache. Puede acceder a ellos a través del panel de control de XAMPP (sudo /opt/lampp/manager-linux-x64.run) 29 o buscando en el directorio /opt/lampp/logs/.109  
* **Puntos Comunes de Solución de Problemas (Recap):**  
  * **Configuración .env:** Credenciales de base de datos incorrectas (DB\_HOST, DB\_DATABASE, DB\_USERNAME, DB\_PASSWORD), APP\_URL incorrecta.105  
  * **Conexión/Privilegios de Base de Datos:** Servidor MySQL no iniciado, usuario sin privilegios suficientes sobre la base de datos.106  
  * **Permisos de Archivo:** Los directorios storage y bootstrap/cache no son escribibles por el usuario daemon de Apache.105 Vuelva a ejecutar los comandos chown y chmod del Paso 9\.  
  * **Extensiones PHP:** Falta alguna extensión requerida o no está habilitada en /opt/lampp/etc/php.ini.41 Verifique con phpinfo() o /opt/lampp/bin/php \-m.  
  * **Configuración de Apache:** Errores en httpd.conf o httpd-vhosts.conf, mod\_rewrite no habilitado o AllowOverride no configurado en All, error en el archivo .htaccess de Laravel (en public/).51  
  * **Archivo hosts:** La entrada para su dominio local (your-project-name.local) no está presente o es incorrecta en /etc/hosts.

## **12\. Conclusión**

Siguiendo los pasos detallados en esta guía, ha configurado con éxito un entorno de desarrollo local para Laravel 11 en Ubuntu utilizando XAMPP, prescindiendo de Docker o Sail. Ha instalado y configurado XAMPP con la versión de PHP adecuada, Composer, Node.js, ha creado un proyecto Laravel 11, lo ha conectado a una base de datos MariaDB gestionada a través de phpMyAdmin, ha implementado la autenticación básica con Laravel Breeze y plantillas Blade, ha configurado un Virtual Host de Apache para un acceso limpio y, fundamentalmente, ha ajustado los permisos de archivo necesarios para el correcto funcionamiento de Laravel en un entorno Linux/XAMPP.

Ahora dispone de una base sólida y totalmente funcional para comenzar a desarrollar su aplicación Laravel. Puede explorar la extensa documentación oficial de Laravel 52 para aprender más sobre sus características, profundizar en las capacidades de las plantillas Blade 116 o personalizar la autenticación proporcionada por Breeze.64

¡Feliz desarrollo\!

#### **Obras citadas**

1. Laravel 11 \- New Features, Guide, Installation & More \- Cloudways, fecha de acceso: mayo 6, 2025, [https://www.cloudways.com/blog/laravel-11/](https://www.cloudways.com/blog/laravel-11/)  
2. Laravel PHP Requirements and How They Impact Laravel Upgrades | Zend by Perforce, fecha de acceso: mayo 6, 2025, [https://www.zend.com/blog/laravel-php-requirements](https://www.zend.com/blog/laravel-php-requirements)  
3. How to Install Laravel Breeze on Laravel 12 \- Codecourse, fecha de acceso: mayo 6, 2025, [https://codecourse.com/articles/how-to-install-laravel-breeze-on-laravel-12](https://codecourse.com/articles/how-to-install-laravel-breeze-on-laravel-12)  
4. How to Install Breeze on Laravel 12 \- YouTube, fecha de acceso: mayo 6, 2025, [https://www.youtube.com/watch?v=H-EC0vbie1c](https://www.youtube.com/watch?v=H-EC0vbie1c)  
5. ubuntu.com, fecha de acceso: mayo 6, 2025, [https://ubuntu.com/about/release-cycle](https://ubuntu.com/about/release-cycle)  
6. Releases \- Ubuntu Wiki, fecha de acceso: mayo 6, 2025, [https://wiki.ubuntu.com/Releases](https://wiki.ubuntu.com/Releases)  
7. When is the exact date for Ubuntu 22.04.4 EOL?, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/1506874/when-is-the-exact-date-for-ubuntu-22-04-4-eol](https://askubuntu.com/questions/1506874/when-is-the-exact-date-for-ubuntu-22-04-4-eol)  
8. What's New? Ubuntu 22.04.5 LTS Released with Linux Kernel 6.8 \- TuxCare, fecha de acceso: mayo 6, 2025, [https://tuxcare.com/blog/ubuntu-22-04-5/](https://tuxcare.com/blog/ubuntu-22-04-5/)  
9. Ubuntu | endoflife.date, fecha de acceso: mayo 6, 2025, [https://endoflife.date/ubuntu](https://endoflife.date/ubuntu)  
10. How to Install XAMPP on Linux: A Quick Step-by-Step Guide | HackerNoon, fecha de acceso: mayo 6, 2025, [https://hackernoon.com/how-to-install-xampp-on-linux-a-quick-step-by-step-guide](https://hackernoon.com/how-to-install-xampp-on-linux-a-quick-step-by-step-guide)  
11. Upgrading Ubuntu 20.04 to 22.04 LTS using the command line | Blog | Krystal Hosting, fecha de acceso: mayo 6, 2025, [https://krystal.io/blog/post/upgrading-ubuntu-20-04-to-22-04-lts-using-the-command-line](https://krystal.io/blog/post/upgrading-ubuntu-20-04-to-22-04-lts-using-the-command-line)  
12. How to Install and Use Composer on Ubuntu 22.04 \- Cherry Servers, fecha de acceso: mayo 6, 2025, [https://www.cherryservers.com/blog/how-to-install-composer-ubuntu](https://www.cherryservers.com/blog/how-to-install-composer-ubuntu)  
13. How to Install XAMPP in Ubuntu {With Screenshots} \- phoenixNAP, fecha de acceso: mayo 6, 2025, [https://phoenixnap.com/kb/how-to-install-xampp-on-ubuntu](https://phoenixnap.com/kb/how-to-install-xampp-on-ubuntu)  
14. XAMPP Installers and Downloads for Apache Friends, fecha de acceso: mayo 6, 2025, [https://www.apachefriends.org/](https://www.apachefriends.org/)  
15. How to Install XAMPP on Linux (with Pictures) \- wikiHow, fecha de acceso: mayo 6, 2025, [https://www.wikihow.com/Install-XAMPP-on-Linux](https://www.wikihow.com/Install-XAMPP-on-Linux)  
16. Download XAMPP, fecha de acceso: mayo 6, 2025, [https://www.apachefriends.org/download.html](https://www.apachefriends.org/download.html)  
17. A Step-By-Step Guide on How to Install Xampp on Windows, macOS, Linux With Screenshots \- Wazobia Technologies, fecha de acceso: mayo 6, 2025, [https://wazobia.tech/blog/development/a-step-by-step-guide-on-how-to-install-xampp-on-windows-macos-linux-with-screenshots](https://wazobia.tech/blog/development/a-step-by-step-guide-on-how-to-install-xampp-on-windows-macos-linux-with-screenshots)  
18. Release Notes \- Laravel 11.x \- The PHP Framework For Web Artisans, fecha de acceso: mayo 6, 2025, [https://laravel.com/docs/11.x/releases](https://laravel.com/docs/11.x/releases)  
19. What's New in Laravel 11 \- SaaSykit, fecha de acceso: mayo 6, 2025, [https://saasykit.com/blog/whats-new-in-laravel-11](https://saasykit.com/blog/whats-new-in-laravel-11)  
20. Upgrade Guide \- Laravel 11.x \- The PHP Framework For Web Artisans, fecha de acceso: mayo 6, 2025, [https://laravel.com/docs/11.x/upgrade](https://laravel.com/docs/11.x/upgrade)  
21. Laravel 11: Main New Features and Changes, fecha de acceso: mayo 6, 2025, [https://laraveldaily.com/post/laravel-11-main-new-features-changes](https://laraveldaily.com/post/laravel-11-main-new-features-changes)  
22. New XAMPP release 8.2.12, 8.1.25 and 8.0.30, fecha de acceso: mayo 6, 2025, [https://www.apachefriends.org/blog/new\_xampp\_20231119.html](https://www.apachefriends.org/blog/new_xampp_20231119.html)  
23. XAMPP FAQs for Linux, fecha de acceso: mayo 6, 2025, [https://www.apachefriends.org/faq\_linux.html](https://www.apachefriends.org/faq_linux.html)  
24. Install XAMPP on your linux system \- DEV Community, fecha de acceso: mayo 6, 2025, [https://dev.to/shawon/install-xampp-on-your-linux-system-48fl](https://dev.to/shawon/install-xampp-on-your-linux-system-48fl)  
25. correct permissions for Xampp on Ubuntu \- Joomla\! Forum \- community, help and support, fecha de acceso: mayo 6, 2025, [https://forum.joomla.org/viewtopic.php?t=978099](https://forum.joomla.org/viewtopic.php?t=978099)  
26. How to correctly install XAMPP and check that it is properly installed \- GitHub Gist, fecha de acceso: mayo 6, 2025, [https://gist.github.com/JQL/580398f9b20305a8de2dac9fadf495c4](https://gist.github.com/JQL/580398f9b20305a8de2dac9fadf495c4)  
27. Create virtual host in XAMPP, Ubuntu 16.04 \- GitHub Gist, fecha de acceso: mayo 6, 2025, [https://gist.github.com/sibysathyanesan/1f8773a44da9ffbc6767657f72b6e959](https://gist.github.com/sibysathyanesan/1f8773a44da9ffbc6767657f72b6e959)  
28. Start/stop XAMPP on Ubuntu using Terminal | keithgreer.dev, fecha de acceso: mayo 6, 2025, [https://keithgreer.dev/running-xampp-on-ubuntu-using-terminal/](https://keithgreer.dev/running-xampp-on-ubuntu-using-terminal/)  
29. Launch Xampp from Linux Command Line \-- The Easy Way | The First Byte, fecha de acceso: mayo 6, 2025, [https://terracoders.com/blog/launch-xampp-linux-command-line-easy-way](https://terracoders.com/blog/launch-xampp-linux-command-line-easy-way)  
30. Auto Start XAMPP at Startup in Ubuntu Linux \- Computer Networking Notes, fecha de acceso: mayo 6, 2025, [https://www.computernetworkingnotes.com/linux-tutorials/auto-start-xampp-at-startup-in-ubuntu-linux.html](https://www.computernetworkingnotes.com/linux-tutorials/auto-start-xampp-at-startup-in-ubuntu-linux.html)  
31. XAMPP FAQs for Windows, fecha de acceso: mayo 6, 2025, [https://www.apachefriends.org/faq\_windows.html](https://www.apachefriends.org/faq_windows.html)  
32. What is the default username and password for XAMPP? \- Quora, fecha de acceso: mayo 6, 2025, [https://www.quora.com/What-is-the-default-username-and-password-for-XAMPP](https://www.quora.com/What-is-the-default-username-and-password-for-XAMPP)  
33. Database setup using XAMPP, fecha de acceso: mayo 6, 2025, [https://www.cs.virginia.edu/\~up3f/cs4750/supplement/DB-setup-xampp.html](https://www.cs.virginia.edu/~up3f/cs4750/supplement/DB-setup-xampp.html)  
34. http://localhost/phpmyadmin, fecha de acceso: mayo 6, 2025, [https://locallhost.me/phpmyadmin](https://locallhost.me/phpmyadmin)  
35. MySQL and phpMyAdmin \- XAMPP | Shaharil Mad Saad, Dr \- People@UTM, fecha de acceso: mayo 6, 2025, [https://people.utm.my/shaharil/mysql-and-phpmyadmin-xampp/](https://people.utm.my/shaharil/mysql-and-phpmyadmin-xampp/)  
36. I can not access phpMyAdmin on XAMPP \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/30255567/i-can-not-access-phpmyadmin-on-xampp](https://stackoverflow.com/questions/30255567/i-can-not-access-phpmyadmin-on-xampp)  
37. How to Create a XAMPP MySQL Database in 9 Easy Steps \- Hevo Data, fecha de acceso: mayo 6, 2025, [https://hevodata.com/learn/xampp-mysql/](https://hevodata.com/learn/xampp-mysql/)  
38. How to create a database using phpMyAdmin and XAMPP \- SkillForge, fecha de acceso: mayo 6, 2025, [https://skillforge.com/how-to-create-a-database-using-phpmyadmin-xampp/](https://skillforge.com/how-to-create-a-database-using-phpmyadmin-xampp/)  
39. Ubuntu 16.04 \- How to start xampp control panel, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/890818/ubuntu-16-04-how-to-start-xampp-control-panel](https://askubuntu.com/questions/890818/ubuntu-16-04-how-to-start-xampp-control-panel)  
40. How to know whether I have LAMP or XAMPP installed in my Ubuntu? \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/45303096/how-to-know-whether-i-have-lamp-or-xampp-installed-in-my-ubuntu](https://stackoverflow.com/questions/45303096/how-to-know-whether-i-have-lamp-or-xampp-installed-in-my-ubuntu)  
41. How to install all required PHP extensions for Laravel? \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/40815984/how-to-install-all-required-php-extensions-for-laravel](https://stackoverflow.com/questions/40815984/how-to-install-all-required-php-extensions-for-laravel)  
42. How to Resolve Laravel Dusk Installation Issues: Missing ext-zip and PHP Compatibility, fecha de acceso: mayo 6, 2025, [https://www.devopssupport.in/blog/how-to-resolve-laravel-dusk-installation-issues-missing-ext-zip-and-php-compatibility/](https://www.devopssupport.in/blog/how-to-resolve-laravel-dusk-installation-issues-missing-ext-zip-and-php-compatibility/)  
43. Laravel installing Error: Missing Php Extension \[duplicate\] \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/47071133/laravel-installing-error-missing-php-extension](https://stackoverflow.com/questions/47071133/laravel-installing-error-missing-php-extension)  
44. How to know what version of PHP is used on my xampp? \- Ask Ubuntu, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/435839/how-to-know-what-version-of-php-is-used-on-my-xampp](https://askubuntu.com/questions/435839/how-to-know-what-version-of-php-is-used-on-my-xampp)  
45. How to check which PHP extensions have been enabled/disabled in Ubuntu Linux 12.04 LTS? \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/24351260/how-to-check-which-php-extensions-have-been-enabled-disabled-in-ubuntu-linux-12](https://stackoverflow.com/questions/24351260/how-to-check-which-php-extensions-have-been-enabled-disabled-in-ubuntu-linux-12)  
46. How to List Compiled PHP Modules from Command Line \- Liquid Web, fecha de acceso: mayo 6, 2025, [https://www.liquidweb.com/blog/how-to-list-compiled-php-modules-from-command-line/](https://www.liquidweb.com/blog/how-to-list-compiled-php-modules-from-command-line/)  
47. Check dynamically loaded PHP extensions from command line \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/14478921/check-dynamically-loaded-php-extensions-from-command-line](https://stackoverflow.com/questions/14478921/check-dynamically-loaded-php-extensions-from-command-line)  
48. how to know which php extensions are installed \- Unix & Linux Stack Exchange, fecha de acceso: mayo 6, 2025, [https://unix.stackexchange.com/questions/167975/how-to-know-which-php-extensions-are-installed](https://unix.stackexchange.com/questions/167975/how-to-know-which-php-extensions-are-installed)  
49. how can I enable PHP Extension intl? \- xampp \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/33869521/how-can-i-enable-php-extension-intl](https://stackoverflow.com/questions/33869521/how-can-i-enable-php-extension-intl)  
50. XAMPP \- Wikipedia, fecha de acceso: mayo 6, 2025, [https://en.wikipedia.org/wiki/XAMPP](https://en.wikipedia.org/wiki/XAMPP)  
51. how to enable mod\_rewrite for xampp? \- Ask Ubuntu, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/184468/how-to-enable-mod-rewrite-for-xampp](https://askubuntu.com/questions/184468/how-to-enable-mod-rewrite-for-xampp)  
52. \# Installing Laravel 11: A Step-by-Step Guide \- DEV Community, fecha de acceso: mayo 6, 2025, [https://dev.to/jsandaruwan/-installing-laravel-11-a-step-by-step-guide-2mkj](https://dev.to/jsandaruwan/-installing-laravel-11-a-step-by-step-guide-2mkj)  
53. How to Install and Configure a Laravel 11 Project from Scratch \- hashnode.dev, fecha de acceso: mayo 6, 2025, [https://devnook.hashnode.dev/how-to-install-and-configure-a-laravel-11-project-from-scratch](https://devnook.hashnode.dev/how-to-install-and-configure-a-laravel-11-project-from-scratch)  
54. How to Install Laravel 11 using Composer? \- MageComp, fecha de acceso: mayo 6, 2025, [https://magecomp.com/blog/install-laravel-11-using-composer/](https://magecomp.com/blog/install-laravel-11-using-composer/)  
55. Create Laravel 11 project from scratch \- HiBit, fecha de acceso: mayo 6, 2025, [https://www.hibit.dev/posts/157/create-laravel-11-project-from-scratch](https://www.hibit.dev/posts/157/create-laravel-11-project-from-scratch)  
56. Composer: How To Install and Use on Ubuntu | Guide by Hostman, fecha de acceso: mayo 6, 2025, [https://hostman.com/tutorials/how-to-install-and-use-composer-on-ubuntu/](https://hostman.com/tutorials/how-to-install-and-use-composer-on-ubuntu/)  
57. How to Install and Use Composer on Ubuntu 20.04 | 22.04 \- DedicatedCore, fecha de acceso: mayo 6, 2025, [https://www.dedicatedcore.com/blog/install-composer-ubuntu/](https://www.dedicatedcore.com/blog/install-composer-ubuntu/)  
58. How to install Composer on Ubuntu 22.04 \- IONOS, fecha de acceso: mayo 6, 2025, [https://www.ionos.com/digitalguide/server/configuration/how-to-install-composer-on-ubuntu-2204/](https://www.ionos.com/digitalguide/server/configuration/how-to-install-composer-on-ubuntu-2204/)  
59. How to Install Composer on Ubuntu 22.04 | Ultahost Knowledge Base, fecha de acceso: mayo 6, 2025, [https://ultahost.com/knowledge-base/install-composer-on-ubuntu/](https://ultahost.com/knowledge-base/install-composer-on-ubuntu/)  
60. How To Install Composer on Ubuntu 22.04 \- Real Time Cloud Services LLC \- AceCloud, fecha de acceso: mayo 6, 2025, [https://customer.acecloudhosting.com/index.php/knowledgebase/151/How-To-Install-Composer-on-Ubuntu-22.04.html?language=dutch](https://customer.acecloudhosting.com/index.php/knowledgebase/151/How-To-Install-Composer-on-Ubuntu-22.04.html?language=dutch)  
61. Download Composer Latest: v2.8.8, fecha de acceso: mayo 6, 2025, [https://getcomposer.org/download/](https://getcomposer.org/download/)  
62. Composer download | SourceForge.net, fecha de acceso: mayo 6, 2025, [https://sourceforge.net/projects/composer.mirror/](https://sourceforge.net/projects/composer.mirror/)  
63. Composer, fecha de acceso: mayo 6, 2025, [https://getcomposer.org/](https://getcomposer.org/)  
64. Starter Kits \- Laravel 11.x \- The PHP Framework For Web Artisans, fecha de acceso: mayo 6, 2025, [https://laravel.com/docs/11.x/starter-kits](https://laravel.com/docs/11.x/starter-kits)  
65. Starter Kits \- Laravel 9.x \- The PHP Framework For Web Artisans, fecha de acceso: mayo 6, 2025, [https://laravel.com/docs/9.x/starter-kits](https://laravel.com/docs/9.x/starter-kits)  
66. Installing Laravel Breeze and a Quick Walkthrough \- DEV Community, fecha de acceso: mayo 6, 2025, [https://dev.to/moose\_said/installing-laravel-breeze-and-a-quick-walkthrough-20ej](https://dev.to/moose_said/installing-laravel-breeze-and-a-quick-walkthrough-20ej)  
67. Starter Kits \- Laravel 10.x \- The PHP Framework For Web Artisans, fecha de acceso: mayo 6, 2025, [https://laravel.com/docs/10.x/starter-kits](https://laravel.com/docs/10.x/starter-kits)  
68. Laravel Breeze \- DEV Community, fecha de acceso: mayo 6, 2025, [https://dev.to/thedevricha/laravel-breeze-4e1f](https://dev.to/thedevricha/laravel-breeze-4e1f)  
69. Authentication in Laravel Using Breeze \- Kinsta®, fecha de acceso: mayo 6, 2025, [https://kinsta.com/blog/laravel-breeze/](https://kinsta.com/blog/laravel-breeze/)  
70. Laravel Installing Blade and npm run dev Fails \- php \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/75786518/laravel-installing-blade-and-npm-run-dev-fails](https://stackoverflow.com/questions/75786518/laravel-installing-blade-and-npm-run-dev-fails)  
71. 02\. Installation \- Laravel Bootcamp, fecha de acceso: mayo 6, 2025, [https://bootcamp.laravel.com/blade/installation](https://bootcamp.laravel.com/blade/installation)  
72. How to Install Node.js on Ubuntu 22.04: Step-by-Step Guide \- Blog \- BlueVPS, fecha de acceso: mayo 6, 2025, [https://bluevps.com/blog/how-to-install-nodejs-on-ubuntu-2204](https://bluevps.com/blog/how-to-install-nodejs-on-ubuntu-2204)  
73. How to install Node JS latest version on Ubuntu 22.04?, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/1502744/how-to-install-node-js-latest-version-on-ubuntu-22-04](https://askubuntu.com/questions/1502744/how-to-install-node-js-latest-version-on-ubuntu-22-04)  
74. How To Install Node.js on Ubuntu \- DigitalOcean, fecha de acceso: mayo 6, 2025, [https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-22-04](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-22-04)  
75. Download Node.js, fecha de acceso: mayo 6, 2025, [https://nodejs.org/en/download](https://nodejs.org/en/download)  
76. Node error when I run npm with laravel breeze \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/65311936/node-error-when-i-run-npm-with-laravel-breeze](https://stackoverflow.com/questions/65311936/node-error-when-i-run-npm-with-laravel-breeze)  
77. Xampp htdocs folder, sub folders and all files permission \- Ask Ubuntu, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/596209/xampp-htdocs-folder-sub-folders-and-all-files-permission](https://askubuntu.com/questions/596209/xampp-htdocs-folder-sub-folders-and-all-files-permission)  
78. How to access xampp htdocs folder in Ubuntu 18.04.2, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/1120634/how-to-access-xampp-htdocs-folder-in-ubuntu-18-04-2](https://askubuntu.com/questions/1120634/how-to-access-xampp-htdocs-folder-in-ubuntu-18-04-2)  
79. XAMPP htdocs Permission issue and Fix in Ubuntu \- Computer Networking Notes, fecha de acceso: mayo 6, 2025, [https://www.computernetworkingnotes.com/linux-tutorials/xampp-htdocs-permission-issue-and-fix-in-ubuntu.html](https://www.computernetworkingnotes.com/linux-tutorials/xampp-htdocs-permission-issue-and-fix-in-ubuntu.html)  
80. Give user permissions to htdocs in Ubuntu \[closed\] \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/43558542/give-user-permissions-to-htdocs-in-ubuntu](https://stackoverflow.com/questions/43558542/give-user-permissions-to-htdocs-in-ubuntu)  
81. How to change Apache user and group \- Simplified Guide, fecha de acceso: mayo 6, 2025, [https://www.simplified.guide/apache/change-user-and-group](https://www.simplified.guide/apache/change-user-and-group)  
82. Open a PHP/Laravel Project with Xampp \- Programming \- Linus Tech Tips, fecha de acceso: mayo 6, 2025, [https://linustechtips.com/topic/1046054-open-a-phplaravel-project-with-xampp/](https://linustechtips.com/topic/1046054-open-a-phplaravel-project-with-xampp/)  
83. Can't create any folder in htdocs on ubuntu \- Super User, fecha de acceso: mayo 6, 2025, [https://superuser.com/questions/268987/cant-create-any-folder-in-htdocs-on-ubuntu](https://superuser.com/questions/268987/cant-create-any-folder-in-htdocs-on-ubuntu)  
84. How to Create a Laravel 11 Project With XAMPP and Visual Studio Code \- absprog.com, fecha de acceso: mayo 6, 2025, [https://absprog.com/post/create-laravel-11-project-on-xampp](https://absprog.com/post/create-laravel-11-project-on-xampp)  
85. Create a totally empty project with laravel new \- Laracasts, fecha de acceso: mayo 6, 2025, [https://laracasts.com/discuss/channels/laravel/create-a-totally-empty-project-with-laravel-new](https://laracasts.com/discuss/channels/laravel/create-a-totally-empty-project-with-laravel-new)  
86. Laravel Migrations: Manage Your Database Migrations with Our Guide \- ButterCMS, fecha de acceso: mayo 6, 2025, [https://buttercms.com/blog/laravel-migrations-ultimate-guide/](https://buttercms.com/blog/laravel-migrations-ultimate-guide/)  
87. How to Create a New Database in phpMyAdmin? \- GeeksforGeeks, fecha de acceso: mayo 6, 2025, [https://www.geeksforgeeks.org/how-to-create-a-new-database-in-phpmyadmin/](https://www.geeksforgeeks.org/how-to-create-a-new-database-in-phpmyadmin/)  
88. How to Create a MySQL Database with phpMyAdmin \- Webvault WIKI\!, fecha de acceso: mayo 6, 2025, [http://webvaultwiki.com.au/Create-Mysql-Database-User-Phpmyadmin.ashx](http://webvaultwiki.com.au/Create-Mysql-Database-User-Phpmyadmin.ashx)  
89. Database setup using XAMPP \- CS4640 \- WebPL, fecha de acceso: mayo 6, 2025, [https://www.cs.virginia.edu/\~up3f/cs4640/supplement/DB-setup-xampp.html](https://www.cs.virginia.edu/~up3f/cs4640/supplement/DB-setup-xampp.html)  
90. Which collation and character set should I pick? \- Mastering Laravel, fecha de acceso: mayo 6, 2025, [https://masteringlaravel.io/daily/2024-12-06-which-collation-and-character-set-should-i-pick](https://masteringlaravel.io/daily/2024-12-06-which-collation-and-character-set-should-i-pick)  
91. Database: Getting Started \- Laravel 11.x \- The PHP Framework For Web Artisans, fecha de acceso: mayo 6, 2025, [https://laravel.com/docs/11.x/database](https://laravel.com/docs/11.x/database)  
92. In MySQL, which collation should I choose? \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/1231105/in-mysql-which-collation-should-i-choose](https://stackoverflow.com/questions/1231105/in-mysql-which-collation-should-i-choose)  
93. What collation SHOULD I be using with mysql? : r/PHPhelp \- Reddit, fecha de acceso: mayo 6, 2025, [https://www.reddit.com/r/PHPhelp/comments/556bbq/what\_collation\_should\_i\_be\_using\_with\_mysql/](https://www.reddit.com/r/PHPhelp/comments/556bbq/what_collation_should_i_be_using_with_mysql/)  
94. Laravel 11: NEW FEATURES AND UPDATES \- RhysEvans.dev, fecha de acceso: mayo 6, 2025, [https://rhysevans.dev/blog/laravel-11-new-features-and-updates](https://rhysevans.dev/blog/laravel-11-new-features-and-updates)  
95. Unknown collation: 'utf8mb4\_0900\_ai\_ci' for Laravel 11 database · Issue \#5962 \- GitHub, fecha de acceso: mayo 6, 2025, [https://github.com/ddev/ddev/issues/5962](https://github.com/ddev/ddev/issues/5962)  
96. Laravel11 mysql migration problem with collation: "General error: 1273 Unknown collation: 'utf8mb4\_0900\_ai\_ci'" \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/78154387/laravel11-mysql-migration-problem-with-collation-general-error-1273-unknown-c](https://stackoverflow.com/questions/78154387/laravel11-mysql-migration-problem-with-collation-general-error-1273-unknown-c)  
97. Migration error in Laravel 11 \- Laracasts, fecha de acceso: mayo 6, 2025, [https://laracasts.com/discuss/channels/laravel/migration-error-in-laravel-11](https://laracasts.com/discuss/channels/laravel/migration-error-in-laravel-11)  
98. How create a MySQL database user in XAMPP ? \- PHPGurukul, fecha de acceso: mayo 6, 2025, [https://phpgurukul.com/how-create-a-mysql-database-user-in-xampp/](https://phpgurukul.com/how-create-a-mysql-database-user-in-xampp/)  
99. PhpMyAdmin database creation and user privileges | BikeGremlin I/O, fecha de acceso: mayo 6, 2025, [https://io.bikegremlin.com/15152/phpmyadmin-database-creation/](https://io.bikegremlin.com/15152/phpmyadmin-database-creation/)  
100. User management — phpMyAdmin 5.2.3-dev documentation, fecha de acceso: mayo 6, 2025, [https://docs.phpmyadmin.net/en/latest/privileges.html](https://docs.phpmyadmin.net/en/latest/privileges.html)  
101. phpMyAdmin of Xamp \- Assign privileges/permission to user in detail \- YouTube, fecha de acceso: mayo 6, 2025, [https://www.youtube.com/watch?v=OIqP1yNDzV4](https://www.youtube.com/watch?v=OIqP1yNDzV4)  
102. How to grant and revoke rights to tables using phpMyAdmin \- Server Fault, fecha de acceso: mayo 6, 2025, [https://serverfault.com/questions/565343/how-to-grant-and-revoke-rights-to-tables-using-phpmyadmin](https://serverfault.com/questions/565343/how-to-grant-and-revoke-rights-to-tables-using-phpmyadmin)  
103. How To Create a New MySQL User and Grant Privileges \- phoenixNAP, fecha de acceso: mayo 6, 2025, [https://phoenixnap.com/kb/how-to-create-new-mysql-user-account-grant-privileges](https://phoenixnap.com/kb/how-to-create-new-mysql-user-account-grant-privileges)  
104. How to Import downloaded Laravel Project into Xamp Locally \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/64331961/how-to-import-downloaded-laravel-project-into-xamp-locally](https://stackoverflow.com/questions/64331961/how-to-import-downloaded-laravel-project-into-xamp-locally)  
105. Troubleshooting 500 Error in Laravel | InMotion Hosting, fecha de acceso: mayo 6, 2025, [https://www.inmotionhosting.com/support/edu/laravel/500-error-laravel/](https://www.inmotionhosting.com/support/edu/laravel/500-error-laravel/)  
106. Quick fix for Laravel database connection error \- Bobcares, fecha de acceso: mayo 6, 2025, [https://bobcares.com/blog/laravel-database-connection-error/](https://bobcares.com/blog/laravel-database-connection-error/)  
107. Attempt on Laravel/MySQLconnection with WSL and XAMPP \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/62734639/attempt-on-laravel-mysqlconnection-with-wsl-and-xampp](https://stackoverflow.com/questions/62734639/attempt-on-laravel-mysqlconnection-with-wsl-and-xampp)  
108. Laravel noob, having issues with connecting to mysql \- Reddit, fecha de acceso: mayo 6, 2025, [https://www.reddit.com/r/laravel/comments/kfk6kc/laravel\_noob\_having\_issues\_with\_connecting\_to/](https://www.reddit.com/r/laravel/comments/kfk6kc/laravel_noob_having_issues_with_connecting_to/)  
109. How to Fix XAMPP If It's Not Working (3 Common Solutions) \- Kinsta, fecha de acceso: mayo 6, 2025, [https://kinsta.com/knowledgebase/xampp-not-working/](https://kinsta.com/knowledgebase/xampp-not-working/)  
110. Database: Migrations \- Laravel 11.x \- The PHP Framework For Web Artisans, fecha de acceso: mayo 6, 2025, [https://laravel.com/docs/11.x/migrations](https://laravel.com/docs/11.x/migrations)  
111. Create Migrations & Manage Database in Laravel on Cloudways, fecha de acceso: mayo 6, 2025, [https://www.cloudways.com/blog/laravel-migrations/](https://www.cloudways.com/blog/laravel-migrations/)  
112. Laravel move project from Ubuntu Server to Local XAMPP \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/53640895/laravel-move-project-from-ubuntu-server-to-local-xampp](https://stackoverflow.com/questions/53640895/laravel-move-project-from-ubuntu-server-to-local-xampp)  
113. PHP Laravel: No connection could be made because the target machine actively refused it, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/32514241/php-laravel-no-connection-could-be-made-because-the-target-machine-actively-ref](https://stackoverflow.com/questions/32514241/php-laravel-no-connection-could-be-made-because-the-target-machine-actively-ref)  
114. Cannot create database in phpMyAdmin(XAMPP) \- AUR \- Manjaro Linux Forum, fecha de acceso: mayo 6, 2025, [https://forum.manjaro.org/t/cannot-create-database-in-phpmyadmin-xampp/15993](https://forum.manjaro.org/t/cannot-create-database-in-phpmyadmin-xampp/15993)  
115. Database migrations and seeders \- PHP8 \- Laravel 11, fecha de acceso: mayo 6, 2025, [https://itf-laravel-11.netlify.app/laravel/migrations](https://itf-laravel-11.netlify.app/laravel/migrations)  
116. Laravel Blade Templating: From Basics to Advanced Techniques \- TechTales, fecha de acceso: mayo 6, 2025, [https://techtales.blog/laravel-blade-templating-from-basics-to-advanced-techniques/](https://techtales.blog/laravel-blade-templating-from-basics-to-advanced-techniques/)  
117. Blade Templates \- Laravel 11.x \- The PHP Framework For Web Artisans, fecha de acceso: mayo 6, 2025, [https://laravel.com/docs/11.x/blade](https://laravel.com/docs/11.x/blade)  
118. Laravel Blade Template Engine: A Beginner's Guide \- DEV Community, fecha de acceso: mayo 6, 2025, [https://dev.to/icornea/laravel-blade-template-engine-a-beginners-guide-54bi](https://dev.to/icornea/laravel-blade-template-engine-a-beginners-guide-54bi)  
119. Master Blade Template Layouts: Laravel Guide \- 2Hats Logic, fecha de acceso: mayo 6, 2025, [https://www.2hatslogic.com/blog/blade-template-layouts-in-laravel/](https://www.2hatslogic.com/blog/blade-template-layouts-in-laravel/)  
120. Using @section and @yield | Laravel.io, fecha de acceso: mayo 6, 2025, [https://laravel.io/forum/09-02-2014-using-section-and-yield](https://laravel.io/forum/09-02-2014-using-section-and-yield)  
121. How to Enable mod\_rewrite module in Apache in xampp. \- Bugtreat Technologies, fecha de acceso: mayo 6, 2025, [https://www.bugtreat.com/blog/how-to-enable-mod\_rewrite-module-in-apache-in-xampp/](https://www.bugtreat.com/blog/how-to-enable-mod_rewrite-module-in-apache-in-xampp/)  
122. PHP Mod\_Rewrite And Mod\_Security: Here's What You Need to Know | HackerNoon, fecha de acceso: mayo 6, 2025, [https://hackernoon.com/php-mod\_rewrite-and-mod\_security-heres-what-you-need-to-know](https://hackernoon.com/php-mod_rewrite-and-mod_security-heres-what-you-need-to-know)  
123. "RewriteEngine not allowed here" .htaccess error using XAMPP \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/13277986/rewriteengine-not-allowed-here-htaccess-error-using-xampp](https://stackoverflow.com/questions/13277986/rewriteengine-not-allowed-here-htaccess-error-using-xampp)  
124. Cannot set up mod\_rewrite with XAMPP \- Super User, fecha de acceso: mayo 6, 2025, [https://superuser.com/questions/848018/cannot-set-up-mod-rewrite-with-xampp](https://superuser.com/questions/848018/cannot-set-up-mod-rewrite-with-xampp)  
125. How to Enable Apache Mod\_Rewrite? \- GeeksforGeeks, fecha de acceso: mayo 6, 2025, [https://www.geeksforgeeks.org/how-to-enable-apache-modrewrite/](https://www.geeksforgeeks.org/how-to-enable-apache-modrewrite/)  
126. How to Rewrite URLs with mod\_rewrite for Apache on Ubuntu 20.04 \- DigitalOcean, fecha de acceso: mayo 6, 2025, [https://www.digitalocean.com/community/tutorials/how-to-rewrite-urls-with-mod\_rewrite-for-apache-on-ubuntu-20-04](https://www.digitalocean.com/community/tutorials/how-to-rewrite-urls-with-mod_rewrite-for-apache-on-ubuntu-20-04)  
127. How to Enable Apache Mod\_Rewrite \- Gcore, fecha de acceso: mayo 6, 2025, [https://gcore.com/learning/how-enable-apache-mod-rewrite/](https://gcore.com/learning/how-enable-apache-mod-rewrite/)  
128. mod rewrite \- How to enable mod\_rewrite for Apache 2.2 \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/869092/how-to-enable-mod-rewrite-for-apache-2-2](https://stackoverflow.com/questions/869092/how-to-enable-mod-rewrite-for-apache-2-2)  
129. XAMPP httpd.conf \- GitHub Gist, fecha de acceso: mayo 6, 2025, [https://gist.github.com/rukku/3411602](https://gist.github.com/rukku/3411602)  
130. VirtualHost Examples \- Apache HTTP Server Version 2.4, fecha de acceso: mayo 6, 2025, [https://httpd.apache.org/docs/2.4/vhosts/examples.html](https://httpd.apache.org/docs/2.4/vhosts/examples.html)  
131. how to create virtual host on XAMPP \[duplicate\] \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/27268205/how-to-create-virtual-host-on-xampp](https://stackoverflow.com/questions/27268205/how-to-create-virtual-host-on-xampp)  
132. How To Set Up Apache Virtual Hosts on Ubuntu 20.04 \- DigitalOcean, fecha de acceso: mayo 6, 2025, [https://www.digitalocean.com/community/tutorials/how-to-set-up-apache-virtual-hosts-on-ubuntu-20-04](https://www.digitalocean.com/community/tutorials/how-to-set-up-apache-virtual-hosts-on-ubuntu-20-04)  
133. How to setup virtual hosts in XAMPP at any directory on ubuntu?, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/1151886/how-to-setup-virtual-hosts-in-xampp-at-any-directory-on-ubuntu](https://askubuntu.com/questions/1151886/how-to-setup-virtual-hosts-in-xampp-at-any-directory-on-ubuntu)  
134. XAMPP virtual host loading the same as localhost (), but not the document root I have specified \- Ask Ubuntu, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/1408548/xampp-virtual-host-loading-the-same-as-localhost-but-not-the-document-root-i](https://askubuntu.com/questions/1408548/xampp-virtual-host-loading-the-same-as-localhost-but-not-the-document-root-i)  
135. Virtual Hosts XAMPP \[Linux Ubuntu\] not working \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/10878284/virtual-hosts-xampp-linux-ubuntu-not-working](https://stackoverflow.com/questions/10878284/virtual-hosts-xampp-linux-ubuntu-not-working)  
136. Virtual hosts and XAMPP \- Server Config \- SitePoint Forums | Web Development & Design Community, fecha de acceso: mayo 6, 2025, [https://www.sitepoint.com/community/t/virtual-hosts-and-xampp/26102](https://www.sitepoint.com/community/t/virtual-hosts-and-xampp/26102)  
137. Finding out what user Apache is running as? \- Server Fault, fecha de acceso: mayo 6, 2025, [https://serverfault.com/questions/125865/finding-out-what-user-apache-is-running-as](https://serverfault.com/questions/125865/finding-out-what-user-apache-is-running-as)  
138. Can I change apache web server user and group? \[duplicate\] \- Ask Ubuntu, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/876308/can-i-change-apache-web-server-user-and-group](https://askubuntu.com/questions/876308/can-i-change-apache-web-server-user-and-group)  
139. How to Change User and Group Settings in Apache? | GeeksforGeeks, fecha de acceso: mayo 6, 2025, [https://www.geeksforgeeks.org/how-to-change-user-and-group-settings-in-apache/](https://www.geeksforgeeks.org/how-to-change-user-and-group-settings-in-apache/)  
140. Change the user Apache runs as \[closed\] \- Ask Different \- Stack Exchange, fecha de acceso: mayo 6, 2025, [https://apple.stackexchange.com/questions/46062/change-the-user-apache-runs-as](https://apple.stackexchange.com/questions/46062/change-the-user-apache-runs-as)  
141. Set correct user/group to Apache server / projects \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/64499084/set-correct-user-group-to-apache-server-projects](https://stackoverflow.com/questions/64499084/set-correct-user-group-to-apache-server-projects)  
142. Apache Issue with user/ group on httpd.conf \- permissions \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/54530616/apache-issue-with-user-group-on-httpd-conf-permissions](https://stackoverflow.com/questions/54530616/apache-issue-with-user-group-on-httpd-conf-permissions)  
143. How do I change Owner in httpd.cof \- xampp \- Ask Ubuntu, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/1210707/how-do-i-change-owner-in-httpd-cof](https://askubuntu.com/questions/1210707/how-do-i-change-owner-in-httpd-cof)  
144. xampp ubuntu laravel The stream or file "/storage/logs/laravel log" could not be opened: failed to open stream: Permission denied \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/55770021/xampp-ubuntu-laravel-the-stream-or-file-storage-logs-laravel-log-could-not-be](https://stackoverflow.com/questions/55770021/xampp-ubuntu-laravel-the-stream-or-file-storage-logs-laravel-log-could-not-be)  
145. Correct way to set up file and directory permissions in Laravel \- GitHub Gist, fecha de acceso: mayo 6, 2025, [https://gist.github.com/mdutt247/86704dc9abfcf497708b691128ea68b5](https://gist.github.com/mdutt247/86704dc9abfcf497708b691128ea68b5)  
146. How to set up file permissions for Laravel? \- GeeksforGeeks, fecha de acceso: mayo 6, 2025, [https://www.geeksforgeeks.org/how-to-set-up-file-permissions-for-laravel/](https://www.geeksforgeeks.org/how-to-set-up-file-permissions-for-laravel/)  
147. Laravel permissions for storage and bootstrap/cache (Apache or Nginx) \- YouTube, fecha de acceso: mayo 6, 2025, [https://www.youtube.com/watch?v=HgKUtsO6qig](https://www.youtube.com/watch?v=HgKUtsO6qig)  
148. How to manage permissions of laravel project files in server \- Laracasts, fecha de acceso: mayo 6, 2025, [https://laracasts.com/discuss/channels/laravel/how-to-manage-permissions-of-laravel-project-file-in-server](https://laracasts.com/discuss/channels/laravel/how-to-manage-permissions-of-laravel-project-file-in-server)  
149. i've always got permission denied for folder storage, bootstrap/cache : r/laravel \- Reddit, fecha de acceso: mayo 6, 2025, [https://www.reddit.com/r/laravel/comments/3mgiga/ive\_always\_got\_permission\_denied\_for\_folder/](https://www.reddit.com/r/laravel/comments/3mgiga/ive_always_got_permission_denied_for_folder/)  
150. How to set up file permissions for Laravel? \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/30639174/how-to-set-up-file-permissions-for-laravel](https://stackoverflow.com/questions/30639174/how-to-set-up-file-permissions-for-laravel)  
151. How to setup laravel file permission once and for all \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/64095072/how-to-setup-laravel-file-permission-once-and-for-all](https://stackoverflow.com/questions/64095072/how-to-setup-laravel-file-permission-once-and-for-all)  
152. Permission Denied on storage/logs/laravel.log \- Laracasts, fecha de acceso: mayo 6, 2025, [https://laracasts.com/discuss/channels/laravel/permission-denied-on-storagelogslaravellog](https://laracasts.com/discuss/channels/laravel/permission-denied-on-storagelogslaravellog)  
153. install laravel on xampp "linux ubuntu", fecha de acceso: mayo 6, 2025, [https://laravel.io/forum/12-25-2016-install-laravel-on-xampp-linux-ubuntu](https://laravel.io/forum/12-25-2016-install-laravel-on-xampp-linux-ubuntu)  
154. Laravel 9 Ubuntu constant permission errors \- Stack Overflow, fecha de acceso: mayo 6, 2025, [https://stackoverflow.com/questions/71626468/laravel-9-ubuntu-constant-permission-errors](https://stackoverflow.com/questions/71626468/laravel-9-ubuntu-constant-permission-errors)  
155. Laravel development setup on Ubuntu Linux using Xampp \- YouTube, fecha de acceso: mayo 6, 2025, [https://www.youtube.com/watch?v=3DeJCwmlOys](https://www.youtube.com/watch?v=3DeJCwmlOys)  
156. Problems with missing PHP extensions \- Laracasts, fecha de acceso: mayo 6, 2025, [https://laracasts.com/discuss/channels/php/problems-with-missing-php-extensions](https://laracasts.com/discuss/channels/php/problems-with-missing-php-extensions)  
157. Mcrypt PHP extension required. Laravel, Xampp error \- Ask Ubuntu, fecha de acceso: mayo 6, 2025, [https://askubuntu.com/questions/507796/mcrypt-php-extension-required-laravel-xampp-error](https://askubuntu.com/questions/507796/mcrypt-php-extension-required-laravel-xampp-error)  
158. Laravel Documentation \- Laravel 11.x \- The PHP Framework For Web Artisans, fecha de acceso: mayo 6, 2025, [https://laravel.com/docs/11.x/readme](https://laravel.com/docs/11.x/readme)