# 1. node 설치
# 이미 node가 설치되어있는 baseImage로 변경
FROM node:12

# 2. 소스 복사
COPY      . /usr/src/app

# 3. Nodejs 패키지 설치
WORKDIR   /usr/src/app
RUN       npm install

# 4. WEB 서버 실행 (Listen 포트 정의)
EXPOSE    3000
CMD       node app.js
