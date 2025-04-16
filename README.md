Flask_-APi_COVID_19_Data
Este proyecto es una API desarrollada con Flask que proporciona datos sobre el COVID-19. Utiliza JSON Web Tokens (JWT) para la autenticación de usuarios y permite obtener información detallada sobre el estado global de la pandemia.

Requisitos
Python 3.7 o superior

Flask

requests

PyJWT

Una base de datos SQL (como MySQL o PostgreSQL) para la gestión de usuarios

Instalación
Clona el repositorio:

git clone https://github.com/tu_usuario/Flask_-APi_COVID_19_Data.git
cd Flask_-APi_COVID_19_Data
Crea un entorno virtual y actívalo:
python3 -m venv venv
source venv/bin/activate  # Para Linux/MacOS
venv\Scripts\activate     # Para Windows
Instala las dependencias:
pip install -r requirements.txt
Configura tu base de datos y asegura que la tabla usuarios tenga los campos email y password.

Ejecuta la aplicación:

python app.py
Endpoints
1. /login [GET]
Genera un token JWT para los usuarios válidos.

Cuerpo de la solicitud:

json
Copiar
Editar
{
  "username": "usuario@example.com",
  "password": "contraseña"
}
Respuesta:

json

{
  "token": "JWT_TOKEN",
  "status": "Inicio de sesión exitoso.",
  "message": "Token generado exitosamente.",
  "expiration_time": "2025-04-20 12:30:00"
}
2. /get_covid_data [GET]
Obtiene los datos sobre el COVID-19.

Parámetros de solicitud:

token: El token JWT generado previamente.

Respuesta:

json

{
  "Resume_Covid_19": {
    "casos": "10,000,000",
    "recuperados": "9,500,000",
    "fallecidos": "500,000",
    "casosHoy": "1,000",
    "fallecidosHoy": "50",
    ...
  },
  "status": true,
  "message": "Datos del resumen del COVID-19 obtenidos exitosamente."
}
