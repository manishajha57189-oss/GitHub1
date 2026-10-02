# Practical: Three-Tier Student Management Application

## Aim

To create a three-tier Student Management Application using React, Flask and PostgreSQL.

## Software Required

* Python
* Node.js
* React
* Flask
* PostgreSQL
* pgAdmin
* VS Code

---

## Step 1: Check Installation

Open CMD and run:

```cmd
python --version
node --version
npm --version
"C:\Program Files\PostgreSQL\18\bin\psql.exe" --version
```

Check PostgreSQL port:

```cmd
netstat -ano | findstr :5433
```

Use PostgreSQL on:

```text
localhost:5433
```

---

## Step 2: Create Project Folder

```cmd
cd %USERPROFILE%\Desktop
mkdir three-tier-student-app
cd three-tier-student-app
mkdir backend
mkdir frontend
```

---

## Step 3: Create PostgreSQL Database

Open **pgAdmin**.

Go to:

```text
Servers → PostgreSQL 18 → Databases
```

Right-click **Databases → Create → Database**

Database name:

```text
studentdb2
```

Click **Save**.

---

## Step 4: Create Students Table

Open:

```text
studentdb2 → Query Tool
```

Run:

```sql
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL,
    course VARCHAR(100) NOT NULL
);
```

Insert sample data:

```sql
INSERT INTO students (name, email, course)
VALUES
('Rahul', 'rahul@gmail.com', 'BCA'),
('Priya', 'priya@gmail.com', 'BSc CS'),
('Amit', 'amit@gmail.com', 'BCA');
```

---

# Step 5: Create Flask Backend

Open CMD:

```cmd
cd %USERPROFILE%\Desktop\three-tier-student-app
cd backend
```

Create virtual environment:

```cmd
python -m venv venv
```

Activate:

```cmd
venv\Scripts\activate
```

Install packages:

```cmd
python -m pip install flask flask-cors "psycopg[binary]"
```

---

# Step 6: Create app.py

Open the project in VS Code:

```cmd
code .
```

Create:

```text
backend
└── app.py
```

Add:

```python
from flask import Flask, jsonify, request
from flask_cors import CORS
import psycopg

app = Flask(__name__)
CORS(app)

DB_CONFIG = {
    "host": "localhost",
    "port": 5433,
    "dbname": "studentdb2",
    "user": "postgres",
    "password": "YOUR_POSTGRES_PASSWORD"
}

def get_connection():
    return psycopg.connect(**DB_CONFIG)

@app.route("/")
def home():
    return jsonify({
        "message": "Student Management API is running"
    })

@app.route("/students", methods=["GET"])
def get_students():
    conn = get_connection()
    cur = conn.cursor()

    cur.execute(
        "SELECT id, name, email, course FROM students ORDER BY id"
    )

    students = cur.fetchall()
    cur.close()
    conn.close()

    result = []

    for student in students:
        result.append({
            "id": student[0],
            "name": student[1],
            "email": student[2],
            "course": student[3]
        })

    return jsonify(result)

@app.route("/students", methods=["POST"])
def add_student():
    data = request.get_json()

    name = data.get("name")
    email = data.get("email")
    course = data.get("course")

    conn = get_connection()
    cur = conn.cursor()

    cur.execute(
        """
        INSERT INTO students (name, email, course)
        VALUES (%s, %s, %s)
        RETURNING id
        """,
        (name, email, course)
    )

    student_id = cur.fetchone()[0]
    conn.commit()

    cur.close()
    conn.close()

    return jsonify({
        "message": "Student added successfully",
        "id": student_id
    }), 201

if __name__ == "__main__":
    app.run(debug=True, port=5000)
```

Replace:

```python
"password": "YOUR_POSTGRES_PASSWORD"
```

with your PostgreSQL password.

---

## Step 7: Run Flask Backend

```cmd
python app.py
```

Open:

```text
http://127.0.0.1:5000/
```

Then:

```text
http://127.0.0.1:5000/students
```

Student records should be displayed.

---

# Step 8: Create React Frontend

Keep Flask running and open a **new CMD**.

```cmd
cd %USERPROFILE%\Desktop\three-tier-student-app
npm create vite@latest frontend -- --template react
```

Type:

```text
y
```

Then:

```cmd
cd frontend
npm install
npm install axios
```

---

# Step 9: Replace App.jsx

Open:

```text
frontend → src → App.jsx
```

Delete the existing code and add:

```jsx
import { useEffect, useState } from "react";
import axios from "axios";
import "./App.css";

function App() {
  const [students, setStudents] = useState([]);

  useEffect(() => {
    axios
      .get("http://127.0.0.1:5000/students")
      .then((response) => {
        setStudents(response.data);
      })
      .catch((error) => {
        console.error("Error fetching students:", error);
      });
  }, []);

  return (
    <div className="container">
      <h1>Student Management System</h1>

      <table>
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

# Step 10: Add CSS

Open:

```text
frontend → src → App.css
```

Replace its contents with:

```css
body {
  font-family: Arial, sans-serif;
  background: #f2f2f2;
  margin: 0;
  padding: 40px;
}

.container {
  max-width: 900px;
  margin: auto;
  background: white;
  padding: 30px;
  border-radius: 10px;
}

h1 {
  text-align: center;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 25px;
}

th, td {
  border: 1px solid #ccc;
  padding: 12px;
  text-align: left;
}

th {
  background: #eeeeee;
}
```

Save the files.

---

# Step 11: Run React

In the frontend CMD:

```cmd
npm run dev
```

Open:

```text
http://localhost:5173/
```

## Expected Output

```text
Student Management System

ID    Name     Email              Course

1     Rahul    rahul@gmail.com    BCA
2     Priya    priya@gmail.com    BSc CS
3     Amit     amit@gmail.com     BCA
```

## Result

The three-tier Student Management Application was successfully created using **React, Flask and PostgreSQL**.
