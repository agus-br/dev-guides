# 📖 Guía de Estandarización de Logs Estructurados (v0.1)

Esta guía define un **formato estandarizado de registros de logs** para garantizar consistencia, trazabilidad y facilidad de análisis en distintos entornos, servicios y herramientas de observabilidad.

--- 
## 📑 Tabla de Contenido
- [📖 Guía de Estandarización de Logs Estructurados (v0.1)](#-guía-de-estandarización-de-logs-estructurados-v01)
  - [📑 Tabla de Contenido](#-tabla-de-contenido)
  - [🗂️ Campos Estándar](#️-campos-estándar)
    - [1. `timestamp`](#1-timestamp)
      - [Timestamp ISO 8601](#timestamp-iso-8601)
      - [Timestamp Unix](#timestamp-unix)
    - [2. `env`](#2-env)
    - [3. `severity`](#3-severity)
      - [Valores estándar (basado en Python Logging):](#valores-estándar-basado-en-python-logging)
      - [Valores según Syslog:](#valores-según-syslog)
    - [4. `service`](#4-service)
    - [5. `module`](#5-module)
    - [6. `function`](#6-function)
    - [7. `message`](#7-message)
    - [8. `metadata`](#8-metadata)
      - [📌 Categorías sugeridas dentro de `metadata`](#-categorías-sugeridas-dentro-de-metadata)
        - [🔹 http](#-http)
        - [🔹 db](#-db)
        - [🔹 user](#-user)
        - [🔹 error](#-error)
        - [🔹 system](#-system)
        - [🔹 custom](#-custom)
  - [🏷️ Estructura General del Log](#️-estructura-general-del-log)
    - [📌 Buenas Prácticas](#-buenas-prácticas)
    - [📓 Notas importantes](#-notas-importantes)



---

## 🗂️ Campos Estándar

### 1. `timestamp`
Hay dos buenas opciones para almacenar fechas, según tus necesidades podrías com: 
#### Timestamp ISO 8601
  - **Formato**: ISO 8601 con milisegundos y en UTC.  
  - **Ejemplo**: `"2025-08-15T09:30:45.123Z"`  

#### Timestamp Unix
- **Formato**: Epoch time en segundos con milisegundos como decimal.  
- **Ejemplo**: `1755240645.123`  

---

### 2. `env`
- **Entorno de ejecución**.  
- **Valores posibles**:
  - `"production"`
  - `"staging"`
  - `"development"`
  - `"test"`

---

### 3. `severity`
Existen dos formatos principales para indicar la severidad de un evento:
#### Valores estándar (basado en Python Logging):
  | Nivel      | ¿Cuando se usa?                                                                                                                                                                        |
  | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
  | `DEBUG`    | Información detallada, típicamente de interés sólo durante el diagnóstico de problemas.                                                                                                |
  | `INFO`     | Confirmación de que las cosas están funcionando como se esperaba.                                                                                                                      |
  | `WARNING`  | Un indicio de que algo inesperado sucedió, o indicativo de algún problema en el futuro cercano (por ejemplo, «espacio de disco bajo»). El software sigue funcionando como se esperaba. |
  | `ERROR`    | Debido a un problema más grave, el software no ha sido capaz de realizar alguna función.                                                                                               |
  | `CRITICAL` | Un grave error, que indica que el programa en sí mismo puede ser incapaz de seguir funcionando.                                                                                        |

#### Valores según Syslog:
  | Nivel de Severidad | Descripción de Severidad                         |
  | ------------------ | ------------------------------------------------ |
  | 0                  | EMERGENCIA - El sistema es inutilizable          |
  | 1                  | ALERTA - Se debe actuar de inmediato             |
  | 2                  | CRÍTICO - Condiciones críticas                   |
  | 3                  | ERROR - Condiciones de error                     |
  | 4                  | ADVERTENCIA - Condiciones de advertencia         |
  | 5                  | AVISO - Condiciones normales pero significativas |
  | 6                  | INFORMATIVO - Mensajes informativos              |
  | 7                  | DEPURACIÓN - Mensajes de nivel de depuración     |

---

### 4. `service`
- **Nombre del servicio, microservicio o aplicación.**  
- **Ejemplo**: `"SiceNet"`, `"SiceCore"`, `"SiceDroid"`

---

### 5. `module`
- **Nombre del archivo o módulo donde ocurrió el log.**  

---

### 6. `function`
- **Nombre exacto de la función o método que generó el log.**  

---

### 7. `message`
- **Mensaje descriptivo del evento o error.**  
- No incluir stacktrace aquí.  
- Si aplica, usar el campo `stacktrace`.  

---

### 8. `metadata`
**Descripción:** Objeto que contiene datos adicionales relevantes para diagnóstico y contexto.  

**Estructura recomendada:** Subobjetos temáticos (`http`, `db`, `user`, `error`, `system`, `custom`, etc.).  

---

#### 📌 Categorías sugeridas dentro de `metadata`

---

##### 🔹 http  
Información relacionada con peticiones HTTP.

```json
"http": {
  "method": "POST",
  "url": "/api/v1/login",
  "status_code": 401,
  "payload_size": 512,
  "response_time_ms": 123
}
```

---

##### 🔹 db  
Eventos relacionados con base de datos.

```json
"db": {
  "query": "SELECT * FROM users WHERE id = ?",
  "duration_ms": 45,
  "rows_returned": 1,
  "error": "timeout"
}
```

---

##### 🔹 user
Datos relacionados con el usuario afectado.

```json
"user": {
  "id": "12345",
  "role": "admin",
  "ip": "192.168.1.100"
}
```

---

##### 🔹 error
Detalles de errores y excepciones.

```json
"error": {
  "code": "AUTH_INVALID_CREDENTIALS",
  "stacktrace": "...stacktrace aquí..."
}
```

---

##### 🔹 system
Contexto del sistema o infraestructura. 

```json
"error": {
  "code": "AUTH_INVALID_CREDENTIALS",
  "stacktrace": "...stacktrace aquí..."
}
```

---

##### 🔹 custom
Datos adicionales específicos del negocio o la aplicación. 

```json
"custom": {
  "transaction_id": "TX-20250815-ABC123",
  "feature_flag": "beta_login_flow"
}
```

---

## 🏷️ Estructura General del Log

```json
{
  "timestamp": "2025-08-15T09:30:45.123Z",
  "timestamp_unix": 1755240645.123,
  "env": "production",
  "severity": "ERROR",
  "service": "SiceNet",
  "module": "auth_controller.py",
  "function": "login_user",
  "message": "Error de autenticación",
  "metadata": {
    "http": {
      "method": "POST",
      "url": "/api/v1/login",
      "status_code": 401,
      "payload_size": 512
    },
    "user": {
      "id": "12345",
      "role": "admin"
    },
    "error": {
      "code": "AUTH_INVALID_CREDENTIALS",
      "stacktrace": "...stacktrace aquí..."
    }
  }
}
```

---

### 📌 Buenas Prácticas

- **Consistencia:** Todos los servicios deben seguir esta misma estructura.
- **Estandarización:** metadata debe contener categorías predefinidas.
- **Evitar duplicidad:** No repetir información ya cubierta en campos principales.
- **Sensibilidad:** Nunca registrar contraseñas, tokens ni datos confidenciales.
- **Legibilidad + Maquinabilidad:** Los logs deben ser fáciles de leer y procesar.
- **Flexibilidad:**
    - metadata siempre debe existir como objeto.
    - Los subcampos no son obligatorios; solo se incluyen si son relevantes.
    - Evitar campos vacíos o redundantes.

---

### 📓 Notas importantes

👉 La idea es que metadata funcione como un contenedor extensible y adaptable.
Ejemplo:
- Un evento de autenticación puede incluir http y user.
- Un fallo de base de datos puede incluir db y error.