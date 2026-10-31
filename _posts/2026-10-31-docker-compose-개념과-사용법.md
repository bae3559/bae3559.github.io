---
layout: post
title: "Docker Compose, 복잡한 컨테이너 애플리케이션을 한 번에 관리하는 비법!"
date: 2026-10-31
categories: [Docker, DevOps]
tags: [Docker Compose, Docker, 컨테이너, 개발환경, YAML, 튜토리얼]
---

# Docker Compose, 복잡한 컨테이너 애플리케이션을 한 번에 관리하는 비법!

안녕하세요! 개발 생산성 향상을 위해 늘 고민하는 블로그 작가입니다. 오늘은 여러분의 개발 환경을 획기적으로 개선해 줄 도구, 바로 **Docker Compose**에 대해 이야기해보려 합니다. 여러 개의 컨테이너를 함께 다뤄야 하는 상황에서 일일이 명령어를 입력하며 스트레스받으셨던 분이라면, 이 글이 큰 도움이 될 거예요!

## 💡 왜 Docker Compose가 필요한가요?

현대의 애플리케이션은 대부분 여러 개의 컴포넌트로 구성됩니다. 예를 들어, 간단한 웹 서비스만 해도 웹 서버(Nginx 또는 Apache), 백엔드 애플리케이션(Node.js, Python Flask/Django, Java Spring Boot 등), 그리고 데이터베이스(PostgreSQL, MySQL, MongoDB 등)가 필요하죠. 때로는 캐시(Redis), 메시지 큐(RabbitMQ) 같은 서비스도 함께 사용됩니다.

이 모든 컴포넌트를 Docker 컨테이너로 각각 띄운다고 상상해 보세요.
`docker run ... 웹 서버`
`docker run ... 백엔드`
`docker run ... 데이터베이스`
... 이런 식으로 여러 개의 `docker run` 명령어를 일일이 입력해야 할 겁니다. 컨테이너 간의 네트워크 연결도 설정해야 하고, 환경 변수도 맞춰줘야 합니다. 개발 단계에서 애플리케이션을 껐다 켰다 할 때마다 이 모든 과정을 반복하는 것은 여간 번거로운 일이 아닐 수 없습니다. 게다가 팀원 간에 동일한 환경을 설정하는 것도 쉽지 않겠죠?

이러한 문제들을 해결해 주는 마법 같은 도구가 바로 **Docker Compose**입니다!

## 🚀 Docker Compose란 무엇인가요?

Docker Compose는 **다중 컨테이너 Docker 애플리케이션을 정의하고 실행하는 도구**입니다. 쉽게 말해, 우리 애플리케이션을 구성하는 여러 컨테이너(서비스)들의 정보를 하나의 YAML 파일에 정의해두고, 이 파일을 이용해 모든 컨테이너를 한 번에 `시작`, `중지`, `재시작`할 수 있게 해줍니다.

핵심은 `docker-compose.yml`이라는 설정 파일입니다. 이 파일 안에 우리가 필요한 서비스들(웹, DB 등)이 어떤 Docker 이미지로 실행될지, 어떤 포트를 사용할지, 어떤 볼륨을 연결할지, 어떤 환경 변수를 가질지 등을 명시합니다.

**Docker Compose를 사용하면 얻을 수 있는 이점:**

1.  **단일 파일 정의**: 모든 서비스 구성을 `docker-compose.yml` 파일 하나로 관리할 수 있습니다.
2.  **간편한 실행**: 단 하나의 명령어로 모든 서비스를 시작하고 중지할 수 있습니다.
3.  **환경 일관성**: 팀원 모두가 동일한 개발 환경을 쉽게 구축하고 유지할 수 있습니다.
4.  **재현 가능성**: 개발, 테스트, 배포 환경 간의 일관성을 유지하기 용이합니다.

## 🛠️ Docker Compose 사용법: 간단한 예제와 함께

이제 실제로 Docker Compose를 사용하는 방법을 예제와 함께 알아보겠습니다. 간단한 Python Flask 웹 애플리케이션과 PostgreSQL 데이터베이스를 연동하는 시나리오를 만들어볼게요.

### 1. 프로젝트 구조 만들기

먼저, 다음과 같은 프로젝트 구조를 만듭니다.

