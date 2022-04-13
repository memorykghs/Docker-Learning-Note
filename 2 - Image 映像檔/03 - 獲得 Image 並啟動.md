# 03 - 取得映像檔
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
## 取得映像檔
```docker
docker pull nginx

# 效果同上，冒號後面可以指定版本
docker pull nginx:latest

# 查看現在有的 image
docker images
```
![](/images/docker/3-1.png)<br/>
![](/images/docker/3-2.png)

如果官方倉庫註冊服務器下載較慢，可以從其他倉庫下載，但必須指定完整的倉庫伺服器位址，如：
```docker
docker pull dl.dockerpool.com:5000/ubuntu:12.04
```

#### 使用拉下來的 Image 建立 Container
```docker
# 啟動 container
# -d：在背景直行，不要阻擋下指令的視窗
# -p：指定內外 port 的映射
docker run -d -p 80:80 nginx
docker run -d -p 81:80 nginx
```
如果 run Continer 時沒有指定 `TAG`，預設使用 `latest`。

```docker
# 查看現有的 container 運行狀態
docker ps
```
![](/images/docker/3-3.png)

到目前為止已經使用 `pull` 及 `run` 指令將 image 拉下來、建立容器並啟動。啟動後，可以在網址列上輸入 `localhost:80` ( 或是替換成自己的 port 號 )，就可以看到預設的畫面。<br/>

![](/images/docker/3-5.png)

##### Step 3
```docker
# 對 nginx 這個 container 進行修改
# docker exec -it <container_id> bash
docker exec -it 34 bash
```

其中 `container id` 只要可以辨別現有正在 run 的容器即可，不需要打太長。輸入指令後可以發現角色已經切換到 container 中 ( 就是最下面那行 `root@34e023e61ff1:/#` )。<br/>

![](/images/docker/3-4.png)

#### Step 4
接著修改 html 文件。

```docker
cd /usr/share/nginx/html/

cat index.html

# 修改 index.html 文件
echo hello > index.html
```

![](/images/docker/3-6.png)<br/>

![](/images/docker/3-7.png)

此時重新整理網頁，會發現內容已經被我們改掉了!<br/>

![](/images/docker/3-8.png)

#### Step 5
要退出 container 的輸入區的話，打 `exit` 就可以回到 docker 的指令區。

```docker
# 刪除容器
docker rm -f
```

![](/images/docker/3-9.png)

#### Step 6
```docker
# 指定某個映像黨的名稱
docker commit 34 my-image
```

![](/images/docker/3-10.png)


## 參考
* [Docker 入門](https://www.youtube.com/watch?v=AASsRoUfisk)