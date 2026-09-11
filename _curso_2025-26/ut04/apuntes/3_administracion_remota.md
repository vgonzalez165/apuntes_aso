# UT04.- INSTALACIÓN Y PUESTA EN MARCHA DE WINDOWS SERVER


## 3.- Administración remota de Windows Server

Windows Server 2019 ofrece múltiples métodos para gestionar servidores de forma remota, adaptándose a diferentes necesidades de infraestructura y experiencia del administrador. Estas opciones permiten una administración eficiente y segura, especialmente en servidores instalados en modo Core o en entornos sin acceso directo a la consola.

### 3.1.- Administración con Windows Admin Center

**Windows Admin Center (WAC)** es una interfaz basada en navegador que facilita la administración remota. Esta herramienta está disponible a partir de Windows 10 en entornos de escritorio y de Windows Server 2016

Sus características principales son:

- Gestión centralizada de servidores (incluso en modo Core).
- Herramientas gráficas para administrar configuraciones, roles y características.
- Integración con servicios en la nube de Azure.
- Permite realizar tareas como:
    - Monitorizar el rendimiento.
    - Administrar roles como Hyper-V, DNS o DHCP.
    - Configurar el almacenamiento y las actualizaciones.


#### 3.1.1.- Instalación en entorno gráfico


#### 3.1.2.- Instalación 

Install-WindowsFeature -Name Web-Server
Install-WindowsFeature -Name Management-Tools

    Configuración inicial a través del instalador descargable desde la página oficial de Microsoft.

### 3.2.- Administración remota mediante PowerShell

PowerShell es una herramienta potente para la gestión remota y automatización de tareas en Windows Server 2019.
Métodos de conexión remota:

    WinRM (Windows Remote Management):
        Permite administrar servidores mediante PowerShell Remoting.
        Comando para habilitar WinRM:

Enable-PSRemoting -Force

Conectar a un servidor remoto:

    Enter-PSSession -ComputerName <nombre_servidor> -Credential (Get-Credential)

Scripts remotos: Ejecuta scripts directamente en servidores remotos con:

    Invoke-Command -ComputerName <nombre_servidor> -ScriptBlock { <comando> } -Credential (Get-Credential)

Ventajas:

    Escalabilidad: Ejecuta comandos en múltiples servidores.
    Automatización avanzada.
    Compatible con entornos Core.

3. Administración con Remote Desktop (RDP)

Remote Desktop Protocol (RDP) permite el acceso remoto a una sesión gráfica del servidor.
Configuración:

    Habilitar RDP en el servidor:
        Usar el comando sconfig (opción 7) en servidores en modo Core.
        Configurar el firewall para permitir conexiones RDP:

        Set-NetFirewallRule -Name "RDP" -Enabled True

    Conectar desde otro equipo:
        Utilizar la herramienta "Conexión a Escritorio Remoto" (mstsc.exe) en Windows.

Ventajas:

    Gestión de servidores con GUI.
    Útil para tareas administrativas rápidas.

4. Administración con Herramientas RSAT

Las Remote Server Administration Tools (RSAT) permiten gestionar roles y características de Windows Server desde un equipo con Windows 10/11 o un servidor con GUI.
Instalación:

    En Windows 10/11:
        Acceder a Configuración > Aplicaciones > Características opcionales.
        Buscar e instalar las herramientas necesarias, como "Herramientas de Hyper-V" o "Herramientas de Active Directory".

Ventajas:

    Configuración gráfica de roles como DNS, DHCP y Active Directory.
    Facilita la administración remota de servidores Core.

5. Administración por línea de comandos con SSH

Desde Windows Server 2019, el soporte de SSH (Secure Shell) permite la administración remota con herramientas estándar en sistemas Unix/Linux.
Configuración:

    Instalar el servidor OpenSSH:

Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0

Iniciar el servicio SSH:

Start-Service sshd
Set-Service -Name sshd -StartupType Automatic

Conexión remota:

    Desde un cliente SSH:

        ssh usuario@ip_servidor

Ventajas:

    Alternativa multiplataforma.
    Útil para entornos heterogéneos con Linux y Windows.

6. Administración mediante Scripts y Automatización

El uso de scripts personalizados permite administrar servidores sin interacción manual:

    Programación con PowerShell o Bash (en sistemas con SSH).
    Integración con herramientas como Ansible o Terraform para la administración masiva.

7. Administración desde la Consola de Azure

Si el servidor está vinculado a Azure, puedes usar la consola de administración en la nube para:

    Monitorización y gestión centralizada.
    Configuración de backups y alta disponibilidad.

Requisitos:

    Configurar la extensión de Azure Arc en los servidores.
    Conexión estable a Internet.

Comparativa de Métodos de Administración
Método	Requisitos	Interfaz	Casos de Uso
Windows Admin Center	Navegador, instalación local	Gráfica (Web)	Gestión centralizada de roles y rendimiento.
PowerShell Remoting	WinRM habilitado	Texto (CLI)	Automatización y tareas avanzadas.
Escritorio Remoto (RDP)	RDP habilitado, GUI opcional	Gráfica (Desktop)	Tareas rápidas y configuración visual.
RSAT	Cliente Windows con RSAT	Gráfica (Desktop)	Configuración de roles y características.
SSH	SSH habilitado en servidor	Texto (CLI)	Gestión en entornos multiplataforma.
