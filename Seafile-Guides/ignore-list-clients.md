# Ignore list for Seafile clients
This works for Windows, Linux and macOS clients.
You can if you want ignore files and folders for sync.
What you need to do is that you need to create a file named seafile-ignore.txt in the library where the files and folders are located that you want to ignore and then the rules for that library are sett, so you need to create this file for every library that you have.
If you want to ignore a folder you can write it like this.
```
example/
```
If you want to ignore a file you can type it like this.
```
# ignore the exact file.
example.ini
# This will ignore every file that's ending with .ini
*.ini
```
Ignore list also supports wildecards so you can modify this list in many different ways, but if you want to learn more how to modify the list with wildecards, read this link https://www.seafile.com/en/help/ignore/
