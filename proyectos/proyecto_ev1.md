# Proyecto de la 1ª Evaluación

## xxxxxxxxxx

### Descripción del proyecto

El objetivo es desarrollar un script en Bash que permita gestionar tareas programadas en el sistema utilizando `cron`. Con este proyecto, los alumnos aprenderán a automatizar tareas repetitivas en el sistema y a manejar cron jobs (trabajos programados), lo cual es fundamental en la administración de sistemas para realizar backups, limpieza de archivos, monitoreo, entre otras tareas.

Queremos realizar un script que nos permita automatizar la creación de copias de seguridad en un sistema Linux.

### Estructura del Proyecto

Dispondrá de un menú con las siguientes opciones:

- Crear nueva copia de seguridad


Cuando el usuario seleccione la opción "Crear nueva copia de seguridad" se le pedirá lo siguiente:

- Directorios que quiere incluir en la copia de seguridad
- Directorio destino de la copia de seguridad
- Temporalización de la copia de seguridad, en formato `crontab`

El proyecto se puede dividir en las siguientes secciones:

- Creación de tareas programadas: configurar tareas en `cron` usando un script.
- Visualización de tareas programadas: mostrar las tareas programadas existentes en el sistema.
- Eliminación de tareas programadas: eliminar tareas programadas específicas.
- Registro de eventos: registrar en un archivo de log cada tarea que se crea, elimina o modifica.
- Automatización y pruebas: validar la funcionalidad del script para asegurarse de que maneja correctamente las tareas de `cron`.