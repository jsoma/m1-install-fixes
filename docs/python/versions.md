# Python 3.10 M1 issues? Switch to older, more stable Python!

> Fix for: Python 3.10 has errors on M1 Macs/new OS X installing Jupyter, pandas, matplotlib, or other libraries

## Intro
When you install Python packages, there are **two ways they can be installed:**

* Ready-made versions that work with your computer called "wheels." They come straight from the internet! It takes like two seconds to install!
* You download a bunch of code that then gets turned into a library. This is "compiling" or "building" the wheel.

You don't make this decision – pip does! If there's a ready-made version, it uses that. If not, your computer builds it. If you have a super-new computer or are using a super-new version of Python, there might not be a wheel so your computer has to compile its own.

Sometimes your computer *can't* build it, though. It might be that you need more software installed, or the version of the library doesn't work with your kind of computer, or the library just hasn't gotten support for the Python version yet.

**The easiest solution is to use an older version of Python and try to install again.**

For example, maybe we're using 3.10.0. It's a new Python, maybe matplotlib doesn't have a wheel! We can't build the wheel for WHO KNOWS WHY: NO ONE KNOWS. Could be a thousand reasons. So instead, we'll switch to an earlier version of Python that has pandas/matplotlib/requests wheels (or ones that we can build successfully) and we'll be good to go.

This is why `pyenv` rules. It allows you to easily install and switch between Python versions.

## How to fix it

### Cleaning house

First, **CLOSE ALL OF YOUR TERMINALS**. Make sure there are non that are minimized! We're doing this to make sure that we don't have any Python 3.10.0 Jupyter notebook servers floating around.

### Changing our default Python

Now let's install abandon Python 3.10.0! We'll use `pyenv` to **install Python 3.9.7** and **set it as the default Python version**.

> Remember: Don't include the `$` in your command!

```bash
$ pyenv install 3.9.7
$ pyenv global 3.9.7
```

The first one might take a while to download and install 3.9.7, but it'll finish up eventually.

### Reinstalling Jupyter/Connecting it to Python 3.9.7

Previously we were using Jupyter with Python 3.10.0. Now we need to **hook Jupyter into Python 3.9.7**.

First, check to make sure you're using Python 3.9.7:

```bash
$ python --version
Python 3.9.7
```

If it does't say 3.9.7, run the `pyenv` commands above again.

Now we can **install Jupyter**...

```bash
$ pip install jupyter
```

...then we can **connect Jupyter to the new version of Python**. The Jupyter-Python connection is called a "kernel," and these commands make sure our Python 3 kernel (aka Jupyter's Python) is the same Python as `python` on the command line.

```bash
$ python -m pip install ipykernel
$ python -m ipykernel install --user
```

### Installing some more libraries

We were having problems installing pandas and matplotlib before on some of our computers. **Let's try installing these libraries** on the command line right now – along with a few other common libraries – just to be sure we don't get any errors.

```bash
$ pip install pandas matplotlib requests beautifulsoup4 pg8000
```

If you get an error here, try installing them one at a time. Which one gives the error?

### Testing out Jupyter

Now that we've installed a new Python, installed Jupyter, and connected Jupyter to the new Python, we can try to run Jupyter notebook.

```bash
$ jupyter notebook
```

When it opens up, select **New** and then **Python 3 (ipykernel)**. This will connect us to our newly installed Python.

Now we can run some Python code to check our installation. Let's make sure our **imports work** and our libraries were installed correctly...

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import matplotlib as plt
```

...then make sure our **pandas is good to go**...

```python
df = pd.DataFrame([
    { 'col1': 4, 'col2': 8 },
    { 'col1': 14, 'col2': 12 },
    { 'col1': 8, 'col2': 1 },
])
df
```

...then make sure our **matplotlib is functional**...

```python
df.plot(x='col1',
        y='col2',
        kind='scatter',
        title="It works!!!")
```

...and then we're all set!