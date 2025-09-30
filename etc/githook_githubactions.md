# Git Hooks와 GitHub Actions의 차이점

코드 품질 관리는 현대 소프트웨어 개발에서 필수 요소입니다. 이전에 다룬 pre-commit과 ruff를 통해 로컬에서의 코드 품질 관리를 살펴봤다면, 이번에는 더 넓은 관점에서 **Git Hooks**와 **GitHub Actions**라는 두 가지 자동화 도구를 비교해보겠습니다.

## Git Hooks란?

### 정의와 개념

Git Hooks는 Git에서 특정 이벤트(commit, merge, push 등)이 발생할 때 자동으로 실행되는 스크립트를 말합니다. Git의 내부 동작 과정에서 "갈고리(hook)"처럼 끼어들어 추가 작업을 수행합니다.

### 동작 원리

```
# Git 명령어 실행 과정
git add . → git commit → [pre-commit hook 실행] → 커밋 생성 → [post-commit hook 실행]
```

Git 명령어 실행 전/후에 트리거되어 사용자가 정의한 스크립트를 실행합니다.

### 파일 위치

```
.git/hooks/
├── pre-commit.sample
├── pre-push.sample
├── post-commit.sample
└── ... (기타 샘플 파일들)
```

### 주요 Hook 종류

**클라이언트 사이드 훅 (로컬에서 실행)**

-   `pre-commit`: 커밋 메시지 작성 전 실행
-   `prepare-commit-msg`: 커밋 메시지 편집기 실행 전
-   `commit-msg`: 커밋 메시지 검증
-   `post-commit`: 커밋 완료 후 실행
-   `pre-push`: 원격 저장소로 push 전 실행

**서버 사이드 훅 (원격 저장소에서 실행)**

-   `pre-receive`: push 받기 전 실행
-   `post-receive`: push 완료 후 실행
-   `update`: 각 브랜치별로 실행

### 특징

-   **로컬환경에서만 실행**: 개발자 개인의 환경에서 동작
-   **빠른 실행 속도**: 네트워크 지연 없이 즉시 실행
-   **개인화 가능**: 각 개발자가 자신만의 규칙 설정 가능

### 실제 pre-commit Hook 예시

```
#!/bin/sh
# .git/hooks/pre-commit

echo "🔍 Running pre-commit checks..."

# Python 파일이 있는지 확인
if git diff --cached --name-only | grep -q "\.py$"; then
    echo "📝 Python files detected, running Ruff..."

    # Ruff 실행
    ruff check .
    if [ $? -ne 0 ]; then
        echo "❌ Ruff check failed. Please fix the issues before committing."
        exit 1
    fi

    # Ruff 자동 수정 적용
    ruff check . --fix

    echo "✅ Ruff check passed!"
fi

echo "🎉 All pre-commit checks passed!"
exit 0
```

## GitHub Actions란?

### 정의와 개념

GitHub에서 제공하는 CI/CD(Continuous Integration/Continuous Deployment) 자동화 플랫폼입니다. 코드 변경사항을 자동으로 빌드, 테스트, 배포할 수 있게 해줍니다.

### 동작 원리

GitHub 저장소 이벤트(push, pull request, 스케줄 등)를 기반으로 미리 정의된 워크플로우를 실행합니다.

### 파일 위치

```
.github/workflows/
├── quality-check.yml
├── deploy.yml
└── test.yml
```

### 특징

-   **클라우드에서 실행**: GitHub의 서버에서 동작
-   **팀 협업 최적화**: 저장소를 공유하는 모든 팀원에게 동일하게 적용
-   **다양한 환경 지원**: Ubuntu, Windows, macOS 환경 선택 가능
-   **복잡한 워크플로우**: 여러 단계의 복합적인 작업 수행 가능

## GitHub Actions 핵심 구성 요소

### 기본 구조 이해하기

```
# 이 워크플로우의 이름
name: 🚀 Comprehensive Code Quality Check

# 언제 실행할지 정하기
on:
  push:
    branches: [ main, develop ]  # main, develop 브랜치에 코드 올릴 때
  pull_request:
    branches: [ main ]           # main 브랜치로 PR 만들 때
  schedule:
    - cron: '0 0 * * 1'         # 매주 월요일 자정에 자동 실행

# 모든 작업에서 쓸 공통 변수
env:
  PYTHON_VERSION: '3.11'
  NODE_VERSION: '18'
```

### 작업(Jobs) 정의

