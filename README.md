# Flask API COVID-19 Data

API REST desarrollada con **Flask** que expone un resumen global de datos de COVID-19. El acceso está protegido con **JSON Web Tokens (JWT)** y los usuarios se validan contra una base de datos **MySQL**.

![Demo](docs/Data%20Covid.gif)

## Qué hace

1. **Autenticación (`/login`)**: recibe usuario y contraseña, los valida contra la tabla `usuarios` y devuelve un JWT que expira a los 5 minutos.
2. **Datos de COVID-19 (`/get_covid_data`)**: verifica el token y consulta la API pública [disease.sh](https://disease.sh/v3/covid-19/all). Devuelve un resumen global ya formateado en español (casos, recuperados, fallecidos, activos, críticos, valores por millón, pruebas, población y países afectados).
3. **Manejo de errores**: responde con mensajes claros cuando falta el token, es inválido o expiró, falla la base de datos o falla la API externa.

## Estructura

```
apiCovid19/
├── app.py             # Aplicación Flask: endpoints y manejo de JWT
├── dbConnection.py    # Conexión a MySQL (PyMySQL)
└── requirements.txt   # Dependencias
docs/                  # Demo (GIF)
zappa_settings.json    # Configuración de despliegue en AWS Lambda (Zappa)
.env                   # Variables de entorno (no se sube al repositorio)
```

## Requisitos

- Python 3.9 o superior
- MySQL con la base de datos `webdinarZappa` y la tabla `usuarios` (campos `email` y `password`)

Dependencias principales: Flask, requests, PyJWT, PyMySQL, python-dotenv.

## Instalación

```powershell
git clone https://github.com/DanielNovoa553/Flask_API_COVID_19_Data.git
cd Flask_API_COVID_19_Data\apiCovid19
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

En Linux/macOS activa el entorno con `source .venv/bin/activate`.

## Configuración

Crea un archivo `.env` en la raíz del repositorio:

```
SECRET_KEY=una-clave-larga-y-secreta
```

La conexión a la base de datos está en `apiCovid19/dbConnection.py` (por defecto `localhost`, usuario `root`, sin contraseña, base `webdinarZappa`). Ajústala a tu entorno.

Tabla mínima de usuarios:

```sql
CREATE TABLE usuarios (
  id INT AUTO_INCREMENT PRIMARY KEY,
  email VARCHAR(255) NOT NULL,
  password VARCHAR(255) NOT NULL
);
```

## Ejecución

```powershell
cd apiCovid19
python app.py
```

La API queda en `http://127.0.0.1:5000`.

## Endpoints

### `GET /login`

Genera un token JWT para un usuario válido. Requiere cuerpo JSON.

```json
{
  "username": "usuario@example.com",
  "password": "contraseña"
}
```

Respuesta:

```json
{
  "token": "JWT_TOKEN",
  "status": "Inicio de sesión exitoso.",
  "message": "Token generado exitosamente.",
  "expiration_time": "20-04-2025 12:30:00"
}
```

### `GET /get_covid_data?token=<JWT>`

Devuelve el resumen global de COVID-19.

```json
{
  "Resume_Covid_19": {
    "casos": "704,753,890",
    "recuperados": "675,619,811",
    "fallecidos": "7,010,681",
    "casosHoy": "1,000",
    "fallecidosHoy": "50"
  },
  "status": true,
  "message": "Resume data Covid 19 obtained successfully."
}
```

(Los valores son ilustrativos; la respuesta incluye todos los campos listados arriba.)

## Despliegue

El proyecto incluye `zappa_settings.json` con los entornos `dev` y `prod` para desplegar en AWS Lambda con [Zappa](https://github.com/zappa/Zappa).

## Objetivo y hoja de ruta

El objetivo es construir una API sencilla y publicable que sirva datos de COVID-19 a un front-end con visualizaciones. Pendientes:

- [ ] **Seguridad**: consultas SQL parametrizadas (hoy se arman con f-strings y son vulnerables a inyección), contraseñas con hash y credenciales de BD en variables de entorno.
- [ ] **Autenticación**: decidir si la API será pública (sin JWT, con límite de peticiones y CORS) o privada (login en el front, roles).
- [ ] **Front-end**: panel con gráficas (Chart.js, React o Vue) para visualizar la información.
- [ ] **Más datos**: consulta por país e histórico, además del resumen global.
- [ ] **Producción**: servidor WSGI (gunicorn), HTTPS, caché y límite de peticiones.
- [ ] **Calidad**: pruebas automáticas y CI.
