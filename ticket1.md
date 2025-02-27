Configuración de PostgreSQL con Docker

Este documento explica cómo configurar una base de datos PostgreSQL en Docker, solucionar problemas comunes y conectarse desde la terminal o DBeaver.

Requisitos

Docker y Docker Compose instalados.

DBeaver (opcional, para conectarse visualmente).

Instalación y configuración

🔹 ** 1 Clonar el repositorio y entrar en la carpeta del proyecto**

git clone <URL_DEL_REPO>
cd inventory-database

🔹 ** 2 Crear un archivo .env con las credenciales**

touch .env
nano .env

 Contenido del archivo .env:

POSTGRES_USER=admin
POSTGRES_PASSWORD=adminpassword
POSTGRES_DB=inventory_db
POSTGRES_PORT=5432

Guarda y cierra (CTRL + X, Y, Enter).

🔹 ** 3 Configurar docker-compose.yml**

touch docker-compose.yml
nano docker-compose.yml

 Contenido del docker-compose.yml:

version: '3.8'

services:
  postgres:
    image: postgres:latest
    container_name: inventory-db
    restart: always
    ports:
      - "${POSTGRES_PORT}:5432"
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${POSTGRES_DB}
    volumes:
      - pg_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
      interval: 10s
      retries: 5

volumes:
  pg_data:

Guarda y cierra (CTRL + X, Y, Enter).

🔹 ** 4 Levantar el contenedor con Docker**

docker-compose up -d

Para verificar que el contenedor está corriendo:

docker ps

🔹 ** 5 Conectarse a la base de datos**
✅ Desde la terminal:

psql -h localhost -p 5432 -U admin -d inventory_db

Si pide contraseña, ingresa adminpassword.

✅ Desde DBeaver:

Abrir DBeaver y crear una conexión nueva.

Seleccionar PostgreSQL y hacer clic en Next.

Ingresar los datos de conexión:

Host: localhost

Port: 5432

Database: inventory_db

Username: admin

Password: adminpassword

Hacer clic en "Test Connection" y luego en "Finish".

🔄 Comandos útiles

🔹 Ver logs del contenedor

docker-compose logs

🔹 Detener la base de datos

docker-compose down

🔹 Eliminar completamente la base de datos (si hay problemas de conexión)

docker-compose down
docker volume rm inventory-database_pg_data
docker-compose up -d

🔹 Reiniciar el contenedor

docker restart inventory-db

🔹 Ver los usuarios en PostgreSQL

docker exec -it inventory-db psql -U admin -d inventory_db -c "\du"

🔹 Cambiar la contraseña manualmente en PostgreSQL

docker exec -it inventory-db psql -U admin -d inventory_db

Dentro de PostgreSQL, ejecutar:

ALTER USER admin WITH PASSWORD 'adminpassword';

Salir de PostgreSQL con:

\q
