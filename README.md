## Compose sample application

## Node.js application with Nginx proxy and Redis database

Project structure:
```
.
├── README.md
├── docker-compose.yaml
├── nginx
│   ├── Dockerfile
│   └── nginx.conf
└── web
    ├── Dockerfile
    ├── package.json
    └── server.js

```

![Architecture Diagram](./images/ARCH.png)

```
git clone https://github.com/surendrababu2012/nginx-nodejs-redis.git

cd nginx-nodejs-redis

git add .

git commit -m "any message"

git push origin main

```
> ℹ️ **_INFO_**  
> You have to add env variables into your github repository. Go to Settings > Secrets and variables > Actions > New repository secrets
> Env variables(DOCKERHUB_TOKEN,DOCKERHUB_USERNAME,EC2_HOST,EC2_KEY,EC2_USER)
> Create a Docker Hub personal access token by going into hub.docker.com.


The compose file defines an application with four services `redis`, `nginx`, `web1` and `web2`.
When deploying the application, docker compose maps port 80 of the nginx service container to port 80 of the host as specified in the file.


> ℹ️ **_INFO_**  
> Redis runs on port 6379 by default. Make sure port 6379 on the host is not being used by another container, otherwise the port should be changed.

## Create a EC2 instance in AWS

> Go to AWS management console and create a EC2 instance in a public subnet, while creating instance provide the userdata as below to bootstrap the docker compose and make sure to select ubuntu distro(Latest is preffered). 



```
# Add Docker's official GPG key:

usermod -aG sudo ubuntu


sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo service docker start



```


Source : [Docker Docs](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

> Note: Security group must allow inbound traffic from ports 80,443,6379 and 22. Add rule accrodingly

## 


> You should able to find the hostname(EC2_HOST), username (EC2_USER=ubuntu). Don't forget to create a SSH key while EC2 creation, we then use it as EC2_KEY. 

> If you have below setup done. 
> Cloned GitHub
> Docker Hub registry created
> Env varibles created
>
```
```
## Pipelines 


![Pipeline Diagram](./images/CI-CD-Flow.png)

## Testing the app

To start the application you should have the above things setup and after first commit the pipelines should trigger. Now navigate to `http://EC2-DNSNAME:80` or `http://EC2-PUBLICIP:80`in your web browser or inside of ec2 instance run:

```
curl localhost:80
curl localhost:80
web1: Total number of visits is: 1
```

```
curl localhost:80
web1: Total number of visits is: 2
```
```
$ curl localhost:80
web2: Total number of visits is: 3
```


![Test Image Web](./images/web2.png)


## Stop and teardown the EC2 instance



