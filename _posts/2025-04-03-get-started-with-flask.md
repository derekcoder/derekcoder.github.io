---
title: Get Started with Flask
layout: post
category: Backend
tag: [Python, Flask]
---

Today we will talk about **Flask**.

## What is Flask?

**Flask** is a **lightweight WSGI web application framework** in Python. It is designed to make getting started quickly and easily, with the ability to scale up to complex applications.

## How to use Flask?

### 1. Create a project folder

```shell
mkdir hello_world
cd hello_world
```

You can use script above to create a project folder `hello_world`.

### 2. Setup the Python virtual environment

```shell
python3 -m venv .venv
source .venv/bin/activate
```

Create Python virtual environment using `python3 -m venv .venv` and activate it using `source .venv/bin/activate`.

> By the way, you can exit from the Python virtual environment using `deactivate`.

### 3. Install Flask

```shell
pip install Flask
```

Run the script to install Flask module.

### 4. Write your application

Write your code inside a Python file named `app.py`. 

> You can use any name for your Python file.

```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"
```

- First, we imported the `Flask` class at line 2. An instance of this class will be our WSGI application.
- We created an instance of `Flask` named `app` at line 4. The first argument is the name of the application’s module or package.
- We then used the `route()` decorator to tell Flask what URL should trigger our function at line 6.
- The function `hello_world` returned the HTML message.

### 5. Test the application

```shell
flask run
```

```shell
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
```

> Since I used `app.py`, I can run the application using `flask run` directly. If you used other names, you may need to specify where your application is using `flask --app hello run` (hello.py). You can check [here](https://flask.palletsprojects.com/en/stable/cli/) for more details.

Now open the link ([http://127.0.0.1:5000](http://127.0.0.1:5000)) in your browser, you can see **Hello, World!**.

## References

- [Flask User's Guide](https://flask.palletsprojects.com/en/stable/)
- [VENV Installation](https://flask.palletsprojects.com/en/stable/installation/)
- [Flask Quickstart](https://flask.palletsprojects.com/en/stable/quickstart/)