```
jobs:
  code-quality:                    # 첫 번째 작업 이름
    name: 📋 Code Quality Analysis  # 화면에 보여질 이름
    runs-on: ubuntu-latest          # 어떤 컴퓨터에서 실행할지
```

### 여러 Python 버전에서 테스트하기

```
    strategy:
      matrix:
        python-version: ['3.9', '3.10', '3.11']
```

**설명**: Python 3.9, 3.10, 3.11 세 버전에서 모두 테스트하겠다는 뜻입니다.

### 단계별 실행 과정 (Steps)

#### 1\. 코드 가져오기

```
steps:
  - name: 📥 Checkout code
    uses: actions/checkout@v4
```

**의미**: GitHub에서 내 코드를 컴퓨터로 다운로드

#### 2\. Python 설치하기

```
  - name: 🐍 Set up Python ${{ matrix.python-version }}
    uses: actions/setup-python@v4
    with:
      python-version: ${{ matrix.python-version }}
      cache: 'pip'
```

**의미**: Python을 설치하고, 이전에 설치한 패키지들은 기억해서 빠르게 설치

#### 3\. 필요한 도구들 설치

```
  - name: 📦 Install dependencies
    run: |
      pip install --upgrade pip
      pip install ruff pytest coverage
      if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
```

**의미**:

-   pip 최신 버전으로 업데이트
-   ruff(코드 검사), pytest(테스트), coverage(테스트 커버리지) 설치
-   requirements.txt 파일이 있으면 거기 적힌 패키지들도 설치

#### 4\. 코드 품질 검사

```
  - name: 🔍 Run Ruff Linting
    run: ruff check . --output-format=github

  - name: 🎨 Check Code Formatting
    run: ruff format . --check
```

**의미**:

-   첫 번째: 코드에 문제 없는지 검사
-   두 번째: 코드 formatting이 올바른지 확인

#### 5\. 테스트 실행하고 결과 업로드

```
  - name: 🧪 Run Tests
    run: |
      coverage run -m pytest
      coverage xml

  - name: 📊 Upload Coverage to Codecov
    uses: codecov/codecov-action@v3
    with:
      file: ./coverage.xml
```

**의미**:

-   테스트 실행하면서 어느 부분이 테스트되었는지 확인
-   그 결과를 Codecov라는 서비스에 보내서 시각화

### 보안 검사 작업

```
  security-scan:
    name: 🔒 Security Scan
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: 🛡️ Run Bandit Security Scan
        run: |
          pip install bandit
          bandit -r . -f json -o bandit-report.json
```

**의미**: 별도로 보안 취약점이 있는지 검사하는 작업

## 초보자를 위한 간단한 예시

실제로는 이렇게 간단하게 시작할 수 있습니다.

```
# .github/workflows/simple-check.yml
name: 코드 검사

# main 브랜치에 코드 올릴 때만 실행
on:
  push:
    branches: [ main ]

jobs:
  check-code:
    runs-on: ubuntu-latest

    steps:
      # 1. 코드 다운로드
      - name: 코드 가져오기
        uses: actions/checkout@v4

      # 2. Python 설치
      - name: Python 설치
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      # 3. ruff 설치
      - name: ruff 설치
        run: pip install ruff

      # 4. 코드 검사
      - name: 코드 검사 실행
        run: ruff check .
```

## Git Hooks와 GitHub Actions 주요 차이점

| 특성 | Git Hooks | GitHub Actions |
| --- | --- | --- |
| **실행 위치** | 로컬 머신 | GitHub 클라우드 서버 |
| **트리거 시점** | Git 명령어 실행 시 | GitHub 이벤트 발생 시 |
| **비용** | 무료 (로컬 리소스 사용) | 월 2,000분 무료, 이후 유료 |
| **실행 속도** | 매우 빠름 (로컬) | 네트워크 지연 + 환경 설정 시간 |
| **복잡성** | 단순한 스크립트 | 복잡한 워크플로우 구성 |
| **공유/협업** | 개인별 설정 필요 | 저장소 공유 자동 적용 |
| **환경 일관성** | 개발자마다 다를 수 있음 | 표준화된 클라우드 환경 |
| **실행 환경** | 개발자 로컬 환경 | Ubuntu, Windows, macOS 선택 |
| **실패 시 대응** | 로컬에서 즉시 수정 | PR 블록, 재실행 필요 |

## 사용 시기와 전략

### Git Hooks를 사용해야 하는 경우

