```
---------------- ADMINISTRACIÓN DE SISTEMAS INFORMÁTICOS Y REDES ----------------
---------------------------------------------------------------------------------

Módulo:                     ADMINISTRACIÓN DE SISTEMAS OPERATIVOS
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT06
Práctica:                   PR0604. Manipulación de colecciones
Resultados de aprendizaje:  RA7
```



# PR0604: Manipulación de colecciones en powerShell

## Bloque I: Arrays fijos



**1. Reconfiguración de DNS y Verificación**

Como administrador, tienes un script que carga los servidores DNS de una sucursal en un array fijo. Se ha detectado que el servidor secundario está caído y la configuración actual es errónea.

- **Pasos a realizar:**

    1.  Declara el array con los valores iniciales: `192.168.1.10` (Primario) y `10.0.0.50` (Secundario erróneo).
    2.  Muestra en pantalla: "Configuración actual: [Primario] - [Secundario]".
    3.  Accede directamente al índice correspondiente y cambia la IP del secundario por la de Google: `8.8.8.8`.
    4.  Muestra en pantalla el número total de servidores DNS configurados.
    5.  Muestra la configuración final corregida.

**2. Rotación de logs de backups (LIFO - Last In, First Out)**

Un sistema antiguo guarda los nombres de los últimos 3 backups en un array. Debes identificar cuál es el más antiguo y cuál el más nuevo para un informe, asumiendo que el índice 0 es el más antiguo.

- **Pasos a realizar:**
- 
    1.  Declara el array con las siguientes cadenas: `Backup_Lunes.zip`, `Backup_Martes.zip`, `Backup_Miercoles.zip`.
    2.  Guarda en una variable `$oldest` el primer elemento.
    3.  Guarda en una variable `$newest` el último elemento.
    4.  El backup del Miércoles ha salido corrupto. Modifica el último elemento del array añadiéndole el texto " (CORRUPTO)" al final del nombre.
    5.  Imprime un resumen: "Rotación de backups: Del [Viejo] al [Nuevo]".



## Bloque II: ArrayLists


**3. Gestión de Cola de incidencias (Priorización)**

Estás programando un dashboard de soporte técnico. Las incidencias llegan y se ponen en cola. De repente, llega una incidencia crítica del CEO que debe saltarse la cola.

- **Pasos a realizar:**
- 
    1.  Crea un `ArrayList` vacío.
    2.  Añade las incidencias: "Monitor parpadea" y "Ratón no va". (Recuerda ocultar la salida por pantalla del `.Add()`).
    3.  Llega una urgencia: Inserta en la posición 0 la incidencia: "SERVIDOR CAÍDO".
    4.  El técnico resuelve la incidencia del "Ratón". Búscala por su nombre y elimínala de la lista.
    5.  Imprime la lista actual de tareas pendientes y cuenta cuántas quedan.

**4. Validación de lista negra de IPs (Seguridad)**

Tu script de firewall recibe IPs sospechosas y debe decidir si bloquearlas o no.

- **Pasos a realizar:**

    1.  Inicializa un `ArrayList` con las IPs ya bloqueadas: `10.10.10.5`, `192.168.50.4`, `80.80.80.80`.
    2.  Declara una variable `$nuevaAmenaza = "192.168.50.4"`.
    3.  Usa una estructura `if` para:
        - Si la IP ya está en la lista, muestra: "La IP [IP] ya estaba bloqueada. No se hace nada."
        - Si no está, añádela a la lista y muestra: "IP [IP] añadida a la lista negra."
    4.  Muestra la lista final ordenada alfabéticamente


#### Bloque III: Listas genéricas 


**5. Hardening de puertos de Firewall (List[int])**

Vamos a configurar una regla de firewall que solo permite ciertos puertos numéricos.

- **Pasos a realizar:**

    1.  Instancia una nueva lista genérica que solo acepte Enteros: `[System.Collections.Generic.List[int]]::new()`.
    2.  Añade los puertos estándar: 80, 443, 53.
    3.  Por error, alguien abrió el puerto Telnet (23). Añádelo a la lista.
    4.  Auditoría de seguridad: Elimina el puerto 23 de la lista.
    5.  Intenta añadir (solo escribe la línea comentada) el texto "HTTP" a la lista y explica en un comentario qué error daría.
    6.  Muestra la lista de puertos permitidos ordenada de menor a mayor.

**6. Inventario de servicios críticos (List[string])**

Necesitas monitorizar una lista de servicios de Windows.

- **Pasos a realizar:**
    1.  Crea una lista genérica de Strings a partir de un array existente (`::new($array)`):
        Array origen: `Spooler`, `W3SVC`, `LanmanWorkstation`.
    2.  Te das cuenta de que falta el servicio de actualizaciones. Añade "wuauserv".
    3.  Ordena la lista alfabéticamente.
    4.  Recorre la lista (foreach) y para cada elemento muestra el mensaje: "Monitorizando servicio: [NombreServicio]... OK".



#### Bloque IV: Manipulación de texto 

**7. Análisis de Log de usuario**

Tienes una línea de log cruda extraída de un servidor Linux. Necesitas sacar el usuario y la IP limpia.

- **Entrada:** `$logLine = "  User: admin ; IP: 192.168.1.55 ; Status: Failed  "` (Nota los espacios extra al principio y final).
- **Pasos a realizar:**
    1.  Limpia los espacios en blanco sobrantes del principio y el final de la cadena original.
    2.  Divide la cadena usando el punto y coma `;` como separador.
    3.  El primer elemento será "User: admin". Vuelve a dividirlo para quedarte solo con "admin".
    4.  El segundo elemento es la IP. Límpialo para obtener solo "192.168.1.55".
    5.  Muestra un mensaje final claro: "ALERTA: El usuario [Usuario] intentó conectar desde [IP]".


**8. Generador de CSV para recursos humanos**

RRHH te pide un listado de los correos de los nuevos empleados separados por comas para importarlos en su herramienta de Mailing.

- **Pasos a realizar:**
    1.  Tienes tres variables con correos: `$m1="ana@empresa.com"`, `$m2="luis@empresa.com"`, `$m3="bea@empresa.com"`.
    2.  Crea un array temporal que contenga esas tres variables.
    3.  Utiliza el operador `-join` para crear una única cadena de texto separada por comas `,`.
    4.  Al resultado final, añádele manualmente el encabezado "EmailAddress," al principio.
    5.  Muestra el resultado, que debería parecerse al contenido de un archivo .csv.