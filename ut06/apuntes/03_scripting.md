# UT06.- LENGUAJES DE SCRIPTING EN WINDOWS: POWERSHELL

## 3.- Scripting y lógica de programación

**Índice**

<!-- no toc -->
1. Declaración de variables
2. Tipos de datos básicos
3. Comillas simples vs comillas dobles
4. Estructuras de datos complejas: arrays y colecciones


En este punto, dejamos de ser "operadores de consola" para convertirnos en "desarrolladores de scripts". Un script (`.ps1`) es un archivo de texto con instrucciones secuenciales que nos permite automatizar tareas complejas, repetitivas y propensas a error humano.

### 3.1.- Declaración de variables (`$variable`)

Una **variable** es un contenedor en la memoria del ordenador donde almacenamos datos temporalmente para manipularlos durante la ejecución del script.

En PowerShell, las variables se declaran y utilizan precediéndolas del símbolo dólar `$` y, al igual que en otros lenguajes de programación, se declaran asignándoles un valor con el operador `=`. Algo importante a tener en cuenta es que Windows es *case-insensitive*, es decir, que el sistema trata por igual una letra en mayúscula que en minúscula.

**Ejemplo de declaración y uso:**

```powershell
# Declaramos la variable
$nombreServidor = "SRV-DC01"

# La usamos en un comando
Write-Host "Conectando al servidor $nombreServidor..."

# Podemos cambiar su valor en cualquier momento
$nombreServidor = "SRV-DC02"
Write-Host "Cambiando destino a $nombreServidor..."
```

Una característica fundamental que hay que tener en cuenta en Powershell es que puede tener tipado tanto dinámico como estático. 

Por defecto, cuando asignamos valor a una variable estamos utilizando **tipado dinámico**. Esto quiere decir que Powershell analizará el valor que le asignamos a la variable e inferirá el tipo de datos que tiene que tener. Si posteriormente hacemos una asignación de la misma variable con otro valor de otro tipo de datos diferente cambiará el tipo de la variable.

```powershell
PS C:\> $var = 10
PS C:\> $var.GetType().Name
Int32
PS C:\> $var = "hola"
PS C:\> $var.GetType().Name
String
```

En cambio, cuando usamos **tipado estático**, indicamos explícitamente qué tipo de datos queremos que tenga la variable de forma que, si intentamos asignarle un valor de otro tipo de datos nos dará error.

```powershell
PS C:\> [int]$my_var = 32
PS C:\> $my_var.GetType().Name
Int32
PS C:\> $my_var = "Hola"
No se puede convertir el valor "Hola" al tipo "System.Int32". Error: "La cadena de entrada no tiene el formato
correcto."
```

### 3.2.- Tipos de datos básicos

Dado que PowerShell está construido sobre **.NET Framework**, tiene una inmensa variedad de datos, de entre los que se pueden destacar:

- **Tipos de texto**
  - **String (`[string]`):** es el más común. Almacena cadenas de caracteres (texto). 
  
    ```powershell
    [string]$mensaje = "Bienvenido a PowerShell"

    # Podemos manipular el texto fácilmente
    Write-Host $mensaje.ToUpper()   # Convierte a mayúsculas
    Write-Host $mensaje.Length      # Cuenta los caracteres (31)
    ```

  - **Char (`[char]`):** almacena un **único** carácter. Es útil cuando recorres texto letra por letra.

    ```powershell
    [char]$letra = 'A'
    [char]$simbolo = '$'

    # Convertir un número a su carácter ASCII
    [char]64  # Salida: @
    ```

