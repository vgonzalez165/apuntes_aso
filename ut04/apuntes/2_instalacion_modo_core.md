# UT04.- INSTALACIÓN Y PUESTA EN MARCHA DE WINDOWS SERVER


## 2.- Versión Server Core

### 2.1.- Instalación Core

Una decisión que hay que tomar en la instalación es si instalar la versión con entorno gráfico o si es suficiente con la versión **Server Core** en línea de comandos. Hay que tener en cuenta que muchos servidores están dedicados a un rol particular y un entorno gráfico únicamente sirve para consumir recursos sin aportar ninguna ventaja.  

Si escogemos la versión Server Core obtendremos una versión desnuda del sistema operativo: no hay menú de inicio, no hay interfaz gráfica ni consola de administración (MMC, Microsoft Management Console) y, por supuesto, ninguna aplicación gráfica (con algunas excepciones, por ejemplo, podemos acceder al administrador de tareas ejecutando el comando `taskmgr`).

Esta carencia de elementos gráficos nos puede proporcionar numerosas ventajas:

- **Conservación de recursos hardware**. Muchos elementos de la GUI requieren grandes cantidades de memoria, así como capacidad de procesamiento. No tener interfaz gráfica permite dedicar estos recursos a otros servicios más importantes para el servidor.
- **Ahorro de espacio en disco**. La instalación Server Core requiere menos espacio en disco.
- **Reducción de la frecuencia de instalación de actualizaciones**. Los elementos gráficos de Windows son los que más frecuentemente requieren actualizaciones por lo que su eliminación implicará una menor frecuencia de instalación de actualizaciones. Menos actualizaciones conllevan menos reinicios del servidor y por tanto menos tiempo sin servicio (alta disponibilidad).
- **Reducción de la superficie de ataque**. Cuantas menos aplicaciones se estén ejecutando en el sistema menos puntos de entrada habrá para un potencial atacante. 

En Windows Server 2012R2 y 2016, es posible alternar cambiar entre el modo core y el modo con GUI después de haber instalado el sistema operativo. Además, permite un tercer modo denominado **Minimal Server Interface**, que mantiene únicamente algunos elementos de la interfaz gráfica, eliminando aquellos que más recursos consumen.

Entre los elementos eliminados se incluyen Internet Explorer, el escritorio, el explorador de ficheros y algunos elementos del Panel de Control como *Programas y Características*, *Dispositivos e impresoras*, *Windows Update* o *Fuentes*. Algunas de las aplicaciones que se mantienen son la consola de administración MMC (Microsoft Management Console), el Administrador de Dispositivos y toda la interfaz de Windows PowerShell.

Algo importante es que **en Windows Server 2019 se eliminó la opción de alternar entre los modos en un sistema operativo ya instalado**, por lo que es una decisión irrevocable que hay que tomar durante la instalación del sistema.

### 2.2.- Activar y desactivar experiencia de escritorio (solo hasta Windows Server 2016)

Hay dos formas de alternar estos dos modos (sólo válido para Windows Server 2012R2 y 2016): desde el Administrador del Servidor o mediante Powershell.

#### 2.2.1.- Habilitar Server Core desde el Administrador del servidor

Para alternar entre interfaz con GUI, Server Core y Minimal Server Interface lo podemos hacer desde *Administrador de servidor -> Administrar -> Quitar roles y funciones ->Infraestructura e interfaces de usuario*. Ahí tenemos estas opciones:

- **Infraestructura y herramientas de administración**: corresponde a la interfaz mínima por lo que es el único checkbox que habría que dejar marcado.
- **Shell gráfico de servidor**: habilitar esta casilla implica la instalación de la interfaz gráfica completa.
- **Experiencia de escritorio**: incluye características propias de Windows 8 tales como Windows Media o los temas de escritorio.
 

#### 2.2.2- Habilitar la GUI desde la línea de comandos con Powershell

Realizar el proceso contrario es algo más complicado ya que debes tener en cuenta que no tendrás entorno gráfico por lo que deberá realizarse a través de la línea de comandos.  Para ello utilizaremos PowerShell.

Para entrar en PowerShell utilizamos introducimos la siguiente orden:

```powershell
C:\>Powershell
```

Esto nos llevará a PowerShell. Fíjate que la única diferencia que apreciarás es que aparecen los caracteres **PS** al principio del prompt. La orden para añadir la interfaz gráfica es:

```powershell
PS C:\>Add-WindowsFeature Server-Gui-Shell, Server-Gui-Mgmt-Infra
```

Como habrás podido deducir la orden `Add-WindowsFeature` sirve para instalar características a Windows. Los parámetros que se pasan corresponden a las características que queremos instalar. El primero corresponde a la interfaz gráfica completa y el segundo a la interfaz mínima.

