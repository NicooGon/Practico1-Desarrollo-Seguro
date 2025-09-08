# Desarrollo de Software Seguro - Practico 1

### Ignacio Rodriguez, Nicolas Gonzalez, Agustin Garcia

## Instalacion VM

- Como primer paso de la instalacion buscamos la maquina virtual a instalar, en este caso [VirtualBox](https://www.oracle.com/es/virtualization/technologies/vm/downloads/virtualbox-downloads.html?source=:ow:o:p:nav:mmddyyVirtualBoxHero_es&intcmp=:ow:o:p:nav:mmddyyVirtualBoxHero_es).

- Luego de haber instalado VirtualBox, lo ejecutamos y creamos una nueva maquina virtual poniendole un nombre, ademas de especificar la Memoria RAM y CPU que utilizara la misma. Tambien especificamos que el Sistema operativo sea Debian ya que Kali esta basado en el.

- Para poder crear la maquina virtual nos pedira una imagen .iso, la cual vamos a descargar de [Kali](https://www.kali.org/get-kali/#kali-installer-images).

- Una vez ejecutada la maquina virtual con la imagen de kali, nos pedira algunas configuraciones del usuario que vamos a tener creado en nuestro ambiente, es importante acordarnos de el nombre de usuario y password, luego de estas configuraciones la VM se reiniciara y mostrara el inicio de Kali que se ve asi.
  ![Image](https://i.imgur.com/d1wrKG3.png)

## Instalacion de Proxy

- En este caso, Kali linux ya trae integrado Burp Suite.

## Instalacion VSCode

- Ejecutamos comando para actualizar paquetes
<pre><code>sudo apt update && sudo apt upgrade -y</code></pre>

- Seguimos las instrucciones copiando los scripts de la pagina oficial de [VSCode for Linux](https://code.visualstudio.com/docs/setup/linux#_install-vs-code-on-linux)
  <pre><code>sudo apt install wget gpg apt-transport-https software-properties-common -y</code></pre>
  <pre><code>wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg</code></pre>
  <pre><code>sudo install -o root -g root -m 644 packages.microsoft.gpg /usr/share/keyrings/</code></pre>
- Para no tener que crear el archivo con las referencias manualmente ejecutamos
  <pre><code>sudo sh -c 'echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'</code></pre>
  <pre><code>rm -f packages.microsoft.gpg</code></pre>
- Finalmente actualizamos paquetes e instalamos VSCode
  <pre><code>sudo apt update</code></pre>
  <pre><code>sudo apt install code</code></pre>

## Instalacion Docker

- Para instalar Docker podemos descargar Docker Desktop o Docker engine, en este caso utilizamos Docker Engine para familiarizarnos mas con los comandos, ademas que tiene un mayor rendimiento y no consume tantos recursos.

- Ejecutamos los scripts de la pagina oficial de [docker para linux debian](https://docs.docker.com/engine/install/debian/), donde lo instalaremos desde el repositorio apt.
- Agregamos la llave GPG de docker descargada con el comando curl a la carpeta keyrings que vamos a crear.
    <pre><code>sudo apt install -y ca-certificates curl gnupg lsb-release</code></pre>
    <pre><code>sudo install -m 0755 -d /etc/apt/keyrings</code></pre>
    <pre><code>sudo curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg</code></pre>
    <pre><code>echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian bookworm stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null</code></pre>
- Actualizamos paquetes e instalamos ahora si docker del repositorio correcto verificado por la clave docker gpg que descargamos para kali antes
    <pre><code>sudo apt update</code></pre>
    <pre><code>sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin</code></pre>
- Para no tener que utilizar sudo siempre cada vez que queramos utilizar el comando docker agregamos el usuario al grupo docker
    <pre><code>sudo usermod -aG docker $USER</code></pre>
- Verificamos que tengamos docker correctamente
    <pre><code>docker --version</code></pre>

## Ejecucion de OWASP Juice Shop en Docker

- Para correr OWASP Juice Shop en Docker tenemos que hacer un pull a la imagen oficial la cual se encuentra en el [GitHub de Juice Shop](https://github.com/juice-shop/juice-shop)
    <pre><code>docker pull bkimminich/juice-shop</code></pre>
- Luego de tener la imagen la corremos especificando el puerto 3000 para que se cree un contenedor con la misma
    <pre><code>docker run -d -p 3000:3000 bkimminich/juice-shop:latest</code></pre>

- Abrimos localhost:3000 y ya deberia verse la pagina.

## Ejecucion de Crapi en Docker

Para correr Crapi en Docker tenemos que ejecutar los comandos de script especificados en el [GitHub de Crapi](https://github.com/OWASP/crAPI)

- Utilizamos wget para descargar ya que curl no funciono, luego descomprimimos el archivo descargado.
    <pre><code>wget -O /tmp/crapi.zip https://github.com/OWASP/crAPI/archive/refs/heads/main.zip</code></pre>
    <pre><code>unzip /tmp/crapi.zip</code></pre>

- Nos movemos a donde se encuentra el docker-compose.yaml con los contenedores a correr y hacemos el pull.
    <pre><code>cd crAPI-main/deploy/docker</code></pre>
    <pre><code>docker compose pull</code></pre>

- Ejecutamos el docker compose
    <pre><code>docker compose -f docker-compose.yml --compatibility up -d</code></pre>

- Utilizamos el comando docker ps para ver los contenedores corriendo, tambien ingresamos a localhost:8888 y localhost:8025 para acceder a los servicios de crapi.
  ![Crapi](https://i.imgur.com/4d2gvdz.png)

## Visualizacion del trafico en Burp Suite

- Para poder visualizar el trafico en Burp tenemos que ir a Proxy y Intercept, activamos el Intercept y abrimos el browser de Burp con el cual vamos a acceder a localhost:3000 y localhost:8888. De esta forma podemos interceptar las requests.

- Luego de Interceptar todas las request y ver como son podemos apagar el Intercept y entrar al HTTP History para ver las respuestas del servidor a esas requests.

![Intercept](https://i.imgur.com/m666yPW.png)
