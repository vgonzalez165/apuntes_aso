```
----------------- ADMINISTRACIÓN DE SISTEMAS INFORMÁTICOS EN RED ----------------
---------------------------------------------------------------------------------

Módulo:                     ADMINISTRACIÓN DE SISTEMAS OPERATIVOS
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT02. Administración remota del sistema
Apartado:                   Administración remota de sistemas Windows
Resultados de aprendizaje:  RA4
```

# UT02.- ADMINISTRACIÓN REMOTA DEL SISTEMA

## 2.- Administración remota de sistemas Windows

La administración moderna de servidores en entornos de producción se fundamenta en un principio operativo clave: el desacoplamiento entre los servicios del servidor y las herramientas utilizadas para su gestión. En sistemas Windows Server, especialmente al desplegar instalaciones mínimas como Windows Server Core, la interacción directa mediante teclado y monitor local se reduce al mínimo imprescindible. El objetivo del administrador de sistemas consiste en centralizar la monitorización, configuración y automatización desde estaciones de trabajo remotas, garantizando la seguridad, la trazabilidad y la eficiencia en la gestión de la infraestructura.

Para articular esta gestión remota, Windows Server ofrece diversas alternativas complementarias que abarcan desde interfaces basadas en línea de comandos hasta consolas gráficas web y de escritorio.


## 2.1. PowerShell Remoting, WinRM y PowerShell Direct

La herramienta principal para la administración por línea de comandos en Windows es PowerShell Remoting, cuya conectividad se sustenta en el servicio **Windows Remote Management (WinRM)**. WinRM constituye la implementación de Microsoft del protocolo estándar WS-Management (Web Services-Management), un protocolo basado en SOAP que permite ejecutar comandos, transferir configuraciones y automatizar tareas de forma segura entre equipos heterogéneos y servidores Windows.

Por defecto, WinRM escucha peticiones a través del puerto TCP `5985` cuando opera bajo el protocolo no seguro HTTP, y mediante el puerto TCP `5986` cuando se configura con cifrado de transporte HTTPS. Para poner en marcha este servicio y habilitar la infraestructura de PowerShell Remoting en el servidor, se debe abrir una consola con privilegios elevados y ejecutar el cmdlet `Enable-PSRemoting -Force`. Esta orden inicia automáticamente el servicio WinRM, define su tipo de arranque como automático, crea los agentes de escucha (*listeners*) y genera las excepciones correspondientes en el Firewall de Windows.

```powershell
Enable-PSRemoting -Force

```

### Preparación del Firewall de Windows para pruebas de laboratorio

En entornos de pruebas o durante el despliegue inicial en laboratorio, los administradores suelen necesitar verificar la conectividad IP básica antes de diagnosticar capas superiores de gestión. Si se desea deshabilitar por completo los perfiles de red del cortafuegos de Windows con propósitos de prueba, se puede recurrir al cmdlet `Set-NetFirewallProfile`, deshabilitando en una sola orden los perfiles Domain, Public y Private:

```powershell
Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled False
Get-NetFirewallProfile | Select-Object Name, Enabled
```

No obstante, en entornos reales deshabilitar el firewall al completo debilita gravemente la seguridad del sistema. Una práctica más precisa consiste en mantener el cortafuegos encendido y habilitar selectivamente las respuestas a paquetes ICMP (ping). A través de `New-NetFirewallRule`, es posible crear una regla de entrada que autorice el protocolo ICMPv4 (tipo 8, correspondiente a la solicitud de eco) originado desde una subred determinada:

```powershell
New-NetFirewallRule -DisplayName "ICMP Permitir ping" -Direction Inbound -Protocol ICMPv4 -IcmpType 8 -RemoteAddress 10.0.0.0/8 -Action Allow
```

### Protocolos de autenticación: Kerberos frente a NTLM

Al establecer una conexión administrativa remota, el primer mecanismo de control consiste en verificar la identidad de quien inicia la sesión. En infraestructuras corporativas gobernadas por un dominio de Active Directory, WinRM se apoya automáticamente en el protocolo **Kerberos**. Este protocolo proporciona autenticación mutua, lo que significa que el Centro de Distribución de Claves (KDC) valida tanto que el usuario es quien dice ser frente al servidor, como que el servidor remoto es un equipo legítimo frente al cliente, impidiendo ataques de intermediario (*Man-In-The-Middle*).

