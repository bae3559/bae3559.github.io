---
layout: post
title: "Docker Compose, 한 번에 여러 컨테이너 관리하기! 초보자를 위한 개념부터 실전까지"
date: 2026-11-08
categories: [Docker, 개발 환경, DevOps]
tags: [Docker Compose, Docker, 컨테이너, 개발 팁, YAML, Flask, Redis]
---

# Docker Compose, 한 번에 여러 컨테이너 관리하기! 초보자를 위한 개념부터 실전까지

안녕하세요, 여러분! 개발자 블로그 작가 블록오버플로우입니다.

개발을 하다 보면 여러 가지 서비스를 동시에 운영해야 할 때가 많습니다. 예를 들어, 웹 애플리케이션을 개발한다면 웹 서버, 데이터베이스, 캐시 서버 등 여러 개의 독립적인 컨테이너가 필요하죠. 도커(Docker)를 사용하면 각각의 서비스를 컨테이너로 분리하여 효율적으로 관리할 수 있지만, 이 모든 컨테이너를 수동으로 `docker run` 명령어를 통해 실행하고 연결하는 것은 여간 번거로운 일이 아닙니다.

이런 고민을 해결해주는 마법 같은 도구가 바로 **Docker Compose**입니다! 오늘은 Docker Compose가 무엇인지, 왜 필요한지, 그리고 어떻게 사용하는지 초보자 눈높이에 맞춰 쉽고 친절하게 설명해 드릴게요.

---

## 1. Docker Compose, 왜 필요한가요?

상상해 보세요. 여러분이 블로그 서비스를 개발하고 있습니다. 이 서비스는 다음과 같은 구성 요소들을 가지고 있습니다.
*   **웹 애플리케이션 (Python Flask)**: 사용자 요청을 처리하고 데이터를 보여줍니다.
*   **데이터베이스 (PostgreSQL)**: 게시글, 사용자 정보 등을 저장합니다.
*   **캐시 서버 (Redis)**: 자주 조회되는 데이터를 빠르게 제공합니다.

이 세 가지를 도커 컨테이너로 실행하려면 `docker run` 명령어를 최소 세 번 입력해야 할 겁니다.
*   `docker run -d --name my-db postgres`
*   `docker run -d --name my-redis redis`
*   `docker run -d --name my-web -p 5000:5000 --link my-db:db --link my-redis:redis my-flask-app`

여기서 끝이 아닙니다.
*   컨테이너 간의 **네트워크 연결**은 어떻게 할까요?
*   데이터베이스 **데이터가 사라지지 않도록** 볼륨은 어떻게 설정할까요?
*   환경 변수는 어떻게 전달할까요?

이렇게 여러 컨테이너를 동시에 다루다 보면 설정이 복잡해지고, 팀원들과의 환경 공유도 어려워지며, 무엇보다 휴먼 에러가 발생할 가능성이 커집니다. Docker Compose는 이 모든 문제를 하나의 설정 파일로 깔끔하게 해결해 줍니다.

---

## 2. Docker Compose, 핵심 개념 파헤치기

Docker Compose는 여러 개의 도커 컨테이너를 하나의 애플리케이션으로 정의하고 실행하기 위한 도구입니다. 그 핵심은 바로 `docker-compose.yml` 파일에 있습니다.

### 2.1. `docker-compose.yml` 파일: 모든 설정의 중심

`docker-compose.yml` 파일은 YAML 형식으로 작성되며, 여러분의 애플리케이션을 구성하는 모든 서비스(컨테이너), 네트워크, 볼륨 등을 정의합니다. 이 파일 하나만 있으면 복잡한 `docker run` 명령어들을 반복할 필요 없이 `docker compose up` 명령 한 줄로 전체 애플리케이션을 실행할 수 있습니다.

주요 섹션은 다음과 같습니다.
*   **`version`**: Docker Compose 파일의 버전입니다.
*   **`services`**: 여러분의 애플리케이션을 구성하는 각 컨테이너를 정의합니다. (가장 중요!)
*   **`networks`**: 서비스 간 통신을 위한 사용자 정의 네트워크를 정의합니다.
*   **`volumes`**: 데이터를 영구적으로 저장하기 위한 볼륨을 정의합니다.

