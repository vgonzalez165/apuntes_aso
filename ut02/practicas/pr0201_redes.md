# PR0101: Introducción a Vagrant

### ¿Qué vamos a aprender?

Esta práctica servirá para afianzar tus conocimientos de configuración de redes en Linux

### ¿Qué recursos me pueden ser útiles?

A continuación se indican algunos recursos que complementan los apuntes del módulo y que te pueden ser útiles para realizar esta práctica:

- [Imagen Ubuntu 22.04](https://app.vagrantup.com/generic/boxes/ubuntu2204)

### ¿Con qué entorno vamos a trabajar?

Por comodidad utilizaremos máquinas levantadas con Vagrant, en concreto, usaremos la imagen denominada **generic/ubuntu2204**. Sin embargo, ten en cuenta que la configuración de red no se aplicará desde el Vagrantfile, sino que la realizaremos directamente desde la propia máquina.

Necesitarás dos máquinas que se llamarán `server-{iniciales}-1` y `server{iniciales}-2`.


### ¿Qué hay que hacer?

Una vez que tengas las máquinas levantadas apágalas (`vagrant halt`), vete a configuración de red y añade dos nuevos adaptadores de red: uno en modo solo anfitrión y otro en modo puente. A continuación vuelve a arrancar las máquinas (`vagrant start`).

Realiza las siguientes configuraciones de red en la máquina:

- En el adaptador en modo **solo anfitrión**:
  - En ambas máquinas obtendrá la IP automáticamente
  - Averigua qué IP les asigna VirtualBox
- En el adaptador en modo **puente**:
  - Asígnale a cada equipo una IP de la red `172.21.XX.0/24` donde `XX` es tu número de equipo en el aula

### ¿Qué hay que entregar?

Debes entregar:

- Documentación de los pasos más relevantes



[Volver al índice](../index.html)