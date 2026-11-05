---
layout: post
title: "GitHub Actions CI/CD 입문: 자동화된 개발 워크플로우의 시작"
date: 2026-11-05
categories: [CI/CD, GitHub Actions, 자동화, 개발 팁, 튜토리얼]
tags: [GitHubActions, CI, CD, DevOps, 워크플로우, 파이프라인, 자동화, 개발, 입문, 초보]
---

# GitHub Actions CI/CD 입문: 자동화된 개발 워크플로우의 시작

안녕하세요, 개발 블로그 독자 여러분! 🚀

개발자의 삶은 끊임없이 새로운 코드를 작성하고, 기능을 추가하고, 버그를 수정하는 과정의 연속입니다. 이 과정에서 우리는 코드를 빌드하고, 테스트하고, 서버에 배포하는 일련의 작업을 반복하게 됩니다. 혹시 이 모든 작업을 수동으로 처리하느라 지쳐본 적 없으신가요? 코드를 조금만 수정해도 다시 빌드하고, 테스트하고, 배포하는 것이 번거롭고 실수도 잦지는 않으셨나요?

오늘 우리는 이 모든 번거로움을 해결해 줄 마법 같은 도구, 바로 **GitHub Actions를 활용한 CI/CD**에 대해 이야기해볼까 합니다. 특히 개발 자동화의 첫걸음을 떼는 초보 개발자분들을 위해, CI/CD의 기본 개념부터 GitHub Actions로 간단한 파이프라인을 구축하는 방법까지 친절하게 안내해 드릴게요.

## 1. CI/CD, 그게 뭔데요?

먼저, CI/CD가 무엇인지부터 간단하게 짚고 넘어갈까요?

### 1.1. CI (Continuous Integration, 지속적인 통합)

CI는 개발자들이 작성한 코드를 **자주, 그리고 지속적으로 메인 브랜치에 병합(Integrate)하는 것**을 의미합니다. 단순히 코드만 합치는 것이 아니라, 병합될 때마다 **자동으로 빌드하고 테스트를 실행**하여 코드의 오류나 충돌을 조기에 발견하는 과정을 포함합니다.

**왜 중요할까요?**
*   **문제 조기 발견:** 작은 변경 사항마다 테스트를 진행하여 문제가 커지기 전에 해결할 수 있습니다.
*   **팀 협업 개선:** 여러 개발자가 동시에 작업해도 코드 충돌을 최소화하고 안정적인 코드 베이스를 유지할 수 있습니다.
*   **코드 품질 향상:** 자동화된 테스트를 통해 코드의 품질을 일정하게 유지할 수 있습니다.

### 1.2. CD (Continuous Delivery/Deployment, 지속적인 전달/배포)

CD는 CI 과정을 거쳐 **안정적으로 통합된 코드를 언제든지 배포 가능한 상태로 유지**하거나, 아예 **자동으로 실제 서비스 환경에 배포하는 것**을 의미합니다.

*   **Continuous Delivery (지속적인 전달):** CI가 성공적으로 완료된 후, 언제든지 수동으로 배포할 수 있는 상태로 애플리케이션을 유지하는 것을 목표로 합니다. 즉, 배포 자체는 수동으로 이루어집니다.
*   **Continuous Deployment (지속적인 배포):** 지속적인 전달에서 한 단계 더 나아가, CI 과정이 성공하면 자동으로 애플리케이션을 실제 서비스 환경에 배포하는 것을 의미합니다. 사람의 개입 없이 코드가 프로덕션 환경까지 도달하게 됩니다.

**왜 중요할까요?**
*   **빠른 릴리즈 주기:** 새로운 기능이나 버그 수정 사항을 고객에게 더 빠르게 전달할 수 있습니다.
*   **배포 안정성:** 수동 배포 시 발생할 수 있는 휴먼 에러를 줄이고 일관된 배포 프로세스를 보장합니다.
*   **피드백 루프 단축:** 변경 사항이 빠르게 배포되므로 고객 피드백을 신속하게 반영할 수 있습니다.

## 2. GitHub Actions, 왜 사용해야 하나요?

CI/CD 도구는 Jenkins, GitLab CI/CD, CircleCI 등 다양합니다. 그중에서도 GitHub Actions는 다음과 같은 장점들 덕분에 특히 많은 개발자들에게 사랑받고 있습니다.

*   **GitHub와 완벽한 통합:** 여러분의 코드가 있는 GitHub 저장소에 바로 CI/CD 파이프라인을 구축할 수 있습니다. 별도의 서비스 연동이 필요 없어 매우 편리합니다.
*   **YAML 기반의 쉬운 설정:** 복잡한 스크립트 대신 YAML 파일을 사용하여 워크플로우를 정의합니다. 직관적이고 가독성이 높아 초보자도 쉽게 접근할 수 있습니다.
*   **풍부한 마켓플레이스:** 다른 개발자들이 미리 만들어둔 수많은 '액션(Action)'들을 조합하여 재사용할 수 있습니다. 코드를 직접 작성할 필요 없이 원하는 기능을 손쉽게 추가할 수 있습니다.
*   **무료 사용 가능:** 공개 저장소(Public Repository)에서는 무료로 사용할 수 있으며, 개인 저장소(Private Repository)에도 충분히 넉넉한 무료 사용량을 제공합니다.

