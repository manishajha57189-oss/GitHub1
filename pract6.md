# PRACTICAL 6: Docker Compose for Multi-Container Applications

## Aim

To create and run a multi-container application using Docker Compose with:

* Flask Application
* PostgreSQL Database

## Requirements

* Docker Desktop
* VS Code
* Python 3.x
* Internet connection

## Project Structure

```text
FlaskComposeProject/
│
├── app.py
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
└── .env
```

# Step 1: Create Flask Application

Create `app.py`:

```python
from flask import Flask
import psycopg2
import os

app = Flask(__name__)

@app.route('/')
def home():
    try:
        conn = psycopg2.connect(
            host=os.getenv("DB_HOST"),
            database=os.getenv("DB_NAME"),
            user=os.getenv("DB_USER"),
            password=os.getenv("DB_PASSWORD")
        )
        conn.close()
        return "Connected Successfully to PostgreSQL!"
    except Exception as e:
        return "Database Connection Failed: " + str(e)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

# Step 2: Create requirements.txt

```text
Flask
psycopg2-binary
```

# Step 3: Create Dockerfile

Create a file named `Dockerfile`:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

# Step 4: Create .env File

```text
DB_HOST=db
DB_NAME=mydatabase
DB_USER=postgres
DB_PASSWORD=postgres
```

# Step 5: Create docker-compose.yml

```yaml
services:
  web:
    build: .
    container_name: flask_app
    ports:
      - "5000:5000"
    depends_on:
      - db
    environment:
      DB_HOST: db
      DB_NAME: mydatabase
      DB_USER: postgres
      DB_PASSWORD: postgres

  db:
    image: postgres:16
    container_name: postgres_db
    restart: always
    environment:
      POSTGRES_DB: mydatabase
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

# Step 6: Open Terminal

Go to the project folder:

```bash
cd FlaskComposeProject
```

# Step 7: Build the Containers

```bash
docker compose build
```

Docker builds the Flask application image.

# Step 8: Start the Containers

```bash
docker compose up -d
```

Expected output:

```text
Creating network "flaskcomposeproject_default"
Creating volume "postgres_data"
Creating postgres_db
Creating flask_app
```

# Step 9: Verify Running Containers

```bash
docker ps
```

Expected output:

```text
CONTAINER ID    IMAGE          STATUS
xxxxxxxx       flask_app      Up
xxxxxxxx       postgres:16    Up
```

Both containers should be running.

# Step 10: Test Communication Between Services

Open a browser and visit:

```text
http://localhost:5000
```

Expected output:

```text
Connected Successfully to PostgreSQL!
```

This confirms that the Flask application successfully connected to PostgreSQL.

# Step 11: Stop the Application

```bash
docker compose down
```

Expected output:

```text
Stopping flask_app
Stopping postgres_db
Removing network
```

## Result

The Flask and PostgreSQL containers were successfully created and run using Docker Compose, and communication between the two services was successfully tested.
