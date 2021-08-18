# Python

## Introduction

Before integrating a new BugSplat SDK with your application, make sure to review the [Getting Started](https://www.bugsplat.com/resources/bugsplat-101/) resources and complete the simple startup tasks listed below.

* [Sign up](https://app.bugsplat.com/v2/sign-up) for a BugSplat account
* [Log in](https://app.bugsplat.com/auth0/login) using your email address
* Create a new [database](https://app.bugsplat.com/v2/company) for your application

{% hint style="info" %}
**Need any further help?** Check out the full BugSplat documentation [here](https://www.bugsplat.com/docs), or email the team at [support@bugsplat.com](mailto:support@bugsplat.com).
{% endhint %}

## bugsplat-py

A BugSplat integration for reporting Unhandled Exceptions in Python.

### Installing

Install the bugsplat package using pip

```bash
pip install bugsplat
```

### Usage

Import the BugSplat class

```python
from bugsplat import BugSplat
```

Create a new BugSplat instance passing it the name of your BugSplat database, application, and version

```python
bugsplat = BugSplat(database, application, version)
```

Optionally, you set default values for key, description, email, user, and additionaFilePaths

```python
bugsplat.setDefaultAppKey('key!')
bugsplat.setDefaultDescription('description!')
bugsplat.setDefaultEmail('fred@bugsplat.com')
bugsplat.setDefaultUser('Fred')
bugsplat.setDefaultAdditionalFilePaths([
    './path/to/additional-file.txt',
    './path/to/additional-file-2.txt'
])
```

Wrap your application code in a try/except block. In the except block call post. You can override any of the default properties that were set in step 3

```python
try:
    crash()
except Exception as e:
    bugsplat.post(e, additionalFilePaths=[], appKey='other key!', description='other description!', email='barney@bugsplat.com', user='Barney')
```

Once you've posted a crash, navigate to the Crashes page and click the link in the ID column to see the crash's details

![BugSplat python crash example](https://www.bugsplat.com/assets/img/docs/python-bs-crash.png)

Thanks for using BugSplat ❤️