El escenario cambia drásticamente cuando los servidores se encuentran en un entorno de **Grupo de Trabajo (Workgroup)** sin controladores de dominio. En esta situación, al no existir un KDC centralizado, WinRM debe recurrir al protocolo de desafío/respuesta **NTLM**. A diferencia de Kerberos, NTLM no puede verificar criptográficamente la identidad del servidor de destino ante el cliente. Por esta razón de seguridad, el cliente de WinRM rechaza por defecto cualquier comunicación que emplee NTLM sobre HTTP, a menos que el administrador aplique una de las siguientes dos medidas de mitigación:

1. Configurar un certificado digital SSL/TLS en el servidor remoto firmado por una entidad en la que el cliente confíe.
2. Añadir explícitamente el nombre o la dirección IP del servidor a la lista local de equipos de confianza (`TrustedHosts`) del cliente.

### Configuración de la lista de equipos de confianza (`TrustedHosts`)

La lista `TrustedHosts` reside en la configuración del cliente WinRM y contiene las direcciones IP o nombres de aquellos equipos con los que el cliente tiene permitido negociar credenciales bajo NTLM. Para consultar los valores vigentes del cliente y verificar el contenido de dicha lista, se emplea la utilidad de línea de comandos `winrm`:

```powershell
winrm get winrm/config/client
```

Cuando se desea autorizar a un servidor específico, el administrador debe modificar el elemento correspondiente en el proveedor WSMan. Es importante resaltar que la asignación directa sobrescribe cualquier valor previamente registrado. Por ello, si ya existían servidores de confianza y se desea incorporar uno nuevo, primero se extrae la cadena actual en una variable auxiliar y posteriormente se concatena la nueva dirección separada por una coma:

```powershell
# Asignación de un único host
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "10.0.0.11"

# Inclusión de un host adicional sin sobreescribir la configuración previa
$existing = (Get-Item WSMan:\localhost\Client\TrustedHosts).value
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "$existing, 10.0.0.12"
```

Existe la posibilidad técnica de indicar un asterisco (`-Value "*"`) para confiar de manera indiscriminada en cualquier equipo de la red. No obstante, esta práctica está totalmente desaconsejada desde la óptica de la seguridad, ya que deja al equipo cliente expuesto al envío de credenciales hacia hosts no autorizados o comprometidos.

### Ejecución de comandos remotos y sesiones interactivas

Una vez resuelta la confianza entre los equipos, PowerShell ofrece dos modalidades principales de trabajo remoto:

- **Ejecución puntual y desatendida (`Invoke-Command`):** Este cmdlet permite enviar un bloque de código (`-ScriptBlock`) para que se ejecute en el servidor remoto, procese la tarea en el procesador del destino y devuelva únicamente los objetos resultantes formateados a la consola local. Es la opción idónea para tareas de mantenimiento repetitivas, auditorías y scripts masivos:

    ```powershell
    Invoke-Command -ComputerName 10.0.0.11 -Credential (Get-Credential) -ScriptBlock {
        $env:computername
    }
    ```

-**Sesión interactiva en consola (`Enter-PSSession`):** Cuando el administrador precisa una consola interactiva para trabajar en tiempo real como si estuviera sentado frente al servidor físico, se recurre a `Enter-PSSession`. Al ejecutarse, el prompt de la terminal cambia reflejando entre corchetes la dirección o el nombre del servidor remoto. Todas las órdenes tecleadas a partir de ese instante se ejecutan directamente en el contexto del servidor. Para liberar los recursos y cerrar la sesión, basta con escribir la instrucción `exit`:

    ```powershell
    Enter-PSSession -ComputerName 10.0.0.11 -Credential (Get-Credential)
    # Una vez finalizada la tarea:
    [10.0.0.11]: PS C:\> exit
    ```

### Configuración avanzada: Cifrado y securización de WinRM sobre HTTPS

Aunque WinRM cifra las cargas útiles a nivel de aplicación cuando se usa Kerberos o NTLM, el transporte a través de HTTP estándar no ofrece validación criptográfica de extremos. La alternativa recomendada en redes empresariales o en entornos de Grupo de Trabajo sin `TrustedHosts` consiste en desplegar **WinRM sobre HTTPS** en el puerto `5986`. Este procedimiento exige la generación, vinculación y exportación de un certificado digital.

