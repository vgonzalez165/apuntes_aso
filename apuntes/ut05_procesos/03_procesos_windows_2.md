```
----------------- ADMINISTRACIÓN DE SISTEMAS INFORMÁTICOS EN RED ----------------
---------------------------------------------------------------------------------

Módulo:                     ADMINISTRACIÓN DE SISTEMAS OPERATIVOS
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT05. Administración de procesos
Apartado:                   3. Procesos en Windows
Resultados de aprendizaje:  RA2
```


# UT04.- ADMINISTRACIÓN DE PROCESOS

## 3.- Procesos en Windows

### 3.1.- Arquitectura de arranque y jerarquía de procesos en Windows

A diferencia de los sistemas Unix/Linux, donde toda la jerarquía de procesos nace de un único proceso raíz en espacio de usuario (`systemd` o `init` con `PID 1`), la arquitectura de Windows separa desde el inicio el núcleo del sistema operativo de las sesiones de usuario mediante un aislamiento denominado **Session 0 Isolation**.

Cuando el equipo arranca, el *kernel* crea una cadena secuencial de procesos críticos del sistema antes de que el usuario pueda iniciar sesión:

```text
System (PID 4)
 └── smss.exe (Gestor de sesiones)
      ├── csrss.exe (Sesión 0)
      ├── wininit.exe (Inicialización de servicios - Sesión 0)
      │    ├── services.exe (Service Control Manager - SCM)
      │    │    ├── svchost.exe (Servicios de red, RPC, etc.)
      │    │    ├── svchost.exe (Windows Update, audio, etc.)
      │    │    └── spoolsv.exe (Cola de impresión)
      │    └── lsass.exe (Autenticación y seguridad local)
      └── winlogon.exe (Inicio de sesión interactivo - Sesión 1)
           ├── dwm.exe (Administrador de ventanas del escritorio)
           └── explorer.exe (Interfaz gráfica de usuario)
                ├── firefox.exe
                └── WindowsTerminal.exe
                     └── pwsh.exe (PowerShell)
```

Los principales componentes de la jerarquía son:

- **`System` (`PID 4`) y `System Idle Process` (`PID 0`):**
  - El **`PID 0`** (*Proceso inactivo del sistema*) no es un proceso real con código ejecutable, sino un contador que utiliza el planificador del *kernel* para medir el porcentaje de tiempo que cada núcleo de la CPU permanece libre.
  - El **`PID 4`** (`System`) aloja los hilos que se ejecutan directamente en **modo núcleo** (*Kernel Mode*), encargados de gestionar la memoria virtual, los controladores de dispositivos (*drivers*), el sistema de archivos y la planificación de hilos.
- **`smss.exe` (*Session Manager Subsystem*):** es el primer proceso real en **modo usuario** (*User Mode*). Crea las variables de entorno del sistema, carga el subsistema gráfico básico y divide el arranque en dos entornos aislados:
    - **Sesión 0 (Servicios no interactivos):** reservada exclusivamente para los procesos automáticos del sistema operativo, evitando que un servicio con privilegios elevados (`SYSTEM`) pueda interactuar directamente con el escritorio de un usuario (medida de seguridad frente a ataques de elevación de privilegios).
    - **Sesión 1, 2... (Sesiones de usuario):** cada usuario que inicia sesión (localmente o por Escritorio Remoto) recibe su propio espacio de sesión aislado.
- **`wininit.exe` y los procesos del sistema (Sesión 0):** lanza la infraestructura fundamental que mantiene operativo el equipo en segundo plano:
    - **`services.exe` (*Service Control Manager* - SCM):** es el equivalente funcional a `systemd` para los servicios de Windows. Se encarga de arrancar, detener y supervisar todos los servicios configurados en el sistema.
    - **`svchost.exe` (*Service Host*):** en Windows, muchos servicios del sistema no son ejecutables independientes (`.exe`), sino bibliotecas dinámicas (`.dll`). Para ahorrar memoria RAM, `services.exe` agrupa varios servicios dentro de un mismo proceso contenedor llamado `svchost.exe`, motivo por el cual es habitual ver decenas de instancias simultáneas en el Administrador de tareas.
    - **`lsass.exe` (*Local Security Authority Subsystem Service*):** gestiona la autenticación de usuarios, las directivas de seguridad locales, los *tokens* de acceso y el protocolo Kerberos o NTLM.
- **`winlogon.exe` y el entorno interactivo (Sesión 1):** Controla la pantalla de inicio de sesión segura (`Ctrl + Alt + Supr`). Una vez validada la contraseña del usuario, lanza:
    - **`explorer.exe`:** el explorador de Windows, que dibuja la barra de tareas, el menú Inicio y el escritorio. Actúa como **proceso padre** de todas las aplicaciones interactivas que el usuario abre mediante interfaz gráfica.
    - **`dwm.exe` (*Desktop Window Manager*):** renderiza la composición visual de las ventanas, transparencias y efectos gráficos.






