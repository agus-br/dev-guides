# 📘 Guía: Montar un contenedor de SQL Server con Docker y restaurar un backup (v1.0)

Esta guía explica cómo levantar un contenedor de SQL Server en Docker, copiar un backup dentro del contenedor y restaurar una base de datos.

---

- [📘 Guía: Montar un contenedor de SQL Server con Docker y restaurar un backup (v1.0)](#-guía-montar-un-contenedor-de-sql-server-con-docker-y-restaurar-un-backup-v10)
  - [1️⃣ Descargar la imagen oficial de SQL Server](#1️⃣-descargar-la-imagen-oficial-de-sql-server)
  - [2️⃣ Levantar el contenedor](#2️⃣-levantar-el-contenedor)
      - [2.1 Sin volumen (No tan recomendado, pero funciona para probar)](#21-sin-volumen-no-tan-recomendado-pero-funciona-para-probar)
      - [2.2 Con volumen (autogestionado por docker)](#22-con-volumen-autogestionado-por-docker)
  - [3️⃣ Copiar el backup dentro del contenedor](#3️⃣-copiar-el-backup-dentro-del-contenedor)
  - [4️⃣ Conectarse al contenedor](#4️⃣-conectarse-al-contenedor)
  - [5️⃣ Ver los archivos dentro del backup](#5️⃣-ver-los-archivos-dentro-del-backup)
  - [6️⃣ Restaurar la base de datos](#6️⃣-restaurar-la-base-de-datos)
  - [7️⃣ Verificar la base de datos restaurada](#7️⃣-verificar-la-base-de-datos-restaurada)


---

## 1️⃣ Descargar la imagen oficial de SQL Server
```bash
docker pull mcr.microsoft.com/mssql/server
```

---

## 2️⃣ Levantar el contenedor

Puedes hacerlo con volumen (persistencia de datos) o sin volumen (datos se borran al eliminar el contenedor).
Usaremos `sql_server_container` como nombre de nuestro contenedor.

#### 2.1 Sin volumen (No tan recomendado, pero funciona para probar)
Sustituye los datos como: Contraseña, puertos y el nombre del contenedor.
```bash
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Tu_contraseña" -p PUERTO_HOST:PUERTO_CONTENEDOR --name sql_server_container -d mcr.microsoft.com/mssql/server
```

#### 2.2 Con volumen (autogestionado por docker)
```bash
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Tu_contraseña" -p PUERTO_HOST:PUERTO_CONTENEDOR  -v sqlserver_data:/var/opt/mssql/data --name sql_server_container -d mcr.microsoft.com/mssql/server
```

- 🔑 Nota: la contraseña debe cumplir con las políticas de SQL Server (mínimo 8 caracteres, mayúscula, minúscula, número y símbolo).

---

## 3️⃣ Copiar el backup dentro del contenedor

Detén el contenedor:
```bash
docker stop sql_server_container
```

Copia el archivo .bak desde tu máquina a la carpeta de datos de SQL Server en el contenedor (preferentemente una ruta de un usuario, como en `\Escritorio`, rutas como C:\windows requiere otorgar permisos):
```bash
docker cp C:\RUTA\backup.bak sql_server_container:/var/opt/mssql/data/
```

Vuelve a correr el contenedor:
```bash
docker start sql_server_container
```


---

## 4️⃣ Conectarse al contenedor

Aquí hay dos opciones: 
1. Conectarse mediante [SQL Server Management Studio](https://learn.microsoft.com/en-us/ssms/install/install) (Recomendado si es para desarrollo). 
Con las credenciales definidas y al `localhost,Puerto` del host ligado al puerto del contenedor (Ojo! solo si se prueba en el mismo host, si no sería una `IP,PORT`) [Ver paso 2️⃣](#2️⃣-levantar-el-contenedor) 
1. Instalar [sqlcmd](https://learn.microsoft.com/es-es/sql/tools/sqlcmd/sqlcmd-download-install?view=sql-server-ver17&tabs=windows) en tu host (Windows / Linux / WSL) y probar una conexión remota al servidor:
```bash
sqlcmd -S IP,PORT -U SA -P "TuContraseña"
```

## 5️⃣ Ver los archivos dentro del backup

Antes de restaurar, revisa cómo se llaman los archivos lógicos dentro del .bak:

```sql
RESTORE FILELISTONLY 
FROM DISK = '/var/opt/mssql/data/bakdb.bak';
GO
```

Esto devuelve algo como:
- DB_NAME → archivo de datos (.mdf)
- DB_NAME_log → archivo de logs (.ldf)

---

## 6️⃣ Restaurar la base de datos

Con la información anterior, ejecuta:

```sql
RESTORE DATABASE DB_NAME
FROM DISK = '/var/opt/mssql/data/backup.bak'
WITH 
   MOVE 'DB_NAME' TO '/var/opt/mssql/data/DB_NAME.mdf',
   MOVE 'DB_NAME_log' TO '/var/opt/mssql/data/DB_NAME_log.ldf',
   REPLACE;
GO
```

---

## 7️⃣ Verificar la base de datos restaurada

Ejecuta en `sqlcmd` o en un archvio de consultas en `SQL Server Management Studio`:
```sql
SELECT name FROM sys.databases;
GO
```
Si aparece **DB_NAME (el nombre de tu base de datos pues)**, ¡todo salió bien! 🎉 
Si no, levántame un issue😅