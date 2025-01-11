# Union Local-Dev

Entorno de desarrollo local para los módulos de sindicato.

Para utilizar este enterno necesitamos tener instalados ***Docker*** y ***Git***. Para instalarlos en *Ubuntu* o *Debian* se puede seguir [este](#instalación) instructivo.

## Uso

Para clonar el repositorio y los submódulos:

```bash
git clone --recurse-submodules git@github.com:AraCba/soltec-localdev-union.git
cd soltec-localdev-union
```

Para actualizar los submódulos a su última versión

```bash
git submodule update --remote
```

### Docker Compose

En el archivo [`docker-compose.yml`](/docker-compose.yml) están configurados para correr en un mismo contenedor los siguientes servicios:

- *Odoo* del programa SolTec.
- Base de datos *Postgres*.
- Visualizador de bases de datos *Pgadmin4*

Para crear la imagen del entorno de desarrollo ejecutamos:

```bash
docker-compose build --no-cache
```

Para levantarlo localmente:

```bash
docker-compose up -d
```

Luego en un navegador ingresamos a [localhost:8069](http://localhost:8069).

## Agregar módulos

Para agregar módulos de *Odoo* a la imagen basta con copiarlos en el directorio [**`custom-addons/`**](/custom-addons/) y luego generar nuevamente la imagen.

Una vez copiados los módulos, nos aseguramos que no esté comentada la linea 12  en [`docker-compose.yml`](/docker-compose.yml), que agrega el contenido de custom-addons a la imagen:

```yml
    volumes:
      - ./custom-addons:/mnt/extra-addons # ESTA LINEA
      - odoo-web-data:/var/lib/odoo
```

Luego generamos nuevamente la imagen:

```bash
docker-compose build --no-cache
```

### Submodulos de *Git*

Para un mejor control de versiones, se pueden integrar los módulos de *Odoo* directamente desde su repositorio agregandolos como submódulos de *Git*.

1. Agregamos los repositorios como submódulos:

```bash
cd submodules
git submodule add <link-al-repositorio>
cd ..
```

2. Ejecutamos el script para copiar el contenido necesario de los módulos al directorio [**`custom-addons/`**](/custom-addons/):

```bash
bash copy_addons.sh
```

3. Por último creamos nuevamente la imagen para finalmente integrar los módulos:

```bash
docker-compose build --no-cache
```

## Conexión de la base de datos con *Pgadmin4*

Para un mejor manejo y visualización de las bases de datos se incluye *Pgadmin4*, para utilizarlo seguimos los siguientes pasos:

1. Con la imagen corriendo nos dirigimos a [localhost:5050](http://localhost:5050) para abrir la interfaz gráfica de *Pgadmin4* y nos logueamos con las credenciales configuradas en el [`docker-compose.yml`](/docker-compose.yml):

    - Email Address / Username : `admin@hola.com`
    - Password: `admin`

2. Presionamos en *`Add new server`*.

3. Agregamos un nombre a la base de datos, *odoo* por ejemplo.

4. Luego nos dirigimos al campo *`Connection`* y completamos los siguientes campos:

    - Host name/address: `db`
    - Port: `5432`
    - Username: `odoo`
    - Password: `odoo`

5. Presionamos *`Save`* para guardar los cambios y agregar la conexión.

## Instalación

### Git

```bash
sudo apt update && sudo apt install git
```

### Docker

#### Ubuntu

```bash
sudo bash docker/install_ubuntu.sh
```

#### Debian

```bash
sudo bash docker/install_debian.sh
```

#### Crear grupo de usuarios docker

 Para no tener que agregar `sudo` a cada comando de *Docker* que utilicemos podemos crear un grupo de usuarios docker y agregar nuestro usuario al grupo.

1. Crear el grupo docker.

```bash
sudo groupadd docker
```

2. Agregar tu usuario al grupo.

```bash
sudo usermod -aG docker $USER
```

3. Para confirmar los cambios podemos cerrar sesión y volver a abrirla o ejecutar el siguiente comando.

```bash
newgrp docker
```

## Paso a paso para el desarrollo - Workflow

Se puede trabajar en el submodulo union. Se hace commit, luego de realizar los cambios.

### Para actualizar los submódulos al último commit

git submodule update --remote --merge

### Para actualizar un sólo submódulo al último commit

git submodule update --remote --merge <path-to-submodule>

Luego de modificar los cambios, se debe correr el script para copiar los modulos actualizados a custom-addons

```bash
bash copy_addons.sh
```

### Se puede trabajar sobre un branch de desarrollo propia?
