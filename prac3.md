# Practical: Git Repository Management

## Aim

Create a Git repository, initialize it, and add a Python project.

## Software Required

* Git for Windows
* Visual Studio Code
* Python

## Step 1: Open Visual Studio Code

Open **Visual Studio Code**.

Go to:

**Terminal → New Terminal**

## Step 2: Go to Desktop

```bash
cd Desktop
```

Press **Enter**.

## Step 3: Create Project Folder

```bash
mkdir Git_Practical
```

## Step 4: Move into the Folder

```bash
cd Git_Practical
```

## Step 5: Open Folder in VS Code

```bash
code .
```

The `Git_Practical` folder opens in VS Code.

## Step 6: Create Python File

Create a new file named:

```text
hello.py
```

Write:

```python
print("Hello Git!")
```

Save the file.

## Step 7: Create README File

Create another file named:

```text
README.md
```

Write:

```text
# Git Practical

This is my first Git repository.

Project Name: Python Demo
Created by: Your Name
```

Save the file.

## Step 8: Initialize Git Repository

In the terminal, type:

```bash
git init
```

Output:

```text
Initialized empty Git repository in
C:/Users/YourName/Desktop/Git_Practical/.git/
```

## Step 9: Check Git Status

```bash
git status
```

Output:

```text
On branch master

No commits yet

Untracked files:
    hello.py
    README.md
```

## Step 10: Add Files

```bash
git add .
```

## Step 11: Configure Git

If Git is being used for the first time, configure your name and email:

```bash
git config --global user.name "Your Name"
```

```bash
git config --global user.email "your@email.com"
```

## Step 12: Commit the Project

```bash
git commit -m "Initial Python Project"
```

Output:

```text
[master (root-commit) abc1234] Initial Python Project
2 files changed
create mode 100644 hello.py
create mode 100644 README.md
```

## Step 13: Check Status

```bash
git status
```

Output:

```text
On branch master
nothing to commit, working tree clean
```

## Step 14: Modify Python File

Open `hello.py` and change it to:

```python
print("Hello Git!")
print("Welcome to Git Repository")
```

Save the file.

## Step 15: Add and Commit Changes

```bash
git add hello.py
```

Then:

```bash
git commit -m "Updated hello.py"
```

## Step 16: View Commit History

```bash
git log
```

The commits will be displayed, including:

```text
Updated hello.py
Initial Python Project
```

## Final Folder Structure

```text
Git_Practical
│
├── .git
├── hello.py
└── README.md
```

## Result

A Git repository was successfully created, initialized, and a Python project was added and committed successfully.
