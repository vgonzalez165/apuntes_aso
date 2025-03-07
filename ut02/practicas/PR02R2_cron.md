# PR02R2: Gestión de procesos y programación de tareas


## Entorno de trabajo

Para esta práctica sólo necesitas una máquina virtual que tenga Ubuntu Server instalado.


## Enunciado

Desarrolla un sistema automatizado en un entorno Linux para gestionar procesos y programar tareas utilizando cron. El proyecto consiste en crear un conjunto de scripts en Bash que realicen las siguientes funciones:


1. **Copia de seguridad**

   - Crea un script que realice una copia de seguridad comprimida (usando `tar` o `zip`) de un directorio específico (por ejemplo, `/var/www/html`).
   - La copia de seguridad debe incluir la fecha y hora en el nombre del archivo para facilitar su identificación.
   - Almacena las copias de seguridad en un directorio dedicado (`/backups`).
   - Implementa un mecanismo para eliminar automáticamente las copias de seguridad con más de 30 días de antigüedad.


2. **Limpieza de archivos temporales**

   - Desarrolla un script que limpie archivos temporales en directorios específicos (por ejemplo, `/tmp` y `/var/tmp`).
   - Eliminará los archivos que no hayan sido modificados en los últimos 7 días.
   - Registra las operaciones realizadas en un archivo de log (`/var/log/cleanup.log`).


3. **Monitorización de recursos del sistema:**
   
   - Crea un script que monitorice el uso de CPU, memoria y espacio en disco.
   - Si el uso de alguno de estos recursos supera un umbral predefinido (por ejemplo, 80%), el script debe enviar un aviso al administrador del sistema.
   - Para enviar los avisos utilizaremos el comando `write`, que permite enviar un mensaje a la consola de un usuario que esté conectado en ese momento en el sistema. Puedes ver su funcionamiento [aquí](https://nebul4ck.wordpress.com/comandos-para-la-mensajeria-desde-linea-de-comandos/)
   - Los mensajes no los envíes a `root`, sino al nombre del usuario que es administrador.
   - Guarda un historial de los datos monitorizados en un archivo de log (`/var/log/system_monitor.log`).


4. **Programación con `cron`**
   
   - Configura cron para ejecutar los scripts de la siguiente manera:
     - El script de copia de seguridad debe ejecutarse diariamente a las 2:00 AM.
     - El script de limpieza de archivos temporales debe ejecutarse cada lunes a las 3:00 AM.
     - El script de monitoreo de recursos debe ejecutarse cada 5 minutos.


## A tener en cuenta

- Asegúrate de que los scripts manejen errores adecuadamente (por ejemplo, si no se puede crear la copia de seguridad o si no hay archivos temporales para eliminar).
- Vamos a utilizar el comando `write` para implementar un sistema de notificaciones al administrador para informar sobre el éxito o fallo de cada tarea programada.