-   **빠른 피드백이 필요한 기본 검사**: 문법 오류, 포맷팅
-   **개인 워크플로우 최적화**: 커밋 메시지 형식, 개인 규칙
-   **네트워크가 불안정한 환경**: 오프라인에서도 동작
-   **민감한 정보 처리**: 로컬에서만 접근 가능한 자원

```
# 실제 활용 예시: Django 개발에서 자주 하는 실수 방지
#!/bin/sh
# .git/hooks/pre-commit

# Django migrations 파일이 추가되었는지 확인
if git diff --cached --name-only | grep -q "migrations/.*\.py$"; then
    echo "⚠️  Migration file detected. Did you run 'python manage.py makemigrations'?"
    echo "Press Enter to continue or Ctrl+C to abort"
    read
fi

# SECRET_KEY가 커밋되는지 확인
if git diff --cached | grep -q "SECRET_KEY.*="; then
    echo "❌ SECRET_KEY found in staged changes!"
    exit 1
fi
```

### GitHub Actions를 사용해야 하는 경우

-   **팀 협업과 코드 리뷰**: 모든 PR에 대한 표준화된 검사
-   **다양한 환경 테스트**: 여러 Python 버전, OS에서 테스트
-   **배포 자동화**: 테스트 통과 후 자동 배포
-   **복잡한 파이프라인**: 빌드 → 테스트 → 보안 스캔 → 배포

## 두 방식의 조합 전략 (Best Practice)

### 계층적 품질 관리 시스템

```
로컬 (Git Hooks + pre-commit) → GitHub (GitHub Actions)
     ↓                              ↓
1차 필터링: 기본 품질 검사     →    2차 검증: 심화 검사 및 배포
```

### 실전 조합 예시

**로컬 (pre-commit + Git Hooks)**

-   Ruff 린팅 및 포맷팅
-   기본 문법 검사
-   커밋 메시지 형식 검증

**GitHub Actions**

-   여러 Python 버전 테스트
-   보안 스캔 (bandit, safety)
-   커버리지 측정 및 리포팅
-   Docker 이미지 빌드
-   자동 배포

### pre-commit 프레임워크와의 연계

이미 작성한 pre-commit 글과 연결하여, 수동 Hook 대신 pre-commit 프레임워크 활용:

```
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.6
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
```

## 성능 최적화 팁

### GitHub Actions 최적화

```
# 캐시 활용으로 빌드 시간 단축
- name: Cache pip packages
  uses: actions/cache@v3
  with:
    path: ~/.cache/pip
    key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}

# 조건부 실행으로 불필요한 작업 방지
- name: Run expensive tests
  if: github.event_name == 'pull_request' && github.base_ref == 'main'
  run: pytest -m slow
```

### Git Hooks 최적화

```
#!/bin/sh
# 변경된 파일만 검사하여 속도 향상
CHANGED_PY_FILES=$(git diff --cached --name-only --diff-filter=ACM | grep '\.py$')

if [ -n "$CHANGED_PY_FILES" ]; then
    echo "Checking Python files: $CHANGED_PY_FILES"
    ruff check $CHANGED_PY_FILES
else
    echo "No Python files changed, skipping ruff check"
fi
```

## GitHub Actions 핵심 요약

1.  **name**: 워크플로우 이름
2.  **on**: 언제 실행할지 (push, PR 등)
3.  **jobs**: 실제로 할 일들
4.  **steps**: 순서대로 실행할 명령어들
5.  **uses**: 다른 사람이 만든 도구 사용
6.  **run**: 직접 명령어 실행

## 마무리

Git Hooks와 GitHub Actions는 각각의 장점을 가진 보완적인 도구입니다. 효과적인 코드 품질 관리를 위해서 로컬에서는 Git Hooks(pre-commit)로 빠른 1차 검증을 하고 원격에서는 GitHub Actions로 철저한 2차 검증이 가능합니다. 팀 협업에서는 GitHub Actions로 표준화를 진행하고 개인 워크플로우에서는 Git Hooks로 효율성 극대화할 수 있습니다.

이전에 다룬 pre-commit과 ruff를 기반으로, 이제 더 체계적인 코드 품질 관리 파이프라인을 구축해볼 수 있습니다.

## 참고 자료

### 공식 문서

-   [Git Hooks 공식 문서](https://git-scm.com/book/ko/v2/Git%EB%A7%9E%EC%B6%A4-Git-Hooks)
-   [GitHub Actions 공식 문서](https://docs.github.com/ko/actions)
-   [pre-commit 공식 사이트](https://pre-commit.com/)
-   [Ruff 공식 문서](https://docs.astral.sh/ruff/)