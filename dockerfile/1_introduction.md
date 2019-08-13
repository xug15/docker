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

```sh
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"] (this form is required for paths containing whitespace)
```
ADD 函数用来拷贝新的文件，文件夹，或者远程文件。从scr 拷贝到image的路径中。
```sh
ADD hom* /mydir/        # adds all files starting with "hom"
ADD hom?.txt /mydir/    # ? is replaced with any single character, e.g., "home.txt"
```

dest 是绝对路径或者是相对与 workdir 路径
```sh
ADD test relativeDir/          # adds "test" to `WORKDIR`/relativeDir/
ADD test /absoluteDir/         # adds "test" to /absoluteDir/
```
拷贝arr[0].txt到/mydir
```sh
ADD arr[[]0].txt /mydir/    # copy a file named "arr[0].txt" to /mydir/
```
拷贝的文件默认的UID/GID为0. 
```sh
ADD --chown=55:mygroup files* /somedir/
ADD --chown=bin files* /somedir/
ADD --chown=1 files* /somedir/
ADD --chown=10:11 files* /somedir/
```

* 文件从远程目录拷贝到系统到的文件权限是600.

## COPY
```sh
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"] (this form is required for paths containing whitespace)
```
复制文件和文件夹从scr到dest 文件中。
拷贝的时候使用通配符。
```sh
COPY hom* /mydir/        # adds all files starting with "hom"
COPY hom?.txt /mydir/    # ? is replaced with any single character, e.g., "home.txt"
```
拷贝文件到相对到WORKDIR 或者绝对路径。
```sh
COPY test relativeDir/   # adds "test" to `WORKDIR`/relativeDir/
COPY test /absoluteDir/  # adds "test" to /absoluteDir/
```
拷贝有特殊名字到文件。例如arr[0].txt
```sh
COPY arr[[]0].txt /mydir/    # copy a file named "arr[0].txt" to /mydir/
```
拷贝文件通过特殊到用户。
```sh
COPY --chown=55:mygroup files* /somedir/
COPY --chown=bin files* /somedir/
COPY --chown=1 files* /somedir/
COPY --chown=10:11 files* /somedir/
```

## ENTRYPOINT
两种形式：
```sh
ENTRYPOINT ["executable", "param1", "param2"] (exec form, preferred)
ENTRYPOINT command param1 param2 (shell form)
```
entrypoint 允许用户配置容器，使容器变得可执行。
例如：启动 nginx 监听 80端口。
```sh
docker run -i -t --rm -p 80:80 nginx
```
命令行参数docker run <image> 将会将exec的元素添加到 ENTRYPOINT 中执行。docker run <image> -d 将会把 -d 的参数传给 entry point.用户可以越过 ENTRYPOINT, docker run --entrypoint 标签。
Exec form ENTRYPOINT example

```sh
FROM ubuntu
ENTRYPOINT ["top", "-b"]
CMD ["-c"]
```
当你允许容器的时候，可以看到top是唯一运行的进程。
```sh
$ docker run -it --rm --name test  top -H
top - 08:25:00 up  7:27,  0 users,  load average: 0.00, 0.01, 0.05
Threads:   1 total,   1 running,   0 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.1 us,  0.1 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem:   2056668 total,  1616832 used,   439836 free,    99352 buffers
KiB Swap:  1441840 total,        0 used,  1441840 free.  1324440 cached Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
    1 root      20   0   19744   2336   2080 R  0.0  0.1   0:00.04 top
```
为了更进一步验证结果。可以使用docker exec:
```sh
$ docker exec -it test ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  2.6  0.1  19752  2352 ?        Ss+  08:24   0:00 top -b -H
root         7  0.0  0.1  15572  2164 ?        R+   08:25   0:00 ps aux
```
我们可以通过 docker stop test 来停止 top 命令。
下面的 Dockerfile 使用 ENTRYPOINT 来运行 Apache.（提前运行，PID 1）
```sh
FROM debian:stable
RUN apt-get update && apt-get install -y --force-yes apache2
EXPOSE 80 443
VOLUME ["/var/www", "/var/log/apache2", "/etc/apache2"]
ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

如果需要写一个启动脚本，你可以确定最后可执行。可以接收Unix signals 通过使用 exec 和 gosu.
```sh
#!/usr/bin/env bash
set -e

if [ "$1" = 'postgres' ]; then
    chown -R postgres "$PGDATA"

    if [ -z "$(ls -A "$PGDATA")" ]; then
        gosu postgres initdb
    fi

    exec gosu postgres "$@"
fi

exec "$@"
``
最后，如果需要在关闭的时候做额外的清理，或者需要协调多个可执行的文件。你可能需要 ENTRYPOINT 脚本接收 unix 信号，传递信号，然后再做些事情。

```sh
#!/bin/sh
# Note: I've written this using sh so it works in the busybox container too

# USE the trap if you need to also do manual cleanup after the service is stopped,
#     or need to start multiple services in the one container
trap "echo TRAPed signal" HUP INT QUIT TERM

# start service in background here
/usr/sbin/apachectl start

echo "[hit enter key to exit] or run 'docker stop <container>'"
read

# stop service and clean up here
echo "stopping apache"
/usr/sbin/apachectl stop

echo "exited $0"
```
如果你运行image 是通过： docker run -it --rm -p 80:80 --name test apache 你可以通过 docker exec, or docker top 来进行测试。
 
```sh
$ docker exec -it test ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.1  0.0   4448   692 ?        Ss+  00:42   0:00 /bin/sh /run.sh 123 cmd cmd2
root        19  0.0  0.2  71304  4440 ?        Ss   00:42   0:00 /usr/sbin/apache2 -k start
www-data    20  0.2  0.2 360468  6004 ?        Sl   00:42   0:00 /usr/sbin/apache2 -k start
www-data    21  0.2  0.2 360468  6000 ?        Sl   00:42   0:00 /usr/sbin/apache2 -k start
root        81  0.0  0.1  15572  2140 ?        R+   00:44   0:00 ps aux
$ docker top test
PID                 USER                COMMAND
10035               root                {run.sh} /bin/sh /run.sh 123 cmd cmd2
10054               root                /usr/sbin/apache2 -k start
10055               33                  /usr/sbin/apache2 -k start
10056               33                  /usr/sbin/apache2 -k start
$ /usr/bin/time docker stop test
test
real	0m 0.27s
user	0m 0.03s
sys	0m 0.03s
```
> Note:
> * 你可以覆盖 ENTRYPOINT --entrypoint. 不过只能设置二进制来执行： exec
> * 




