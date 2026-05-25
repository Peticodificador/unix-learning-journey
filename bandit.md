# OverTheWire: Bandit - Guía de Comandos y Resolución

Este documento contiene mi camino resolviendo el wargame **Bandit** de OverTheWire. Las contraseñas han sido omitidas para evitar spoilers.

## Acceso Inicial (Nivel 0)
Para iniciar el juego, es necesario conectarse al servidor mediante SSH especificando el puerto y el usuario.

```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

## Nivel 0 a Nivel 1
**Objetivo:** Leer el contenido de un archivo estándar.
* `pwd`: para verificar el directorio actual.
* `ls`: para listar los archivos disponibles.
* `less readme` (o `cat readme`): para leer el contenido del archivo con la contraseña.

## Nivel 1 a Nivel 2
**Objetivo:** Leer un archivo cuyo nombre es un guion (`-`).
Los nombres de archivo con guiones pueden confundir a los comandos, ya que los interpretan como opciones/argumentos. Para leerlo, hay que especificar la ruta relativa.

```bash
less ./-
```

## Nivel 2 a Nivel 3
**Objetivo:** Leer un archivo que contiene espacios en su nombre.
Para manejar espacios en la terminal, se debe "escapar" el espacio usando barras invertidas (`\`) o envolver el nombre del archivo en comillas sin olvidar lo usado previamente con el guion.
Escribiendo el principio del nombre + tab la terminal te completa

```bash
less ./--spaces\ in\ this\ filename--
```

## Nivel 3 a Nivel 4
**Objetivo:** Encontrar y leer un archivo oculto dentro de un directorio.
Los archivos ocultos en Unix empiezan con un punto (`.`).

```bash
cd inhere
ls -a # La opción -a (all) muestra los archivos ocultos
less ...Hiding-From-You
```

## Nivel 4 a Nivel 5
**Objetivo:** Identificar un archivo legible por humanos entre varios archivos con nombres similares.
El comando `file` es fundamental para determinar el tipo de datos que contiene un archivo, independientemente de su extensión.

```bash
cd inhere
file ./* # Analiza todos los archivos del directorio
# Una vez identificado el archivo de texto ASCII (en este caso el 07):
less ./-file07
```

## Nivel 5 a Nivel 6
**Objetivo:** Identificar un archivo que cumpla los siguientes requisitos: no ejecutable, estar en ascii y pesar 1033 bytes.
El comando `find` te permite buscar dentro de todos los archivos del sistema (o mas limitado en caso de ser necesario).

Puedo filtrar por tamaño con `-size`, por tipo de archivo con `-type f`, primero pensé en buscar los permisos con el código hexa pero luego encontré el `! -executable`
Con `file` puedo saber si es ASCII o solo datos. 

Para usar ambos se me ocurrio usar un pipe pero habria un problema con lo que file toma como argumento, puedo usar el comando `xargs` para volcarlo como argumento o `$()` para correr con prioridad los comandos.

```bash
file $(find . -type f -size 1033c ! -executable) # Busca entre todos los archivos del directorio y los filtra
less archivo_filtrado
```

O la otra alternativa:

```bash
find . -type f -size 1033c ! -executable | xargs file
less archivo_filtrado
```

## Nivel 6 a Nivel 7

**Objetivo:** Identificar un archivo que cumpla las siguientes condiciones: sea del usuario bandit7, del grupo bandit 6 y pese 33 bytes.

Al igual que el punto anterior el protagonista es `find`. Sin embargo, al correr la siguiente linea:

```bash
find / -user bandit7 -group bandit6 -size 33c
```

La terminal te imprime tambien los permisos denegados perdiendo de vista el archivo deseado. Por eso es necesario filtrar, mandando todos los errores a la nada misma.

```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
less archivo_filtrado
```
**0 = Standard Input:** lo que le damos como entrada.  
**1 = Standard Output:** salida exitosa, lo que quiero ver.  
**2 = Standard Error:** mensajes de error que quiero ignorar (en este caso) `>/dev/null`.

## Nivel 7 a Nivel 8
**Objetivo:** Buscar la línea correcta dentro de un archivo grande.

El comando `grep` es ideal para buscar palabras determinadas dentro de un archivo. `grep [lo que busco] [donde lo busco]`

```bash
grep millionth data.txt
```

## Nivel 8 a Nivel 9
**Objetivo:** buscar la unica linea que no se repite en un archivo.

El comando `uniq` es la clave para resolver este desafio. Sin embargo solo funciona con listas ordenadas, para eso debe combinarse con `sort` y unirlos con un pipe `|`.

Mi primer intento fue utilizar la opcion `uniq -c` para contar la frecuencia de aparición de cada linea y filtrarlo con `grep`. Notar el doble espacio para filtrar las lineas que se repiten 10 u 11 veces, asi como los digitos dentro de la linea.

```bash
sort data.txt | uniq -c | grep " 1 "
```

Sin embargo, existe `uniq -u` que imprime unicamente las lineas unicas.

```bash
sort data.txt | uniq -u
```

## Nivel 9 a Nivel 10
**Objetivo:** encontrar la contraseña en un archivo que contiene texto leíble y binario, la contraseña es precedida por varios `=`.

El comando `strings` te permite filtrar los segmentos del archivo que se encuentren en ASCII. Esto, combinado con `grep` y RegEx permite la resolución del ejercicio. Sin embargo, para que `grep`utilice un motor RegEx moderno hay que usar la opción `-E`.

```bash
strings data.txt | grep -E "=+"
```

## Nivel 10 a Nivel 11
**Objetivo:** decodificar la contraseña de un archivo en base64.

El comando `base64` generalmente se utiliza para codificar pero con su opción `-d` nos permite hacer lo contrario.

```bash
base64 -d data.txt
```

## Nivel 11 a Nivel 12
**Objetivo:** decodificar la contraseña de un archivo con root13.

El comando `tr` te permite reemplazar, filtrar y borrar caracteres con variedad de opciones. Si aplicamos root13 de forma manual podemos pasarle que letras corresponden. Siempre se usa con un pipe.

```bash
cat data.txt | tr "A-Z""a-z" "N-ZA-M""n-za-m"
```

Nótese que hay que cambiar las mayúsculas y las minúsculas. Los números se dejan igual.

## Nivel 12 a Nivel 13
**Objetivo:** buscar la contraseña en un hexdump comprimido luego de haberlo copiado en un directorio temporal.

Para crear el directorio temporal utilizo `mktemp -d` (al usarlo te crea una secuencia aleatoria al final, en mi caso la limite a tres caracteres). Luego, copio el archivo y lo descomprimo

```bash
mktemp -d /tmp/ornitorrincovolador_XXX
cp data.txt /tmp/ornitorrincovolador_YA2
cd /tmp/ornitorrincovolador_YA2
```

Al tratar de descomprimir en este punto saltaría un error al ser todavía hexdump, para eso se revierte con el comando `xxd` y se usa `file` para corroborar que sea un comprimido

```bash
xxd -rp data.txt > archivobin
file archivobin
```

Luego del file se vera que tipo de comprimido tiene y en función de eso se siguen 3 caminos (que se repiten hasta que el file sea ASCII):

``` bash
mv archivobin archivobin.gz
gzip -d archivobin.gz
file archivobin
```

```bash
mv archivobin archivobin.bz2
bzip2 -d archivobin.bz2
file archivobin
```

```bash
mv archivobin archivobin.tar
tar -xvf archivobin.tar
file archivobin
```

[**EXTRAS**](./bandit_extra.md#nivel-12-a-nivel-13)


## Nivel 13 a Nivel 14
**Objetivo:** obtener privilegios de bandit14 mediante autenticación con clave privada SSH.

La conexión ya tiene establecida la `public key` por lo que nos queda utilizar la `private key`, para eso usamos el comando `ssh -i` 

```bash
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org
```

Sin embargo, esta deshabilitada la opción de LocalHost (entrar a vos mismo como servidor) por lo que la conexión debe realizarse desde el equipo del jugador. Además, la necesidad de la `private key` nos lleva a usar el comando `scp` (o copiar a mano pero esto es mas divertido).

Es importante notar la `-P` mayúscula y el armado del comando consiste en el destino y el `.` que representa el directorio actual.

```bash
scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private .
```

En caso de querer realizar la conexión apareceria un error de permisos, `sshkey.private` tiene demasiados para ser una `private key`, por lo que hay que cambiarlos con `chmod`.

En caso de usar *wsl* y estar en una carpeta de Windows es necesario que el archivo este en un directorio de Linux.

```bash
chmod 600 sshkey.private 
```

Finalmente:

```bash
 ssh -i ~/sshkey.private -p 2220 bandit14@bandit.labs.overthewire.org # si no estan en el caso que explique previamente no es necesario el ~
 cat /etc/bandit_pass/bandit14
```

[**EXTRAS**](./bandit_extra.md#nivel-13-a-nivel-14)

## Nivel 14 a Nivel 15
**Objetivo:** conectarse al puerto 30000 con la contraseña actual para recibir la nueva.

Mi primer intento fue utilizar el comando `ssh` pero el puerto estaba abierto con otro protocolo así que se puede realizar la comunicación únicamente con `telnet`. Recordando que al estar en el servidor de bandit para realizar una conexión con el puerto 30000 tenemos que conectarnos con nosotros mismos (localhost).

```bash
telnet 127.0.0.1 30000
```

Sin embargo, el comando `nc` suele ser más cómodo. 

```bash
nc 127.0.0.1 30000
```

En ambos casos hay que escribir la contraseña después de establecer la conexión, en el caso de telnet puede haber lag.

Otra alternativa es mandar la contraseña junto con la conexión.

```bash
echo [contraseña] | nc localhost 30000
```

[**EXTRAS**](./bandit_extra.md#nivel-14-a-nivel-15)

## Nivel 15 a Nivel 16
**Objetivo:** conectarse al puerto 30001 con la contraseña actual usando SSL/TLS.

Para resolverlo se puede usar el comando `openssl` abriendo una conexion con el puerto especificado recordando que como ya estamos dentro del servidor "salimos" por localhost (127.0.0.1).
 
```bash
 openssl s_client -crlf -connect localhost:30001 -servername localhost
```

Al correr ese comando se establece la conexión y te muestra toda la información del handshake. Luego, al estar la comunicación abierta solo se ingresa la contraseña. En caso de haber querido mandar la contraseña directamente se podría haber hecho de la misma forma que el anterior, pero para evitar el cierre de la conexión y esperar a recibir la contraseña hay que agregar la opción `-ign_eof`.

[**EXTRAS**](./bandit_extra.md#nivel-15-a-nivel-16)

## Nivel 16 a Nivel 17
**Objetivo:** descubrir cual de los puertos del 31000 al 32000 esta abierto y de los abiertos cual usa SSL/TLS.

Revisar de forma manual los mil puertos disponibles sería una odisea, por lo que se usa el comando `nmap` con sus opciones de especificar puerto y, dentro de esos puertos activos, que protocolo tienen para encontrar el SSL.

```bash
nmap -p 31000-32000 -sV localhost
```

Dentro de la lista vamos a tener puertos que el comando interpreta como `echo` porque devuelven lo mandado (como nos adelantó la consigna), para obtener la contraseña se utiliza el que resta.

```bash
echo [contraseña] | openssl s_client -crlf -ign_eof -connect localhost:[puerto] -servername localhost
```

Al ejecutar el comando recibimos una *private key* que nos permite entrar al próximo nivel.

[**EXTRAS**](./bandit_extra.md#nivel-16-a-nivel-17)

## Nivel 17 a Nivel 18
**Objetivo:** comprar dos archivos y encontrar la única línea diferente entre ambos.

Como el desafío anterior nos dio una *private key* en este es necesario crear el archivo, cambiarle los permisos y luego realizar la conexión como se hizo en puntos anteriores. Luego, una vez dentro del servidor se puede utilizar el comando `diff`, que compara que cambiar del primer archivo para tener el segundo.

```bash
diff passwords.new passwords.old
```

La salida estará compuesta por el numero de línea donde se produce el cambio, el contenido de un archivo y el contenido del otro, se diferencian con los símbolos `<>`.

## Nivel 18 a Nivel 19
**Objetivo:** 