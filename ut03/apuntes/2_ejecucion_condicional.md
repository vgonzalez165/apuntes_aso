# UT03: LENGUAJES DE SCRIPTING EN LINUX: BASH


## 2.- Ejecución condicional

### 2.1.- Bifurcación condicional


El comando estructurado más sencillo de que disponemos es el comando `if-then`. La sintaxis de este comando es la siguiente:

```bash
if comando
then
	comandos
fi
```

El formato de este comando puede resultar confuso al principio ya que no funciona como en otros lenguajes de programación en los que se evalúa una expresión para saber si es falsa o verdadera.

El comando `if` ejecuta el comando indicado en la misma línea que el `if`. Si el estado de salida de este comando es cero (el comando se ha ejecutado con éxito), los comandos indicados en las siguientes líneas se ejecutan. Si el estado de salida del comando tiene otro valor el entonces el `shell` se salta todas las líneas y continúa ejecutando el comando después de la línea con el comando `fi`.

```bash
#!/bin/bash
usuario=victor
if grep $usuario /etc/passwd > /dev/null
then
        echo "Los ficheros bash para $usuario son"
        ls -a /home/$usuario/.b*
fi
```
 
### 2.2. - El comando `if-then-else`

El comando `if-then-else` va un poco más allá del comando `if-then` permitiendo especificar dos grupos de comandos, uno que se ejecutará si el estado de salida del comando es cero y otro para ejecutar si el estado del comando no es cero.

La sintaxis del comando es:

```bash
if comando
then
	comandos
else
	comandos
fi
```

### 2.3.- El comando `test`

Por lo que se ha visto hasta ahora el comando `if` es poco versátil ya que únicamente es capaz de evaluar si el resultado de la ejecución de un comando es correcta o no.

Para subsanar este inconveniente podemos recurrir al comando `test`. Este comando toma como parámetro una condición y si el resultado de evaluar esa condición de verdadero finaliza con el código de estado de salida 0.

La sintaxis de este comando es muy sencilla:

```bash
test condición
```

Por lo que su utilización junto con el comando `if-then` será de la forma:

```bash
if test condición
then
comandos
fi
```

El shell Bash proporciona una forma alternativa de declarar el comando test dentro de un comando `if-then`.

```bash
if [ condición ]
then
comandos
fi
```

Con esta sintaxis hay que ser muy cuidadoso ya que requiere un espacio en blanco después del corchete de apertura y otro espacio en blanco antes del corchete de cierre. En caso contrario el comando dará error.

Hay tres tipos de condiciones que el comando test puede evaluar:
- Comparaciones numéricas
- Comparaciones de cadenas
- Comparaciones de ficheros

#### 2.3.1.- Comparaciones numéricas

El uso más común del comando `test` es para realizar comparaciones entre dos números. Los valores a comparar pueden venir dados tanto por un número como por una variable.

```bash
if [ $val1 -gt 5 ]
then
        echo "El valor $val1 es mayor que 5"
fi
```

Los diferentes operandos que podemos utilizar cuando realizamos comparaciones entre números vienen dados en la siguiente tabla.

**Comparaciones Numéricas en el comando test**

| Comparación  | Descripción                             |
|--------------|-----------------------------------------|
| `n1 –eq n2` | Comprueba si n1 es igual a n2           |
| `n1 –ge n2` | Comprueba si n1 es mayor o igual que n2 |
| `n1 –gt n2` | Comprueba si n1 es mayor que n2         |
| `n1 –le n2` | Comprueba si n1 es menor o igual que n2 |
| `n1 –lt n2` | Comprueba si n1 es menor que n2         |
| `n1 –ne n2` | Comprueba si n1 no es igual a n2        |

#### 2.3.2.- Comparaciones de cadenas

El comando `test` dispone de las siguientes comparaciones de cadenas:

Comparaciones de Cadenas en el comando test
Comparación	Descripción
str1 = str2	Comprueba si str1 es la misma cadena que str2
str1 ¡= str2	Comprueba si str1 no es la misma cadena que str2
str1 < str2	Comprueba si str1 es menor que str2
str1 > str2	Comprueba si str1 es mayor que str2
-n str1	Comprueba si str1 tiene una longitud mayor que cero
-z str1	Comprueba si str1 tiene una longitud de cero
Las comparaciones de igualdad o desigualdad tienen una funcionalidad bastante clara, determinando si las cadenas son iguales o si por el contrario son diferentes.
Hay que tener en cuenta al realizar estas comparaciones que las mayúsculas y las minúsculas son tratadas como caracteres diferentes.
 
