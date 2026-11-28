---
layout: post
title: "초보자를 위한 완벽 가이드: Docker Compose 개념과 사용법 마스터하기!"
date: 2026-11-28
categories: [Docker, DevOps, 개발 환경]
tags: [Docker Compose, 컨테이너, 다중 컨테이너, YAML, 개발 생산성, 초보자 가이드]
---

# 초보자를 위한 완벽 가이드: Docker Compose 개념과 사용법 마스터하기!

안녕하세요, 개발 블로그 독자 여러분! 개발 환경을 설정하다 보면 수많은 서비스(웹 서버, 데이터베이스, 캐시 등)들을 동시에 실행해야 하는 경우가 많습니다. 이때 각각의 서비스를 일일이 Docker 컨테이너로 띄우고 관리하는 것이 번거롭다고 느껴지시나요? 바로 이때 여러분의 구세주가 되어줄 도구가 있습니다. 오늘 우리는 **Docker Compose**에 대해 알아보며, 어떻게 다중 컨테이너 애플리케이션을 쉽고 효율적으로 관리할 수 있는지 함께 배워볼 예정입니다.

이 글은 Docker에 익숙하지 않은 초보자분들도 Docker Compose의 강력함을 이해하고 바로 활용할 수 있도록 친절하게 설명하고자 합니다. 자, 그럼 시작해볼까요?

---

## 1. Docker Compose, 왜 필요할까요? (문제점과 해결책)

Docker는 개별 애플리케이션이나 서비스를 컨테이너라는 독립적인 환경에서 실행할 수 있게 해주는 혁신적인 도구입니다. 하지만 대부분의 실제 애플리케이션은 웹 서버, 데이터베이스, 캐시 서버 등 여러 개의 독립적인 서비스로 구성됩니다.

예를 들어, 간단한 웹 애플리케이션을 개발한다고 가정해봅시다.
*   웹 애플리케이션 (Python Flask, Node.js Express 등)
*   데이터베이스 (PostgreSQL, MySQL 등)
*   아마도 Redis 같은 캐시 서버

이 세 가지 서비스를 Docker 컨테이너로 실행하려면, 여러분은 다음과 같은 과정을 거쳐야 할 것입니다.

1.  Flask 애플리케이션용 Dockerfile 작성 및 빌드
2.  PostgreSQL 이미지 다운로드
3.  Redis 이미지 다운로드
4.  각각의 컨테이너를 `docker run` 명령어를 통해 실행
5.  컨테이너 간의 네트워크 설정 및 환경 변수 연결

이 과정은 생각보다 복잡하고, 각 서비스의 설정이 변경될 때마다 명령어를 다시 입력하거나 스크립트를 만들어야 하는 번거로움이 있습니다. 특히 여러 개발자가 함께 작업하는 환경에서는 개발 환경의 일관성을 유지하기 어렵다는 문제도 발생합니다.

**Docker Compose는 이 모든 문제를 한 번에 해결해줍니다.** 여러 개의 Docker 컨테이너를 하나의 서비스로 정의하고, 단일 명령어로 손쉽게 관리할 수 있도록 돕는 도구입니다.

---

## 2. Docker Compose란 무엇인가요? (개념 이해)

**Docker Compose**는 **다중 컨테이너 Docker 애플리케이션을 정의하고 실행하기 위한 도구**입니다. YAML 파일을 사용하여 애플리케이션의 서비스, 네트워크, 볼륨 등을 한 번에 구성하고, `docker compose up`이라는 단일 명령어로 모든 서비스를 동시에 실행할 수 있습니다.

### 핵심 개념: `docker-compose.yml` 파일

Docker Compose의 핵심은 `docker-compose.yml`이라는 설정 파일입니다. 이 YAML 파일 안에는 다음과 같은 정보들이 정의됩니다.

*   **서비스 (Services):** 애플리케이션을 구성하는 각각의 컨테이너 (예: 웹 서버, 데이터베이스)
*   **네트워크 (Networks):** 서비스 간의 통신을 위한 네트워크 설정
*   **볼륨 (Volumes):** 컨테이너 내의 데이터를 영구적으로 저장하기 위한 설정

한번 `docker-compose.yml` 파일을 작성해두면, 이 파일을 공유하는 모든 팀원은 동일한 개발 환경을 몇 초 만에 구축할 수 있게 됩니다.

---

## 3. Docker Compose 사용법 (예제와 함께)

