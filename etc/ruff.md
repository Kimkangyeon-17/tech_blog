## Ruff란?

Python은 코드 스타일을 일관되게 유지하는 것이 매우 중요합니다. 그렇기에 이를 도와주는 다양한 linter와 formatter들이 있습니다. 이중에서 Ruff는 Rust로 작성된 Python linter이자 formatter입니다.

Ruff는 기존에 많이 사용되고 있는 Flake8, Black, isort 등 기존 도구들의 기능을 대부분 지원하기 때문에 Ruff 하나만으로도 프로젝트를 관리하는 데 유용합니다.

> An extremely fast Python linter and code formatter, written in Rust.

공식 문서에서 Ruff를 위와 같이 한 문장으로 소개하고 있습니다.

## Linter vs Formatter

Ruff에 대한 설명을 들어가기에 앞서 Linter와 Formatter에 대해 먼저 알아보겠습니다.

**Linter (린터)**

-   코드의 문법적 오류, 스타일 위반, 잠재적 버그를 찾아내는 도구입니다
-   예시: 사용하지 않는 import, 정의되지 않은 변수, 너무 긴 줄, 코딩 규칙 위반 등
-   코드를 실행하지 않고도 문제점을 미리 발견할 수 있습니다

**Formatter (포매터)**

-   코드의 형식을 자동으로 일관되게 맞춰주는 도구입니다
-   예시: 들여쓰기, 공백, 따옴표 스타일, 줄바꿈 위치 등
-   코드의 기능은 변경하지 않고 외관만 정리합니다

## Ruff의 특징

공식 문서에 따르면 Ruff의 특징은 다음과 같습니다.

⚡️ **극도로 빠른 속도**: 기존 도구들(Flake8, Black)보다 10~100배 빠름

🐍 **간편한 설치**: pip, conda, uv 등으로 쉽게 설치 가능

🛠️ **설정 파일 지원**: `pyproject.toml`, `ruff.toml` 등 다양한 설정 방식 지원

🤝 **최신 Python 호환**: Python 3.12, 3.13까지 지원

⚖️ **올인원 도구**: Flake8, isort, Black을 모두 대체 가능

📦 **효율적 캐싱**: 변경되지 않은 파일은 다시 분석하지 않음

🔧 **자동 수정**: 대부분의 오류를 자동으로 수정 가능

📏 **풍부한 규칙**: 800개 이상의 기본 내장 규칙 제공

⌨️ **에디터 통합**: VS Code, PyCharm 등 주요 에디터 지원

🌎 **모노레포 친화적**: 대규모 프로젝트에서도 효율적 동작

### 성능 비교

| 도구 조합 | 실행 시간 (예시) | Ruff 대비 속도 |
| --- | --- | --- |
| Ruff | 0.2초 | 1x |
| Black + isort + Flake8 | 15초 | 75x 느림 |
| pylint | 45초 | 225x 느림 |

\*약 50,000줄 Python 코드 기준

## 설치 및 기본 사용법

### 설치

Ruff는 다양한 방법으로 설치할 수 있습니다.

**pip를 사용한 설치**

```bash
# 전역 설치
pip install ruff

# 개발용으로 설치 (권장)
pip install ruff --upgrade

# 가상환경에 설치
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install ruff
```

**uv를 사용한 설치**

```bash
# 프로젝트에 의존성으로 추가
uv add ruff

# 개발 의존성으로 추가
uv add --dev ruff

# 전역 설치
uv tool install ruff

# uv가 없다면 먼저 uv 설치
curl -LsSf https://astral.sh/uv/install.sh | sh  # Linux/macOS
# Windows: powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### 기본 사용법

설치 후 바로 사용할 수 있는 기본 명령어들입니다.

**pip를 사용하는 경우**

```bash
# 현재 디렉토리의 모든 Python 파일 검사 (linting)
ruff check .

# 코드 포맷팅
ruff format .

# 자동 수정 가능한 오류들 수정
ruff check --fix .
```

**uv를 사용하는 경우**

```bash
# uv run을 통해 실행 (권장)
uv run ruff check .
uv run ruff format .
uv run ruff check --fix .

# 또는 uv tool을 통해 전역 설치했다면
ruff check .
ruff format .
```

**추가 유용한 명령어들**

```bash
# 특정 파일만 검사
ruff check my_file.py
# uv 사용 시: uv run ruff check my_file.py

# 특정 규칙만 검사
ruff check --select E9,F63,F7,F82 .

# 특정 규칙 무시
ruff check --ignore E501 .

# 상세한 출력
ruff check --verbose .

# 통계 표시
ruff check --statistics .

