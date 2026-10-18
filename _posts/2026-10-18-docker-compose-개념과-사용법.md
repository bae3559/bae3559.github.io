---
layout: post
title: "Docker Compose, 복잡한 컨테이너 오케스트레이션의 시작 (초보자를 위한 완벽 가이드)"
date: 2026-10-18
categories: [Docker, DevOps, 컨테이너]
tags: [DockerCompose, 컨테이너오케스트레이션, 개발환경설정, 초보자가이드, YAML]
---

# Docker Compose, 복잡한 컨테이너 오케스트레이션의 시작 (초보자를 위한 완벽 가이드)

안녕하세요, 개발자 여러분! 컨테이너 기술, 특히 Docker는 현대 개발 환경에서 빼놓을 수 없는 중요한 도구가 되었습니다. 하나의 서비스를 컨테이너로 띄우는 것은 이제 익숙한 일이지만, 웹 서버, 데이터베이스, 캐시 서버 등 여러 컨테이너가 서로 유기적으로 연결되어 동작해야 하는 복잡한 애플리케이션 환경을 설정하는 것은 여전히 쉽지 않은 작업입니다.

매번 `docker run` 명령어를 여러 번 입력하고, 각 컨테이너의 네트워크와 볼륨을 수동으로 연결하는 과정은 매우 번거롭고 실수할 확률도 높죠. 이런 고민을 해결하기 위해 등장한 것이 바로 **Docker Compose**입니다!

이번 글에서는 Docker Compose가 무엇인지, 왜 필요한지, 그리고 실제로 어떻게 사용하는지에 대해 초보자도 이해할 수 있도록 친절하게 설명해 드릴게요. 이제 복잡한 환경 설정은 잊고, 단 하나의 명령어로 여러분의 서비스를 띄워보세요!

---

## 1. Docker Compose란 무엇일까요?

Docker Compose는 **다중 컨테이너 Docker 애플리케이션을 정의하고 실행하기 위한 도구**입니다. 간단히 말해, 여러분의 애플리케이션을 구성하는 여러 컨테이너들(예: 웹 서버, 데이터베이스, Redis 캐시 등)의 설정 정보를 하나의 `YAML` 파일에 정의해두고, 이 파일을 통해 모든 컨테이너를 한 번에 생성, 시작, 중지, 삭제할 수 있게 해줍니다.

각 컨테이너의 이미지, 포트 매핑, 볼륨, 환경 변수, 네트워크 설정 등을 `docker-compose.yml` 파일 하나에 명시함으로써, 개발 환경을 일관성 있게 유지하고 배포 과정을 간소화할 수 있습니다.

## 2. 왜 Docker Compose를 사용해야 할까요?

Docker Compose를 사용하면 다음과 같은 장점들을 얻을 수 있습니다.

*   **단순성 및 일관성:** 복잡한 `docker run` 명령어를 일일이 입력할 필요 없이, 모든 설정을 `docker-compose.yml` 파일 하나로 관리할 수 있습니다. 덕분에 개발 환경을 팀원 간에 쉽게 공유하고 일관성을 유지할 수 있죠.
*   **재현성 (Reproducibility):** 동일한 `docker-compose.yml` 파일만 있다면, 어느 환경에서든 동일한 컨테이너 환경을 정확하게 재현할 수 있습니다. "제 컴퓨터에서는 잘 되는데요?"라는 말을 줄일 수 있습니다!
*   **효율성:** 개발 환경을 빠르게 구축하고, 테스트 후 쉽게 정리할 수 있습니다. 단일 명령어로 전체 서비스를 시작하고 중지할 수 있다는 것은 개발 생산성을 크게 향상시킵니다.
*   **쉬운 의존성 관리:** 컨테이너 간의 의존성을 설정하여, 데이터베이스 컨테이너가 먼저 시작된 후에 웹 애플리케이션 컨테이너가 시작되도록 제어할 수 있습니다.
*   **가벼운 오케스트레이션:** Docker Swarm이나 Kubernetes와 같은 대규모 오케스트레이션 도구에 비해 가볍고 빠르게 다중 컨테이너 환경을 구성할 수 있습니다.

## 3. Docker Compose의 핵심 개념

`docker-compose.yml` 파일을 작성할 때 알아야 할 주요 구성 요소들을 살펴볼게요.