En lo que respecta a las comparaciones mayor que y menor que entre cadenas nos podemos encontrar con dos problemas importantes:
•	Los símbolos > y < tienen un significado especial para el shell (redireccionamiento de ficheros) por lo que se hace necesario escaparlos para poder utilizarlos. En caso de no escaparlos el script no dará ningún mensaje de error, pero el resultado no será el esperado.
 
 
•	El segundo problema es que el orden que utiliza el comando test no es el mismo que el utilizado por el comando sort. Esto se debe a que el comando test utilizar la comparación en función de los valores ASCII de los caracteres (las mayúsculas aparecen antes que las minúsculas) mientras que el comando sort utiliza el orden definido por la configuración local del sistema.
Las otras dos comparaciones serán útiles para comprobar si una cadena contiene datos o no.
2.3.3.- COMPARACIONES DE FICHEROS
La última categoría de comparaciones nos va a permitir verificar el estado de ficheros y directorios en el sistema de ficheros. Las diferentes opciones vienen dadas por la siguiente tabla:
Comparaciones de Ficheros en el comando test
Comparación	Descripción
-d file	Comprueba si file existe y es un directorio
-e file	Comprueba si file existe
-f file	Comprueba si file existe y es un fichero
-r file	Comprueba si file existe y tiene permisos de lectura
-s file	Comprueba si file existe y no está vacío
-w file	Comprueba si file existe y tiene permisos de escritura
-x file	Comprueba si file existe y tiene permisos de ejecución
-O file	Comprueba si file existe y el propietario es el usuario actual
-G file	Comprueba si file existe y el grupo propietario es el grupo del usuario actual
file1 –nt file2	Comprueba si file1 es más reciente que file2
file1 –ot file2	Comprueba si file2 es más antiguo que file2
Las opciones son:
•	Comprobando objetos: El comando –d comprueba si valor indicado corresponde a un directorio existente en el sistema. Es útil para comprobar si un directorio existe antes de copiar un fichero en él o antes de moverse a ese directorio. La comparación –e permite verificar si un directorio o fichero existe. Finalmente, la comparación –f nos va a permitir averiguar si un fichero indicado existe en el sistema de ficheros.
 
 
•	Verificación de la lectura: Antes de intentar leer datos de un fichero es interesante comprobar si realmente podemos leer de ese fichero. Esta verificación la podemos realizar con el comparador –r.
•	Verificando si un fichero está vacío: La comparación –s permite verificar si un fichero existe, pero está vacío, es decir, no tienen ningún dato almacenado en él. Hay que tener en cuenta que esta comparación devuelve verdadero si el fichero contiene datos.
•	Verificación de la escritura: De forma similar a la verificación de la escritura, el comparador –w nos permitirá verificar si tenemos permisos para escribir en un fichero antes de hacerlo.
•	Verificación de la ejecución: Dado que existe un comparador para verificar la lectura y otro para verificar la escritura resulta obvio que haya un tercero para comprobar si tenemos permisos de ejecución sobre un fichero. Este será el comparador –x.
•	Comprobando el propietario: Con el comparador –O podremos verificar la pertenencia del objeto. Hay que tener en cuenta que esta opción no nos permite comprobar si el propietario es otro usuario, solamente si es el usuario actual. De forma similar el comparador –G nos permitirá saber si el grupo propietario del objeto es el del usuario actual.
 
•	Fecha del fichero: El último grupo de comparaciones nos permite evaluar dos ficheros y saber cuál tiene una fecha de creación anterior. Esto puede ser útil al escribir scripts que instalen software para evitar instalar ficheros anteriores a los que ya tenemos en el sistema. La comparación –nt nos sirve para saber si un fichero es más nuevo que otro mientras que la comparación –ot nos indica si es más antiguo.
2.4.- CONDICIONES COMPUESTAS
El comando if-then nos permite también utilizar lógica booleana para combinar comprobaciones. Se puede utilizar dos operadores booleanos.
•	[ condición 1 ] && [ condición 2 ]
•	[ condición 1 ] || [ condición 2 ]
El primer operador realiza el AND booleano para combinar las dos condiciones, debiendo cumplirse ambas. Mientras que el segundo operador corresponde al OR booleano, en el que únicamente es necesario que se cumpla una de las dos condiciones.
Otra utilidad de los operadores && y || es enlazar diversos comandos condicionando la ejecución de al resultado de la ejecución de los anteriores.
El operador && ejecuta el segundo comando únicamente si el primer comando se ejecuta con éxito. Por ejemplo, la siguiente línea comprueba si un directorio existe y en caso afirmativo entra en él.
 
El operador || ejecuta el segundo comando únicamente si el primer comando falla en la ejecución. Por ejemplo, la siguiente orden intenta acceder a un directorio y si falla sale con el código de error 1.
 
Incluso se pueden combinar ambos operadores.
 

2.5.- EL COMANDO CASE
Hay ocasiones en las que se quieren ejecutar diferentes secuencias de código en función del valor de una variable. Aunque esta operación se puede realizar con varios comandos if, el shell nos proporciona un comando más adecuado para ello. Este es el comando case. Este comando compara una variable con múltiples valores y ejecuta secuencias de código diferentes para cada valor. 
Su sintaxis es:
case variable in
patron1 | patron2) comandos;;
patron3) comandos;;
*) comandos;;
esac
El comando case compara la variable especificada con los diferentes patrones. Si la variable coincide con un patrón, el shell ejecuta los comandos especificados para dicho patrón. Se puede indicar más de un patrón en una línea utilizando el símbolo barra para separarlos. El símbolo * permitirá especificar los comandos por defecto a ejecutar para el caso de que la variable no coincida con ningún patrón.
 
