# docker

docker安装jenkins

```bash
docker run -d --name=jenkins --restart on-failure:3 -p 8083:8080 -v /d/docker/jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

