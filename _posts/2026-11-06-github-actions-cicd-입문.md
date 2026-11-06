---
layout: post
title: "GitHub Actions CI/CD 입문: 반복적인 배포, 이젠 안녕! 👋"
date: 2026-11-06
categories: [개발, GitHub, CI/CD, 자동화]
tags: [GitHubActions, CI, CD, 워크플로우, 초보자, 튜토리얼, 개발블로그]
---

# GitHub Actions CI/CD 입문: 반복적인 배포, 이젠 안녕! 👋

안녕하세요! 개발 블로그에서 인사드립니다. 개발자로 일하다 보면 매일 똑같이 반복되는 작업들, 혹시 지루하거나 실수로 인해 밤샘 작업을 해본 경험이 있으신가요? 코드를 짜는 것도 중요하지만, 그 코드를 테스트하고, 빌드하고, 배포하는 과정 또한 개발의 중요한 부분입니다. 하지만 이 모든 과정을 수동으로 진행한다면 시간 낭비는 물론, 휴먼 에러의 가능성까지 커지게 됩니다.

이런 고민을 해결해 줄 마법 같은 도구가 바로 **CI/CD**이며, GitHub를 사용하고 있다면 **GitHub Actions**는 강력한 CI/CD 파트너가 되어줄 수 있습니다. 오늘은 GitHub Actions를 활용한 CI/CD의 기본 개념부터 간단한 워크플로우를 직접 만들어보는 시간까지, 초보자 눈높이에서 친절하게 안내해 드리겠습니다.

---

## 1. CI/CD, 왜 필요할까요? 🚀

CI/CD는 Continuous Integration(지속적인 통합)과 Continuous Delivery(지속적인 제공) 또는 Continuous Deployment(지속적인 배포)의 약자입니다. 이 개념들은 소프트웨어 개발 생명주기(SDLC)를 자동화하여 개발 생산성을 높이고, 더 빠르고 안정적으로 제품을 출시하는 것을 목표로 합니다.

### 1.1. CI (Continuous Integration): 지속적인 통합

CI는 개발자들이 작성한 코드를 중앙 코드 저장소에 자주 병합(merge)하는 개발 방식입니다. 코드를 병합할 때마다 자동으로 빌드 및 테스트를 수행하여 잠재적인 문제를 조기에 발견하고 해결할 수 있도록 돕습니다.

**주요 이점:**
*   **문제 조기 발견:** 통합으로 인한 충돌이나 버그를 빠르게 찾아낼 수 있습니다.
*   **코드 품질 향상:** 자동화된 테스트를 통해 코드의 안정성을 확보합니다.
*   **팀 협업 강화:** 개발자 간의 통합 주기를 단축하여 협업 효율을 높입니다.

### 1.2. CD (Continuous Delivery/Deployment): 지속적인 제공/배포

CI 단계를 성공적으로 거친 코드를 자동으로 사용자에게 배포할 수 있는 상태로 만들거나(Delivery), 실제로 배포까지 진행하는(Deployment) 과정입니다.

*   **Continuous Delivery (지속적인 제공):** 모든 변경 사항이 프로덕션 환경에 배포될 준비를 마친 상태로 유지됩니다. 수동 승인 단계를 거쳐 언제든 배포할 수 있습니다.
*   **Continuous Deployment (지속적인 배포):** 모든 테스트를 통과한 변경 사항이 사람의 개입 없이 자동으로 프로덕션 환경에 배포됩니다.

**주요 이점:**
*   **빠른 릴리즈:** 새로운 기능을 더 빠르게 고객에게 제공할 수 있습니다.
*   **안정성 증가:** 배포 과정의 자동화로 인해 수동 작업 시 발생할 수 있는 오류를 줄입니다.
*   **피드백 주기 단축:** 고객의 피드백을 빠르게 반영하여 제품을 개선할 수 있습니다.

---

## 2. GitHub Actions, 너는 누구니? 🧑‍💻

GitHub Actions는 GitHub에서 제공하는 CI/CD 도구입니다. 여러분의 GitHub 리포지토리에서 코드 푸시, 풀 리퀘스트 생성 등 특정 이벤트가 발생했을 때, 정의된 워크플로우(Workflow)를 자동으로 실행시켜 빌드, 테스트, 배포 등의 작업을 수행할 수 있도록 해줍니다.

