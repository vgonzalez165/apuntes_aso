```
---------------- ADMINISTRACIÓN DE SISTEMAS INFORMÁTICOS Y REDES ----------------
---------------------------------------------------------------------------------

Módulo:                     ADMINISTRACIÓN DE SISTEMAS OPERATIVOS
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT04
Práctica:                   PR0401. Administración remota de Windows
Resultados de aprendizaje:  RA4
```

# PR0401:  Administración remota con Powershell


En esta práctica vamos a realizar tareas de administración remota de un servidor en modo Core utilizando Powershell..

## 1.- Entorno virtualizado

Necesitarás la siguiente configuración de máquinas virtuales:
- **Windows Server 2019 con experiencia de escritorio** 
- **Windows Server 2016 en modo Core**
- **Windows Server 2016 en modo Core**

Todos los equipos tendrán que tener un adaptador en modo solo-anfitrión en la misma red y otro en modo NAT por si necesitaras acceso a Internet.


## 2. Preparación de las máquinas

- Comprueba la conectividad entre los tres equipos:
- Asigna **nombres a los equipo**, estos nombres serán:
  - Windows Server 2019 con entorno gráfico: `{INICIALES}-2019`
  - Windows Server 2019 en modo core: `{INICIALES}-CORE-2019`
  - Windows Server 2016 en modo core: `{INICIALES}-CORE-2016`
- Edita el fichero `hosts` de cada equipo para la habilitar la resolución local de nombres entre ellos.

## 3. Configuración del acceso remoto al nuevo equipo

El objetivo es realizar los pasos necesarios para administrar los dos equipos en modo Core desde el equipo con entorno gráfico.

Para comprobar que funciona crea, desde el equipo con entorno gráfico, un usuario con privilegios de administrador llamado `admin_{iniciales}`. Si no sabes cómo hacerlo tienes una breve guía [aquí](https://intelaf.wordpress.com/2022/08/12/como-crear-usuario-administrador-desde-powershell-en-windows-11/)


## 4. Configuración del acceso remoto sobre HTTPS

- Una vez que hayas comprobado que tienes todo bien configurado es el momento de asegurar nuestra red preparándola para que utilice **WinRM sobre HTTPS** utilizando un certificado autofirmado.
- Realiza los pasos necesarios para que la comunicación con ambos servidores utilice este mecanismo.


## 5. Configuración remota con Windows Admin Center

- Por último, configura tus equipos para poder administrarlos de forma remota utilizando **Windows Admin Center** desde el equipo con entorno gráfico.


## 6. Documentación

Como es habitual, tienes que documentar los pasos más relevantes que has seguido para realizar la práctica.