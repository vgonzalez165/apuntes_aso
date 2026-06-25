```
---------------- ADMINISTRACIÓN DE SISTEMAS INFORMÁTICOS Y REDES ----------------
---------------------------------------------------------------------------------

Módulo:                     ADMINISTRACIÓN DE SISTEMAS OPERATIVOS
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT06
Práctica:                   PR0601. Introducción a PowerShell
Resultados de aprendizaje:  RA
```

# PR0601:  Introducción a Powershell

## Objetivo

El objetivo de esta práctica será demostrar destreza en la búsqueda de comandos, comprensión de objetos y uso avanzado del pipeline para filtrar y formatear datos.


### Bloque 1: Descubrimiento y ayuda


1.  **Búsqueda por nombre (Sustantivo):** lista todos los comandos disponibles en el sistema que tengan la palabra `Service` en su nombre (noun) para identificar qué herramientas tienes para gestionar servicios.
2.  **Búsqueda por acción (Verbo):** lista todos los comandos disponibles cuya acción sea `Stop` (detener), independientemente de lo que detengan.
3.  **Uso de la ayuda:** muestra por pantalla la ayuda detallada del comando `Get-Process`, pero asegúrate de que se muestren específicamente los **ejemplos** de uso.


### Bloque 2: Exploración de objetos

4.  **Introspección de tipos:** ejecuta el comando para obtener la fecha actual (`Get-Date`) pero canaliza su salida para ver la lista de sus **Miembros** (Members).
      * Responde: ¿Cuál es el `TypeName` del objeto devuelto?.
5.  **Identificación de Propiedades vs Métodos:** usando el comando `Get-Member` sobre un proceso cualquiera (ej: `Get-Process`), identifica el nombre de un **Método** que permita finalizar (matar) el proceso.


### Bloque 3: El Pipeline (selección y ordenación)


6.  **Selección de columnas:** obtén la lista de todos los procesos, pero muestra por pantalla **únicamente** las propiedades `Id` y `ProcessName`. El resto de información debe ser descartada.
7.  **Ordenación básica:** lista todos los procesos del sistema, ordenados por su consumo de `CPU` de forma **descendente** (el que más consume primero).
8.  **Formato de tabla:** obtén los servicios del sistema y fuerza la salida para que se muestre como una tabla (`Format-Table`) que se auto-ajuste (`-AutoSize`) al ancho de la ventana.


### Bloque 4: Filtrado y lógica (Where-Object)

9.  **Filtrado exacto:** muestra una lista de los servicios cuyo estado (`Status`) sea exactamente igual (`-eq`) a "Running".
10. **Filtrado numérico:** lista los procesos cuyo identificador (`Id`) sea mayor que (`-gt`) 2000.
11. **Filtrado con comodines:** busca y muestra todos los procesos cuyo nombre (`Name`) comience por la letra "s" utilizando el operador `-like` y el comodín adecuado.


### Bloque 5: Agrupación y estadísticas

12. **Agrupación de datos:** agrupa todos los servicios del sistema en función de su `Status`. El comando debe devolverte cuántos hay en cada grupo.
13. **Cálculo estadístico:** obtén el listado de archivos del directorio actual (`Get-ChildItem`). Usando una tubería, calcula el **promedio** (`Average`) de la propiedad `Length` (tamaño) de todos los archivos.


### Bloque 6: Gestión del Historial

14. **Consulta de actividad:** muestra por pantalla la lista de los últimos comandos que has ejecutado en la sesión actual.
15. **Exportación de datos:** exporta todo tu historial de comandos actual a un archivo en formato **CSV** llamado `historial_lab.csv`.

