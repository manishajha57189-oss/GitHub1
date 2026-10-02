# Part A: Dockerize Each Component and Use Docker Compose

## Aim

To Dockerize the React frontend, Flask backend and PostgreSQL database and run them using Docker Compose.

## Step 1: Create Project Folder

Open CMD:

```cmd
cd %USERPROFILE%\Desktop
mkdir three-tier-docker
cd three-tier-docker
mkdir backend
mkdir frontend
mkdir database
```

Structure:

```text
three-tier-docker/
├── backend/
├── frontend/
├── database/
└── docker-compose.yml
```

---

## Step 2: Create Flask Backend

Go to backend:

```cmd
cd backend
```

Create `app.py`:

```python
from flask import Flask, jsonify
import psycopg2
import os

app = Flask(__name__)

@app.route("/")
def home():
    return "Flask Backend is Running"

@app.route("/students")
def students():
    try:
        conn = psycopg2.connect(
            host=os.getenv("DB_HOST"),
            database=os.getenv("DB_NAME"),
            user=os.getenv("DB_USER"),
            password=os.getenv("DB_PASSWORD")
        )

        cur = conn.cursor()
        cur.execute("SELECT id, name, email, course FROM students ORDER BY id")
        rows = cur.fetchall()

        cur.close()
        conn.close()

        result = []

        for row in rows:
            result.append({
                "id": row[0],
                "name": row[1],
                "email": row[2],
                "course": row[3]
            })

        return jsonify(result)

    except Exception as e:
        return jsonify({"error": str(e)}), 500

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

## Step 3: Create requirements.txt

Inside `backend`, create `requirements.txt`:

```text
Flask
psycopg2-binary
flask-cors
```

---

## Step 4: Create Backend Dockerfile

Create `Dockerfile` inside `backend`:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

---

## Step 5: Create React Frontend

Go back to the main folder:

```cmd
cd ..
cd frontend
```

Create React application:

```cmd
npm create vite@latest . -- --template react
```

Type:

```text
y
```

Install packages:

```cmd
npm install
npm install axios
```

---

## Step 6: Modify React App

Open:

```text
frontend/src/App.jsx
```

Replace the code with:

```jsx
import { useEffect, useState } from "react";
import axios from "axios";

function App() {
  const [students, setStudents] = useState([]);

  useEffect(() => {
    axios
      .get("http://localhost:5000/students")
      .then((response) => {
        setStudents(response.data);
      })
      .catch((error) => {
        console.error(error);
      });
  }, []);

  return (
    <div>
      <h1>Student Management System</h1>

      <table border="1" cellPadding="10">
        <thead>
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Email</th>
            <th>Course</th>
          </tr>
        </thead>

        <tbody>
          {students.map((student) => (
            <tr key={student.id}>
              <td>{student.id}</td>
              <td>{student.name}</td>
              <td>{student.email}</td>
              <td>{student.course}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}

export default App;
```

---

## Step 7: Create Frontend Dockerfile

Inside `frontend`, create `Dockerfile`:

```dockerfile
FROM node:20

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0"]
```

---

## Step 8: Configure PostgreSQL

Inside the `database` folder, create `init.sql`:

```sql
CREATE TABLE IF NOT EXISTS students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL,
    course VARCHAR(100) NOT NULL
);

INSERT INTO students (name, email, course)
VALUES
('Rahul', 'rahul@gmail.com', 'BCA'),
('Priya', 'priya@gmail.com', 'BSc CS'),
('Amit', 'amit@gmail.com', 'BCA');
```

---

## Step 9: Create docker-compose.yml

Go back to the main folder:

```cmd
cd ..
```

Create `docker-compose.yml`:

```yaml
services:

  frontend:
    build: ./frontend
    container_name: student_frontend
    ports:
      - "5173:5173"
    depends_on:
      - backend

  backend:
    build: ./backend
    container_name: student_backend
    ports:
      - "5000:5000"
    environment:
      DB_HOST: database
      DB_NAME: studentdb
      DB_USER: postgres
      DB_PASSWORD: postgres
    depends_on:
      - database

  database:
    image: postgres:16
    container_name: student_database
    restart: always
    environment:
      POSTGRES_DB: studentdb
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./database/init.sql:/docker-entrypoint-initdb.d/init.sql

volumes:
  postgres_data:
```

---

## Step 10: Build Containers

Open terminal in `three-tier-docker` and run:

```cmd
docker compose build
```

---

## Step 11: Start Application

```cmd
docker compose up -d
```

---

## Step 12: Check Containers

```cmd
docker ps
```

You should see:

```text
student_frontend
student_backend
student_database
```

All should show **Up** status.

---

## Step 13: Test Backend

Open:

```text
http://localhost:5000/
```

Output:

```text
Flask Backend is Running
```

Then open:

```text
http://localhost:5000/students
```

Student records should be displayed.

---

## Step 14: Test Frontend

Open:

```text
http://localhost:5173
```

Expected output:

```text
Student Management System

ID    Name     Email              Course

1     Rahul    rahul@gmail.com    BCA
2     Priya    priya@gmail.com    BSc CS
3     Amit     amit@gmail.com     BCA
```

---

## Result

The React frontend, Flask backend and PostgreSQL database were successfully Dockerized and run using Docker Compose.
