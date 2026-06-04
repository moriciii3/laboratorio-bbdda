# Laboratorio 2 Bases de Datos Avanzadas - MongoDB

## Canva
https://canva.link/4ssuud4l3s0sk3z

## 1. Milanote
https://app.milanote.com/1WqvXv1a5RqTaD?p=eBAyBpEa6Vi

## 2. Informe
https://docs.google.com/document/d/1czC1HJErA9iWZBaV5lb_k82w8JsqfmN7JPsVRzxuEoI/edit?tab=t.0

## 3. Pasos para levantar arquitectura y testear

### 3.0 Editar el archivo 'hosts' de Windows
Gracias a esto Docker puede actuar como un DNS y resolver los nombres de host que le asignamos a cada nodo (mongo-primary, mongo-secondary1, mongo-secondary2) a la IP del contenedor correspondiente.

Abrir PowerShell como administrador y ejecutar el siguiente comando para agregar las entradas al archivo 'hosts':

```bash
Add-Content -Path "C:\Windows\System32\drivers\etc\hosts" -Value "`n127.0.0.1   mongo-primary`n127.0.0.1   mongo-secondary1`n127.0.0.1   mongo-secondary2"
```

Verificar que quedó listo el cambio:

```bash
Get-Content "C:\Windows\System32\drivers\etc\hosts"
```

### 3.1. Levantar arquitectura MongoDB
Ejecutamos el comando de compose para levantar la arquitectura de 3 nodos
```bash
docker-compose up -d --build
```

Esto dejará los siguientes nodos corriendo:
- mongo-primary (puerto 30001)
- mongo-secondary1 (puerto 30002)
- mongo-secondary2 (puerto 30003)

Y nuestro Réplica Set se llamará **rs-politica**

### 3.2. Conectarse a MongoDB

Ejecutamos el nodo primario (puerto 30001) para conectarnos a la base de datos y ejecutar comandos.

```bash
docker exec -it mongo-primary mongo --port 30001
```

Ahora podemos probar comandos tales como:
```javascript
show dbs                        // ver todas las bases de datos
use Política                    // seleccionar la BD
show collections                // ver colecciones
db.Discursos.find()             // ver documentos
db.Discursos.find().pretty()    // ver documentos formateado
```
