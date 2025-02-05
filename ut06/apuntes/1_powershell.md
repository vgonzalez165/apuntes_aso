# UT04.- INSTALACIÓN Y PUESTA EN MARCHA DE WINDOWS SERVER


## Índice

- [UT04.- INSTALACIÓN Y PUESTA EN MARCHA DE WINDOWS SERVER](#ut04--instalación-y-puesta-en-marcha-de-windows-server)
  - [Índice](#índice)
  - [1.- Repaso de Powershell](#1--repaso-de-powershell)
    - [1.1- Introducción](#11--introducción)
      - [1.1.1- Conceptos importantes de Windows Powershell](#111--conceptos-importantes-de-windows-powershell)
      - [1.1.2.- Powershell vs Powershell Core](#112--powershell-vs-powershell-core)
      - [1.1.3.- Preparación del entorno](#113--preparación-del-entorno)
    - [1.2.- Conociendo los *cmdlets*](#12--conociendo-los-cmdlets)
      - [1.2.1.- Nomenclatura de cmdlets](#121--nomenclatura-de-cmdlets)
      - [1.2.2.- Obtención de ayuda](#122--obtención-de-ayuda)
      - [1.2.3.- Ayudas al escribir los comandos](#123--ayudas-al-escribir-los-comandos)
    - [1.3.- Control de la salida con pipelines](#13--control-de-la-salida-con-pipelines)
      - [1.3.1.- La canalización o pipeline](#131--la-canalización-o-pipeline)
      - [1.3.2.- Objetos en Powershell](#132--objetos-en-powershell)
    - [1.4.- Comandos para la manipulación de objetos](#14--comandos-para-la-manipulación-de-objetos)
      - [1.4.1.- Ordenando con el comando `Sort-Object`](#141--ordenando-con-el-comando-sort-object)
      - [1.4.2.- Agrupando con `Group-Object`](#142--agrupando-con-group-object)
      - [1.4.3.- Midiendo con el `Measure-Object`](#143--midiendo-con-el-measure-object)
      - [1.4.4.- Filtrado de objetos con `Where-Object`](#144--filtrado-de-objetos-con-where-object)
    - [1.5.- Aplicando formato a la salida](#15--aplicando-formato-a-la-salida)
      - [1.5.1.- Format-Wide](#151--format-wide)
      - [1.5.2.- Format-Table](#152--format-table)
      - [1.5.3.- Format-List](#153--format-list)


## 1.- Repaso de Powershell

### 1.1- Introducción

#### 1.1.1- Conceptos importantes de Windows Powershell

El diseño de Windows PowerShell integra conceptos de muchos entornos distintos. Algunos de estos conceptos son:

- **Los comandos no están basados en texto**: los cmdlets están diseñados para usar objetos: información estructurada que es más que una simple cadena de caracteres que se muestra en pantalla.
- **El conjunto de comandos es ampliable**: las interfaces como `cmd.exe` no proporcionan al usuario una manera de ampliar directamente el conjunto de comandos integrados. Los comandos nativos de PowerShell, denominados **cmdlets**, se pueden ampliar con cmdlets que crees o agregues mediante complementos o módulos.
- **PowerShell controla la entrada y la presentación de la consola**: cuando se escribe un comando la información es procesada y aplica un formato a los resultados que se muestren en pantalla. Esto es importante porque el usuario siempre puede hacer las cosas de la misma manera independientemente del cmdlet utilizado.
- **PowerShell utiliza sintaxis del lenguaje C#**: incluye palabras clave y funciones de sintaxis muy parecidas a las que se usan en el lenguaje C# por lo que su aprendizaje facilitaría el aprendizaje de C#.


#### 1.1.2.- Powershell vs Powershell Core

En el año 2018 Microsoft anunció una nueva edición de Powershell que denominó **Powershell Core** de forma que en la actualidad coexisten ambas ediciones. La diferencia más reseñable es que, mientras que Powershell es exclusivo de Windows, Powershell Core puede ser instalado en otros sistemas, como Mac OS X o Linux, lo que permite crear scripts compatibles para múltiples plataformas.

|               | Powershell        | Powershell Core   |
|---------------|-------------------|-------------------|
| Versiones     | 1.0 a 5.1         | 6.0 a 7.1         |
| Plataforma    | Solo Windows      | Windows, MAC OS y Linux |
| Dependencia   | .NET Framework    | .NET Core         |
| Comando       | powershell.exe    | pwsh.exe          |
| Políticas de actualización    | Solo correcciones de errores críticos | Todas las actualizaciones |


#### 1.1.3.- Preparación del entorno

Si vamos a trabajar con Powershell es conveniente preparar el entorno de trabajo. Por defecto, Windows 10 incluye Powershell, pero si queremos utilizar Powershell Core deberemos instalarlo manualmente. La última versión de esta herramienta siempre la podremos encontrar en el repositorio oficial disponible en GitHub, accesible en [web del proyecto de Github](https://github.com/PowerShell/PowerShell).

Tras instalarlo podremos acceder a uno u otro entorno ejecutando los comandos `powershell.exe` o `pwsh.exe`. Como trabajaremos con diversos terminales o intérpretes de comandos, una opción recomendable es instalar **Windows Terminal** (accesible desde la tienda), que permite integrar diversos terminales en diferentes pestañas.

Si quieres personalizar más Windows Terminal tienes múltiples guías por internet, por ejemplo [aquí](https://terminaldelinux.com/terminal/wsl/configurar-windows-terminal/) o [aquí](https://www.developerro.com/2020/11/18/custom-windows-terminal/).

Un complemento de Powershell es el editor **Powershell ISE**, un editor que ya viene instalado en Windows 10 y que permite el trabajo con scripts de Powershell. Sin embargo, este editor es bastante limitado y además, solo compatible con Powershell (no con Core). Por ello, en caso de querer realizar scripts de Powershell, lo ideal es utilizar el editor [**Visual Studio Code**](https://code.visualstudio.com/) con el [plugin de Powershell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). En la [documentación de Microsoft](https://docs.microsoft.com/es-es/powershell/scripting/dev-cross-plat/vscode/using-vscode?view=powershell-7.1) se explican los pasos a realizar para configurar este editor.

### 1.2.- Conociendo los *cmdlets*

#### 1.2.1.- Nomenclatura de cmdlets

Los cmdlets utilizan un sistema de nombres con la estructura **“verbo-sustantivo”**: el nombre de cada cmdlets consta de un verbo estándar y un sustantivo concreto. Los verbos expresan acciones concretas mientras que los sustantivos describen siempre a qué se aplica un comando. La idea detrás de esto es crear un entorno autodescriptivo y uniforme de forma que los comandos sean más fáciles de recordar para los usuarios y que les permita hacerse una idea de su objetivo a partir de su nombre. Por ejemplo, el comando `Stop-Computer` se puede identificar fácilmente como el comando que sirve para apagar el ordenador.

Esta uniformidad en la nomenclatura también es útil para conocer los comandos que afectan a un elemento determinado (nombre) o que realizan una tarea en concreto (verbo). Para ello debemos utilizar el comando `Get-Command` con los parámetros `-Noun` o `–Verb` respectivamente. Tras el parámetro indicamos el nombre o verbo que deseemos y nos mostrará todos los comandos que hay en el sistema que utilizan dicho nombre o verbo.

```powershell
PS C:\>Get-Command –Verb Get
PS C:\>Get-Command –Noun Service
```

Respecto a los parámetros que admiten los cmdlets se fomenta que estén **normalizados**, es decir, que sean descriptivos y que normalmente un parámetro con resultado similar sea el mismo para diferentes cmdlets. Los nombres de los parámetros se utilizan siempre con un guión (-) como prefijo para que Windows los identifique como tales. Por ejemplo, en `Get-Command –Name Clear-Host` el parámetro es `–Name`.

Hay una serie de parámetros que son comunes a todos los comandos. Probablemente el más importante de todos es `-?`, que muestra la ayuda del comando, pero hay otros muchos como: `Whatif`, `Confirm`, `Verbose`, `Debug`, `Warn`, `ErrorAction`, `ErrorVariable`, `OutVariable` y `OutBuffer`.


#### 1.2.2.- Obtención de ayuda

Powershell tiene cientos de cmdlets, cada uno con un gran número de parámetros. Esto hace que sea imposible conocerlos todos, por lo que tendremos que recurrir frecuentemente a la ayuda.

El primer sitio para recurrir a la ayuda es en la propia [Web de Microsoft](https://docs.microsoft.com/es-es/powershell/), aquí hay disponibles en castellano guías, manuales y recursos de todo tipo, por lo que es un sitio al que deberás acudir frecuentemente cuando tengas cualquier duda sobre Powershell.

Si queremos obtener información sobre un determinado comando también hay varias opciones para hacerlo desde la línea de comandos.En este caso, lo primero que habría que hacer es actualizar la ayuda para poder tener en nuestro equipo la última versión de la misma. Para ello simplemente debemos ejecutar el comando ```Update-Help``` y esperar unos minutos a que la descargue de Internet y la instale.

Hay tres comandos relativos a la ayuda en Powershell: `Get-Command`, `Get-Help` y `Get-Member`. El tercero lo vamos a dejar para más adelante, así que veamos como funcionan los dos primeros.


**El comando `Get-Help`**


Es el comando principal para buscar la ayuda de otro comando. Simplemente necesitamos pasarle el nombre del otro comando con el parámetro ```-name``` para que nos proporcione la ayuda completa de dicho comando.

```powershell
PS C:\> Get-Help -Name Get-Process
```

La información que nos muestra la ayuda de un comando es la siguiente:
- Nombre del comando
- Sinopsis
- Sintaxis
- Descripción 
- Vínculos relacionados
- Comentarios

Aunque eso puede parecer mucha información, la realidad es que las entradas de la ayuda tienen muchos más apartados que 
nos pueden ser interesantes, como para qué sirven los parámetros o ejemplos de utilización. Si queremos una salida completa de la ayuda entonces tendremos que utilizar el parámetro `-full`.

```powershell
PS C:\> Get-Help -Name Get-Process -Full
```

Como se puede apreciar, ahora la ayuda obtenida es bastante más extensa, incluyendo también una explicación detallada de cada parámetro, ejemplos de uso, …

Un comando equivalente pero que muestra la ayuda página a página es `Help`. El resultado es muy similar con la diferencia de que ahora se muestra página a página por pantalla, requiriendo la pulsación de la barra espaciadora para pasar a la siguiente página y pudiendo navegar utilizando las teclas de cursor.

El comando `Get-Help` tiene otros muchos parámetros, lo más útiles son:
- `-Examples`: muestra únicamente ejemplos de uso del comando indicado por name
- `-Online`: abre la ayuda en el avegador Web
- `-ShowWindow`: muestra la ayuda en una ventana emergente.
- `Parameter Nombre`: muestra únicamente del parámetro cuyo nombre se indique

**El comando `Get-Command`**

Este comando nos servirá para encontrar otros comandos. Powershell tiene cientos e incluso miles de cmdlets, por lo que es casi imposible conocer el nombre de todos. El comando `Get-Command` nos ayudará a encontrar el comando que estemos buscando.

Si lo ejecutamos directamente, sin parámetros, mostrará todos los comandos disponibles en nuestro sistema, Pero cuando es realmente útil este comando es al utilizar los parámetros `-Verb` y `-Noun`. Hay que recordar que la nomenclatura de todos los cmdlets es consistente, utilizando todos ellos una combinación de verbo y nombre, es decir, qué hace (verbo) y con qué lo hace (nombre). Por ejemplo, el comando `Get-Help` es el comando que obtiene (Get) la ayuda (Help).

Por tanto, podemos obtener una lista de todos los comandos que tengan un nombre o verbo determinado utilizando los parámetros anteriores. Por ejemplo:

```powershell
PS C:\> Get-Command -Noun Process
```

Con el comando anterior podemos ver todos los cmdlets que realizan alguna operación con procesos.


#### 1.2.3.- Ayudas al escribir los comandos

La primera herramienta de la consola para buscar comandos es autocompletar, que consiste en rellenar el comando que queremos al pulsar la **tecla tabulador** después de haber tecleado las primeras letras de un comando. Si hay un único comando que comience con esas teclas lo completará, si hubiera más de uno, completará con el primero en orden alfabético y, tras cada nueva pulsación de la tecla tabulador, irá mostrando el resto de los comandos que comiencen por dichas letras.

El uso del tabulador también es útil si no conocemos los parámetros de un comando, si tras el guión que precede a todos los parámetros pulsamos la tecla tabulador ira iterando sobre todos los parámetros que admite el comando.

Cuando queremos recurrir a algún comando que hayamos introducido recientemente, podemos utilizar las teclas de cursor. Cada vez que tecleemos cursor arriba navegará hacia atrás en el historial mostrando el comando previo al que se ve en pantalla. De forma análoga, el cursor abajo avanzará al siguiente comando.
Pero, si queremos más opciones para trabajar con el historial, tenemos una serie de comando relacionados con el historial.

El primero de ellos es `Get-History`, que mostrará numerados todos los comandos que hemos introducido previamente en la sesión actual. Los parámetros más relevantes para este comando son:

- `-Id`: Si pasamos como identificador un valor numérico correspondiente a una orden del historial nos mostrará únicamente esa orden.
- `-Count`: Por defecto se muestran todos los comandos desde que se inició la sesión. Con este parámetro podemos indicar cuántos comandos se mostrarán, bien desde el último introducido o bien desde el comando indicado si se utiliza en combinación con el parámetro `-Id`.
  
Si queremos volver a ejecutar un comando que ya está en el historial podemos utilizar el cmdlet `Invoke-History`. Podemos ejecutarlo sin parámetros, en cuyo caso volverá a ejecutar el último comando del historial, o utilizar el parámetro `-Id` para indicar el número de entrada del historial que se ejecutará.

Para agilizar la ejecución de comandos del historial podemos utilizar `r`, que es el alias de `Invoke-History`. De igual manera, un alias para `Get-History` es `h`.

Si queremos buscar dentro del historial de una forma rápida, tenemos las combinaciones de teclas `Ctrl-R` y `Ctrl-S`, la primera para buscar hacia atrás y la segunda para buscar hacia adelante. Cuando pulsamos `Ctrl-R` cambiará el prompt y nos pedirá que introduzcamos un texto. Al introducirlo, se mostrará el último comando ejecutado que contenga dicho texto. Con cada nueva pulsación de `Ctrl-R` retrocederemos en el historial buscando más comandos que contengan el texto, mientras que si pulsamos las teclas `Ctrl-S` avanzaremos en el historial.

Algo que hay que destacar respecto a la búsqueda en el historial es que se obtendrán resultados de la propia sesión o de anteriores, mientras que el comando `Get-History` solo muestra las entradas de la sesión actual.

Si queremos borrar el historial de nuestra máquina, solo tenemos que ejecutar el comando `Clear-History` que vacía por completo el historial. Sin embargo, podemos ser más precisos utilizando algunos de los parámetros disponibles. Podemos borrar entradas individuales con el parámetro `-Id`. Por ejemplo, si queremos borrar las entradas con identificador 5 y 9 ejecutaríamos la siguiente orden.

```powershell
PS C:\> Clear-History -Id 5, 9
```

También podemos eliminar un número concreto de entradas con el parámetro `-Count`. En el siguiente ejemplo, se eliminan las 5 entradas anteriores a la que tiene como identificador el número 11, mientras que en el segundo ejemplo se eliminan las últimas 5 entradas al combinarlo con el parámetro `-Newest`.

```powershell
PS C:\> Clear-History -Count 5 -Id 11
PS C:\> Clear-History -Count 5 -Newest
```

Por último, se pueden filtrar las entradas eliminadas según contengan una cadena usando el parámetro `-CommandLine`. En el siguiente ejemplo se eliminan todas las entradas del historial que contienen el texto Help y también las que finalizan con el texto Newest.

```powershell
PS C:\> Clear-History -CommandLine *Help*, *Newest
```

Otra utilidad interesante para realizar con el historial es exportarlo para posteriormente importarlo en una máquina diferente o en la misma máquina en otra sesión. Para conseguir esto debemos exportar el historial a un fichero, que podrá ser en formato XML o CSV y posteriormente importarlo en el otro equipo.

Para exportarlo podemos usar una de las siguientes órdenes, la primera si queremos utilizar el formato XML y la segunda para el caso de querer utilizar el formato CSV.

```powershell
PS C:\> Get-History | Export-Clixml -Path ‘C:\history.xml’
PS C:\> Get-History | Export-CSV -Path ‘C:\history.csv’
```

Si tienes curiosidad, puedes ver el contenido del fichero exportado con el comando `Get-Content`, que muestra el contenido de un archivo de texto.

```powershell
PS C:\> Get-Content ‘C:\history.xml’
```

Para importarlo, el proceso es similar, pero utilizando el comando Add-History de la siguiente forma.

```powershell
PS C:\> Import-Clixml -Path ‘C:\history.xml’ | Add-History
PS C:\> Import-CSV -Path ‘C:\history.csv’ | Add-History
```






### 1.3.- Control de la salida con pipelines

#### 1.3.1.- La canalización o pipeline

El **pipeline** o la **canalización**, representado por el símbolo barra vertical (`|`), es utilizado para combinar diversos cmdlets de forma que la salida de uno es enviada a la entrada del siguiente, de forma muy similar a cómo se hace en Linux. Esto permite enlazar varios comandos en una especie de flujo de datos en el que cada uno de los comandos realiza algún tipo de operación sobre los datos generados por el comando anterior. 

La sintaxis es de la forma:

```powershell
Comando1 | Comando2 | Comando3
```

En este ejemplo, la salida de `Comando1`es enviada a `Comando2`, que realizará algún tipo de operación con esos datos y enviará la salida generada a `Comando3`. Como este ya es el último comando, su salida será enviada a la consola para ser mostrada al usuario.

Veamos un ejemplo sencillo de utilización de la canalización.

```powershell
C:\> Get-Process notepad | Stop-Process
```

Aquí, el primer comando de la canalización buscará todos los procesos que hay en el sistema que se llamen `notepad`, es decir, todas las instancias del bloc de notas que tengamos abiertas. Este listado de procesos lo enviará al siguiente comando de la canalización, `Stop-Process`, cuyo cometido es finalizar un proceso. Por tanto, tomará cada proceso del listado que recibe y lo cerrará.



#### 1.3.2.- Objetos en Powershell

Un aspecto importante al trabajar con la canalización, sobre todo si has trabajado antes con Linux, es que Powershell es un Shell **orientado a objetos**, y, por tanto, la información que se intercambia entre los comandos no son meras cadenas de caracteres, sino que son objetos.

Un **objeto** se puede entender como una abstracción de un elemento, por ejemplo, un usuario, un proceso o un fichero. Cada objeto tiene una serie de **propiedades**, que son las características intrínsecas de dicho objeto, y una serie de **métodos**, que son las acciones que se pueden realizar sobre dicho objeto.

Por ejemplo,supongamos que MS-DOS ejecutamos la orden:

```powershell
C:\> dir c:\datos\*.txt
```

Cuando ejecutamos esa orden el sistema mira el contenido del directorio `C:\datos` buscando los ficheros que tengan extensión `txt` y, a partir del mismo, genera una salida por pantalla, es decir, una secuencia de caracteres, que muestran el contenido de dicho directorio. Si quisiéramos realizar algún tipo de operación automatizada con la salida de este comando, tendremos que trabajar con esa secuencia de caracteres que ha generado. Por ejemplo, podríamos guardar todos esos caracteres en un fichero, o filtrarlos para que muestre todas las líneas que contengan una determinada cadena, pero solo operaciones que trabajen con esa cadena de texto.

Veamos ahora la orden equivalente en Powershell, que será:

```powershell
C:\> Get-Item C:\datos\*.txt
```

Si la ejecutamos veremos que la salida es muy similar a la del comando anterior, pero, sin embargo, lo que hace es muy diferente. El comando obtiene el listado de ficheros del disco, pero lo hace como una **lista de objetos**, cada uno de los cuales representa un fichero. Una vez que tiene esta lista de objeto mira a ver que hay que hacer con ellos: si tiene que enviárselos a otro comando mediante el pipeline (como veremos un poco más adelante) le enviará la lista de objetos; en cambio, si tiene que mostrar la salida por pantalla tomará esos objetos y elegirá la representación más adecuada para mostrársela al usuario, momento en el que dejarán de ser un conjunto de objetos para ser una secuencia de caracteres.

Podemos saber qué tipo de objetos devuelve un comando de Powershell, así como el listado completo de propiedades y métodos del mismo,  canalizando la salida al comando `Get-Member`.

```powershell
PS C:\> Get-LocalUser -Name Victor| Get-Member

   TypeName: Microsoft.PowerShell.Commands.LocalUser

Name                   MemberType Definition
----                   ---------- ----------
Clone                  Method     Microsoft.PowerShell.Commands.LocalUser Clone()
Equals                 Method     bool Equals(System.Object obj)
GetHashCode            Method     int GetHashCode()
GetType                Method     type GetType()
ToString               Method     string ToString()
AccountExpires         Property   System.Nullable[datetime] AccountExpires {get;set;}
Description            Property   string Description {get;set;}
Enabled                Property   bool Enabled {get;set;}
FullName               Property   string FullName {get;set;}
LastLogon              Property   System.Nullable[datetime] LastLogon {get;set;}
...

```

En el ejemplo anterior se puede ver que el comando `Get-LocalUser` devuelve objetos de tipo `Microsoft.Powershell.Commands.LocalUser`. Estos objetos representan cada uno de los usuarios del sistema, y por tanto, cada objeto tendrá una serie de propiedades que definen al usuario y también de métodos que permiten realizar operaciones sobre dicho usuario. 

Por ejemplo, la propiedad `Enabled` almacenará un valor booleano (que puede ser verdadero o falso) que indicará si la cuenta está habilitada o no. Hay muchas formas de ver el valor de la propiedad de un objeto, una de ellas es rodeando el comando que genera el objeto entre paréntesis y poniendo a continuación el nombre de la propiedad que queremos obtener separándola del comando con el carácter punto (`.`).


```powershell
PS C:\> (Get-LocalUser -Name Victor).Enabled
True
```
 
Aparte de las propiedades, los objetos en Powershell pueden tener otros tipos de elementos. El tipo de cada elemento se puede ver en la columna `MemberType`, siendo los más relevantes:

- **Property**: son las propiedades del objeto, por ejemplo, el nombre del proceso o su identificador.
- **Method**: los métodos permiten realizar algún tipo de operación con el objeto, por ejemplo, el método ```kill()``` mataría el objeto. 
- **AliasProperty**: un nombre alºternativo para una propiedad, normalmente más conciso, por ejemplo, **Name** contiene el mismo valor que la propiedad **ProcessName**.

Es posible mostrar únicamente los elementos de un determinado tipo con el parámetro `-MemberType` del comando `Get-Member`.

```powershell
PS C:\> Get-LocalUser -Name Victor | Get-Member -MemberType Properties

   TypeName: Microsoft.PowerShell.Commands.LocalUser

Name                   MemberType Definition
----                   ---------- ----------
AccountExpires         Property   System.Nullable[datetime] AccountExpires {get;set;}
Description            Property   string Description {get;set;}
Enabled                Property   bool Enabled {get;set;}
FullName               Property   string FullName {get;set;}
LastLogon              Property   System.Nullable[datetime] LastLogon {get;set;}
...
```

Si nos fijamos vemos que los objetos devueltos por el comando ```Get-LocalUser``` tienen varias decenas de propiedades. Sin embargo, cuando lo ejecutamos únicamente podemos ver por pantalla unas pocas: el nombre, si está habilitado el usuario y la descripción.

```powershell
PS C:\Users\victor> Get-LocalUser

Name               Enabled Description
----               ------- -----------
Administrador      False   Cuenta integrada para la administración del equipo o dominio
DefaultAccount     False   Cuenta de usuario administrada por el sistema.
Invitado           False   Cuenta integrada para el acceso como invitado al equipo o dominio
victor             True
WDAGUtilityAccount False   Una cuenta de usuario que el sistema administra y usa para escenarios de Protección de apli…
```

Esto se debe a que Powershell solo nos muestra por pantalla las propiedades que considera más relevantes para aumentar la legibilidad. Si queremos decidir qué propiedades queremos, debemos hacer uso del comando ```Select-Object``` que permite filtrar un objeto seleccionando únicamente un subconjunto de las propiedades.

```powershell
PS C:\> Get-LocalUser | Select-Object Name, SID

Name               SID
----               ---
Administrador      S-1-5-21-236780772-618523378-4141520374-500
DefaultAccount     S-1-5-21-236780772-618523378-4141520374-503
Invitado           S-1-5-21-236780772-618523378-4141520374-501
victor             S-1-5-21-236780772-618523378-4141520374-1001
WDAGUtilityAccount S-1-5-21-236780772-618523378-4141520374-504
```

Si lo que se desea es ver todas las propiedades de los objetos se puede utilizar el carácter comodín (`*`), que fuerza a que se muestren todas las propiedades.

```powershell
PS C:\> Get-LocalUser -Name Victor | Select-Object *

AccountExpires         :
Description            :
Enabled                : True
FullName               :
PasswordChangeableDate : 29/09/2021 16:26:41
PasswordExpires        :
UserMayChangePassword  : True
PasswordRequired       : False
PasswordLastSet        : 29/09/2021 16:26:41
LastLogon              : 27/10/2021 10:30:53
Name                   : victor
SID                    : S-1-5-21-236780772-618523378-4141520374-1001
PrincipalSource        : Local
ObjectClass            : User
```

### 1.4.- Comandos para la manipulación de objetos

Hay comandos que proporcionan un gran número de elementos al ejecutarse por lo que puede ser conveniente manipular dicha salida para que sea más fácilmente comprensible. Para ello utilizaremos los cmdlets `Sort-Object`, `Group-Object` y `Measure-Object`, que ordenan, agrupan y cuentan respectivamente.


#### 1.4.1.- Ordenando con el comando `Sort-Object`

El comando `Sort-Object` admite un conjunto de objetos como entrada y devuelve ese mismo conjunto de objetos pero **ordenados** según el valor de la propiedad que se indique. 

Por ejemplo, para ordenar los procesos que se están ejecutando actualmente en el sistema ordenados por uso de CPU ejecutaríamos la orden:

```powershell
PS C:\> get-process | Sort-Object cpu

 NPM(K)    PM(M)      WS(M)     CPU(s)      Id  SI ProcessName
 ------    -----      -----     ------      --  -- -----------
     12     3,74       2,68       0,00    1616   0 amdfendrsr
     19     6,27       9,71       0,00    1908   0 svchost
     13     3,62       6,50       0,00    1964   0 svchost
     14    17,88      20,35       0,00    2064   0 svchost
...
```

El parámetro más relevante de este comando es `-Descending`, que ordena la salida de mayor a menor.


#### 1.4.2.- Agrupando con `Group-Object`

El comando `Group-Object` recoge un conjunto de objetos y, en lugar de mostrarlos todos secuencialmente, los agrupa en función del valor de la propiedad que indiquemos.

Veámoslo con un ejemplo. El comando `Get-Service` nos devuelve todos los servicios que hay en el sistema de la siguiente forma:

```powershell
PS C:\> Get-Service

Status   Name               DisplayName
------   ----               -----------
Stopped  AarSvc_1f230e      Agent Activation Runtime_1f230e
Running  AdobeARMservice    Adobe Acrobat Update Service
Stopped  AJRouter           Servicio de enrutador de AllJoyn
Stopped  ALG                Servicio de puerta de enlace de nivel…
```

Si quisiéramos saber cuántos están actualmente en ejecución tendríamos que contarlos manualmente. La mejor alternativa es utilizar `Group-Object` para agruparlos por la propiedad `status`, lo que los agrupará en función del valor de estado de cada servicio y además nos indicará cuántos hay en cada grupo.

```powershell
PS C:\> Get-Service | Group-Object -Property status

Count Name                      Group
----- ----                      -----
  168 Stopped                   {AarSvc_1f230e, AJRouter, ALG, AppIDSvc…}
  103 Running                   {AdobeARMservice, AMD Crash Defender Service, AMD External Events Utility, Appinfo…}
```

Incluso podríamos indicar más de una propiedad para que agrupe en función de las diferentes combinaciones de los valores de dicha propiedad.

```powershell
PS C:\> Get-Service | Group-Object -Property Status,StartType

Count Name                      Group
----- ----                      -----
    6 Stopped, Automatic        {dbupdate, edgeupdate, gpsvc, gupdate…}
  150 Stopped, Manual           {AarSvc_1f230e, AJRouter, ALG, AppIDSvc…}
   11 Stopped, Disabled         {AppVClient, DialogBlockingService, MsKeyboardFilter, NetTcpPortSharing…}
   61 Running, Automatic        {AdobeARMservice, AMD Crash Defender Service, AMD External Events Utility, AudioEndpoi…
   43 Running, Manual           {Appinfo, AppXSvc, BthAvctpSvc, cbdhsvc_1f230e…}
```

#### 1.4.3.- Midiendo con el `Measure-Object`

Este comando realiza cálculos con los valores de las propiedades de un objeto. En el caso de propiedades numéricas puede calcular el mínimo, el máximo, la suma y el promedio. En el caso de propiedades de tipo texto puede contar y calcular el número de líneas, palabras y caracteres.

```powershell
PS C:\> Get-ChildItem | Measure-Object -Property Length -Minimum -Maximum -Average

Count             : 4
Average           : 1242
Sum               :
Maximum           : 3572
Minimum           : 58
StandardDeviation :
Property          : Length
```

Por ejemplo, el comando anterior muestra el tamaño máximo, mínimo y medio de todos los ficheros que se encuentran en un directorio.


#### 1.4.4.- Filtrado de objetos con `Where-Object`

Muchos comandos disponen de parámetros para filtrar la salida en función del valor de alguna propiedad. Por ejemplo, se puede utilizar el parámetro `-Name` del comando `Get-Process` para que solo nos devuelva los procesos que tengan un nombre determinado.

```powershell
PS C:\> Get-Process -Name calculator

 NPM(K)    PM(M)      WS(M)     CPU(s)      Id  SI ProcessName
 ------    -----      -----     ------      --  -- -----------
     30    21,46      28,66       0,73   14060   1 Calculator
```

Pero esta opción solo está disponible en algunos comandos y para propiedades muy determinadas. Cuando se desea un mayor control en el filtrado de los objetos proporcionados por un comando lo ideal es enviar dicha salida por el pipeline al comando `Where-Object`, el cual admite un gran número de criterios para filtrar.

Los operadores de comparación que soporta el comando se muestran en la siguiente tabla.

| Operador        | Significado       | Operador        | Significado       |
|-----------------|-------------------|-----------------|-------------------|
| `-eq`           | Es igual a        | `-ne`           | No es igual a     |
| `-lt`           | Es menor que      | `-gt`           | Es mayor que      |
| `le`            | Es menor o igual que  | `-ge`       | Es mayor o igual que    |
| `-like`         | Es como           | `-notlike`      | No es como        |

Se pueden utilizar dos sintaxis diferentes para indicar el filtro en este comando: mediante *scriptblocks* o indicando directamente las comparaciones en los parámetros de `Where-Object`. La primera opción es válida para todas las versiones de Powershell, aunque tiende a hacer menos legible el código, la segunda opción es la recomendada porque aumenta la legibilidad, pero solo está disponible a partir de la versión 3 de Powershell.

Veamos como mostraríamos todos los procesos que corresponden a la calculadora con ***scriptblocks**.

```powershell
PS C:\> Get-Process | Where-Object -FilterScript { $_.Name -eq “Calculator” }
```

Lo más destacable de esta sintaxis es:

- Las comparaciones se incluyen entre llaves, en lo que se llama un bloque de script
- El bloque de script se indica mediante el parámetro `-FilterScript`, sin embargo, esto es opcional y podríamos omitir el nombre del parámetro.
- La variable `$_` hace referencia a cada uno de los objetos que recibe. Es decir, el comando `Get-Process` devuelve un conjunto de objetos, los cuales comprueba mediante el filtro de `Where-Object` en sucesivas iteraciones. La forma de hacer referencia a cada uno de los objetos en cada iteración es mediante la variable `$_`.

La otra forma de hacerlo se muestra en el siguiente código, donde se puede ver que no es necesario incluir llaves ni ningún parámetro:

```powershell
PS C:\> Get-Process | Where-Object Name -eq “Calculator”

 NPM(K)    PM(M)      WS(M)     CPU(s)      Id  SI ProcessName
 ------    -----      -----     ------      --  -- -----------
     31    24,09      42,77       0,78   20804   1 Calculator
```

Respecto a qué operadores usar en cada situacion, los operadores de comparación (mayor y menor) se utilizan con **propiedades numéricas**. Por ejemplo, si queremos obtener la lista de objetos cuyo consumo de CPU ha sido superior a los 120 segundos deberíamos introducir la siguiente orden:

```powershell
PS C:\> Get-Process | Where-Object CPU -gt 100

 NPM(K)    PM(M)      WS(M)     CPU(s)      Id  SI ProcessName
 ------    -----      -----     ------      --  -- -----------
     23     7,12       8,47     137,81    2120   1 AdobeCollabSync
     19     7,87      13,88     167,69    7528   1 ctfmon
    126   189,64     143,66   1.053,48    7828   1 explorer
     62   188,28     128,72     101,16    3732   1 firefox
```

Los operadores `-like` y `-notlike` se utilizan para realizar comparaciones de cadenas utilizando comodines. Podemos utilizar dos comodines:

- **Asterisco**: representa una cadena de cualquier longitud que también puede ser la cadena vacía.
- **Interrogación**: representa un único carácter.
- 
Por ejemplo, si queremos localizar el proceso correspondiente al programa *Word* y no sabemos exactamente como se llama podemos hacer una búsqueda de la siguiente forma, representando cadenas que contengan la cadena *word* en su interior y que pueden tener cualquier cadena tanto antes como después.

```powershell
PS C:\> Get-Process | Where-Object Name -like *word*

 NPM(K)    PM(M)      WS(M)     CPU(s)      Id  SI ProcessName
 ------    -----      -----     ------      --  -- -----------
     67   112,89     154,90      86,31   16732   1 WINWORD
```


### 1.5.- Aplicando formato a la salida

Cuando ejecutamos un comando en Powershell vemos un texto con la salida del comando, pero en realidad cualquier comando devuelve uno o varios objetos y lo que se nos muestra por pantalla son las propiedades más relevantes de dichos objetos en un formato predeterminado. Sin embargo, es posible modificar tanto las propiedades que se nos muestran de cada objeto, como el formato en que se hace.
Powershell tiene cinco cmdlets para el **formato de la salida** de comandos, de los que nosotros veremos `Format-List`, `Format-Table` y `Format-Wide`.


#### 1.5.1.- Format-Wide

Este comando muestra únicamente una propiedad de cada objeto, mostrando todos estos valores en una tabla. Como alternativa más breve se puede utilizar el alias `fw`.
Los parámetros más destacables de este comando son:
•	`-Property`: para indicar si queremos que se muestre una propiedad diferente a la que muestra por defecto.
•	`-Column`: que mediante un valor numérico señalará cuántas columnas tendrá la tabla con los resultados mostrados.

#### 1.5.2.- Format-Table

Formatea la salida del comando redireccionado en forma de tabla donde mostrará un objeto en cada fila y las propiedades más relevante en las columnas. El alias para este comando es `ft`.
Un parámetro útil con `Format-Table` es `-AutoSize`, que adapta el tamaño de la salida para que se ajuste al tamaño disponible en la pantalla y así evitar que se recorte la salida.


#### 1.5.3.- Format-List

Por último, `Format-List` muestra la salida del comando como una lista de propiedades, indicando cada una de estas propiedades en una línea diferente.


 








