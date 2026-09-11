```
---------------- ADMINISTRACIÓN DE SISTEMAS INFORMÁTICOS Y REDES ----------------
---------------------------------------------------------------------------------

Módulo:                     ADMINISTRACIÓN DE SISTEMAS OPERATIVOS
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT05
Práctica:                   PR0504. Aplicación de directivas
Resultados de aprendizaje:  RA1
```



# PR0504: Directivas de seguridad, GPP y Filtros WMI

Continuamos con la administración del dominio **`iessanandres.local`**. La dirección del centro quiere endurecer la seguridad de los equipos basándose en las normativas de seguridad y mejorar la experiencia de usuario desplegando configuraciones dinámicas mediante Preferencias (GPPs).

## Objetivos

- Implementar directivas de **Opciones de Seguridad** (Security Options).
- Desplegar configuraciones mediante **Preferencias de Directiva de Grupo (GPP)** utilizando **Item-Level Targeting**.
- Aplicar GPOs de forma selectiva mediante consultas **WMI** avanzadas.


## Parte 1: Endurecimiento de la seguridad 

Debes crear una GPO llamada **`GPO_Harden_Security_Equipos`** vinculada a la UO `_Equipos`. Configura las siguientes directivas:

### 1.1. Protección de cuentas y acceso

- **Renombrar cuenta de administrador:** por seguridad, la cuenta local `Administrador` es un objetivo común de ataques. Configura la directiva para que esta cuenta se renombre a **`Admin_Local_IES`**.
- **Inicio de sesión interactivo:** configura el equipo para que **no requiera** pulsar `Ctrl+Alt+Supr` para iniciar sesión.

### 1.2. Aviso legal (Consentimiento Informado)

- Configura el sistema para que, antes de iniciar sesión, muestre un mensaje legal a los usuarios.
    - **Título:** `Aviso de Seguridad del IES San Andrés`
    - **Texto:** `El uso de este equipo está monitorizado. El acceso está restringido únicamente a personal y alumnado autorizado.`

### 1.3. Privacidad y apagado

- **Privacidad:** Configura el inicio de sesión interactivo para que **no muestre el último nombre de usuario** que inició sesión.
- **Apagado:** Deshabilita la opción que permite **apagar el sistema sin tener que iniciar sesión**. Queremos evitar que alumnos apaguen equipos de aulas remotamente o desde la pantalla de bloqueo sin identificarse.



## Parte 2: Preferencias de Grupo (GPP) y segmentación

En esta sección usarás **Preferencias** (Configuración de usuario -> Preferencias) en lugar de Directivas (Policies). Debes crear una GPO llamada **`GPO_Configuracion_Usuario_Dinamica`** y vincularla a la raíz del dominio (o a las UOs de usuarios pertinentes).

### 2.1. Mapeo de unidades de red (Drive Maps)

Los profesores necesitan acceder a una carpeta compartida para sus materiales, pero los alumnos no deben ver esa unidad.

- Crea una unidad de red mapeada (ej. letra **`P:`**) que apunte a una carpeta compartida del servidor (puedes crear una carpeta compartida llamada `Recursos_Profesores` en el DC).
- **Requisito:** Usa **Item-Level Targeting (Destinatarios)** para que esta unidad **SOLO** se monte si el usuario pertenece al grupo `GRP_Profesores_General`.

### 2.2. Accesos Directos (Shortcuts)

El departamento de informática quiere un acceso directo a la Intranet en el escritorio, pero solo para los alumnos del ciclo **DAM**, ya que son los que están desarrollando la nueva web.

- Crea un acceso directo en el Escritorio que apunte a `http://intranet.iessanandres.local` (puedes inventar la URL).
- **Requisito:** Usa **Item-Level Targeting** para que este acceso directo **SOLO** aparezca a los miembros del grupo `GRP_Alumnos_DAM`.


## Parte 3: Filtrado WMI Avanzado

En la práctica anterior usaste un filtro simple para portátiles. Ahora realizaremos filtros basados en el hardware y el sistema operativo para aplicar directivas de rendimiento.

### 3.1. Gestión de Memoria Virtual

Existe una directiva de seguridad llamada **"Apagado: borrar el archivo de paginación de la memoria virtual"**. Esta directiva mejora la seguridad (evita que queden datos sensibles en el disco) pero ralentiza mucho el apagado, por lo que solo queremos aplicarla en equipos potentes.

1.  Crea una GPO llamada **`GPO_HighPerf_Security`**.
2.  Habilita la directiva mencionada (Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Local Policies -> Security Options).
3.  Crea y vincula un **Filtro WMI** que haga que esta GPO solo se aplique a equipos que tengan **más de 4 GB de memoria RAM**.
    * *Pista:* Tendrás que consultar la clase `Win32_ComputerSystem` y la propiedad `TotalPhysicalMemory` (el valor se expresa en bytes).

### 3.2. Diferenciación de Sistema Operativo (Workstation vs Server)

Queremos aplicar una configuración de **Control de Cuentas de Usuario (UAC)** específica, desactivando la "Detección de instalaciones de aplicaciones", pero **SOLO a los equipos Clientes (Windows 10/11)**, nunca a los Servidores del dominio.

1.  Crea una GPO llamada **`GPO_Clientes_UAC`**.
2.  Configura la directiva de UAC para deshabilitar la detección de instalaciones.
3.  Crea y vincula un **Filtro WMI** que seleccione únicamente sistemas operativos de escritorio (no servidores).
    - *Pista:* Consulta la clase `Win32_OperatingSystem` y la propiedad `ProductType`. El valor `1` corresponde a Workstation (Cliente), mientras que `2` y `3` son Servidores.
