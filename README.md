# Servicios en Red - Práctica 6: Servidores Web
## Recordatorio de Teoría
En una aplicación/página web, el **cliente** es el navegador web (como Chrome, Firefox, etc.) que realiza solicitudes HTTP para obtener recursos (páginas HTML, imágenes, scripts, etc.) a un **servidor web**. El servidor web es un software que escucha las solicitudes entrantes en un puerto específico (generalmente el puerto 80 para HTTP y el puerto 443 para HTTPS) y responde enviando los recursos solicitados al cliente. 

El **servidor web** solamente atiende las solicitudes HTTP, mientras que el **servidor de aplicaciones** es responsable de ejecutar la lógica de negocio, procesar datos y generar contenido dinámico. 

En algunos casos, el servidor web y el servidor de aplicaciones pueden estar integrados en un solo software (como Node.js), pero son conceptos distintos.

Ademas de esto, en la arquitectura típica de una aplicación web, el servidor web puede interactuar con otros componentes como bases de datos, servicios externos, etc.

## Descripción de la Práctica
En esta práctica nos vamos a crear y desplegar un servidor web básico utilizando primero  **Apache HTTP Server** y luego lo mismo con **Node.js + Express**. Despues nos conectaremos a ambos servidores desde un navegador web en la máquina host.

Apache y Node.js + Express, son junto con Nginx, los servidores web mas utilizados en la actualidad con bastante diferencia con el resto. Node es ademas, utilizado como servidor de aplicaciones en muchas aplicaciones web modernas.

### Preparación del Entorno
Ambas partes de la práctica la haremos en máquinas virtuales Debian. Necesitaremos dos máquinas virtuales separadas, una para cada servidor web. Yo las llamare **VM-Apache** y **VM-Node**.

En ambas máquinas virtuales deberemos:
- No tocar el adaptador 1 (NAT) para tener acceso a internet.
- Configurar el adaptador 2 en modo "Bridge" (Puente) para que ambas máquinas estén en la misma red que la máquina host. De esta forma podremos acceder a los servidores web desde el navegador de la máquina host o desde otras máquinas en la misma red. Este adaptador, al estar en modo puente, se configurará automáticamente a través del servidor DHCP del router.
- Crear un usuario con nuestro nombre y darle permisos de administrador metiendolo en el grupo "sudo".
-  Cambiar el nombre de la máquina virtual a "VM-Apache" o "VM-Node" según corresponda. Utilizando el comando `hostnamectl` o editando el archivo `/etc/hostname` y `/etc/hosts`.
-  Actualizar los repositorios e instalar las actualizaciones disponibles con:
  ```bash
  sudo apt update
  sudo apt upgrade
  ```


### Parte 1: Servidor Web con Apache HTTP Server (VM-Apache)
Primero tenemos que instalar el paquete de Apache HTTP Server en la máquina virtual VM-Apache utilizando el gestor de paquetes `apt`. Este paquete se llama `apache2`. Podemos instalarlo ejecutando el siguiente comando en la terminal:

```bash
sudo apt install apache2
```

Despues de la instalación, el servicio de Apache debería iniciarse automáticamente. Podemos verificar que el servicio está corriendo con el siguiente comando:

```bash
sudo systemctl status apache2
```

Deberíamos ver que el servicio está "active (running)" y "enabled". Si no es así, podemos iniciarlo y habilitarlo para que arranque automáticamente al iniciar el sistema con los siguientes comandos:

```bash
sudo systemctl start apache2
sudo systemctl enable apache2
```

Para comprobar que el servidor web está funcionando correctamente, podemos abrir un navegador web en la máquina host y acceder a la dirección IP de la máquina virtual VM-Apache. Podemos encontrar la dirección IP ejecutando el siguiente comando en la terminal de VM-Apache:

```bash
ip a
```

Luego, en el navegador web de la máquina host, podemos acceder a `http://X.X.X.X`, donde `X.X.X.X` es la dirección IP de VM-Apache. Deberíamos ver la página de bienvenida predeterminada de Apache.

Si queremos servir nuestra propia página web, podemos reemplazar el contenido de la carpeta `/var/www/html` en VM-Apache con un proyecto nuestro. Por ejemplo, podemos crear un archivo `index.html` con el siguiente contenido:

```html
<!DOCTYPE html>
<html>
<head>
    <title>El Servidor de Fabián Servidor Web con Apache</title>
</head>
<body>
    <h1>¡Hola desde Apache HTTP Server!</h1>
    <p>Esta es una página servida por Apache en una máquina virtual Debian.</p>
</body>
</html>
```

(Cambiando "Fabián" por vuestro nombre)

