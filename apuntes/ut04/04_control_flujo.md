# UT06.- LENGUAJES DE SCRIPTING EN WINDOWS: POWERSHELL

## 4.- Control de flujo

**Índice**

<!-- no toc -->
1. [Declaración de variables](#31--declaración-de-variables-variable)



### 4.1. Operadores de comparación 

A diferencia de C, Java o Python, PowerShell **NO** usa símbolos (`>`, `<`, `==`) para comparar, sino que utiliza **flags**. 

| Operador | Significado | Equivalente C/Java | Ejemplo |
| :--- | :--- | :--- | :--- |
| `-eq` | Equal (Igual) | `==` | `$a -eq 5` |
| `-ne` | Not Equal (Distinto) | `!=` | `$a -ne 5` |
| `-gt` | Greater Than (Mayor que) | `>` | `$a -gt 10` |
| `-lt` | Less Than (Menor que) | `<` | `$a -lt 10` |
| `-ge` | Greater/Equal (Mayor o igual) | `>=` | `$a -ge 10` |
| `-le` | Less/Equal (Menor o igual) | `<=` | `$a -le 10` |
| `-like` | Coincidencia (usando `*`) | N/A | `$nombre -like "Win*"` |
| `-not` | Negación (lógico) | `!` | `if (-not $activado)` |



### 4.2.- Condicionales: tomando decisiones



#### 4.2.1.- La sentencia `If / Else`

Permite ejecutar código solo si se cumple una condición.

**Sintaxis:**

```powershell
if (condición) {
    # Código si es VERDADERO
} elseif (otra_condición) {
    # Código si la primera falló y esta es VERDADERA
} else {
    # Código si NADA de lo anterior se cumplió
}
```

**Ejemplo** Comprobar si el servicio de Spooler de impresión está corriendo.

```powershell
$servicio = Get-Service -Name Spooler

if ($servicio.Status -eq "Running") {
    Write-Host "El servicio de impresión funciona correctamente." -ForegroundColor Green
} else {
    Write-Host "El servicio está detenido. Iniciando..." -ForegroundColor Red
    Start-Service -Name Spooler
}
```

#### 4.2.2.- La sentencia `Switch`

Ideal cuando tienes que evaluar una variable contra múltiples valores posibles. Es más limpio que muchos `elseif`.

**Ejemplo:** menú de opciones para un script de mantenimiento.

```powershell
$opcion = Read-Host "Elige opción: (1) Copia seguridad, (2) Limpiar Logs, (3) Salir"

switch ($opcion) {
    "1" { Write-Host "Iniciando backup..."; break }
    "2" { Write-Host "Borrando logs..."; break }
    "3" { Write-Host "Saliendo."; exit }
    Default { Write-Host "Opción no válida" }
}
```


### 4.3.- Bucles

Los bucles son fundamentales para aplicar una acción a múltiples objetos (usuarios, archivos, servicios).

#### 4.3.1.- El bucle `ForEach`

Es el más usado en scripting. Recorre una colección de objetos uno por uno.

**Sintaxis:**

```powershell
foreach ($item in $coleccion) {
    # Hacer algo con $item
}
```

**Ejemplo:** rReiniciar una lista específica de servicios.

```powershell
$serviciosCriticos = "Dnscache", "Dhcp", "Spooler"

foreach ($srv in $serviciosCriticos) {
    Write-Host "Verificando servicio: $srv"
    Restart-Service -Name $srv -Force
}
```

#### 4.3.2.- `ForEach-Object` (El método del pipeline `|`)

PowerShell permite pasar objetos por una tubería. Es menos legible para scripts largos, pero muy rápido para comandos de una línea ("One-liners").

Aquí usamos la variable automática **`$_`** (o `$PSItem`) para referirnos al objeto actual.

```powershell
# Obtiene todos los servicios detenidos e intenta iniciarlos uno a uno
Get-Service | Where-Object Status -eq 'Stopped' | ForEach-Object {
    Write-Host "Iniciando $($_.Name)..."
    $_.Start()
}
```

#### 4.3.3.- El bucle `For`

El clásico bucle con contador. Se usa menos en administración de sistemas, salvo que necesites un número exacto de repeticiones.

```powershell
# Hacer ping 4 veces
for ($i = 1; $i -le 4; $i++) {
    Write-Host "Intento de conexión número $i"
    Test-Connection -ComputerName "8.8.8.8" -Count 1 -Quiet
}
```

#### 4.3.4.- El bucle `While` y `Do-While`

Ejecutan código mientras una condición sea verdadera.

  - **While:** evalúa antes de entrar (puede no ejecutarse nunca).
  - **Do-While:** ejecuta al menos una vez y evalúa al final.

**Ejemplo:** esperar a que un servidor se reinicie y responda al ping.

```powershell
Write-Host "Esperando a que el servidor responda..."

while (-not (Test-Connection -ComputerName "Servidor01" -Count 1 -Quiet)) {
    Write-Host "." -NoNewline
    Start-Sleep -Seconds 2
}

Write-Host "`n¡El servidor está online!"
```


### 4.4.- Control de flujo dentro de bucles

A veces necesitas salir de un bucle antes de tiempo o saltarte una vuelta.

  - **`Break`**: sale del bucle inmediatamente y sigue con el resto del script. Por ejemplo, si buscas un archivo y lo encuentras, no necesitas seguir buscando.
  - **`Continue`**: salta el resto del código de la vuelta actual y pasa a la siguiente iteración. Por ejemplo, si estás procesando usuarios y encuentras al "Administrador", saltas esa vuelta para no borrarlo por error.

**Ejemplo**

```powershell
$usuarios = Get-ChildItem "C:\Users"

foreach ($user in $usuarios) {
    # Si la carpeta es Administrator o Public, la ignoramos
    if ($user.Name -eq "Administrator" -or $user.Name -eq "Public") {
        continue 
    }
    
    Write-Host "Auditando usuario: $($user.Name)"
    # Aquí iría el código de auditoría
}
```

