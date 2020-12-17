# Substrate-workshop-introduccion-a-substrate

En este repositorio está toda la información relacionada con el workshop.

**Índice**   
1. [Información general del workshop](#id1)
2. [Prerrequisitos para hacer la parte práctica o taller](#id2)
3. [Definición de instrucciones, opciones y código utilizados en el taller](#id3)
4. [Referencias y recursos para profundizar en los diferentes aspectos mostrados en el taller](#id4)


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

## 3. Definición de instrucciones, opciones y código utilizados en el taller

### Instrucciones de Cargo

La instrucción **cargo build** lo que hace es compilar el proyecto rust, la opción <code>--release</code> lo que hace es compilar el proyecto usando un "perfil" es decir, utiliza varias opciones definidas por defecto a la hora de compilar, estas opciones hacen que compile el proyecto y genere un binario optimizado.

~~~
cargo build --release
~~~

La instrucción **cargo check** la utilizaremos para comprobar que las modificaciones que hayamos hecho en el proyecto no producen ningún error, este comando tiene varias peculiaridades, no utiliza la compilación que se usa utilizando cargo build, por lo que compilará todo el proyecto. La ventaja de comprobar errores con esta instrucción con respecto a usar directamente cargo build es que tarda 10 minutos normalmente, en cambio, cargo build puede ocupar muchísimo más tiempo.

~~~
cargo check
~~~

### Opciones en la ejecución del binario

Cuando ya hemos compilado el proyecto con <code>cargo build --release</code>, el binario se genera en el directorio <code>/substrate-node-template/target/release/</code> donde estará con el nombre node-template. Para ejecutar dicho binario lo ejecutaremos como cualquier otro archivo ejecutable en Linux:

~~~
cd substrate-node-template
~~~

Estando en el directorio del proyecto:

~~~
./target/release/node-template
~~~

En el taller mostraremos las siguientes opciones:

* La opción <code>--dev</code >, la cual permite que la blockchain cree bloques sin la necesidad de que tengamos que tener 2 nodos en la red. También creará varias cuentas por defecto con fondos para poder hacer pruebas en la red. Estas cuentas no tienen claves por lo que podemos cambiar entre una y otro directamente. 

* La opción <code>--tmp</code>, esta opción nos sirve para que los cambios que hagamos interactuando con la red no se almacenen.

* La opción <code>purge-chain</code>, esta opción se utiliza para eliminar los datos de la blockchain, es decir, si queremos iniciar la chain desde el bloque 0, deberíamos ejecutar esto para eliminar los datos de la blockchain que haya sido ejecutada anteriormente. (Si utilizamos la opción tmp, no deberemos utilizar esta instrucción).


### Código utilizado en el taller

El código ha sido recogido del tutorial de añadir un pallet que se encuentra en el siguiente [link](https://substrate.dev/docs/en/tutorials/add-a-pallet/) de la web de substrate. Aún así lo añado aquí para facilitar su acceso y con comentarios en español.

En el archivo <code>./substrate-node-template/runtime/Cargo.toml</code> añadimos :

~~~
pallet-nicks = { default-features = false, version = '2.0.0' }
~~~

Y esta línea dónde se define la feature std:

~~~
'pallet-nicks/std', 
~~~

En el archivo <code>./substrate-node-template/runtime/src/lib.rs </code> añadimos la implementación del Trait de configuración del pallet nicks:

~~~
// Pallet de Nicks

//Primer paso, definición de parámetros


parameter_types! {
	
	// Definir las variables que tenemos en el Trait de configuración, consejo: si es get normalmente es un parámetro a añadir

    pub const NickReservationFee: u128 = 100;
    pub const MinNickLength: usize = 8;
    pub const MaxNickLength: usize = 32;
}




// Implementación del Trait de configuración del Pallet


impl pallet_nicks::Trait for Runtime {

    // The Balances pallet implements the ReservableCurrency trait.
    // https://substrate.dev/rustdocs/v2.0.0/pallet_balances/index.html#implementations-2
    type Currency = pallet_balances::Module<Runtime>;

    
    type ReservationFee = NickReservationFee;

    // El uso de () es para decir que no haga nada, esta configuración de slashed es opcional, por ello podemos hacer ésto.
    type Slashed = ();

    // Configure the FRAME System Root origin as the Nick pallet admin.
    // https://substrate.dev/rustdocs/v2.0.0/frame_system/enum.RawOrigin.html#variant.Root
    type ForceOrigin = frame_system::EnsureRoot<AccountId>;

    
    type MinLength = MinNickLength;

    
    type MaxLength = MaxNickLength;

    // Al utilizar la palabra Event, es un tipo que existe en la system library, y al utilizar creamos y lanzamos un evento.
    type Event = Event;
}

~~~

## 4. Referencias y recursos para profundizar en los diferentes aspectos mostrados en el taller

He organizado las referencias y recursos de la siguiente forma, primero nombraré en orden los recursos que creo que para empezar estaría bien. Y en las referencias colocaré la dirección del vídeo o documentación para profundizar en diferentes aspectos de substrate.

### Recursos aprendizaje Rust 

Aunque para empezar con substrate no es necesario tener alto conocimientos de Rust, recomiendo saber conceptos básicos porque te ayudará a comprender el código más fácilmente.

Los recursos que me han parecido interesantes para aprender y usar este lenguaje desde 0 han sido:

* https://github.com/rust-lang/rustlings es un proyecto de github que consiste en pequeños ejercicios para empezar a utilizar rust, ya sea creando funciones simples, tratamiento de errores, definición de tipos, etc. Es básico y está muy bien para empezar con el lenguaje.

* https://doc.rust-lang.org/rust-by-example by examples) es la misma idea que rustling, pero incluso más simple, con explicaciones previas, e incluye pequeños ejercicios para hacer en rust, lo interesante es que los ejercicios puedes resolverlos online en su plataforma.

* https://doc.rust-lang.org/book/index.html incluyo la propia documentación de Rust porque siempre que tengais dudas con alguna instrucción, de cargo, de lo que sea la podeis encontrar en la documentación. Yo, al menos todos los conceptos utilizados en el taller los he encontrado en su documentación.

### Recursos de substrate

Para empezar con susbtrate, lo que voy a recomendar es por supuesto empezar con la sección de tutoriales que tienen en https://substrate.dev/en/tutorials . En estos tutoriales enseñan desde cómo crear un entorno de desarrollo apropiado para substrate a como empezar a crear tus propios pallets.

A medida que hagas tutoriales, es muy importante que leas su https://substrate.dev/docs/en/ , así como leas la guía que tienen para desarrolladores https://substrate.dev/recipes/ , esta parte está algo desactualizada, me refiere al código, por lo es posible que tengas que hacer ligeros cambios en el código de los pallets que usen ellos para hacerlos funcionar.

Todo este contenido está en inglés, yo he traducido bastante parte de su documentación y casi todos sus tutoriales a español, si bien no aparecen en su web, lo puedes encontrar en la https://crowdin.com/project/substrate-developer-hub/es-ES#. Dónde, además podeis colaborar en las traducciones (recomiendo que hagais un backup de las traducciones que hagais porque como substrate y su documentación están en desarrollo pueden borrar y modificar cosas). También podeis acceder a mi traducción en https://github.com/tropicar/Spanish_Translation_Substrate: 

### Referencias

Aquí voy a colocar diferentes videos dónde podeis profundizar en aspectos que hemos nombrado en la charla y que recomiendo, son eventos en inglés.

https://www.youtube.com/watch?v=YJwbpF6yROk (Introducción a Substrate) Es una exposición sobre substrate, se explica muchos conceptos básicos generales de substrate. 

https://www.youtube.com/watch?v=MQgDV37JrIY (Forkless Runtime upgrades) Se explica el proceso de actualización del runtime, cómo se lleva a cabo la migración del storage. Es un charla/taller super interesante y que explica los conceptos técnicos para llevar a cabo este proceso.

https://www.youtube.com/watch?v=Qa6sTyUqgek (Benchmarking a los pallets) La exposición es muy larga 3 horas, y es un aspecto muy específico, pero creo que la explicación del concepto de weight (una unidad de medida que se utiliza en substrate para contabilizar el gasto de computo que requiere una operación en la blockchain, me parece muy recomendable y eso se encuentra en el comienzo de la exposición)

https://www.youtube.com/watch?v=unBtPOc6DZg (Substrate 2.0.0) Esta exposición habla sobre el estado actual de substrate, muy recomendable para los que quiera saber más sobre en qué estado se encuentra substrate y hacía dónde quiere seguir desarrollándose.

https://www.crowdcast.io/e/substrate-pallets?utm_campaign=discover&utm_source=crowdcast&utm_medium=discover_web (Taller de añadir pallets en substrate) Es un taller dónde se añaden pallets tal y como hemos aquí, los pallets que utilizan son el pallet de vesting y de utility.

