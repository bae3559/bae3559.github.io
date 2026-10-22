---
layout: post
title: "GitHub Actions CI/CD, 자동화의 마법을 시작해보세요! (초보자 가이드)"
date: 2026-10-22
categories: [개발, DevOps, CI/CD]
tags: [GitHubActions, CI/CD, 자동화, 개발블로그, 초보자, 파이썬, YAML]
---

# GitHub Actions CI/CD, 자동화의 마법을 시작해보세요! (초보자 가이드)

안녕하세요, 개발 블로그 독자 여러분! 🚀

혹시 여러분의 개발 과정이 이런 식이었나요?
*   코드 변경 후 수동으로 테스트를 실행하고...
*   서버에 접속해서 파일을 일일이 업로드하거나, 복잡한 명령어를 입력해서 배포하고...
*   이 과정에서 깜빡하거나 실수해서 버그가 발생하고...

이런 반복적이고 실수하기 쉬운 작업들을 자동화해주는 든든한 친구, 바로 **CI/CD**입니다! 그리고 이 CI/CD를 여러분의 GitHub 저장소 안에서 손쉽게 구현할 수 있게 해주는 도구가 바로 **GitHub Actions**입니다.

오늘은 GitHub Actions를 활용한 CI/CD의 기본 개념부터, 여러분의 첫 번째 자동화 워크플로우를 직접 만들어보는 시간까지 가져볼 거예요. 초보자분들도 쉽게 따라 할 수 있도록 친절하게 안내해 드릴 테니, 함께 자동화의 세계로 떠나볼까요?

---

## 1. CI/CD, 왜 중요할까요? 🤔

CI/CD는 **Continuous Integration (지속적인 통합)**과 **Continuous Delivery/Deployment (지속적인 배포)**의 약자입니다. 이름만 들어도 벌써 뭔가 대단해 보이죠?

### 1.1. Continuous Integration (CI) - 지속적인 통합

개발자들이 각자 작업한 코드를 메인 코드베이스에 자주(지속적으로) 통합하는 과정을 의미합니다. 이때 단순히 코드를 합치는 것을 넘어, **자동화된 테스트, 빌드, 코드 품질 검사** 등을 함께 수행합니다.

*   **주요 목표:** 통합 충돌을 조기 발견하고, 버그를 빠르게 찾아내어 개발 비용을 절감합니다.
*   **예시:** 새로운 기능 코드를 `main` 브랜치에 `push`하면, GitHub Actions가 자동으로 코드 스타일을 검사하고, 유닛 테스트를 실행하여 문제가 없는지 확인합니다.

### 1.2. Continuous Delivery / Deployment (CD) - 지속적인 배포

CI 과정에서 성공적으로 통합되고 테스트된 코드를 **자동으로 운영 환경(또는 스테이징 환경)에 배포할 준비를 하거나, 실제로 배포하는 과정**을 의미합니다.

*   **Continuous Delivery:** 언제든 배포할 수 있는 상태로 만들지만, 최종 배포는 수동으로 결정합니다.
*   **Continuous Deployment:** CI를 통과한 코드를 아무런 수동 개입 없이 자동으로 운영 환경에 배포합니다.
*   **주요 목표:** 수동 배포로 인한 실수를 줄이고, 더 빠르고 안정적인 소프트웨어 출시를 가능하게 합니다.
*   **예시:** CI를 통과한 코드가 `main` 브랜치에 병합되면, GitHub Actions가 자동으로 서버에 코드를 배포하고 애플리케이션을 재시작합니다.

결론적으로 CI/CD는 개발 프로세스를 효율적이고 안정적으로 만들어주어, 개발자는 핵심적인 코딩에 집중할 수 있게 됩니다!

---

## 2. GitHub Actions, 무엇인가요? 🤖

GitHub Actions는 GitHub 저장소에서 코드를 빌드, 테스트, 배포하는 등의 작업을 자동화할 수 있도록 도와주는 플랫폼입니다. 특정 이벤트(예: 코드 Push, Pull Request 생성)가 발생했을 때 미리 정의해둔 일련의 작업을 자동으로 실행시켜 줍니다.

몇 가지 핵심 용어를 알아볼까요?

