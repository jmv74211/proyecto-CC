# Automatización  de la creación de máquinas virtuales desde línea de órdenes

**Tabla de Contenidos**

- [Pasos para la creación de una instancia en la nube con CLI de Azure](#id0)
- [Trabajando con máquinas virtuales en la nube](#id1)
  - [CLI de Azure](#id2)
  - [Instalación de CLI de Azure](#id3)
  - [Inicio de sesión con la CLI de Azure](#id4)
  - [Listado de las imágenes de máquinas virtuales disponibles de Azure](#id5)

El objetivo de las plataformas de virtualización es, eventualmente, crear y gestionar una máquina virtual completa que funcione de forma aislada del resto del sistema y que permita trabajar con sistemas virtualizados de forma flexible, escalable y adaptada a cualquier objetivo.

Eventualmente, el objetivo de este hito es aprender a crear infraestructura como servicio tal como vimos en el primer tema. Para ello necesitamos configurar una serie de infraestructuras virtuales. Los programas que permiten crear infraestructuras virtuales se denominan [hipervisores](http://en.wikipedia.org/wiki/Hypervisor). Un hipervisor permite manejar las diferentes infraestructuras desde línea de órdenes o mediante un programa.

Por encima de los hipervisores están los sistemas de gestión de máquinas virtuales en la nube que, aunque se puedan usar desde las mismas herramientas y con el mismo cometido, en realidad abstraen el trabajo del hipervisor y permiten trabajar con una API uniforme independientemente del hipervisor real que esté por debajo.

## Pasos para la creación de una instancia en la nube <a name="id0"></a>

Para echar a andar una instancia, o máquina virtual, en la nube, hay que seguir
los siguientes pasos:

1. **Iniciar sesión** con nuestra cuenta en la plataforma determinada.

2. **Localizar la imagen que deseamos**. Esta imagen contendrá el sistema operativo, así como alguna utilidad adicional que nos permita trabajar fácilmente con sistemas de aprovisionamiento o configurar alguna otra cosa.

3. **Crear el grupo de recursos***, que indica cómo va a escalar, el centro de datos en el que se va a alojar, y alguna cosa adicional, como el tipo de instancia que se va a usar.

4. **Crear una forma automática de acceder a la máquina virtual**, generalmente mediante generación y copia a la instancia en funcionamiento de la clave pública cuyo par está bajo tu ontrol. Dependiendo del sistema, se tendrá que hacer "a mano" o usará las claves ya disponibles.

5. **Acceso a la máquina virtual** en funcionamiento.

## Trabajando con máquinas virtuales en la nube <a name="id1"></a>

### CLI de Azure <a name="id2"></a>

Azure permite la creación de máquinas virtuales desde el panel de control, pero también desde la [línea de órdenes](https://github.com/WindowsAzure/azure-sdk-tools-xplat). En nuestro caso, haremos uso de la línea de órdenes, para ello, lo primero que tenemos que hacer es [instalarlo](https://docs.microsoft.com/es-es/cli/azure/install-azure-cli-macos?view=azure-cli-latest).


### Instalación de CLI de Azure <a name="id3"></a>

Para la plataforma macOS, se puede instalar la CLI de Azure mediante el administrador de paquetes de Homebrew:

~~~
# Actualizar la información del repositorio de Homebrew
$ brew update

# Instalar la CLI de Azure
$ brew install azure-cli
~~~

<p align="center">
  <img width="650" height="500" src="images/hito 4/instalarCLIAzure0.png">
</p>

~~~
# Desinstalar la CLI de Azure
$ brew uninstall azure-cli
~~~

### Inicio de sesión con la CLI de Azure <a name="id4"></a>

Una vez instalada, se debe usar el comando `az` para ejecutar la CLI de Azure. Sin embargo, antes de ponernos a trabajar con ella, deberemos de estar autenticados usando el comando `az login`. El método de autenticación predeterminado de la CLI de Azure usa un explorador web y un token de acceso para iniciar sesión y al ejecutar el comando se abre una pestaña del navegador diciendo que nos hemos _loggeado_ con éxito:

<p align="center">
  <img width="600" height="90" src="images/hito 4/instalarCLIAzure1.png">
</p>

En nuestra consola nos aparecerá la siguiente información sobre la cuenta y la suscripción, devuelto en un JSON:

<p align="center">
  <img width="590" height="180" src="images/hito 4/instalarCLIAzure2.png">
</p>

_**Pincha [aquí](https://docs.microsoft.com/es-es/cli/azure/authenticate-azure-cli?view=azure-cli-latest) para ver varias formas de iniciar sesión en la CLI de Azure.**_

### Listado de las imágenes de máquinas virtuales disponibles de Azure <a name="id5"></a>

A continuación, vamos a listar las imágenes de máquinas virtuales disponibles [[1][1]], para ello se debe de usar el comando `az vm image list`, en donde, nos aparecerá una lista con detalles sobre la imagen: _nombre_, _versión_, _donde está disponible_, entre otros.

~~~
# Información sobre las imágenes disponibles de máquinas virtuales
$ az vm image list
~~~

<p align="center">
  <img width="600" height="400" src="images/hito 4/CLIAzure0.png">
</p>

_**Pincha [aquí](https://github.com/Gecofer/proyecto-CC/blob/master/docs/salida-az-vm%20image-list.txt) para ver la salida completa que devuelve. Si se añade `-all` se listan todas las imágenes disponibles.**_

Dicho comando devuelve el grupo de máquinas virtuales disponibles en formato JSON, es por eso que al ser tan grande la salida es conveniente filtrarla. Para ello usaremos el tremendamente útil `jq`, [un lenguaje de peticiones para JSON](https://stedolan.github.io/jq/manual/) con cierta similitud con los selectores CSS. Previamente, se deberá tener [instalado *jq*](https://stedolan.github.io/jq/download/):

~~~
# Descarga e instalación de jq en macOS
$ brew install jq
~~~

<p align="center">
  <img width="650" height="210" src="images/hito 4/instalarjq0.png">
</p>

Hecho esto, ya podremos pasar a filtrar la búsqueda. La búsueda siguiente filtra solo aquellas imágenes que contengan `buntu` (no sabemos si va a estar en mayúsculas o en minúsculas):

~~~
# Filtrar solo aquellas imágenes que contengan `buntu`
$ az vm image list | jq '.[] | select( .offer | contains("buntu"))'
~~~

<p align="center">
  <img width="550" height="120" src="images/hito 4/CLIAzure1.png">
</p>

También se pueden buscar todos los proveedores de una imagen determinada, como por ejemplo listar todas las imágenes de _UbuntuServer_ que incluyan una `location` indicada, como es el caso para el sur de Reino Unido.

~~~
$ az vm image list --offer UbuntuServer --all --location uksouth --output table
~~~

<p align="center">
  <img width="600" height="150" src="images/hito 4/CLIAzure2.png">
</p>

Con este tipo de técnicas y herramientas, podemos reducir considerablemente el tiempo de búsqueda de imágenes para máquinas virtuales.


[1]: https://docs.microsoft.com/en-us/cli/azure/vm/image?view=azure-cli-latest
