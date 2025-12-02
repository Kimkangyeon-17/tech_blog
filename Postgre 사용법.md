# PostgreSQL 사용법

## 📌 기본 정보
- **버전**: PostgreSQL 16.11
- **설치 경로**: `C:\pgsql`
- **포트**: 5432
- **사용자**: postgres
- **데이터 디렉토리**: `C:\pgsql\data`

---

## 🚀 서버 시작/중지

### 서버 시작
```powershell
C:\pgsql\bin\pg_ctl.exe -D C:\pgsql\data start
```

### 서버 중지
```powershell
C:\pgsql\bin\pg_ctl.exe -D C:\pgsql\data stop
```

### 서버 재시작
```powershell
C:\pgsql\bin\pg_ctl.exe -D C:\pgsql\data restart
```

### 서버 상태 확인
```powershell
C:\pgsql\bin\pg_ctl.exe -D C:\pgsql\data status
```

---

## 💻 psql 접속 (커맨드라인)

### 기본 접속
```powershell
C:\pgsql\bin\psql.exe -U postgres
```

### 특정 데이터베이스 접속
```powershell
C:\pgsql\bin\psql.exe -U postgres -d 데이터베이스명
```

---

## 📊 psql 주요 명령어

| 명령어 | 설명 |
|--------|------|
| `\l` | 데이터베이스 목록 조회 |
| `\c 데이터베이스명` | 데이터베이스 전환 |
| `\dt` | 현재 DB의 테이블 목록 |
| `\d 테이블명` | 테이블 구조 확인 |
| `\du` | 사용자 목록 |
| `\q` | psql 종료 |
| `\?` | 도움말 |

---

## 🗄️ 데이터베이스 관리

### 데이터베이스 생성
```sql
CREATE DATABASE 데이터베이스명;
```

### 데이터베이스 삭제
```sql
DROP DATABASE 데이터베이스명;
```

### 데이터베이스 목록 확인
```sql
SELECT datname FROM pg_database;
```

---

## 👤 사용자 관리

### 새 사용자 생성
```sql
CREATE USER 사용자명 WITH PASSWORD '비밀번호';
```

### 사용자에게 권한 부여
```sql
GRANT ALL PRIVILEGES ON DATABASE 데이터베이스명 TO 사용자명;
```

### 사용자 삭제
```sql
DROP USER 사용자명;
```

---

## 🔧 pgAdmin 4 사용

### pgAdmin 실행
- Windows 시작 메뉴에서 "pgAdmin 4" 검색 및 실행

### 서버 연결
1. 왼쪽 **Servers** → **PostgreSQL 17** 클릭
2. 비밀번호 입력

### 새 데이터베이스 생성
1. **Databases** 우클릭
2. **Create** → **Database**
3. 이름 입력 후 **Save**

### 쿼리 실행
1. 데이터베이스 우클릭
2. **Query Tool** 선택
3. SQL 작성 후 **F5** 또는 ▶️ 버튼 클릭

---

## 🐍 Django 연동

### 1. psycopg2 설치
```bash
pip install psycopg2 --break-system-packages
```

### 2. settings.py 설정
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': '데이터베이스명',
        'USER': 'postgres',
        'PASSWORD': '비밀번호',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

### 3. 마이그레이션
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 🔍 유용한 SQL 쿼리

### 현재 버전 확인
```sql
SELECT version();
```

### 현재 데이터베이스 확인
```sql
SELECT current_database();
```

### 테이블 크기 확인
```sql
SELECT pg_size_pretty(pg_total_relation_size('테이블명'));
```

### 활성 연결 확인
```sql
SELECT * FROM pg_stat_activity;
```

---

## ⚠️ 문제 해결

### 서버가 시작되지 않을 때
```powershell
# 로그 파일 확인
type C:\pgsql\logfile
```

### 포트 충돌 확인
```powershell
netstat -ano | findstr :5432
```

### 프로세스 강제 종료
```powershell
Get-Process | Where-Object {$_.Name -like "*postgres*"} | Stop-Process -Force
```

---

## 📚 참고 자료
- 공식 문서: https://www.postgresql.org/docs/
- pgAdmin 문서: https://www.pgadmin.org/docs/
- Django 연동: https://docs.djangoproject.com/en/stable/ref/databases/#postgresql-notes

---

## 💡 팁
- pgAdmin에서 비밀번호 저장하면 매번 입력 안 해도 됨
- 정기적으로 백업 수행: `pg_dump` 명령어 사용
- 개발 중에는 서버 자동 시작 설정 권장