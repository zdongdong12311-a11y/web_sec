docker pull kalilinux/kali-rolling

docker run -it --name my-kali-new \
  --privileged \
  --net=host \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  kalilinux/kali-rolling /bin/bash


docker ps -a
docker start names              # 启动容器
docker exec -it names /bin/bash  # 进入容器

# 在容器内部执行
apt update
apt install -y net-tools iputils-ping vim curl bash-completion
apt update
apt install -y kali-linux-headless
apt update