## 3. GitHub Actions 핵심 용어 정리

본격적으로 실습에 들어가기 전에, GitHub Actions에서 자주 사용되는 핵심 용어들을 빠르게 살펴볼까요?

*   **Workflow (워크플로우):** CI/CD 파이프라인의 전체적인 흐름을 정의하는 `.yml` 파일입니다. 특정 이벤트(예: 코드 푸시)가 발생하면 실행됩니다.
*   **Event (이벤트):** 워크플로우를 트리거하는 특정 활동입니다. `push` (코드 푸시), `pull_request` (PR 생성), `schedule` (정해진 시간) 등이 있습니다.
*   **Job (작업):** 워크플로우 내에서 독립적으로 실행되는 단위입니다. 여러 개의 스텝(Steps)으로 구성되며, 각각 다른 가상 환경(Runner)에서 실행될 수 있습니다.
*   **Step (단계):** Job 내에서 실행되는 개별적인 작업 단위입니다. 셸 명령어(`run`)를 실행하거나 미리 정의된 액션(`uses`)을 사용할 수 있습니다.
*   **Action (액션):** 특정 작업을 수행하는 재사용 가능한 코드 블록입니다. GitHub Marketplace에서 다양한 액션을 찾아 사용할 수 있습니다. (예: `actions/checkout`, `actions/setup-python`)
*   **Runner (러너):** 워크플로우의 Job이 실제로 실행되는 가상 머신(VM)입니다. Ubuntu, Windows, macOS 등 다양한 운영체제를 선택할 수 있습니다.

## 4. GitHub Actions CI/CD 파이프라인 구축하기 (Python 예제)

이제 실제로 GitHub Actions 워크플로우를 만들어볼 시간입니다! 간단한 Python 프로젝트를 예시로 CI(테스트)와 CD(가상 배포) 파이프라인을 구축해볼게요.

### 4.1. 프로젝트 준비

먼저, 로컬 환경에 간단한 Python 프로젝트를 생성하고 GitHub 저장소에 푸시해주세요.

```
my-python-app/
├── .github/
│   └── workflows/
│       └── ci-cd.yml  # 여기에 워크플로우 파일을 생성할 거예요.
├── main.py
├── test_main.py
└── requirements.txt
```

**`main.py` 파일:**

```python
# main.py
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

if __name__ == "__main__":
    print(f"1 + 2 = {add(1, 2)}")
    print(f"5 - 3 = {subtract(5, 3)}")
```

**`test_main.py` 파일 (pytest 사용):**

```python
# test_main.py
import pytest
from main import add, subtract

def test_add():
    assert add(1, 2) == 3
    assert add(0, 0) == 0
    assert add(-1, 1) == 0

def test_subtract():
    assert subtract(5, 3) == 2
    assert subtract(10, 0) == 10
    assert subtract(0, 5) == -5
```

**`requirements.txt` 파일:**

```
pytest
```

이 파일들을 모두 생성하고 GitHub 저장소에 `main` 브랜치로 푸시합니다.

### 4.2. CI/CD 워크플로우 파일 생성 (`.github/workflows/ci-cd.yml`)

이제 CI/CD 워크플로우를 정의하는 YAML 파일을 `.github/workflows/ci-cd.yml` 경로에 생성하고 아래 내용을 추가해 주세요.

```python
name: Python CI/CD Workflow

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repository
      uses: actions/checkout@v4 # 저장소 코드를 러너로 가져옵니다.

    - name: Set up Python 3.9
      uses: actions/setup-python@v5 # Python 환경을 설정합니다.
      with:
        python-version: '3.9'
        cache: 'pip' # pip 캐싱을 사용하여 의존성 설치 속도를 높입니다.

    - name: Install dependencies
      run: pip install -r requirements.txt # requirements.txt에 명시된 의존성을 설치합니다.

    - name: Run tests
      run: pytest # pytest를 사용하여 테스트를 실행합니다.

  deploy:
    runs-on: ubuntu-latest
    needs: build # 'build' Job이 성공적으로 완료되어야 이 Job이 실행됩니다.
    if: github.ref == 'refs/heads/main' && success() # main 브랜치에 푸시되었고, 이전 Job이 성공했을 때만 실행합니다.

    steps:
    - name: Deploy application
      run: |
        echo "🎉 CI가 성공했습니다! 이제 배포를 시작합니다..."
        echo "========================================"
        echo "🚀 가상 배포: my-python-app 서비스 배포 완료!"
        echo "실제 배포 로직이 여기에 들어갑니다."
        echo "예시: ssh를 통해 서버에 접속하여 애플리케이션 재시작, Docker 이미지 빌드 및 푸시, 클라우드 서비스 배포 명령어 등"
        echo "========================================"
```

