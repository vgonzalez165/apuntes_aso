# Proyecto de la 2ª Evaluación


## Descripción del proyecto

Este proyecto consistirá en desarrollar un dominio para un centro educativo en el que se imparten ciclos formativos de la familia de informática y comunicaciones. Tu tarea será planificar dicho dominio **justificando** tus decisiones. Aunque se dan unos requisitos generales, eres libre de tomar cualquier decisión que no esté indicada en los requisitos.

## Situación inicial

Se indican las características del centro educativo:

- Se imparten los ciclos formativos de ASIR, SMR, DAM y DAW
- Cada ciclo formativo tiene un curso de primero y otro de segundo
- Los primeros cursos tienen 30 alumnos matriculados y los segundos cursos 15
- Los datos de los alumnos se encuentran en este [fichero](./alumnos.csv)
- Cada curso tiene un aula propia con 15 equipos con Windows 10
- Hay 15 profesores que pueden impartir clase en cualquier aula

## Requisitos

Debes realizar, por lo menos, las siguientes tareas:

- Establece una estructura de unidades organizativas y grupos, justificando las decisiones tomadas.
- Investiga cómo automatizar el alta de alumnos en el dominio investigando el comando `Import-csv`
- Cada alumno debe tener una carpeta personal. Investiga cómo automatizar esto y únelo al script anterior. Puedes empezar por [aquí](https://controlshiftesc.wordpress.com/2015/11/30/administrar-active-directory-con-powershell-modificar-perfil-de-usuario/)
- Cada grupo tendrá una carpeta personal compartida por todo el grupo
- Debes establecer las políticas del dominio para maximizar la seguridad. Estas políticas deben incluir, por lo menos, las directivas de `Configuración de seguridad`. Debes **justificar** las decisiones tomadas y desarrollar una correcta política de excepciones (por ejemplo, tiene sentido prohibir el cmd a los alumnos, pero habría que excluir de esta política a los profesores)

## Entrega

La entrega del proyecto consistirá en un documento que refleje la estructura del dominio, así como las decisiones tomadas. Este documento será un documento técnico que refleje la estructura del dominio, por lo que  **únicamente debe tener capturas de pantalla representativas** y cuya función será únicamente complementar las explicaciones.

Durante la corrección, y en caso de duda, el profesor podrá solicitarte que le muestres tu controlador de dominio para comprobar que todo se corresponde con la documentación entregada o para comprobar que realmente entiendes la estructura de tu dominio.


## Calificación del proyecto

Para la calificación del proyecto se tendrán en cuenta los siguientes aspectos:

| Concepto                              | Valoración  |
|---------------------------------------|-------------|
| Organización del dominio              | 1.5 puntos  |
| Automatización                        | 4 puntos    |
| Estructura de carpetas compartidas    | 1 punto     |
| Política de directivas                | 3.5 puntos  |

Para cada uno de los puntos anteriores, se evaluarán los siguientes aspectos:

### 1. Presentación de la documentación (25%)
- Se incluye toda la información necesaria.
- El formato es claro y estructurado.
- Uso adecuado de diagramas o esquemas si es necesario.

### 2. Claridad con que se presentan los datos (25%)
- La información es comprensible y bien explicada.
- No hay ambigüedades en la descripción de los procesos.
- Se utilizan términos técnicos adecuados.

### 3. Corrección de la solución (25%)
- Implementación funcional y sin errores.
- Se siguen buenas prácticas recomendadas. 
- Cumple con los requisitos establecidos.

### 4. Justificación de las decisiones tomadas (25%)
- Explicación clara de las decisiones tomadas.
- Razones técnicas bien argumentadas.
- Comparación con posibles alternativas cuando sea relevante.



