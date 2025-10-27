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

El comando `printf` en **Linux** es una herramienta muy útil para **mostrar texto formateado** en la salida estándar (pantalla o fichero). Funciona de forma similar al `printf()` del lenguaje **C**, y es **más potente y predecible** que el clásico `echo`.

##### Sintaxis general

```bash
printf FORMAT [ARGUMENTO...]
```

- **FORMAT** → cadena de formato que indica cómo deben mostrarse los valores.
- **ARGUMENTO** → uno o varios valores a mostrar según el formato.


##### Ejemplos básicos

Mostrar texto simple

```bash
printf "Hola mundo\n"
```

> Muestra: `Hola mundo`
> 🔸 Es obligatorio añadir `\n` al final si quieres un salto de línea.

Mostrar varias líneas

```bash
printf "Línea 1\nLínea 2\nLínea 3\n"
```

##### Formatos de impresión

| Código | Tipo de dato        | Ejemplo                   | Resultado |
| ------ | ------------------- | ------------------------- | --------- |
| `%s`   | Cadena de texto     | `printf "%s\n" "Hola"`    | `Hola`    |
| `%d`   | Entero decimal      | `printf "%d\n" 42`        | `42`      |
| `%f`   | Número flotante     | `printf "%.2f\n" 3.14159` | `3.14`    |
| `%x`   | Entero hexadecimal  | `printf "%x\n" 255`       | `ff`      |
| `%o`   | Entero octal        | `printf "%o\n" 8`         | `10`      |
| `%c`   | Carácter individual | `printf "%c\n" 65`        | `A`       |


##### Ejemplos prácticos de formato

Mostrar columnas alineadas

```bash
printf "%-10s %-10s %-10s\n" "Nombre" "Edad" "País"
printf "%-10s %-10d %-10s\n" "Ana" 23 "España"
printf "%-10s %-10d %-10s\n" "Luis" 30 "Chile"
```

> `%-10s` → reserva 10 caracteres alineado a la izquierda (el `-` alinea).
> Resultado:

```
Nombre     Edad       País      
Ana        23         España    
Luis       30         Chile     
```

Números con ceros a la izquierda

```bash
printf "%04d\n" 7
```

> Resultado: `0007`


Redondear números decimales

```bash
printf "%.3f\n" 3.1415926
```

> Resultado: `3.142`

Crear salidas CSV o tablas

```bash
printf "%s,%s,%s\n" "Nombre" "Edad" "País" > datos.csv
printf "%s,%s,%s\n" "Ana" "23" "España" >> datos.csv
```


##### Caracteres de escape comunes

| Secuencia | Significado     |
| --------- | --------------- |
| `\n`      | Nueva línea     |
| `\t`      | Tabulación      |
| `\\`      | Barra invertida |
| `\"`      | Comillas dobles |
| `\a`      | Campana (beep)  |
| `\b`      | Retroceso       |

Ejemplo:

```bash
printf "Col1\tCol2\tCol3\n"
```


### 1.3.- Variables en los scripts

Al igual que en todos los lenguajes de programación también podremos utilizar variables dentro de nuestros scripts para almacenar cualquier valor que deseemos.

Estas variables pueden ser de dos tipos:
- Variables de entorno
- Variables definidas por el usuario.

#### 1.3.1.- Variables de entorno

Las variables de entorno están definidas en el sistema y almacenan información relativa al mismo, tales como el nombre del sistema, el nombre del usuario, su UID, el directorio home del usuario, …

Se puede ver una lista completa de todas las variables de entorno con sus respectivos valores con el comando `set`.

Se pueden utilizar estas variables dentro de los scripts mediante su nombre precedido del símbolo dólar. 

```bash
#!/bin/bash
# Muestra información del usuario
echo "Información del usuario: $USER"
echo UID: $UID
echo HOME: $HOME
```
 
Como se puede ver en el ejemplo utilizamos el comando `echo` para mostrar el valor de las variables por pantalla. Hay que tener en cuenta que esto implica que el símbolo `$` va a tener un significado especial dentro del comando `echo`, por lo que si quisiéramos utilizarlo debemos escaparlo.


#### 1.3.2.- Variables de usuario

Nosotros también definir nuestras propias variables dentro de los scripts. Para definirlas debemos asignarles un nombre de no más de 20 caracteres alfanuméricos o el símbolo subrayado (`_`).

Para asignarles un valor lo haremos con el símbolo `=` seguido del valor que le asignaremos. Recuerda que **no debe haber espacios en blanco** entre el nombre de la variable y el símbolo `=`, ni entre este símbolo y el valor.

Las variables almacenarán el valor mientras el script se encuentre en ejecución y serán borradas a la finalización de éste. Para referenciarlas se hace igual que con las variables de entorno, precediendo el nombre del símbolo dólar.

Hay que recordar que el símbolo dólar hay que utilizarlo al referenciar la variable, pero no al asignarle un valor.
 
```bash
#!/bin/bash
mensaje="Hola mundo!!"
echo $mensaje
```

#### 1.3.3.- El carácter backtick (`)

Este carácter es el que está situado en el teclado a la derecha de la letra p, no confundirlo con el acento ni las comillas simples. Nos permitirá asignar la salida de un comando del *shell* a una variable. Para ello debemos rodear el comando entero con este símbolo.

```bash
#!/bin/bash
fecha=`date`
echo "La fecha y hora son:" $fecha
```
 
El *shell* ejecutará el comando y la salida del mismo, en lugar de mostrarla por pantalla, la almacenará en la variable indicada.

Actualmente, **el uso de este carácter está desaconsejado**, siendo recomendable utilizar en su lugar una expansión de la forma `$(comando)`.

```bash
#!/bin/bash
today=$(date +%y%m%d)
ls /usr/bin -al > log.$today
```

 
### 1.4.- Saliendo del script

Linux dispone de una variable de entorno especial que nos permite comprobar como finalizó el último comando ejecutado, esta variable es `$?` y almacenará un valor numérico que equivaldrá al estado de la salida.

Los diferentes valores que puede devolver este comando se pueden ver en la siguiente tabla:

**Códigos de estado de salida en Linux**

| Código |	Descripción                        |
|--------|-------------------------------------|
| 0      |	El comando ha finalizado con éxito |
| 1      |	Error desconocido general          |
| 2      |	Uso incorrecto del comando         |
| 126    |	El comando no puede ser ejecutado  |
| 127    |	Comando no encontrado              |
| 128    |	Argumento de salida inválido       |
| 130    |	Comando terminado con Ctrl+C       |
| 255    |	Estado de salida fuera de rango    |

Para especificar un valor de salida determinado dentro de tu script puedes utilizar el comando `exit`. 

```bash
#!/bin/bash
var1=10
var2=30
var3=$[ var1 + var2 ]
echo La suma es $var3
exit 5
```

### 1.5.- Operaciones aritméticas en Bash

