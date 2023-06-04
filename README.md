## Laboratory work VIII

Данная лабораторная работа посвещена изучению систем автоматизации развёртывания и управления приложениями на примере **Docker**

## Tutorial

```sh
$ export GITHUB_USERNAME=<имя_пользователя>
```

```
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab07 lab08
$ cd lab08
$ git submodule update --init
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab08
```

```sh
$ cat > Dockerfile <<EOF
FROM ubuntu:18.04
EOF
```

```sh
$ cat >> Dockerfile <<EOF

RUN apt update
RUN apt install -yy gcc g++ cmake
EOF
```

```sh
$ cat >> Dockerfile <<EOF

COPY . print/
WORKDIR print
EOF
```

```sh
$ cat >> Dockerfile <<EOF

RUN cmake -H. -B_build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
RUN cmake --build _build
RUN cmake --build _build --target install
EOF
```

```sh
$ cat >> Dockerfile <<EOF

ENV LOG_PATH /home/logs/log.txt
EOF
```

```sh
$ cat >> Dockerfile <<EOF

VOLUME /home/logs
EOF
```

```sh
$ cat >> Dockerfile <<EOF

WORKDIR _install/bin
EOF
```

```sh
$ cat >> Dockerfile <<EOF

ENTRYPOINT ./demo
EOF
```

```sh
dmitrii@DESKTOP-9P3LE74:~/workspace/projects/lab08$ sudo docker build -t logger .
Sending build context to Docker daemon  1.073MB
Step 1/12 : FROM ubuntu:18.04
 ---> f9a80a55f492
Step 2/12 : RUN apt update
 ---> Using cache
 ---> 7478c6b600d1
Step 3/12 : RUN apt install -yy gcc g++ cmake
 ---> Using cache
 ---> 04a8dafc452d
Step 4/12 : COPY . print/
 ---> 54696c18ffc7
Step 5/12 : WORKDIR print
 ---> Running in 9a95f1c4aa1b
Removing intermediate container 9a95f1c4aa1b
 ---> fb02b2cdfd02
Step 6/12 : RUN cmake -H. -B_build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
 ---> Running in b3c5884e00ca
-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /print/_build
Removing intermediate container b3c5884e00ca
 ---> 5ff5174ab1b7
Step 7/12 : RUN cmake --build _build
 ---> Running in dc9fb1b43e68
Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
Scanning dependencies of target demo
[ 75%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
Removing intermediate container dc9fb1b43e68
 ---> 6d69f8711e37
Step 8/12 : RUN cmake --build _build --target install
 ---> Running in 4c41f794e310
[ 50%] Built target print
[100%] Built target demo
Install the project...
-- Install configuration: "Release"
-- Installing: /print/_install/lib/libprint.a
-- Installing: /print/_install/include
-- Installing: /print/_install/include/print.hpp
-- Installing: /print/_install/cmake/print-config.cmake
-- Installing: /print/_install/cmake/print-config-release.cmake
-- Installing: /print/_install/bin/demo
Removing intermediate container 4c41f794e310
 ---> 5be9b92861ea
Step 9/12 : ENV LOG_PATH /home/logs/log.txt
 ---> Running in 0c93e8cc73a1
Removing intermediate container 0c93e8cc73a1
 ---> af5c84e70f69
Step 10/12 : VOLUME /home/logs
 ---> Running in 4c2ede99b8c5
Removing intermediate container 4c2ede99b8c5
 ---> 3dacf9709454
Step 11/12 : WORKDIR /print/_install/bin
 ---> Running in 7f8647bbed14
Removing intermediate container 7f8647bbed14
 ---> a419b93c7a13
Step 12/12 : ENTRYPOINT ./demo
 ---> Running in 77389a32bf8e
Removing intermediate container 77389a32bf8e
 ---> bb48821d3eb0
Successfully built bb48821d3eb0
Successfully tagged logger:latest

```

```sh
dmitrii@DESKTOP-9P3LE74:~/workspace/projects/lab08$   sudo docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
logger       latest    bb48821d3eb0   16 minutes ago   335MB
<none>       <none>    e366756e7917   19 minutes ago   334MB
<none>       <none>    3c155f9fc7ee   26 minutes ago   334MB
<none>       <none>    4344675f35d1   30 minutes ago   334MB
<none>       <none>    1d7c0b5cce6a   31 minutes ago   334MB
<none>       <none>    bb3f29aa460c   5 hours ago      334MB
<none>       <none>    0122a6ee9a84   34 hours ago     334MB
<none>       <none>    af7e7724cdf8   35 hours ago     334MB
<none>       <none>    06df6656bb69   35 hours ago     334MB
ubuntu       18.04     f9a80a55f492   4 days ago       63.2MB
```

