# docker-practice-for-beginners

[Udemy] 초보자를 위한 Docker 실습

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