### 3.2.- Herramientas gráficas de monitorización e inspección (GUI)

En entornos Windows, la administración de procesos cuenta con herramientas gráficas nativas y avanzadas que ofrecen distintos niveles de profundidad diagnóstica, desde la supervisión rápida de consumo hasta el análisis forense de hilos, bibliotecas y bloqueos.


#### 1. Administrador de tareas (`taskmgr.exe`)

Es la herramienta de primera línea para supervisar el rendimiento general y gestionar procesos activos. Se abre mediante el atajo `Ctrl + Mayús + Esc`, haciendo clic derecho en la barra de tareas o ejecutando `taskmgr`.

- **Pestaña *Procesos* (Vista orientada al usuario):**
Agrupa los procesos en tres bloques funcionales: **Aplicaciones** (ventanas abiertas en primer plano), **Procesos en segundo plano** (tareas auxiliares y demonios de usuario) y **Procesos de Windows** (servicios críticos del sistema operativo). Muestra el consumo porcentual de CPU, memoria, disco y red, y permite desplegar los subprocesos o pestañas internas de una aplicación (por ejemplo, cada pestaña del navegador).
- **Pestaña *Detalles* (Vista técnica del sistema):**
Equivale a la salida de `ps aux` o `top` en Linux, mostrando los ejecutables reales (`.exe`), su identificador numérico (**`PID`**), el usuario propietario, el consumo de memoria física y el estado actual (**En ejecución** o **Suspendido**). Desde el menú contextual (clic derecho) sobre cualquier proceso ofrece operaciones avanzadas:
* **Finalizar árbol de procesos (*End process tree*):** Detiene no solo el proceso seleccionado, sino también todos los procesos hijos que haya creado.
* **Establecer prioridad (*Set priority*):** Permite modificar dinámicamente la clase de prioridad de planificación (desde *Baja* hasta *Tiempo real*).
* **Establecer afinidad (*Set affinity*):** Restringe en qué núcleos lógicos concretos de la CPU tiene permiso para ejecutarse ese proceso.
* **Ir a servicios (*Go to service(s)*):** Salta directamente a la pestaña *Servicios* resaltando qué servicio o servicios están alojados dentro de ese proceso (imprescindible para inspeccionar instancias de `svchost.exe`).



* **El estado *Suspendido* (icono de hoja verde / *Modo eficiencia*):**
A diferencia de Linux (donde la suspensión `T` suele aplicarse manualmente con `Ctrl + Z`), Windows gestiona de forma automática la suspensión de aplicaciones modernas (UWP/Microsoft Store) minimizadas y permite activar el **Modo de eficiencia** sobre procesos en segundo plano para reducir su prioridad al mínimo y ahorrar energía y memoria RAM.

---

#### 2. Monitor de recursos (`resmon.exe`)

Accesible desde la pestaña *Rendimiento* del Administrador de tareas (*Abrir el Monitor de recursos*) o ejecutando `resmon`, proporciona una visión granular por subsistema (CPU, Memoria, Disco y Red) cuando se necesita diagnosticar un cuello de botella real.

* **Suspensión y reanudación manual de procesos:**
En la pestaña **CPU**, haciendo clic derecho sobre cualquier proceso, permite ejecutar **Suspender proceso** y **Reanudar proceso**. Esta función replica gráficamente el comportamiento de las señales `SIGSTOP` y `SIGCONT` de Linux, congelando los hilos del proceso en memoria sin cerrarlo.
* **Análisis de cadena de espera (*Analyze Wait Chain*):**
Cuando una aplicación deja de responder (*No responde*), el Monitor de recursos (también accesible desde la pestaña *Detalles* del Administrador de tareas) permite analizar su **cadena de espera**. Esta herramienta inspecciona los hilos bloqueados y muestra gráficamente qué otro proceso, hilo o recurso compartido (un archivo bloqueado, una llamada de red o un *mutex*) está reteniendo la ejecución del proceso bloqueado.
* **Monitorización de E/S por proceso:**
En las pestañas **Disco** y **Red** detalla exactamente qué archivo concreto está leyendo o escribiendo cada `PID` (en bytes/segundo) y qué puertos TCP/UDP tiene abiertos.

---

#### 3. Process Explorer (Microsoft Sysinternals)

**Process Explorer** (`procexp.exe`) es la herramienta estándar de referencia para administradores de sistemas Windows y técnicos de soporte avanzado, ya que combina en una única interfaz las capacidades de `pstree`, `htop`, `lsof` y `strace` de Linux.

