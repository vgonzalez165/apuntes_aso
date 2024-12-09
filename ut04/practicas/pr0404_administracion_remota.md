# PR0404: Administración remota del servidor

En esta práctica vamos a complementar lo que hicimos en clase el otro día preparando la administración remota de un servidor en modo Core.

## 1.- Entorno virtualizado

Necesitarás la siguiente configuración de máquinas virtuales:
- **Windows Server 2019 con experiencia de escritorio** (debes utilizar el que ya tienes preparado del otro día)
- **Windows Server 2016 en modo Core**, este equipo tendrá dos adaptadores de red, uno en modo NAT y otro en modo solo-anfitrión

Vamos a aprovechar la misma red que preparamos el otro día (`172.25.0.0/16`) para añadir el nuevo equipo a la misma a través de su adaptador en modo solo-anfitrión.


## 2. Preparación de las máquinas

- **Añade el nuevo equipo a la red**, *comprueba que tiene visibilidad* con los tres equipos que ya tienes en ella (las dos máquinas virtuales y el equipo anfitrión)
- Asigna **nombres a los equipo**, estos nombres serán:
  - Windows Server 2019 con entorno gráfico: `{INICIALES}-2019`
  - Windows Server 2019 en modo core: `{INICIALES}-CORE-2019`
  - Windows Server 2016 en modo core: `{INICIALES}-CORE-2016`

## 3. Configuración del acceso remoto al nuevo equipo

- Realiza los pasos necesarios para poder utilizar **PowershellDirect** sobre el nuevo equipo desde el servidor principal (el que tiene entorno gráfico)
- Asegúrate de que sigas pudiendo administrar el otro equipo en modo Core
- Para comprobarlo, crea de forma remota en ambos equipo en modo Core un usuario con privilegios de administrador llamado `{iniciales}`. Si no sabes cómo hacerlo tienes una breve guía [aquí](https://intelaf.wordpress.com/2022/08/12/como-crear-usuario-administrador-desde-powershell-en-windows-11/)


## 4. Configuración del acceso remoto sobre HTTPS

- Una vez que hayas comprobado que tienes todo bien configurado es el momento de asegurar nuestra red preparándola para que utilice **WinRM sobre HTTPS** utilizando un certificado autofirmado.
- Realiza los pasos necesarios para que la comunicación con ambos servidores utilice este mecanismo.


## 5. Configuración remota con Windows Admin Center

- Por último, configura tus equipos para poder administrarlos de forma remota utilizando **Windows Admin Center** desde el equipo con entorno gráfico.


## 6. Documentación

Como es habitual, tienes que documentar los pasos más relevantes que has seguido para realizar la práctica.