El proceso comienza en el servidor generando un certificado digital vinculado a su identidad (IP o nombre FQDN) y almacenándolo en el repositorio del equipo local mediante `New-SelfSignedCertificate`:

```powershell
New-SelfSignedCertificate -DnsName "10.0.0.11" -CertStoreLocation Cert:\LocalMachine\My -KeyLength 2048
```

El resultado de esta operación devuelve una huella digital (*Thumbprint*) en formato hexadecimal que identifica unívocamente al certificado. A continuación, para evitar conflictos de puertos o configuraciones obsoletas, es una buena práctica eliminar los agentes de escucha previos con `Remove-Item -Path WSMan:\localhost\Listener\Listener* -Recurse`. Con la huella obtenida, se registra el nuevo agente HTTPS vinculando la dirección del servidor:

```powershell
New-Item -Path WSMan:\localhost\Listener -Transport HTTPS -Address * -CertificateThumbPrint <HUELLA_HEXADECIMAL>
```

Para permitir el tráfico a través del puerto seguro, se crea la correspondiente regla de entrada en el Firewall de Windows para el puerto TCP `5986`:

```powershell
New-NetFirewallRule -DisplayName "WinRM HTTPS" -Direction Inbound -Protocol TCP -LocalPort 5986 -Action Allow
```

Si en este punto intentamos conectarnos desde un equipo cliente añadiendo el modificador `-UseSSL`, la conexión fallará arrojando un error de transporte (habitualmente con código 12175). El origen del fallo radica en que el cliente no conoce ni confía en la entidad que ha emitido dicho certificado, al tratarse de un certificado autofirmado por el propio servidor.

Para solventar esta limitación, el certificado debe exportarse desde el servidor a un archivo público `.cer` mediante el cmdlet `Export-Certificate`, transferirse al equipo cliente (por ejemplo, aprovechando una carpeta de red compartida con `Copy-Item`) e importarse en el almacén de Entidades de Certificación Raíz de Confianza de la máquina local (`Cert:\LocalMachine\Root`):

```powershell
# 1. En el Servidor: Exportar el certificado a fichero
Export-Certificate -Cert Cert:\LocalMachine\My\<HUELLA_HEXADECIMAL> -FilePath C:\servercert.cer

# 2. En el Servidor o Cliente: Copiar el certificado a través de la red
Copy-Item -Path C:\servercert.cer -Destination "\\10.0.0.1\compartida\"

# 3. En el Cliente: Importar en el almacén de entidades de confianza raíz del equipo local
Import-Certificate -FilePath C:\compartida\servercert.cer -CertStoreLocation Cert:\LocalMachine\Root
```

Completada la importación, el sistema operativo del cliente reconocerá la legitimidad del certificado del servidor. A partir de ese instante, cualquier ejecución remota mediante `Invoke-Command` o `Enter-PSSession` que incorpore el parámetro `-UseSSL` completará la conexión de forma transparente y bajo un canal seguro cifrado por TLS.



## 2.2. Windows Admin Center (WAC)

**Windows Admin Center** constituye la evolución estratégica de Microsoft para la gestión visual de servidores, planteada para sustituir progresivamente a las consolas tradicionales basadas en MMC (Microsoft Management Console). Se trata de una solución basada en navegador web, modular y ligera, que puede instalarse sin coste adicional de licencia tanto en servidores físicos como en máquinas virtuales locales o instancias en Microsoft Azure.

Arquitectónicamente, Windows Admin Center se despliega como una pasarela (*gateway*). El administrador accede con un navegador web a dicha pasarela a través de HTTPS, y es la propia pasarela la que se encarga de interrogar a los servidores administrados de la red utilizando protocolos estándar como WinRM y WMI sobre PowerShell.

### Preparación previa del entorno: Configuración de seguridad mejorada de Internet Explorer (IE ESC)

Cuando se realiza la descarga de Windows Admin Center desde un servidor Windows Server con interfaz gráfica, el administrador se topa habitualmente con un obstáculo: el navegador integrado histórico (Internet Explorer) incorpora por defecto la directiva de **Configuración de seguridad mejorada (IE ESC)**. Esta directiva bloquea de forma preventiva el acceso a scripts y descargas procedentes de sitios que no hayan sido agregados manualmente a la zona de confianza.

