# Introduction
* [Back home](../README.md)

* [Reference](https://docs.docker.com/engine/reference/builder/)

## 1. Usage
1. 文件夹里面要有Dockerfile.
```sh
$ docker build .
Sending build context to Docker daemon  6.51 MB
```
2. 指定其他位置的Dockerfile用来构建docker。
```sh
$ docker build -f /path/to/a/Dockerfile .
```
3. 指定一个储存和标签，在一个image搭建成功后。
```sh
$ docker build -t shykes/myapp .
```
4. 可以将image 储存到多个地方。多加个 -t 就可以了。
```sh
$ docker build -t shykes/myapp:1.0.2 -t shykes/myapp:latest .
```

## Dockerfile 格式
 大小写不敏感。不过一般习惯将命令都大写。
 Dockerfile 必须从 ‘From instruction’ 指定一个基础都 ‘Base Image’. 也可以使用‘ARG’命令。
 “#”可以用于注释掉
 ```sh
 # Comment
RUN echo 'we are running some # of cool things'
```

## 命令解析
命令解析是可以选择执行的。可以影响Dockerfile命令行的解析顺序。解析命令不会添加层到build.
注释的行，空白行，builder instruction 被执行的，docker 不会再解析命令。所以解析命令必须要放在最顶端的地方。
解析命令
**无效的命令：**
不连续
```sh
# direc \
tive=value
```
**出现两次**
```sh
# directive=value1
# directive=value2

FROM ImageName
```
**在builder instruction被当作注释**
```sh
FROM ImageName
# directive=value
```

## 语法：
```sh
# syntax=[remote image reference]

# syntax=docker/dockerfile
# syntax=docker/dockerfile:1.0
# syntax=docker.io/docker/dockerfile:1
# syntax=docker/dockerfile:1.0.0-experimental
# syntax=example.com/user/repo:tag@sha256:abcdef...
```

## 避免
```sh
# escape=\ (backslash) #反斜杠

# escape=` (backtick) #反引号

```
用来注释掉符号。默认用‘\’
```sh
FROM microsoft/nanoserver
COPY testfile.txt c:\\
RUN dir c:\

# Results in:

PS C:\John> docker build -t cmd .
Sending build context to Docker daemon 3.072 kB
Step 1/2 : FROM microsoft/nanoserver
 ---> 22738ff49c6d
Step 2/2 : COPY testfile.txt c:\RUN dir c:
GetFileAttributesEx c:RUN: The system cannot find the file specified.
PS C:\John>
```
##
```sh

# escape=`

FROM microsoft/nanoserver
COPY testfile.txt c:\
RUN dir c:\

# Results in:

PS C:\John> docker build -t succeeds --no-cache=true .
Sending build context to Docker daemon 3.072 kB
Step 1/3 : FROM microsoft/nanoserver
 ---> 22738ff49c6d
Step 2/3 : COPY testfile.txt c:\
 ---> 96655de338de
Removing intermediate container 4db9acbb1682
Step 3/3 : RUN dir c:\
 ---> Running in a2c157f842f5
 Volume in drive C has no label.
 Volume Serial Number is 7E6D-E0F7

 Directory of c:\

10/05/2016  05:04 PM             1,894 License.txt
10/05/2016  02:22 PM    <DIR>          Program Files
10/05/2016  02:14 PM    <DIR>          Program Files (x86)
10/28/2016  11:18 AM                62 testfile.txt
10/28/2016  11:20 AM    <DIR>          Users
10/28/2016  11:20 AM    <DIR>          Windows
           2 File(s)          1,956 bytes
           4 Dir(s)  21,259,096,064 bytes free
 ---> 01c7f3bef04f
Removing intermediate container a2c157f842f5
Successfully built 01c7f3bef04f
PS C:\John>
```


