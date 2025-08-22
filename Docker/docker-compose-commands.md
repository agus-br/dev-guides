# 📌 DOCKER COMPOSE - COMANDOS ÚTILES (v1.0)
---

- [📌 DOCKER COMPOSE - COMANDOS ÚTILES (v1.0)](#-docker-compose---comandos-útiles-v10)
  - [🔹 Inicialización y configuración](#-inicialización-y-configuración)
  - [🔹 Ciclo de vida de los servicios](#-ciclo-de-vida-de-los-servicios)
  - [🔹 Gestión de servicios](#-gestión-de-servicios)
  - [🔹 Escalado](#-escalado)
  - [🔹 Ejecución dentro de contenedores](#-ejecución-dentro-de-contenedores)
  - [🔹 Construcción y reconstrucción](#-construcción-y-reconstrucción)
  - [🔹 Limpieza](#-limpieza)

---

## 🔹 Inicialización y configuración
```bash
docker compose version               # Ver la versión instalada
docker compose config                # Validar y mostrar configuración final del docker-compose.yml
```

## 🔹 Ciclo de vida de los servicios
```bash
docker compose up                    # Levanta los servicios definidos (en foreground)
docker compose up -d                 # Levanta los servicios en segundo plano (detached)
docker compose down                  # Detiene y elimina contenedores, redes y volúmenes anónimos
docker compose down -v               # Igual que down, pero elimina volúmenes persistentes
```

## 🔹 Gestión de servicios
```bash
docker compose start                 # Inicia contenedores ya creados
docker compose stop                  # Detiene contenedores sin borrarlos
docker compose restart               # Reinicia servicios
docker compose ps                    # Lista los servicios corriendo con su estado
docker compose logs                  # Muestra logs de todos los servicios
docker compose logs -f               # Logs en tiempo real (follow)
```

## 🔹 Escalado
```bash
docker compose up -d --scale web=3   # Escalar servicio "web" a 3 contenedores
```

## 🔹 Ejecución dentro de contenedores
```bash
docker compose exec db bash          # Ejecuta un shell dentro del servicio "db"
docker compose exec app ls -la       # Ejecuta un comando dentro del servicio "app"
```

## 🔹 Construcción y reconstrucción
```bash
docker compose build                 # Construye servicios definidos en el archivo
docker compose build --no-cache      # Fuerza la reconstrucción sin usar cache
docker compose up --build            # Construye y levanta en un solo paso
```

## 🔹 Limpieza
```bash
docker compose rm                    # Elimina contenedores detenidos
docker compose down --rmi all        # Elimina también imágenes asociadas
```
