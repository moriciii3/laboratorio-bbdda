# Laboratorio 1 Bases de Datos Avanzadas

## 1. Milanote e Informe

https://app.milanote.com/1W7fVD1oO8XT8B?p=6EJ6mRjOnjw

https://docs.google.com/document/d/16xM8XSetlkASOjV8QMwzE4HNa5CV8qQfETfRsPgctHs/edit?tab=t.0

## 2. Pasos para levantar arquitectura y testear consola de Cassandra

### 2.1. Levantar arquitectura Cassandra con Docker Compose
```bash
docker-compose up -d --build
```

SER PACIENTE, demora en levantar.

De aquí en adelante todo se realiza desde el archivo `notebook.ipynb`.

### 2.2. Testear conexión a Cassandra nodo_1 con cqlsh
```bash
docker exec -it nodo_1 cqlsh
```

### 2.3. Testear creación de Keyspace

Usaremos **Factor de replicación** de 3 y **SimpleStrategy** debido a que solicitan eso en el enunciado:

```sql
CREATE KEYSPACE inf325grupo2 WITH replication = { 'class': 'SimpleStrategy',
    'replication_factor': 3
};
```

Luego accededemos al keyspace:

```sql
use inf325grupo2;
``` 

## 3. Pasos para conectar Cassandra a Power BI

### 3.1 Instalar Nginx

Descargar la **stable version** para Windows desde:
👉 https://nginx.org/en/download.html

Extraer el zip en alguna carpeta.

### 3.2 Configurar Nginx

Abrir el archivo `conf/nginx.conf` y agregar al **final**:
```
stream {
upstream cassandra_nodes {
server 127.0.0.1:9042;
server 127.0.0.1:9043;
server 127.0.0.1:9044;
}
server {
listen 19042;
proxy_pass cassandra_nodes;
}
}
```

### 3.4 Iniciar Nginx

Hacer doble click en `nginx.exe`. Se abrirá una consola por medio segundo y se cerrará, ta corriendo, pa q no t asustes.

### 3.5 Instalar el driver ODBC de Cassandra

1. Ir a [cdata.com/drivers/cassandra/odbc](https://www.cdata.com/drivers/cassandra/odbc/) y hacer click en **Download Trial**.
2. Ingresar el correo y hacer click en **Download**.
3. En el instalador, completar solo los campos obligatorios.
4. Cuando pregunte por **Product Key Activation**, seleccionar **Trial Key** (dura 30 días, suficientes para el laboratorio).


### 3.6 Configurar la conexión

Rellenar el formulario con los siguientes datos:

| Campo             | Valor          |
|-------------------|----------------|
| Server *          | 127.0.0.1      |
| Port *            | 19042          |
| Auth Scheme       | Basic          |
| User *            |                |
| Password *        |                |
| Database          | inf325grupo2   |
| Use SSL           | False          |
| Consistency Level | ONE            |

> **Importante:** Antes de q hagas click en **Test Server**, asegúrate de tener Cassandra levantada, si no dará error.

Si todo está correcto, aparecerá el mensaje: _"The connection test was successful."_


### 3.7 Verificar en ODBC Data Sources

1. Buscar **ODBC Data Sources** en el buscador de Windows.
2. Comprobar que el driver de Cassandra aparece listado.
3. Si no aparece, ir a la pestaña **System DSN** → **Add** → seleccionar el driver de Cassandra y confirmar.


### 3.8 Instalar y configurar Power BI

1. Descargar **Power BI** desde la Microsoft Store.
2. Abrir la aplicación y abrir el proyecto **Dashboard.pbix**
3. En la cinta de herramientas, presionar **Actualizar Datos**
4. Se abrirá una pestaña para ingresar usuario y contraseña (puedes poner el que quieras pero no lo olvides).
5. Y listo :), ya tienes cargado PowerBI con tu conexión a la BDD.

## 4. Qué resuelve Nginx?: alta disponibilidad en Cassandra

Para que Power BI no pierda conexión cuando cae un nodo de Cassandra,
se usa Nginx como intermediario. CData apunta a Nginx, y este
redirige al nodo disponible automáticamente.

Power BI → CData (127.0.0.1:19042) → Nginx → nodo_1:9042
→ nodo_2:9043
→ nodo_3:9044


### 4.4 En caso de error: Verificar CData

En la configuración del DSN de CData debe tener los siguientes valores:
- **Server**: `127.0.0.1`
- **Port**: `19042`

