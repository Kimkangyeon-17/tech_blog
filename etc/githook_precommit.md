# Git hooks과 pre-commit이란?

## Git hooks란?

`Git hooks`란 Git에서 이벤트가 발생할 때 자동으로 특정 스크립트를 실행하는 시스템입니다. 클라이언트 훅과 서버 훅으로 나눌 수 있으며, 클라이언트 훅의 경우 commit이나 merge를 할 때 실행되고, 서버 훅의 경우는 push할 때 서버에서 실행됩니다. 

Git 디렉토리 밑에 `hooks` 디렉토리에 스크립트를 작성하여 사용하며 디렉토리는 `.git/hooks`입니다. Git hooks는 일반적으로 commit 정책 장려, 레포지토리 상태에 따른 프로젝트 환경 변경, 지속적 통합 워크플로우 구현이 포함됩니다. 하지만 스크립트는 사용자 지정이 무한대로 가능하기 때문에 Git hooks를 사용하여 개발 워크플로우를 거의 모두 자동화하거나 최적화가 가능합니다.

### Git Hook의 주요 종류

**클라이언트 훅 (Client-side Hooks)**
- `pre-commit`: 커밋하기 직전에 실행 (코드 스타일 검사, 린팅 등)
- `pre-push`: 푸시하기 직전에 실행 (테스트 실행, 빌드 확인 등)
- `commit-msg`: 커밋 메시지 검증
- `post-commit`: 커밋 완료 후 실행 (알림, 로그 등)

**서버 훅 (Server-side Hooks)**  
- `pre-receive`: 서버가 푸시를 받기 전에 실행
- `post-receive`: 푸시가 완료된 후 실행 (배포, 알림 등)

### Git Hook의 한계점

하지만 혼자가 아닌 여럿이 프로젝트에 참여하여 개발할 때는 다음과 같은 문제가 발생합니다:

- Hook 스크립트의 공유가 어려움
- 모든 팀원이 같은 버전의 hook을 사용한다는 보장이 없음
- 새로운 팀원이 합류할 때마다 hook 설정을 다시 해야 함

그렇기에 팀 프로젝트에서는 `pre-commit`을 통해 훅을 효율적으로 관리합니다.

## pre-commit

### pre-commit이란?

pre-commit이란 이름에서도 쉽게 알 수 있듯 `git commit`을 수행하기 이전에 자동으로 특정 작업을 수행하도록 하는 기능입니다. 프로젝트 내 설정 파일을 통해 hooks의 버전을 관리할 수 있으며 로컬에도 손쉽게 설치가 가능합니다.

### pre-commit의 장점

1. **버전 관리**: `.pre-commit-config.yaml` 파일로 훅 버전을 Git으로 관리
2. **쉬운 공유**: 팀원 모두가 동일한 훅 설정을 사용 가능
3. **자동 설치**: `pre-commit install` 한 번으로 모든 훅 설치
4. **다양한 언어 지원**: Python, JavaScript, Go 등 다양한 언어의 훅 사용 가능
5. **커뮤니티 지원**: 이미 검증된 수많은 훅들을 쉽게 사용 가능

### Git Hook 파일 확인하기

Git 저장소에서 훅 파일들을 확인해보겠습니다:

```bash
# Git 저장소가 아닌 경우 먼저 초기화
git init

# .git/hooks 폴더 확인
ls -la .git/hooks/
```

### pre-commit 설치하기

```bash
$ pip install pre-commit # pip를 통한 설치
$ uv install pre-commit # uv를 통한 설치
```

위와 같이 pip 혹은 uv를 통해서 쉽게 설치가 가능합니다.

### 설정 파일 생성하기

```bash
$ pre-commit sample-config > .pre-commit-config.yaml
```

위와 같은 명령어를 통해 프로젝트의 루트 경로에 `.pre-commit-config.yaml`이라는 파일을 생성합니다. 이때 `sample-config`라는 템플릿으로 내부에 설정 파일을 만들 수 있습니다.

설정 파일에는 다음과 같은 내용이 작성되어 있습니다:

```yaml
# See https://pre-commit.com for more information
# See https://pre-commit.com/hooks.html for more hooks
repos:                                    # 훅 저장소 목록
-   repo: https://github.com/pre-commit/pre-commit-hooks  # 훅 저장소 URL
    rev: v3.2.0                          # 사용할 버전 (태그, 브랜치, 커밋)
    hooks:                               # 사용할 훅 목록
    -   id: trailing-whitespace          # 줄 끝 공백 제거
    -   id: end-of-file-fixer           # 파일 끝 줄바꿈 추가
    -   id: check-yaml                  # YAML 파일 문법 검사
    -   id: check-added-large-files     # 큰 파일 추가 방지
```

### pre-commit 설정 완료하기

설정 파일을 생성한 후, 실제로 Git에 훅을 설치해야 합니다:

```bash
$ pre-commit install # 또는 uv run pre-commit install
pre-commit installed at .git/hooks/pre-commit
```

이제 commit을 할 때마다 설정한 훅들이 자동으로 실행됩니다.

### 실제 동작 확인해보기

간단한 테스트를 해보겠습니다:

```bash
# 끝에 공백이 있는 파일 생성
$ echo "test " > test.txt

# 파일 추가 및 첫 번째 커밋 시도
$ git add test.txt
$ git commit -m "테스트 커밋"

Trim Trailing Whitespace.................Failed
- hook id: trailing-whitespace
- exit code: 1
- files were modified by this hook

Fixing test.txt

Fix End of Files.........................Passed
Check Yaml...........................(no files to check)Skipped
Check for added large files..............Passed
```

**첫 번째 커밋이 실패한 이유:**
- `trailing-whitespace` 훅이 파일 끝의 불필요한 공백을 발견
- 자동으로 공백을 제거하고 파일을 수정
- 파일이 수정되었으므로 다시 add 후 커밋해야 함

**uv 환경에서 사용하는 경우:**
```bash
$ uv run git commit -m "테스트 커밋"
# 또는
$ uv run pre-commit run
```

**수정된 파일로 다시 커밋:**
```bash
# 수정된 파일 다시 추가
$ git add test.txt

# 두 번째 커밋 시도
$ git commit -m "테스트 커밋"

Trim Trailing Whitespace.................Passed
Fix End of Files.........................Passed
Check Yaml...........................(no files to check)Skipped
Check for added large files..............Passed
[master d822556] 테스트 커밋
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt
```

이제 모든 훅이 통과하여 커밋이 성공했습니다!

### 유용한 추가 명령어들

```bash
# 커밋 없이 모든 파일에 pre-commit 적용
$ pre-commit run --all-files  # 또는 uv run pre-commit run --all-files

# 특정 훅만 실행
$ pre-commit run trailing-whitespace

# pre-commit 훅 업데이트
$ pre-commit autoupdate
```

## 마무리

이번 글에서는 Git Hook의 기본 개념과 pre-commit 도구의 기초적인 사용법을 알아보았습니다.

**핵심 포인트:**
- Git Hook은 Git 이벤트 발생 시 자동으로 실행되는 스크립트
- 팀 프로젝트에서는 훅 공유와 버전 관리가 어려운 문제
- pre-commit으로 이런 문제들을 쉽게 해결 가능
- `.pre-commit-config.yaml`로 설정을 Git으로 관리
- 코드 품질을 자동으로 유지하여 팀의 생산성 향상

**다음 글에서는:**
- Django 프로젝트에 특화된 pre-commit 설정
- Ruff, Black, isort 등 Python 도구들과의 연동
- 실제 프로젝트 적용 과정과 팁들

을 다룰 예정이니 많은 관심 부탁드립니다!