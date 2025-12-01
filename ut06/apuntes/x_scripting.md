

## 6.- Scripting y lógica de programación

En este punto, dejamos de ser "operadores de consola" para convertirnos en "desarrolladores de scripts". Un script (`.ps1`) es un archivo de texto con instrucciones secuenciales que nos permite automatizar tareas complejas, repetitivas y propensas a error humano.

## 6.1.- Variables y tipos de datos

Una variable es un contenedor en la memoria del ordenador donde almacenamos datos temporalmente para manipularlos durante la ejecución del script.

### 6.1.1.- Declaración de variables (`$variable`)

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

### 6.1.2.- Tipos de datos

PowerShell es un lenguaje de **tipado dinámico**, lo que significa que intenta adivinar qué tipo de dato le estás pasando. Sin embargo, para scripts robustos, podemos usar el **tipado estático**, que consiste en forzar el tipo de datos que tendrá la variable.

Dado que PowerShell está construido sobre **.NET Framework**, tiene una inmensa variedad de datos, de entre los que se pueden destacar:

- **Tipos de texto**
  - **String (`[string]`):** es el más común. Almacena cadenas de caracteres (texto). *Ejemplo:* `$nombre = "Juan"`
  - **Char (`[char]`):** almacena un **único** carácter. Es útil cuando recorres texto letra por letra. *Ejemplo:* `$letra = 'A'`.
  
- **Tipos numéricos**: PowerShell distingue entre números enteros y decimales, y dentro de ellos, por su capacidad de almacenamiento.
  - **Integer (`[int]` o `Int32`):** números enteros (sin decimales) estándar. Rango aproximado de -2 mil millones a +2 mil millones. *Uso:* Contadores, edades, números de puertos.
  - **Long (`[long]` o `Int64`):** enteros muy grandes. *Uso:* Tamaños de archivos en bytes, cálculos de disco duro.
  - **Double (`[double]`):** es el tipo por defecto para números con decimales (coma flotante). *Uso:* Cálculos matemáticos generales, porcentajes.
  - **Decimal (`[decimal]`):** números con decimales de **alta precisión**. *Uso:* **Dinero y finanzas**. El tipo `Double` puede tener errores de redondeo pequeños; `Decimal` es exacto. Se fuerza añadiendo una `d` al final del número o usando el cast `[decimal]`.

- **Tipos lógicos**
  - **Boolean (`[bool]`):** Solo tiene dos valores posibles: `$true` (Verdadero) o `$false` (Falso). *Uso:* Interruptores, validaciones, resultados de comparaciones.

- Tipos temporales
  - **DateTime (`[datetime]`):** almacena una fecha y hora específicas. *Ejemplo:* `Get-Date` devuelve un objeto de este tipo. Permite extraer `.Year`, `.Hour`, o hacer cálculos como `.AddDays(5)`.
  - **TimeSpan (`[timespan]`):** representa un **intervalo** de tiempo (duración), no una fecha específica. *Ejemplo:* La diferencia entre dos fechas (`$fechaFin - $fechaInicio`) devuelve un `TimeSpan` (ej. "3 días y 4 horas").

- Tipos de colección (agrupaciones)

  - **Array (`[array]` o `Object[]`):** una lista de tamaño fijo.
  - **Hashtable (`[hashtable]` o `@{}`):** pares de Clave-Valor desordenados.
  - **PSCustomObject (`[PSCustomObject]`):** objetos personalizados con propiedades nombradas (como filas de una base de datos).

- Tipos especiales

  - **Void (`[void]`):** representa "nada" o "vacío". Se usa mucho para **suprimir la salida** de un comando en la consola. *Ejemplo:* `[void]$lista.Add("Item")` evita que aparezca el índice en pantalla.
  - **Null (`$null`):** representa la ausencia de valor. Es vital para comprobar si una variable está vacía antes de usarla.


En PowerShell, a veces las variables cambian de tipo dinámicamente. Para saber qué es una variable en cualquier momento, usa el método `.GetType()`.


```powershell
# 1. Definimos variables variadas
$texto = "PowerShell"
$numero = 100
$decimalPreciso = 100.50d  # La 'd' fuerza a decimal
$esVerdad = $true
$fecha = Get-Date

# 2. Comprobamos sus tipos
Write-Host "Tipo de 'texto':          $($texto.GetType().Name)"
Write-Host "Tipo de 'numero':         $($numero.GetType().Name)"
Write-Host "Tipo de 'decimalPreciso': $($decimalPreciso.GetType().Name)"
Write-Host "Tipo de 'esVerdad':       $($esVerdad.GetType().Name)"
Write-Host "Tipo de 'fecha':          $($fecha.GetType().Name)"

# Salida:
# Tipo de 'texto':          String
# Tipo de 'numero':         Int32
# Tipo de 'decimalPreciso': Decimal
# Tipo de 'esVerdad':       Boolean
# Tipo de 'fecha':          DateTime
```


```powershell
# --- Tipado dinámico (PowerShell decide) ---
$edad = "30"          # PowerShell cree que esto es texto (String)
$numero = 30          # PowerShell sabe que esto es número (Int32)

# --- Tipado estático ---
# Esto es útil para evitar errores de lógica más adelante

[string]$nombreUsuario = "Admin01"
[int]$puertoRDP = 3389
[bool]$esCritico = $true

# ¿Cómo verificamos el tipo? Usando el método .GetType()
Write-Host "El tipo de dato de puertoRDP es: $($puertoRDP.GetType().Name)"

# Ejemplo de error de tipo forzado:
# Si intentas ejecutar la siguiente línea, dará error porque "Hola" no es un número.
# [int]$cantidad = "Hola"  <-- Error: Input string was not in a correct format.
```

