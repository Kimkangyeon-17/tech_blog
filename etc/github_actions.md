# Github actions

## Github actions란 무엇인가
### Github actions란?
GitHub Actions는 GitHub에서 제공하는 CI/CD (Continuous Integration/Continuous Deployment) 플랫폼입니다.

github actions를 사용하는 이유는 아래와 같습니다.
- 자동화: 반복 작업을 자동으로 처리
- 품질 관리: 코드 품질 자동 검사
- 팀 협업: 일관된 개발 환경 제공
- 시간 절약: 수동 작업 최소화

## 기본 개념과 용어 정리
### 1. Workflow (워크플로우)
**정의**: 자동화할 작업들의 전체 과정

```yaml
name: 📊 Daily Check (Organization)  # 워크플로우 이름
```

### 2. Job (잡)
**정의**: 워크플로우 내의 독립적인 작업 단위

```yaml
jobs:
  check-daily-progress:  # 잡 이름
    runs-on: ubuntu-latest  # 실행 환경
```

### 3. Step (스텝)
**정의**: 잡 내의 개별 작업 단계

```yaml
steps:
- name: 📥 체크아웃  # 스텝 이름
  uses: actions/checkout@v4  # 사용할 액션
```

### 4. Event (이벤트)
**정의**: 워크플로우를 실행시키는 트리거

```yaml
on:
  workflow_dispatch:  # 수동 실행
  schedule:
    - cron: '0 15 * * *'  # 매일 15시 실행
```

### 5. Runner (러너)
**정의**: 워크플로우를 실행하는 가상 머신

```yaml
runs-on: ubuntu-latest  # Ubuntu 환경에서 실행
```

## 프로젝트 실제 사용 사례

현재 제가 참여하고 있는 **알고리즘 스터디**에서 GitHub Actions를 활용한 사례를 소개하겠습니다.

### 📁 폴더 구조 규칙

우리 스터디는 체계적인 폴더 구조를 사용합니다:

```
📦 algorithm-study/
├── 📂 8월3주차/
│   ├── 📂 0815/
│   │   ├── 📂 SWEA_1234/
│   │   │   ├── 📄 SWEA_1234_kky.py
│   │   │   ├── 📄 SWEA_1234_alice.py
│   │   │   └── 📄 SWEA_1234_bob.py
│   │   └── 📂 BOJ_5678/
│   │       ├── 📄 BOJ_5678_kky.py
│   │       └── 📄 BOJ_5678_alice.py
│   ├── 📂 0816/
│   │   └── 📂 SWEA_9999/
│   │       └── 📄 SWEA_9999_kky.py
│   └── 📄 README.md
├── 📂 9월1주차/
│   ├── 📂 0901/
│   ├── 📂 0902/
│   └── 📄 README.md
└── 📂 .github/
    └── 📂 workflows/
        ├── 📄 daily_check_final.yml
        └── 📄 debug_check.yml
```

### 작업 플로우

#### 1단계: 폴더 생성
```bash
# 주차 폴더 생성 (예: 9월1주차)
mkdir "9월1주차"

# 날짜 폴더 생성 (예: 0901, 0902, ...)
mkdir "9월1주차/0901"
mkdir "9월1주차/0902"
```

#### 2단계: 문제 폴더 및 파일 생성
```bash
# 문제 폴더 생성
mkdir "9월1주차/0901/SWEA_1234"

# 본인 풀이 파일 생성
touch "9월1주차/0901/SWEA_1234/SWEA_1234_kky.py"
```

#### 3단계: Git 작업
```bash
# 개인 브랜치에서 작업 (충돌 방지)
git checkout -b feature/kky

# 파일 추가 및 커밋
git add .
git commit -m "solve: SWEA_1234_kky"
git push origin feature/kky
```

#### 4단계: PR 생성 및 병합
1. GitHub에서 Pull Request 생성
2. 코드 리뷰 후 master 브랜치에 merge
3. GitHub Actions가 자동으로 학습 진도 체크

### 🤖 GitHub Actions의 역할

매일 자정(한국시간 기준)에 자동으로:
- ✅ 각 멤버의 오늘 문제 해결 현황 체크
- 📊 일별/주차별 진도율 계산
- 📈 README.md 파일에 진도 현황 업데이트
- 📝 로그 파일 생성으로 히스토리 관리

### 💡 이 시스템의 장점

