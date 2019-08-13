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
有2种格式：
```sh
RUN <command> (shell form, the command is run in a shell, which by default is /bin/sh -c on Linux or cmd /S /C on Windows)
RUN ["executable", "param1", "param2"] (exec form)
```
RUN 命令将在当前 image 中，创建一个新的层，执行任何新的命令。结果会提交作为下一步的准备。
RUN 层和产生 commits 作为 Docker 的核心概念。Docker 的 commits 是廉价， 容器可以被在任意image 的点 创造。
exec表单可以避免shell字符串重写，并使用不包含指定shell可执行文件的基本映像来运行RUN命令。
在下面的 shell 中， 我们使用 \ (backslash) 将连续的一行分成2行。
```sh
RUN /bin/bash -c 'source $HOME/.bashrc; \
echo $HOME'
```
上面的等价于下面的一行。
```sh
RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'
```
> Note:
为了使用不同的 shell, 比如使用 '/bin/bash' ，我们使用 exec 格式来传递希望的 shell.
```sh
RUN ["/bin/bash", "-c", "echo hello"]
```

> Note:
exec 格式使用  JSON array ， 所以必须使用双引号。

> Note:
不像 shell 脚本，  exec form 不会激活 命令 shell, 意味着正常的 shell 进程不会发生。RUN [ "echo", "$HOME" ] 无法正常运行，需要改为： RUN [ "sh", "-c", "echo $HOME" ]


## CMD
CMD 有3种格式：
```sh
CMD ["executable","param1","param2"] (exec form, this is the preferred form)
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
CMD command param1 param2 (shell form)
```
在 Dockerfile 应该只有一个 CMD 。如果你有多个 CMD ，那么只有最有一个 CMD 会生效。
**CMD 的意义在于对于可执行的容器提供一个默认功能。** 这些可执行的包括一个可执行的文件或者可以忽略可执行文件，但是必须指定一个 ENTRYPOINT 。

> Note:
如果 CMD 被用来提供 ENTRYPOINT 默认参数， 那么 ENTRYPOINT 和 CMD 都必须使用  JSON array 
> Note:
使用  JSON array ， 所以必须使用双引号。
> Note:
不像 shell 脚本，  exec form 不会激活 命令 shell, 意味着正常的 shell 进程不会发生。RUN [ "echo", "$HOME" ] 无法正常运行，需要改为： RUN [ "sh", "-c", "echo $HOME" ] 

当使用 shell or exec 格式， CMD 的命令会在运行 image  时运行。
如果你使用 shell 格式 CMD, 那么 命令会通过 /bin/sh -c 来执行
```sh
FROM ubuntu
CMD echo "This is a test." | wc -
```
如果您想 run your <command> without a shell  那么你必须用 json 表达并且给出全路径来进行执行程序。 任何一个参数必须要在 array 中表示为独立的一个字符串。

```sh
FROM ubuntu
CMD ["/usr/bin/wc","--help"]
```
如果你想你的容器每次都跑同样都执行文件，那么你可以考虑使用 ENTRYPOINT 并结合 CMD.

如果用户在 docker run 指定了参数，那么它会覆盖掉原理默认的 CMD 。

> NOte: 不要混淆 RUN with CMD 。 RUN 只是在跑一下命令，并 commits 结果；CMD 不会在 build 中执行任何命令，但是会在启动指定的容器时执行。

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
> * 你可以覆盖 ENTRYPOINT --entrypoint. 不过只能设置二进制来执行： exec (sh -c 不会被执行)
> * 可执行到格式会被按照 JSON 的向量来执行。对于字符串要加双引号。
> * 不像执行shell脚本，exec 不会激活command shell. 所以 ENTRYPOINT [ "sh", "-c", "echo $HOME" ] 会执行而 ENTRYPOINT [ "echo", "$HOME" ]不会执行。

**shell格式的ENTRYPOINT**
你可以把ENTRYPOINT写成简单的文本，它会按照 /bin/bash -c 来执行。这个格式的进程会代替 shell 环境变量。而且还会忽略 CMD or docker run 命令参数。为了保证 docker stop 将信号传出任何运行的 ENTRYPOINT 正确的执行，你需要记住以 exec 打头
```sh
FROM ubuntu
ENTRYPOINT exec top -b
```
当你运行这个image的时候，它会只有一个 进程：PID 1
```sh
$ docker run -it --rm --name test top
Mem: 1704520K used, 352148K free, 0K shrd, 0K buff, 140368121167873K cached
CPU:   5% usr   0% sys   0% nic  94% idle   0% io   0% irq   0% sirq
Load average: 0.08 0.03 0.05 2/98 6
  PID  PPID USER     STAT   VSZ %VSZ %CPU COMMAND
    1     0 root     R     3164   0%   0% top -b
```
退出 docker stop:
```sh
$ /usr/bin/time docker stop test
test
real	0m 0.20s
user	0m 0.02s
sys	0m 0.04s
```
如果你忘记在 ENTRYPOINT 开头加 exec
```sh
FROM ubuntu
ENTRYPOINT top -b
CMD --ignored-param1
```
```sh
$ docker run -it --name test top --ignored-param2
Mem: 1704184K used, 352484K free, 0K shrd, 0K buff, 140621524238337K cached
CPU:   9% usr   2% sys   0% nic  88% idle   0% io   0% irq   0% sirq
Load average: 0.01 0.02 0.05 2/101 7
  PID  PPID USER     STAT   VSZ %VSZ %CPU COMMAND
    1     0 root     S     3168   0%   0% /bin/sh -c top -b cmd cmd2
    7     1 root     R     3164   0%   0% top
```
如果你运行 docker stop test 那么容器不会很干净的退出。stop 命令会被迫发出一个 SIGKILL 在超时之后。
```sh
$ docker exec -it test ps aux
PID   USER     COMMAND
    1 root     /bin/sh -c top -b cmd cmd2
    7 root     top -b
    8 root     ps aux
$ /usr/bin/time docker stop test
test
real	0m 10.19s
user	0m 0.04s
sys	0m 0.03s
```

