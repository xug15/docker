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

## 环境变量的替换

环境变量（使用 'ENV' 来声明）。在dockerfile中一般用 '$variable_name','${variable_name}'。加括号是解决'${foo}_bar'.
```sh
FROM busybox
ENV foo /bar
WORKDIR ${foo}   # WORKDIR /bar
ADD . $foo       # ADD . /bar
COPY \$foo /quux # COPY $foo /quux
```
环境变量可以用在下面的instructions.
**ADD**
**COPY**
**ENV**
**EXPOSE**
**FROM**
**LABEL**
**STOPSIGNAL**
**USER**
**VOLUME**
**WORKDIR**

```sh
ENV abc=hello
ENV abc=bye def=$abc
ENV ghi=$abc
```

## .dockerignore file
.dockerignore
```sh
# comment
*/temp*
*/*/temp*
temp?
```

## FROM 命令
**语法**
```sh
FROM <image> [AS <name>]
#Or
FROM <image>[:<tag>] [AS <name>]
#Or
FROM <image>[@<digest>] [AS <name>]
```
FROM 命令支持使用 ARG（在from 之前） 命令。
```sh
ARG  CODE_VERSION=latest
FROM base:${CODE_VERSION}
CMD  /code/run-app

FROM extras:${CODE_VERSION}
CMD  /code/run-extras
```
ARG 在执行完一次FROM 就bueng。如果需要再用，就需要再次声明。
```sh
ARG VERSION=latest
FROM busybox:$VERSION
ARG VERSION
RUN echo $VERSION > image_version
```

## RUN
RUN <command> (shell form, the command is run in a shell, which by default is /bin/sh -c on Linux or cmd /S /C on Windows)
RUN ["executable", "param1", "param2"] (exec form)

```sh
RUN /bin/bash -c 'source $HOME/.bashrc; \
echo $HOME'
```
```sh
RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'
```
## CMD
CMD ["executable","param1","param2"] (exec form, this is the preferred form)
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
CMD command param1 param2 (shell form)
```sh
FROM ubuntu
CMD echo "This is a test." | wc -
```
```sh
FROM ubuntu
CMD ["/usr/bin/wc","--help"]
```

## LABEL
LABEL <key>=<value> <key>=<value> <key>=<value> ...
```sh
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates \
that label-values can span multiple lines."
```

```sh
LABEL multi.label1="value1" multi.label2="value2" other="value3"

LABEL multi.label1="value1" \
      multi.label2="value2" \
      other="value3"
```
docker inspect
```sh
"Labels": {
    "com.example.vendor": "ACME Incorporated"
    "com.example.label-with-value": "foo",
    "version": "1.0",
    "description": "This text illustrates that label-values can span multiple lines.",
    "multi.label1": "value1",
    "multi.label2": "value2",
    "other": "value3"
},
```

## EXPOSE
EXPOSE <port> [<port>/<protocol>...]

```sh
EXPOSE 80/udp
# To expose on both TCP and UDP, include two lines:
EXPOSE 80/tcp
EXPOSE 80/udp
```
```sh
docker run -p 80:80/tcp -p 80:80/udp ...
```

## ENV
ENV <key> <value>
ENV <key>=<value> ...

```sh
ENV myName="John Doe" myDog=Rex\ The\ Dog \
    myCat=fluffy
# or
ENV myName John Doe
ENV myDog Rex The Dog
ENV myCat fluffy
```

## ADD

