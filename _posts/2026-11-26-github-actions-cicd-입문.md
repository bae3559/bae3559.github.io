---
layout: post
title: "GitHub Actions CI/CD 입문: 자동화된 개발의 첫걸음 🚀"
date: 2026-11-26
categories: [개발, GitHub, CI/CD, 자동화]
tags: [GitHubActions, CI/CD, 입문, 초보자, 개발자동화, 워크플로우, 파이썬, 개발블로그]
---

# GitHub Actions CI/CD 입문: 자동화된 개발의 첫걸음 🚀

안녕하세요! 개발자 블로그 작가입니다. 오늘 우리는 현대 소프트웨어 개발에서 빼놓을 수 없는 핵심 개념인 CI/CD(Continuous Integration/Continuous Delivery)와 이를 GitHub 환경에서 가장 쉽고 강력하게 구현할 수 있는 도구, 바로 **GitHub Actions**에 대해 이야기해 보려 합니다.

혹시 여러분의 개발 과정은 이런가요? 😥
*   코드 변경 후 수동으로 빌드하고 테스트해야 한다.
*   배포할 때마다 복잡한 스크립트를 직접 실행해야 한다.
*   팀원들의 코드가 합쳐질 때마다 예상치 못한 오류가 발생한다.

이제 이런 고민은 GitHub Actions와 함께 날려버릴 수 있습니다! 초보자도 쉽게 따라 할 수 있도록 친절하게 안내해 드릴게요.

---

## 🧐 CI/CD, 왜 중요할까요? (개념 설명)

본격적으로 GitHub Actions를 알아보기 전에, CI/CD가 무엇인지부터 짚고 넘어갑시다.

### CI: 지속적 통합 (Continuous Integration)
CI는 개발자들이 작성한 코드를 중앙 저장소에 **지속적으로 통합(merge)**하는 과정을 의미합니다. 단순히 코드를 합치는 것을 넘어, 통합될 때마다 자동으로 **빌드(Build)**하고 **테스트(Test)**를 실행하여 코드 변경으로 인한 문제를 빠르게 발견하고 해결하는 것을 목표로 합니다.

**✨ CI의 장점:**
*   **문제 조기 발견:** 작은 변경 사항이라도 즉시 테스트하여 오류를 빠르게 찾아냅니다.
*   **코드 품질 향상:** 자동화된 테스트를 통해 코드의 안정성을 높입니다.
*   **팀 협업 강화:** 코드 충돌을 줄이고 통합 과정을 원활하게 만듭니다.

### CD: 지속적 배포/전달 (Continuous Delivery/Deployment)
CD는 CI 단계를 성공적으로 거친 코드를 **자동으로 배포(Deploy)** 가능한 상태로 만들거나(Continuous Delivery), 혹은 아예 **실제 서비스 환경에 배포(Continuous Deployment)**하는 과정을 말합니다.

**✨ CD의 장점:**
*   **빠른 릴리즈:** 새로운 기능을 더 빠르고 자주 사용자에게 제공할 수 있습니다.
*   **수동 작업 감소:** 반복적인 배포 작업을 자동화하여 개발자의 부담을 줄입니다.
*   **안정적인 배포:** 자동화된 프로세스를 통해 배포 오류 가능성을 낮춥니다.

한마디로 CI/CD는 **"개발자가 코드를 작성하면, 자동으로 빌드하고 테스트해서 문제가 없으면 알아서 배포까지 해주는 똑똑한 비서"**라고 생각하시면 됩니다!

---

## 🐙 GitHub Actions, 너는 누구니?

GitHub Actions는 GitHub에서 제공하는 CI/CD 도구입니다. 여러분의 GitHub 저장소에서 발생하는 다양한 이벤트(예: 코드 푸시, 풀 리퀘스트 생성 등)에 반응하여 정의된 워크플로우를 자동으로 실행해 줍니다. 별도의 서버나 외부 도구 없이 GitHub 하나로 모든 것을 처리할 수 있다는 것이 가장 큰 장점이죠!