이제 실제 예제를 통해 Docker Compose를 사용하는 방법을 자세히 알아보겠습니다. 간단한 Python Flask 웹 애플리케이션과 PostgreSQL 데이터베이스를 Docker Compose로 함께 실행하는 시나리오입니다.

### 3.1. 준비물

*   **Docker Desktop:** Docker Compose는 Docker Desktop에 기본적으로 포함되어 있습니다. 아직 설치하지 않았다면 [Docker 공식 웹사이트](https://www.docker.com/products/docker-desktop/)에서 다운로드하여 설치해주세요.

### 3.2. 프로젝트 구조 설정

먼저 프로젝트를 위한 디렉토리를 만들고 필요한 파일들을 준비합니다.

```
my-flask-app/
├── app/
│   └── app.py
│   └── requirements.txt
└── docker-compose.yml
```

### 3.3. Flask 애플리케이션 코드 (`app/app.py`)

간단한 Flask 애플리케이션입니다. 데이터베이스 연결은 Compose 환경 변수를 통해 나중에 이루어질 것을 상정합니다.

```python
# python
from flask import Flask
import os
import psycopg2 # DB 연결 라이브러리 (여기서는 실제 연결하지 않음)
from datetime import datetime

app = Flask(__name__)

# 데이터베이스 연결 정보는 환경 변수에서 가져올 수 있습니다.
# 여기서는 실제 연결 대신 메시지만 출력합니다.
DATABASE_URL = os.getenv('DATABASE_URL', 'PostgreSQL URL not set')

@app.route('/')
def hello_world():
    # 실제 애플리케이션에서는 DATABASE_URL을 사용하여 DB에 연결할 것입니다.
    return f"<h1>Hello from Flask!</h1><p>The time is {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}</p><p>DB Info: {DATABASE_URL}</p>"

if __name__ == '__main__':
    # 0.0.0.0으로 설정해야 컨테이너 외부에서 접근 가능합니다.
    app.run(host='0.0.0.0', port=5000)
```

### 3.4. Flask 애플리케이션 의존성 (`app/requirements.txt`)

```
# python
Flask
psycopg2-binary # PostgreSQL 드라이버
```

### 3.5. `docker-compose.yml` 파일 작성

이제 가장 중요한 `docker-compose.yml` 파일을 작성해봅시다. 이 파일은 `my-flask-app` 디렉토리 바로 아래에 위치합니다.

```yaml
# python
version: '3.8' # Docker Compose 파일 형식 버전 (최신 버전을 사용하는 것이 좋습니다)

# 애플리케이션을 구성하는 서비스들을 정의합니다.
services:
  # 1. 웹 서비스 (Flask 애플리케이션)
  web:
    build:
      context: ./app # Dockerfile이 위치한 경로 (현재 디렉토리의 ./app 폴더)
      dockerfile: Dockerfile # 사용할 Dockerfile 이름 (여기서는 app 폴더 내의 Dockerfile)
    ports:
      - "5000:5000" # 호스트의 5000번 포트를 컨테이너의 5000번 포트에 연결
    environment: # 컨테이너 내에서 사용할 환경 변수 설정
      DATABASE_URL: postgres://user:password@db:5432/mydatabase # DB 접속 URL (db는 서비스 이름)
    depends_on: # 이 서비스가 시작하기 전에 의존하는 서비스들이 먼저 시작되어야 함
      - db # db 서비스가 먼저 시작된 후에 web 서비스가 시작됩니다.
    networks:
      - my_app_network # my_app_network라는 이름의 사용자 정의 네트워크에 연결

  # 2. 데이터베이스 서비스 (PostgreSQL)
  db:
    image: postgres:13 # 사용할 Docker 이미지 (PostgreSQL 13 버전)
    environment: # 컨테이너 내에서 사용할 환경 변수 설정
      POSTGRES_DB: mydatabase # 데이터베이스 이름
      POSTGRES_USER: user # 데이터베이스 사용자 이름
      POSTGRES_PASSWORD: password # 데이터베이스 사용자 비밀번호
    volumes:
      - db_data:/var/lib/postgresql/data # db_data라는 이름의 볼륨을 컨테이너의 DB 데이터 경로에 연결
    networks:
      - my_app_network # my_app_network라는 이름의 사용자 정의 네트워크에 연결

# 데이터 영속성을 위한 볼륨을 정의합니다.
volumes:
  db_data: # db_data라는 이름의 볼륨 정의 (PostgreSQL 데이터 저장)

# 서비스 간의 통신을 위한 네트워크를 정의합니다.
networks:
  my_app_network: # my_app_network라는 이름의 네트워크 정의
    driver: bridge # 브리지 네트워크 드라이버 사용
```

### 3.6. Flask 애플리케이션용 `Dockerfile` (`app/Dockerfile`)

`app` 폴더 내에 `Dockerfile`을 생성하여 Flask 애플리케이션 이미지를 빌드합니다.

```dockerfile
# python
# app/Dockerfile
FROM python:3.9-slim-buster # Python 3.9 슬림 버전 이미지를 기반으로 사용
WORKDIR /app # 컨테이너 내부 작업 디렉토리를 /app으로 설정
COPY requirements.txt . # 호스트의 requirements.txt 파일을 컨테이너의 /app 디렉토리로 복사
RUN pip install --no-cache-dir -r requirements.txt # 의존성 패키지 설치
COPY . . # 현재 디렉토리의 모든 파일(app.py 포함)을 컨테이너의 /app 디렉토리로 복사
CMD ["python", "app.py"] # 컨테이너 실행 시 app.py 파일을 실행
```

---

### 3.7. Docker Compose 실행하기

모든 파일이 준비되었다면, `my-flask-app` 디렉토리로 이동하여 다음 명령어를 실행합니다.

```bash
# python
docker compose up -d
```

*   `docker compose up`: `docker-compose.yml` 파일에 정의된 모든 서비스를 빌드하고 시작합니다.
*   `-d`: 컨테이너를 백그라운드(detached) 모드로 실행하여 터미널을 점유하지 않도록 합니다.

명령어를 실행하면 Docker Compose가 `web` 서비스의 Docker 이미지를 빌드하고, `db` 서비스를 위한 PostgreSQL 이미지를 다운로드한 후, 두 컨테이너를 네트워크에 연결하여 실행합니다.

모든 서비스가 성공적으로 시작되었는지 확인하려면 다음 명령어를 사용합니다.

```bash
# python
docker compose ps
```

출력 결과는 다음과 비슷하게 나타날 것입니다.

```
NAME                    COMMAND                  SERVICE             STATUS              PORTS
my-flask-app-db-1       "docker-entrypoint.s…"   db                  running (healthy)   5432/tcp
my-flask-app-web-1      "python app.py"          web                 running             0.0.0.0:5000->5000/tcp
```

이제 웹 브라우저를 열고 `http://localhost:5000`으로 접속해보세요! Flask 애플리케이션이 실행되고 있는 것을 확인할 수 있습니다.

### 3.8. 로그 확인하기

서비스의 로그를 확인하고 싶다면 다음 명령어를 사용합니다.

```bash
# python
docker compose logs
```

특정 서비스의 로그만 보고 싶다면 서비스 이름을 지정합니다.

```bash
# python
docker compose logs web
```

### 3.9. Docker Compose 중지 및 삭제

더 이상 서비스가 필요 없거나, 다시 시작하기 위해 모든 컨테이너를 중지하고 삭제하고 싶다면 다음 명령어를 사용합니다.

```bash
# python
docker compose down
```

*   이 명령은 `docker-compose.yml` 파일에 정의된 모든 컨테이너와 네트워크를 중지하고 제거합니다.
*   `volumes`에 정의된 볼륨은 기본적으로 삭제되지 않으므로, 데이터는 보존됩니다. 볼륨까지 삭제하려면 `docker compose down -v` 옵션을 사용합니다.

---

## 4. 마치며

이제 여러분은 Docker Compose의 기본 개념과 함께, 실제 다중 컨테이너 애플리케이션을 설정하고 실행하는 방법을 익히셨습니다. `docker-compose.yml` 파일 하나로 복잡한 개발 환경을 손쉽게 정의하고 관리할 수 있다는 것이 얼마나 강력한지 느끼셨으리라 생각합니다.

Docker Compose는 로컬 개발 환경을 구축하고, 테스트 환경을 일관되게 유지하며, 소규모 애플리케이션을 배포하는 데 매우 유용합니다. 이제 이 지식을 바탕으로 여러분의 개발 워크플로우를 한층 더 효율적으로 만들어보세요! 직접 다양한 서비스를 조합하여 `docker-compose.yml` 파일을 작성해보는 것을 추천합니다.

궁금한 점이나 추가적으로 다루었으면 하는 내용이 있다면 언제든지 댓글로 남겨주세요! 다음번에도 유익한 개발 이야기로 찾아오겠습니다.