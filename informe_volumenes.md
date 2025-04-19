
# Práctica: Persistencia de Datos con Docker Volumes y PostgreSQL

## 1. Título
**Implementación de Persistencia en PostgreSQL usando Volúmenes de Docker**

## 2. Tiempo de duración
**90 minutos**

## 3. Fundamentos

Docker es una plataforma que permite crear, ejecutar y gestionar contenedores. Un contenedor es una unidad ligera y portátil de software que incluye todo lo necesario para ejecutar una aplicación. En este caso, se usó Docker para levantar una base de datos PostgreSQL con persistencia de datos, utilizando volúmenes.

Los volúmenes en Docker son una manera de almacenar datos fuera del contenedor, de modo que cuando el contenedor se elimina, los datos permanezcan disponibles. Esto es especialmente útil para bases de datos, donde la persistencia es crucial.

PostgreSQL es un sistema de gestión de bases de datos relacional y open source que permite almacenar, modificar y consultar datos de forma eficiente.

Cuando un contenedor se crea sin un volumen, los datos generados dentro del contenedor desaparecen al eliminarlo. Al usar volúmenes, se puede mantener la información incluso si el contenedor es destruido y vuelto a crear.

A continuación, se muestra cómo Docker maneja los volúmenes:

![Figura 3-1. Volúmenes en Docker](./img/volumenes_docker.png)

*Figura 3-1. Volúmenes en Docker. Fuente: Docker Docs*

En esta práctica, se crea un contenedor PostgreSQL, se conecta a través de un administrador gráfico como DataGrip, se crean tablas y se insertan registros. Luego, se detiene y elimina el contenedor, y al volverlo a crear usando el volumen, se comprueba que los datos siguen estando allí.

## 4. Conocimientos previos

Para realizar esta práctica, el estudiante necesita tener claro los siguientes temas:

- Comandos básicos de Linux.
- Instalación y manejo de Docker.
- Comandos para administración de contenedores (`docker run`, `docker stop`, `docker rm`, etc).
- Conexión de PostgreSQL con herramientas externas como DataGrip.
- Creación de bases de datos y tablas SQL.

## 5. Objetivos a alcanzar

- Implementar contenedores con PostgreSQL.
- Asociar un volumen al contenedor para garantizar persistencia de datos.
- Manipular bases de datos desde un administrador gráfico como DataGrip.
- Verificar la persistencia de datos después de reiniciar el contenedor.

## 6. Equipo necesario

- Computadora con sistema operativo Linux (recomendado), Windows o macOS.
- Docker instalado (versión recomendada: 24.x o superior).
- Acceso a internet.
- Editor gráfico para bases de datos como DataGrip o TablePlus.

## 7. Material de apoyo

- [Documentación oficial de Docker](https://docs.docker.com)
- [Documentación de PostgreSQL](https://www.postgresql.org/docs/)
- Guía de asignatura
- Linux Cheat Sheet (https://cheatography.com/davechild/cheat-sheets/linux-command-line/)

## 8. Procedimiento

**Paso 1:** Crear un volumen llamado `pgdata`.

```bash
docker volume create pgdata
```

**Paso 2:** Crear el contenedor PostgreSQL con el volumen asociado.

```bash
docker run --name server_db2 -e POSTGRES_PASSWORD=admin123 -v pgdata:/var/lib/postgresql/data -d -p 5432:5432 postgres
```

**Paso 3:** Conectarse al contenedor desde DataGrip usando los siguientes datos:
- Host: `localhost`
- Puerto: `5432`
- Usuario: `postgres`
- Contraseña: `admin123`

**Paso 4:** Crear la base de datos `test`, y dentro de ella crear la tabla `customer`.

```sql
CREATE DATABASE test;

\c test

CREATE TABLE IF NOT EXISTS customer (
    id SERIAL PRIMARY KEY,
    fullname VARCHAR(40),
    status BOOLEAN
);

INSERT INTO customer (fullname, status) VALUES
('Diego Pulgarin', true);
```

**Figura 8-1. Conexión a PostgreSQL desde DataGrip.**

![Figura 8-1. Conexión desde DataGrip](./img/datagrip_connection.png)

**Paso 5:** Verificar que la tabla y datos existen.

```sql
SELECT * FROM customer;
```

**Paso 6:** Detener y eliminar el contenedor.

```bash
docker stop server_db2
docker rm server_db2
```

**Paso 7:** Volver a crear el contenedor usando el mismo volumen.

```bash
docker run --name server_db2 -e POSTGRES_PASSWORD=admin123 -v pgdata:/var/lib/postgresql/data -d -p 5432:5432 postgres
```

**Paso 8:** Conectarse nuevamente desde DataGrip y verificar que la base de datos y los datos siguen existiendo.

## 9. Resultados esperados

Al reiniciar el contenedor y conectarse desde DataGrip, se espera que:
- La base de datos `test` siga existiendo.
- La tabla `customer` esté presente.
- Los registros insertados se conserven.

Esto indica que la persistencia de datos con volúmenes fue implementada correctamente.

**Figura 9-1. Visualización de tabla customer después de reiniciar el contenedor.**

![Figura 9-1. Tabla persistente](./img/tabla_persistente.png)

## 10. Bibliografía

Docker Inc. (2024). *Docker Documentation*. https://docs.docker.com  
The PostgreSQL Global Development Group. (2024). *PostgreSQL Documentation*. https://www.postgresql.org/docs/  
Torres, J. (2022). *Guía rápida de Docker para desarrolladores*. Ed. Alfaomega.