```
my-flask-app/
├── app.py
├── Dockerfile
├── requirements.txt
└── docker-compose.yml
```

### 2. Flask 애플리케이션 코드 작성 (`app.py`)

PostgreSQL에 접속하여 간단한 데이터를 조회하는 Flask 앱입니다.

```python
# app.py
import os
from flask import Flask
import psycopg2

app = Flask(__name__)

# 환경 변수에서 DB 접속 정보 가져오기
DB_HOST = os.environ.get('DB_HOST', 'localhost')
DB_NAME = os.environ.get('DB_NAME', 'mydb')
DB_USER = os.environ.get('DB_USER', 'user')
DB_PASSWORD = os.environ.get('DB_PASSWORD', 'password')

@app.route('/')
def hello():
    try:
        conn = psycopg2.connect(
            host=DB_HOST,
            database=DB_NAME,
            user=DB_USER,
            password=DB_PASSWORD
        )
        cur = conn.cursor()
        cur.execute("SELECT version();")
        db_version = cur.fetchone()[0]
        cur.close()
        conn.close()
        return f"Hello from Flask! Connected to PostgreSQL: {db_version}"
    except Exception as e:
        return f"Failed to connect to DB: {e}", 500

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)

```

### 3. Python 의존성 파일 작성 (`requirements.txt`)

Flask 앱이 필요로 하는 라이브러리 목록입니다.

```
# requirements.txt
Flask
psycopg2-binary
```

### 4. Flask 앱을 위한 Dockerfile 작성 (`Dockerfile`)

Flask 앱을 컨테이너로 만들기 위한 Dockerfile입니다.

```dockerfile
# Dockerfile
FROM python:3.9-slim-buster

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

### 5. Docker Compose 설정 파일 작성 (`docker-compose.yml`)

이제 핵심인 `docker-compose.yml` 파일을 작성합니다. 여기에 웹 서비스와 데이터베이스 서비스를 정의합니다.

```yaml
# docker-compose.yml
version: '3.8' # Docker Compose 파일 형식 버전

services:
  # 웹 서비스 (Flask 애플리케이션) 정의
  web:
    build: . # 현재 디렉토리의 Dockerfile을 사용하여 이미지 빌드
    ports:
      - "5000:5000" # 호스트의 5000번 포트를 컨테이너의 5000번 포트에 연결
    environment: # 컨테이너 내에서 사용할 환경 변수 설정
      DB_HOST: db # 데이터베이스 컨테이너의 서비스 이름 (Docker Compose 내부 DNS로 해결됨)
      DB_NAME: mydb
      DB_USER: user
      DB_PASSWORD: password
    depends_on: # 이 서비스가 시작되기 전에 'db' 서비스가 시작되어야 함을 명시
      - db

  # 데이터베이스 서비스 (PostgreSQL) 정의
  db:
    image: postgres:13 # PostgreSQL 13 버전을 사용
    environment: # 데이터베이스 설정에 필요한 환경 변수
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes: # 데이터 지속성을 위한 볼륨 마운트
      - db_data:/var/lib/postgresql/data # 호스트의 'db_data' 볼륨을 컨테이너의 데이터 저장 경로에 연결

volumes: # Docker 볼륨 정의 (데이터 지속성 유지)
  db_data:
