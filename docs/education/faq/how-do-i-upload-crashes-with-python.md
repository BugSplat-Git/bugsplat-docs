# How Do I Upload Crashes with Python?

If you'd like to bulk upload minidump files, you can download the following script as `upload.py` and invoke it as follows, being sure to replace `database`, `application`, `version`, and `file.dmp` with values specific to your BugSplat configuration:

```bash
python upload.py database application version ./file.dmp
```

{% @github-files/github-code-block url="https://github.com/BugSplat-Git/python-crash-upload/blob/main/upload.py" %}