Para cambiar el puerto por defecto que utiliza Apache (80), que es el puerto estándar para HTTP, podemos editar el archivo de configuración `/etc/apache2/ports.conf` y cambiar la línea que dice `Listen 80` por `Listen 8080` (o cualquier otro puerto que queramos utilizar). Después de hacer este cambio, debemos reiniciar el servicio de Apache para que los cambios surtan efecto:

```bash
sudo systemctl restart apache2
```

Hay muchas mas configuraciones que podemos hacer en Apache, pero para esta práctica nos quedamos con esto.

### Parte 2: Servidor Web con Node.js y Express (VM-Node)
Primero, necesitamos instalar Node.js. Node.js es un entorno de ejecución que nos permite ejecutar JavaScript fuera del navegador. Podemos instalar Node.js utilizando el gestor de paquetes `apt` para instalar el paquete `nodejs`. Podemos hacerlo ejecutando el siguiente comando en la terminal:

```bash
sudo apt install nodejs
```

A parte de Node.js, también necesitaremos instalar npm y Express. npm es el gestor de paquetes de Node.js que nos permite instalar librerías y frameworks de Node.js. Express es un framework web para Node.js que nos facilita la creación de servidores web. Podemos instalar npm ejecutando el siguiente comando:

Nota: un framework viene a ser una librería. (Conjunto de herramientas y funcionalidades listas para usar)z

```bash
sudo apt install npm
```


Ahora necesitaremos un proyecto de ejemplo para nuestro servidor web con Node.js y Express. Una vez dentro del directorio del proyecto, podemos inicializar un nuevo proyecto de Node.js ejecutando el siguiente comando:

```bash
npm init
```

Despues, podemos instalar Express ejecutando el siguiente comando:

```bash
npm install express
```

Por defecto, npm instala los paquetes solamente para el proyecto actual, guardandolos en la carpeta `node_modules` dentro del directorio del proyecto. Nuestro proyecto debería tener una estructura similar a la siguiente:

Todo el contenido del proyecto debería estar en una carpeta llamada `public`, que contendrá los archivos que queremos servir (HTML, CSS, JS, imágenes, etc.) al cliente (navegador). La estructura del proyecto debería ser similar a esta:

```
mi-proyecto-node/
├── node_modules/
├── public/
│   ├── index.html
│   └── (otros archivos accesibles públicamente)
├── package.json
└── server.js
```

En la carpeta `public`, podemos crear un archivo `index.html` con el siguiente contenido:

```html
<!DOCTYPE html>
<html>
<head>
    <title>El Servidor de Fabián Servidor Web con Node.js y Express</title>
</head>
<body>
    <h1>¡Hola desde Node.js y Express!</h1>
    <p>Esta es una página servida por un servidor web creado con Node.js y
    Express en una máquina virtual Debian.</p>
</body>
</html>
```

(Cambiando "Fabián" por vuestro nombre)

Ahora podemos crear un archivo llamado `server.js` en el directorio del proyecto con el siguiente contenido:

```javascript
const express = require('express'); // Importamos el framework Express
const app = express();              // Creamos una instancia de la aplicación Express
app.use(express.static('public'));  // Servimos archivos estáticos desde la carpeta 'public'
// Hacemos que el servidor escuche en el puerto 8080 y que imprima un mensaje en la consola cuando esté listo
app.listen(8080, () => {
  console.log("Servidor web escuchando en http://localhost:8080");
});
```

Este código crea un servidor web que escucha en el puerto 8080 y sirve archivos estáticos desde la carpeta `public`.

Para iniciar el servidor web, podemos ejecutar el siguiente comando en la terminal dentro del directorio del proyecto:

```bash
node server.js
```

Ahora podemos abrir un navegador web en la máquina host y acceder a la dirección IP de la máquina virtual VM-Node seguida del puerto 8080. Por ejemplo, si la dirección IP de VM-Node es `X.X.X.X`, deberíamos acceder a `http://X.X.X.X:8080` para ver la página servida por nuestro servidor web con Node.js y Express.


Nota: No os preocupéis por entender a fondo el código de Node.js y Express, ya que eso lo veremos en la siguiente práctica de Aplicaciones en Web.

Express tiene muchas mas funcionalidades y configuraciones que podemos hacer, pero para esta práctica nos quedamos con esto.

### Entrega
Un pdf con las siguientes capturas:

- Página de bienvenida de Apache abierta desde el navegador de la máquina host
- Página servida por Apache abierta desde el navegador de la máquina host
- ports.conf de Apache
- Página servida por Node.js y Express abierta desde el navegador de la máquina host
- server.js de Node.js

Debereis desplegar la página de Apache en el puerto 9000 y la de Node.js en el puerto 10000.