*   **`version`**: `docker-compose.yml` 파일의 형식 버전을 지정합니다. 최신 버전은 `3.x`대이며, 기능이 계속 업데이트되고 있습니다. (예: `version: '3.8'`)
*   **`services`**: 여러분의 애플리케이션을 구성하는 각각의 컨테이너 단위를 정의하는 섹션입니다. 각 서비스는 고유한 이름을 가집니다 (예: `web`, `db`).
    *   **`image`**: 이 서비스를 시작할 때 사용할 Docker 이미지 이름을 지정합니다 (예: `nginx:latest`, `postgres:13`).
    *   **`build`**: Dockerfile을 사용하여 사용자 정의 이미지를 빌드해야 하는 경우, Dockerfile이 위치한 경로를 지정합니다 (예: `./app`).
    *   **`ports`**: 호스트(내 컴퓨터)와 컨테이너 간의 포트 매핑을 설정합니다 (예: `"80:80"`는 호스트의 80번 포트를 컨테이너의 80번 포트에 연결합니다).
    *   **`volumes`**: 데이터를 영구적으로 저장하거나, 호스트의 특정 디렉터리를 컨테이너 내부에 마운트할 때 사용합니다 (예: `./data:/var/lib/mysql`).
    *   **`environment`**: 컨테이너 내부에 전달할 환경 변수를 설정합니다 (예: `DATABASE_URL: postgres://user:password@db/mydb`).
    *   **`depends_on`**: 서비스 간의 의존성을 정의합니다. 예를 들어 `web` 서비스가 `db` 서비스에 의존한다면, `db` 서비스가 먼저 시작될 것입니다.
    *   **`networks`**: 컨테이너들이 통신할 네트워크를 지정합니다. 별도로 설정하지 않으면 Docker Compose가 기본 네트워크를 생성하여 연결해줍니다.
*   **`volumes`**: `services` 섹션에서 사용할 명명된 볼륨(Named Volume)을 정의합니다. 영구적인 데이터 저장을 위해 유용합니다.
*   **`networks`**: 사용자 정의 네트워크를 정의할 수 있습니다.

## 4. Docker Compose 사용법 (예제와 함께)

간단한 파이썬 Flask 웹 애플리케이션과 PostgreSQL 데이터베이스로 구성된 환경을 Docker Compose로 설정하는 예제를 통해 사용법을 익혀봅시다.

### 4.1. 프로젝트 구조

먼저 다음과 같은 디렉터리 구조를 만들어주세요.

```
my-flask-app/
├── app/
│   ├── app.py
│   └── requirements.txt
├── Dockerfile
└── docker-compose.yml
```

### 4.2. `app/app.py` 파일 작성

간단한 Flask 애플리케이션입니다. 데이터베이스 연결을 시도하고 성공 여부를 반환합니다.

```python
# app/app.py
import os
import time
from flask import Flask
import psycopg2

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Flask! Trying to connect to DB..."

@app.route('/db_test')
def db_test():
    try:
        # 환경 변수로부터 DB 연결 정보 가져오기
        db_host = os.environ.get("DB_HOST", "db")
        db_name = os.environ.get("POSTGRES_DB", "mydatabase")
        db_user = os.environ.get("POSTGRES_USER", "myuser")
        db_password = os.environ.get("POSTGRES_PASSWORD", "mypassword")

        # 데이터베이스 연결 시도 (DB가 완전히 준비될 때까지 기다릴 수 있음)
        conn = None
        for _ in range(5): # 최대 5번 시도
            try:
                conn = psycopg2.connect(
                    host=db_host,
                    database=db_name,
                    user=db_user,
                    password=db_password
                )
                if conn:
                    cursor = conn.cursor()
                    cursor.execute("SELECT version();")
                    db_version = cursor.fetchone()
                    cursor.close()
                    conn.close()
                    return f"Successfully connected to PostgreSQL! Version: {db_version}"
            except psycopg2.OperationalError as e:
                print(f"DB connection failed, retrying in 2 seconds... Error: {e}")
                time.sleep(2)
        
        return "Failed to connect to PostgreSQL after multiple retries."

    except Exception as e:
        return f"An error occurred: {e}"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)

```

### 4.3. `app/requirements.txt` 파일 작성

Flask와 PostgreSQL 드라이버를 정의합니다.

```
# app/requirements.txt
Flask
psycopg2-binary
```

### 4.4. `Dockerfile` 파일 작성

Flask 애플리케이션을 빌드하기 위한 Dockerfile입니다.

```dockerfile
# Dockerfile
FROM python:3.9-slim-buster

WORKDIR /app

COPY ./app/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY ./app .

EXPOSE 5000

CMD ["python", "app.py"]
```

