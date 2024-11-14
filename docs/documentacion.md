# Documentación examen Apache

---

## Parte 1 Implantación de una web estática con Apache

### Instalamos apache

`sudo apt update`
`sudo apt install apache2 -y`

#### Creamos los directorios para los sitios

`sudo mkdir -p /var/www/html/pepe.com`
`sudo mkdir -p /var/www/html/tono.com`

#### Borramos el index.html por defecto

`cd /var/www/html`
`sudo rm -rf index.html`
`sudo systemctl restart apache2.service`

#### Borramos el 000.conf por defecto

`cd /etc/apache2/sites-enabled`
`sudo a2dissite 000-default.conf`
`sudo systemctl reload apache2.service` 

#### Creamos el index.html para el sitio-1

`sudo nano /var/www/html/pepe.com/index.html`

```html
<html>
    <body>
        <h1>Pagina-apache-estatico</h1>
        <h2>Dominio pepe.com</h2>
    </body>
</html>
```

`sudo systemctl restart apache2.service`

#### Configuramos el sitio apache para el sitio-1

`cd /etc/apache2/sites-available/`
`sudo nano pepe.conf`

```html
<VirtualHost *:3380>
    ServerName www.pepe.com
    ServerAlias pepe.com
    ServerAdmin webmaster@pepe.com
    DocumentRoot /var/www/html/pepe.com
    DirectoryIndex index.html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

#### Comprobamos la sintaxis

`sudo apachectl configtest`
`sudo a2ensite pepe.conf`
`sudo systemctl reload apache2.service`

#### Creamos el index.html para el sitio-2

`sudo nano /var/www/html/pepe.com/index.html`
`sudo cp /home/yo/Escritorio/Comparte/site /var/www/html/tono.com`
`sudo systemctl restart apache2.service`

#### Configuramos el sitio apache para el sitio-2

`cd /etc/apache2/sites-available/`
`sudo nano tono.conf`

```html
<VirtualHost *:4480>
    ServerName www.tono.com
    ServerAlias tono.com
    ServerAdmin webmaster@tono.com
    DocumentRoot /var/www/html/tono.com/site/
    DirectoryIndex index.html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

#### Comprobamos-sintaxis

`sudo apachectl configtest`
`sudo a2ensite tono.conf`
`sudo systemctl reload apache2.service`

#### Configuramos apache para escuchar por los puertos `3380` para el primer sitio y `4480` para el segundo sitio

`sudo nano /etc/apache2/ports.conf`

```bash
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 80
Listen 3380
Listen 4480

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>
```

`sudo systemctl restart apache2.service`

#### Agregamos estas líneas en el archivo /etc/hosts para las pruebas

`sudo nano /etc/hosts`

```bash
127.0.0.1 localhost
127.0.1.1 yo-Virtual
127.0.0.1 www.pepe.com
127.0.0.1 www.tono.com
```

#### Asignar los permisos correctos para el usuario www-data

`sudo chown -R www-data:www-data /var/www/html/pepe.com`
`sudo chown -R www-data:www-data /var/www/html/tono.com`
`sudo chmod 755 /var/www/html/tono.com`
`sudo chmod 755 /var/www/html/pepe.com`
`sudo systemctl restart apache2.service`

#### Permisos adicionales

Al estar fuera del directorio `/var/www` en mi caso debo ajustar los permisos para que apache pueda acceder a ellos, por lo que daré además permisos a los directorios que apache necesita atravesar, por que sino me da error de permisos:

`sudo chmod o+x ~`
`sudo chmod -R 755 ~/Directorio-site1 fuera de /var/www`
`sudo chmod -R 755 ~/Directorio-site2 fuera de /var/www`
`sudo systemctl restart apache2.service`

Para el primer sitio: <http://www.pepe.com:3380>
Para el segundo sitio: <http://www.tono.com:4480>
