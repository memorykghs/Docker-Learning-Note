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

上面的作法是已經有現成的 Image 了，若尚未建立 Image，也可以使用下面的寫法，這樣一來執行 docker-compose 時
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
* `args` - 傳入參數

## Docker Compose 相關指令
* `docker-compose start`
* `docker-compose stop`
* `docker-compose pause`
* `docker-compose unpause`
* `docker-compose ps`
* `docker-compose up`
* `docker-compose down`

## 參考
* https://docs.docker.com/compose/
* https://docs.docker.com/compose/compose-file/#compose-file
* https://askie.today/docker-dockerfile-dockercompose-intro/