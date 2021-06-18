## 도커 설치

### Docker for Linux

```bash
curl -s https://get.docker.com/ | sudo sh

sudo usermod -aG docker ubuntu
```

### Docker for Mac(Windows)

[Docker Desktop](https://www.docker.com/products/docker-desktop)에서 다운로드

## 설치 확인

```bash
docker version
```

# 도커 기본 명령어

## run - 컨테이너 실행

```bash
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
```

## exec - 실행중인 컨테이너에 접속할 때 사용

`run` 명령어와 달리 실행중인 도커 컨테이너에 접속할 때 사용하며, 컨테이너 안에 ssh server 등을 설치하지 않고 `exec` 명령어로 접속 가능하다.

```bash
docker exec [OPTIONS] CONTAINER
```

## ps - 실행중인 컨테이너 목록 확인

`-a` 옵션을 붙이면 중지된 컨테이너도 확인할 수 있다.

```bash
docker ps

docker ps -a
```

## stop - 실행중인 컨테이너 중지

실행중인 컨테이너를 하나 또는 여러개(띄어쓰기로 구분) 중지할 수 있다.

```bash
docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

`docker ps` 로 컨테이너 아이디 확인 후 `docker stop [container id]` 로 중지

## rm - 종료된 컨테이너를 완전히 제거

```bash
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

## logs - 로그 확인

컨테이너가 정상적으로 동작하는 지 확인하기 위해 로그 확인

```bash
docker logs [OPTIONS] CONTAINER

docker logs -f [CONTAINER ID]
```

`-f` 옵션은 추가적인 로그가 생기면 계속 출력함 (실시간 로그 확인)

## images - 도커가 다운로드한 이미지 목록 확인

```bash
docekr images [OPTIONS] [REPOSITORY[:TAG]]
```

## pull - 이미지 다운로드

수동으로 이미지를 다운로드 할 수 있다

```bash
docker pull [OPTIONS] NAME[:TAG|@DIGEST]

docker pull ubuntu:18.04
```

## rmi - 이미지 삭제

`images` 명령어를 통해 얻는 `IMAGE ID`를 입력해 삭제

실행중인 컨테이너의 이미지는 삭제 되지 않음

```bash
docker rmi [OPTIONS] IMAGE [IMAGE...]
```

## network create - 도커 컨테이너끼리 통신하는 가상 네트워크 생성

컨테이너끼리 이름으로 통신할 수 있는 가상 네트워크를 만든다

```bash
docker network create [OPTIONS] NETWORK

## app-network라는 이름으로 wordpress와 mysql이 통신할 네트워크를 만든다
docker network create app-network
```

## network connect - 컨테이너에 네트워크 추가

기존에 생성한 컨테이너에 네트워크를 추가한다

```bash
docker network connect [OPTIONS] NETWORK CONTAINER

## mysql 컨테이너에 네트워크를 추가
docker network connect app-network mysql
```

## network option

```bash
## 워드프레스를 app-network에 속하게 하고, mysql을 이름으로 접근
docker run -d -p 8080:80 \
  --network=app-network \
  -e WORDPRESS_DB_HOST=mysql \
  -e WORDPRESS_DB_NAME=wp \
  -e WORDPRESS_DB_USER=wp \
  -e WORDPRESS_DB_PASSWORD=wp \
  wordpress
```

## volume mount (-v)

mysql 컨테이너 종료 후 `-v` 옵션 없이 다시 실행하면 wordpress는 오류가 발생한다

→ 컨테이너를 종료하면서 데이터도 함께 사라졌기 때문에!

```bash
## mysql 중단 후 삭제
docker stop mysql
docker rm mysql

## 다시 실행
docker run -d -p 3306:3306 \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
  --network=app-network \
  --name mysql \
  -v /Users/subicura/Workspace/github.com/subicura/docker-guide/ch02/mysql:/var/lib/mysql \
  mysql:5.7
```

`-v` 옵션은 본인의 디렉터리와 연결을 해서 사용할 수 있음

사라지면 안 되는 데이터가 있는 경우에는 `-v` 옵션을 주어 실행해야 한다

```bash
-v /my/own/datadir:/var/lib/mysql
```

# docker-compose

docker for mac(windows)을 설치 했다면, 기본적으로 함께 설치된다.

도커 명령어를 합쳐서 관리하는..? 실무에서는 대부분 yml파일을 만들어서 관리한다.

## 설치 확인

```bash
docker-compose version
```

### docker-compose만들기

우선 빈 디렉토리를 하나 생성한 후, `docker-compose.yml` 파일을 생성한다.

```yaml
version: '2'
services:
  db:
    image: mysql:5.7
    volumes:
      - ./mysql:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: wordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
	wordpress:
	  image: wordpress:latest
	  volumes:
	    - ./wp:/var/www/html
	  ports:
	    - "8000:80"
	  restart: always
	  environment:
	    WORDPRESS_DB_HOST: db:3306
	    WORDPRESS_DB_PASSWORD: wordpress
```

## up - docker compose를 이용하여 실행

`docker-compose.yml` 을 실행

```bash
docker-compose up -d
```

## down - docker compose를 이용하여 종료

```bash
docker-compose down
```
