# OverTheWire: Bandit - Guía de Comandos y Resolución

Este documento contiene mis apuntes y comandos utilizados para resolver los primeros niveles del wargame **Bandit** de OverTheWire. Las contraseñas han sido omitidas para evitar spoilers.

## Acceso Inicial (Nivel 0)
Para iniciar el juego, es necesario conectarse al servidor mediante SSH especificando el puerto y el usuario.

```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

## Nivel 0 a Nivel 1
**Objetivo:** Leer el contenido de un archivo estándar.
* `pwd`: Para verificar el directorio actual.
* `ls`: Para listar los archivos disponibles.
* `less readme` (o `cat readme`): Para leer el contenido del archivo con la contraseña.

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