# 搭建图数据库Neo4j

- 从docker hub拉Neo4j镜像

  ```shell
  $ docker pull neo4j
  latest: Pulling from library/neo4j
  bf5952930446: Pull complete
  092c9b8e633f: Pull complete
  0b793152b850: Pull complete
  7900923f09cb: Pull complete
  9650282c0ca0: Pull complete
  8a23f374cdc0: Pull complete
  2112a6011323: Pull complete
  e17497b6ad32: Pull complete
  Digest: sha256:823ac7c3d2fd06a53a9e2fbd56183db54508f481406f01fd607159bb502a8431
  Status: Downloaded newer image for neo4j:latest
  docker.io/library/neo4j:latest
  ```

- 在$HOME路径下建立文件夹neo4j/data

- 启动容器

  ```shell
  $ docker run --publish=7474:7474 --publish=7687:7687 --volume=$HOME/neo4j/data:/data neo4j
  Directories in use:
    home:         /var/lib/neo4j
    config:       /var/lib/neo4j/conf
    logs:         /logs
    plugins:      /var/lib/neo4j/plugins
    import:       /var/lib/neo4j/import
    data:         /var/lib/neo4j/data
    certificates: /var/lib/neo4j/certificates
    run:          /var/lib/neo4j/run
  Starting Neo4j.
  2020-09-01 07:00:21.773+0000 INFO  Starting...
  2020-09-01 07:00:24.615+0000 INFO  ======== Neo4j 4.1.1 ========
  2020-09-01 07:00:26.088+0000 INFO  Performing postInitialization step for component 'security-users' with version 2 and status CURRENT
  2020-09-01 07:00:26.088+0000 INFO  Updating the initial password in component 'security-users'
  2020-09-01 07:00:26.566+0000 INFO  Bolt enabled on 0.0.0.0:7687.
  2020-09-01 07:00:27.666+0000 INFO  Remote interface available at http://localhost:7474/
  2020-09-01 07:00:27.667+0000 INFO  Started.
  ```

- 浏览器访问http://localhost:7474/查看用户界面，默认的用户名和密码都是neo4j，登入之后可以修改密码

  ![image-20200901150227717](/Users/evelyn/Library/Application Support/typora-user-images/image-20200901150227717.png)



## 参考链接

https://www.jianshu.com/p/97c6752e928b