2.6.- BUCLES
Los bucles sirven para ejecutar varias veces un conjunto de comandos. El shell proporciona tres tipos de bucles: while, until y for. Los dos primeros se ejecutan hasta que una condición sea verdadera o falsa, y el tercer itera sobre una lista de valores.
2.6.1.- EL COMANDO WHILE
El comando while ejecuta un conjunto de comandos mientras la condición sea verdadera. Su sintaxis es:
while <condición>
do
	<comandos>
done
Por ejemplo, incrementando una variable en cada iteración, se puede forzar que se ejecute un número determinado de veces. 
 
Observa en el ejemplo anterior la sintaxis utilizada para realizar una operación matemática sobre una variable utilizando el símbolo $ y con la operación entre dobles paréntesis. Alternativamente, se podría haber utilizado el comando let.
 
Los bucles while pueden ser utilizados para leer un fichero línea a línea:
 
2.6.2.- EL COMANDO UNTIL
Es el opuesto al comando while, se ejecutan los comandos mientras la condición sea falsa.
 

2.6.3.- EL COMANDO FOR
El comando for nos permitirá iterar una variable sobre una serie de valores. Cada iteración ejecutará los comandos definidos tomando el valor que le corresponda a la variable para esa iteración. El formato básico para este comando es:
for var in lista
do
comandos
done
Los valores que va a tener la variable se pueden especificar de varias maneras.
2.6.3.1.- INDICANDO LOS VALORES EN UNA LISTA
La forma más sencilla es enumerar los valores uno a uno en una lista dentro del propio comando for.
 
Un problema con nos podemos encontrar aquí es cuando queremos que alguno de los valores no sea una palabra, sino que sea una frase o una palabra compuesta. La solución pasa por rodear los valores compuestos de más de una palabra entre comillas.
 
2.6.3.2.- RECORRIENDO UN RANGO DE NÚMEROS
Si queremos que la variable itere sobre un rango de números podemos hacerlo especificándolo de la siguiente manera:
	for var in {1..10}
Esto realizará 10 iteraciones en el bucle asignándole a la variable i los valores 1, 2, 3,…
2.6.3.3.- LEYENDO LA LISTA DE UNA VARIABLE
Otra opción que tenemos es que podemos utilizar una variable para ir acumulando los valores en la lista y luego indicar dicha variable en el comando for, como se puede ver en el siguiente ejemplo.
 
2.6.3.4.- LEYENDO LOS VALORES DE UN COMANDO
Otra opción más es obtener los diferentes valores que va a tener la variable en las sucesivas iteraciones de la ejecución de un comando. Para ello utilizamos $( ). 
 
Un aspecto que hay que tener en cuenta cuando utilizamos esta opción es el carácter que utiliza el shell para separar los diferentes valores que tomará la variable. Si en el ejemplo anterior en una línea hay más de una palabra veremos que la variable no tomará el valor de la línea completa, sino que realizará una iteración para cada una de las palabras del texto.
La causa de este comportamiento se debe a la variable de entorno IFS, denominada separador interno de caracteres (internal field separator). La variable de entorno IFS define una lista de caracteres que utiliza el shell como separadores de campo. Por defecto esta variable define los siguientes separadores:
•	El espacio
•	El carácter de tabulador
•	El salto de línea
Por lo tanto, si queremos por ejemplo que solamente nos tome los saltos de línea como separadores de campo, la solución será modificar temporalmente el valor de la variable IFS, indicándolo de la forma:
IFS=$’\n’
Hay que tener cuidado de volver a dejar el valor anterior que tenía esta variable, por lo que es aconsejable almacenar en una variable temporal su valor y volver a restablecerlo antes de finalizar el script.
 
También podemos utilizar esta variable para definir otros separadores. Si por ejemplo queremos recorrer todos los valores individuales del fichero /etc/passwd, que están separados por el símbolo dos puntos (:), lo podemos hacer de la forma:
 
2.6.3.5.- LEYENDO UN DIRECTORIO UTILIZANDO CARACTERES COMODÍN
Finalmente, la última posibilidad del comando for es recorrer una serie de ficheros de un directorio. Para hacer esto podemos utilizar los caracteres comodín dentro de una ruta especificada en el comando for.
 
En este ejemplo hay que fijarse en que rodeamos la variable en el comando if entre comillas dobles. Esto porque en Linux es posible definir nombres de ficheros que tengan espacios en medio, por lo que al rodearlo de comillas forzamos a que trate a estos nombres compuestos como una única cadena. Si no hacemos esto y hay algún nombre compuesto el comando if nos dará un error ya que encontrará más parámetros de lo esperado.
Por último, señalar que también podemos especificar varias rutas dentro del comando for de la siguiente manera.
 

 
