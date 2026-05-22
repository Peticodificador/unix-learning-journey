# Command Challenge: Guía de Comandos y Resolución

Este documento contiene mi camino resolviendo los desafíos de Command Challenge por lo que contendrá spoilers de las resoluciones finales junto con la deducción que me llevó a resolverlos.

Por otro lado, los primeros niveles al ser un comando directo en su mayoría no contendrá explicación.

- [[#Hello World|Hello World]]
- [[#CWD|CWD]]
- [[#List Files|List Files]]
- [[#File Contents|File Contents]]
	- [[#File Contents#Last Lines|Last Lines]]
	- [[#File Contents#Create a File|Create a File]]
- [[#Create a Directory|Create a Directory]]
- [[#Copy File|Copy File]]
- [[#Move File|Move File]]
- [[#Create Symlink|Create Symlink]]

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

### Last Lines

```bash
tail -5 access.log
```

### Create a File

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

```