# Command Challenge: Guía de Comandos y Resolución

Este documento contiene mi camino resolviendo los desafíos de Command Challenge por lo que contendrá spoilers de las resoluciones finales junto con la deducción que me llevó a resolverlos.

Por otro lado, los primeros niveles al ser un comando directo en su mayoría no contendrá explicación.

## Hello World

```bash
echo hello world
```

## CWD

```bash
pwd
```

## List Files

```bash
ls -1
```

## File Contents

```bash
cat access.log
```

## Last Lines

```bash
tail -5 access.log
```

## Create a File

```bash
touch take-the-command-challenge
```

## Create a Directory

```bash
mkdir -p tmp/files
```

## Copy File

```bash
cp take-the-command-challenge tmp/files
```

## Move File

```bash
mv take-the-command-challenge tmp/files
```

## Create Symlink

```bash
ln -s tmp/files/take-the-command-challenge take-the-command-challenge
```

## Delete Files

El comando `rm -rf *` no funciona porque el `*` sirve actuar sobre todo lo visible, en cambio podemos usar `$(ls -A)` para listar todo lo que deseamos borrar.  

```bash
rm -rf $(ls -A) 
```

## Remove Files with Extension

```bash
rm -rf $(find . -name "*.doc")
```

## Find String

```bash
grep GET  access.log
```

## Search for String

Un primer acercamiento podría ser:

```bash
grep 500 *
```

Pero esta solución tiene el problema que `grep` no imprime el nombre del archivo que contiene lo buscado sino que imprime cada línea de cada archivo que lo contenga, incumpliendo la consigna; para corregir ese comportamiento se agrega la opción `l`.

```bash
grep -l 500 *
```

## Search for Extension

```bash
find . -name "access.log*" 
```

## Search Recursive

Para esta búsqueda resulta de utilidad el `-exec`, que le dice el `find` que comando correr para cada coincidencia que encuentre. 

Al momento de ejecutarse las llaves `{}` se reemplazan por la ruta de cada archivo encontrado, el `\;` son el símbolo de escape y el cierre de comando respectivamente-

```bash
find . -name "access.log*" -exec grep 500 {} \;
```

## Find IP Address

Para resolver este desafío es necesario tener conocimiento de **CLF** (Common Log Format), es el que habitualmente se usa para los `.log`, en el la dirección IP es lo primero en aparecer en la línea.

Se puede seguir el mismo patrón que en el ejercicio anterior agregándole una búsqueda RegEx para encontrar las IP, es importante agregar la opción `-o` para que muestre únicamente el match y no la línea entera.

```bash
find . -name "access.log*" -exec grep -Eo "^[0-9]{1,3}(\.[0-9]{1,3}){3}"  {} \;
```

## Count Lines

No existe (o no conozco) un comando simple que te de la salida necesaria de un solo accionar, así que primero se lista el contenido del directorio (`-A` te permite listar todo menos `.` y `..`) y luego se cuenta la lista.

No hace falta agregar `xargs` porque `wc` puede actuar sobre texto, no necesariamente sobre objetos. En caso de usarlo contaría las líneas de cada uno de los archivos listados.

```bash
ls -A | wc -l
```

## Simple Sort

```bash
sort access.log
```

## Count the Strings

```bash
grep -c "GET" access.log
```