# JSON 형태로 결과 출력
ruff check --output-format json .
```

## 설정 방법

### 설정 파일

Ruff는 `pyproject.toml`, `ruff.toml`, `.ruff.toml` 파일을 통해 설정할 수 있습니다.

#### pyproject.toml을 사용한 설정

```toml
# pyproject.toml

[tool.ruff]
# 한 줄 최대 길이
line-length = 88

# Python 버전 지정
target-version = "py38"

# 검사할 파일 확장자
extend-include = ["*.ipynb"]

# 제외할 디렉토리/파일
exclude = [
    ".bzr",
    ".direnv", 
    ".eggs",
    ".git",
    ".venv",
    "migrations",
    "__pycache__"
]

[tool.ruff.lint]
# 활성화할 규칙들
select = [
    "E",   # pycodestyle errors
    "W",   # pycodestyle warnings  
    "F",   # pyflakes
    "I",   # isort
    "B",   # flake8-bugbear
    "C4",  # flake8-comprehensions
    "UP",  # pyupgrade
]

# 무시할 규칙들
ignore = [
    "E501",  # line too long (Black이 처리)
    "B008",  # function call in argument defaults
]

[tool.ruff.format]
# 따옴표 스타일
quote-style = "double"

# 들여쓰기 스타일  
indent-style = "space"

# 마지막 요소 뒤 쉼표
skip-magic-trailing-comma = false
```

#### ruff.toml을 사용한 설정

```toml
# ruff.toml

line-length = 88
target-version = "py38"

[lint]
select = ["E", "W", "F", "I", "B", "C4", "UP"]
ignore = ["E501", "B008"]

[format]
quote-style = "double"
indent-style = "space"
```

### 주요 규칙 카테고리

Ruff에서 사용하는 주요 규칙들과 그 의미입니다.

-   **E (pycodestyle errors)**: PEP 8 스타일 가이드 오류
-   **W (pycodestyle warnings)**: PEP 8 스타일 가이드 경고
-   **F (Pyflakes)**: 문법 오류, 정의되지 않은 변수 등
-   **I (isort)**: import 구문 정렬 및 스타일
-   **N (pep8-naming)**: 변수명, 함수명 명명 규칙
-   **B (flake8-bugbear)**: 잠재적 버그나 안티패턴
-   **C4 (flake8-comprehensions)**: 리스트/딕셔너리 컴프리헨션 개선
-   **UP (pyupgrade)**: Python 버전 업그레이드 관련 개선
-   **DJ (flake8-django)**: Django 관련 규칙

## VS Code 통합

### 1\. Ruff 확장 프로그램 설치

VS Code에서 Ruff 확장 프로그램을 설치합니다:

1.  VS Code에서 확장 프로그램 탭 열기 (`Ctrl + Shift + X`)
2.  "Ruff" 검색
3.  "Ruff" (게시자: Astral Software) 설치

[##_Image|kage@cU8tjs/btsQgQ0sHPa/AAAAAAAAAAAAAAAAAAAAAFLb8GHxCPAWkwdXmZdzIEWOZDBvSQ8ulq8cXX7pl_7y/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&amp;expires=1759244399&amp;allow_ip=&amp;allow_referer=&amp;signature=ZSw931KmX4t3ZJovS8jN4Ac3QOE%3D|CDM|1.3|{"originWidth":533,"originHeight":199,"style":"alignCenter","filename":"image.png"}_##]

### 2\. VS Code 설정

`settings.json`에 다음 설정을 추가합니다:

#### 기본 설정

```json
{
    "[python]": {
        "editor.defaultFormatter": "charliermarsh.ruff",
        "editor.codeActionsOnSave": {
            "source.organizeImports": "explicit",
            "source.fixAll": "explicit"
        },
        "editor.formatOnSave": true
    }
}
```

#### 상세 설정 (추천)

```json
{
    "[python]": {
        "editor.defaultFormatter": "charliermarsh.ruff",
        "editor.codeActionsOnSave": {
            "source.organizeImports": "explicit",
            "source.fixAll": "explicit"
        },
        "editor.formatOnSave": true,
        "editor.formatOnType": true
    },

    // Ruff 확장 프로그램 전용 설정
    "ruff.lint.enable": true,
    "ruff.format.enable": true,

    // 다른 도구들과의 충돌 방지
    "python.linting.enabled": false,
    "python.linting.pylintEnabled": false,
    "python.linting.flake8Enabled": false,
    "black-formatter.showNotifications": "off"
}
```

### 3. 설정 확인

올바르게 설정되었다면 VS Code 하단 상태바에 **"Ruff (native)"**가 표시됩니다.

## 실제 사용 예시

### Before/After 비교

Ruff를 적용하기 전과 후의 코드를 비교해보겠습니다.

#### 적용 전 코드

```python
# test.py
import os,sys
import pandas as pd
import numpy as np

