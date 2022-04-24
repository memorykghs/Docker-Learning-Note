# 09 - Docker Volume 資料卷
執行 Docker Container 的時侯，檔案系統會分為 Image 層、Init 層以及可讀可寫層這三個部份。把容器刪除後，存放在上面的資料也就會跟著刪除掉，這時候可以將不想要被刪除的資料存放在實體機器上，避免資料不見。

## Docker Volume 相關指令
| 指令 | 描述 |
| --- | --- |
| `docker volume ls` | 列出逤有的 volume |
| `docker inspect -f '{{.Mounts}}' ${container_id}` | 查詢實體機資料夾所在位置 |

## 使用 Docker Volume
使用 Docker Volume 功能將資料寫入實體機的方法主要有兩個：

1. `docker run` 時加上 `-v` 參數，讓容器內的檔案路徑 mapping 到實體機的檔案路徑。

    ```docker
    # docker run -it -v ${實體機路徑}:${容器路徑}
    docker run -it -v /home/user1/storage:/storage centos /bin/bash
    ```
    * `/home/user1/storage` - 實體機的資料夾路徑
    * `/storage` - 容器的資料夾路徑
<br/>

2. 撰寫 Dockerfile 時加上 `VOLUME` 指令，把資料存放在時體積上。使用此方法需要搭配 `docker inspect`，才能在容器啟動後查到實體機檔案的存放路徑。

    ```docker
    FROM centos
    VOLUME ["/storage"]
    ```

    也可以寫多個路徑：
    ```docker
    VLOUME ["/storage1", "/storage2", "/storage2"]
    ```

## 參考
* https://ithelp.ithome.com.tw/articles/10192397
* https://ithelp.ithome.com.tw/articles/10192703