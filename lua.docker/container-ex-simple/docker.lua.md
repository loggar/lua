# lua docker container

`Dockerfile.debug`

```dockerfile
FROM alpine:3.12

RUN apk update
RUN apk add git \
  && git --version
RUN git config --global user.name "Charly LEE" \
  && git config --global user.password "charly.loggar@gmail.com"
RUN set -ex \
  && apk add --no-cache --virtual .lua-builddeps \
    ca-certificates \
    curl \
    gcc \
    libc-dev \
    make \
    readline-dev \
  && curl -fsSL -o /tmp/lua.tar.gz "https://www.lua.org/ftp/lua-5.4.3.tar.gz" \
  && cd /tmp \
  && echo "1dda2ef23a9828492b4595c0197766de6e784bc7 *lua.tar.gz" | sha1sum -c - \
  && mkdir /tmp/lua \
  && tar -xf /tmp/lua.tar.gz -C /tmp/lua --strip-components=1 \
  && cd /tmp/lua \
  && make linux \
  && make install \
  && cd / \
  && apk add --no-network --no-cache --virtual .lua-rundeps \
    readline \
  && apk del --no-network .lua-builddeps \
  && rm -rf /tmp/lua /tmp/lua.tar.gz \
  && lua -v
RUN mkdir /home/workspace \
  && cd /home/workspace \
  && git clone https://github.com/loggar/lua.git ./workspace-lua \
  && ls /home/workspace/workspace-lua
```

docker build an image

```
docker build -f ./Dockerfile.debug -t loggar/labs-lua:1.0 .
```

run a container

```
docker run -t -d --name lua1 loggar/labs-lua:1.0
```
