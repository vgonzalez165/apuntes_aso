


### 1\. El Escenario

Eres el administrador de sistemas de una empresa de seguridad. Un servidor legacy (antiguo) está generando unos logs de aplicación con un formato terrible y poco estándar. El departamento de seguridad necesita un informe limpio en CSV para poder auditar quién ha accedido al sistema.

No puedes cambiar cómo el servidor genera los logs. Tu única opción es crear un script en PowerShell que "digiera" ese texto crudo y lo transforme en información estructurada.

### 2\. La Entrada (Raw Data)

Debes copiar y pegar este bloque de texto en su script dentro de una variable (Heredoc) o leerlo desde un archivo `.txt`.

```powershell
$logCrudo = @"
[INFO] ID:8842 :: 2023/11/01_10:00 :: User:admin_01 :: Action:Login_Success
[ERROR] ID:9921 :: 2023/11/01_10:05 :: User:guest_user :: Action:Auth_Fail (Pass)
[WARN] ID:8843 :: 2023-11-01 10:15 :: User:dev_team :: Action:Upload_Limit_Exceeded
[CRITICAL] ID:1001 :: 01-11-2023_10:20 :: User:ROOT :: Action:Service_Stop
"@
```

Observa bien los datos. Hay inconsistencias deliberadas:

- Las fechas tienen formatos distintos (`/`, `-`, `_`).
- Los delimitadores principales son `::`, pero hay espacios variables.
- Los usuarios a veces están en mayúsculas y otras en minúsculas.

### 3\. Los Requisitos (La Misión)

Tu script debe procesar `$logCrudo` y cumplir **estrictamente** con los siguientes puntos. 

1.  **Normalización de Fechas:** todas las fechas deben salir en formato estándar ISO `yyyy-MM-dd HH:mm`.
2.  **Limpieza de Usuarios:**
      * Deben estar todos en **minúsculas**.
      * Si el usuario es "ROOT", debe ser renombrado automáticamente a "administrator" por política de seguridad.
3.  **Clasificación de Gravedad:**
      * Debes extraer el nivel de log (INFO, ERROR, etc.) sin los corchetes `[]`.
4.  **Filtrado:**
      * Solo nos interesan las líneas que **NO** sean `[INFO]`. Ignora los logs informativos.
5.  **Salida Estructurada:**
      * El script no debe escribir texto plano en la consola. Debe generar **Objetos PowerShell** (PSCustomObject) y finalmente exportarlos a un archivo `reporte_seguridad.csv`.

### 4\. El Resultado Esperado (Output)

Al abrir el CSV generado, debería verse algo así (el orden de columnas puede variar):

| TimeStamp | Severity | User | EventID | Action |
| :--- | :--- | :--- | :--- | :--- |
| 2023-11-01 10:05 | ERROR | guest\_user | 9921 | Auth\_Fail (Pass) |
| 2023-11-01 10:15 | WARN | dev\_team | 8843 | Upload\_Limit\_Exceeded |
| 2023-11-01 10:20 | CRITICAL | administrator | 1001 | Service\_Stop |

-----

### 5\. Caja de Herramientas (Pistas Opcionales)

*Si te atascas, investiga sobre estos conceptos (pero tú decides cuál usar):*

  - `Wait-Debugger` (para ver qué pasa línea a línea).
  - El método `.Split("separador")` (Ojo: ¿puedes usar múltiples caracteres como separador?).
  - `[datetime]::ParseExact()` o `Get-Date` para arreglar las fechas locas.
  - `Switch` o `if/elseif` para la lógica de renombramiento de usuarios.
  - `[PSCustomObject]@{}` para crear la tabla final.

-----

### 6\. Criterios de Evaluación

Esta práctica no se evalúa por "hacerlo como dijo el profesor", sino por la robustez de la solución:

1.  **Resiliencia:** ¿El script falla si hay un espacio de más en el log original? (Uso de `.Trim()`).
2.  **Lógica de Objetos:** ¿El alumno ha entendido que PowerShell trabaja con objetos y no con "texto plano" al final? (Uso de `Export-Csv`).
3.  **Limpieza de Código:** ¿Variables bien nombradas? ¿Indentación correcta?
4.  **Creatividad:** ¿Cómo resolvió el problema de las fechas con formatos distintos? (¿Usó un regex complejo o múltiples `replace`?).