**✨ GitHub Actions의 핵심 요소:**

*   **Workflow (워크플로우):** CI/CD 파이프라인의 전체 과정을 정의한 파일입니다. `.yml` 확장자를 가진 파일로 작성하며, 프로젝트의 `.github/workflows` 디렉토리 안에 위치합니다.
*   **Event (이벤트):** 워크플로우를 실행시키는 특정 활동입니다. `push`, `pull_request`, `schedule` 등 다양한 이벤트가 있습니다.
*   **Job (작업):** 워크플로우 내에서 실행되는 논리적인 작업 단위입니다. 각 작업은 독립적으로 실행되며, 여러 단계(step)로 구성될 수 있습니다.
*   **Step (단계):** Job 내에서 실행되는 개별적인 명령 또는 액션입니다. 예를 들어, 코드 체크아웃, 의존성 설치, 테스트 실행 등이 하나의 단계가 될 수 있습니다.
*   **Action (액션):** GitHub Marketplace에서 제공되거나 직접 만들 수 있는 재사용 가능한 코드 단위입니다. 특정 작업을 수행하는 데 필요한 코드를 미리 정의해두어 워크플로우 작성을 간소화합니다. (예: `actions/checkout`, `actions/setup-python`)
*   **Runner (러너):** 워크플로우의 Job을 실행하는 가상 머신입니다. GitHub이 호스팅하는 러너를 사용하거나, 직접 호스팅하는 러너를 사용할 수도 있습니다. (예: `ubuntu-latest`, `windows-latest`)

---

## 🛠️ 첫 GitHub Actions 워크플로우 만들기 (코드 예제)

자, 이제 직접 간단한 파이썬 프로젝트에 CI 워크플로우를 적용해 봅시다. 목표는 다음과 같습니다:
1.  `main` 브랜치에 코드가 푸시될 때마다 워크플로우가 실행됩니다.
2.  Python 환경을 설정합니다.
3.  코드 스타일 검사(lint)를 실행합니다.
4.  간단한 스크립트를 실행하여 테스트를 흉내 냅니다.

### 1단계: 프로젝트 준비

먼저 간단한 파이썬 파일 `hello.py`를 만듭니다.
```python
# hello.py
import os

def greet(name):
    # 이 부분은 의도적으로 오류가 나지 않도록 작성했습니다.
    # 만약 들여쓰기 오류나 사용하지 않는 변수 등이 있다면 flake8이 잡아낼 것입니다.
    message = f"Hello, {name} from GitHub Actions CI/CD!"
    print(message)
    return message

if __name__ == "__main__":
    greet("Developer")

```

### 2단계: Workflow 파일 생성

여러분의 GitHub 저장소 루트에 `.github/workflows` 디렉토리를 만들고, 그 안에 `python-ci.yml` 파일을 생성합니다.

```
your-repo/
├── .github/
│   └── workflows/
│       └── python-ci.yml  <-- 이 파일을 만들 거예요!
└── hello.py
```

`python-ci.yml` 파일의 내용은 다음과 같습니다.