### 2.2. 서비스 (Services): 각각의 컨테이너를 정의

`services` 섹션 안에 여러분이 실행하고자 하는 각각의 컨테이너들을 정의합니다. 예를 들어, `web`, `database`, `redis` 등이 서비스 이름이 될 수 있습니다. 각 서비스는 다음과 같은 속성들을 가질 수 있습니다.

*   **`image`**: 도커 이미지 이름 (예: `nginx:latest`, `postgres:13`)
*   **`build`**: Dockerfile이 있는 경로를 지정하여 이미지를 직접 빌드합니다.
*   **`ports`**: 호스트와 컨테이너 간의 포트 매핑 (예: `5000:5000`)
*   **`environment`**: 컨테이너 내부에서 사용할 환경 변수 설정
*   **`volumes`**: 호스트와 컨테이너 간의 볼륨 매핑
*   **`depends_on`**: 서비스 간의 의존성 지정 (예: `web`은 `database`가 먼저 실행되어야 함)

### 2.3. 네트워크 (Networks): 서비스 간의 소통창구

Docker Compose는 기본적으로 여러분이 정의한 모든 서비스들을 하나의 기본(default) 네트워크에 연결합니다. 이 덕분에 서비스들은 서로의 서비스 이름을 통해 통신할 수 있습니다. 예를 들어, `web` 서비스가 `database` 서비스와 통신하려면 `database`라는 호스트 이름으로 접근하면 됩니다.

### 2.4. 볼륨 (Volumes): 데이터의 영원한 보관함

컨테이너는 기본적으로 휘발성입니다. 컨테이너가 삭제되면 그 안에 있던 모든 데이터도 함께 사라집니다. 볼륨을 사용하면 컨테이너 내부의 특정 디렉터리를 호스트 머신의 디렉터리나 도커 관리 볼륨에 연결하여 데이터를 영구적으로 저장할 수 있습니다.

---

## 3. Docker Compose, 직접 사용해보기 (feat. Python Flask & Redis)

이제 실제로 Docker Compose를 사용하여 간단한 웹 애플리케이션을 배포해 볼 시간입니다! Python Flask 웹 서버와 Redis 캐시 서버를 함께 구동하는 예제를 만들어 보겠습니다.

**준비물**: Docker Desktop이 설치되어 있어야 합니다. (Docker Desktop에는 Docker Compose가 기본적으로 포함되어 있습니다.)

### 3.1. 애플리케이션 파일 구조

먼저, 다음과 같은 파일 구조를 만듭니다.

```
.
├── docker-compose.yml
├── app/
│   ├── app.py
│   └── Dockerfile
│   └── requirements.txt
```

### 3.2. Flask 애플리케이션 (app/app.py)

간단하게 Redis에 접속하여 페이지 방문 횟수를 카운트하는 Flask 앱을 작성합니다.

```python
# app/app.py
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379) # 'redis'는 docker-compose.yml에 정의된 서비스 이름

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello from Docker! I have been seen {} times.\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
```

### 3.3. Flask 애플리케이션의 의존성 (app/requirements.txt)

```python
# app/requirements.txt
flask
redis
```

### 3.4. Flask 앱을 위한 Dockerfile (app/Dockerfile)

Flask 앱을 컨테이너 이미지로 빌드하기 위한 Dockerfile입니다.

```python
# app/Dockerfile
FROM python:3.9-slim-buster

WORKDIR /app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

### 3.5. `docker-compose.yml` 파일 작성

이제 `docker-compose.yml` 파일을 작성하여 Flask 앱과 Redis 캐시 서버를 함께 정의합니다.
**참고**: 아래 코드 블록은 `docker-compose.yml` 파일이지만, 프롬프트의 지시에 따라 ````python`으로 표기합니다. 실제로는 YAML 형식입니다.

```python
# docker-compose.yml
version: '3.8'

services:
  web:
    build: ./app
    ports:
      - "5000:5000"
    environment:
      REDIS_HOST: redis # Redis 서비스의 호스트 이름을 'redis'로 설정
    depends_on:
      - redis # web 서비스는 redis 서비스가 먼저 실행되어야 함

  redis:
    image: "redis:alpine" # 경량화된 Redis 이미지 사용
```

