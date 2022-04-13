# 05 - Dockerfile - 1
當使用 Image 建立 Container 後，有時候需要進入 Docker Container 裡面下一些指令安裝程式或是修改設定檔，起多個 Container 就需要重複執行動作不少次。

而 Dockerfile 內可以包含一些啟動 Container 之後要執行的指令，之後就使用 `docker build` 指令來建立 Image 即可。

Dockerfile 是由一行一行的命令語句組成，並支援以 `#` 開頭的註解行。一般而言，Dockerfile 分為四部分：
1. 基底映像檔資訊
2. 維護者資訊
3. 映像檔操作指令
4. 容器啟動值型指令

Dockerfile 中每一條指令都會建立一層映像檔，例如：
```docker
# 基本映像檔，必須是第一個指令
FROM ubuntu

# 維護者： docker_user <docker_user at email.com> (@docker_user)
MAINTAINER docker_user docker_user@email.com

# 更新映像檔的指令
RUN echo "deb http://archive.ubuntu.com/ubuntu/ raring main univ
erse" >> /etc/apt/sources.list
RUN apt-get update && apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf

# 建立新容器時要執行的指令
CMD /usr/sbin/nginx
```

* 一開始必須指明作為基底的映像檔名稱，接下來說明維護者資訊。
* 接著是映像檔操作指令，例如 `RUN` 指令， `RUN` 指令將對映像檔執行相對應的命令。
* 每運行一條 `RUN` 指令，映像檔就會新增一層。
* 最後是 `CMD` 指令，指定執行容器時的操作命令。

## 指令
指令的一般格式為 `INSTRUCTION arguments`，指令包括
`FROM`、`MAINTAINER`、`RUN` 等。

#### FROM
Dockerfile 中第一條指令必須為 `FROM` 指令，如果要在同一個 Dockerfile 中建立多個映像檔時，可以使用多個 `FROM` 指令 ( 每個映像檔一次 )。
* 格式：`FROM <image>` 或 `FROM <image>:<tag>`。
  ```docker
  FROM scratch # 製作 base image
  FROM ubuntu:14.04 # 使用 base image
  ```
* 告訴 Docker 使用哪個映像檔作為基底。
* 基於安全性的問題，盡量使用官方 Base Image

#### MAINTAINTER
* 格式：`MAINTAINER <name>`，指定維護者訊息。

#### LABEL
設定映像檔的 Metadata 資訊，有許多種的 `<key>` 可選用，可以在這邊寫入較多關於此 Dockerfile 的資訊。

* 格式：`LABEL [<key>=value]`
  ```docker
  LABEL maintainer="12345@gmail.com"
  LABEL version="1.0"
  LABEL description="This is description"
  ```

* 通常建議寫 LABEL 幫助了解這個 Image

#### RUN
每條 RUN 指令將在當前映像檔基底上執行指定命令，並產生新的映像檔。
* 格式：`RUN <command>` 或 `RUN ["executable", "param1", "param2"]`。
<br/>

  * `RUN <command>`：在 shell 終端中運行命令，即 `/bin/sh -c`
  * `RUN ["executable", "param1", "param2"]`：使用 exec 執行。
  <br/>
  
* 指定使用其它終端可以透過第二種方式實作，例如 `RUN ["/bin/bash", "-c", "echo hello"]`。
* 當命令較長時可以使用 `\` 來換行，避免建立多層 Image。
* 使用 `&&` 串聯多個 `RUN` 指令，因為每個指令都會建立一層 Layer，使用 `&&` 語法串聯可以避免新增過多層的 Layer。

在 `build image` 時，可以使用一般常見的 shell 指令和 exec 指令，詳細可以參考官網的 [RUN 解說](https://docs.docker.com/engine/reference/builder/#run)。

> 簡單來說 EXEC 的指令下法可以避免一些預設的 shell 指令，而選擇想要的 shell 指令進行操作，此外還須注意 EXEC 執行的程式碼，必須使用 雙引號 的格式框住指令。

#### WORKDIR
```docker
# 如果沒有會自動建立該目錄
WORKDIR /root

# 多行最終結果為串接後的路徑，下面例子即為：/test/demo
WORKDIR /test
WORKDIR demo
```

* 使用 `RUN cd` 也可以改變目錄，但非常不建議
* 盡量使用絕對路徑

#### ADD 與 COPY
```docker
# 將 hello 文件加入根目錄
ADD hello /

# 將 hello 文件加入根目錄下的 test 資料夾，即 /root/test/hello
WORKDIR root/
ADD hello test/ 

# 將檔案放到跟目錄並解壓縮
ADD test.tar.gz /

# 複製 hello 文件到根目錄下的 test 資料夾
COPY ${source_path} ${target_path}
COPY hello test/
```

* `ADD` 跟 `COPY` 都可以將檔案加入指定資料夾
* `ADD` 與 `COPY` 的區別在於，`ADD` 除了可以將檔案加入指定目錄，還可以解壓縮
* 大部分的情況 `COPY` 比 `ADD` 好
* 在使用 `COPY` 時，若是使用相對路徑要特別注意，該路徑是相對於執行 Docker 命令時所在的位置，而不是 Dockerfile所在的位置。等於目標路徑就是相對於 `WORKDIR` 所設定的當前路徑。

#### ENV
* 格式：`ENV <key> <value>`
* 用來設定環境常數，可以被後續的 `RUN` 指令使用，並在容器運行時保持

下面的例子在執行 RUN 時同時把環境常數帶入，這樣就只需要修改 Dockerfile 的 ENV 的值就好，不需要一個一個做修改。
```docker
ENV MYSQL_VERSION 5.6
RUN apt-get install -y mysql-server="${MYSQL_VERSION}" \
    && rm -rf /var/lib/apt/lists/*
```

## 參考
* https://hackmd.io/FriZ-QVpQd6FC7OrzMsZzw
* https://www.aurotek.com.tw/uploads/files/hello.pdf

#### 影片
* https://www.youtube.com/watch?v=BTUffhYLsvc&list=PLwIrqQCQ5pQkSTTzJU6ljaaaou-_Iq9N_&index=21