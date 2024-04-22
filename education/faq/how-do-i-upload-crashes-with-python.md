# How Do I Upload Crashes with Python

If you'd like to bulk upload minidump files, you can download the following script as `upload.py` and invoke it as follows, being sure to replace `database`, `application`, `version`, and `file.dmp` with values specific to your BugSplat configuration:

```
python upload.py database application version ./file.dmp
```

{% embed url="https://gist.github.com/bobbyg603/76bc3777817a7df3ca178a8eebf82f91" %}
BugSplat Python Crash Upload Script
{% endembed %}