- **Tipos numéricos**: PowerShell distingue entre números enteros y decimales, y dentro de ellos, por su capacidad de almacenamiento.

  - **Integer (`[int]` o `Int32`):** números enteros (sin decimales) estándar. Rango aproximado de -2 mil millones a +2 mil millones. 
    ```powershell
    [int]$puertoSSH = 22
    [int]$intentos = 3

    # Operaciones básicas
    $resultado = $intentos * 2
    Write-Host "El puerto es $puertoSSH y el resultado es $resultado"
    ```

  - **Long (`[long]` o `Int64`):** enteros muy grandes. 

    ```powershell
    # Un disco de 1 TB en bytes
    [long]$bytesDisco = 1099511627776 

    # Si intentaras poner este número en un [int], daría error de desbordamiento
    Write-Host "Bytes totales: $bytesDisco"
      ```

  - **Double (`[double]`):** es el tipo por defecto para números con decimales (coma flotante). Cálculos matemáticos generales, porcentajes.

    ```powershell
    [double]$pi = 3.14159
    [double]$temperatura = 36.5

    # Precisión estándar
    $calculo = $pi * 2
    Write-Host "El diámetro es: $calculo"
    ```

  - **Decimal (`[decimal]`):** números con decimales de **alta precisión**. Se usa para dinero y finanzas ya que el tipo `Double` puede tener errores de redondeo pequeños; `Decimal` es exacto. Se fuerza añadiendo una `d` al final del número o usando el cast `[decimal]`.

    ```powershell
    # Sin la 'd', PowerShell lo trataría como double
    $precio = 19.99d 
    $impuesto = 4.50d

    $total = $precio + $impuesto
    Write-Host "El total a pagar es: $total €"
    ```

- **Tipos lógicos**
  - **Boolean (`[bool]`):** Solo tiene dos valores posibles: `$true` (Verdadero) o `$false` (Falso). 

    ```powershell
    [bool]$servicioActivo = $true
    [bool]$esAdmin = $false

    # Resultado de una comparación
    $esMayor = (10 -gt 5)  # Esto guarda $true automáticamente

    if ($esMayor) {
        Write-Host "La condición se cumple."
    }
    ```

- **Tipos temporales**

  - **DateTime (`[datetime]`):** almacena una fecha y hora específicas. Por ejemplo,  `Get-Date` devuelve un objeto de este tipo. Permite extraer `.Year`, `.Hour`, o hacer cálculos como `.AddDays(5)`.

    ```powershell
    $hoy = Get-Date

    # Extraer partes
    Write-Host "Estamos en el año: $($hoy.Year)"
    Write-Host "Día de la semana: $($hoy.DayOfWeek)"

    # Manipular fechas (Futuro/Pasado)
    $vencimiento = $hoy.AddDays(30)
    Write-Host "La licencia vence el: $vencimiento"
    ```

  - **TimeSpan (`[timespan]`):** representa un **intervalo** de tiempo (duración), no una fecha específica. *Ejemplo:* La diferencia entre dos fechas (`$fechaFin - $fechaInicio`) devuelve un `TimeSpan` (ej. "3 días y 4 horas").

    ```powershell
    $inicio = Get-Date
    Start-Sleep -Seconds 2 # Esperamos 2 segundos
    $fin = Get-Date

    # Calculamos la diferencia
    [TimeSpan]$duracion = $fin - $inicio

    Write-Host "El script tardó: $($duracion.TotalSeconds) segundos"
    ```

- **Tipos de colección** (agrupaciones)

  - **Array (`[array]` o `Object[]`):** una lista de tamaño fijo.

    ```powershell
    $colores = @("Rojo", "Verde", "Azul")

    Write-Host "El primer color es: $($colores[0])"
    ```

  - **Hashtable (`[hashtable]` o `@{}`):** pares de Clave-Valor desordenados.

    ```powershell
    $config = @{
        Usuario = "Admin"
        Nivel   = 5
        Activo  = $true
    }

    Write-Host "El nivel del usuario es: $($config['Nivel'])"
    ```

  - **PSCustomObject (`[PSCustomObject]`):** objetos personalizados con propiedades nombradas (como filas de una base de datos).

    ```powershell
    $servidor = [PSCustomObject]@{
        Nombre = "SRV-01"
        IP     = "192.168.1.10"
        Estado = "Online"
    }

    # Acceso limpio por propiedades
    Write-Host "Conectando a $($servidor.Nombre) en la IP $($servidor.IP)"
    ```

