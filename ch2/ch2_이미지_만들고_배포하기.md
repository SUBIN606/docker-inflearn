# 이미지

- 도커는 레이어드 파일 시스템 기반
- AUFS, BTRFS, Overlayfs, ... 같은 파일시스템을 사용
- 프로세스가 실행되는 파일들의 집합(환경)
- 프로세스는 환경(파일)을 변경할 수 있음
- 이 환경을 저장해 새로운 이미지를 만듦

---

## 이미지 만들어보기 - docker commit

### 1. ubuntu:latest 이미지 실행

`bash` 명령을 했기 때문에, 바로 터미널로 접속한다.

`git` 명령어를 사용했을 때 아직 git이 설치되지 않은 상태라 command not found 라고 뜸

### 2. git 설치

업데이트 후 git 설치, `git --version` 명령어로 설치확인

```bash
apt-get update

apt-get install -y git

git --version
```

### 3. 우분투 이미지에 git이 설치된 것을 새로운 이미지로 만들기

이미지 이름은 `[namespace]/[이미지이름]:[태그]` 형식을 따라 작성한다

git이라는 이름의 컨테이너를 `unbuntu:git` 이라는 이미지로 만듦

```bash
docker commit git ubuntu:git
```

### 4. 만든 이미지로 컨테이너 실행

```bash
docker run -it --name git2 ubuntu:git bash
```

---

## 이미지 만들기 - docker build

`Dockerfile` 을 이용해 이미지를 빌드해본다.

### 1. Dockerfile 작성

컨테이너 실행 후 git을 설치하던 명령어들을 그대로 적고, 실행되게 함

```docker
FROM ubuntu:latest

RUN apt-get update
RUN apt-get install -y git
```

### 2. 이미지 빌드

`Dockerfile` 이 있는 디렉터리에서 실행한다.

```bash
docker build -t {이미지명:이미지태그} {빌드 컨텍스트}
```

`-f` 옵션을 사용해 다른위치의 Dockerfile을 사용하는 것도 가능하다.
