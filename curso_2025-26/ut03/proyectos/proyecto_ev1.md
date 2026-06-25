# PROYECTO DE LA PRIMERA EVALUACIÓN


## Creación de un script de gestión de 

### 1.- Objetivo

Vamos a crear un script en Bash que nos ayude a automatizar la configuración inicial de un servidor Linux. Esta configuración inicial consistirá en:

- Configuración de la red
- Actualización y descarga de paquetes
- Automatización de copias de seguridad
- Creación de usuarios y grupos

El script se mostrará como un menú donde el usuario podrá escoger entre las cuatro opciones. A continuación, se desarrollan estas cuatro opciones:

#### Configuración de la red

Se solicitará al usuario los siguientes datos:

- Dirección IP a asignar al equipo
- Dirección IP de la puerta de enlace


El script aplicará la configuración de red indicada por el usuario y realizará una prueba de conectividad con la puerta de enlace.

#### Actualización y descarga de paquetes

El script realizará una actualización de los repositorios e instalará algunos paquetes automáticamente. Como ejemplo, supondremos que se instalarán los paquetes `git`, `curl`, `ufw` y `wget`.

#### Automatización de copias de seguridad