* **Árbol jerárquico real padre-hijo y código de colores:**
A diferencia del Administrador de tareas (que agrupa por ventanas), Process Explorer muestra por defecto la **jerarquía exacta de creación (`PID` y `PPID`)** indentada en árbol y colorea cada fila según su naturaleza:
* **Rosa:** Procesos que alojan servicios de Windows (`svchost.exe` y servicios propios).
* **Azul:** Procesos que se ejecutan bajo la misma cuenta de usuario que el administrador actual.
* **Verde / Rojo (parpadeo breve):** Procesos recién creados (verde) o recién finalizados (rojo) en tiempo real.
* **Morado:** Procesos empaquetados o comprimidos (útil para detectar ejecutables sospechosos).


* **Panel inferior de inspección (*Handles* y *DLLs*):**
Al seleccionar un proceso en el panel superior y activar el panel inferior (`Ctrl + L`), permite elegir entre dos vistas críticas:
* **Vista de manejadores (*Handles*, `Ctrl + H`):** Lista todos los recursos del sistema operativo que el proceso tiene abiertos en ese instante (archivos en disco, claves del Registro de Windows, *threads*, puertos o eventos). Permite desbloquear archivos que Windows impide borrar ("el archivo está abierto en otro programa") buscando qué `PID` posee su *handle*.
* **Vista de bibliotecas (*DLLs*, `Ctrl + D`):** Muestra todas las bibliotecas dinámicas cargadas en el espacio de direcciones del proceso y su ruta en disco.


* **Inspección de hilos y seguridad:**
Haciendo doble clic sobre un proceso se abre su ficha técnica (*Properties*), donde en la pestaña **Threads** se visualizan cada uno de sus hilos individuales (`TID`), su consumo de CPU y su pila de llamadas (*Stack*), y en la pestaña **Image** se puede verificar la firma digital del ejecutable o enviarlo directamente a VirusTotal.



### 3.3.- Administración y automatización de procesos en Windows con PowerShell

En la administración moderna de sistemas Windows (tanto en estaciones de trabajo como en entornos *Windows Server Core* sin interfaz gráfica), **PowerShell** es el estándar de referencia. A diferencia de los comandos heredados basados en texto plano, PowerShell opera mediante un **pipeline orientado a objetos .NET**: cada proceso recuperado es un objeto vivo con propiedades inspeccionables (`Id`, `CPU`, `WorkingSet`, `PriorityClass`, `Responding`) y métodos ejecutables (`.Kill()`, `.WaitForExit()`), lo que permite filtrar, ordenar, auditar y actuar sobre decenas de procesos con precisión quirúrgica.


#### 1. Inspección y análisis de procesos: `Get-Process` y `Get-CimInstance`

PowerShell ofrece dos niveles de inspección según la profundidad técnica requerida:

**A) Consulta rápida de rendimiento y estado (`Get-Process`)**

El cmdlet `Get-Process` (alias `gps` o `ps`) consulta directamente la tabla de procesos del sistema y devuelve objetos `System.Diagnostics.Process`:

```powershell
# Listado general de procesos activos
Get-Process

# Top 10 procesos con mayor consumo de tiempo de CPU
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Id, ProcessName, CPU, WorkingSet

# Filtrar procesos que consumen más de 200 MB de memoria física (WorkingSet en bytes)
Get-Process | Where-Object { $_.WorkingSet -gt 200MB } | Select-Object Id, ProcessName, @{Name="RAM_MB"; Expression={[math]::Round($_.WorkingSet / 1MB, 2)}}

# Detectar aplicaciones gráficas bloqueadas que no responden a eventos de ventana
Get-Process | Where-Object { $_.MainWindowHandle -ne 0 -and -not $_.Responding }
```

**B) Consulta profunda de jerarquía, ruta y argumentos (`Get-CimInstance`)**

Por motivos de rendimiento y seguridad, `Get-Process` no expone directamente el identificador del proceso padre (`PPID`) ni la línea de comandos completa con la que se invocó un binario. Para auditar la jerarquía real (equivalente a `ps -ef` en Linux), se consulta la clase instrumental **CIM/WMI `Win32_Process**`:

```powershell
# Listar PID, PPID (proceso padre), nombre y línea de comandos exacta
Get-CimInstance Win32_Process | Select-Object ProcessId, ParentProcessId, Name, CommandLine

# Rastrear todos los procesos hijos lanzados por una instancia concreta de PowerShell o Explorer
Get-CimInstance Win32_Process | Where-Object { $_.ParentProcessId -eq 4120 } | Select-Object ProcessId, Name, CommandLine

# Identificar qué servicios de Windows aloja cada instancia de svchost.exe
Get-CimInstance Win32_Service | Where-Object { $_.ProcessId -ne 0 } |
  Select-Object ProcessId, Name, DisplayName, State | Sort-Object ProcessId
```

**C) Inspección de hilos internos (`Threads`)**

Cada objeto devuelto por `Get-Process` contiene una colección `Threads` que permite bajar al nivel de unidad de ejecución:

