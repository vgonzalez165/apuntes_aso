```
---------------- ADMINISTRACIÓN DE SISTEMAS INFORMÁTICOS Y REDES ----------------
---------------------------------------------------------------------------------

Módulo:                     ADMINISTRACIÓN DE SISTEMAS OPERATIVOS
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT06
Práctica:                   PROYECTO
Resultados de aprendizaje:  RA1, RA2, RA3, RA4, RA6, RA7
```

# Proyecto de Módulo: Integración de Sistemas Heterogéneos

## 1. Contexto empresarial

Trabajas para una empresa que históricamente ha trabajado solo con entornos Windows. Debido al aumento de costes en licencias, la dirección técnica ha decidido que el nuevo **Servidor de Almacenamiento** sea una máquina **Linux**, ya que es más eficiente y además, es gratuito.

Sin embargo, el Director de Seguridad ha impuesto un requisito innegociable: **"No quiero gestionar dos bases de datos de usuarios distintas. Los usuarios deben usar sus contraseñas actuales de Windows para entrar a las carpetas de Linux."**

Como el equipo técnico actual desconoce cómo conectar ambos mundos, se te ha encargado la investigación del procedimiento y la ejecución del despliegue.

## 2. Objetivos del proyecto

En este proyecto tienes que alcanzar los siguientes objetivos:

1. Desplegar una infraestructura de red básica con Active Directory.
2. Documentar y analizar los métodos existentes para integrar un host Linux en un dominio Microsoft.
3. Implementar la solución investigada para unir el servidor Linux al dominio.
4. Configurar Samba para compartir recursos utilizando ACLs basadas en usuarios/grupos del dominio (no locales).


## 3. Fases de Ejecución

### Fase A: Infraestructura base

Debes desplegar el siguiente entorno con todos los equipos en la misma red:

- **Servidor 1 (Windows Server):** controlador de dominio (Nombre de dominio: `pry-{iniciales}.local`, por ejemplo, `pry-vjgr.local`). Crea una estructura de usuarios (Ventas, IT, Gerencia) con.
- **Servidor 2 (Ubuntu Server LTS):** instalación básica, configuración de red y almacenamiento.
- **Cliente (Windows 10/11):** unido al dominio para pruebas.

### Fase B: Fase de investigación

Antes de configurar nada en el Linux, debes entregar un **informe técnico preliminar** que responda a lo siguiente:

1. **Análisis de herramientas:** investiga qué paquetes o servicios permiten a Linux *hablar* con Active Directory. Busca información sobre conceptos clave como: `Kerberos`, `Winbind`, `SSSD` y `Realmd`.
2. **Resolución de nombres:** ¿por qué es crítico el archivo `/etc/resolv.conf` y la configuración NTP (hora) para que esto funcione?
3. **Estrategia elegida:** ¿qué método vas a usar para unir el equipo?
   - *Opción Clásica:* Samba + Winbind.
   - *Opción Moderna:* SSSD + Realmd (Recomendada investigar).
4. **Mapeo de identidad:** explica teóricamente cómo Linux, que usa UIDs (números), va a entender los SIDs (identificadores) de Windows.

### Fase C: implantación e interoperabilidad

Una vez validada tu investigación, procede a la ejecución:

1. **Unión al dominio:** aplica el método investigado. Debes ser capaz de ejecutar un comando en Linux (ej. `getent passwd` o `wbinfo -u`) y ver listados los usuarios del Directorio Activo.

2. **Configuración de Samba:** configura el fichero `smb.conf`. Las carpetas compartidas NO deben usar usuarios locales de Linux (`useradd`). Deben validarse contra el AD.

3. **Permisos:** crea la carpeta `/srv/samba/ventas` y asegúrate de que solo el grupo `Ventas` del dominio Windows tiene acceso.



## 4. Entregables y evaluación

El proyecto se calificará en base a dos documentos, una demostración y, eventualmente, una exposición en público:

1. **Informe de la investigación** (40%): debes reflejar en un documento todo el proceso que has seguido para investigar cómo se añade una máquina Linux a un dominio Windows. En concreto:
   - ¿Qué tutoriales o documentación oficial consultaste? (bibliografía/webgrafía).
   - ¿Qué errores te dio al intentar unir el dominio y cómo supiste qué fallaba? (Logs, mensajes de error).
   - Justificación de por qué elegiste una herramienta (ej. *Realmd*) sobre otra.

2. **Manual de despliegue** (30%): guía limpia paso a paso para que un compañero pudiera repetir tu trabajo sin tener que investigar de nuevo.

3. **Defensa funcional** (30%): debes grabar un **vídeo** de una duración no superior a 10 minutos donde se muestre la funcionalidad de la solución implementada. El vídeo debe incluir por lo menos lo siguiente:
   - Se os pedirá crear un usuario nuevo en Windows Server en tiempo real.
   - Sin tocar el servidor Linux, ese usuario deberá poder iniciar sesión en una carpeta compartida inmediatamente.

