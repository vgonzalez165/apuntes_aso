# UT04.- INSTALACIÓN Y PUESTA EN MARCHA DE WINDOWS SERVER


## 4.- Grupos de almacenamiento con Powershell

Un **grupo de almacenamiento** en Windows Server es una colección de discos físicos que se agrupan para organizar el almacenamiento y facilitar la administración. Estos grupos se crean utilizando la funcionalidad de Storage Spaces, que permite la creación de volúmenes tolerantes a fallos y de alta disponibilidad.

Los requisitos previos para crear grupos de almacenamiento son:
- Tener acceso administrativo al servidor.
- Contar con al menos dos discos físicos sin particionar en el servidor.
- Asegúrate de que el servidor tiene habilitada la característica Storage Spaces.

### 4.1.- Enumerar discos disponibles

Antes de crear un grupo de almacenamiento, identifica los discos físicos disponibles:

```powershell
Get-PhysicalDisk
```

Esto devolverá una lista de discos con su estado actual. Busca discos con el estado `CanPool = True`, que indica que pueden ser usados en un grupo de almacenamiento.

### 4.2.- Creación del grupo de almacenamiento

#### 4.2.1.- Elegir los discos para el grupo

Guarda los discos que deseas incluir en el grupo en una variable:

```powerhsell
$discos = Get-PhysicalDisk | Where-Object CanPool -eq $true
```

#### 4.2.2.- Crear el grupo de almacenamiento

Crea un nuevo grupo de almacenamiento con los discos seleccionados:

```powershell
New-StoragePool -FriendlyName "GrupoAlmacenamiento1" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $discos
```

Los parámetros son:
- `-FriendlyName`: Nombre que identificará al grupo de almacenamiento.
- `-StorageSubsystemFriendlyName`: Normalmente, usa "Storage Spaces*" para seleccionar el subsistema de almacenamiento.
- `-PhysicalDisks`: Lista de discos a incluir en el grupo.

#### 4.2.3.- Crear un Volumen Virtual (Virtual Disk)

Un volumen virtual se encuentra dentro de un grupo de almacenamiento y define cómo se organizarán los datos.

Paso 1: Crear un disco virtual

Crea un disco virtual con tolerancia a fallos (por ejemplo, un espejo):

New-VirtualDisk -StoragePoolFriendlyName "GrupoAlmacenamiento1" -FriendlyName "DiscoVirtual1" -Size 50GB -ResiliencySettingName Mirror -ProvisioningType Thin

    -ResiliencySettingName: Define la configuración de tolerancia a fallos. Opciones comunes:
        Simple: Sin redundancia.
        Mirror: Copias redundantes.
        Parity: Similar a RAID 5.
    -Size: Tamaño del disco virtual.
    -ProvisioningType: Define si el almacenamiento es Thin (aprovisionamiento dinámico) o Fixed (aprovisionamiento fijo).

4. Inicializar y Formatear el Disco Virtual

Una vez creado el disco virtual, inicialízalo, crea una partición y asígnale un sistema de archivos.
Paso 1: Obtener el disco virtual

Identifica el disco virtual recién creado:

Get-VirtualDisk | Where-Object FriendlyName -eq "DiscoVirtual1"

Paso 2: Inicializar el disco

Inicializa el disco:

Initialize-Disk -Number 1

(Reemplaza 1 con el número del disco devuelto por el comando anterior).
Paso 3: Crear una partición

Crea una partición en el disco inicializado:

New-Partition -DiskNumber 1 -UseMaximumSize -AssignDriveLetter

Paso 4: Formatear la partición

Formatea la partición con el sistema de archivos NTFS:

Format-Volume -FileSystem NTFS -DriveLetter E

(Reemplaza E con la letra asignada automáticamente en el paso anterior).
5. Verificar la Configuración
Listar grupos de almacenamiento:

Get-StoragePool

Ver discos virtuales:

Get-VirtualDisk

Ver volúmenes disponibles:

Get-Volume

6. Opcional: Eliminar un Grupo de Almacenamiento

Si necesitas eliminar un grupo de almacenamiento, hazlo con cuidado, ya que se perderán los datos almacenados.

    Desmonta los discos virtuales:

Remove-VirtualDisk -FriendlyName "DiscoVirtual1"

Elimina el grupo de almacenamiento:

Remove-StoragePool -FriendlyName "GrupoAlmacenamiento1"