- **Tipos especiales**

  - **Void (`[void]`):** representa "nada" o "vacío". Se usa mucho para **suprimir la salida** de un comando en la consola. *Ejemplo:* `[void]$lista.Add("Item")` evita que aparezca el índice en pantalla.

    ```powershell
    $lista = New-Object System.Collections.ArrayList

    # Sin [void], esto imprimiría "0" (el índice) en la pantalla
    [void]$lista.Add("Dato 1") 

    # Otra forma común de hacer void es redirigir a $null
    $lista.Add("Dato 2") | Out-Null
    ```

  - **Null (`$null`):** representa la ausencia de valor. Es vital para comprobar si una variable está vacía antes de usarla.

    ```powershell
    $variableVacia = $null

    if ($variableVacia -eq $null) {
        Write-Host "Cuidado, la variable está vacía."
    }

    # Error común: Intentar hacer algo con $null da error
    # $variableVacia.ToUpper() <--- Error: No puedes llamar a un método en una expresión Null
    ```


### 3.3.- Comillas simples vs. comillas dobles

Entender esto es vital para evitar errores al escribir rutas de archivos o mensajes.

  - **Comillas Simples (`' '`):** tratan todo como **texto literal**. Lo que ves es lo que obtienes. PowerShell no procesa nada dentro.
  - **Comillas Dobles (`" "`):** permiten la **expansión de variables**. PowerShell escanea el texto, busca el símbolo `$` e inyecta el valor de la variable.

**La trampa de las propiedades (Subexpresión `$()`):**
Cuando usas comillas dobles, PowerShell expande variables simples (`$var`), pero falla con objetos complejos (`$var.propiedad`). Para solucionar esto, usamos la subexpresión `$()`.

```powershell
$proceso = "notepad"
$fecha = Get-Date

# 1. Comillas Simples: Todo es texto plano
Write-Host 'El proceso es $proceso'
# Salida: El proceso es $proceso  (No interpreta la variable)

# 2. Comillas Dobles: Expansión simple
Write-Host "El proceso es $proceso"
# Salida: El proceso es notepad

# 3. El problema de las propiedades dentro de comillas
# Queremos ver el AÑO de la fecha ($fecha.Year)
Write-Host "Estamos en el año $fecha.Year"
# Salida INCORRECTA: Estamos en el año 10/27/2023 10:00:00.Year
# (PowerShell expandió $fecha, pero trató ".Year" como texto normal)

# 4. SOLUCIÓN: Subexpresión $()
Write-Host "Estamos en el año $($fecha.Year)"
# Salida CORRECTA: Estamos en el año 2023
```


### 3.4.- Estructuras de datos complejas: arrays y colecciones

Las variables simples guardan un dato. Las estructuras complejas guardan colecciones de datos.

En PowerShell, casi todo lo que devuelve un comando puede ser tratado como una **colección**. Entender cómo guardar y manipular listas de datos es fundamental para el scripting.

#### 3.4.1.- **Arrays Fijos (Standard Arrays)**

Es la estructura por defecto en PowerShell. Se comportan como una "caja de tamaño fijo".

Una característica de los arrays fijos es la **inmutabilidad de tamaño:** una vez creado, el array ocupa un espacio exacto en memoria.

Un problema derivado de la inmutabilidad de tamaño es cuando queremos añadir elementos al array, podemos hacerlo de la forma `$array += "nuevo dato"`, lo cual funciona, pero es un error, ya que internamente Powershell hace esto, lo cual es desastroso para el rendimiento:

1.  Crea un **nuevo** array en memoria con tamaño `N + 1`.
2.  Copia todos los datos viejos al nuevo.
3.  Escribe el dato nuevo.
4.  Borra el array viejo.
        
```powershell
# 1. Creación explícita
$servidores = @("SRV01", "SRV02", "SRV03")

# 2. Creación implícita
$numeros = 1, 2, 3, 4, 5

# 3. Acceso a datos (Indexación)
Write-Host "El primero: $($servidores[0])"
Write-Host "El último:  $($servidores[-1])" # -1 es el último elemento

# 4. Slicing (Extraer un rango)
$primerosDos = $servidores[0..1] # Devuelve SRV01 y SRV02

# 5. Obtener el tamaño
$total = $servidores.Count
```


#### 3.4.2.- **ArrayLists**

El `System.Collections.ArrayList` fue la solución para listas que crecen. A diferencia del Array fijo, este reserva memoria "extra" anticipadamente, por lo que añadir datos es rápido.

Los ArraysLists tienen **tipado mixto**, es decir, acepta cualquier tipo de datos (números mezclados con texto), lo cual es flexible pero puede causar errores si no tienes cuidado.