def calculate_area( length,width ):
    return length*width

def process_data(data):
    result=[]
    for item in data:
        if item>0:
            result.append(item*2)
    return result

unused_variable = "hello"
CONSTANT_VALUE="world"
```

#### 적용 후 코드 (`ruff check --fix . && ruff format .`)

```python
# test.py
import sys

import numpy as np
import pandas as pd


def calculate_area(length, width):
    return length * width


def process_data(data):
    result = []
    for item in data:
        if item > 0:
            result.append(item * 2)
    return result


CONSTANT_VALUE = "world"
```

**변경된 내용**

-   사용하지 않는 `import os` 제거
-   `unused_variable` 제거
-   import 구문 정렬 (표준 라이브러리 → 서드파티)
-   함수 파라미터 공백 조정
-   연산자 주변 공백 추가
-   함수 간 적절한 줄바꿈 추가

### 에러 메시지 해석

Ruff가 출력하는 에러 메시지를 이해하는 방법입니다.

```bash
example.py:5:1: F401 [*] `os` imported but unused
example.py:8:5: E261 at least two spaces before inline comment
example.py:12:1: W292 [*] no newline at end of file
```

**해석:**

-   `example.py:5:1`: 파일명과 위치 (5번째 줄, 1번째 문자)
-   `F401`: 규칙 코드
-   `[*]`: 자동 수정 가능한 오류
-   설명: 구체적인 오류 내용

## pre-commit과 함께 사용

Git 커밋 전에 자동으로 Ruff를 실행하도록 설정할 수 있습니다.

### 1. pre-commit 설치

**pip 사용**

```
pip install pre-commit
```

**uv 사용**

```
# 프로젝트 의존성으로 추가
uv add --dev pre-commit

# 전역 도구로 설치
uv tool install pre-commit
```

### 2. 설정 파일 생성

프로젝트 루트에 `.pre-commit-config.yaml` 파일을 생성:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.6.0  # 최신 버전 사용
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
```

### 3\. pre-commit 설치 및 실행

**pip로 pre-commit을 설치한 경우**

```bash
# pre-commit 훅 설치
pre-commit install

# 모든 파일에 대해 실행 (최초 1회)
pre-commit run --all-files
```

**uv로 pre-commit을 설치한 경우**

```bash
# uv run을 통해 실행
uv run pre-commit install

# 모든 파일에 대해 실행
uv run pre-commit run --all-files

# 또는 uv tool로 전역 설치했다면
pre-commit install
pre-commit run --all-files
```

이제 커밋할 때마다 자동으로 Ruff가 실행됩니다.

```bash
git add .
git commit -m "커밋 메시지"
# → 자동으로 Ruff 검사 및 포맷팅 실행
# → 문제가 있으면 커밋이 중단되고 자동 수정됨
# → 수정된 파일을 다시 add하고 커밋해야 함
```

## Django 프로젝트에서의 활용

Django 프로젝트에서 Ruff를 사용할 때 유용한 설정입니다.

```toml
# pyproject.toml

[tool.ruff]
line-length = 88
target-version = "py38"

# Django 프로젝트에서 제외할 파일들
exclude = [
    "migrations",
    "venv",
    ".venv",
    "manage.py",
    "*/settings/*.py",  # 설정 파일들
]

[tool.ruff.lint]
select = [
    "E", "W", "F", "I", "B", "C4", "UP",
    "DJ",  # Django 관련 규칙 추가
]

ignore = [
    "E501",    # line too long
    "DJ001",   # Django model __str__ method
    "DJ008",   # Django model __str__ return type
]

# Django 프로젝트 구조에 맞는 import 정렬
[tool.ruff.lint.isort]
known-first-party = ["myproject"]  # 프로젝트명으로 변경
section-order = ["future", "standard-library", "django", "third-party", "first-party", "local-folder"]

[tool.ruff.lint.isort.sections]
django = ["django"]
```

## 마무리

Ruff는 Python 개발에서 코드 품질을 크게 향상시킬 수 있는 강력한 도구입니다. 기존의 여러 도구들을 하나로 통합하면서도 뛰어난 성능을 제공하기 때문에, 새로운 프로젝트를 시작하거나 기존 프로젝트를 개선할 때 도입을 추천합니다.

더 자세한 정보는 아래 링크에서 확인할 수 있습니다.

[https://docs.astral.sh/ruff/](https://docs.astral.sh/ruff/ "Ruff 공식 문서")

 [Ruff

Docs | Playground An extremely fast Python linter and code formatter, written in Rust. Linting the CPython codebase from scratch. ⚡️ 10-100x faster than existing linters (like Flake8) and formatters (like Black) 🐍 Installable via pip 🛠️ pyproje

docs.astral.sh](https://docs.astral.sh/ruff/)