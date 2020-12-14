# Substrate-workshop-introduccion-a-substrate

En este repositorio está toda la información relacionada con el workshop.

**Índice**   
1. [Información general del workshop](#Información-general-del-workshop)
2. [Prerrequisitos para hacer la parte práctica o taller](#Prerrequisitos-para-hacer-la-parte-práctica-o-taller)

El taller será retransmitido en la plataforma crowcast, podeis acceder usando [este link](https://www.crowdcast.io/e/introduccion-a-substrate-workshop/).

## Información general del workshop

Charla/taller en Español sobre el framework Substrate, la principal herramienta para crear bockchains sobre Polkadot. Vamos a ver qué es Substrate, sus características y cómo añadirle funcionalidades a nuestra blockchain. A continuación, habrá un taller sobre cómo añadir un pallet (módulo runtime de Substrate) a nuestra bockchain.

En la primera parte vamos a tratar los siguientes temas:
* Qué es substrate
* Qué nos permite hacer Susbtrate
* Explicar qué es el runtime de substrate
* Qué partes componen un cliente substrate y con qué proposito
* La arquitectura de substrate(Rust y WASM-webassembly)
* Forkless runtime upgrades (Actualizaciones sin hard-fork): explicar por qué es posible esta característica en las blockchain basadas en substrate. Se explicará en qué consiste un hard fork sin entrar en conceptos muy avanzados.
* Explicar brevemente las 3 formas que existen de trabajar con substrate
* Explicaremos la arquitectura de FRAME, la librería de substrate.
* Explicar el concepto de Pallet en substrate.

La segunda parte corresponde a la parte del taller, aunque habrá una pequeña introducción:

* Explicar el concepto de las macros en substrate y para qué se utilizan.
* Explicar las herramientas que se van a utilizar en el taller: plantilla de un nodo substrate y plantilla del front-end.
* Explicaremos cómo poner en marcha y añadir un pallet a nuestra blockchain.



## Prerrequisitos para hacer la parte práctica o taller

En el caso que querais llevar a cabo el taller o "trastear" con substrate de la forma en que lo hacemos en éste. Teneis dos opciones:

* Descargar una máquina virtual (.ova) donde está todo preparado. No habría que compilar ni realizar nada.
* Configurar tu entorno para desarrollar en substrate en tu máquina. Yo he añadido una guía de cómo hacerlo en Ubuntu (En mi caso utilizo Ubuntu 18.04.5 LTS)

### (Opción 1) Máquina virtual 

La máquina virtual  **workshop-substrate** con todo ya configurado la teneis en [este link](https://www.colmenalabs.org/vm/workshop-substrate.zip).

Solo habría que descompimirla e importarla en vuestro gestor de máquinas virtuales. En caso que os pida loguearos:

* **Usuario:** <code>substrate-workshop</code>
* **Password:** <code>pass</code>

### (Opción 2) Instalación del entorno para desarrollar en substrate en Ubuntu

Para poder trabajar con substrate necesitamos instalar: rust, node >= 12.0.0 y yarn

#### Instalación de Rust

Antes de comenzar con la propia intalación de Rust, hay que tener instaladas varias dependencias básicas. Se instalarían mediante la siguiente instrucción:

~~~
sudo apt install -y cmake pkg-config libssl-dev git build-essential clang libclang-dev curl
~~~

Tras instalar los requisitos, procedemos a instalar rust con el script que nos ofrecen en su documentación:

~~~
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
~~~

Un problema común que puede pasar es que se hayan configurado mal las variables de entorno en la instalación. Para ello, añadimos la variable de entorno modificando el archivo ~/.bashrc añadiéndole la siguiente línea:

~~~
export PATH="$HOME/.cargo/bin:$PATH"
~~~

Tras añadirla ejecutamos la sigueinte orden para cargar las variables de entorno de nuevo:
~~~
source ~/.bashrc
~~~

Tras ejecutar estos pasos, deberías poder ejecutar comandos con cargo y rustup.

Ahora vamos a actualizar la versión que tenemos instalada de Rust. Para ello ejecuta las siguientes 3 instrucciones:

~~~
rustup update nightly
~~~

~~~
rustup update stable
~~~

~~~
rustup target add wasm32-unknown-unknown --toolchain nightly
~~~

Lo que estamos haciendo es actualizar nuestra versión de cargo (nightly y stable) son como versiones, stable es la versión más estable a la hora de añadir actualizaciones en rust, nightly tiene actualizaciones cada 2 o 3 semanas. Para poder ejecutar substrate se utiliza una funcionalidad de nigthty por ello la actualizamos.

#### Instalación de nodejs

Hay varias formas de instalar nodejs, la que yo voy a utilizar es la que me parece más simple que es mediante nvm. Si utilizamos los repositorios de ubuntu nos instalará una versión demasiado antigua. Debemos tener una versión >= 12.0.0. O al menos eso recomiendan desde substrate.

Para instalarlo ejecutamos:

~~~
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash

source ~/.profile

nvm install 14.4.0
~~~

Y volvemos a ejecutar:

~~~
source ~/.profile
~~~

En el caso de que tengás más versiones instaladas, deberías ejecutar la siguiente instrucción para elegir la versión a utilizar:

~~~
nvm use 14.4.0
~~~



#### Instalación de yarn

Para llevar a cabo la instalación de yarn lo haremos desde los repositorios de ubuntu, pero antes hay que añadirlo:

~~~
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
~~~
~~~
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
~~~

Ahora usamos la instrucción siguiente para instalar:

~~~
sudo apt update && sudo apt install yarn
~~~

Comprobamos que se ha instalado correctamente usando :

~~~
yarn --version
~~~

#### Descarga y compilación del substrate-node-template(plantilla de un nodo substrate)

Debemos descargar la plantilla substrate-node-substrate desde el repositorio de parity usando la siguiente orden:

~~~
git clone -b v2.0.0 --depth 1 https://github.com/substrate-developer-hub/substrate-node-template
~~~

El siguiente paso sería compilar el proyecto, pero en la actual versión de substrate es posible que haya un error con una de las herramientas de substrate, por lo que recomiendo hacer este paso para evitar dichos errores:

~~~
rustup uninstall nightly
~~~

~~~
rustup install nightly-2020-10-01
~~~

~~~
rustup target add wasm32-unknown-unknown --toolchain nightly-2020-10-01
~~~

Tras tener configurado ya rust correctamente, nos dirijimos al repositorio de la plantilla node-template:

~~~
cd substrate-node-template
~~~

Y compilamos el proyecto, este proceso tarda bastante tiempo, y si no tienes SSD puede necesitar 1 hora incluso. Esta compilando el proyecto y generando un binario optimizado.

~~~
cargo build --release
~~~

#### Descarga e instalación de la plantilla del front-end (front-end-template)

En el taller vamos a utilizar un front-end para interactuar con nuestra blockchain, es un proyecto en react que ha desarrollado parity para el ámbito de desarrollo en substrate:

Lo obtenemos usando:

~~~
git clone -b v2.0.0 --depth 1 https://github.com/substrate-developer-hub/substrate-front-end-template
~~~

Y lo instalamos simplemente entrando en su repo y usando yarn:

~~~
cd substrate-front-end-template
~~~

~~~
yarn install
~~~

Para ponerlo en marcha simplemente:

~~~
yarn start
~~~

Si no se abre una ventana de nuestro navegador, vamos manualmente yendo a la dirección localhost:8000