**자동화**: 매일 수동으로 진도를 확인할 필요 없음
**투명성**: 모든 멤버의 학습 현황이 한눈에 보임
**동기부여**: 시각적인 진도율로 학습 의욕 증진
**히스토리**: 날짜별 학습 기록이 자동으로 누적


## daily_check_final.yml 핵심 분석
다음은 현재 스터디에서 실제로 사용하고 있는 daily_check_final.yml 파일을 좀 더 자세히 분석해보고자 합니다.
이 워크플로우는 **10단계**로 구성된 완전 자동화 시스템입니다.

### 실행 순서와 핵심 기능

#### **1단계: 📥 체크아웃**
```yaml
uses: actions/checkout@v4
with:
  token: ${{ secrets.PERSONAL_ACCESS_TOKEN }}
  fetch-depth: 0
  ref: master
```
**역할**: GitHub 저장소의 모든 코드를 가져옴 + 특별 권한 사용

#### **2단계: 🔄 강제 동기화 (충돌 방지)**
```bash
git status                    # 현재 상태 확인
git stash push               # 임시 변경사항 저장
git clean -fd                # 추적되지 않는 파일 정리
git reset --hard origin/master  # 원격과 완전히 동일하게 맞춤
```
**역할**: 다른 사람이 동시에 작업했을 때 충돌 방지

#### **3단계: 🐍 Python 설정**
```yaml
uses: actions/setup-python@v4
with:
  python-version: '3.9'
```
**역할**: Python 3.9 환경 준비

#### **4단계: 🔧 Git 설정**
```bash
git config --local user.email "action@github.com"
git config --local user.name "GitHub Action"
```
**역할**: 나중에 커밋할 때 사용할 사용자 정보 설정

#### **5단계: 🔍 환경 및 구조 확인**
```bash
pwd                          # 현재 위치 확인
ls -la                       # 파일 목록 확인
find . -name "README.md"     # README 파일들 찾기
ls -la "8월3주차/"           # 주차 폴더 내용 확인
```
**역할**: 디버깅을 위한 환경 상태 체크

#### **6단계: 📊 일일 진행상황 체크**
```bash
python scripts/daily_check.py
```
**역할**: 오늘 업로드된 파일들을 체크하고 멤버별 현황 파악

#### **7단계: 📈 누적 일별 진행률 업데이트**
```bash
python scripts/update_cumulative_daily_progress.py
```
**역할**: 모든 날짜의 진행률을 계산해서 README에 업데이트

#### **8단계: 🔍 실행 후 상세 확인**
```bash
git status                   # 스크립트 실행 후 변경사항 확인
ls -la logs/                 # 로그 파일들 확인
head -20 "logs/daily_log_*.json"  # 로그 내용 미리보기
```
**역할**: 스크립트가 제대로 실행됐는지 검증

#### **9단계: 💾 스마트 커밋 및 푸시 (핵심!)**
```bash
if [[ -n $(git status --porcelain) ]]; then
  git add .
  git commit -m "자동 업데이트"
  
  # 최대 5번 재시도하면서 푸시
  for i in {1..5}; do
    if git push origin master; then
      break
    else
      git fetch origin master
      git rebase origin/master
      sleep 3
    fi
  done
fi
```
**역할**: 변경된 파일들을 안전하게 커밋하고 푸시 (충돌 자동 해결)

#### **10단계: 📊 결과 요약**
```bash
echo "실행 완료!"
git status                   # 최종 상태
git log --oneline -3         # 최근 커밋 3개
```
**역할**: 전체 실행 결과 요약 출력

---

## debug_check.yml 간단 분석

```yaml
name: Debug Check
on:
  workflow_dispatch:  # 수동 실행만 가능

jobs:
  debug:
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        
      - name: Check directory structure
        run: |
          pwd && ls -la
          find . -name "README.md"
          ls -la "8월3주차/" || echo "No folder"
          
      - name: Check if README exists
        run: |
          if [ -f "8월3주차/README.md" ]; then
            cat "8월3주차/README.md"
          fi
```

**용도**: 메인 워크플로우가 실패했을 때 **문제 진단용**
- 파일 구조 확인
- README 존재 여부 확인
- 권한 문제 확인

현재 저희 스터디의 `daily_check_final.yml` 파일과 `debug_check.yml` 파일은 아래 github을 통해서 확인 가능합니다.

