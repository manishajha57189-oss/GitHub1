# PRACTICAL 4: Creating and Managing a Virtual Machine

## Aim

To create and manage a Virtual Machine using VMware, configure networking, and deploy a Flask application manually on Ubuntu.

## Software Requirements

* VMware Workstation
* Ubuntu
* Python 3
* Flask
* Terminal
* Web Browser

# Part (a): Set Up a Virtual Machine Using VMware

## Step 1: Open VMware Workstation

1. Open **VMware Workstation**.
2. Select the **Ubuntu Virtual Machine**.
3. Click **Power On**.

## Step 2: Log in to Ubuntu

Enter your Ubuntu username and password.

## Step 3: Update Ubuntu

Open Terminal and type:

```bash
sudo apt update
sudo apt upgrade -y
```

## Step 4: Verify Python Installation

```bash
python3 --version
```

Example output:

```text
Python 3.12.3
```

If Python is not installed:

```bash
sudo apt install python3 python3-pip -y
```

## Step 5: Install Flask

```bash
pip3 install flask
```

Verify:

```bash
pip3 show flask
```

# Part (b): Configure Networking

## Step 1: Check IP Address

```bash
hostname -I
```

Example output:

```text
192.168.43.128
```

## Step 2: Configure VMware Network

In VMware:

**VM → Settings → Network Adapter → NAT**

NAT allows the VM to use the host computer's internet connection.

## Step 3: Verify Internet Connection

```bash
ping google.com
```

If replies are received, the network is working.

## Step 4: Check Listening Ports

```bash
sudo ss -tuln
```

Port **5000** will be used by the Flask application.

## Step 5: Allow Flask Port

```bash
sudo ufw allow 5000
```

Check firewall:

```bash
sudo ufw status
```

# Part (c): Deploy Flask Application Manually

## Step 1: Create Project Folder

```bash
mkdir FlaskApp
cd FlaskApp
```

## Step 2: Create `app.py`

Create a file named `app.py` and write:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Ubuntu Virtual Machine!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

Save the file.

## Step 3: Run the Flask Application

```bash
python3 app.py
```

Expected output:

```text
* Running on http://0.0.0.0:5000
```

## Step 4: Test the Application

Open the browser inside Ubuntu and enter:

```text
http://localhost:5000
```

Output:

```text
Hello from Ubuntu Virtual Machine!
```

## Step 5: Stop the Flask Server

Press:

```text
Ctrl + C
```

# Final Project Structure

```text
FlaskApp/
└── app.py
```

## Result

The Virtual Machine was successfully configured using VMware, networking was configured, and the Flask application was successfully deployed and tested on Ubuntu.
