# 08 - Docker 網路基本概念
## 實現原理
Docker 的網路實作其實就是利用了 Linux 上的網路命名空間和虛擬網路設備 ( 特別是 veth pair )，在宿主機虛擬一個 Docker 容器網橋 ( docker0 )。Docker 啟動時會根據網橋的網段分配給容器一個 IP 位址，稱為 Container IP。

Docker 網橋在網路上並非真實存在，而是由宿主機虛擬出來的，所以外部網路無法直接使用 Container IP 訪問到容器。如果要讓容器能夠被外部訪問，可以透過 mapping 容器的 port 到宿主主機。

也就是 `docker run` 建立容器的時候使用 `-p` 或 `-P` 來映射，訪問容器時可以使用 `${宿主機 IP}:${conatiner_port}` 指令進行訪問。

而容器之間可以通過 Container IP 直接通訊，是因為 Docker 網橋是每個容器的預設閘道，在同一個宿主機內的容器都會接入同一個網橋。

##　網路模式
Docker 在建立容器的時候，會執行以下操作：
1. 建立一對虛擬界面，分別放到本地主機和新容器中
2. 本地主機一端橋接到預設的 `docker0` 或指定橋接器上，並具有一個唯一的名字，如 `veth65f9`
3. 容器一端放到新容器中，並修改名字作為 `eth0`，這個界面只在容器的命名空間可見
4. 從橋接器可用位址段中取得一個未使用位址分配給容器的 `eth0`，並設定預設路由到橋接網卡 `veth65f9`

完成這些之後，容器就可以使用 `eth0` 虛擬網卡來連接其他容器和其他網路。

使用 `docker run` 指令建立容器時，可以透過 `--network` 或是 `--net` 參數來指定想要建立的網路模型，可以分為以下 4 種：
1. None
2. Host
3. Bridge ( 預設 )
4. Container:$ID

#### 檢查網路模式
可以透過以下指令檢查當前的網路模式。
```docker
docker network ls
```

### None
Docker Engine 不會管理任何網路功能，即此容器不會有網路卡、IP、路由等資訊，沒有對外上網的能力，但 Container 會有自己的隔離網路空間 ( Network Nanespace )。

要讓外部訪問到的話需要自行為容器新增網路卡、配置 IP。

```docker 
docker run --net=none -d --name none memroykghs/hell-ospring-boot:0.0.1
```

### Host
容器會和宿主機共用同一個 Network Namespace，不會有自己獨立的，即不需要網路隔離，和宿主機共用相同的網路模型即可。

容器也不會有自己的 IP，而是使用宿主機的 IP 和 port。使用此模式的好處就是容器可以使用宿主機的 IP 地址與外部通訊，內部服務的 port 也可以使用宿主機的 port。

```docker 
docker run --net=host -d --name none memroykghs/hell-ospring-boot:0.0.1
```

## 小結
| 網路模式 | 說明 |
| :---: | --- |
| None | Docker Engine 不會管理任何網路功能，但 Container 會有自己的隔離網路空間 ( Network Nanespace ) |
| Host | 和宿主機共用 Network Nanespace、IP 與 port |

## 參考
* https://philipzheng.gitbook.io/docker_practice/underly/network
* https://www.hwchiu.com/docker-network-model-lab.html