**GitHub Actions의 특징:**
*   **GitHub 연동:** GitHub 리포지토리와 완벽하게 통합되어 추가 도구 설치 없이 바로 사용할 수 있습니다.
*   **이벤트 기반:** `push`, `pull_request`, `issue_comment` 등 다양한 GitHub 이벤트에 반응하여 워크플로우를 실행합니다.
*   **YAML 기반:** 복잡한 스크립트 대신 YAML 파일을 사용하여 워크플로우를 쉽게 정의할 수 있습니다.
*   **마켓플레이스:** 수많은 커뮤니티 개발자들이 만든 재사용 가능한 `Action`들을 활용하여 워크플로우를 빠르게 구성할 수 있습니다.

---

## 3. GitHub Actions의 기본 개념 콕콕! 💡

GitHub Actions를 이해하기 위해 몇 가지 핵심 용어를 알아봅시다.

*   **Workflow (워크플로우):**
    *   자동화될 프로세스 전체를 의미합니다. `.github/workflows` 디렉토리 안에 YAML 파일(`.yml` 또는 `.yaml`)로 정의됩니다.
    *   예를 들어, "코드 푸시 시 테스트하고 배포하는 과정" 전체가 하나의 워크플로우가 될 수 있습니다.

*   **Event (이벤트):**
    *   워크플로우를 트리거(실행)하는 특정 활동입니다.
    *   `push` (코드 푸시), `pull_request` (풀 리퀘스트 생성), `schedule` (정해진 시간에 실행), `workflow_dispatch` (수동 실행) 등 다양한 이벤트가 있습니다.

*   **Job (작업):**
    *   워크플로우 내에서 실행되는 독립적인 단위입니다. 각 Job은 자체 가상 환경(Runner)에서 실행됩니다.
    *   여러 Job은 병렬로 실행되거나, 특정 Job이 완료된 후 다음 Job이 실행되도록 순서를 지정할 수 있습니다.
    *   예: `build_job`, `test_job`, `deploy_job`.

*   **Step (단계):**
    *   Job 내에서 실행되는 개별적인 명령 또는 `Action`입니다.
    *   예: "코드 체크아웃", "의존성 설치", "테스트 실행", "결과 출력" 등이 각각의 Step이 될 수 있습니다.

*   **Action (액션):**
    *   특정 기능을 수행하는 재사용 가능한 코드 블록입니다. GitHub Marketplace에서 제공되거나 직접 만들 수 있습니다.
    *   예: `actions/checkout@v3` (리포지토리 코드 가져오기), `actions/setup-node@v3` (Node.js 환경 설정).

*   **Runner (러너):**
    *   워크플로우를 실제로 실행하는 서버입니다. GitHub에서 호스팅하는 가상 머신(`ubuntu-latest`, `windows-latest`, `macos-latest` 등)을 사용하거나, 직접 설정한 사설 러너(Self-hosted runner)를 사용할 수 있습니다.

---

## 4. 첫 번째 GitHub Actions 워크플로우 만들기 (코드 예제) 🏗️

이제 개념을 이해했으니, 실제로 간단한 워크플로우를 만들어 봅시다. 목표는 다음과 같습니다.

**시나리오:** 여러분의 GitHub 리포지토리 `main` 브랜치에 코드를 `push` 할 때마다, GitHub Actions가 자동으로 Python 환경을 설정하고 "Hello, GitHub Actions!"라는 메시지를 출력하는 워크플로우를 만들어보겠습니다.

1.  **리포지토리 생성 및 설정:**
    *   GitHub에 새로운 리포지토리를 만들거나 기존 리포지토리를 사용합니다.
    *   프로젝트의 루트 디렉토리에 `.github/workflows` 라는 디렉토리를 생성합니다.

2.  **워크플로우 파일 생성:**
    *   ` .github/workflows` 디렉토리 안에 `first-ci.yml` 이라는 이름으로 파일을 생성합니다. (이름은 자유롭게 지정할 수 있습니다.)

3.  **`first-ci.yml` 파일 내용 작성:**