Por ejemplo, en la siguiente captura se va a pasar de Server Core a la interfaz mínima.

```powershell
C:\>powershell
Windows PowerShell
Copyright (C) 2013 Microsoft Corporation. Todos los derechos reservados.

PS C:\> Add-WindowsFeature Server-Gui-Mgmt-Infra

Success Restart Needed Exit Code       Feature Result
------- -------------- ---------       --------------
True    Yes            Success Rest... (Infraestructura y herramientas de adm...
ADVERTENCIA: Debe reiniciar este servidor para finalizar el proceso de
instalación.
ADVERTENCIA: La actualización automática de windows no está habilitada. Para
asegurarse de que la característica o el rol recién instalados se actualicen
automáticamente, active Windows Update en el Panel de control.
```

Tras ejecutar la orden debemos reiniciar el servidor. La orden para reiniciar el servidor en PowerShell es:

```powershell
PS C:\>Shutdown –r –t 0
```

Algunos parámetros útiles del comando Shutdown:

- `-r`: indica que el equipo debe reiniciarse tras el apagado.
- `-s`: el equipo se apaga.
- `-t` X: especifica el tiempo que ha de pasar desde que se ejecuta la orden hasta que se apaga el equipo. Esto es útil en entornos en producción ya que así se da tiempo a los usuarios conectados para que cierren las aplicaciones de forma que no pierdan los datos.



### 2.3.- Tareas de post-instalación en modo Core


Cuando estamos trabajando en Server Core estas tareas son algo más complicadas ya que debemos realizarlas utilizando la línea de comandos.


#### 2.3.1.- Cambio del nombre del equipo

Si queremos saber el nombre que tiene actualmente el servidor podemos utilizar el comando `hostname` o `ipconfig`.

Para cambiar el nombre del equipo desde la línea de comandos debes utilizar el comando `netdom` de la siguiente manera:

```powershell
C:\>netdom renamecomputer %ComputerName% /NewName: <NewComputerName>
```

Recuerda que las cadenas rodeadas del símbolo % son variables. En concreto `%ComputerName%` es una variable de entorno definida por el propio sistema que almacena el nombre actual del equipo.

También puedes cambiarlo desde PowerShell mediante el Cmdlet `Rename-Computer`. Los parámetros de este Cmdlet son:

- `-ComputerName`: nombre del equipo que queremos modificar (si no lo ponemos el valor por defecto es la máquina local)
- `-NewName`: nuevo nombre del equipo.
- `-Restart`: reinicia automáticamente el equipo para aplicar los cambios.

```powershell
PS C:\>Rename-Computer –NewName <NewComputerName> -Restart
```


#### 2.3.2.-Establecer una dirección IP estática

El siguiente paso para realizar es cambiar la IP dinámica que tiene el sistema instalado por defecto por una IP estática. Si realizamos esta tarea desde la línea de comandos debemos recurrir a PowerShell.

Los pasos que hay que realizar para asignar una dirección IP estática son los siguientes:

1.	En primer lugar, hay que ejecutar el Cmdlet `Get-NetIPInterface` para obtener información de las interfaces de red. De la salida de este comando nos tenemos que fijar en el valor de la columna **IfIndex** de la interfaz que queremos configurar. Ese valor será el que utilizaremos posteriormente para referencia el adaptador de red.
2.	Para cambiar la configuración de red utilizamos el Cmdlet `New-NetIPAddress` que puede tener los siguientes parámetros:
    - `-InterfaceIndex`: aquí indicamos el valor que obtuvimos en primer paso.
    - `-IPAddress`: dirección estática que se quiere establecer.
    - `-PrefixLenght`: longitud del prefijo, es decir, la máscara de subred en formato numérico (p.e. 24)
    - `-DefaultGateway`: puerta de enlace predeterminada
Así pues, la llamada quedaría de la forma:

```powershell
PS C:\>New-NetIPAddress –InterfaceIndex 12 –IPAddress 192.168.10.20 –PrefixLength 24 –DefaultGateway 192.168.10.1
```
3.	Ahora queda configurar los servidores DNS  si es que nuestro servidor va a tener salida a Internet. Para ello utilizaremos el Cmdlet Set-DNSClientServerAddress que admite los parámetros:
- `-InterfaceIndex`: igual que en el caso anterior
- `-ServerAddresses`: dirección IP del servidor DNS. Admite varios servidores separados por comas.

```powershell
PS C:\>Set-DNSClientServerAddress –InterfaceIndex 12 –ServerAddresses 8.8.8.8,8.8.4.4
```

