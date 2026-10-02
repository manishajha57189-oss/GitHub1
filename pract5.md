# PRACTICAL 5: Dockerize a Flask Application

## Aim

To create a Docker image for a Flask application using a Dockerfile.

## Software Requirements

* Windows 10/11
* Python 3.12+
* Docker Desktop
* VS Code
* Flask

# Step 1: Install and Open Docker Desktop

1. Install **Docker Desktop for Windows**.
2. Open Docker Desktop.
3. Make sure Docker Engine shows **Running**.

## Step 2: Verify Docker Installation

Open Command Prompt or PowerShell:

```bash
docker --version
```

Example output:

```text
Docker version 28.3.2, build xxxx
```

Then check:

```bash
docker info
```

## Step 3: Create Project Folder

```bash
mkdir Flask_Project
cd Flask_Project
```

## Step 4: Open Folder in VS Code

```bash
code .
```

## Step 5: Create Flask Application

Create `app.py`:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Welcome to Dockerized Flask Application"

@app.route("/about")
def about():
    return "Docker Practical"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

## Step 6: Install Flask

```bash
pip install flask
```

## Step 7: Test Flask Application

Run:

```bash
python app.py
```

Open in browser:

```text
http://localhost:5000
```

Output:

```text
Welcome to Dockerized Flask Application
```

Also test:

```text
http://localhost:5000/about
```

Output:

```text
Docker Practical
```

## Step 8: Create requirements.txt

Run:

```bash
pip freeze > requirements.txt
```

The project should contain:

```text
Flask_Project/
│
├── app.py
└── requirements.txt
```

## Step 9: Create Dockerfile

Create a file named exactly:

```text
Dockerfile
```

Write:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

## Step 10: Check Project Structure

```text
Flask_Project/
│
├── app.py
├── Dockerfile
└── requirements.txt
```

## Step 11: Build Docker Image

Make sure the terminal is inside the `Flask_Project` folder.

Run:

```bash
docker build -t flask-app .
```

Expected output:

```text
Successfully built 8b1234567890
Successfully tagged flask-app:latest
```

## Step 12: Verify Docker Image

Run:

```bash
docker images
```

Expected output:

```text
REPOSITORY    TAG       IMAGE ID        SIZE
flask-app     latest    8b1234567890    170 MB
```

## Result

The Flask application was successfully Dockerized and the Docker image `flask-app` was successfully created.