### 4.5. `docker-compose.yml` 파일 작성

이제 핵심인 `docker-compose.yml` 파일을 작성합니다.

```yaml
# docker-compose.yml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    environment:
      # DB 연결 정보는 환경 변수로 전달
      DB_HOST: db # 'db'는 docker-compose.yml 파일 내의 서비스 이름
      POSTGRES_DB: mydatabase
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
    volumes:
      - ./app:/app # 개발 편의를 위해 호스트의 app 폴더를 컨테이너에 마운트
    depends_on:
      - db # db 서비스가 먼저 시작된 후에 web 서비스가 시작됩니다.

  db:
    image: postgres:13
    environment:
      POSTGRES_DB: mydatabase
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
    volumes:
      - db-data:/var/lib/postgresql/data # 데이터 영속성을 위한 볼륨
    restart: always # 컨테이너 종료 시 항상 재시작

volumes:
  db-data: # 명명된 볼륨 정의 (db-data라는 이름으로 생성)
```

**설명:**
*   `web` 서비스는 현재 디렉터리(`build: .`)의 Dockerfile을 사용하여 이미지를 빌드합니다.
*   `web` 서비스의 5000번 포트가 호스트의 5000번 포트에 연결됩니다.
*   `web` 서비스는 `db` 서비스에 의존하므로, `db` 서비스가 먼저 시작됩니다.
*   `db` 서비스는 `postgres:13` 이미지를 사용합니다.
*   `db-data`라는 이름의 볼륨을 사용하여 PostgreSQL 데이터가 컨테이너가 삭제되어도 유지되도록 설정했습니다.

### 4.6. Docker Compose 실행하기

`my-flask-app` 디렉터리에서 터미널을 열고 다음 명령어를 입력합니다.

```bash
docker compose up -d
```

*   `up`: `docker-compose.yml` 파일에 정의된 모든 서비스를 시작합니다.
*   `-d`: 컨테이너들을 백그라운드(detached mode)에서 실행합니다. 터미널을 닫아도 컨테이너는 계속 실행됩니다.

명령어를 실행하면 Docker Compose가 `web` 서비스를 위한 이미지를 빌드하고, `web` 컨테이너와 `db` 컨테이너를 생성한 후 시작할 것입니다.

모든 컨테이너가 성공적으로 시작되면, 웹 브라우저에서 `http://localhost:5000`으로 접속하여 Flask 애플리케이션을 확인할 수 있습니다. `/db_test` 엔드포인트에 접속하여 (`http://localhost:5000/db_test`) 데이터베이스 연결 테스트 결과를 확인할 수도 있습니다.

### 4.7. Docker Compose 주요 명령어

*   **서비스 시작:**
    ```bash
    docker compose up -d
    ```
*   **서비스 중지 및 컨테이너 삭제:** (볼륨은 삭제되지 않음)
    ```bash
    docker compose down
    ```
*   **서비스 중지, 컨테이너 및 볼륨 삭제:** (개발 환경을 완전히 초기화할 때 사용)
    ```bash
    docker compose down --volumes
    ```
*   **서비스 빌드/재빌드:**
    ```bash
    docker compose build
    ```
*   **실행 중인 서비스 목록 확인:**
    ```bash
    docker compose ps
    ```
*   **서비스 로그 확인:**
    ```bash
    docker compose logs -f web # web 서비스의 로그를 실시간으로 확인
    ```
*   **컨테이너 내부로 접속:**
    ```bash
    docker compose exec web bash # web 서비스 컨테이너 내부에 bash 쉘로 접속
    ```

---

## 5. 마치며

이번 글에서는 Docker Compose의 개념부터 실제 사용 예제까지 자세히 살펴보았습니다. 이제 여러분은 복잡한 다중 컨테이너 환경도 `docker-compose.yml` 파일 하나로 손쉽게 관리할 수 있게 되었습니다.

Docker Compose는 개발 환경을 표준화하고 팀원 간의 협업을 원활하게 하는 데 큰 도움을 줍니다. 처음에는 YAML 파일 작성과 설정이 다소 복잡하게 느껴질 수 있지만, 몇 번 연습하다 보면 그 편리함에 푹 빠지게 될 거예요!

여러분의 개발 여정에 Docker Compose가 큰 힘이 되기를 바랍니다. 다음에는 더 유익한 주제로 찾아오겠습니다!

---