# autoDeploy
前端代码自动打包部署到服务器
autoDeploy
项目简介
该项目是基于node实现，支持前端项目进行legacy、docker、docker-compose多种方式的远端自动化部署。

🎉现已支持legacy、docker、docker-compose三种部署方式
🎉现已支持 上传编译后代码部署、上传前端源码远端编译部署两种方式 （ps：远端编译仅支持docker模式）
现已支持多配置信息，支持部署时选择项目、部署方式、上传编译方式
现已支修改服务器连接端口，支持ssh私钥及解密密码连接
现已支持远端备份，时间格式后缀 yyyy-MM-dd_HH:mm:ss
支持
部署方式	legacy	docker	docker-compose
本地打包编译dist🔶	✔	✔	✔
源码远端打包编译🔷	❌	✔	✔
说明
配置文件(src/config/config.js)

🔶上传本地编译后文件夹 (dist)

🔷上传源码远端打包编译 (source)

部署方式说明：

legacy（物理部署）
请将文件上传至nginx配置对应目录
docker
端口修改请参照配置文件 --> ports
🔶上传dist请参照配置文件 --> docker_file
🔷上传source请参照配置文件 --> docker_file__build
🔷source中无需上传文件过滤请参照配置文件 --> exclude
其他需求可自行修改对应Dockerfile
docker-compose
docker-compose.yml中容器名、镜像名请保持与配置文件一致
端口修改请参照docker-compose.yml --> ports
其他需求可自行修改对应docker-compose.yml
使用
Tip：请确保已安装node、npm

npm install # 安装依赖
npm run deploy # 本地运行
使用流程：

选择项目-->选择部署方式-->选择上传源码or编译后代码-->远端自动部署

优点
轻量、便捷、高效
支持legacy、docker、docker-compose多种方式自动部署
支持 多项目管理、多方式连接、本地压缩、远端备份等
配置文件说明
/*
config.js
🔶上传本地编译后文件夹 (dist)
🔷上传源码远端打包编译 (source)
说明：
  name: 项目名称
  ssh: 连接服务器信息
  openBackUp: 是否开启远端备份
  deployDir: 远端部署目录
  releaseDir: 发布目录
  distDir: web编译后目录🔶
  docker_file: Dockerfile文件位置🔶
  sourceDir: web源码目录(云端代码编译使用，仅支持docker模式)🔷
  exclude: 源码目录中排除上传文件 🔷
  docker_file__build: 支持源码编译Dockerfile文件位置🔷
  image: 编译后镜像名
  ports: 端口映射（宿主机:内部，请确保宿主机端口已开放）
  container_name: 容器名称
  docker_compose: docker-compose文件位置
  端口修改请参照`docker-compose.yml` --> ports
  `docker-compose.yml`中容器名、镜像名请保持与`配置文件`一致
更新：
  - 🎉现已支持legacy、docker、docker-compose三种部署方式
  - 🎉现已支持 **上传编译后代码部署**、**上传前端源码远端编译部署**两种方式
  （ps：远端编译仅支持docker模式）
  - 现已支持多配置信息，支持部署时选择项目、部署方式、上传编译方式
  - 现已支修改服务器连接端口，支持ssh私钥及解密密码连接
  - 现已支持远端备份，时间格式后缀 `yyyy-MM-dd_HH:mm:ss`
  */

const config = [
  {
    name: '示例项目',
    ssh: {
      host: '120.26.51.81',
      port: 22,
      username: 'root',
      password: 'xx',
      // privateKey: 'E:/id_rsa', // ssh私钥(不使用注释即可)
      // passphrase: '123456' // ssh私钥对应解密密码(不存在设为''即可)
    },
    openBackUp: true, // 是否开启远端备份
    deployDir: '/app' + '/', // 远端部署目录
    releaseDir: 'spa-web', // 发布目录（最终发布目录为/app/spa-app）
    distDir: 'E:/XX/dist', // web编译后目录🔶
    // 以下为docker模式下使用配置
    docker_file: './Dockerfile_nginx/Dockerfile', // Dockerfile文件位置🔶
    sourceDir: 'E:/XX', // web源码目录🔷
    exclude: [ //  源码目录中 默认排除上传 node_modules, dist, .git🔷
      'node_modules',
      'dist',
      '.git',
    ],
    docker_file__build: './Dockerfile_node_nginx/Dockerfile', // 支持源码编译Dockerfile文件位置🔷
    image: 'spa/web:dev', // 编译后镜像名
    ports: '8800:80', // 端口映射（宿主机:内部，请确保宿主机端口已开放）
    container_name: 'spa_web', // 容器名称
    docker_compose: './docker-compose.yml' // docker-compose文件位置
    // 端口修改请参照`docker-compose.yml` --> ports
    // `docker-compose.yml`中容器名、镜像名请保持与`配置文件`一致
  }
]
注意事项
使用docker部署方式，请提前安装docker、docker-compose(新版本最佳)
若服务器网速较慢，可提前安装node和nginx的docker镜像，以便加快后续部署速度
docker pull node:lts-alpine3.12
docker pull socialengine/nginx-spa:latest
若因docker-compose版本过低，导致部署失败
可降低docker-compose.yml version: "2.x"（降为2.0+ 的版本）
或升级至最新版本docker-compose
远端源码编译中会产生<none>临时镜像，用于加快下次构建速度，无需删除
请确认宿主机端口已开放
示例
这里选取两种情况进行展示：
docker + source build
docker-compose + dist
这里以react的在线壁纸为例，服务器已开放8800、8900端口。
拉取代码至本地，安装依赖，执行本地构建
该项目构建后产生build文件夹且不存在package-lock.json文件，因此修改配置文件和Dockerfile
配置文件
Dockerfile
运行部署程序，选择相应信息开始部署，直至部署完成
选择部署
部署成功（容器信息和预期结果一致）
访问对应地址，验证部署成功，
修改 docker-compose.yml 端口为8900后，再次进行部署，如图
docker-compose.yml
选择部署
由于存在同名容器，部署过程中会停止并删除同名容器，之后使用最新的镜像启动容器，如图
访问对应地址，验证部署成功，如图
