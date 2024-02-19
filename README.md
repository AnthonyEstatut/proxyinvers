# Práctica Guiada: Configuración de un Proxy Inverso con Apache
### Objetivos:
Aprender a configurar Apache como un proxy inverso.
Configurar un servidor web backend básico para trabajar con el proxy inverso.
### Requisitos Previos:
Un servidor Linux con acceso a Internet.
Privilegios de superusuario (root) o capacidad para utilizar sudo.
Apache instalado en el servidor.


## Parte 1: Configuración del Servidor Web Backend
### Instalar Apache (si aún no está instalado):

```sh
sudo apt update
sudo apt install apache2
```
### Crear y configurar un nuevo Virtual Host para el servidor backend:

a. Crea un nuevo archivo de configuración para tu sitio backend en Apache:

 ```sh
 sudo nano /etc/apache2/sites-available/backend.conf
 ```
b. Añade la siguiente configuración al archivo. Asegúrate de reemplazar /var/www/html y el ServerName con la ruta a tu sitio web si es diferente:

 ```apache
 <VirtualHost *:8080>
     ServerName backend.com
     ServerAdmin webmaster@localhost
     DocumentRoot /var/www/html

     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined
 </VirtualHost>
 ```
c. Guarda y cierra el archivo.

d. Habilita el nuevo sitio:

 ```bash
 sudo a2ensite backend.conf
 ```
e. Asegúrate de que Apache esté configurado para escuchar en el puerto 8080, editando el archivo ports.conf:

 ```bash
 sudo nano /etc/apache2/ports.conf
 ```

 Y añade si es necesario:

 ```apache
 Listen 8080
 ```
f. Reinicia Apache para aplicar los cambios:

 ```bash
 sudo systemctl restart apache2
 ```
### Verificar la configuración del servidor backend:

Accede a http://tu_direccion_ip:8080 desde un navegador para asegurarte de que el servidor backend está funcionando correctamente.

## Parte 2: Configuración del Proxy Inverso con Apache
Habilitar los módulos necesarios para el proxy inverso:
 ```sh
sudo a2enmod proxy
sudo a2enmod proxy_http
 ```
### Configurar Apache para actuar como un proxy inverso:

Edita un nuevo archivo de configuración de Apache:
 ```sh
sudo nano /etc/apache2/sites-available/proxyinvers.conf
 ```
Y añade las siguientes líneas dentro del bloque <VirtualHost *:80>

 ```apache
<VirtualHost *:80>
    ServerName proxyinvers.com
    ProxyPreserveHost On
    ProxyPass / http://localhost:8080/
    ProxyPassReverse / http://localhost:8080/
</VirtualHost>
 ```
 Habilita el nuevo sitio:

 ```bash
 sudo a2ensite proxyinvers.conf
 ```
Reiniciar Apache:
 ```sh
sudo systemctl restart apache2
 ```

### Verificar la configuración del proxy inverso:

Accede a tu servidor Apache mediante su dirección IP o dominio. Deberías ver el contenido servido por el servidor backend configurado en el paso 1, pero accediendo a través del puerto 80, el puerto predeterminado de Apache.