**如何理解 CMD and ENTRYPOINT 相互作用**
CMD and ENTRYPOINT 都可以定义当运行容器当时候哪些命令可以运行。
* 1. Dockerfile 必须至少有一个 CMD or ENTRYPOINT 。
* 2. 当把docker 容器作为一个可以执行文件时， ENTRYPOINT 必须被定义。
* 3. CMD 必须被用来定义 ENTRYPOINT 执行时的默认参数，或者作为一个可以执行的临时容器。
* 4. CMD 会被覆盖，当运行容器带有其他可以替换的参数时。

| | No ENTRYPOINT | ENTRYPOINT exec_entry p1_entry | ENTRYPOINT [“exec_entry”, “p1_entry”]|
|:-| :-  | :-  |:-   |
|No CMD	|error, not allowed	|/bin/sh -c exec_entry p1_entry	|exec_entry p1_entry|
|CMD [“exec_cmd”, “p1_cmd”]	|exec_cmd p1_cmd	|/bin/sh -c exec_entry p1_entry	|exec_entry p1_entry exec_cmd p1_cmd|
|CMD [“p1_cmd”, “p2_cmd”]	|p1_cmd p2_cmd	|/bin/sh -c exec_entry p1_entry	|exec_entry p1_entry p1_cmd p2_cmd|
|CMD exec_cmd p1_cmd	|/bin/sh -c exec_cmd p1_cmd	|/bin/sh -c exec_entry p1_entry	exec_entry p1_entry |/bin/sh -c exec_cmd p1_cmd|

> Note: 如果 CMD 是在底层的image被定义的，那么设置 ENTRYPOINT 将会重置 CMD 为空。在当前的场景下， CMD 必须在当前的image中被定义。

## VOLUME
```sh
VOLUME ["/data"]
```
VOLUME 是创建一个有特别定义名字和标志它作为一个外部挂载挂载点。它可以是一个本地的主机或者是其他容器。它可以是一个  JSON array, VOLUME ["/var/log/"] 或者是文本 VOLUME /var/log or VOLUME /var/log /var/db 

docker run 启动一个新创建的卷。它可以包括存在于特定位置中image，任意的数据
```sh
FROM ubuntu
RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting
VOLUME /myvol
```
Dockerfile 将会产生一个image, docker run 创建一个新的挂载点在 /myvol 然后拷贝 greeting 到新到卷中。

Notes about specifying volumes
当在 Dockerfile 使用卷时，记住以下几点：

* 1. 在 Volumes on Windows-based containers: 目标卷必须是：一个不存在的或者空的文件夹，不能在C：盘。之一。
* 2. 在 Dockerfile 修改卷：如何修改数据发生在卷的声明之后，都会被忽略。
* 3. JSON formatting: 双引号而不是单引号。
* 4. 主机文件夹在容器运行的时候会被宣布。

## USER
```sh
USER <user>[:<group>] or
USER <UID>[:<GID>]
```

## WORKDIR
```sh
WORKDIR /path/to/workdir

WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd

ENV DIRPATH /path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
```

## ARG


```sh
ARG <name>[=<default value>]
```
ARG 可以定义一些在 docker build 运行时，传递变量参数：--build-arg <varname>=<value> 。如何用户定义的参数在运行的时候没有给出，就会报错。
```sh
[Warning] One or more build-args [foo] were not consumed.
```
在 Dockerfile 中可以有多个 ARG。例子如下：
```sh
FROM busybox
ARG user1=someuser
ARG buildno=1
...
```

**默认的值**
```sh
FROM busybox
ARG user1=someuser
ARG buildno=1
...
```
如果运行的时候没有参数传入进来，那搭建就会使用默认的参数。

**范围**
ARG 变量的定义来自于 Dockerfile 中行，不是来自于用户在运行是命令行或者其他。
```sh
1 FROM busybox
2 USER ${user:-some_user}
3 ARG user
4 USER $user
...
```
用户通过下面使用：
```sh
$ docker build --build-arg user=what_user .
```
第二行的 USER 被赋值 some_user 。 同时在第三行被定义。 在4行 USER 被赋值 what_user 作为 user 来定义。优先级最高的是 ARG 。

**使用 ARG 变量**
可以使用 ARG or an ENV 来定义变量。 ENV 会优先于 ARG。
```sh
1 FROM ubuntu
2 ARG CONT_IMG_VER
3 ENV CONT_IMG_VER v1.0.0
4 RUN echo $CONT_IMG_VER
```

```sh
$ docker build --build-arg CONT_IMG_VER=v2.0.1 .
```

