# Ejercicios de Configuración de Servidor Web Nginx y FTP

Este repositorio contiene la documentación de los pasos realizados para configurar un servidor web Nginx, crear un sitio web llamado `web`, configurar un servidor FTP seguro (FTPS) y otros ajustes relacionados en una máquina Debian. Cada paso incluye las acciones realizadas y los comandos utilizados.

## 1. Instalación de Servidor Web Nginx

Primero, instalamos el servidor web Nginx en una máquina Debian con los siguientes pasos:

1. **Actualización de los repositorios:**

    ```bash
    sudo apt update
    ```

2. **Instalación del paquete Nginx:**

    ```bash
    sudo apt install nginx
    ```

3. **Comprobación de que Nginx está funcionando correctamente:**

    ```bash
    systemctl status nginx
    ```

    Al realizar esta acción, Nginx se instala y se inicia correctamente, y la página de inicio predeterminada de Nginx es accesible.

## 2. Creación de la Carpeta del Sitio Web

1. **Creación de la carpeta del sitio web en el directorio adecuado:**

    ```bash
    sudo mkdir -p /var/www/web/html
    ```

2. **Clonación del repositorio de ejemplo:**

    Dentro de la carpeta `html`, se clona el siguiente repositorio:

    ```bash
    git clone https://github.com/cloudacademy/static-website-example
    ```

3. **Cambio de propiedad de los archivos a `www-data` y asignación de permisos:**

    ```bash
    sudo chown -R www-data:www-data /var/www/web/html
    sudo chmod -R 755 /var/www/web
    ```

    Esto asegura que el servidor web pueda acceder y servir los archivos correctamente.

4. **Verificación:**

    Se puede acceder a la IP de la máquina virtual a través de un navegador:

    ```bash
    http://192.168.57.102
    ```

    Al acceder, se debe mostrar la página predeterminada de Nginx, indicando que todo funciona correctamente.

## 3. Configuración del Servidor Web Nginx

Para configurar Nginx para que sirva el contenido de nuestro sitio web:

1. **Creación de un archivo de configuración para el sitio web:**

    ```bash
    sudo nano /etc/nginx/sites-available/web
    ```

    El archivo debe tener el siguiente contenido:

    ```nginx
    server {
      listen 80;
      listen [::]:80;
      root /var/www/web/html;
      index index.html index.htm index.nginx-debian.html;
      server_name web;
      location / {
        try_files $uri $uri/ =404;
      }
    }
    ```

2. **Creación de un enlace simbólico entre `sites-available` y `sites-enabled`:**

    ```bash
    sudo ln -s /etc/nginx/sites-available/web /etc/nginx/sites-enabled/
    ```

3. **Reinicio de Nginx para aplicar la configuración:**

    ```bash
    sudo systemctl restart nginx
    ```

## 3.1. Comprobaciones

1. **Modificación del archivo `/etc/hosts` para asociar el nombre del dominio con la IP del servidor:**

    Edité el archivo `/etc/hosts` en la máquina anfitriona y añadí la siguiente línea:

    ```bash
    192.168.57.102 web
    ```

    Esto permite que el nombre de dominio `web` sea reconocido y redirigido a la IP del servidor.

2. **Comprobación de los registros del servidor:**

    Los registros de acceso y errores se pueden revisar en los archivos:

    - **Registros de acceso:**

      ```bash
      /var/log/nginx/access.log
      ```

    - **Registros de errores:**

      ```bash
      /var/log/nginx/error.log
      ```

    Estos registros permiten comprobar que las peticiones se están registrando correctamente.

## 4. Configuración de FTP

1. **Instalación de VSFTPD para habilitar FTP seguro (FTPS):**

    ```bash
    sudo apt-get update
    sudo apt-get install vsftpd
    ```

2. **Creación de un directorio para FTP:**

    ```bash
    mkdir /home/tu_usuario/ftp
    ```

3. **Generación de certificados SSL para FTPS:**

    ```bash
    sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt
    ```

4. **Configuración de VSFTPD:**

    Edité el archivo de configuración de VSFTPD:

    ```bash
    sudo nano /etc/vsftpd.conf
    ```

    Se modificaron las siguientes líneas:

    ```bash
    rsa_cert_file=/etc/ssl/certs/vsftpd.crt
    rsa_private_key_file=/etc/ssl/private/vsftpd.key
    ssl_enable=YES
    allow_anon_ssl=NO
    force_local_data_ssl=YES
    force_local_logins_ssl=YES
    ssl_tlsv1=YES
    ssl_sslv2=NO
    ssl_sslv3=NO
    require_ssl_reuse=NO
    ssl_ciphers=HIGH
    local_root=/home/tu_usuario/ftp
    ```

5. **Reinicio de VSFTPD para aplicar la nueva configuración:**

    ```bash
    sudo systemctl restart vsftpd
    ```

6. **Conexión FTPES:**

    Se configuraron dos tipos de conexión:
    - **FTP sin cifrar (puerto 21)**.
    - **FTPS (FTP sobre SSL explícito)**.

## 5. Tarea Final

1. **Creación de un nuevo dominio para un sitio web propio:**

    Repití el proceso anterior para configurar un nuevo dominio con un sitio web desarrollado por mí. Transferí los archivos al servidor utilizando FTPS, siguiendo los pasos mencionados anteriormente.

2. **Configuración de permisos:**

    Asigné los permisos adecuados a la carpeta del nuevo sitio web de la misma manera que se hizo en el paso 2.

## 6. Cuestiones Finales

### ¿Qué pasa si no hago el link simbólico entre `sites-available` y `sites-enabled` de mi sitio web?

Si no se hace este enlace simbólico, el servidor Nginx no reconocerá la configuración de tu sitio web y no podrá servirlo.

### ¿Qué pasa si no le doy los permisos adecuados a `/var/www/web`?

Si no se asignan los permisos adecuados, Nginx no podrá acceder a los archivos del sitio web y devolverá un error de acceso denegado al intentar cargar la página.

---

**Fin del README**