Hay que tener en cuenta que el comando `New-NetIPAddress` crea una nueva IP y la añade a un adaptador, por lo que si este adaptador de red ya tiene una dirección IP no la sustituirá, sino que pasará a tener ambas direcciones IP.

Si lo que queremos es cambiar la dirección IP asignada al adaptador será necesario eliminar primero la anterior con el comando `Remove-NetIpAddress`.

Para realizar pruebas de conectividad puede ser necesario deshabilitar el firewall de Windows para que no bloquee los mensajes ping entrantes. Esta tarea se puede realizar mediante el comando `Set-NetFirewallProfile` de la siguiente forma:

```powershell
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
```

#### 2.3.3.- Configuración de NIC Teaming

**NIC teaming** es una nueva característica disponible a partir Windows Server 2012R2 que permite a los administradores combinar el ancho de banda de múltiples adaptadores de red, incrementando de esta manera el rendimiento y la tolerancia a fallos.

Como sabes uno de los puntos fuertes de Windows 2012R2 es la virtualización, que entre otras cosas permite separar las funciones de red en diferentes sistemas sin necesidad de comprar una tarjeta de red para cada uno de ellos. Sin embargo, el punto negativo de esta característica es que podemos tener varios servidores virtualizados que dependen de una única tarjeta de red por lo que **un fallo en ésta supondrá la caída de todos los servidores**.

Para solucionar este problema 2012R2 incluye permite realizar NIC teaming. Esto consiste en combinar varias tarjetas de red en una única interfaz balanceando la carga entre ellas y proporcionando tolerancia a fallos en el servicio ya que si una tarjeta falla el resto continuarían con su función. La novedad a partir de 2012R2 es que no es necesario hardware específico, como en anteriores implementaciones, sino que es independiente del hardware. 

![NIC Teaming](imgs/03_01_nic_team.png)

Windows Server dispone de los siguientes modos de NIC teaming:

- **Formación de equipos estática (Static Teaming)**: este modo es dependiente de switch, usa el protocolo IEEE 802.3ad (draft), el cual solo agrupa los adaptadores de red. Este modo nos obliga a configurar los switches y el host para identificar los vínculos que forman el equipo. Este modo no ayuda al switch ni al host a identificar cables desconectados ni otro tipo de errores.
- **Independiente del conmutador (Switch Independient)**: este modo es totalmente independiente al switch, y este no sabe en ningún momento que las tarjetas de red forman un grupo. Este modo nos permite conectar las diferentes tarjetas de red a distintos switches, lo cual no significa que sea obligatorio, pero si muy recomendable. Este modo también nos permite poder poner un adaptador de red en modo espera por si alguno fallase.
- **LACP (Link Aggregation Control Protocol Teaming)** este modo también es dependiente de switch. Usa el protocolo IEEE 802.1ax que funciona como un protocolo de control de enlaces agregados. Crea automáticamente los equipos y reconoce cuando se agrega y se quita un adaptador de red gracias a la emisión y recepción de paquetes LACP. Este modo necesita que activemos el uso del protocolo LACP en el switch y en el host.
 
Además, tenemos que elegir el modo de equilibrio de carga:

- **Hash de dirección**: este modo es muy efectivo cuando tenemos mucho tráfico de salida ya que envía por todos los adaptadores de red, pero solo recibe por uno, se suele usar principalmente para servidores Web y FTP.
- **Puerto Hyper-V**: este modo está muy bien para cuando se tienen máquinas virtuales en el host y solo necesitamos que cada máquina virtual use la velocidad de un solo adaptador de red.
- **Dinámico**: este modo vino de la mano de Windows Server 2012 R2 y es una mezcla de los dos mejorada, nos permite balancear el tráfico de entrada y salida aprovechando el ancho de banda al máximo.
 
Finalmente podemos indicar que un adaptador se ponga en Modo de espera. Esta opción solo está disponible con la formación de equipos modo Independiente del conmutador (Switch Independient) y nos permite poder dejar en modo espera a uno o varios adaptadores de red, así en el caso de que un adaptador dejara de funcionar entraría en funcionamiento uno en modo de espera.
 







### 2.4.- Configuración inicial con `sconfig`


El comando sconfig permite configurar el servidor de forma básica:

- Nombre del equipo (2): Cambia el nombre del servidor.
- Configuración de red (8): Asigna una IP estática.
- Actualizaciones (5): Configura las actualizaciones automáticas.
- Grupo de trabajo/Dominio (1): Agrega el servidor a un dominio.
- Habilitar RDP (7): Activa el acceso remoto.
- Hora y zona horaria (9): Ajusta la configuración horaria.

    Usa el número correspondiente para seleccionar y configurar cada opción.


***
[Volver al índice principal](index_UT07.md)