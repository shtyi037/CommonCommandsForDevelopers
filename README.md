# docker/docker-compose/redis/git/linux指令整理
[TOC]

## GIT 常用指令
設定類
```
git config --list # 檢查讀者的設定
git config --global user.name "Bocky.yu" # 初次設定:名稱 
git config --global user.email bocky.yu@itstar.tw # 初次設定:信箱
cat ~/.ssh/id_rsa.pub # 查看git key
```

操作類
```

git checkout  # 有兩個功能，一個是branch, HASH 間的切換，另外一個則是工作區檔案的還原。
git checkout .  # 還原所有檔案
git checkout apple.html  # 還原一個檔案

git remote update  # 檢查遠端是否有更新
git branch  # 檢查local分支
git branch -a  # 檢查雲端最新分支
git checkout -b <branch> # 同時建立分支和切換。
git branch "featrue/#9999999" # 建立featrue/#9999999分支
git push origin "featrue/#9999999" # push featrue/#9999999分支上ogigin
git branch -d "branch_name" # 移除branch
git push origin :featrue/#9999999 # 移除branch

git status # 查看git 目前資料狀態

git stash  # 備份當前的工作區
git stash save abcdQA# 
git stash list  # 顯示git內的所有備份，可以利用這個列表來決定從那個地方恢復。
git stash apply stash@{0}  # 把Stash撿回來用
git stash drop stash@{0}  # 從列表裡刪掉暫存的Stash了
git stash clear  # 刪除所有暫存的操作

git reset --hard  # 回復到最新提交版本
git reset e12d8ef^  # 拆掉commit的版本

git add -u  # 加入所有被更動的檔案（包含 modified 及 deleted）
git log  # 查看提交歷史紀錄
git log --stat  # 查看提交的文件名稱
git log -p <filename> 查看某個文件的修改歷史

git pull origin  # 從遠端更新

git push origin my-tag  # push 指定標籤 (多個標籤以空白隔開)
git push --tags  # push 所有 tag

git diff  # 檢查修改內容
git remote -v  # 查詢遠端pull、push路徑
```

submodule:
```
git submodule init
git submodule update
```


### tag相關

```
git tag -l  # 查詢所有tag名稱
git tag -a platform-202102w402 -m ""  # 新增一個名為platform-202012w402的tag
git push origin platform-202102w402  # push 指定標籤 (多個標籤以空白隔開)
# git push origin --tag  # push 所有 tag
git push --tags  # push 所有 tag

```


## docker / docker-compos常用指令

指令解說:
* -a: 選項用於列出所有容器，包括停止運行的 
* -q:列出鏡像或容器的數字ID

```
docker --version # 查docekr版本,版本會與yml內的version有關
docker # 查docker相關指令


====image相關====
docker pull mysql:latest # pull image:tag
docker build -t npay_server .  # 建立鏡像(利用該路鏡下的Dockfile建立)
docker image ls # 查詢所有鏡像
docker rmi <imageId> # 刪除鏡像
docker rmi $(docker images -q) # 刪除所有鏡像 !!!慎用
docker tag <IMAGEID> <REPOSITORY>:<TAG> # image改名  範例:docker tag 494c6ec1a4d3 ocmsImage:v1
docker images -q # 顯示所有image的image-id
docker rmi $(docker images -q)  # 刪除所有image !!!慎用
docker image inspect <IMAGEID>  # 查看鏡像資訊

====container相關====
docker container run --name some-mysql -e MYSQL_ROOT_PASSWORD=123456 -d -p 3306:3306 -v mysql-data:/var/lib/mysql mysql:latest # 創建出容器，並將mysql的密碼設為123456


docker container ls -a # 列出所有容器;
docker container stop <CONTAINER_ID> # 中止容器(單一)
docker stop $(docker ps -a -q)  # 停止所有容器運行
docker container start <CONTAINER_ID> # 啟動容器
docker container restart <CONTAINER_ID> # 重啟容器
docker rm -f <CONTAINER_ID> # 強制移除執行中的 container，可以加入 -f
docker stop $(docker ps -a -q) # 中止所有 container !!!慎用
docker rm $(docker ps -a -q) # 移除所有 container !!!慎用
docker exec -it <CONTAINER_ID> bash # 進入容器

docker container run -d --name redis-server --network demo-network redis #創建redis container

====network相關====
docker network ls # 查找網路ID
docker network rm c520032c3d31 # 刪除網路
docker network prune # 删除所有未使用的網路

====volume相關====
docker volume ls # 查詢volume
docker volume inspect mysql-data # 查詢mysql-data的volume資訊


docker logs -f 容器名 # 查看容器的log
docker inspect 容器名 # 查看容器訊息


make shell   
make shell NAME=redis  # 進入容器

```