```powershell
# Ver los hilos activos de un proceso concreto, su estado y prioridad base
(Get-Process -Name explorer).Threads | Select-Object Id, ThreadState, WaitReason, PriorityLevel
```


#### 2. Creación de procesos y control de planificación (`Start-Process` y `PriorityClass`)

**A) Lanzamiento controlado con `Start-Process`**

`Start-Process` (alias `saps` o `start`) permite iniciar ejecutables controlando el estilo de ventana, el directorio de trabajo, las credenciales de ejecución y la sincronización:

```powershell
# Lanzar un proceso en segundo plano sin abrir ventana visible (-WindowStyle Hidden)
Start-Process -FilePath "robocopy.exe" -ArgumentList "C:\Origen D:\Backup /MIR" -WindowStyle Hidden

# Lanzar un proceso y bloquear el script hasta que finalice (-Wait), capturando su código de salida (-PassThru)
$proceso = Start-Process -FilePath "notepad.exe" -Wait -PassThru
Write-Output "El proceso terminó con código de salida (ExitCode): $($proceso.ExitCode)"

# Ejecutar una herramienta administrativa solicitando elevación de privilegios (UAC)
Start-Process -FilePath "pwsh.exe" -Verb RunAs
```

**B) Ajuste dinámico de prioridad y afinidad de CPU**

Para modular el reparto de procesador (equivalente a `nice` y `renice` en Linux), se modifica la propiedad **`PriorityClass`** (`Idle`, `BelowNormal`, `Normal`, `AboveNormal`, `High`, `RealTime`) o la máscara binaria de núcleos **`ProcessorAffinity`**:

```powershell
# Iniciar una tarea pesada y rebajar inmediatamente su prioridad para no penalizar el sistema
$tarea = Start-Process -FilePath "powershell.exe" -ArgumentList "-Command while($true){}" -PassThru
$tarea.PriorityClass = 'BelowNormal'

# Cambiar en caliente la prioridad de un proceso ya existente por su PID
(Get-Process -Id 5340).PriorityClass = 'Low'

# Restringir un proceso para que solo se ejecute en los dos primeros núcleos lógicos (máscara binaria 0011 = 3)
(Get-Process -Id 5340).ProcessorAffinity = 3
```


#### 3. Finalización de procesos y árboles (`Stop-Process` y métodos de objeto)

PowerShell permite distinguir entre un cierre ordenado a nivel de interfaz gráfica y una terminación inmediata a nivel de *kernel*:

```powershell
# 1. Solicitar cierre ordenado de la ventana principal (equivale a pulsar la 'X'; permite guardar cambios)
(Get-Process -Name notepad).CloseMainWindow()

# 2. Terminación forzosa inmediata por PID o por nombre (equivalente a kill -9 / pkill -9)
Stop-Process -Id 5340 -Force
Stop-Process -Name "chrome" -Force

# 3. Terminación forzosa de un proceso y todo su árbol de procesos hijos (PowerShell 7+)
Stop-Process -Id 5340 -Force -IncludeUserName

# 4. Terminar árbol completo mediante método nativo .NET (compatible con cualquier versión)
(Get-Process -Id 5340).Kill($true)
```


#### 4. Ejecución concurrente en segundo plano (*Background Jobs*)

Para ejecutar bloques de código o tareas de administración sin bloquear la consola interactiva, PowerShell incorpora un motor completo de trabajos en segundo plano:

```powershell
# 1. Iniciar un trabajo en segundo plano (equivalente a '&' en Bash)
Start-Job -Name "AuditoriaRed" -ScriptBlock {
    Test-Connection -ComputerName 8.8.8.8 -Count 15
}

# 2. Consultar el estado de todos los trabajos de la sesión (Running, Completed, Failed)
Get-Job

# 3. Esperar a que un trabajo termine antes de continuar un script
Wait-Job -Name "AuditoriaRed"

# 4. Leer la salida generada por el trabajo (-Keep conserva los datos en memoria para volver a leerlos)
Receive-Job -Name "AuditoriaRed" -Keep

# 5. Detener un trabajo en curso y liberar su memoria de la sesión
Stop-Job -Name "AuditoriaRed"
Remove-Job -Name "AuditoriaRed"

```

**Alternativa ligera basada en hilos (`Start-ThreadJob`):** Mientras que `Start-Job` crea un proceso independiente completo (`pwsh.exe` / `powershell.exe`) por cada tarea (con mayor consumo de RAM y arranque más lento), el cmdlet **`Start-ThreadJob`** ejecuta cada tarea en un **hilo (*thread*)** dentro del mismo proceso de PowerShell, compartiendo los mismos comandos de gestión (`Get-Job`, `Receive-Job`, `Stop-Job`, `Remove-Job`) con un rendimiento muy superior.

