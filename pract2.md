# Practical 2: Building a REST API with Flask

## Aim

To build a REST API using Flask with CRUD operations for a **To-Do List** application and test the API using **curl**.

---

# Step 1: Check Python Installation

Open **Command Prompt (CMD)** and run:

```cmd
python --version
```

### Output

```text
Python 3.12.4
```

---

# Step 2: Create a Project Folder

Create a folder named:

```text
Flask_Project
```

Open Command Prompt and navigate to the folder:

```cmd
cd path\to\Flask_Project
```

Example:

```cmd
cd C:\Users\Nikhita\Documents\Flask_Project
```

---

# Step 3: Create a Virtual Environment

Run:

```cmd
python -m venv venv
```

Activate it:

```cmd
venv\Scripts\activate
```

### Output

```text
(venv) C:\Users\Nikhita\Documents\Flask_Project>
```

---

# Step 4: Install Flask

Run:

```cmd
pip install flask
```

Flask will be installed successfully.

---

# Step 5: Verify Flask Installation

Run:

```cmd
python -m flask --version
```

### Output

```text
Flask 3.x.x
Werkzeug 3.x.x
```

---

# Step 6: Create Flask REST API

Create a file named:

```text
app.py
```

Write the following code:

```python
from flask import Flask, jsonify, request

app = Flask(__name__)

# Sample data storage
todos = [
    {
        "id": 1,
        "title": "Learn Flask",
        "completed": False
    }
]

# CREATE a new task
@app.route('/todos', methods=['POST'])
def create_todo():
    data = request.get_json()

    new_todo = {
        "id": len(todos) + 1,
        "title": data['title'],
        "completed": False
    }

    todos.append(new_todo)
    return jsonify(new_todo), 201


# READ all tasks
@app.route('/todos', methods=['GET'])
def get_todos():
    return jsonify(todos)


# READ a single task
@app.route('/todos/<int:id>', methods=['GET'])
def get_todo(id):
    todo = next((t for t in todos if t['id'] == id), None)

    if todo is None:
        return jsonify({"message": "Task not found"}), 404

    return jsonify(todo)


# UPDATE a task
@app.route('/todos/<int:id>', methods=['PUT'])
def update_todo(id):
    todo = next((t for t in todos if t['id'] == id), None)

    if todo is None:
        return jsonify({"message": "Task not found"}), 404

    data = request.get_json()

    todo['title'] = data.get('title', todo['title'])
    todo['completed'] = data.get('completed', todo['completed'])

    return jsonify(todo)


# DELETE a task
@app.route('/todos/<int:id>', methods=['DELETE'])
def delete_todo(id):
    global todos

    todo = next((t for t in todos if t['id'] == id), None)

    if todo is None:
        return jsonify({"message": "Task not found"}), 404

    todos = [t for t in todos if t['id'] != id]

    return jsonify({"message": "Task deleted successfully"})


if __name__ == '__main__':
    app.run(debug=True)
```

---

# Step 7: Run the Flask Application

Run:

```cmd
python app.py
```

### Output

```text
* Running on http://127.0.0.1:5000
```

Keep this Command Prompt running.

---

# Step 8: Test CRUD Operations Using curl

Open another **Command Prompt**.

## 1. GET All Tasks

Run:

```cmd
curl http://127.0.0.1:5000/todos
```

### Output

```json
[
  {
    "completed": false,
    "id": 1,
    "title": "Learn Flask"
  }
]
```

---

## 2. POST - Create a Task

Run:

```cmd
curl -X POST http://127.0.0.1:5000/todos -H "Content-Type: application/json" -d "{\"title\":\"Complete REST API Assignment\"}"
```

### Output

```json
{
  "completed": false,
  "id": 2,
  "title": "Complete REST API Assignment"
}
```

---

## 3. PUT - Update a Task

Run:

```cmd
curl -X PUT http://127.0.0.1:5000/todos/1 -H "Content-Type: application/json" -d "{\"title\":\"Learn Flask API\",\"completed\":true}"
```

### Output

```json
{
  "completed": true,
  "id": 1,
  "title": "Learn Flask API"
}
```

---

## 4. DELETE - Delete a Task

Run:

```cmd
curl -X DELETE http://127.0.0.1:5000/todos/1
```

### Output

```json
{
  "message": "Task deleted successfully"
}
```

---

## 5. Verify Deletion

Run:

```cmd
curl http://127.0.0.1:5000/todos
```

### Output

```json
[
  {
    "completed": false,
    "id": 2,
    "title": "Complete REST API Assignment"
  }
]
```

---

# Final Result

The REST API for the **To-Do List application** was successfully created using Flask.

The CRUD operations performed were:

| Method | Endpoint     | Operation      |
| ------ | ------------ | -------------- |
| GET    | `/todos`   | Read all tasks |
| GET    | `/todos/1` | Read one task  |
| POST   | `/todos`   | Create a task  |
| PUT    | `/todos/1` | Update a task  |
| DELETE | `/todos/1` | Delete a task  |
