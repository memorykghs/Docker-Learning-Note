# 99 - Docker 指令整理
## docker build
```docker
docker build . -t [image_name] -f [Dockerfile]
```
* `-t` - 表示建立的 image 名稱。
* `-f` - 指定讀取的 Dockerfile 檔，若沒指定預設讀取 Dockerfile。
* `--no-cache` - 避免在 Build Docker image 時被 cache 住，而造成沒有 build 到修改過的 Dockerfile