```python
# .github/workflows/python-ci.yml

name: Python CI Workflow # 워크플로우의 이름입니다. GitHub Actions 탭에서 볼 수 있어요.

on:
  push: # 어떤 이벤트가 발생했을 때 워크플로우를 실행할지 정의합니다.
    branches: [ main ] # main 브랜치에 push 이벤트가 발생했을 때 실행합니다.
  pull_request: # pull_request 이벤트에도 실행합니다.
    branches: [ main ]

jobs: # 실행할 작업(Job)들을 정의합니다.
  build-and-test: # 'build-and-test'라는 작업의 이름입니다.
    runs-on: ubuntu-latest # 이 작업을 실행할 러너 환경을 지정합니다. 여기서는 최신 Ubuntu를 사용합니다.

    steps: # 'build-and-test' 작업 내에서 실행될 단계(Step)들입니다.
    - name: Checkout repository # 첫 번째 단계: Git 저장소 코드를 러너로 가져옵니다.
      uses: actions/checkout@v4 # GitHub에서 제공하는 'checkout' 액션을 사용합니다.

    - name: Set up Python # 두 번째 단계: Python 환경을 설정합니다.
      uses: actions/setup-python@v5 # GitHub에서 제공하는 'setup-python' 액션을 사용합니다.
      with:
        python-version: '3.x' # Python 3.x 버전을 사용하도록 지정합니다.

    - name: Install dependencies # 세 번째 단계: 필요한 파이썬 라이브러리를 설치합니다.
      run: | # 'run' 키워드 다음에는 셸 명령어를 직접 작성할 수 있습니다.
        python -m pip install --upgrade pip # pip를 최신 버전으로 업그레이드합니다.
        pip install flake8 # 코드 스타일 검사 도구인 flake8을 설치합니다.

    - name: Lint with flake8 # 네 번째 단계: flake8으로 코드 스타일을 검사합니다.
      run: |
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        flake8 . --count --max-complexity=10 --max-line-length=120 --statistics

    - name: Run a simple script # 다섯 번째 단계: 작성한 hello.py 스크립트를 실행합니다. (간단한 테스트 역할)
      run: |
        python hello.py

```

### 3단계: GitHub에 푸시하기

이제 변경된 파일을 Git에 추가하고 커밋한 다음, GitHub 저장소의 `main` 브랜치에 푸시합니다.

```bash
git add .
git commit -m "Add GitHub Actions CI workflow"
git push origin main
```

### 4단계: 결과 확인하기

GitHub 저장소로 이동하여 상단 메뉴의 **"Actions"** 탭을 클릭해 보세요.
방금 푸시한 커밋 메시지와 함께 워크플로우가 실행 중이거나 이미 완료된 것을 볼 수 있을 겁니다!
✅ 초록색 체크 표시는 모든 작업이 성공적으로 완료되었음을 의미하고, ❌ 빨간색 X 표시는 실패했음을 의미합니다.

워크플로우를 클릭하면 각 Job과 Step의 상세 로그를 확인할 수 있습니다. 예를 들어, `Lint with flake8` 단계에서 오류가 발생했다면, 어떤 파일의 몇 번째 줄에서 어떤 문제가 발생했는지 정확히 알 수 있죠!

---

## 📈 한 걸음 더 나아가기

이 예제는 CI의 가장 기본적인 부분만을 다뤘지만, GitHub Actions는 이보다 훨씬 더 많은 기능을 제공합니다.
*   **배포 자동화:** CI를 넘어 CD를 구현하여, 테스트를 통과한 코드를 자동으로 클라우드 서버(AWS, Azure, GCP 등)나 Docker Hub 등에 배포할 수 있습니다.
*   **Secrets:** API 키나 비밀번호와 같은 민감한 정보를 안전하게 관리하고 워크플로우에서 사용할 수 있습니다.
*   **여러 Job 연결:** 한 Job의 결과물을 다음 Job으로 전달하거나, 여러 Job을 병렬로 실행할 수 있습니다.
*   **Marketplace Actions 활용:** 수많은 개발자들이 만들어 공유한 유용한 Action들을 활용하여 복잡한 기능도 쉽게 구현할 수 있습니다. (예: Slack 알림 보내기, Docker 이미지 빌드 등)

---

## 🙌 마무리하며

GitHub Actions는 개발 생산성을 극대화하고, 코드 품질을 유지하며, 팀 협업을 강화하는 데 필수적인 도구입니다. 처음에는 YAML 문법이나 CI/CD 개념이 어렵게 느껴질 수 있지만, 이 글에서 소개한 기본적인 워크플로우를 직접 만들어 보고 성공을 경험하는 순간, 여러분은 자동화된 개발의 즐거움에 푹 빠지게 될 것입니다!

이제 여러분의 프로젝트에 GitHub Actions를 적용하여 더 스마트하고 효율적인 개발을 시작해 보세요! 궁금한 점이 있다면 언제든지 댓글로 질문해주세요. 😊

---