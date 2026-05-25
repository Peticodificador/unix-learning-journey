# Teoría y aclaraciones adicionales

Este documento comienza como una ayuda personal de conceptos teóricos que no debo olvidar durante la resolución de un desafío, por lo que los apuntes serán mas coloquiales y no tan profesionales.

## Nivel 12 a Nivel 13

**Hex dump:** archivo escrito en Hexa, desde las extensiones hasta el contenido. Si usas el comando hex dump en Unix te muestra tres columnas, espacio de memoria - Hexa - ASCII.

Para eso `hexdump -C [resto]`

**Comprimidos**: los comprimidos son caprichosos y tenes que acomodarles la extensión sino no laburan bien. El `tar` te mantiene el comprimido original

## Nivel 13 a Nivel 14

### SSH
Conexión remota segura a un servidor.
* **Usuario y contraseña:** lo que suena
*  **Keys:** el cliente genera una llave publica y una privada, cuando te querés conectar el servidor chequea que la publica sean iguales y le manda un *desafío* que el cliente resuelve con la privada
		**Private Key:** el permiso debe ser solo de lectura y escritura para el usuario
### Notación Hexa permisos 
* **r:** 4
* **w:** 2
* **x:** 1
* **-:** 0
El orden de los permisos es `PROPIETARIO | GRUPO | OTROS`

## Nivel 14 a Nivel 15
### Localhost
Nombre que le damos a la dirección del loopback, puede ser toda la clase 127.0.0.0/8 pero por convención usamos 127.0.0.1/32, si mandas algo a esa dirección te vuelve

## Nivel 15 a Nivel 16

### SSL/TLS
Ambos son protocolos en la capa de transporte (en realidad esta un poquito en todas las de arriba, es mas presentación o aplicación, pero es más fácil pensarlo así) pero uno es el sucesor del otro, a día de hoy SSL es obsoleto y cuando se lo nombre generalmente uno se refiere a TLS. 

Generalmente se usa el puerto 443 para https (con TLS) y el 80 para http (sin TLS).

La conexión se establece con un handshake en el que se comparte una *public key* fija y una provisional con la que se calcula la *session key* y se encripta todo.

### OpenSSL
Este comando no solo necesita opciones sino que también un subcomando que le diga que hacer.

```bash
openssl command [ command_opts ] [ command_args ]
```

## Nivel 16 a Nivel 17
### nmap
Es un comando que es de mucha utilidad para el escaneo de redes, tiene una notacion particular.

```bash
nmap [Tipo de sondeo] [Opciones] {especificación de objetivo}
```