Para añadir elementos debemos utilizar el método **`Add()`**, pero hay que tener en cuenta que, además de añadir el elemento al array, devuelve el índice donde se guardó el dato (ej: devuelve `0`, `1`, etc.). Esto "ensucia" la consola, por lo que siempre debemos silenciarlo.

Otros métodos de los ArrayLists son:

- `Insert(pos, elem)`: inserta un elemento en la posición indicada
- `Remove(elem)`: elimina el elemento pasado como parámetro
- `RemoveAt(pos)`: elimina el elemento que se encuentra en la posición
- `Contains(elem)`: indica si el ArrayList contiene un elemento o no

```powershell
# Crear la colección
$listaUsuarios = New-Object System.Collections.ArrayList

# Añadir datos silenciando la salida (dos métodos):
[void]$listaUsuarios.Add("Ana")      # Método 1: Cast a void
$null = $listaUsuarios.Add("Pedro")  # Método 2: Redirigir a $null (Ligeramente más rápido)

# Insertar un dato en una posición específica (ej: al principio)
$listaUsuarios.Insert(0, "Jefe")    # Ahora "Jefe" está en índice 0

# Eliminar datos
$listaUsuarios.Remove("Pedro")      # Borra por valor
$listaUsuarios.RemoveAt(0)          # Borra por índice (Borra al "Jefe")

# Verificar si contiene algo
if ($listaUsuarios.Contains("Ana")) {
    Write-Host "Ana está en la lista"
}
```


#### 3.4.3.- **Listas Genéricas `List[T]`**

La alternativa a los problemas que conllevan los Arrays fijos y los ArraysList es el uso de **listas genéricas** (`System.Collections.Generic.List[T]`). Sus principales ventajas son:

- **Seguridad de tipos**: al crear una lista genérica hay que indicar el tipo de datos que va a contener, obligando a que todos sus elementos sean de ese tipo.
- **Sintaxis más limpia**: al no devolver el índice (como hacen los ArrayList), no es necesario silenciar la salida precediendo el comando de `[void`], lo que hace el código más limpio y legible.
- **Mayor rendimiento**: son mucho más eficientes en ejecución que las otras alternativas.

Sus métodos más relevantes son:

- `.Add(elem)`: añade un elemento al final de la lista
- `.AddRange(colección)`: añade múltiples elementos de golpe desde un array o una lista
- `.Insert(pos, elem)`: añade un elemento en la posición indicada
- `.Remove(elem)`: elimina el elemento pasado como parámetro
- `.Clear()`: borra todo el contenido, dejando la lista vacía
- `.Contains(elem)`: devuelve `$true` o `$false` indicando si el elemento está en la lista o no
- `IndexOf(elem)`: indica en qué posición está un elemento. Si no se encontrara en la lista devuelve `-1`
- `.Sort()`: ordena la lista, modificando la lista original
- `.Count`: devuelve el número de elementos de la lista. Observa que es una propiedad, no un método, por lo que va sin paréntesis.


```powershell
# Crear una lista que SOLO acepta Strings (Texto)
$listaIPs = New-Object System.Collections.Generic.List[string]

# Añadir (No devuelve salida, no hace falta [void])
$listaIPs.Add("192.168.1.10")
$listaIPs.Add("10.0.0.1")

# Intentar añadir un número dará error (Seguridad)
# $listaIPs.Add(500) <--- Esto fallaría, protegiendo tu script

# Ordenar la lista fácilmente
$listaIPs.Sort()
```



#### 3.4.4.- Iteración

Tener los datos guardados no sirve de nada si no hacemos algo con ellos. Usamos **bucles** para procesar cada elemento.

Hay dos formas de iterar sobre los elementos de un array: usando `foreach` y mediante una canalización al comando `ForEach-Object`

```powershell
$ciudades = @("Madrid", "Barcelona", "Valencia", "Sevilla")

# Opción A: foreach (La más común)
foreach ($ciudad in $ciudades) {
    Write-Host "Procesando backup en: $ciudad"
}

# Opción B: Tubería (Pipeline) - Estilo PowerShell
$ciudades | ForEach-Object {
    # $_ representa el elemento actual
    Write-Host "La ciudad $_ tiene $($_.Length) letras"
}
```
