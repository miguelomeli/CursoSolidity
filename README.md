# Hola soy Miguel Lomeli

Mis datos de contacto son : 

E-mail : miguel@lomeli.io
FB : miguel.lomeli.mx
Whatsapp : 3314791340

[![N|Solid](https://lomeli.io/images/logo-default-125x45.png)](https://lomeli.io)

# En este taller veremos los siguientes modulos:

  - 1.- Instalación de Nodo Geth
  - 1.1 Comandos basicos sobre web3 , geth
  - 2.- Introduccion Basica a Solidity
  - 3.- Que son los Smart Contracts
  - 4.- Creacion de Tokens
  - 5.- Creacion de Dapsp
  - 6.- Polleo (es un nombre raro que le puse a una acción la cual es similar a walletnotify de bitcoin)
 

# 1.- Instalación de Nodo Geth

Necesitaremos crear un servidor , en este caso lo crearemos en [DigitalOcean](https://digitalocean.com/).
Le instalamos Ubuntu 16.04.4 x64.

Una vez instalado instalaremos algunas librerias requeridas antes de continuar.

```sh
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install -y nano curl git wget vnstat vnstati lsb-release zip cron sudo unzip nscd netcat software-properties-common
$ sudo apt-get install -y build-essential libssl-dev libdb-dev libdb++-dev libboost-all-dev git libssl1.0.0-dbg
$ sudo apt-get install -y libdb-dev libdb++-dev libboost-all-dev libminiupnpc-dev libminiupnpc-dev libevent-dev libcrypto++-dev libgmp3-dev
sudo apt-get install gcc g++ make
```

Una vez terminado instalaremos NodeJS en la version 8.

```sh
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ sudo apt-get install -y nodejs
$ ln -s /usr/bin/nodejs /usr/bin/node
$ sudo apt-get install npm
```

Ahora instalaremos un alias al comando ll para listar directorios.

```sh
$ nano  ~/.bash_profile
alias ll='ls -l --color=auto'
```

Ahora actualizaremos algunas opciones del servicio SSH , esto es para mantener la session activa, algunos servidores cloud no lo requieren pero en este caso si lo requerimos.

```sh
$ nano /etc/ssh/ssh_config 
Host *
TCPKeepAlive yes
ServerAliveInterval 20
```

```sh
$ nano /etc/ssh/sshd_config 
TCPKeepAlive yes
ClientAliveInterval 60
```

Ahora solo queda reiniciar el servicio SSH

```sh
$ /etc/init.d/ssh restart
```

Instalamos Supervisor , es un software el cual permite iniciar servicios automaticamente.
Aqui dejo el link de un manual.
https://hiddentao.com/archives/2016/05/04/setting-up-geth-ethereum-node-to-run-automatically-on-ubuntu/

```sh
$ apt-get install supervisor  
```

Ahora vamos a pasar a instalar el servidor GETH de ethereum.

```sh
$ sudo apt-get -y install software-properties-common
$ sudo add-apt-repository -y ppa:ethereum/ethereum
$ sudo add-apt-repository -y ppa:ethereum/ethereum-dev
$ sudo apt-get -y update
$ sudo apt-get -y install ethereum
```

Reiniciamos el Servidor

```sh
$ reboot
```

Con esto queda instalado por completo el servidor con caracteristicas basicas para poder trabajar en este curso.


# 1.1 Comandos basicos sobre web3 , geth

En mi caso creare una carpeta externa donde se guardara toda la data de ethereum.

```sh
$ cd /root
$ mkdir eth
$ chmod 777 eth
```

Ahora vamos a correr el servidor geth.

```sh
geth --testnet --fast --datadir "/root/eth/" --verbosity 0 --ipcdisable --rpc --rpccorsdomain * --rpcport 8545 --rpcaddr 0.0.0.0 --rpcapi "admin,db,eth,debug,miner,net,shh,txpool,personal,web3" console
```

Explicare un poco sobre los comandos anteriormente al ejecutar geth.
 - --testnet (Red Ropsten: red de prueba preconfigurada de prueba de trabajo)
 - --fast (Habilite la sincronización rápida a través de descargas de estado)
 - --datadir "/path/" (Directorio de datos para las bases de datos y el almacén de claves)
 - --verbosity 0 (Logging verbosity: 0=silent, 1=error, 2=warn, 3=info, 4=debug, 5=detail (default: 3))
 - --ipcdisable (Deshabilitar el servidor IPC-RPC)
 - --rpc (Habilite el servidor HTTP-RPC)
 - --rpccorsdomain * (Lista de dominios separados por comas para aceptar solicitudes de origen cruzadas (se aplica el navegador))
 - --rpcport 8545 (Puerto de escucha del servidor HTTP-RPC (predeterminado: 8545))
 - -rpcaddr 0.0.0.0 (Interfaz de escucha del servidor HTTP-RPC (predeterminado: "localhost"))
 - --rpcapi "lista" (API ofrecidas a través de la interfaz HTTP-RPC)
 - console (Comience un entorno de JavaScript interactivo)
 
Si quieres conocer un poco mas de configuraciones te dejo un enlace.
https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options

Ahora vamos a conectarnos desde otra shell a nuestro servidor geth.

```sh
$ geth attach http://127.0.0.1:8545
```

El comando a continuacion son para sincronizar la red de blockchain

```sh
$ web3.eth.syncing
```

El comando a continuacion es para ver el bloque actual

```sh
$ web3.eth.blockNumber
```

Muy bien ya tenemos nuestro servidor corriendo y sincronizando.
Ahora nos queda interactuar con el servidor atravez de la api web3, a continacion veremos algunos casos basicos

Creacion de nueva cuenta
```sh
$ web3.personal.newAccount("password que quieras ingresar")
"0x210e1b9ae388a036ddad1d46deca4250617e5aaf"
```

Listado de cuentas
```sh
$ web3.eth.accounts
["0x210e1b9ae388a036ddad1d46deca4250617e5aaf", "0xf538dfe7bdb2121deea71f48f0a62acfbfc4f2d3"]
```

Listar saldo de cuenta
```sh
$ web3.eth.getBalance("0x210e1b9ae388a036ddad1d46deca4250617e5aaf")
0
```

Enviar saldo ETH.
En este caso primero necesitamos desbloquear la cuenta ya que ethereum las bloquea para asegurar el saldo.
Una vez desbloqueada mandamos la transaccion.
Y al finalizar la volvemos a bloquear
```sh
$ web3.personal.unlockAccount("CuentaDeDondeTomarElSaldo", "password");
$ web3.eth.sendTransaction({from:De, to:Para, value: web3.toWei(Value, "ether")})
$ web3.personal.lockAccount("CuentaDeDondeTomarElSaldo", "password");
```

Hemos terminado , ahora solo nos queda correr el servicio como demonio , este es un metodo de muchos

```sh
$ ps aux | grep -ie eth | awk '{print $2}' | xargs kill -9
$ cd /etc/supervisor/conf.d/
$ nano geth.conf

[program:geth]
command=/usr/bin/geth --testnet --fast --datadir "/root/eth/" --verbosity 0 --ipcdisable --rpc --rpccorsdomain * --rpcport 8545 --rpcaddr 0.0.0.0 --rpcapi "admin,db,eth,debug,miner,net,shh,txpool,personal,web3"
autostart=true  
autorestart=true  
stderr_logfile=/var/log/supervisor/geth.err.log  

$ supervisorctl reload  
```


Muy bien tenemos nuestro primer tema completado. Como les parecio ? podemos continuar ? 


# 2.- Introduccion Basica a Solidity

¿QUÉ ES SOLIDITY Y PARA QUÉ SIRVE?

Solidity es un lenguaje de programación de alto nivel cuya síntesis es similar a otro de los lenguajes de programación más usados hoy en día: Javascript.

Este lenguaje está diseñado y compilado en código de bytes (bytecode) para crear y desarrollar contratos inteligentes que se ejecuten en la Máquina Virtual Ethereum (EVM de sus siglas en inglés).

Mediante Solidity, los desarrolladores pueden escribir aplicaciones descentralizadas que implementen automatizaciones en los negocios a través de los contratos inteligentes, dejando un registro irrefutable y autorizado de las transacciones.

En palabras menos técnicas, Solidity sirve para la creación de ‘smart contracts’ que permitan que muchas de las partes de un negocio funcionen perfectamente por sí solas y además se lleve un registro de las mismas.

# Visibilidad y Getters
Dado que Solidity conoce dos tipos de llamadas de función (las internas que no crean una llamada EVM real (también llamada una “message call o llamada de mensaje”) y las externas que lo hacen), hay cuatro tipos de visibilidades para funciones y variables de estado.


Las funciones pueden ser especificadas como tipo `external`, `public`, `internal` o `private`, donde el valor predeterminado es public. Para las variables de estado, external no es posible y el valor predeterminado es internal.

 - `external` : Las funciones externas forman parte de la interfaz del contrato, lo que significa que pueden ser llamadas desde otros contratos y por medio de transacciones. Una función externa f no se puede llamar internamente (es decir f() no funciona, pero this.f() si funciona). Las funciones externas son a veces más eficientes cuando reciben grandes arreglos de datos.
 - `public` : Las funciones públicas forman parte de la interfaz del contrato y pueden ser llamadas internamente o por mensajes. Para las variables de estado público, se genera una función getter automática (véase más adelante).
 - `internal` : Esas funciones y variables de estado sólo se pueden acceder internamente (es decir, de dentro del contrato o contratos actuales derivados de él), sin utilizar this.
 - `private` : Las funciones privadas y las variables de estado sólo son visibles para el contrato en el que se definen y no en los contratos derivados.
 
# `NOTA`
Todo lo que está dentro de un contrato es visible para todos los observadores externos. Hacer algo private sólo impide que otros contratos accedan y modifiquen la información, pero seguirá siendo visible para todo el mundo fuera de la blockchain.


# Funciones
Las funciones son las unidades de código ejecutables dentro de un contrato.
```js 
pragma solidity ^0.4.24;
contract lomeli {
    function crear() {
        // ...
    }
}
```


# Modificadores de funciones
Los modificadores de función se pueden utilizar para enmendar la semántica de las funciones de forma declarativa. E pocas palabras son algo como si no se cumple la accion ingnora la accion.
```js 
pragma solidity ^0.4.24;
contract lomeli {
    address public seller;
    
    modifier onlySeller() {
        require(msg.sender == seller);
        _;
    }
    
    function crear() onlySeller {
        // ...
    }
}
```

# Eventos
Los eventos son interfaces de conveniencia con las instalaciones de registro de EVM.
```js 
pragma solidity ^0.4.24;
contract lomeli {
    event HighestBidIncreased(address bidder, uint amount);
    
    function crear() onlySeller {
        HighestBidIncreased(msg.sender, msg.value)
    }
}
```

# Tipos de estructuras
Los Structs son tipos personalizados definidos que pueden agrupar varias variables.
```js 
pragma solidity ^0.4.24;
contract lomeli {
    struct Voter {
        uint weight;
        bool voted;
        address delegate;
        uint vote;
    }
}
```

# Tipos de Enum
Los enums se pueden utilizar para crear tipos personalizados con un conjunto finito de valores.
Enums son una forma de crear un tipo definido por el usuario en Solidity. Están explícitamente convertibles a / y desde todos los tipos de entero, pero no se permite la conversión implícita. Las conversiones explícitas comprueban los rangos de valores en tiempo de ejecución y un error produce una excepción. Enums necesita al menos un miembro.
```js 
pragma solidity ^0.4.24;
contract lomeli {
    enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill }
    ActionChoices choice;
    ActionChoices constant defaultChoice = ActionChoices.GoStraight;

    function setGoStraight() {
        choice = ActionChoices.GoStraight;
    }
    
    function getChoice() returns (ActionChoices) {
        return choice;
    }

    function getDefaultChoice() returns (uint) {
        return uint(defaultChoice);
    }
}
```


# Booleanos
`bool`: Los valores posibles son constantes `true` y `false`.

# Operadores
`bool`: Los valores posibles son constantes `true` y `false`.
 - `!` (Negación lógica)
 - `&&` (Conjunción lógica, “and”)
 - `||` (Disyunción lógica, “or”)
 - `==` (igualdad)
 - `!=` (desigualdad)

# Enteros
`int` / `uint`: Enteros firmados y sin signo de varios tamaños. Las palabras clave `uint8` to `uint256` en pasos de 8 (sin signo de 8 hasta 256 bits) y `int8` to `int256. `uint` and `int` son alias para `uint256` e `int256`, respectivamente.

# Direcciónes
`address`: Contiene un valor de 20 bytes (tamaño de una dirección Ethereum). Los tipos de dirección también tienen miembros y sirven como base para todos los contratos.

# Arrays de bytes de tamaño fijo
`bytes1`, `bytes2`, `bytes3`, ..., `bytes32`. `byte` es un alias para `bytes1`.

# Mappings o Asignaciones
Mapping types o Los tipos de asignación se declaran como `mapping(_KeyType => _ValueType)`. Aquí `_KeyType` puede haber casi cualquier tipo excepto un mapeo, una matriz de tamaño dinámico, un contrato, un enum y una estructura. `_ValueType` puede ser de cualquier tipo, incluyendo asignaciones (mappings).

Las asignaciones se pueden ver como `hash tables` que están virtualmente inicializadas de tal manera que cada clave posible existe y se asigna a un valor cuya representación de bytes es todos ceros: el default value de un tipo. La similitud termina aquí, sin embargo: Los datos de la llave no se almacenan realmente en una correspondencia "mapping", solamente su `keccak256` hash usado para mirar para arriba el valor.

Debido a esto, las asignaciones no tienen una longitud o un concepto de una clave o valor que se "establece". “set”.

Las asignaciones sólo se permiten para variables de estado (o como tipos de referencia de almacenamiento en funciones internas).

Es posible declarar asignaciones `public` y permitir a Solidity crear un getter. El `_KeyType` se convertirá en un parámetro necesario para el getter y retornará `_ValueType`.

El `_ValueType` puede ser un mapping tambien. El getter tendrá un parámetro para cada uno `_KeyType`, recursivamente.
```js
pragma solidity ^0.4.24;
contract lomeli {
    mapping(address => uint) public balances;

    function update(uint newBalance) {
        balances[msg.sender] = newBalance;
    }
}

contract MappingUser {
    function f() returns (uint) {
        MappingExample m = new MappingExample();
        m.update(100);
        return m.balances(this);
    }
}
```

# delete o eliminar
`delete a` asigna el valor inicial para el tipo en `a`. Es decir, para enteros es equivalente a `a = 0`, pero también se puede utilizar en arrays, donde asigna una matriz dinámica de longitud cero o una matriz estática de la misma longitud con todos los elementos restablecidos. Para structs, asigna una estructura con todos los miembros restablecidos.

`delete` No tiene ningún efecto en las asignaciones completas (ya que las claves de las asignaciones pueden ser arbitrarias y generalmente son desconocidas). Así que si eliminas una estructura, restablecerá todos los miembros que no sean asignaciones y también recursar en los miembros a menos que sean asignaciones. Sin embargo, las claves individuales y lo que se asignan a se pueden eliminar.

Es importante notar que `delete a` realmente se comporta como una asignación `a`, es decir, almacena un nuevo objeto en a.
```js
pragma solidity ^0.4.24;
contract lomeli {
    uint data;
    uint[] dataArray;

    function f() {
        uint x = data;
        delete x; // establece x en 0, no afecta data
        delete data; // establece a data en 0, no afecta a x que todavía tiene una copia
        uint[] y = dataArray;
        delete dataArray; // esto establece dataArray.length a cero, pero como uint [] es un objeto complejo, también 
        // se afecta a "y" es un alias al objeto de almacenamiento 
        // Por otro lado: "delete y" no es válido, como asignaciones a variables locales 
        // referenciar objetos de almacenamiento sólo puede hacerse a partir de objetos de almacenamiento existentes.
    }
}
```

# Unidades de Éther
Un número literal puede tomar un sufijo de `wei`, `finney`, `szabo` o `ether` para convertirse entre las subdenominaciones de ether, donde los números de moneda Ether sin un sufijo seran por defecto Wei, por ejemplo, `2 ether == 2000 finney` es evaluado a `true`.

# Unidades de tiempo
Sufijos como `seconds`, `minutes`, `hours`, `days`, `weeks` y `years` después de los números literales se pueden utilizar para convertir entre unidades de tiempo donde segundos son la unidad base y las unidades se consideran nativamente de la siguiente forma:

 - 1 == 1 seconds
 - 1 minutes == 60 seconds
 - 1 hours == 60 minutes
 - 1 days == 24 hours
 - 1 weeks == 7 days
 - 1 years == 365 days


# Propiedades de bloques y transacciones


 - `block.blockhash(uint blockNumber) returns (bytes32)`: hash del bloque dado - sólo funciona para los 256 bloques más recientes excluyendo el actual
 - `block.coinbase (address)`: la dirección actual del minero del bloque
 - `block.difficulty (uint)`: dificultad de bloque actual
 - `block.gaslimit (uint)`: limite de gas del bloque actual
 - `block.number (uint)`: número de bloque actual
 - `block.timestamp (uint)`: timestamp del bloque actual como segundos desde la época de unix
 - `msg.data (bytes)`: calldata completo
 - `msg.gas (uint)`: gas residual
 - `msg.sender (address)`: remitente del mensaje (llamada actual)
 - `msg.sig (bytes4)`: primeros cuatro bytes de los calldata (es decir, identificador de función)
 - `msg.value (uint)`: número de wei enviado con el mensaje
 - `now (uint)`: marca de tiempo de bloque actual (alias para block.timestamp)
 - `tx.gasprice (uint)`: precio del gas de la transacción
 - `tx.origin (address)`: remitente de la transacción (llamada de cadena completa)

# Manejo de errores
`assert(bool condition)`:
Se arroja si la condición no se cumple - para ser utilizado para errores internos.

`require(bool condition)`:
Se arroja si la condición no se cumple - para ser utilizado para errores en entradas o componentes externos.

`revert()`:
cancelar la ejecución y revertir los cambios de estado

# Hemos terminado el modulo 2

Si quieres saber mas te dejo estos enlaces :
https://miethereum.com/smart-contracts/solidity/
https://eleanarey.github.io/solidity-en-espa%C3%B1ol/


# 3.- Que son los Smart Contracts
Un contrato inteligente o smart contract es un código o protocolo informático que facilita verificar y hacer cumplir un contrato de manera automática. Aunque existe discusión acerca de la necesidad o no de acudir a agentes externos para ejecutar la condición prevista, lo que está claro es que aportarán agilidad al sector de los negocios. Estos contratos funcionan en la cadena de bloques y, a priori, no necesitan la intervención de las personas para comprobar y ejecutar su cumplimiento.

Como ejemplo, se acostumbra a explicar el caso de una casa de apuestas. Supongamos que queremos apostar una cantidad de dinero X a que el Barcelona gana la liga. Para ello deberíamos crear una cuenta neutral controlada por un smart contract, a la cual cada una de las partes debería abonar X criptomonedas. Una vez finalizada la liga, si el FCB ha resultado ganador, el propio contrato, accederá a una base de datos oficial, comprobará el ganador de la liga de fútbol y automáticamente enviará los fondos al vencedor de la apuesta.

Sistemas como PayPal usan actualmente los denominados contrato de depósito, a través de los cuales un intermediario conocido como agente de depósito en garantía audita el cumplimiento de ciertas condiciones pactadas en el contrato para activar, en su caso, los protocolos que permitan efectuar esa transacción. Pese a la agilidad de Paypal, el potencial de esta tecnología nos permite prescindir del propio intermediario, es decir de Paypal, y, además nos garantiza la ejecución de la transacción sin renunciar a la seguridad actual. En este ámbito, actualmente Ethereum es la plataforma de smart contracts más destacada de la red.


¿Cómo funciona?
 
El código que constituye el contenido del contrato se almacena en la cadena de bloques, un libro virtual que registra todas las transacciones de una determinada criptomoneda. El código debe basarse en reglas lógicas (si pasa X, entonces Y) y condiciones (que pueden interactuar con dispositivos autónomos como sensores de IOT). El resultado es un acuerdo virtual blindado con todas las eventualidades cubiertas, de manera que si todas las partes entregan lo acordado, no existirá posibilidad de fraude.

En ocasiones resulta imprescindible acudir a agentes externos que verifiquen el cumplimiento de una condición. A estos agentes se les denomina oráculos. Los oráculos son instrumentos informáticos que permiten validar las condiciones previstas en los smart contracts. Generalmente hacen referencia a información externa para decidir si una cláusula del contrato ha sucedido o no. De esta manera, una vez que el oráculo obtiene la información y la contrasta, el contrato se ejecuta y la transacción se produce.

En resumen...

A pesar de todo lo comentado, aunque blockchain todavía se encuentra en una fase temprana de desarrollo, podemos afirmar sin riesgo a equivocarnos que el potencial que nos brinda, nos permitirá introducir cambios en el modo de “intercambiar valor” a través de los canales digitales. Así, de la misma forma que internet nos trajo el intercambio de información de forma ágil y sencilla, esta tecnología introducirá una nueva forma de intercambiar valor entre negocios, instituciones y particulares.

Además, de la mano de la automatización viene aparejado el concepto de justicia y transparencia. Sin duda, los smart contracts aumentaran la velocidad de la ejecución de las transacciones, lo que se traducirá eventualmente en la posibilidad de cerrar un mayor volumen de acuerdos con menor riesgo al cumplimiento.


# 4.- Creacion de Tokens
ERC-20 es un token perteneciente a la plataforma descentralizada de contratos inteligentes Ethereum. Su nombre significa, Ethereum Request For Comments o en español, Solicitud de Comentarios de Ethereum y el número 20 se establece como una identificación estándar para diferenciarlo de los demás.

Estos tokens, que se construyen en la parte superior de la Blockchain de Ethereum, son protocolos estándar que regulan la emisión de nuevos tokens en la red, lo que supone que todos los nuevos tokens deben cumplir ciertas reglas y parámetros para su aceptación. El objetivo y la necesidad de este estándar, aparte de crear un parámetro a seguir, es crear interoperabilidad entre tokens y fomentar mejoras en el ecosistema de Ethereum.

La obtención del suministro total de tokens, obtener el saldo de la cuenta, capacidad de transferencia del token y posibilidad de gastarlo, son algunas de las funciones estándares que deben cumplirse para la incorporación e interacción de nuevos tokens. Aunque no todos los demás token cumplan con estas características, todavía pueden ser compatibles e interactuar con el ERC20 en algunas funciones, según las capacidades que posean.

Este token siendo un activo digital como cualquier otro, también puede comercializarse de forma similar a Bitcoin, Ether o cualquier otro criptoactivo. Agregando también, que sus transacciones pueden rastrearse de igual forma que una criptomoneda, en la Blockchain de Ethereum.


En este ejemplo utilizaremos un token basico el cual nos sirve para crear nuestra propia moneda virtual basada en ERC20
```js
pragma solidity ^0.4.24;

contract LomeliToken {

    string public name = "Lomeli";
    string public symbol = "LOM";
    uint256 public decimals = 18;
    uint256 public totalSupply = 100000000000000000000000000;
    address owner;
    mapping (address => uint256) public balanceOf;
    mapping (address => mapping (address => uint256)) public allowance;

    
    function LomeliToken() {
        owner = msg.sender;
        balanceOf[msg.sender] = totalSupply;
        Transfer(0x0, owner, totalSupply);
    }


    function transfer(address _to, uint256 _value) returns (bool success) {
        require(balanceOf[msg.sender] >= _value);
        require(balanceOf[_to] + _value >= balanceOf[_to]);
        balanceOf[msg.sender] -= _value;
        balanceOf[_to] += _value;
        Transfer(msg.sender, _to, _value);
        return true;
    }

    function transferFrom(address _from, address _to, uint256 _value) returns (bool success) {
        require(balanceOf[_from] >= _value);
        require(balanceOf[_to] + _value >= balanceOf[_to]);
        require(allowance[_from][msg.sender] >= _value);
        balanceOf[_to] += _value;
        balanceOf[_from] -= _value;
        allowance[_from][msg.sender] -= _value;
        Transfer(_from, _to, _value);
        return true;
    }

    function approve(address _spender, uint256 _value) returns (bool success) {
        require(_value == 0 || allowance[msg.sender][_spender] == 0);
        allowance[msg.sender][_spender] = _value;
        Approval(msg.sender, _spender, _value);
        return true;
    }

    function burn(uint256 _value) {
        require(balanceOf[msg.sender] >= _value);
        balanceOf[msg.sender] -= _value;
        balanceOf[0x0] += _value;
        Transfer(msg.sender, 0x0, _value);
    }

    event Transfer(address indexed _from, address indexed _to, uint256 _value);
    event Approval(address indexed _owner, address indexed _spender, uint256 _value);
}
```

En este nuevo ejemplo veremos un contrato un poco mas complicado el cual sirve para poder recibir muchos tokens ala vez y poder enviarlos alguna vez si los requerimos
```js
pragma solidity ^0.4.24;

contract Token {
    bytes32 public standard;
    bytes32 public name;
    bytes32 public symbol;
    uint256 public totalSupply;
    uint8 public decimals;
    bool public allowTransactions;
    mapping (address => uint256) public balanceOf;
    mapping (address => mapping (address => uint256)) public allowance;
    function transfer(address _to, uint256 _value) returns (bool success);
    function approveAndCall(address _spender, uint256 _value, bytes _extraData) returns (bool success);
    function approve(address _spender, uint256 _value) returns (bool success);
    function transferFrom(address _from, address _to, uint256 _value) returns (bool success);
}

contract LomeliMultilpleTokens {
    address public owner;
  
    modifier onlyOwner {
		require(owner == msg.sender);
        _;
	}

    function LomeliMultilpleTokens() {
        owner = msg.sender;
    }

    function withdraw(address token, address para , uint256 amount) onlyOwner public {
        Token(token).transfer(para, amount);
    }
  
}

```
# 5.- Creacion de Dapsp
Una ÐApp (se escribe con esta Ð extraña y pronunciado como [Di-app], similar a como decimos “e-mail” [i-meil]) es una aplicación descentralizada, es decir, una app que no depende de un sistema central, sino que depende de la comunidad de usuarios que la utilizan.

La aplicación descentralizada puede ser una app móvil o una aplicación web que interactúa con un contrato inteligente para llevar a cabo su función.

Si recordamos de forma breve lo que era un contrato inteligente o smart contract, diremos que es un programa informático que se ejecuta a sí mismo cuando se cumplen las condiciones que fueron programadas en su código.

Para entender el funcionamiento de una ÐApp y su interacción con los contratos inteligentes, lo mejor es realizar una breve introducción comparando las aplicaciones web tradicionales con las novedosas ÐApps.

En este nuevo ejemplo veremos un contrato el cual sirve para crear un blog , crear notas, editarlas, eliminarlas
```js
pragma solidity ^0.4.24;
contract BlogsLomeli {

    address public owner;
    uint256 public contador;
    
    struct Notas{
        uint256 id;
        uint256 date;
        string titulo;
        string descripcion;
    }
    mapping (uint256 => Notas) public notas;
    
	modifier onlyOwner {
		require(owner == msg.sender);
        _;
	}

	constructor() public{
		owner = msg.sender;
	}

	function createNota(string _titulo , string _description) onlyOwner public{
		notas[contador].id = contador;
		notas[contador].titulo = _titulo;
		notas[contador].descripcion = _description;
		notas[contador].date = block.timestamp;
		logCreateNota(contador , block.timestamp , _titulo , _description);
		contador++;
	}

	function updateNota(uint256 _code , string _titulo , string _description) onlyOwner public{
		notas[_code].titulo = _titulo;
		notas[_code].descripcion = _description;
		notas[_code].date = block.timestamp;
		logUpdateNota(_code , block.timestamp , _titulo , _description);
	}

	function deleteNota(uint256 _code) onlyOwner public{
		delete notas[_code];
		logDeleteNota(_code , block.timestamp);
	}

	function getNota(uint256 _code) constant public returns(uint256 id , string titulo , string descripcion , uint256 date){
		Notas memory p = notas[_code];
		return (p.id, p.titulo, p.descripcion, p.date);
	}

	event logCreateNota(uint256 indexed _id, uint256 indexed _date, string _titulo, string _description);
	event logUpdateNota(uint256 indexed _id, uint256 indexed _date, string _titulo, string _description);
	event logDeleteNota(uint256 indexed _id, uint256 indexed _date);
}
```


Ahora vamos a necesitar interactuar con nuestra Dapp , requerimos cargar una libreria llamada web3.
```html
<script src="https://code.jquery.com/jquery-1.10.2.js"></script>
<script src="https://cdn.jsdelivr.net/gh/ethereum/web3.js/dist/web3.min.js"></script>
```

Ahora necesitamos cargar un nodo eth , podemos cargar nuestro nodo o uno externo ya que existen muchos libres
```html
<script>
var web3 = new Web3(new Web3.providers.HttpProvider('https://ropsten.infura.io'));
</script>
```

Ahora cargaremos el `abi` , `abi` es el listado de metodos, variables y mas de tu contrato , lo puedes poner fijo o extraerlo, en este caso lo vamos extraer del contrato
```html
<script>
    var contractAddress = "0x00000000000";
    var ABI = '';
    var url = '//ropsten.etherscan.io/api?module=contract&action=getabi&address=' + contractAddress;
    $.getJSON(url, function (data) {
        if (data.status == '0') {
            console.log(data);
        } else {
            ABI = JSON.parse(data.result);
        }
    });
</script>
```

Voy a crear el metodo que es para crear una nota
```html
<script>
var myContract;
var myContractInstance;

function crearNota(_titulo , _description){
    web3.eth.getAccounts(function (err, accounts) {
        if(accounts.length > 0){
            var address = accounts[0];
            var isAddress = web3.isAddress(address);
            if(isAddress){
                myContract = web3.eth.contract(ABI);
                myContractInstance = myContract.at(contractAddress);
                var functiontoCall = 'myContractInstance.createNota';
                var params = "'"+_titulo+"' , '"+_description+"'";
                new Function(functiontoCall + "(" + params + ", function(err, res){ console.log(res); });")();
            }
        }
    });
}
crearNota("Hola" , "Quiero poner una nota aqui");
</script>
```



Voy a crear el metodo para editar la nota
```html
<script>

function updateNota(_id , _titulo , _description){
    web3.eth.getAccounts(function (err, accounts) {
        if(accounts.length > 0){
            var address = accounts[0];
            var isAddress = web3.isAddress(address);
            if(isAddress){
                myContract = web3.eth.contract(ABI);
                myContractInstance = myContract.at(contractAddress);
                var functiontoCall = 'myContractInstance.updateNota';
                var params = "'"+_id+"' , '"+_titulo+"' , '"+_description+"'";
                new Function(functiontoCall + "(" + params + ", function(err, res){ console.log(res); });")();
            }
        }
    });
}
updateNota(1 , "Hola" , "Quiero poner una nota aqui");
</script>
```

Voy a crear el metodo para eliminar la nota
```html
<script>

function deleteNota(_id){
    web3.eth.getAccounts(function (err, accounts) {
        if(accounts.length > 0){
            var address = accounts[0];
            var isAddress = web3.isAddress(address);
            if(isAddress){
                myContract = web3.eth.contract(ABI);
                myContractInstance = myContract.at(contractAddress);
                var functiontoCall = 'myContractInstance.deleteNota';
                var params = "'"+_id+"'";
                new Function(functiontoCall + "(" + params + ", function(err, res){ console.log(res); });")();
            }
        }
    });
}
deleteNota(1);
</script>
```


Voy a crear el metodo para listar una nota.
Para listar el objeto web3 necesitamos cargarlo en otra variable ya que hace conflicto con escritura
```html
<script>
var web3Read = new Web3(new Web3.providers.HttpProvider('https://ropsten.infura.io'));
function getNota(_id){
    var MyContract = web3Read.eth.contract(ABI);
    var myContractInstance = MyContract.at(contractAddress);
    var functionNametoCall = "myContractInstance.getNota";
    var params = "'"+_id+"'";
    var result = eval(functionNametoCall  + "(" + params + ");");
    console.log(result);
}
getNota(1);
</script>
```

`Con esto hemos terminado el modulo de dapps` , Como te parecio ?


# 6.- Polleo (es un nombre raro que le puse a una acción la cual es similar a walletnotify de bitcoin)

En este modulo veremos la opcion de recibir una notificacion al momento de que una direccion reciba una transaccion de eth , este modulo yo lo implemente por la wallet que estoy desarrollando ya que necesitaba detectar en tiempo real cuando alguien me enviara una transaccion , y pues aqui mas que nada es pura programacion , se trata de leer el bloque actual y detectar si mis direcciones estan en ese bloque y me da la cantidad que recibi , el hash, quien me la envio , fecha y muchas cosas mas, bueno manos ala obra, esto esta basado en `NodeJS`.

En esta parte cargamos la libreria web3 , y creamos la instancia a un nodo , en este caso usaremos la de infura , el metodo `web3.eth.getBlockNumber` me retorna el bloque actual , lo que yo hago es restarle 11 bloques al actual para comenzar a extraer desde alli.

```js
<script src="https://cdn.jsdelivr.net/gh/ethereum/web3.js/dist/web3.min.js"></script>

<script>
var web3 = new Web3(new Web3.providers.HttpProvider('https://ropsten.infura.io'));
var idBlock = 0;

web3.eth.getBlockNumber(function(e , b){ 
    idBlock = parseInt(b) - 11;
    getBlocks();
});
</script>
```

Ahora lo que sigue extraeremos las transacciones del bloque actual y compararemos si existen nuestras direcciones

```html

<script>

var miDireccion = '0x00000000000'

function getBlocks(){
    try{
        var Data = web3.eth.getBlock(idBlock , true);
    } catch(e){
        var Data = null;
    }
    if(Data != null && Data != undefined && Data.transactions){
        if(Data.transactions.length > 0){
            for(var key in Data.transactions){
                if(Data.transactions[key].to == miDireccion){
                    console.log(Data.transactions[key]);
                }
            }
        }
        idBlock++;
    }
    setTimeout(getBlocks , 2000);
    return false;    
}

</script>
```

# Con esto hemos terminado el taller.

Que tal te parecio el taller ?


Te gusto el taller ?

Si es de tu agrado felicidades y si no ps tambien jaja

Bueno me despido 

Yo soy Miguel Lomeli un chico hacker y programador desde hace 18 años

Mis datos de contacto son : 

E-mail : miguel@lomeli.io
FB : miguel.lomeli.mx
Whatsapp : 3314791340

[![N|Solid](https://lomeli.io/images/logo-default-125x45.png)](https://lomeli.io)

# Saludos y hasta la proxima


