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

