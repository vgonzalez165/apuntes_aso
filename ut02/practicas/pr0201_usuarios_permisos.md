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


[referencia](https://blog.elhacker.net/2022/02/icheros-etc-passwd-shadow-y-group.html)
[salt](https://unix.stackexchange.com/questions/52108/how-to-create-sha512-password-hashes-on-command-line)

A no ser que se indique lo contrario realiza todas las operaciones desde tu directorio personal.

1.	Crea el directorio PR0201 dentro de tu directorio personal y dentro de él crea los directorios dir1 y dir2 ¿Cuáles son los permisos del directorio dir1? No pongas una captura, explica quiénes tienen permisos sobre el directorio y qué pueden hacer en él.

2.	Utilizando   la   notación   simbólica, elimina   todos   los   permisos de   escritura (propietario, grupo, otros) del directorio dir2.

3.	Utilizando la notación octal, elimina el permiso de lectura del directorio dir2, al resto de los usuarios.

4.	¿Cuáles son ahora los permisos asociados a dir2?

5.	Crear bajo dir2, un directorio llamado dir21.

6.	Concédete a ti mismo permiso de escritura en el directorio dir2 e intenta de nuevo el paso anterior.

7.	Dentro de dir2 crea un fichero en blanco llamado permisos.



8.	Escribe el comando o comandos que necesitarías para establecer los siguientes permisos en el fichero anterior utilizando notación simbólica. En todos los casos, partimos de que el fichero tiene los permisos rw-r--r--

- `rwxrwxr-x` :
- `rwxr--r--` :
- `r--r-----` :
- `rwxr-xr-x` :
- `rwxr-xr-x` :
- `r-x--x--x` :
- `-w-r----x` :
- `-----xrwx` :
- `r---w---x` :
- `-w-------` :
- `rw-r-----` :
- `rwx--x--x` :

9.	Escribe el comando que necesitarías para establecer los siguientes permisos en el fichero anterior utilizando notación octal.
rwxrwxrwx :

- `--x--x--x` :
- `r---w---x` :
- `-w-------` :
- `rw-r-----` :
- `rwx--x--x` :
- `rwxr-xr-x` :
- `r-x--x--x` :
- `-w-r----x` :
- `-----xrwx` :



### ¿Qué hay que entregar?

Debes entregar:

- Documentación de los pasos más relevantes



[Volver al índice](../index.html)