*   **Workflow (워크플로우):** 자동화할 전체 프로세스를 정의하는 파일입니다. `.yml` 확장자를 가진 YAML 형식으로 작성됩니다.
*   **Event (이벤트):** 워크플로우를 실행시키는 특정 활동입니다. `push`, `pull_request`, `schedule`, `workflow_dispatch` 등 다양한 이벤트가 있습니다.
*   **Job (작업):** 워크플로우 내에서 실행되는 독립적인 작업 단위입니다. 여러 개의 Job이 순차적으로 또는 병렬로 실행될 수 있습니다.
*   **Step (단계):** Job 내에서 실행되는 개별적인 명령 또는 Action입니다.
*   **Action (액션):** GitHub에서 제공하거나 커뮤니티에서 만든 재사용 가능한 코드 블록입니다. `checkout`, `setup-python` 등이 대표적입니다.
*   **Runner (러너):** 워크플로우의 Job을 실행하는 서버 환경입니다. `ubuntu-latest`, `windows-latest`, `macos-latest` 등 다양한 운영체제를 지원합니다.

---

## 3. 첫 번째 GitHub Actions 워크플로우 만들어보기 ✨

이제 간단한 Python 프로젝트를 위한 CI/CD 워크플로우를 직접 만들어보겠습니다. 우리의 목표는 다음과 같습니다:
1.  코드가 `main` 브랜치에 Push되거나 Pull Request가 생성될 때 워크플로우 실행.
2.  Python 환경 설정.
3.  필요한 의존성 설치.
4.  유닛 테스트 실행.
5.  코드 린팅(스타일 검사) 실행.
6.  (가상) 배포 메시지 출력.

먼저, 테스트를 위한 간단한 Python 파일을 준비해볼까요? 여러분의 프로젝트 루트에 `main.py`와 `test_main.py`를 만들어주세요.

```python
# main.py
def greet(name):
    """주어진 이름을 사용하여 인사말을 반환합니다."""
    return f"Hello, {name}!"

def add(a, b):
    """두 숫자를 더합니다."""
    return a + b

if __name__ == "__main__":
    print(greet("GitHub Actions User"))
    print(f"1 + 2 = {add(1, 2)}")
```

```python
# test_main.py
import pytest
from main import greet, add

def test_greet():
    """greet 함수가 올바른 인사말을 반환하는지 테스트합니다."""
    assert greet("Alice") == "Hello, Alice!"
    assert greet("Bob") != "Goodbye, Bob!"

def test_add():
    """add 함수가 올바른 덧셈 결과를 반환하는지 테스트합니다."""
    assert add(1, 2) == 3
    assert add(0, 0) == 0
    assert add(-1, 1) == 0
```

그리고 `pytest`와 `flake8`을 위한 `requirements.txt` 파일도 준비합니다.
```
pytest
flake8
```

자, 이제 GitHub Actions 워크플로우 파일을 만들어봅시다.
여러분의 GitHub 저장소 루트에 `.github/workflows/` 디렉토리를 만들고, 그 안에 `python_ci.yml` 파일을 생성합니다.

```yaml
# .github/workflows/python_ci.yml
name: Python CI Workflow # 워크플로우의 이름입니다. GitHub Actions 탭에 표시됩니다.

on:
  push: # 코드가 push 될 때 워크플로우를 실행합니다.
    branches: [ main ] # 'main' 브랜치에 push 될 때만 실행합니다.
  pull_request: # pull request가 생성될 때 워크플로우를 실행합니다.
    branches: [ main ] # 'main' 브랜치로 향하는 pull request에 대해 실행합니다.

jobs: # 워크플로우 내에서 실행될 작업들을 정의합니다.
  build: # 'build'라는 이름의 작업 (Job)을 정의합니다.
    runs-on: ubuntu-latest # 이 Job을 실행할 러너의 운영체제입니다. 여기서는 최신 Ubuntu를 사용합니다.

    steps: # 'build' Job 내에서 실행될 단계(Step)들을 정의합니다.
    - name: 코드 체크아웃 # 1. GitHub 저장소의 코드를 러너로 가져옵니다.
      uses: actions/checkout@v4 # GitHub에서 제공하는 'checkout' 액션을 사용합니다.

    - name: Python 환경 설정 # 2. 러너에 Python 환경을 설정합니다.
      uses: actions/setup-python@v5 # GitHub에서 제공하는 'setup-python' 액션을 사용합니다.
      with:
        python-version: '3.x' # Python 3.x 버전 중 최신 버전을 사용합니다.

    - name: 의존성 설치 # 3. 프로젝트에 필요한 패키지들을 설치합니다.
      run: | # 여러 명령어를 실행할 때는 '|'를 사용합니다.
        python -m pip install --upgrade pip
        pip install -r requirements.txt # requirements.txt에 명시된 패키지 설치

    - name: 테스트 실행 # 4. Pytest를 사용하여 유닛 테스트를 실행합니다.
      run: |
        pytest # pytest 명령어를 실행합니다.

    - name: 린팅 실행 (코드 스타일 검사) # 5. Flake8을 사용하여 코드 스타일을 검사합니다.
      run: |
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics # 오류 검사
        flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics # 경고 검사

    - name: 배포 시뮬레이션 (CD 파트) # 6. (실제 배포 대신) 배포가 완료되었다는 메시지를 출력합니다.
      run: echo "축하합니다! 스테이징 환경 배포가 성공적으로 시뮬레이션되었습니다. 🎉"
```

