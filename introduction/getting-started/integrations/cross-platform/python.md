# Python

### Sample

An example Python project configured with BugSplat exception reporting can be found [here](https://github.com/BugSplat-Git/my-python-crasher).

### Installation

BugSplat recommends you use [bugsplat-py](https://github.com/BugSplat-Git/bugsplat-py) with a Python [virtual environment](https://docs.python.org/3/library/venv.html). To create a virtual environment for your project please run the following command at your project's root directory:

```shell
python -m venv venv
```

Activate your virtual environment by running the following command:

```shell
# unix/macos
source venv/bin/activate

# windows
.\env\Scripts\activate
```

Install the [bugsplat](https://pypi.org/project/bugsplat) package using pip:

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
bugsplat.set_default_app_key('key!')
bugsplat.set_default_description('description!')
bugsplat.set_default_email('fred@bugsplat.com')
bugsplat.set_default_user('Fred')
bugsplat.set_default_additional_file_paths([
    './path/to/additional-file.txt',
    './path/to/additional-file-2.txt'
])
```

Wrap your application code in a try/except block. In the except block call post. You can override any of the default properties that were set in step 3

```python
try:
    crash()
except Exception as e:
    bugsplat.post(
        e,
        additional_file_paths=[],
        app_key='other key!',
        description='other description!',
        email='barney@bugsplat.com',
        user='Barney'
    )
```

Once you've posted a crash, navigate to the Crashes page and click the link in the ID column to see the crash's details

![](<../../../../.gitbook/assets/image (2) (2).png>)

Thanks for using BugSplat ❤️