Para evitar la molestia de autorizar individualmente las decenas de dominios y subdominios asociados a la web de descargas de Microsoft, el procedimiento más ágil consiste en desactivar temporalmente esta directiva. Para ello, se accede al **Administrador del Servidor**, se selecciona en el panel izquierdo la opción **Servidor local**, se localiza en la tabla de propiedades el elemento **Configuración de seguridad mejorada de IE** y se conmuta a la posición **Desactivado** tanto para administradores como para usuarios estándar.

### Despliegue e instalación de la pasarela

El instalador de Windows Admin Center se distribuye en formato ejecutable de Windows Installer (`.msi`). A lo largo del asistente de instalación, el sistema solicita diversas definiciones técnicas fundamentales para el comportamiento de la pasarela:

- La decisión de vincular o no el servicio a **Microsoft Update** para automatizar el ciclo de vida y la recepción de parches del producto.
- La autorización para que el instalador modifique automáticamente la lista de `TrustedHosts` de la máquina anfitriona, facilitando la conexión inmediata hacia otros servidores del entorno.
- La selección del puerto TCP de servicio en el que la pasarela atenderá las conexiones web entrantes, asignado por defecto al puerto estándar HTTPS `443` (modificable si en la máquina ya conviven otros servicios web como IIS).
- La provisión del certificado de seguridad SSL. El instalador ofrece la alternativa de generar un certificado autofirmado con una vigencia temporal de 60 días, o bien vincular la huella digital de un certificado corporativo previamente desplegado en el almacén de certificados del equipo.

### Acceso a la consola y monitorización

Al término de la instalación, la interfaz de administración queda disponible a través de la red en la URL correspondiente (`https://<Nombre_o_IP_del_Servidor>:<Puerto>`). Es fundamental advertir a los alumnos que **Windows Admin Center no es compatible con Internet Explorer**; cualquier intento de acceder desde dicho navegador provocará un mensaje de bloqueo. El acceso debe realizarse obligatoriamente desde exploradores web modernos basados en Chromium o compatibles, tales como **Microsoft Edge** o Google Chrome.

Al conectar por primera vez desde un equipo cliente, el navegador alertará sobre la falta de confianza en el certificado si este fue generado de forma autofirmada. Tras omitir la advertencia y confirmar el acceso al sitio, el sistema operativo solicitará mediante una ventana modal las credenciales de un usuario con privilegios administrativos en el servidor. Una vez autenticado, el panel general de Windows Admin Center ofrece una consola visual completa desde la que es posible monitorizar en tiempo real el rendimiento de la CPU, la memoria RAM y las transferencias de disco y red, así como manipular servicios, procesos, eventos, adaptadores de red, actualizaciones y consolas integradas de PowerShell.

---

## 2.3. Administrador del Servidor (Server Manager)

El **Administrador del Servidor** es la herramienta gráfica clásica que ha vertebrado la administración de Windows Server desde las ediciones de 2008. Aunque concebida inicialmente para controlar la máquina local, la herramienta fue rediseñada en Windows Server 2012 para operar como una consola de administración centralizada multiequipo, capaz de monitorizar y provisionar roles y características en múltiples servidores remotos simultáneamente.

Para poner bajo control un nuevo servidor dentro de esta interfaz, el administrador debe abrir la consola, acceder al menú superior **Administrar** y seleccionar la opción **Agregar servidores**. El asistente emergente clasifica la búsqueda y vinculación de equipos en diferentes pestañas:

* **Active Directory:** Es la vía natural en entornos corporativos. Permite realizar consultas sobre el catálogo global del dominio para descubrir y agregar cualquier servidor registrado, autenticando la gestión de forma automática y transparente mediante tickets Kerberos.
* **DNS:** En entornos de Grupo de Trabajo donde no existe un directorio activo, la pestaña DNS permite localizar servidores introduciendo su nombre de resolución o directamente su dirección IP. Al agregarlos, el Administrador del Servidor verificará la conectividad y añadirá el nodo al inventario unificado.

Una vez agregados los servidores remotos, estos quedan catalogados bajo la categoría "Todos los servidores" del panel lateral. Desde allí, el Administrador del Servidor supervisa continuamente el estado de salud de cada máquina, reflejando alertas del visor de sucesos, servicios detenidos y habilitando el asistente para instalar roles o características de forma remota en cualquiera de ellos.



---
[Volver](./ut02_index.md)
