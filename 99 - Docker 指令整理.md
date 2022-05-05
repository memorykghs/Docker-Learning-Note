# 99 - Docker 指令整理
## 建立映像檔
```docker
# 於當前目錄，按 Dockerfile.test 中的指令，建立 test:v1 的映像檔
docker build . -f Dockerfile.test -t test:v1
```
* `build` 後面的 `.` 是指將 Image 建立在當前執行的目錄下

## 拉取與推送映像檔
```docker
# 從 docker hub 下載 Image
docker pull node:alpine

# 將拉下來的 Image 再給予新的標籤
docker tag node:alpine ${docker_hub}/node:alpine

# 推送新的標籤至私人倉庫
docker push ${docker_hub}/node:alpine
```

如果出現 `denied: requested access to the resource is denied`，代表沒有登入 docker hub。

```docker
# 登入 docker hub
docker login
```

然後輸入帳號密碼即可。

## 執行容器
```docker
# 跑一個新的 apache 容器
docker run httpd:alpine
```

```docker
# 跑一個背景執行的 apache 容器，並取名為 apache
docker run -d --name apache httpd:alpine
```
* `-d` - daemon ( 守護態執行 )
* `--name` - 為 container 命名

```docker
# 跑一個 redis 容器，啟動虛擬終端機，進入互動模式，在容器中執行 sh
docker run -it --rm redis:alpine sh
```
* `-i` - interactive 互動模式
* `-t` - terminal，將 container 的 terminal 叫出來
* `--rm` - 當容器結束後，將容器從系統移除
* `exit` - 離開容器 terminal，因為有加 `--rm`，所以一離開馬上就會停掉並刪除容器

蠻常使用 `--rm` 參數，因為很常只是想要做測試或是在裡面加東西，避免佔了太多的記憶體，所以一般 run container 時都會加上 `--rm`。

## 列出容器
```docker
# 列出所有正在執行中的容器
docker ps
```

```docker
# 列出所有容器
docker ps -a
```
## 重新啟動容器
```docker
docker restart apache
```
* 若容器原本狀態是 stop，直接啟動容器
* 若容器原本狀態是 start ( 正在運行中 )，則會將容器先停止再啟動

## 強制停止容器
會 kill 指定的 container。
```docker
docker kill apache
```

## 查看容器狀態
```docker
# 查看 CPU、記憶體與網路用量
docker stats 
```

按 `ctrl + C` 可以離開。

## 查看容器內行程 ( process )
```docker
docker top apache
```

## 移除容器
```docker
# 移除 apache
docker rm apache
```

```docker
# 移除所有容器
docker rm -f $(docker ps -a -q)
```
* `-f` - force 強制移除
* `$(...)` - 執行指令的過程中，會先執行用 `$()` 包起來的部分
* `-q` - 只列出 ID

此指令可以將 disk 恢復到原本乾淨的狀態。

## 清除映像檔與容器
```docker
# 停掉全部的 container
docker stop $(docker ps -a -q)

# 刪除所有沒有使用的 container 與 image
docker system prune

# 刪除所有映像檔
docker rmi $(docker images -a -q)

# 清除 volume
docker volume rm $(docker volume ls)

# 刪除所有沒有使用的 container、image 即所有 volume
docker system prune -a -f --volumes
```

## 查看容器 Logs
```docker
# 查看 log
docker logs apache

# 持續查看 ( 指停在當前的 log，不載入新的 )
docker logs -f apache2
```

## 複製檔案
```docker
# 建立一個 hello.html
echo "hello" > hello.html
```
↑ 將 `echo` 後面的值透過 `>` 符號，寫入 `hello.html`。
<br/>

```docker
# 建立 apache
docker run -d --name apache httpd:alpine

# 複製檔案到 apache 容器中
# docker cp ${filename} ${container_name}:${container 指定目錄}
docker cp hello.html apache:/usr/local/apache2/htdocs
```
↑ 使用 `cp (copy)` 指令將 `hello.html` 複製到 apache 容器的 `/usr/local/apache2/htdocs` 路徑下。
<br/>

```docker
# 複製 apache 容器豬的檔案到本機
# docker cp ${container_name}:${file_path/file_name} ${target_path/target_file_name}
docker cp apache:/usr/local/apache2/htdocs/index.html ./index.html
```
↑ 使用 `cp (copy)` 指令將 apache 容器中的 `hello.html` 複製到本機的 `index.html` 中。

## 容器執行指令
執行後會進入容器的 terminal。
```docker
docker exec -it apache sh
```
* `exec` - 執行一段指令

## 掛載目錄
由於檔案複製來複製去得極為不便，所以將本機目錄 mount 在容器，或是將容器的某個檔案掛載到本機環境下。當黨名相同時，會覆蓋過去。
```docker
docker run -d --name apache2 -v $PWD/hello.html:/usr/local/apache2/htdocs/index.html httpd:alpine

cp apache2:/usr/local/apache2/htdocs/index.html ./index.html

# 比較兩個檔案
diff index.html hello.html
```
* `-v` - 使用 docker volume 掛載檔案 
* `$PWD` - 執行後會轉換為絕對路徑，指當前值型的目錄下

## 建立外部連線
```docker
docker run -d -v $PWD/hello.html:/usr/local/apache2/htdocs/index.html -p 80:80 httpd:alpine
```

使用 http://127.0.0.1:80 就可以連到本機上的服務。 


> Q. 可以從容器中連到本機上的服務嗎?

可以 ( 但較少用 )，在容器中使用 `host.docker.internal` 就可以連接本機上的服務，是一個 Docker 提供的 DNS。

如在本機環境裝一個 MySQL ( 非 Container )，要將 Container 中的 MySQL 的 port 改為 `host.docker.internal.${MySQL port}` 就可以連線出來。