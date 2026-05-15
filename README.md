# Base de Datos (DB) - Tienda de Perritos

Este repositorio contiene la capa de almacenamiento de datos (Nivel 3) de la arquitectura de la aplicación web "Tienda de Perritos".

## Descripción

El proyecto define una base de datos MySQL contenerizada. En la infraestructura de AWS, esta base de datos debe ser desplegada en una **subred privada**, totalmente bloqueada al acceso externo. Solo recibe conexiones internas a través del puerto `3306` exclusivamente provenientes del grupo de seguridad de la instancia Backend.

## Archivos Principales

- `init.sql`: Script SQL principal que se ejecuta automáticamente al crearse por primera vez el contenedor. Se encarga de:
  - Crear la estructura de la tabla de la tienda (esquema `tienda_perritos`).
  - Poblar la base de datos con los registros y productos iniciales.
- `Dockerfile`: Instruye la creación de la imagen a partir de la imagen oficial de MySQL e inyecta el archivo `init.sql` en el directorio especial de inicialización (`/docker-entrypoint-initdb.d/`).

## Flujo de Trabajo y Despliegue

1. **Desarrollo:** Cualquier migración o cambio en la data inicial debe modificarse en el archivo `init.sql`.
2. **CI/CD:** Al realizar un `push` a la rama `deploy`, el pipeline automático (`.github/workflows/main.yml`) tomará el control:
   - Compilará la imagen de MySQL con el script `init.sql` modificado embebido.
   - Lo subirá a Amazon ECR.
   - Mediante SSM, le ordenará a la EC2 detener la base de datos anterior e iniciar la nueva conservando los volúmenes de datos (`-v dbdata:/var/lib/mysql`) para evitar pérdida de información.

> **Nota:** Este archivo `README.md` es de uso exclusivo para orientar a desarrolladores en el repositorio. Está configurado para ser ignorado tanto por Docker en tiempo de construcción como por GitHub Actions para evitar disparar el pipeline.
