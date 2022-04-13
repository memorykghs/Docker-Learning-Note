# 100 - 【Lab】將 Spring Boot 專案部上容器運行

#### Step 1
隨便寫個簡單的 Spring Boot 專案並用 Maven 打包，如何打包可以參考下面網址。
* https://dotblogs.com.tw/zjh/2018/09/20/maven_3
* https://www.baeldung.com/spring-boot-run-maven-vs-executable-jar

#### Step 2
接下來建立並編輯 Dockerfile。
```
touch dockerfile
vi dockerfile
```

Dockerfile 內容如下，可以到 [open jdk 官方 Docker Hub](https://hub.docker.com/_/openjdk?tab=tags&page=5) 找適合的 jdk 版本。
```docker
FROM openjdk:11-jdk
ARG JAR_FILE=target/*.jar
COPY /*.jar .
ENTRYPOINT ["java","-jar","/helloSpringBoot-0.0.1.jar"]
```

`ENTRYPOINT` 的指令相當於：
```
java -jar target/helloSpringBoot-0.0.1.jar
```

#### Step 3
將 Dockerfile build 起來。由於之後要推上 Docker Hub，所以將名稱改成 Docker Hub 上的 Repo 名稱。
```docker
docker build -t memorykghs/hello-spring-boot:0.0.1 .
docker run -it -p 8080:8080 memorykghs/hello-spring-boot:0.0.1
```

最後在網頁上測試寫好的API：http://localhost:8080/test。

![](/images/docker/100-1.png)

p.s. 如果不小心忘記加上 `-it`，可以按 `alt + F4` 退出容器 console。

## Step 4
使用 `docker container ls` 查看容器運行狀態。
```docker
$ docker container ls
CONTAINER ID   IMAGE                                COMMAND                  CREATED         STATUS         PORTS                    NAMES
203396586b5a   memorykghs/hello-spring-boot:0.0.1   "java -jar /helloSpr…"   3 minutes ago   Up 3 minutes   0.0.0.0:8080->8080/tcp   blissful_elion

```

## 參考
* https://matthung0807.blogspot.com/2019/02/springbootspring-bootjar.html
* https://github.com/spring-guides/gs-spring-boot-docker#initial