# PRACTICAL: Setting Up CI/CD with Jenkins

## Aim

To set up Jenkins for a Flask application and create a CI/CD pipeline for:

**GitHub → Jenkins → Build → Test → Deploy**

# Part A: Install Jenkins

## Step 1: Check Java

Open Command Prompt:

```bash
java -version
```

Expected:

```text
java version "17..."
```

## Step 2: Check Git

```bash
git --version
```

Expected:

```text
git version 2.x.x
```

## Step 3: Install Jenkins

1. Download and install Jenkins for Windows.
2. Keep the default installation settings.
3. Use port **8080**.
4. Complete the installation.

## Step 4: Open Jenkins

Open:

```text
http://localhost:8080
```

When **Unlock Jenkins** appears, open:

```text
C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword
```

Copy the password and enter it in Jenkins.

## Step 5: Install Plugins

Select:

**Install suggested plugins**

Create the Jenkins administrator account.

The Jenkins dashboard will open.

# Part B: Create Flask Application

Create a folder:

```text
flask-jenkins-demo
```

Create these files:

```text
flask-jenkins-demo/
│
├── app.py
├── requirements.txt
└── test_app.py
```

## app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from Flask CI/CD!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

## requirements.txt

```text
Flask
pytest
```

## test_app.py

```python
from app import app

def test_home():
    client = app.test_client()
    response = client.get("/")

    assert response.status_code == 200
    assert response.data == b"Hello from Flask CI/CD!"
```

# Part C: Test the Application

Open the VS Code terminal.

Create virtual environment:

```bash
python -m venv venv
```

Activate:

```bash
venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Run the test:

```bash
pytest
```

Expected:

```text
1 passed
```

Run Flask:

```bash
python app.py
```

Open:

```text
http://localhost:5000
```

Output:

```text
Hello from Flask CI/CD!
```

# Part D: Upload Project to GitHub

Create a GitHub repository named:

```text
flask-jenkins-demo
```

In the VS Code terminal:

```bash
git init
```

```bash
git add .
```

```bash
git commit -m "Initial Flask application"
```

```bash
git branch -M main
```

```bash
git remote add origin YOUR_GITHUB_REPOSITORY_URL
```

```bash
git push -u origin main
```

The GitHub repository should contain:

```text
app.py
requirements.txt
test_app.py
```

# Part E: Create Jenkins Pipeline

Go to:

**Jenkins Dashboard → New Item**

Enter:

```text
Flask-CI-CD
```

Select:

**Pipeline**

Click **OK**.

## Configure Pipeline

Scroll to **Pipeline**.

Select:

**Pipeline script**

Enter:

```groovy
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'YOUR_GITHUB_REPOSITORY_URL'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'python -m pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                bat 'pytest'
            }
        }

        stage('Deploy') {
            steps {
                bat 'echo Application deployed successfully'
            }
        }
    }
}
```

Replace:

```text
YOUR_GITHUB_REPOSITORY_URL
```

with your GitHub repository URL.

Click **Save**.

# Part F: Run Jenkins Pipeline

Open:

**Jenkins → Flask-CI-CD**

Click:

**Build Now**

Open the build number and select:

**Console Output**

Expected output includes:

```text
[Pipeline] { (Checkout)

[Pipeline] { (Install Dependencies)

[Pipeline] { (Test)

1 passed

[Pipeline] { (Deploy)

Application deployed successfully

Finished: SUCCESS
```

## Result

Jenkins was successfully installed and configured. A Flask project was connected with GitHub, and a Jenkins CI/CD pipeline successfully performed checkout, dependency installation, testing, and deployment.