*   `version: '3.8'` : Docker Compose 파일의 버전을 명시합니다.
*   `services:` : 두 개의 서비스를 정의합니다. `web`과 `redis`.
    *   `web:`
        *   `build: ./app` : `./app` 디렉터리에 있는 `Dockerfile`을 사용하여 이미지를 빌드합니다.
        *   `ports: - "5000:5000"` : 호스트의 5000번 포트를 컨테이너의 5000번 포트에 연결합니다.
        *   `environment: REDIS_HOST: redis` : `app.py`에서 사용할 `REDIS_HOST` 환경 변수를 `redis`로 설정합니다. `redis`는 아래에 정의된 서비스 이름입니다.
        *   `depends_on: - redis` : `web` 서비스가 시작되기 전에 `redis` 서비스가 먼저 시작되어야 함을 명시합니다.
    *   `redis:`
        *   `image: "redis:alpine"` : Docker Hub에서 `redis:alpine` 이미지를 다운로드하여 사용합니다.

### 3.6. Docker Compose 실행하기

모든 파일 준비가 끝났으니, 이제 터미널을 열고 `docker-compose.yml` 파일이 있는 디렉터리로 이동한 후 다음 명령어를 실행합니다.

```python
# 터미널 명령어
docker compose up -d
```

*   `docker compose up` : `docker-compose.yml` 파일에 정의된 모든 서비스를 시작합니다.
*   `-d` (detached mode) : 컨테이너들을 백그라운드에서 실행하고 터미널을 다시 사용할 수 있도록 합니다.

이 명령어를 실행하면 Docker Compose가 `web` 서비스의 이미지를 빌드하고 (`Dockerfile` 사용), `redis` 이미지를 다운로드한 후, 두 컨테이너를 모두 시작합니다.

### 3.7. 실행 확인하기

컨테이너들이 잘 실행되고 있는지 확인하려면 다음 명령어를 사용합니다.

```python
# 터미널 명령어
docker compose ps
```

이 명령어를 실행하면 `web`과 `redis` 서비스가 `running` 상태임을 확인할 수 있습니다.

이제 웹 브라우저를 열고 `http://localhost:5000`으로 접속해 보세요. 페이지를 새로고침할 때마다 방문 횟수가 증가하는 것을 볼 수 있습니다. 이는 Flask 앱이 Redis 캐시 서버와 성공적으로 통신하고 있다는 의미입니다!

### 3.8. Docker Compose 종료하기

애플리케이션 사용을 마쳤다면, 다음 명령어를 사용하여 모든 서비스를 중지하고 컨테이너를 삭제할 수 있습니다.

```python
# 터미널 명령어
docker compose down
```

*   `docker compose down` : `docker-compose.yml` 파일에 정의된 모든 컨테이너와 네트워크를 중지하고 삭제합니다.

만약 볼륨도 함께 삭제하고 싶다면 `-v` 옵션을 추가합니다.
```python
# 터미널 명령어
docker compose down -v
```

---

## 4. 마무리하며

오늘 우리는 Docker Compose의 개념부터 실제 사용 방법까지 자세히 알아보았습니다. 여러 개의 컨테이너를 동시에 다루는 복잡한 애플리케이션 환경을 `docker-compose.yml` 파일 하나로 간결하게 정의하고 관리할 수 있다는 점이 Docker Compose의 가장 큰 매력입니다.

개발 환경 설정, CI/CD 파이프라인 구성, 심지어 소규모 배포에 이르기까지 Docker Compose는 개발자에게 엄청난 편리함을 제공합니다. 처음에는 YAML 파일 작성에 익숙하지 않을 수 있지만, 몇 번 연습하다 보면 그 강력함에 매료될 겁니다.

이제 여러분도 Docker Compose를 활용하여 복잡한 환경 설정의 번거로움에서 벗어나세요! 직접 예제를 따라 해보면서 자신만의 멀티 컨테이너 애플리케이션을 구성해 보는 것을 강력히 추천합니다.

다음번에도 유익한 개발 정보로 찾아오겠습니다. 감사합니다!

---