```
docker-compose --version # 查docekr-compose版本 
docker-compose # 查docker-compose相關指令
docker-compose up --build db
docker-compose pull # 只pull dockerhub的image
docker-compose up -d # 背景執行
docker-compose up -d --build # 有修改鏡像文件時，需要重新構建image
docker-compose up -d --remove-orphans # 刪除不需要使用的service
docker-compose stop # 停止應用相關的所有容器
docker-compose restart # 重啟
docker-compose rm  # 刪除已停止的Compose 應用
docker-compose ps  # 輸出內容包括當前狀態、容器運行的命令以及網絡端口
docker-compose down  #  停止並刪除運行中的Compose 應用 !!!慎用,資料會不見

更新文件指令:
docker-compose build # 只build dockerfile 的image
```

dockerfile 的命令摘要
```
FROM image從那裡來
MAINTAINER 鏡像維護者信息
RUN 構建鏡像執行的命令，每一次RUN都會構建一層
CMD 容器啟動的命令，如果有多個則以最後一個為準，也可以為ENTRYPOINT提供參數
VOLUME 定義數據卷，如果沒有定義則使用默認
USER 指定後續執行的用戶組和用戶
WORKDIR 切換當前執行的工作目錄
HEALTHCHECH 健康檢測指令
ARG 變量屬性值，但不在容器內部起作用
EXPOSE 暴露端口
ENV 變量屬性值，容器內部也會起作用
ADD 添加文件，如果是壓縮文件也解壓
COPY 添加文件，以復制的形式
ENTRYPOINT 容器進入時執行的命令
```

## redis

```
redis-server --version 或 redis-server -v # 查服務端版本
redis-cli --version  或 redis-cli -v# 查看客户端版本
info # redis內查版本
redis-server # 啟動服務端
redis-cli # 啟動客戶端
service redis-server status # 檢查服務狀態
service redis-server stop # 停止redis server
service redis-server start # 啟動redis server
```

```
select 0~15 # 切換DB, 預設16個
keys *  # 查看所有key
dbsize  # 查看key的總數
```


pub/sub
```
pubsub channels * # 查詢所有channels
subscribe channel_name # 訂閱1個channel
subscribe channel_name_1 channel_name_2 # 訂閱多個channel

unsubscribe channel_name  # 退訂

pubsub channels channels_name* # 或使用通配符名稱查詢channels
pubsub numsub channel_name # 返回頻道訂閱者的數量
publish runoobChat "發送的訊息" # runoobChat發送訊息message出去給所有訂閱者


```


## nginx

```
sudo systemctl status nginx  # 查看nginx server的狀態
sudo systemctl stop nginx  # 停止 Nginx
sudo systemctl start nginx  # 開啟Nginx
sudo systemctl restart nginx  # 強制重新執行nginx

sudo nginx -t # 測試 Nginx 配置 正常則顯示下方
----------
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
----------
```

## ubuntu 系統常用指令
```
sudo su -  # 切換root
pwd # 印出目前所在位置
ps aux  # 搜尋正在執行的行程
kill -9 pid  # 殺掉執行中的行程(PID需自改)

shutdown -r now # 整台ubuntu/linux重開機
shutdown -h now # 整台ubuntu/linux關機

# 刪除資料夾和檔案的命令
-r 就是向下遞迴，不管有多少級目錄，一併刪除
-f 就是直接強行刪除，不作任何提示的意思
rm -rf 資料夾名 # !!!慎用, 資料夾會直接全部移除
sudo fuser -k 80/tcp # 停止使用端口 80 的進程

netstat | grep 80  # 查port 80
netstat -tulpn | grep :80

sudo kill -9 $(lsof -t -i:8080) # 刪除8000prot 的進程 # 將該kill命令與lsof（打開的文件列表）命令結合使用
lsof -t -i:8080 | xargs kill -9  # 
```

scp

```
scp -r /path/directory1  user@192.168.1.1:/path/directory2   # 將本地端的目錄1複製到遠端目錄2
scp -p nadi@20.48.113.161:/home/nadi/server/foundation_service/main_service/Middleware_TPKC.py C:\da820  # 遠端的檔案保留時間與權限複製到本地端
```

查找
```
whereis python3 # 路徑
```

### 建議安裝

編輯文字用
```micro
cd /usr/bin
curl https://getmic.ro/r | sudo sh
micro 檔名

curl https://getmic.ro | bash
```

安裝yum
```
sudo -s 
apt-get install build-essential
apt-get install yum
```

Debian / Ubuntu 安裝netstat
```
apt install net-tools
```
## windows 系統常用指令

```
tree # 顯示資料格式
tree /f # 顯示完整資料副檔名
```

## pycharm

```
所有代碼折疊： ctrl + alt + -
所有代碼展開：ctrl + alt + +
折疊某一點：ctrl + -
展開某一層：ctrl + +
```

## iTerm2



## vim

```
vi 看 log
:AnsiEsc  # 可以讓AsciiColor Code變成有顏色的代碼

```
