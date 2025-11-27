```
---------------- ADMINISTRACIÓN DE SISTEMAS INFORMÁTICOS Y REDES ----------------
---------------------------------------------------------------------------------

Módulo:                     ADMINISTRACIÓN DE SISTEMAS OPERATIVOS
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT05
Práctica:                   PR0503. Aplicación de directivas
Resultados de aprendizaje:  RA1
```



# PR0503: Aplicación de directivas

Para esta práctica seguirás trabajando con el dominio que creamos en la primera práctica de la unidad

### Directiva 1

- **Directiva**: No se puede  cambiar el fondo de pantalla del escritorio (`Configuración de usuario -> Plantillas administrativas -> Escritorio`)
- **A quienes se aplica**: a los usuarios de ASIR

### Directiva 2

- **Directiva**: se pueden ejecutar scripts de Powershell sin restricciones (`Configuración de equipo -> Plantillas administrativas -> Componentes de Windows -> Windows Powershell`).
- **A quienes se aplica**: a los usuarios de DAM

### Directiva 3

- **Directiva**: el firewall de Windows está habilitado (`Configuración de equipo -> Directivas -> Configuración de Windows -> Configuración de seguridad -> Windows Defender Firewall -> Perfil del dominio`)
- **A quienes se aplica**: todos los equipos del dominio
- **Excepciones**: esta directiva no debe aplicarse a un equipo específico denominado `DEV-PC1` (lo puedes poner en cualquier aula)

### Directiva 4

- **Directiva**: configura las actualizaciones para que se descarguen automáticamente y se instalen fuera del horario laboral (`Configuración de equipo -> Directivas -> Plantillas administrativas -> Componentes de Windows -> Windows Update -> Configurar actualizaciones automáticas`)
- **A quienes se aplica**: a todos los equipos del dominio

### Directiva 5

- **Directiva**: desactivar el acceso de lectura y escritura a dispositivos USB (`Configuración de equipo -> Directivas -> Plantillas administrativas -> Sistema -> Acceso de almacenamiento extraíble`)
- **A quienes se aplica**: a todos los equipos del dominio

### Directiva 6

- **Directiva**: el usuario no podrá repetir ninguna de las 10 últimas contraseñas-
- **A quienes se aplica**: a todos los usuarios del dominio
- **Excepciones**: habrá dos usuarios en el dominio llamados `jefe_dpto_1` y `jefe_dpto_2`, que pertenecen a las UO `informatica` y `administracion` respectivamente que no podrán repetir ninguna de las 2 últimas contraseñas.

### Directiva 7

- **Directiva**: especifica que los equipos portátiles pasen a hibernación después de 30 minutos de inactividad (`Configuración de equipo -> Plantillas administrativas -> Sistema -> Administración de energía`)
- **A quienes se aplica**: a los equipos portátiles, por lo que tendrás que utilizar un **filtro WMI** (necesitarás la clase [`Win32_Battery`](https://powershell.one/wmi/root/cimv2/win32_battery))



## Qué hay que entregar

Tienes que hacer un documento donde en el que expliques qué haces para que la GPO correspondiente se aplique a quienes pide el enunciado. Puedes acompañarlo de capturas de pantalla, pero lo importante es la explicación de cómo gestionas cada GPO

