## Laboratory work 8

Установим Докер на компьютер. Воспользуемся сайтом: https://docs.docker.com/engine/install/ubuntu/ - гайд по установке. 

Напишем Dockerfile со следующим содержимым:
```
FROM ubuntu:18.04
RUN apt update
RUN apt  install -yy gcc g++ cmake
COPY . /solver_application
WORKDIR /solver_application
RUN cmake -H. -B_build -DDCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
RUN cmake --build _build
RUN cmake --build _build --target install
ENV LOG_PATH=/home/logs/log.txt
VOLUME /home/logs
WORKDIR /solver_application/_build/
ENTRYPOINT ["./equation"]
```
И изменим файл yml
```
name: Actions
on:
  push:
    branches: [master]
  pull_request:
    branches: [master]

jobs: 
 build_Linux:

  runs-on: ubuntu-latest

  steps:
  - uses: actions/checkout@v4

  - name: Build the Docker
    run: docker build -t logger .

  - name: Put logs
    run: docker run -v "$(pwd)/logs/:/home/logs/" logger
```
Далее проверим работоспособность на локальной машине:
```
docker build -t logger .
docker run -it -v "$(pwd)/logs/:/home/logs/" logger
```
На выходе получим:
```
docker build -t logger .        
[+] Building 73.3s (14/14) FINISHED                                                                                          docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                         0.0s
 => => transferring dockerfile: 436B                                                                                                         0.0s 
 => [internal] load metadata for docker.io/library/ubuntu:18.04                                                                              2.3s 
 => [internal] load .dockerignore                                                                                                            0.1s
 => => transferring context: 2B                                                                                                              0.0s 
 => [1/9] FROM docker.io/library/ubuntu:18.04@sha256:152dc042452c496007f07ca9127571cb9c29697f42acbfad72324b2bb2e43c98                        4.8s
 => => resolve docker.io/library/ubuntu:18.04@sha256:152dc042452c496007f07ca9127571cb9c29697f42acbfad72324b2bb2e43c98                        0.0s 
 => => sha256:7c457f213c7634afb95a0fb2410a74b7b5bc0ba527033362c240c7a11bef4331 25.69MB / 25.69MB                                             3.9s 
 => => extracting sha256:7c457f213c7634afb95a0fb2410a74b7b5bc0ba527033362c240c7a11bef4331                                                    0.6s
 => [internal] load build context                                                                                                            0.3s
 => => transferring context: 2.09MB                                                                                                          0.1s
 => [2/9] RUN apt update                                                                                                                     9.1s
 => [3/9] RUN apt  install -yy gcc g++ cmake                                                                                                41.1s
 => [4/9] COPY . /solver_application                                                                                                         0.7s 
 => [5/9] WORKDIR /solver_application                                                                                                        0.2s 
 => [6/9] RUN cmake -H. -B_build -DDCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install                                                 2.0s 
 => [7/9] RUN cmake --build _build                                                                                                           1.4s 
 => [8/9] RUN cmake --build _build --target install                                                                                          0.8s 
 => [9/9] WORKDIR /solver_application/_build/                                                                                                0.2s 
 => exporting to image                                                                                                                      10.3s 
 => => exporting layers                                                                                                                      7.8s 
 => => exporting manifest sha256:913745a34272c8bc86777a0692f592cce43710597228635f81f3c1c955caf497                                            0.0s 
 => => exporting config sha256:42f88975de71e79470ff2f3165bae540041d36fe35eec49c357d15b1dbd5a894                                              0.1s 
 => => exporting attestation manifest sha256:f7ec78ca27de406a50ff8ba6f813d8cdd22753f16c8c59de1498bb26bc68ceb5                                0.1s 
 => => exporting manifest list sha256:250d67a79e791963f43e321a46709c84940ceab3d90a31fc78d7bb058d06b823                                       0.0s 
 => => naming to docker.io/library/logger:latest                                                                                             0.0s
```
```

 => => unpacking to docker.io/library/logger:latest                                                                                          2.2s
```
```
docker run -it -v "$(pwd)/logs/:/home/logs/" logger
1
2
1
-------------------------
x1 = -1.000000
-------------------------
-------------------------
x2 = -1.000000
-------------------------
```
