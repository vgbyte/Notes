# Flask

[TOC]

## Virtual Environment

To create an environment:

```
conda create --name myFlaskEnv
```

### Getting started with virtualenv

First, run this command on your command prompt or terminal:

```bash
pip install virtualenv
```

Second, do the following:

```bash
virtualenv “name of virtual environment”
```

Here you can give a name to the environment. I usually give it a name of virtual. It will look like this:
 `virtualenv virtual`**.**

After setting up virtual environment, check your project folder. It should look like this. The virtual environment needs to be created in the **same directory where your app files are located.**

![img](https://cdn-media-1.freecodecamp.org/images/nlsgTQVp9ZrBHudAD4yKWF7tywO5fsaiYvHq)How the directory looks like

### Activating the virtual environment

Now go to your terminal or command prompt. Go to the directory that contains the file called **activate.** The file called **activate** is found inside a folder called **Scripts for Windows** and **bin for OS X and Linux.**

For OS X and Linux Environment:

```bash
$ name of virtual environmnet/bin/activate
```

For Windows Environment:

```bash
name of virtual environment\Scripts\activate
```

Since I am using a Windows machine, when I activate the environment it will look like this:

![img](https://cdn-media-1.freecodecamp.org/images/FnmOzwRngsHOTcuJr4gMBnvyB6VhYGhcdyI2)You should see this at beginning of your command prompt line

The next step is to install flask on your virtual environment so that we can run the application inside our environment. Run the command:

If you want to switch projects or otherwise leave your virtual environment, simply run:

```bash
deactivate
```

## Intro

```
pip install flask
python -m flask --version
```

```
export FLASK_APP=flaskblog.py
flask run
```

```
export FLASK_DEBUG=1 // to start the debug mode
```

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
@app.route("/home")  # For multiple routes addressing same function just add decorator
def hello():
    return "<h1>Home Page</h1>"

@app.route("/about")
def about():
    return "<h1><em>About Page</em></h1>"

if __name__ == "__main__":
    app.run(debug=True)
```

```
{% ... %} for Statements
{{ ... }} for Expressions to print to the template output
{# ... #} for Comments not included in the template output
# ... ## for Line Statements
```

## render_template

**render_template** is used to render template which are present in **templates** directory.

The Flask Framework looks for HTML files in a folder called **templates.** You **need to create a templates** folder and put all your HTML files in there.

## url_for

The **url_for()** function is very useful for dynamically building a  URL for a specific function. The function accepts the name of a function as first argument, and one or more keyword arguments, each  corresponding to the variable part of URL.

So if you want to get files from the `static/bootstrap` folder you use this code:

```html
<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='bootstrap/bootstrap.min.css') }}">
```

Which will be converted to (using default settings):

```html
<link rel="stylesheet" type="text/css" href="static/bootstrap/bootstrap.min.css">
```