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

Puedo filtrar por tamaño con `-size`, por tipo de archivo con -type f, primero pense en buscar los permisos con el codigo hexa pero luego encontre el `! -executable`
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
**Objetivo:** decodificar la contraseña de un archivo con root13

El comando `tr` te permite reemplazar, filtrar y borrar caracteres con variedad de opciones. Si aplicamos root13 de forma manual podemos pasarle que letras corresponden. Siempre se usa con un pipe.

```bash
cat data.txt | tr "A-Z""a-z" "N-ZA-M""n-za-m"
```

Notese que hay que cambiar las mayusculas y las minusculas. Los números se dejan igual.
