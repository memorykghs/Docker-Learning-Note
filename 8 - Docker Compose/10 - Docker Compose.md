# 10 - Docker Compose
Docker Compose 是一個可以定義且可組合多個 Container 成為一個完整服務的工具，因應現在很多的服務背後都是採用分散式系統架構，所以一個為服務放在一個容器中，多個服務就可以使用 Docker Compose 這個工具來管理 ( 但近期可能都轉向 k8s ??? )。

Compose 可以使用 YAML 檔案來進行配置，Docker Compose 也能夠用於各種階段：PROD、STG ( Staging )、DEV、testing，或是 CI 流程中。

使用 Compose 基本上可以遵循 3 步驟：

1. 使用  Dockerfile 來定義應用程式環境，這樣就可以在任何地方用同樣的參數使用。
2. 在 `docker-compose.yml` 檔案中定義應用程式 services，這樣可以讓他們在獨立的環境中一起運行。
3. 執行 `docker compose up` 指令啟動並運行整個應用程式。

也就是說，Docker Compose 最終也是呼叫 `docker run` 指令啟動 Container。另外，Docker Compose 有用於管理應用程序生命週期的指令，包括：
* 啟動、停止和重建服務
* 查看運行服務的狀態
* 以 Stream 的形式輸出正在運行的服務的 Log
* 在服務上運行一次性 ( one-off command ) 的命令

## docker-compose 文件格式
docker-compose 的預設文件格式就是 `docker-compose.yml`。
* [docker-compose version](https://docs.docker.com/compose/compose-file/compose-file-v3/)

```yml
# Part1 - docker compose 的版本
version: "3.9"  # optional since v1.27.0

# Part2 - 撰寫此服務的組成有哪些 container
services:
  web: # web 是自己定義的服務名稱
    build: .
    ports:
      - "8000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis

# 可以指定 volume 將想要的目錄 mount 進來    
volumes:
  logvolume01: {}
```

上面的作法是已經有現成的 Image 了，若系統還沒有建立 Image，也可以使用下面的寫法，這樣一來執行 docker-compose 時，一旦發現沒有要使用的 Image，就會自動先 run dockerfile 建立 Image。
```yml
services:
  web: # web 是自己定義的服務名稱
    build:
      context: . # 指定當前目錄
      dockerfile: Dockerfile-alternate # 指定 dockerfile
      args: # 運行時傳入的參數
        buildno: 1
```

* `context` - 指定當前目錄
* `dockerfile` - 指定目錄下的 dockerfile 名稱
* `args` - build image 時要傳入的參數

## Docker Compose 相關指令
可以參考官網：https://docs.docker.com/compose/reference/

### 執行 docker compose
```docker
# 前景執行
docker-compose up

# 背景執行
docker-compose up -d
```

在資料夾中建立 `docker-compose.yml` 並使用 `docker-compose up` 將服務建立起來。

```yml
version: "3"
services:
    webapp:
        # 指定 Image
        image: httpd:alpine
        ports:
            - "80:80"
        # 掛載一個 volume 到執行容器目錄下的 /data
        volumes:
            - "/data"
```

![](/images/docker/10-5.PNG)

### 啟動 / 停止 docker compose
```docker
# 啟動
docker-compose star

# 停止
docker-compose stop
```

### 移除 docker compose
```docker
# 與 docker-compose up 相反，但只移除掉 container，不移掉 volume
# 移掉 container 再建立一個，Docker 會自動 mount 回來
docker-compose down

# 同步移除 volume
docker-compose down -v
```

![](/images/docker/10-2.PNG)

執行指令後可以看到會順便移除掉 network，因為 docker-compose 在建立的時候會將這些服務放在同一個網路底下，這樣一來他們就可以直接內部溝通。

### 列出 containers
```docker
# 同 docker ps 一樣的功能
docker-compose ps
```
![](/images/docker/10-3.PNG)

### 監控 logs
```docker
docker-compose logs

# follow logs，等 log 進來不斷滾動
docker-compose logs -f
```

### 執行程式
```docker
docker-compose exec webapp sh
```

### 顯示執行程序 ( process )
```docker
docker-compose top
```
![](/images/docker/10-4.PNG)

### 其他
* `docker-compose pause`
* `docker-compose unpause`

## 參考
* https://docs.docker.com/compose/
* https://docs.docker.com/compose/compose-file/#compose-file
* https://askie.today/docker-dockerfile-dockercompose-intro/