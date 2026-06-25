```
---------------- ADMINISTRACIÓN DE SISTEMAS INFORMÁTICOS Y REDES ----------------
---------------------------------------------------------------------------------

Módulo:                     ADMINISTRACIÓN DE SISTEMAS OPERATIVOS
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT06
Práctica:                   PROYECTO
Resultados de aprendizaje:  RA1, RA2, RA3, RA4, RA6, RA7
```
# Proyecto Final: "Deploy & Connect" – Automatización de Infraestructuras Heterogéneas

## 1. Escenario y Contexto

La empresa tecnológica **"StartUp Tech S.L."** está en plena expansión. Actualmente, el administrador de sistemas (tú) pierde demasiado tiempo creando usuarios manualmente y configurando carpetas cada vez que entra personal nuevo o se abre un departamento. Además, los empleados se quejan de que "es difícil encontrar las carpetas en la red".

La dirección técnica te ha encargado el proyecto **"Deploy & Connect"** con dos objetivos claros:

1. **En el servidor (Linux):** crear un sistema de aprovisionamiento masivo que lea ficheros CSV y configure usuarios, grupos y recursos Samba automáticamente.
2. **En el cliente (Windows):** crear un script inteligente que conecte automáticamente las unidades de red al iniciar sesión, sin intervención del usuario.


## 2. Requisitos Previos (Entradas)

Para que el sistema sea escalable, no puedes escribir nombres a mano en los scripts. Debes diseñar dos ficheros `.csv` que actuarán como base de datos:

### A. Fichero `usuarios.csv`

Debe contener la lista de empleados a dar de alta. El formato será `usuario,grupo,password`

```csv
jgarcia,ventas,Ventas2024!
amartin,contabilidad,Conta2024!
llopez,desarrollo,Dev2024!
sadmin,sistemas,AdminRoot1!

```

### B. Fichero `recursos.csv`

Debe definir las carpetas compartidas y sus propiedades. El formato será: `nombre_recurso,ruta_absoluta,grupo_propietario,permisos_octales,solo_lectura`

```csv
doc_ventas,/srv/samba/ventas,ventas,770,no
doc_conta,/srv/samba/contabilidad,contabilidad,770,no
publico,/srv/samba/publico,users,775,no
isos_read,/srv/samba/isos,sistemas,755,yes

```

## 3. Tareas a Desarrollar

### Tarea 1: el script de aprovisionamiento (Linux Bash)

Debes crear un script llamado `deploy_infra.sh` que realice lo siguiente:

1. **Validación:** comprobar que se ejecuta como `root` y que los ficheros CSV existen.
2. **Sistema de Logs:** toda acción realizada (creación de usuario, error, éxito) debe quedar registrada con fecha y hora en `/var/log/deploy_samba.log`.
3. **Procesar usuarios (lectura de CSV):**
    - Leer línea a línea `usuarios.csv`.
    - Crear el grupo del sistema si no existe.
    - Crear el usuario del sistema si no existe (con su *home* y *shell*).
    - Añadir el usuario a la base de datos de Samba (`smbpasswd`) y habilitarlo.
4. **Procesar recursos (lectura de CSV):**
    - Crear la carpeta física si no existe.
    - Asignar propietario (`root:grupo`) y permisos (`chmod` + `SGID bit`).
    - **Inyección de configuración:** el script debe escribir automáticamente los bloques de configuración al final del fichero `/etc/samba/smb.conf` si no existen previamente.
5. **Reinicio:** reiniciar los servicios para aplicar cambios.

**Nota:** El script debe ser **idempotente**. Si lo ejecuto dos veces seguidas, la segunda vez no debe dar errores (debe detectar que los usuarios/carpetas ya existen y saltar ese paso informando en el log).


### Tarea 2: El script de conexión (Windows PowerShell)

Debes crear un script llamado `Connect-Drives.ps1` para los clientes:

1. **Limpieza:** desconectar cualquier unidad de red existente para evitar conflictos.
2. **Conectividad:** hacer un `Ping` al servidor. Si no responde, mostrar error y salir.
3. **Autenticación:** solicitar credenciales al usuario (en un entorno real sería SSO, aquí usaremos `Get-Credential`).
4. **Lógica de Montaje:**
    - Montar siempre el recurso `publico` en la unidad **P:**.
    - Intentar montar el recurso de departamento (ej. `ventas`) en la unidad **Z:**.
    - El script debe capturar errores (`Try/Catch`): si el usuario no tiene permiso para montar `ventas` (porque es de `contabilidad`), el script no debe mostrar errores rojos feos, sino un mensaje amigable *"No tienes acceso a Ventas"*.


### Tarea 3: Verificación de Interoperabilidad

Realizar las pruebas funcionales que demuestren que los permisos funcionan:

- Un usuario de *Ventas* puede escribir en su carpeta pero no entrar en *Contabilidad*.
- Todos pueden leer *Publico*.
- Los permisos definidos en el CSV (770, 755) se reflejan correctamente en el servidor.

## 4. Criterios de Evaluación

La calificación del proyecto se distribuirá de la siguiente manera:

| Apartado                            | Puntos | Detalles requeridos |
| ----------------------------------- | ------- | --- |
| **Script Bash: estructura y CSV**   | **3.0** | Uso correcto de `while IFS=',' read...`. Parsing correcto de variables. |
| **Script Bash: lógica y logs**      | **2.5** | Control de errores (idempotencia), creación de usuarios/grupos y función de logging operativa. |
| **Configuración Samba (inyección)** | **1.5** | El script modifica `smb.conf` correctamente. Los permisos (ACLs) son seguros. |
| **Script PowerShell**               | **2.0** | Limpieza de unidades, comprobación de ping y manejo de errores (Try/Catch). |
| **Documentación y Pruebas**         | **1.0** | Capturas de pantalla demostrando que Windows accede (y deniega acceso) según lo previsto. |

---

## 5. Pistas y Ayuda

**Sobre la lectura de CSV en Bash:**
Recuerda que para leer un archivo separado por comas línea a línea se utiliza esta estructura:

```bash
while IFS=',' read -r columna1 columna2 columna3
do
    echo "He leído: $columna1 con valor $columna2"
done < "nombre_fichero.csv"

```

**Sobre la inyección en smb.conf:**
Para añadir texto al final de un archivo sin usar un editor de texto, puedes usar `cat` con redirección:

```bash
cat <<EOT >> /etc/samba/smb.conf
[$nombre_recurso]
   path = $ruta
   valid users = @$grupo
EOT

```

**Sobre PowerShell:**
Para mapear una unidad usando credenciales específicas:

```powershell
New-PSDrive -Name "Z" -PSProvider FileSystem -Root "\\IP_SERVIDOR\Carpeta" -Credential $variable_credenciales -Persist

```

---

**¡Buena suerte, SysAdmin!**





IP           USER    STATUS
192.168.1.1  admin   OK
10.0.0.5     guest   ERROR


