# 04 - Create Image
建立映像檔有很多方法，可以從 Docker Hub 取得已有映像檔並更新，或是在本機建立一個。首先從修改已存在的映像檔開始。

## 獲得映像檔並啟動
如果沒有映像檔，Docker Hub 上有一個 hello-world image 可以使用，先把這個 Image pull 下來。
```docker 
docker pull hello-world
```

使用下載的映像檔啟動容器。

```docker
docker run -t -i redis /bin/bash
```

* `-i` - 以交互模式運行容器，通常和 `-t` 一起使用。
  > flag allows you to make an interactive connection by grabbing the standard in (STDIN) of the container.


* `-t` - 為容器重新分配一個偽輸入中端，簡而言之就是進入容器的終端，通常與 `-i` 一起使用。
  > flag assigns a pseudo-tty or terminal inside the new container.

* `-d` - 背景執行

* `/bin/bash` - 在 Container 中啟動一個 bash shell
  > launches a Bash shell inside our container

![](/images/docker/4-1.png)

按 `Ctrl + C` 可以退出。

## 利用 Dockerfile 建立 Image
使用 `docker build` 來建立一個新的映像檔，所以在此之前要先建立一個 Dockerfile。

#### Step 1
首先新增一個 java 專案的目錄。
```docker
mkdir -p DockerLab/java-docker-app
cd DockerLab/java-docker-app
```

#### Step 2
接著隨便寫一個 java 檔案丟到此目錄下，寫下下面 java 內容後儲存檔案。
```
vim Hello.java
```
```java
class Hello{  
    public static void main(String[] args){  
        System.out.println("This is first java app by using Docker");  
    }  
}
```

#### Step 3
建立一個 Dokcerfile 文件。
```
# 作法一
touch Dockerfile
vi Dockerfile

# 作法二
vim Dockerfile
```

撰寫 Dockerfile 內容如下：
```docker
FORM java:8
COPY . /DockerLab/java-docker-app
WORKDIR /DockerLab/java-docker-app
RUN javac Hello.java
CMD ["java", "hello"]
```

Dockerfile 中每一條指令都會建立一層映像檔。

* `FROM` - 告訴 Docker 使用哪個映像檔作為基底，上面的例子後面接著是維護者的信息。
* `RUN` - 開頭的指令會在建立中執行，比如安裝一個套件 ( Linux 環境可以使用 `apt-get` 來安裝了一些套件 )。
* `COPY` - 複製本地端的 `<src>` ( 為 Dockerfile 所在目錄的相對路徑 ) 到容器中的 `<dest>`。
* `WORKDIR` - 為後續的 `RUN`、`CMD`、`ENTRYPOINT` 指令指定工作目錄。
* `RUN` - 在當前映像檔基底上執行指定命令，並產生新的映像檔。
* `CMD` - 指定啟動容器時執行的命令，每個 Dockerfile 只能有一條 `CMD` 命令。

上面的例子是以 java8 為基底映像檔，如果要建立基底映像檔，則要寫成下面這樣：
```docker
# scratch 代表不是任何一個基底 Image
FORM scratch
```

## Docker Build
完成 Dockerfile 後可以使用 `docker build` 建立映像檔。其中 `java-app` 是映像檔的名稱，可以自行更改。
```docker
docker build -t java-app . --no-cache
```

* `-t` - 表示建立的 image 名稱。
* `-f` - 指定讀取的 Dockerfile 檔，若沒指定預設讀取 Dockerfile。
* `--no-cache` - 避免在 Build Docker image 時被 cache 住，而造成沒有 build 到修改過的 Dockerfile

![](/images/docker/4-3.png)

最後啟動剛剛建立好的 Image。
![](/images/docker/4-4.png)

## 上傳映像檔
使用者可以通過 docker push 命令，把自己建立的映像檔上傳到倉庫中來共享。例如，使用者在 Docker Hub 上完成註冊後，可以推送自己的映像檔到倉庫中。

不過在推送 Image 之前，要先產生 token 進行登入，可以參考 [04.1 - Manage Access Token](https://github.com/memorykghs/Kubernetes-Learning-Notes/blob/main/01%20-%20Docker/04.1%20-%20Manage%20Access%20Token.md)。

如果建立的 Image 沒有 Tag，無法上傳，所以要先將 Image 打上 Tag。注意這邊的 Image Name 要跟要上傳的目的 Repo 名稱一樣，才有辦法上傳到指定位置。
```docker
# docker image tag ${image_id} ${image_name}:${tag_name}
docker image tag 5cb8cb0107c0 memorykghs/docker-playground:my-redis

# 將上面的 java-app 改成我們要推上去的 tag
docker image tag 870e8b26cc5b memorykghs/docker-playground:java-app
```

![](/images/docker/4-6.png)

```docker
# docker push ${user_name}/${repo_name}:${tag_name}
docker push memorykghs/docker-playground:java-app
```
p.s. Docker Hub 上的 Repository 都必須是小寫，不能出現大寫，但可以包含一些特殊符號。

![](/images/docker/4-7.png)

上傳成功後到自己的 Docker Hub 上面看就會出現啦~

![](/images/docker/4-8.png)

## 刪除 Image
```docker
# docker rmi ${image_name} ${image_id}
docker rmi memorykghs/docker-playground

# docker rmi ${image_id}
docker rmi 5cb8cb0107c0
```
`rmi` 代表要 `rm` 的是 Image。如果是 `rm`，那麼移除的會是容器。

## 歷史
```docker
# docker history ${image_id}
docker history 2062d58f112d
```
可以看到此 Image 中的分層。因為 Base Image 是 java8，所以會連帶該基底映像檔資訊都列出來。

![](/images/docker/4-9.png)

## 參考
* [菜鳥教程 - Docker run 命令](https://www.runoob.com/docker/docker-run-command.html)
* https://www.1ju.org/docker/docker-java-example
* https://blog.csdn.net/dongdong9223/article/details/52998375
* https://ithelp.ithome.com.tw/articles/10191016?sc=hot
* https://joshhu.gitbooks.io/dockercommands/content/DockerImages/CommandArgs.html

#### Tag
* https://itnext.io/docker-tips-about-none-images-39fb34b20bc5
* https://stackoverflow.com/questions/41984399/denied-requested-access-to-the-resource-is-denied-docker