#### 8.3. Comillas Simples vs. Comillas Dobles

Entender esto es vital para evitar errores al escribir rutas de archivos o mensajes.

  * **Comillas Simples (`' '`):** Tratan todo como **texto literal**. Lo que ves es lo que obtienes. PowerShell no procesa nada dentro.
  * **Comillas Dobles (`" "`):** Permiten la **expansión de variables**. PowerShell escanea el texto, busca el símbolo `$` e inyecta el valor de la variable.

**La trampa de las propiedades (Subexpresión `$()`):**
Cuando usas comillas dobles, PowerShell expande variables simples (`$var`), pero falla con objetos complejos (`$var.propiedad`). Para solucionar esto, usamos la subexpresión `$()`.

**Ejemplo avanzado de comillas:**

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

-----

### 9\. Estructuras de Datos Complejas

Las variables simples guardan un dato. Las estructuras complejas guardan colecciones de datos.

#### 9.1. Arrays (Arreglos) y ArrayLists

Un **Array** es una lista indexada de elementos. Piensa en una estantería numerada empezando por el 0.

  * **Arrays Fijos (`@()`):** Son rápidos de crear, pero lentos si necesitas añadir o quitar elementos constantemente, ya que PowerShell tiene que destruir el array y crear uno nuevo cada vez que lo modificas.

**Ejemplo de Array Fijo:**

```powershell
# Creación explícita
$dias = @("Lunes", "Martes", "Miércoles")

# Acceso por índice (El primer elemento es 0)
Write-Host "El primer día es: $($dias[0])"

# Modificar un elemento existente
$dias[1] = "Jueves" # Cambia Martes por Jueves

# Ver cuantos elementos hay
Write-Host "Total de días: $($dias.Count)"
```

  * **ArrayLists (Listas Dinámicas):** Si vas a estar añadiendo elementos en un bucle (por ejemplo, escaneando 1000 ordenadores), usa `System.Collections.ArrayList`. Es mucho más eficiente en memoria.

**Ejemplo de ArrayList:**

```powershell
# Crear la lista vacía
$logDeErrores = New-Object System.Collections.ArrayList

# Añadir elementos (El método .Add() devuelve el índice, usamos [void] para ocultarlo)
[void]$logDeErrores.Add("Error 404")
[void]$logDeErrores.Add("Error 500")

# Eliminar elementos fácilmente
$logDeErrores.Remove("Error 404")
```

#### 9.2. Hash Tables (Diccionarios o Mapas)

Mientras que un Array usa números (índices) para encontrar datos, una **Hash Table** usa nombres (**Claves**). Es ideal para almacenar configuraciones, credenciales o parejas de datos relacionados.

  * Sintaxis: `@{ Clave = Valor; Clave2 = Valor2 }`

**Ejemplo práctico:**

```powershell
# Definimos una configuración para un usuario
$usuarioConfig = @{
    Nombre   = "Ana"
    Rol      = "Administrador"
    Activo   = $true
    ID       = 1024
}

# Búsqueda rápida: "Dame el Rol del usuario"
Write-Host "El rol es: $($usuarioConfig.Rol)"

# También podemos acceder como si fuera un índice de texto
Write-Host "El ID es: $($usuarioConfig['ID'])"

# Son muy útiles para pasar parámetros a comandos (Splatting)
# Set-ADUser -Identity $usuarioConfig.Nombre -Description $usuarioConfig.Rol
```

#### 9.3. Objetos Personalizados (`PSCustomObject`)

Esta es la estructura **más profesional**. Convierte una Hash Table desordenada en un **Objeto de PowerShell** real.

¿Por qué es importante? Porque los Arrays y Hash Tables a veces se ven mal al exportarlos. Un `PSCustomObject` ordena los datos en filas y columnas (Propiedades y Valores), permitiendo que funcionen con tuberías (`|`), ordenación (`Sort-Object`) y exportación (`Export-Csv`).

**Ejemplo: Creando un reporte de sistema**

Imagina que quieres un reporte que combine datos de red y de sistema.

```powershell
# Recopilamos datos sueltos
$pc = $env:COMPUTERNAME
$ip = (Test-Connection $pc -Count 1).IPV4Address.IPAddressToString
$os = (Get-CimInstance Win32_OperatingSystem).Caption

# Creamos el Objeto Personalizado
# [ordered] garantiza que las columnas salgan en el orden que escribimos
$objetoReporte = [PSCustomObject][ordered]@{
    NombrePC          = $pc
    DireccionIP       = $ip
    SistemaOperativo  = $os
    FechaRevision     = Get-Date
}

# Ahora podemos tratarlo como cualquier comando nativo de PS
# Ejemplo 1: Mostrar en tabla bonita
$objetoReporte | Format-Table -AutoSize

# Ejemplo 2: Exportar directamente a CSV (Esto no se puede hacer bien con HashTables simples)
$objetoReporte | Export-Csv -Path "C:\Temp\Reporte.csv" -NoTypeInformation
```

-----

### ¿Te gustaría que prepare un pequeño examen tipo test de 5 preguntas sobre estos conceptos para verificar si han quedado claros?