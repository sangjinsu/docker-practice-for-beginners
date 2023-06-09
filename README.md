# docker-practice-for-beginners

[Udemy] 초보자를 위한 Docker 실습
[Docker 교육 사이트] https://dockerlabs.collabnix.com/

## Docker 개요

### 도커가 필요한 이유

- Compatibility, Dependency: 호환성 문제
- Long Setup Time
- 같은 운영체제로 컨테이너 분리
- 운영체제 커널이 동일하고 그 위에서 도커가 운영됨
- 도커는 모든 운영체제와 호환되는 Docker 호스트의 기반 커널을 활용
- 윈도우와 맥은 리눅스 가상머신을 사용하여 도커의 컨테이너를 사용
- 가상 머신은 용량이 무겁지만 도커의 컨테이너는 가벼우며 부트가 쉬움

### 도커 작동 원리

- docker run 명령어를 사용하여 애플리케이션 실행
- 간단하게 생성 및 삭제 가능

## Docker Command

- docker run
- docker ps
- docker ps -a
- docker stop [ID or NAMES]
- docker rm [ID or NAMES]
- docker images
- docker rmi [image]
- docker pull [image]
- docker run ubuntu sleep 5
- docker exec [NAMES] cat /etc - 명령어 실행
- docker run -d [image] - 백그라운드 실행
- docker attach [ID or NAMES]

## Docker Run

- -tag : 버전 관리
- -i : interactive, 값 입력
- -t : terminal
- -it : 터미널 대화 모드
- -port 외부포트:컨테이녀 내부포트 : 포트 설정
- -v : 볼륨 설정, 컨테이너 종료되어도 데이터 유지
- -d : 백그라운드 실행
- docker logs : 백그라운드 실행 컨테이너 로그 확인

## Docker image

### Dockerfile

1. start from a base os or another image
2. install all dependencies
3. copy source code
4. specify entry point

```
docker build . -f Dockerfile -t [username]/[image name]
```

### Environment variables

```
docker run -e 옵션=옵션값 이미지
```

### Entry Point

엔트리 포인트는 파라미터에 앞서 고정적으로 사용된다

## Docker Compose

```bash
docker run -d --name=redis redis
docker run -d --name=db postgres
docker run -d --name=vote -p 5000:80 --link redis:redis voting-app
docker run -d --name=result -p 5001:80 --link db:db result-app
docker run -d --name=worker --link db:db --link redis:redis worker
```

```yaml
redis:
  image: redis
db:
  image: postgres:9.4
vote:
  image: voting-app
  ports:
    - 5000:80
  links:
    - redis
result:
  image: result-app
  ports:
    - 5001:80
  links:
    - db
worker:
  image: worker
  links:
    - redis
    - db 
```

```bash
docker-compose up
```

```yaml
redis:
  build: ./redis
db:
  build: postgres:9.4
vote:
  build: ./voting-app
  ports:
    - 5000:80
  links:
    - redis
result:
  build: ./result-app
  ports:
    - 5001:80
  links:
    - db
worker:
  build: ./worker
  links:
    - redis
    - db 
```

### docker compose - versions

- version 키워드를 사용해서 파일의 버전을 표시한다.

### docker compose - networks

```yaml
version: "2
services:
  redis:
    image: redis
    networks:
      - back-end
  db:
    image: postgres:9.4
    networks:
      - back-end
  vote:
    image: voting-app
    networks:
      - front-end
      - back-end
  result:
    image: result-app
    networks:
      - front-end
      - back-end
```

### 사전 지식 - YAML

![img.png](img.png)

### docker compose - 파일 개선하기

- 개선 전

```yaml
redis:
  image: redis
db:
  image: postgres:9.4
vote:
  image: voting-app
  ports:
    - 5000:80
  links:
    - redis
result:
  image: result-app
  ports:
    - 5001:80
  links:
    - db
worker:
  image: worker
  links:
    - redis
    - db 
```

-- 개선 후

```yaml
version: "3"
services:
  redis:
    image: redis
    networks:
      - back-end
  db:
    image: postgres:9.4
    networks:
      - back-end
  vote:
    image: voting-app
    networks:
      - front-end
      - back-end
  result:
    image: result-app
    networks:
      - front-end
      - back-end
networks:
  front-end:
  back-end:
```

![img_1.png](img_1.png)

## Docker Registry

### Deploy Private Registry

```bash
docker run -d -p 5000:5000 --name registry registry:2

docker image tag my-image localhost:5000/my-image

docker push localhost:5000/my-image

# push 확인
curl -X GET localhost:5000/v2/_catalog
```

## Docker Engine

### Docker Engine 구조

- Docker CLI
- REST API
- Docker Daemon

### Containerization

- 프로세스 간 통신(inter process), mount, unix timesharing 시스템이 독립된 Namespace에 생성되고 따라서 컨테이너가 각각 분리됨
    - Namespace
        - PID
          ```bash
            docker run -d -it --rm -p 8888:8080 tomcat:9.0
            # 내부 실행 중인 pid 확인
            docker exec [container] ps -eaf
          
          ```
- 컨테이너는 기본 호스트의 모든 리소스 활용 가능
- cgroups 를 사용하여 각 컨테이너에 할당된 하드웨어 리소스 양을 제한합니다
    ```bash
    docker run --cpu=.5 ubuntu
    docker run --memory=100m ubuntu
    ```

## Docker storage

### File System

- /var/lib/docker 폴더 생성 - 컨테이너 관련 정보 저장

### Layered architecture

![img_2.png](img_2.png)

- docker build - read only
- docker run - read write

### volumes

- 데이터 보존
- -v 옵션을 사용하여 저장할 폴더를 지정하여 사용

## Docker Networks

- Default networks
  - docker run ubuntu 
  - docker run ubuntu --network=none
  - docker run ubuntu --network=host

- User-defined networks
    - docker network create 

- Inspect Network
  - docker inspect [network]
  
- Embedded DNS
  - we can use container name like address

```bash
docker network create --driver bridge --subnet 182.18.0.1/24 --gateway 182.18.0.1 wp-mysql-network
docker run --name mysql-db --network wp-mysql-network -e MYSQL_ROOT_PASSWORD=db_pass123 mysql:5.6
docker run --name webapp --network wp-mysql-network -p 38080:8080 -e DB_Host=mysql-db -e DB_Password=db_pass123 kodekloud/simple-webapp-mysql
```

![img_3.png](img_3.png)

## docker Orchestrate

- 컨테이너 상태와 성능 모니터링
```bash
docker service create --replica=100 nodejs
```

### docker swarm 

- 여러 docker machine 을 단일 클러스터로 결합할 수 있다
- 서비스나 애플리케이션 인스턴스를 여러 호스트로 배포한다 
- swam manager 라고 불리는 마스터를 지정하고  worker 를 지정한다 


```yaml

```

![img_4.png](img_4.png)
