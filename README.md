# Docker-Learning-Note

## 學習資源
* [Docker 官方文件](https://docs.docker.com/engine/reference/run/)
* https://cwhu.medium.com/docker-tutorial-101-c3808b899ac6
* https://philipzheng.gitbook.io/docker_practice/introduction/what

## Cheatsheet
* [Docker CLI cheatsheet](https://devhints.io/docker)
* [Dockerfile cheatsheet](https://devhints.io/dockerfile)
* [docker-compose chearsheet](https://devhints.io/docker-compose)

---

## 章節
#### 1 - Docker 簡介與基本概念

#### 2 - Image 映像檔

#### 3 - Dockerfile 

#### 4 - Docker Hub 與 Repository

#### 5 - Container 容器

---

## 其他
#### MySQL
* [在 host 端連接 Docker MySql Container](http://abrazotech.blogspot.com/2018/10/hostdocker-mysql-container.html)
* [MySQL 設定參考](https://myapollo.com.tw/zh-tw/docker-mysql/)

---
## 待解決 
- [ ] Image 與其檔案類型(amd???) (OS不同系統：arm、X86)? 
與 Docker 的 nulti-CPU architecture support 有關 
https://docs.docker.com/desktop/multi-arch/

- [ ] 呈上，如果 build image 沒有 support multi architecture，那就有可能在 run 的時候，Docker 會模擬出一個 X86 的環境在 arm 上面跑，但是會使得效能極慢。所以有另外一個 BuildX 可以使用。
