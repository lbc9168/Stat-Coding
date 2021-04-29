

## Install packages

如PyCharm内安装报错，则安装Anaconda之后，管理员模式打开cmd控制台，使用`conda install`或制作者指定命令安装。


## Path joint 

*(reference: https://www.geeksforgeeks.org/python-os-path-join-method/)*

Using `os.path.join()` method.

```python
# importing os module 
import os
  
# Path
path = "/home"
  
# Join various path components 
print(os.path.join(path, "User/Desktop", "file.txt"))
```

The output will be:
```python
/home/User/Desktop/file.txt
```
