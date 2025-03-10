# ♾️常用命令
## 💫docker
- docker image ls

> 启动容器

- docker start my-container
- 进入命令行: docker exec -it my-container /bin/bash

# ♾️常见问题
## 💫如果容器中没有运行的程序, 会自动关闭
例如一个ubuntu镜像,需要这样run
```shell
docker run -it ubuntu /bin/bash
```
赋予该容器一个终端命令的线程

或者不让他休眠
```shell
docker run -d --name my-container ubuntu sleep infinity
```

## 💫将本地文件复制到容器中
- docker cp <本地路径> <容器名称或ID>:<容器路径>

## 💫解压
- tar -xzvf example.tar.gz
- tar -xzvf <文件名>.tar.gz -C <目标目录>






























