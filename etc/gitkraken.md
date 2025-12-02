# GitKraken으로 Git 쉽게 시작하기 - 설치부터 브랜치 관리까지


## GitKraken이란?

GitKraken은 Git GUI 클라이언트로, 복잡한 Git 명령어를 시각적인 인터페이스로 쉽게 사용할 수 있게 해주는 도구입니다.

**주요 특징:**
- 직관적인 커밋 히스토리 그래프
- GitHub/GitLab 등 원격 저장소 연동
- 드래그 앤 드롭으로 브랜치 관리
- 충돌 해결 도구 내장
- Windows, Mac, Linux 모두 지원

---

## 설치 및 초기 설정

### 설치하기

1. [GitKraken 공식 웹사이트](https://www.gitkraken.com/)에서 다운로드
2. 설치 파일 실행
3. 설치 완료 후 바탕화면 바로가기 생성


### GitHub 계정 연동

GitKraken을 처음 실행하면 로그인 화면이 나타납니다.
- GitHub 계정으로 로그인하면 저장소 연동이 자동으로 됩니다.

---

## 저장소 생성하기

실습을 위해 새로운 저장소를 만들어보겠습니다.

### Step 1: Init 메뉴 열기

1. 왼쪽 상단의 **폴더 아이콘** 클릭 (또는 `Ctrl + I`)
2. **"Init"** 탭 선택

### Step 2: GitHub 저장소 생성

- **GitHub.com** 옵션 선택 (로컬과 원격 저장소 동시 생성)
- **Account**: 본인의 GitHub 계정
- **Name**: `gitkraken-practice` (저장소 이름)
- **Access**: Public 또는 Private 선택
- **Where to clone to**: 저장소를 저장할 폴더 선택
- **"Create Repository and Clone"** 버튼 클릭

**📸 [이미지 1: 저장소 생성 완료 화면]**
> 첫 번째 업로드 이미지 (gitkraken-practice 저장소가 생성된 화면)

저장소가 생성되면 GitKraken 중앙에 **커밋 그래프**가 나타나고, "Initial commit"이 보입니다.

---

## 기본 Git 작업

### 파일 수정하기

VS Code나 원하는 텍스트 에디터로 저장소 폴더를 열어 README.md 파일을 수정해봅시다.

```markdown
# GitKraken 실습

## 목표
GitKraken을 사용한 Git 작업 익히기

## 실습 내용
1. 저장소 생성
2. 파일 수정 및 커밋
3. 브랜치 생성 및 관리
```

파일을 저장(`Ctrl + S`)하면 GitKraken에서 변경사항이 자동으로 감지됩니다.

**주의:** VS Code에서 올바른 저장소 폴더를 열어야 합니다!
- 경로 확인: GitKraken에서 저장소 이름 오른쪽 클릭 → "Open in File Manager"

**📸 [이미지 2: 변경사항 감지 화면]**
> 두 번째 업로드 이미지 (Unstaged Files에 README.md가 표시된 화면)

### Commit 하기

**Step 1: Stage**
- 오른쪽 패널에서 **"Stage All Changes"** 버튼 클릭
- 또는 개별 파일을 드래그해서 "Staged Files" 영역으로 이동

**Step 2: 커밋 메시지 작성**
```
docs: README 내용 추가
```

**Step 3: Commit**
- 하단의 **"Commit Changes to 1 File"** 버튼 클릭

**📸 [이미지 3: 커밋 완료 화면]**
> 세 번째 업로드 이미지 (커밋이 완료되어 그래프에 표시된 화면)

### Push 하기 (GitHub에 업로드)

- 상단 툴바의 **"Push" 버튼 (↑)** 클릭
- "Pushed Successfully" 알림 확인

**📸 [이미지 4: Push 성공 화면]**
> 네 번째 업로드 이미지 (Pushed Successfully 알림이 표시된 화면)

이제 GitHub 저장소에서도 변경사항을 확인할 수 있습니다!

### Pull 하기 (GitHub에서 가져오기)

Pull은 GitHub의 최신 변경사항을 로컬로 가져오는 작업입니다.

**실습: GitHub에서 직접 파일 수정하기**

1. GitHub 웹사이트에서 저장소 접속
2. README.md 파일 클릭 → 연필 아이콘(Edit) 클릭
3. 내용 추가:
```markdown

## Pull 실습
GitHub에서 직접 수정한 내용입니다!
```
4. "Commit changes" 버튼 클릭

**GitKraken에서 Pull 하기:**
- 상단 툴바의 **"Pull" 버튼 (↓)** 클릭
- "Pulled Successfully" 알림 확인

**📸 [이미지 5: Pull 완료 화면]**
> 다섯 번째 업로드 이미지 (GitHub에서 수정한 커밋이 추가된 화면)

로컬 파일을 확인하면 GitHub에서 추가한 내용이 반영되어 있습니다!

---

## 브랜치 생성 및 관리

브랜치는 메인 코드를 건드리지 않고 독립적으로 작업할 수 있는 공간입니다.

### 브랜치 만들기

**방법 1 (추천):**
- 상단 툴바의 **"Branch"** 버튼 클릭 (또는 `Ctrl + B`)

**방법 2:**
- 중앙 그래프에서 브랜치를 생성할 커밋에 오른쪽 클릭 → "Create branch here"

**브랜치 이름 입력:**
```
dev
```

브랜치 이름 규칙:
- `feature/기능명`: 새 기능 개발
- `fix/버그명`: 버그 수정
- `dev`: 개발용

**📸 [이미지 6: 브랜치 생성 완료]**
> 여섯 번째 업로드 이미지 (dev 브랜치가 생성된 화면)

왼쪽 상단을 보면 현재 브랜치가 **"dev"**로 표시됩니다.

### dev 브랜치에서 작업하기

새 파일 `practice.txt`를 만들어봅시다.

**VS Code에서:**
1. 저장소 폴더에 `practice.txt` 파일 생성
2. 내용 입력:
```
이것은 dev 브랜치에서 만든 파일입니다.
GitKraken 브랜치 실습 중!
```
3. 저장 (`Ctrl + S`)

**GitKraken에서 커밋:**
1. "Stage All Changes" 클릭
2. 커밋 메시지 작성: `feat: practice.txt 파일 추가`
3. "Commit" 버튼 클릭

**📸 [이미지 7: dev 브랜치 커밋 완료]**
> 일곱 번째 업로드 이미지 (dev 브랜치에 새 커밋이 추가된 그래프)

그래프를 보면 **dev 브랜치가 main에서 분기**된 것을 확인할 수 있습니다!

### 브랜치 전환하기

**main 브랜치로 전환:**
- 왼쪽 패널 "LOCAL" 섹션에서 **"main"** 더블클릭

**신기한 현상 발견!**
파일 탐색기에서 저장소 폴더를 확인해보세요.
- main 브랜치: `practice.txt` 파일이 **없습니다**
- dev 브랜치: `practice.txt` 파일이 **있습니다**

브랜치를 전환하면 파일 시스템도 자동으로 변경됩니다! 이것이 브랜치의 마법입니다. ✨

**📸 [이미지 8: main 브랜치로 전환]**
> 여덟 번째 업로드 이미지 (main 브랜치로 전환된 화면)

---

## 브랜치 병합(Merge)

dev 브랜치의 작업을 main 브랜치에 합쳐봅시다.

### Merge 하기

**Step 1: main 브랜치로 전환**
- 왼쪽 패널에서 "main" 더블클릭
- 왼쪽 상단이 "branch: main"인지 확인

**Step 2: dev 브랜치를 Merge**
- 중앙 그래프에서 **"dev" 브랜치 라벨**을 오른쪽 클릭
- **"Merge dev into main"** 선택

**Step 3: Merge 완료 확인**
- "Merged" 알림 표시
- 그래프에 "Merge branch 'dev'" 커밋 생성

**📸 [이미지 9: Merge 완료 화면]**
> 아홉 번째 업로드 이미지 (Merge가 완료된 그래프)

이제 main 브랜치에도 `practice.txt` 파일이 생겼습니다!

### GitHub에 Push

- 상단 툴바의 **"Push" 버튼 (↑)** 클릭
- "Pushed Successfully" 알림 확인

**📸 [이미지 10: Merge 후 Push 완료]**
> 열 번째 업로드 이미지 (Push가 완료된 화면)

### dev 브랜치도 GitHub에 올리기

dev 브랜치를 GitHub에도 올려서 팀원들과 공유할 수 있습니다.

**Step 1: dev 브랜치로 전환**
- 왼쪽 패널에서 "dev" 더블클릭

**Step 2: Push**
- 상단 툴바의 "Push" 버튼 클릭
- 처음 Push하는 브랜치라면 설정 창이 나타납니다

**Step 3: 설정 확인**
- Remote: `origin` (GitHub)
- Branch: `dev`
- **"Submit"** 버튼 클릭

**📸 [이미지 11: dev 브랜치 Push 설정]**
> 열한 번째 업로드 이미지 (dev 브랜치 Push 설정 화면)

**📸 [이미지 12: dev 브랜치 Push 완료]**
> 열두 번째 업로드 이미지 (REMOTE에 origin/dev가 추가된 화면)

이제 GitHub에서 브랜치 드롭다운을 확인하면 **"dev" 브랜치**를 볼 수 있습니다!

---

## GitKraken 주요 화면 구성

GitKraken의 화면 구성을 이해하면 더 효율적으로 사용할 수 있습니다.

### 왼쪽 패널
- **LOCAL**: 로컬 브랜치 목록
- **REMOTE**: 원격 저장소의 브랜치
- **PULL REQUESTS**: GitHub Pull Request 목록
- **ISSUES**: GitHub Issues

### 중앙 영역
- **커밋 히스토리 그래프**: 시각적으로 커밋 흐름 표시
- 브랜치 간 관계를 한눈에 파악 가능

### 오른쪽 패널
- **Unstaged Files**: 아직 스테이징 안 된 변경사항
- **Staged Files**: 커밋 준비된 파일
- 커밋 상세 정보 (작성자, 시간, 변경 내용)

### 상단 툴바
- **Undo/Redo**: 작업 되돌리기
- **Pull (↓)**: GitHub에서 가져오기
- **Push (↑)**: GitHub에 올리기
- **Branch**: 브랜치 생성
- **Stash**: 임시 저장
- **Terminal**: 터미널 열기

---

## 유용한 팁

### 단축키
- `Ctrl + I`: 저장소 열기/생성
- `Ctrl + B`: 브랜치 생성
- `Ctrl + P`: Push
- `Ctrl + Shift + P`: Pull
- `Ctrl + R`: 새로고침

### 커밋 메시지 컨벤션
좋은 커밋 메시지 작성 규칙:
```
feat: 새로운 기능 추가
fix: 버그 수정
docs: 문서 수정
style: 코드 포맷팅
refactor: 코드 리팩토링
test: 테스트 코드 추가
chore: 기타 변경사항
```

예시:
- `feat: 로그인 기능 추가`
- `fix: 회원가입 버그 수정`
- `docs: README에 설치 방법 추가`

### 파일 경로 주의사항
VS Code에서 작업할 때 **반드시 GitKraken에서 생성한 저장소 폴더**를 열어야 합니다.
- GitKraken에서 저장소 경로 확인: 저장소 이름 오른쪽 클릭 → "Open in File Manager"

---

## 마무리

### GitKraken의 장점
- ✅ 직관적인 GUI로 Git 명령어 없이 사용 가능
- ✅ 커밋 히스토리를 시각적으로 확인
- ✅ 브랜치 관리가 드래그 앤 드롭으로 쉬움
- ✅ GitHub/GitLab 등과 완벽한 연동
- ✅ 충돌 해결 도구 내장

### GitKraken의 단점
- ❌ 무료 버전은 Public 저장소만 사용 가능 (Private는 유료)
- ❌ 프로그램 용량이 큰 편
- ❌ 복잡한 Git 명령어는 터미널이 더 빠를 수 있음

### 추천 대상
- Git을 처음 배우는 초보자
- 시각적 인터페이스를 선호하는 개발자
- 브랜치가 많은 프로젝트를 관리하는 팀
- GitHub/GitLab을 주로 사용하는 개발자