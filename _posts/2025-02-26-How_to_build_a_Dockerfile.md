---
title: ["How to Build a Dockerfile for CTF"]
date: 2025-02-26 09:00:00 +0900
categories: [cheatsheet, Docker]
tags: [docker]
---

## What is Dockerfile?
CTF 문제에서 제공되는 Dockerfile은 보통 다음과 같은 구조를 가진다:
```dockerfile
# 기본 이미지 지정
FROM ubuntu:20.04

# 환경 변수 설정
ENV DEBIAN_FRONTEND=noninteractive

# 필요한 패키지 설치
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    netcat \
    socat \
    && rm -rf /var/lib/apt/lists/*

# 작업 디렉토리 설정
WORKDIR /app

# 문제 파일 복사
COPY ./challenge /app/
COPY ./flag.txt /app/flag.txt

# 실행 권한 부여
RUN chmod +x /app/start.sh

# 포트 노출
EXPOSE 1337

# 사용자 생성 및 권한 설정
RUN useradd -m ctf
RUN chown -R root:root /app
RUN chmod -R 755 /app
RUN chmod 444 /app/flag.txt

# 사용자 전환
USER ctf

# 컨테이너 시작 시 실행할 명령어
CMD ["/app/start.sh"]
# 또는
ENTRYPOINT ["/app/start.sh"]
```

### Dockerfile Command
- FROM: 기본 이미지를 지정한다. 대부분의 CTF 문제는 Ubuntu나 Alpine Linux와 같은 가벼운 이미지를 사용한다.
- ENV: 환경 변수를 설정한다. DEBIAN_FRONTEND=noninteractive는 패키지 설치 중 사용자 입력을 요구하지 않도록 한다.
- RUN: 이미지 빌드 과정에서 실행할 명령어를 지정한다. 패키지 설치, 파일 권한 변경 등에 사용된다.
- WORKDIR: 작업 디렉토리를 설정한다. 이후의 명령어는 이 디렉토리를 기준으로 실행된다.
- COPY: 호스트 시스템의 파일을 컨테이너로 복사한다. 문제 파일, 플래그 등을 컨테이너 내부로 가져올 때 사용된다.
- EXPOSE: 컨테이너가 리스닝할 포트를 지정한다. 실제 포트 포워딩은 docker run 명령어로 설정해야 한다.
- USER: 컨테이너 내에서 명령을 실행할 사용자를 지정한다. 보안상의 이유로 root가 아닌 별도의 사용자를 생성하는 경우가 많다.
- CMD/ENTRYPOINT: 컨테이너가 시작될 때 실행될 명령어를 지정한다. 

## Building Image from Dockerfile
```bash
# 문제 파일이 있는 디렉토리로 이동
cd /path/to/challenge

# Docker 이미지 빌드
docker build -t ctf-challenge .
```
`-t` 옵션은 이미지의 태그를 지정한다. `.`은 현재 디렉토리에 있는 Dockerfile을 사용하라는 의미이다.

## Running Container by Image
```bash
# 기본 실행
docker run -p 1337:1337 ctf-challenge

# 백그라운드에서 실행
docker run -d -p 1337:1337 ctf-challenge

# 컨테이너 종료 시 자동 삭제
docker run --rm -p 1337:1337 ctf-challenge

# 볼륨 마운트하여 실행 (로컬 파일 변경 시 컨테이너에 즉시 반영)
docker run -v $(pwd)/challenge:/app/challenge -p 1337:1337 ctf-challenge

# 대화형 셸로 접속
docker run -it ctf-challenge /bin/bash
```
`-p` 옵션은 호스트의 포트를 컨테이너의 포트로 포워딩한다. `[호스트 포트]:[컨테이너 포트]` 순이다.

## Debugging Container
### 실행 중인 컨테이너 확인
```bash
# 실행 중인 컨테이너 목록 조회
docker ps

# 모든 컨테이너 목록 조회(종료된 컨테이너 포함)
docker ps -a
```
### 컨테이너 로그 확인
```bash
# 컨테이너 로그 확인
docker logs <container_id>

# 로그 실시간 확인
docker logs -f <container_id>
```
### 실행 중인 컨테이너에 접속
```bash
# 실행 중인 컨테이너에 새 셸로 접속
docker exec -it <container_id> /bin/bash

# root 권한으로 접속
docker exec -it -u 0 <container_id> /bin/bash
```

## Advanced Tips
### Using Docker Compose
여러 컨테이너가 필요한 문제의 경우 아래와 같은 `docker-compose.yml` 파일을 제공한다.  
```yaml
# docker-compose.yml
version: '3'
services:
  web:
    build: ./web
    ports:
      - "8080:80"
  database:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: ctf
    volumes:
      - ./db:/docker-entrypoint-initdb.d
```
`docker-compose`를 활용해서 yml에 정의된 모든 서비스를 한 번에 세팅할 수 있다.
```bash
docker-compose up -d
```
#### Docker Compose의 다양한 옵션
1. 파일이 다른 이름인 경우: `-f` 옵션을 사용한다.
   ```bash
   Copydocker-compose -f custom-compose.yml up -d
   ```
2. 특정 서비스만 실행하려면:
   ```bash
   Copydocker-compose up -d web database  # web과 database 서비스만 실행
   ```
3. 실행 중인 서비스 확인:
   ```bash
   Copydocker-compose ps
   ```
4. 로그 확인:
   ```bash
   Copydocker-compose logs -f  # 모든 서비스의 로그를 실시간으로 확인
   ```
5. 서비스 중지 및 삭제:
   ```bash
   Copydocker-compose down  # 컨테이너 중지 및 삭제
   docker-compose down -v  # 볼륨까지 함께 삭제
   ```