```

**`docker-compose.yml` 파일의 주요 요소 설명:**

*   `version`: Docker Compose 파일 형식 버전을 지정합니다.
*   `services`: 이 섹션 아래에 애플리케이션을 구성하는 각 컨테이너(서비스)를 정의합니다.
    *   `web`: Flask 웹 애플리케이션 서비스입니다.
        *   `build: .`: 현재 디렉터리의 `Dockerfile`을 사용하여 Docker 이미지를 빌드합니다.
        *   `ports: - "5000:5000"`: 호스트 머신의 5000번 포트를 컨테이너의 5000번 포트로 포워딩합니다. 웹 브라우저에서 `localhost:5000`으로 접속할 수 있게 됩니다.
        *   `environment`: 컨테이너 내부에서 사용할 환경 변수를 설정합니다. Flask 앱이 데이터베이스에 접속할 때 이 변수들을 사용합니다. `DB_HOST: db`는 중요합니다. Docker Compose는 서비스 이름을 내부 DNS로 자동으로 해결해 주기 때문에, `db`라는 이름으로 데이터베이스 컨테이너에 접근할 수 있습니다.
        *   `depends_on: - db`: `web` 서비스가 시작되기 전에 `db` 서비스가 먼저 시작되어야 함을 Docker Compose에게 알려줍니다.
    *   `db`: PostgreSQL 데이터베이스 서비스입니다.
        *   `image: postgres:13`: Docker Hub에서 공식 `postgres:13` 이미지를 가져와 사용합니다.
        *   `environment`: PostgreSQL 데이터베이스의 초기 설정(데이터베이스 이름, 사용자, 비밀번호)을 위한 환경 변수입니다.
        *   `volumes: - db_data:/var/lib/postgresql/data`: 데이터베이스 데이터가 컨테이너가 삭제되어도 사라지지 않도록 영구적으로 저장하기 위해 볼륨을 마운트합니다. `db_data`는 아래 `volumes` 섹션에서 정의된 이름을 사용합니다.
*   `volumes`: Docker 볼륨을 정의하는 섹션입니다. `db_data`라는 이름의 볼륨을 생성하여 `db` 서비스의 데이터를 저장합니다.

### 6. 애플리케이션 실행하기

이제 `my-flask-app` 디렉토리로 이동하여 다음 명령어를 실행합니다.

```bash
docker compose up -d
```

*   `docker compose up`: `docker-compose.yml` 파일에 정의된 모든 서비스를 시작합니다.
*   `-d`: `detached` 모드를 의미합니다. 백그라운드에서 컨테이너들을 실행하고 터미널을 다시 사용할 수 있게 합니다. 이 옵션이 없으면 컨테이너 로그가 터미널에 계속 출력됩니다.

명령어를 실행하면 Docker Compose가 Flask 앱 이미지를 빌드하고, PostgreSQL 이미지를 다운로드한 뒤, 두 서비스를 동시에 시작할 것입니다.

### 7. 실행 확인 및 접속

모든 컨테이너가 잘 실행 중인지 확인합니다.

```bash
docker compose ps
```

출력 결과는 다음과 비슷할 것입니다:

```
NAME                COMMAND                  SERVICE             STATUS              PORTS
my-flask-app-db-1   "docker-entrypoint.s…"   db                  running             5432/tcp
my-flask-app-web-1  "python app.py"          web                 running             0.0.0.0:5000->5000/tcp
```

이제 웹 브라우저를 열고 `http://localhost:5000`으로 접속해 보세요. 다음과 같은 메시지를 볼 수 있을 겁니다.

```
Hello from Flask! Connected to PostgreSQL: PostgreSQL 13.x (...)
```

만약 `Failed to connect to DB` 같은 메시지가 나온다면, `docker compose logs web` 명령어로 웹 서비스의 로그를 확인하여 문제를 진단할 수 있습니다.

### 8. 애플리케이션 중지 및 삭제

모든 컨테이너를 중지하고 삭제하려면 다음 명령어를 사용합니다.

```bash
docker compose down
```

이 명령어는 `docker-compose.yml` 파일에 정의된 모든 컨테이너, 네트워크를 중지하고 제거합니다. 단, `volumes` 섹션에 정의된 볼륨(예: `db_data`)은 기본적으로 삭제되지 않아, 다음에 다시 `up` 할 때 이전 데이터가 유지됩니다. 볼륨까지 완전히 삭제하려면 `docker compose down --volumes` 옵션을 사용하세요.

## ✨ 마치며

오늘은 Docker Compose의 개념부터 간단한 예제를 통해 사용법까지 자세히 알아보았습니다. Docker Compose는 여러 컨테이너로 구성된 복잡한 애플리케이션을 훨씬 쉽고 효율적으로 개발하고 관리할 수 있도록 돕는 강력한 도구입니다.

이제 더 이상 여러 개의 `docker run` 명령어를 수동으로 입력할 필요 없이, 단 하나의 `docker-compose.yml` 파일과 `docker compose up` 명령어로 여러분의 개발 환경을 손쉽게 구축하고 관리할 수 있을 겁니다! 개발 환경 설정의 번거로움에서 벗어나, 오직 코드 작성에만 집중해 보세요.

다음번에는 더 유용한 개발 이야기로 찾아오겠습니다! Happy Hacking!

---