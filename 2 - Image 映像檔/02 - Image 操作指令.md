# 02 - Docker Image 操作指令
## 1. 列出 image 基本資訊
```docker
docker images
docker image ls
docker image ls -a
```
以上三個功能完全相同，因為預設會幫忙帶入 `-a` 的指令。所以不需要特別加，Docker 都會秀出所有 Image。
![](/images/docker/2-1.png)

## 2. 使用 Tag 列出特定 Image 資訊
`TAG` 用來標記來自同一個倉庫的不同映像檔，而 `ID` 則是映像檔的唯一標識。

```docker
# docker images [image_name] 或 docker image ls [image_name]
docker images nginx
docker image ls nginx

# 利用 tag 資訊
#docker image ls [image_name]:[tag]
docker image ls nginx:latest

# docker images [image_name]:[tag]
docker images nginx:latest
```
![](/images/docker/2-2.png)

## 3. 列出 Image 完整資訊
```docker
# --no-trunc 顯示完整資訊
docker images --no-trunc
docker image ls --no-trunc
```
![](/images/docker/2-3.png)

## 4. 查詢 Digest 值
```docker
docker images --digests
docker image ls --digests
```
![](/images/docker/2-4.png)

###### 關於 Digest
* 是 Docker 1.6 和 registory 2 加入的資訊，是用來識別 Image 的 SHA 值
* SHA 值是經由 SHA ( 全名Secure Hash Algorithm 安全散列函式 ) 算出的值，被廣泛使用在資安認證上
* SHA 同時也是 FIPS 所認證的安全雜湊演算法
* 補充：[指令可用選項](https://blog.yowko.com/docker-images-command/)

## 5. 透過 Filter 篩選
使用 Filter 選項指令如下：
```docker
-f
--filter

# 後續可以接應篩選字串
-f=dangling=true
```
![](/images/docker/2-5.png)

* dangling 的意思是"懸著的"，代表那些沒有 TAG 的 image，通常發生於自行建立的 image 上。
* 系統判定這些未被 TAG 的 image，不被用於任何 Images Layer 中，同時也占用了磁碟空間，因此註記為 dangling。

* 可以做的動作是將其 TAG、保持不變或刪除。

#### since & before：
```docker
# docker images -f=before=[ImageName:TAG]
docker images -f=before=nginx

# docker images -f=since=[ImageName:TAG]
docker images -f=since=redis
```
![](/images/docker/2-6.png)

* `before` - 篩選列出某個 image 往前的其他 image
* `since` - 篩選列出某個 image 往後的其他 image

#### reference
```docker
# docker images -f=reference=要查詢的字串
docker images -f=reference=
```
![](/images/docker/2-7.png)

像是 OS、DB 等這類型的 images 有可能會下載不只一個版本，這時候就可以利用 `reference` 來篩選同個來源的 images。

## 參考
* https://hackmd.io/VXlsyHahSMOTT1CPLy_iyw