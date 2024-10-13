# UT03: LENGUAJES DE SCRIPTING EN LINUX: BASH


## 1.- Conceptos básicos de la creación de scripts

### 1.1.- Creación de scripts

A la hora de crear un script hay que tener en cuenta que la primera línea ha de indicar el shell que se utilizará para procesar dicho script. Esta línea deberá ir precedida del **símbolo `#!`** de la siguiente forma:

```bash
#!/bin/bash
```

En el resto del script el carácter `#` servirá para insertar los comentarios, por lo que **cualquier línea que comience por un # no será procesada**.

Después de indicar el shell indicaremos los comandos que se ejecutarán el script cada uno en una línea diferente.

```bash
#!/bin/bash
# Este script muestra los usuarios logados en el sistema
date
who
```

También hay que tener en cuenta dos cosas más antes de ejecutar el script:

- Primero debemos asegurarnos de que el fichero tenga **permisos de ejecución** y en caso contrario debemos dárselos.
- En segundo lugar, debemos referenciar el script a ejecutar mediante su ruta (absoluta o relativa). Alternativamente a esto podemos guardar el script en uno de los directorios incluidos en el `PATH` (podemos ver estos ficheros mediante la orden `echo $PATH`)


### 1.2.- Mostrar mensajes por pantalla

#### 1.2.1.- El comando `echo`

Para mostrar un mensaje por pantalla durante la ejecución del script debemos utilizar el comando `echo` cuyo cometido es mostrar por pantalla el texto indicado a continuación.

En este comando no es necesario indicar el texto a mostrar entre comillas. Sin embargo, si el texto incluye comillas el resultado puede no ser el esperado.

```bash
victor@SERVER:~$ echo 'Hola mundo!!!' 
Hola mundo!!!
```

Este comando admite tanto comillas simples como comillas dobles. 

Otra característica de este comando es que por defecto incluye un salto de línea después de la cadena a mostrar. Si no deseamos que tenga este comportamiento lo podemos evitar utilizando el parámetro `–n`.

#### 1.2.2.- Imprimiendo datos con formato

El comando del shell `printf` deriva de la función del mismo nombre del lenguaje de programación C y se basa en el uso de una cadena donde se indica cómo se presentarán el resto de argumentos.

Su sintaxis es:

```bash
printf FORMAT ARG …
```

La cadena FORMAT puede tener caracteres ordinarios, secuencias de escape y especificadores de formato. 
•	Los caracteres ordinarios son impresos tal cual en la salida
•	Las secuencias de escape son convertidas a los caracteres que representan. La secuencia de escape más utilizada es \n, que representa un salto de línea.
•	Los especificadores de formato son reemplazados por los argumentos que se indican a continuación.
Los especificadores de formato son letras precedidas del símbolo de porcentaje. Los modificadores son reemplazados por el argumento correspondiente. Cuando hay más argumentos que especificadores, la cadena de formato es reutilizada hasta que todos los argumentos se han consumido. Los especificadores más utilizados son %s, %d, %f y %x.
El especificador %s imprime los caracteres literales en el argumento.
 
El especificador %b es cómo %s con la diferencia de que si hay secuencias de escape en los argumentos las reemplaza por su equivalente.
 
Los enteros son impresos con %d. El entero puede ser especificado como decimal, octal (comenzando con un 0) o hexadecimal (precediéndole de 0x). Si el número no es válido mostrará un error.
 
Para fracciones o números en coma flotante hay que usar %f. Por defecto, se imprimen con seis decimales.
 
Los enteros se pueden imprimir en hexadecimal usando %x para minúsculas y %X para mayúsculas. 
 
También es posible modificar los formatos indicando un ancho después del símbolo de porcentaje. El argumento será impreso rellenando por la derecha en un campo del ancho indicado o bien por la izquierda si el número es negativo.
 
Si la especificación del ancho es precedida de un 0, entonces el ancho es completado utilizando ceros.
 

1.3.- VARIABLES EN LOS SCRIPTS
Al igual que en todos los lenguajes de programación también podremos utilizar variables dentro de nuestros scripts para almacenar cualquier valor que deseemos. Estas variables pueden ser de dos tipos: variables de entorno y variables definidas por el usuario.
1.3.1.- VARIABLES DE ENTORNO
Las variables de entorno están definidas en el sistema y almacenan información relativa al mismo, tales como el nombre del sistema, el nombre del usuario, su UID, el directorio home del usuario, …
Se puede ver una lista completa de todas las variables de entorno con sus respectivos valores con el comando set.
Se pueden utilizar estas variables dentro de los scripts mediante su nombre precedido del símbolo dólar. 
 
Como se puede ver en el ejemplo utilizamos el comando echo para mostrar el valor de las variables por pantalla. Hay que tener en cuenta que esto implica que el símbolo $ va a tener un significado especial dentro del comando echo, por lo que si queremos utilizarlo debemos escaparlo.
1.3.2.- VARIABLES DE USUARIO
Nosotros también definir nuestras propias variables dentro de los scripts. Para definirlas debemos asignarles un nombre de no más de 20 caracteres alfanuméricos o el símbolo subrayado (_).
Para asignarles un valor lo haremos con el símbolo = seguido del valor que le asignaremos. Recuerda que no debe haber espacios en blanco entre el nombre de la variable y el símbolo =, ni entre este símbolo y el valor.
Las variables almacenarán el valor mientras el script se encuentre en ejecución y serán borradas a la finalización de éste. Para referenciarlas se hace igual que con las variables de entorno, precediendo el nombre del símbolo dólar.
Hay que recordar que el símbolo dólar hay que utilizarlo al referenciar la variable, pero no al asignarle un valor.
 
1.3.3.- EL CARÁCTER BACKTICK (`)
Este carácter es el que está situado en el teclado a la derecha de la letra p, no confundirlo con el acento ni las comillas simples. Nos permitirá asignar la salida de un comando del shell a una variable. Para ello debemos rodear el comando entero con este símbolo.
 
El shell ejecutará el comando y la salida del mismo, en lugar de mostrarla por pantalla, la almacenará en la variable indicada.
Actualmente el uso de este carácter está desaconsejado, siendo recomendable utilizar en su lugar una expansión de la forma $(comando).
 
1.4.- SALIENDO DEL SCRIPT
Linux dispone de una variable de entorno especial que nos permite comprobar como finalizó el último comando ejecutado, esta variable es $? y almacenará un valor numérico que equivaldrá al estado de la salida.
Los diferentes valores que puede devolver este comando se pueden ver en la siguiente tabla:
Códigos de estado de salida en Linux
Código	Descripción
0	El comando ha finalizado con éxito
1	Error desconocido general
2	Uso incorrecto del comando 
126	El comando no puede ser ejecutado
127	Comando no encontrado
128	Argumento de salida inválido
130	Comando terminado con Ctrl+C
255	Estado de salida fuera de rango
Para especificar un valor de salida determinado dentro de tu script puedes utilizar el comando exit. 
 