```yaml
# .github/workflows/first-ci.yml
name: 나의 첫 CI 워크플로우 # 이 워크플로우의 이름을 지정합니다. GitHub Actions 탭에서 이 이름이 보입니다.

on:
  push:
    branches:
      - main # 'main' 브랜치에 코드를 푸시할 때 이 워크플로우가 실행되도록 설정합니다.
      # 여러 브랜치를 지정할 수도 있고, 'pull_request'와 같은 다른 이벤트로 변경할 수도 있습니다.

jobs: # 이 워크플로우 내에서 실행될 작업들을 정의합니다.
  build: # 'build'라는 이름의 Job을 정의합니다. Job 이름은 원하는 대로 지정할 수 있습니다.
    runs-on: ubuntu-latest # 이 Job이 실행될 환경을 지정합니다. 여기서는 최신 Ubuntu 가상 환경을 사용합니다.
    steps: # 'build' Job 내에서 순차적으로 실행될 단계(Step)들을 정의합니다.
      - name: 🚀 코드 체크아웃 # 첫 번째 Step의 이름입니다.
        uses: actions/checkout@v3 # GitHub 리포지토리의 코드를 Runner(가상 환경)로 가져오는 Action을 사용합니다.
                                # @v3는 Action의 버전을 의미합니다.

      - name: 🐍 Python 환경 설정 # 두 번째 Step의 이름입니다.
        uses: actions/setup-python@v4 # Python 환경을 설정하는 Action을 사용합니다.
        with: # 이 Action에 전달할 인자(parameters)를 정의합니다.
          python-version: '3.x' # 3.x 버전의 Python을 설치하도록 지정합니다.

      - name: ✨ 'Hello, GitHub Actions!' 출력 # 세 번째 Step의 이름입니다.
        run: echo "Hello, GitHub Actions! CI/CD의 세계에 오신 것을 환영합니다!" # Runner에서 실행할 쉘 명령어를 정의합니다.
```

**코드 설명:**

*   `name`: GitHub Actions 탭에서 이 워크플로우를 식별할 이름입니다.
*   `on: push: branches: - main`: `main` 브랜치에 새로운 코드가 `push`될 때 이 워크플로우가 트리거되도록 설정합니다.
*   `jobs: build:`: `build`라는 이름의 단일 작업을 정의합니다.
*   `runs-on: ubuntu-latest`: 이 `build` 작업이 최신 Ubuntu 운영체제 환경에서 실행될 것임을 지정합니다.
*   `steps:`: `build` 작업 내에서 수행될 일련의 단계들을 정의합니다.
    *   `uses: actions/checkout@v3`: GitHub 리포지토리의 코드를 워크플로우가 실행될 가상 환경으로 가져오는 필수적인 `Action`입니다. 이 `Action`이 없으면 코드를 찾을 수 없습니다.
    *   `uses: actions/setup-python@v4`: Python 개발 환경을 설정하는 `Action`입니다. `with` 키워드를 통해 `python-version: '3.x'`와 같이 필요한 Python 버전을 지정할 수 있습니다.
    *   `run: echo "..."`: 가상 환경의 터미널에서 직접 쉘 명령어를 실행하는 부분입니다. 여기서는 간단히 메시지를 출력합니다.

4.  **GitHub에 푸시:**
    *   생성한 `first-ci.yml` 파일을 커밋하고 `main` 브랜치로 푸시합니다.

    ```bash
    git add .github/workflows/first-ci.yml
    git commit -m "feat: Add first GitHub Actions CI workflow"
    git push origin main
    ```

5.  **결과 확인:**
    *   GitHub 리포지토리로 이동하여 상단의 `Actions` 탭을 클릭합니다.
    *   방금 푸시한 커밋 메시지와 함께 `나의 첫 CI 워크플로우`가 실행 중이거나 완료된 것을 볼 수 있습니다.
    *   워크플로우를 클릭한 후, `build` Job을 클릭하면 각 `Step`의 실행 로그를 자세히 확인할 수 있습니다. `Hello, GitHub Actions!` 메시지가 성공적으로 출력된 것을 확인해 보세요!

---

## 마무리하며 🌟

오늘은 GitHub Actions를 활용한 CI/CD의 기본 개념부터 아주 간단한 워크플로우를 직접 만들어보는 과정까지 함께 해보았습니다. 반복적인 수동 작업을 자동화하여 개발 프로세스의 효율성을 높이고, 더 빠르고 안정적으로 소프트웨어를 배포하는 CI/CD의 강력함을 직접 경험하셨기를 바랍니다.

이것은 GitHub Actions의 빙산의 일각일 뿐입니다. 앞으로 테스트 코드 실행, Docker 이미지 빌드, 클라우드 환경으로의 자동 배포 등 훨씬 더 복잡하고 유용한 워크플로우를 구축해 나갈 수 있습니다. 오늘 배운 내용을 바탕으로 여러분의 프로젝트에 CI/CD를 적용해 보며 개발 생산성을 한 단계 업그레이드해보세요!

다음 글에서는 더 실용적인 GitHub Actions 활용 팁이나 고급 기능으로 찾아오겠습니다. 읽어주셔서 감사합니다!