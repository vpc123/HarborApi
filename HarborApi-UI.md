## 为harbor部署swagger-ui


有了swagger，所有REST API就一览无余了。

Harbor官方已经准备好了部署脚本，部署起来其实特别简单，这里仅作记录。


1、下载部署脚本和swagger文件到本地harbor目录。

    # cd /docker/harbor
    # wget https://raw.githubusercontent.com/vmware/harbor/master/docs/prepare-swagger.sh https://raw.githubusercontent.com/vmware/harbor/master/docs/swagger.yaml


2、将部署脚本里的SCHEME和SERVER_IP改成harbor的访问协议和地址。


    # vim prepare-swagger.sh
    SCHEME=https
    SERVER_IP=harbor.xxx.com


3、给予执行权限。

    # chmod +x prepare-swagger.sh


4、执行脚本。

    ./prepare-swagger.sh

5、修改docker-compose.yml文件，添加两行。

    # vim docker-compose.yml
    ui:
    image: vmware/harbor-ui:v1.2.0
    container_name: harbor-ui
    env_file:
      - ./common/config/ui/env
    restart: always
    volumes:
      - ./common/config/ui/app.conf:/etc/ui/app.conf:z
      - ./common/config/ui/private_key.pem:/etc/ui/private_key.pem:z
      - /docker/harbor/data/secretkey:/etc/ui/key:z
      - /docker/harbor/data/ca_download/:/etc/ui/ca/:z
      - /docker/harbor/data/psc/:/etc/ui/token/:z
      # 添加下面两行
      - ../src/ui/static/vendors/swagger-ui-2.1.4/dist:/harbor/static/vendors/swagger
      - ../src/ui/static/resources/yaml/swagger.yaml:/harbor/static/resources/yaml/swagger.yaml



6、重启harbor。

    # docker-compose down -v
    # docker-compose up -d

7、这个页面是部署在harbor的ui里面的，直接通过harbor的ui就可以访问。

用浏览器访问：https://harbor.xxx.com/static/vendors/swagger/index.html



## 参考文档：
http://www.mamicode.com/info-detail-2188785.html ##