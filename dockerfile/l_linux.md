# Linux
* [Back to home](../README.md)

```sh
FROM dongzhuoer/lulab:code

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"

# add data
COPY data linux

RUN chown -R test:test linux

USER test
```