### 코드 설명 덧붙이기

*   **`name`**: 이 워크플로우의 이름을 지정합니다. GitHub Actions 탭에서 쉽게 알아볼 수 있게 해줍니다.
*   **`on`**: 워크플로우가 언제 실행될지 정의합니다. 여기서는 `main` 브랜치에 `push`가 발생하거나, `main` 브랜치로 `pull_request`가 생성될 때 실행되도록 설정했습니다.
*   **`jobs`**: 실제 수행할 작업들의 집합입니다.
    *   **`build`**: `jobs` 아래에 정의된 첫 번째 작업의 이름입니다. 여러분은 여러 개의 Job을 정의할 수 있습니다.
    *   **`runs-on: ubuntu-latest`**: 이 Job을 실행할 가상 서버의 운영체제를 지정합니다. GitHub Actions는 다양한 OS 환경을 제공합니다.
    *   **`steps`**: `build` Job 안에서 순서대로 실행될 개별적인 명령들입니다.
        *   **`uses: actions/checkout@v4`**: GitHub에서 제공하는 액션으로, 저장소의 코드를 러너로 가져옵니다. 모든 워크플로우의 시작점에 거의 필수적입니다.
        *   **`uses: actions/setup-python@v5`**: Python 프로젝트를 위한 환경을 설정해주는 액션입니다. `with` 키워드를 통해 세부 설정(예: `python-version`)을 전달할 수 있습니다.
        *   **`run: ...`**: 러너의 셸에서 직접 명령어를 실행합니다. 여기서는 `pip install`로 의존성을 설치하고, `pytest`로 테스트를 실행하며, `flake8`로 린팅을 수행합니다.

---

## 4. 워크플로우 실행 및 확인 ✅

`python_ci.yml` 파일을 저장소에 커밋하고, `main` 브랜치로 푸시하거나 `main` 브랜치로 향하는 Pull Request를 생성해보세요.

1.  **코드 푸시:**
    ```bash
    git add .
    git commit -m "feat: Add GitHub Actions CI workflow"
    git push origin main
    ```

2.  **GitHub Actions 탭 확인:**
    GitHub 저장소로 이동하여 상단의 `Actions` 탭을 클릭합니다.
    방금 푸시한 커밋 메시지와 함께 `Python CI Workflow`가 실행 중이거나 완료된 것을 볼 수 있을 거예요.

    ![GitHub Actions Tab](https://docs.github.com/assets/images/help/actions/main-actions-tab.png)
    *(이미지는 GitHub 공식 문서에서 가져온 예시이며, 실제 화면은 다를 수 있습니다.)*

3.  **로그 확인:**
    실행된 워크플로우를 클릭하면, `build` Job과 그 안의 각 Step들이 어떻게 실행되었는지 상세한 로그를 확인할 수 있습니다. 모든 Step이 초록색 체크 표시(✔️)와 함께 성공적으로 완료되었다면, 여러분의 첫 번째 CI/CD 워크플로우가 성공적으로 작동한 것입니다!

    만약 빨간색 X 표시(❌)가 보인다면, 해당 Step의 로그를 확인하여 어떤 오류가 발생했는지 파악하고 수정할 수 있습니다. 예를 들어, 테스트가 실패했거나, 린팅 규칙에 위배되는 코드가 있었을 수 있습니다.

---

## 마무리하며 💡

오늘은 GitHub Actions를 활용한 CI/CD의 기본 개념부터 실제 워크플로우를 만들어보는 과정까지 함께했습니다. 이제 여러분은 코드를 푸시하거나 Pull Request를 생성할 때마다 자동으로 테스트되고 검증되는 마법 같은 경험을 하게 될 거예요!

GitHub Actions는 오늘 살펴본 것보다 훨씬 더 많은 기능을 제공합니다.
*   다양한 이벤트 트리거 (스케줄, 수동 실행 등)
*   환경 변수 관리 및 Secrets 사용 (민감한 정보 보호)
*   외부 서비스와의 연동 (Slack 알림, 클라우드 배포 등)
*   Artifacts (빌드 결과물 저장 및 다운로드)

이 글이 여러분의 개발 생산성을 한 단계 끌어올리는 데 도움이 되었기를 바랍니다. 이제 이 강력한 도구를 바탕으로 여러분만의 멋진 자동화 파이프라인을 구축해나가시길 응원합니다! 다음에는 더 심화된 내용으로 찾아오겠습니다. 해피 코딩! ✨

---