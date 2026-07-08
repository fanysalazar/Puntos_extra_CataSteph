# Despliegue Automatizado de WordPress con Docker, Git y Jenkins

Proyecto de práctica de la asignatura **Organización de Archivos** — Universidad Politécnica Internacional.
Prof. Ing. José Javier Mata Guerrero, MBA.

## Objetivo

Implementar un ambiente completo de WordPress utilizando Docker Compose, versionarlo con Git
y automatizar el despliegue mediante Jenkins.

## Estructura del proyecto

```
.
├── docker-compose.yml   # Definición de servicios: WordPress, MySQL, red y volúmenes
├── .env                 # Variables de entorno (credenciales, puertos, nombres)
├── Jenkinsfile           # Pipeline de despliegue automatizado
└── README.md             # Este archivo
```

## Requisitos previos

- Docker y Docker Compose instalados en el servidor/host.
- Jenkins instalado con acceso al daemon de Docker (usuario `jenkins` agregado al grupo `docker`).
- Git instalado y repositorio remoto configurado en GitHub.

## Actividad 1 y 2 — Docker Compose y configuración

El archivo `docker-compose.yml` define dos servicios:

- **mysql_db**: base de datos MySQL 8.0, con volumen persistente `db_data`, variables tomadas de `.env` y `restart: always`.
- **wordpress**: imagen oficial de WordPress, conectada a MySQL mediante la red personalizada `wordpress_network`, con volumen persistente `wp_data`.

Todas las contraseñas, nombres de base de datos, usuario, puertos y nombres de contenedores se definen en `.env` y se inyectan en el compose mediante variables `${VARIABLE}`.

## Actividad 3 — Levantar el ambiente

```bash
docker compose up -d
```

Verificación:

```bash
docker ps
docker network ls
docker volume ls
```

## Actividad 4 — Validar comunicación

Para comprobar que WordPress y MySQL están en la misma red y se comunican correctamente:

```bash
docker inspect wordpress_network
docker inspect wordpress_app | grep -A 5 Networks
docker inspect mysql_db | grep -A 5 Networks
```

## Actividad 5 — Git

```bash
git init
git add .
git commit -m "Agrega docker-compose.yml con servicios WordPress y MySQL"
git remote add origin <URL_DEL_REPOSITORIO>
git push -u origin main
```

Se recomienda realizar al menos tres commits descriptivos, por ejemplo:

1. `Agrega docker-compose.yml con servicios WordPress y MySQL`
2. `Agrega archivo .env con variables de entorno`
3. `Agrega Jenkinsfile con pipeline de despliegue automatizado`

## Actividad 6 — Jenkins

El `Jenkinsfile` define un pipeline declarativo con las siguientes etapas:

1. **Checkout**: obtiene el código desde el repositorio Git.
2. **Detener contenedores previos**: ejecuta `docker compose down`.
3. **Levantar ambiente**: ejecuta `docker compose up -d`.
4. **Verificar despliegue**: ejecuta `docker ps`, `docker network ls` y `docker volume ls`.

Para configurarlo en Jenkins:

1. Crear un nuevo item tipo **Pipeline**.
2. En la sección *Pipeline*, seleccionar **Pipeline script from SCM**.
3. Indicar el repositorio Git del proyecto y la rama (`main`).
4. Guardar y ejecutar **Build Now**.

El pipeline permite redesplegar automáticamente cada vez que se ejecuta, ya que primero elimina los contenedores existentes y luego los vuelve a crear a partir del `docker-compose.yml` versionado.

## Actividad 7 — Validación final

Acceder desde el navegador a:

```
http://localhost:8081
```

Debe aparecer el asistente de instalación de WordPress, confirmando que el contenedor de WordPress se comunica correctamente con la base de datos MySQL.

## Restricciones cumplidas

- No se utilizó Docker Desktop para crear contenedores manualmente; todo el despliegue se realiza mediante `docker-compose.yml`, `.env` y el `Jenkinsfile`.
- El pipeline es capaz de redesplegar automáticamente el ambiente completo en cada ejecución.

## Entregables

- Repositorio en GitHub con el proyecto completo.
- `docker-compose.yml`
- `.env`
- `Jenkinsfile`
- Capturas de pantalla de: ejecución del Pipeline en Jenkins, salida de `docker ps`, `docker network ls`, `docker volume ls` y la pantalla inicial del asistente de instalación de WordPress.