[algorithm_study](https://github.com/about-algorithm-study/About_Algorithm_Study.git)


---

## 📝 YAML 파일 작성법

### 🎯 YAML 기본 문법

#### 1. **들여쓰기** (매우 중요!)
```yaml
# ✅ 올바른 예시
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

# ❌ 잘못된 예시 (들여쓰기 불일치)
jobs:
build:
  runs-on: ubuntu-latest
   steps:
- name: Checkout
```

#### 2. **문자열 표현**
```yaml
# 일반 문자열
name: My Workflow

# 공백이 포함된 문자열
name: "My GitHub Actions Workflow"

# 여러 줄 문자열
run: |
  echo "첫 번째 줄"
  echo "두 번째 줄"
  python script.py
```

#### 3. **리스트 표현**
```yaml
# 방법 1: 하이픈 사용
steps:
  - name: Step 1
    run: echo "Hello"
  - name: Step 2
    run: echo "World"

# 방법 2: 대괄호 사용 (한 줄)
python-version: ['3.8', '3.9', '3.10']
```

### 📋 워크플로우 파일 템플릿

```yaml
# 워크플로우 이름
name: 🚀 My First Workflow

# 실행 조건
on:
  push:  # 코드 푸시 시
    branches: [ main, develop ]
  pull_request:  # PR 생성 시
    branches: [ main ]
  schedule:  # 정기적으로
    - cron: '0 9 * * *'  # 매일 오전 9시
  workflow_dispatch:  # 수동 실행

# 실행할 작업들
jobs:
  # 첫 번째 잡
  build:
    name: 🏗️ Build and Test
    runs-on: ubuntu-latest
    
    steps:
    # 1. 코드 체크아웃
    - name: 📥 Checkout code
      uses: actions/checkout@v4
      
    # 2. Python 환경 설정
    - name: 🐍 Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.9'
        
    # 3. 의존성 설치
    - name: 📦 Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        
    # 4. 테스트 실행
    - name: 🧪 Run tests
      run: |
        python -m pytest
        
    # 5. 결과 저장
    - name: 📊 Upload results
      uses: actions/upload-artifact@v3
      if: always()
      with:
        name: test-results
        path: test-results/
```

## 🐛 문제 해결과 디버깅

### 🔍 일반적인 오류와 해결법

#### 1. **YAML 문법 오류**
```
Error: Invalid workflow file
```

**해결책:**
- 들여쓰기 확인 (스페이스 2칸 또는 4칸 일관성 유지)
- YAML 온라인 검증기 사용: https://yamlchecker.com/

#### 2. **권한 오류**
```
Error: Permission denied
```

**해결책:**
```yaml
permissions:
  contents: write
  actions: read
  checks: write
```

#### 3. **패키지 설치 실패**
```
Error: Could not find a version that satisfies the requirement
```

**pip 해결책:**
```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install --upgrade setuptools wheel
    pip install ruff pre-commit
```

**uv 해결책:**
```yaml
- name: Install dependencies
  run: |
    uv pip install --system --upgrade ruff pre-commit
```

#### 4. **스크립트 실행 오류**
```
Error: python: can't open file 'scripts/daily_check.py': [Errno 2] No such file or directory
```

**해결책:**
```yaml
- name: Check file exists
  run: |
    if [ -f "scripts/daily_check.py" ]; then
      echo "✅ 스크립트 파일 존재"
      python scripts/daily_check.py
    else
      echo "❌ 스크립트 파일이 없습니다"
      find . -name "*.py" -type f
    fi
```

### 🔧 디버깅 팁

#### 1. **로그 출력 활용**
```yaml
- name: Debug information
  run: |
    echo "🔍 현재 작업 디렉토리: $(pwd)"
    echo "📁 파일 목록:"
    ls -la
    echo "🐍 Python 버전: $(python --version)"
    echo "📦 설치된 패키지:"
    pip list | grep -E "(ruff|pre-commit)"
```

#### 2. **조건부 실행**
```yaml
- name: Run only on main branch
  if: github.ref == 'refs/heads/main'
  run: echo "메인 브랜치에서만 실행"
  
- name: Run on PR
  if: github.event_name == 'pull_request'
  run: echo "PR에서만 실행"
```

#### 3. **실패 시에도 계속 실행**
```yaml
- name: Run tests
  continue-on-error: true
  run: python -m pytest
  
- name: Always run cleanup
  if: always()
  run: echo "항상 실행되는 정리 작업"
```


## 마무리

이 가이드를 통해 GitHub Actions의 기초부터 실전 활용까지 모든 것을 배웠습니다!