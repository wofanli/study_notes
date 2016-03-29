Docker

轻量级的linux开源container。
hardware-agnostic and platform-agnostic.
比VM更轻量级

# before installing

对于linux来说，docker client, docket daemon, container都直接在localhost上跑。
<img src="/linux_docker_host.svg" alt="Linux Architecture Diagram">
<img src="/mac_docker_host.svg" alt="Linux Architecture Diagram">

对于Mac来说，因为docker需要利用一些linux kernel的feature，所以不能直接跑在localhost上。这里的解决方法是创建一个VM, 上面跑linux。然后把docker daemon/container跑在VM上。container的port会映射到VM的port。docker client跑在localhost上。



# Install Docker on Mac

https://docs.docker.com/installation/mac/
1， 下载安装boot2docker/osx-installer 
2，  创建一个vm：
docker-machine create
这步骤只需要运行一次。安装好后，不需要重复创建VM。

2， 启动这个vm
docker-machine start

3， 设置docker的环境变量
eval $(docker-machine env)

$(cmd) 把cmd的运行结果替换到cli里。双引号会把里面的内容当做一个整体，然后再执行。

4， 运行hello-world，verify正确安装好docker了。hello-world是内置好的一个docker image.
docker run hello-world


# Run an image 

docker run docker/whalesay cowsay boo

docker deamon先在local找这个image，找不到的话就在docker hub上找，找到的话，会自动下载下来，然后运行。
https://hub.docker.com/

Note: Image 文件放在哪里？Docker client，还是docker host?
应该是在client

# build an image

## 创建Dockerfile. 这个文件是image的配置文件
~/mydockerbuild:wenxianl$ cat Dockerfile
FROM docker/whalesay:latest
RUN apt-get -y update && apt-get install -y fortunes
CMD /usr/games/fortune -a | cowsay

## 编译image

docker build -t docker-whale .

-t标明image名字。.指定Dockerfile文件位置

~/mydockerbuild:wenxianl$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker-whale        latest              7918fc6a0610        3 minutes ago       274.4 MB
hello-world         latest              690ed74de00f        5 months ago        960 B
docker/whalesay     latest              6b362a9f73eb        10 months ago       247 MB

## mount一个目录到container里

~:wenxianl$ echo "my new site" > index.html
~:wenxianl$ pwd
/Users/wenxianl
~:wenxianl$ docker run -d -P -v $HOME/site:/usr/share/nginx/html --name mysite nginx

~/site:wenxianl$ docker-machine ip
192.168.99.100

~:wenxianl$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                           NAMES
a25444b0edad        nginx               "nginx -g 'daemon off"   10 seconds ago      Up 10 seconds       0.0.0.0:32771->80/tcp, 0.0.0.0:32770->443/tcp   mysite

用浏览器登陆
http://192.168.99.100:32771

如果实时在Host里修改site/里面的内容，会实时反应到container里


## Tag image

docker tag 7d9495d03763 maryatdocker/docker-whale:latest

## login docker hub

docker login --username=maryatdocker --email=mary@docker.com

## push

 docker push maryatdocker/docker-whale

 

# reference

https://docs.docker.com/linux/step_two/
docker-machine ref: https://docs.docker.com/machine/reference/
