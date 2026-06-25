# PR04R1: Ejercicios Powershell

Realiza las siguientes tareas en Powershell:

### 1.- Gestión de usuarios desde CSV

Crea un script en Powershell que lea un archivo CSV con nombres de usuario, contraseñas y grupos, y cree los usuarios localmente.


### 2.- Informe del sistema

Crea un script que genere un informe del estado del sistema y lo guarde en un archivo.

Debe incluir:
- Espacio libre de los discos
- Memoria RAM disponible
- Uptime del sistema
- Fecha y hora actual
- Guardar el informe en formato TXT o HTML


### 3.- Búsqueda de archivos grandes

 Crea un script que busque todos los archivos que superen un cierto tamaño en un directorio y sus subdirectorios. Para cada archivo encontrado que cumpla los requisitos mostrará la ruta, el tamaño y la fecha de modificación.


### 5.- Backup selectivo

Crea un script que copie todos los archivos modificados en los últimos 7 días a una carpeta de respaldo. Debe incluir los subdirectorios y conservar la estructura.


### 6.- Gestión de servicios

Crea un script que liste los servicios que no estén iniciados, y de la opción al usuario de iniciarlos uno a uno.


### 7.- Compro

Crea un script que compruebe si una lista de hosts ubicada en un archivo de texto está accesible (ping). Los resultados se guardarán en un archivo de log.


### 8.- Cambiar extensiones de archivos

Crea un scritp que cambie la extensión de todos los archivos `.txt` por `.bak` en una carpeta. Además, debe contar el número de archivos que se han modificado.


### 9.- Script interactivo con menú

Crea un script con un menú en consola (tipo "1. Ver procesos", "2. Ver espacio en disco", etc.). Puedes poner las opciones que tú quieras (con 3 ó 4 es suficiente). 

Debes utilizar los comandos `while` y `switch` para que el usuario pueda escoger cada opción por su número y se mantenga ejecutando hasta que el usuario escoja la opción de "Salir".


