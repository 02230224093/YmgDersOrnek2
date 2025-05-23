pyhton flask için de  
app.py requirements.txt Dockerfile ve docker-compose.yml dosyaları açman gerekir.

docker-compose.yml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/app
    depends_on:
      - db
    environment:
      - DB_HOST=db
      - DB_PORT=5432
      - DB_NAME=flask_db
      - DB_USER=flask_user
      - DB_PASSWORD=flask_pass
    restart: unless-stopped

  db:
    image: postgres:15
    environment:
      POSTGRES_DB: flask_db
      POSTGRES_USER: flask_user
      POSTGRES_PASSWORD: flask_pass
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:

requirements.txt 
flask
psycopg2-binary

app.py
from flask import Flask
import psycopg2
import os

app = Flask(__name__)

# Veritabanı bağlantı bilgileri
DB_HOST = os.environ.get("DB_HOST", "localhost")
DB_PORT = os.environ.get("DB_PORT", "5432")
DB_NAME = os.environ.get("DB_NAME", "flask_db")
DB_USER = os.environ.get("DB_USER", "flask_user")
DB_PASSWORD = os.environ.get("DB_PASSWORD", "flask_pass")

@app.route("/")
def hello():
    try:
        conn = psycopg2.connect(
            host=DB_HOST,
            port=DB_PORT,
            dbname=DB_NAME,
            user=DB_USER,
            password=DB_PASSWORD
        )
        cursor = conn.cursor()
        cursor.execute("SELECT version();")
        version = cursor.fetchone()
        conn.close()
        return f"Connected to PostgreSQL!<br>Version: {version[0]}"
    except Exception as e:
        return f"Database connection failed: {str(e)}"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)


# Proje klasörüne gir
cd flask-docker-app

# Docker image oluştur ve çalıştır
docker-compose up –build