### 4.3. 코드 설명

*   **`name: Python CI/CD Workflow`**: 워크플로우의 이름입니다. GitHub Actions 탭에서 이 이름으로 워크플로우를 식별할 수 있습니다.
*   **`on: push`, `pull_request`**: `main` 브랜치에 코드가 푸시되거나 Pull Request가 생성될 때 이 워크플로우를 실행하도록 트리거를 설정합니다.
*   **`jobs:`**: 워크플로우를 구성하는 작업들입니다. 여기서는 `build`와 `deploy` 두 가지 Job을 정의했습니다.
    *   **`build:`**:
        *   **`runs-on: ubuntu-latest`**: 이 Job이 최신 Ubuntu 운영체제에서 실행되도록 지정합니다.
        *   **`steps:`**: `build` Job 내에서 순차적으로 실행될 단계들입니다.
            *   `uses: actions/checkout@v4`: GitHub 저장소의 코드를 러너 가상 머신으로 가져오는 공식 액션입니다.
            *   `uses: actions/setup-python@v5`: Python 환경을 설정하는 공식 액션입니다. `python-version`으로 사용할 Python 버전을, `cache: 'pip'`로 의존성 캐싱을 설정하여 다음 실행 시 속도를 향상시킬 수 있습니다.
            *   `run: pip install -r requirements.txt`: 셸 명령어를 실행하여 `requirements.txt`에 명시된 Python 라이브러리들을 설치합니다.
            *   `run: pytest`: `pytest` 명령어를 실행하여 작성된 테스트 코드를 수행합니다. 테스트에 실패하면 이 Job은 실패하게 됩니다.
    *   **`deploy:`**:
        *   **`runs-on: ubuntu-latest`**: `deploy` Job도 Ubuntu 러너에서 실행됩니다.
        *   **`needs: build`**: 이 Job은 `build` Job이 성공적으로 완료된 후에만 실행되도록 의존성을 설정합니다.
        *   **`if: github.ref == 'refs/heads/main' && success()`**: `if` 조건을 사용하여 특정 상황에서만 Job이 실행되도록 할 수 있습니다. 여기서는 현재 브랜치가 `main`이고, 이전 Job(여기서는 `build`)이 성공했을 때만 `deploy` Job을 실행하도록 합니다.
        *   **`run: | ...`**: 실제 배포 로직이 들어갈 부분입니다. 예제에서는 "가상 배포" 메시지를 출력하지만, 실제로는 여기에 SSH를 통한 서버 접속, Docker 이미지 빌드 및 푸시, 클라우드 서비스 배포 명령어 등을 작성하게 됩니다.

### 4.4. 워크플로우 실행 및 결과 확인

`ci-cd.yml` 파일을 GitHub 저장소의 `main` 브랜치에 푸시해 보세요.

```bash
git add .github/workflows/ci-cd.yml
git commit -m "Add GitHub Actions CI/CD workflow"
git push origin main
```

이제 GitHub 저장소로 이동하여 상단의 **`Actions` 탭**을 클릭해 보세요.
방금 푸시한 커밋 메시지와 함께 워크플로우가 실행되고 있는 것을 확인할 수 있을 거예요.

*   워크플로우가 성공하면 초록색 체크 표시(✅)가 나타납니다.
*   실패하면 빨간색 X 표시(❌)가 나타나며, 어느 Job 또는 Step에서 실패했는지 상세 로그를 통해 확인할 수 있습니다.

`build` Job의 `Run tests` 단계에서 `pytest`가 실행되고, 모든 테스트가 통과하면 `deploy` Job이 이어서 실행되어 "가상 배포" 메시지가 출력될 것입니다.

## 5. 마치며

오늘은 GitHub Actions를 활용하여 CI/CD를 시작하는 방법에 대해 알아보았습니다. CI/CD는 개발 생산성을 높이고, 코드 품질을 향상시키며, 배포의 안정성을 보장하는 현대 소프트웨어 개발의 핵심 요소입니다.

GitHub Actions는 이러한 CI/CD 파이프라인을 쉽고 빠르게 구축할 수 있도록 도와주는 강력한 도구입니다. 처음에는 YAML 파일이 낯설고 복잡하게 느껴질 수도 있지만, 한 번 익숙해지고 나면 여러분의 개발 워크플로우를 혁신적으로 변화시켜 줄 것입니다.

작은 프로젝트부터 GitHub Actions CI/CD를 적용해 보면서 자동화의 편리함을 경험해 보세요! 다음 글에서는 더욱 심화된 GitHub Actions 활용법에 대해 다뤄보도록 하겠습니다.

긴 글 읽어주셔서 감사합니다! 궁금한 점이 있다면 언제든지 댓글로 남겨주세요.

---