```sh
$ mkdir logs
dmitrii@DESKTOP-9P3LE74:~/workspace/projects/lab08$ sudo docker run -it -v "$(pwd)/logs/:/home/logs/" logger
text1
text2
text3
^D
```

```sh
dmitrii@DESKTOP-9P3LE74:~/workspace/projects/lab08$ sudo docker inspect logger
[
    {
        "Id": "sha256:bb48821d3eb0cc1198e6511b8ceb2291f45236e26f0dd4bfaab12fa462c8b13a",
        "RepoTags": [
            "logger:latest"
        ],
        "RepoDigests": [],
        "Parent": "sha256:a419b93c7a13631994545fbc1392642a58eb7d72916fbaba353572ce44b4b2e0",
        "Comment": "",
        "Created": "2023-06-04T00:55:50.936427137Z",
        "Container": "77389a32bf8ec7cbd05ddef5195a353e956f6074eab4bc0d0e35dc086537431a",
        "ContainerConfig": {
            "Hostname": "77389a32bf8e",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LOG_PATH=/home/logs/log.txt"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "ENTRYPOINT [\"/bin/sh\" \"-c\" \"./demo\"]"
            ],
            "Image": "sha256:a419b93c7a13631994545fbc1392642a58eb7d72916fbaba353572ce44b4b2e0",
            "Volumes": {
                "/home/logs": {}
            },
            "WorkingDir": "/print/_install/bin",
            "Entrypoint": [
                "/bin/sh",
                "-c",
                "./demo"
            ],
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "18.04"
            }
        },
        "DockerVersion": "20.10.21",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LOG_PATH=/home/logs/log.txt"
            ],
            "Cmd": null,
            "Image": "sha256:a419b93c7a13631994545fbc1392642a58eb7d72916fbaba353572ce44b4b2e0",
            "Volumes": {
                "/home/logs": {}
            },
            "WorkingDir": "/print/_install/bin",
            "Entrypoint": [
                "/bin/sh",
                "-c",
                "./demo"
            ],
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "18.04"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 334567006,
        "VirtualSize": 334567006,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/25115a63e66e8aa1ec45f64b21370e724d4af8150b1e8d0d442a4f0132eea553/diff:/var/lib/docker/overlay2/4ff5b4247f225c9de8c2b3101a1bdc75f1e467b56d549ae5c39c0d7d5e2a5650/diff:/var/lib/docker/overlay2/c78cbc062b1fcb0c9a017fc862e553d473f8eef90da98849670f9870fcfd6b72/diff:/var/lib/docker/overlay2/75a0d3cf766f409e9587e016900b976c7fdd8e2419cb3625073cc6651e907d1f/diff:/var/lib/docker/overlay2/e633a6069288bc6c10adf4f7aab6e6cc48ce8bea57b1e34edcbd5469b0de9bed/diff:/var/lib/docker/overlay2/db8aaca45302cafc2f1b0acbf84eed69ccc55d2b79590deb5b53341e15f813e0/diff",
                "MergedDir": "/var/lib/docker/overlay2/dfd92ad6edf9798f46bea78bdedae2f7b18f9f1ad1241ccb83fd052e23e19149/merged",
                "UpperDir": "/var/lib/docker/overlay2/dfd92ad6edf9798f46bea78bdedae2f7b18f9f1ad1241ccb83fd052e23e19149/diff",
                "WorkDir": "/var/lib/docker/overlay2/dfd92ad6edf9798f46bea78bdedae2f7b18f9f1ad1241ccb83fd052e23e19149/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:548a79621a426b4eb077c926eabac5a8620c454fb230640253e1b44dc7dd7562",
                "sha256:7609eb95842f0ebec6fae8abf97bd67176d98aeed3fdd2ea9ff77ee2e2b865e1",
                "sha256:15367511d32facf0dcb07093a8a7f7435ace7e57e8b303fc71912c4f357dd89d",
                "sha256:9dc4a594240c13437452a55e39f961aeea57f08491f1c5961bdc5c81eff04898",
                "sha256:029dae5d7755189c343fb4e19c89276c726d211db1ef268bf4286e0b4be0d8c8",
                "sha256:e297e1f3284fb3727238b9358776d2445a3e6386c6c92bc27cc9d9216912c5db",
                "sha256:2e76f6228e017e4cf5390483c39b723cab183407eab1d691f257ce202c8889c6"
            ]
        },
        "Metadata": {
            "LastTagTime": "2023-06-04T03:55:50.95860295+03:00"
        }
    }
]
```

```sh
dmitrii@DESKTOP-9P3LE74:~/workspace/projects/lab08$ cat logs/log.txt
text1
text2
text3
```
## Links

- [Book](https://www.dockerbook.com)
- [Instructions](https://docs.docker.com/engine/reference/builder/)

```
Copyright (c) 2015-2021 The ISC Authors
```
