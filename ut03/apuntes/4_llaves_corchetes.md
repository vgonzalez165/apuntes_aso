# UT03: LENGUAJES DE SCRIPTING EN LINUX: BASH


## 4.- Llaves, corchetes y paréntesis

Algo que te habrá llamado la atención en lo que hemos visto hasta ahora es el profuso uso de paréntesis, llaves y corchetes y diversas situaciones, lo que puede llegar a ser confuso en múltiples ocasiones.

Pongamos un poco de orden en todo este caos.

### 4.1.- Paréntesis sencillos

Rodear un comando entre paréntesis hará que se ejecute dentro de un **subshell**. Esto significa que ejecutará todos los comandos que hayamos puesto entre los paréntesis y luego retorne un código de salida.

Cualquier variable que hayamos creado o cambio realizado en el entorno se perderá al salir del subshell.

```bash
a='asir'
( a=dam; mkdir $a )
echo $a     # asir
ls          # Se ha creado el directorio dam
```

### 4.2.- Doble paréntesis

Se utilizan para la aritmética de enteros. Permite realizar asignaciones, operaciones lógicas y operaciones matemáticas como módulo o la multiplicación. 

Cualquier cambio en las variables que se realice dentro se mantendrá, pero no podremos asignar el resultado a nada. Si el resultado de la operación no es cero, devolverá un código de salida `0` (éxito), en caso contrario el código de salida será `1`

```bash
i=4
(( i += 3 ))
echo $i             # 7 
(( 4 + 8 ))         # No hay salida
echo $?             # 0
(( 5 - 5 ))         
echo $?             # 1

# No se puede usar en una expresión
a=(( 4 + 1 ))       # => bash: syntax error near unexpected token '('
```

### 4.3.- Paréntesis precedidos de símbolo menor


### 4.4.- Dólar y paréntesis simples

Se utiliza para obtener la salida de un comando en una cadena. El comando que se indique entre paréntesis se ejecutará en un subshell y toda la expresión se reemplazará por la salida del comando.

```bash
str="Mi nombre es $(whoami)"
echo $str
```

### 4.5.- Dólar y paréntesis simples, seguidos de `$?`

Debemos utilizar esta expresión si queremos ejecutar un comando en un subshell y quedarnos con el estado de salida, no la cadena de salida.

```bash
if [[ $( grep -q PATTERN FILE )$? ]]
then
   echo "Se encontró la expresión regular en el fichero"
else
   echo "No se encontró la expresión regular"
```

### 4.6.- Dólar y doble paréntesis

Te acuerdas del segundo punto, utilizar doble paréntesis realiza la operación pero no hace nada con el resultado. Si queremos quedarnos con el resultado debemos utilizar la expresión `$(( command ))`. Esto realizará la operación requerida y devolverá el resultado de la operación.

```bash
a=9
b=$(( a *= 2 ))
echo $b
```

### 4.7.- Corchetes simples

La expresión `[ command ]` es la versión alternativa al comando `test`, como ya hemos visto anteriormente.


### 4.8.- Dobles corchetes

La funcionalidad de los dobles corchetes es muy similar a la de los corchetes simples, pero hay una diferencia fundamental: `[` es realmente un comando del sistema (lo puedes encontrar el el directorio `/bin`), mientras que `[[ ]]` forma parte de Bash. Esto significa que todo lo que pongamos dentro de los dobles corchetes **no será tratado como argumentos**. Por ello, si queremos alguna funcionalidad propia de los argumentos (como, por ejemplo, la expansión de nombres de fichero), debemos utilizar los corchetes simples.

Observa el siguiente ejemplo:

```bash
victor@SERVER:~$ ls
script.sh
victor@SERVER:~$ [[ -f *.sh ]]  # Busca literalmente un archivo llamado *.sh
victor@SERVER:~$ echo $?
1
victor@SERVER:~$ [ -f *.sh ]    # Se expande a todos los ficheros con extensión sh
victor@SERVER:~$ echo $?
0
```


**Fuente**: [https://dev.to/rpalo/bash-brackets-quick-reference-4eh6](https://dev.to/rpalo/bash-brackets-quick-reference-4eh6)