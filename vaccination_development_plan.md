# 예방접종 관리 시스템 개발 계획

## Epic: 예방접종 관리 시스템

---

## Story 1: 기본 데이터 모델 및 초기 데이터 구축

**목표**: 예방접종 관리에 필요한 기본 데이터 구조와 백신 정보 준비

### Task 1.1: Django 앱 생성 및 기본 설정
- [ ] `vaccinations` 앱 생성
- [ ] `settings.py`에 앱 등록
- [ ] `MEDIA_URL`, `MEDIA_ROOT` 설정
- [ ] `.gitignore` 설정 확인
- **예상 시간**: 30분
- **완료 조건**: 앱이 정상적으로 인식되고 기본 설정 완료

### Task 1.2: 데이터 모델 설계 및 생성
- [ ] `Child` 모델 작성
- [ ] `Vaccine` 모델 작성
- [ ] `VaccineSchedule` 모델 작성
- [ ] `VaccinationRecord` 모델 작성
- [ ] `VaccinationReminder` 모델 작성
- [ ] `__str__` 메서드, `Meta` 클래스 작성
- **예상 시간**: 2시간
- **완료 조건**: 모든 모델이 정의되고 필드 관계가 명확함

### Task 1.3: 마이그레이션 생성 및 적용
- [ ] `makemigrations` 실행
- [ ] 마이그레이션 파일 검토
- [ ] `migrate` 실행
- [ ] DB 테이블 생성 확인
- **예상 시간**: 30분
- **완료 조건**: DB에 모든 테이블이 정상 생성됨

### Task 1.4: Admin 페이지 구성
- [ ] 모든 모델 Admin 등록
- [ ] `list_display` 설정
- [ ] `list_filter`, `search_fields` 설정
- [ ] `readonly_fields` 설정
- [ ] Superuser 생성
- **예상 시간**: 1시간
- **완료 조건**: Admin에서 모든 모델 CRUD 가능

### Task 1.5: 백신 데이터 수집 및 정리
- [ ] 질병관리청 자료 조사
- [ ] 0~12세 필수 백신 목록 정리
- [ ] 각 백신별 접종 시기 정리
- [ ] 엑셀/노션으로 데이터 정리
- **예상 시간**: 3시간
- **완료 조건**: 모든 백신의 접종 일정이 문서화됨

### Task 1.6: 초기 데이터 로드 스크립트 작성
- [ ] `management/commands` 디렉토리 생성
- [ ] `load_all_vaccine_data.py` 작성
- [ ] 15개 백신 데이터 코드로 작성
- [ ] 접종 일정 데이터 작성
- [ ] 스크립트 실행 및 검증
- **예상 시간**: 3시간
- **완료 조건**: 명령어 실행 시 모든 백신 데이터가 DB에 저장됨

**Story 1 완료 기준**: Admin에서 모든 백신과 접종 일정 조회 가능

**Story 1 총 예상 시간**: 약 10시간

---

## Story 2: 유틸리티 함수 개발

**목표**: 날짜 계산 및 접종 일정 자동 생성 로직 구현

### Task 2.1: python-dateutil 설치 및 테스트
- [ ] `pip install python-dateutil`
- [ ] `requirements.txt`에 추가
- [ ] Django shell에서 간단한 테스트
- **예상 시간**: 30분
- **완료 조건**: relativedelta 정상 작동 확인

### Task 2.2: 날짜 계산 함수 작성
- [ ] `utils.py` 파일 생성
- [ ] `calculate_vaccination_date()` 함수 작성
- [ ] `calculate_age_in_months()` 함수 작성
- [ ] 월말 처리 테스트 (1월 31일생 등)
- **예상 시간**: 1시간
- **완료 조건**: 다양한 생년월일로 정확한 접종일 계산

### Task 2.3: 접종 상태 판단 함수 작성
- [ ] `get_vaccination_status()` 함수 작성
- [ ] 상태 분류 로직 (completed/upcoming/overdue/scheduled)
- [ ] 경계 케이스 처리 (오늘이 접종일인 경우 등)
- **예상 시간**: 1.5시간
- **완료 조건**: 모든 상태가 정확히 판단됨

### Task 2.4: 접종 일정 자동 생성 함수 작성
- [ ] `create_vaccination_schedule_for_child()` 함수 작성
- [ ] 중복 방지 로직 (get_or_create)
- [ ] 알림 자동 생성 로직
- [ ] 트랜잭션 처리 고려
- **예상 시간**: 2시간
- **완료 조건**: 아이 생성 시 모든 접종 기록 자동 생성

### Task 2.5: 타임라인 조회 함수 작성
- [ ] `get_child_vaccination_timeline()` 함수 작성
- [ ] 상태별 분류 로직
- [ ] 날짜순 정렬
- [ ] 성능 최적화 (select_related)
- **예상 시간**: 2시간
- **완료 조건**: 상태별로 정확히 분류되고 정렬됨

### Task 2.6: 다가오는 접종 조회 함수 작성
- [ ] `get_next_vaccinations()` 함수 작성
- [ ] 기간 필터링 (days_ahead 파라미터)
- [ ] D-day 계산
- **예상 시간**: 1시간
- **완료 조건**: 30일 이내 접종 목록 정확히 조회

### Task 2.7: 유틸리티 함수 단위 테스트 작성
- [ ] `tests/test_utils.py` 생성
- [ ] 각 함수별 테스트 케이스 작성
- [ ] 경계값 테스트 (0개월, 월말 등)
- [ ] 모든 테스트 통과 확인
- **예상 시간**: 2시간
- **완료 조건**: `python manage.py test` 모두 통과

**Story 2 완료 기준**: 모든 유틸리티 함수가 테스트를 통과하고 정확히 작동함

**Story 2 총 예상 시간**: 약 10시간

---

## Story 3: Signal 및 자동화 구현

**목표**: 아이 등록 시 자동으로 접종 일정이 생성되도록 구현

### Task 3.1: Signal 파일 생성
- [ ] `signals.py` 파일 생성
- [ ] `post_save` signal 작성
- [ ] `create_child_vaccination_schedule()` 함수 작성
- **예상 시간**: 1시간
- **완료 조건**: Signal 함수가 정의됨

### Task 3.2: AppConfig에 Signal 등록
- [ ] `apps.py` 수정
- [ ] `ready()` 메서드에 signal import
- **예상 시간**: 30분
- **완료 조건**: 앱 시작 시 signal이 자동 등록됨

### Task 3.3: Signal 동작 테스트
- [ ] Django shell에서 Child 객체 생성
- [ ] VaccinationRecord 자동 생성 확인
- [ ] VaccinationReminder 자동 생성 확인
- [ ] Admin에서 데이터 확인
- **예상 시간**: 1시간
- **완료 조건**: 아이 생성 시 모든 일정이 자동 생성됨

### Task 3.4: Signal 테스트 코드 작성
- [ ] `tests/test_signals.py` 생성
- [ ] Signal 발동 테스트
- [ ] 생성된 레코드 개수 검증
- **예상 시간**: 1시간
- **완료 조건**: Signal 테스트 통과

**Story 3 완료 기준**: 아이 등록 시 자동으로 모든 접종 일정이 생성됨

**Story 3 총 예상 시간**: 약 3.5시간

---

## Story 4: REST API 개발 (DRF)

**목표**: 프론트엔드에서 사용할 API 엔드포인트 구현

### Task 4.1: DRF 설치 및 설정
- [ ] `pip install djangorestframework`
- [ ] `settings.py`에 `rest_framework` 추가
- [ ] DRF 기본 설정 (페이지네이션, 권한 등)
- **예상 시간**: 30분
- **완료 조건**: DRF가 프로젝트에 통합됨

### Task 4.2: Serializer 작성
- [ ] `serializers.py` 파일 생성
- [ ] `ChildSerializer` 작성
- [ ] `VaccineSerializer` 작성
- [ ] `VaccineScheduleSerializer` 작성
- [ ] `VaccinationRecordSerializer` 작성
- [ ] `VaccinationReminderSerializer` 작성
- [ ] Nested serializer 처리
- **예상 시간**: 3시간
- **완료 조건**: 모든 모델의 Serializer 작성 완료

### Task 4.3: 아이 정보 CRUD API
- [ ] `ChildViewSet` 작성
- [ ] List (목록 조회)
- [ ] Create (아이 등록)
- [ ] Retrieve (상세 조회)
- [ ] Update (정보 수정)
- [ ] Destroy (삭제)
- [ ] URL 라우팅 설정
- **예상 시간**: 2시간
- **완료 조건**: Postman/Swagger로 CRUD 테스트 통과

### Task 4.4: 백신 정보 조회 API
- [ ] `VaccineViewSet` 작성 (읽기 전용)
- [ ] 필수/선택 백신 필터링
- [ ] URL 라우팅 설정
- **예상 시간**: 1시간
- **완료 조건**: 백신 목록 조회 가능

### Task 4.5: 접종 기록 관리 API
- [ ] `VaccinationRecordViewSet` 작성
- [ ] 접종 완료 처리 (PATCH)
- [ ] 접종 정보 수정 (병원, 날짜 등)
- [ ] 증명서 이미지 업로드
- [ ] 아이별 필터링
- **예상 시간**: 3시간
- **완료 조건**: 접종 기록 CRUD 가능

### Task 4.6: 접종 타임라인 API
- [ ] Custom action 작성 (`@action` 데코레이터)
- [ ] `/api/children/{id}/timeline/` 엔드포인트
- [ ] 상태별 분류된 데이터 반환
- **예상 시간**: 2시간
- **완료 조건**: 타임라인 조회 API 작동

### Task 4.7: 다가오는 접종 API
- [ ] Custom action 작성
- [ ] `/api/children/{id}/upcoming/` 엔드포인트
- [ ] days_ahead 쿼리 파라미터 처리
- **예상 시간**: 1.5시간
- **완료 조건**: 다가오는 접종 조회 가능

### Task 4.8: API 문서화 (Swagger)
- [ ] `drf-yasg` 또는 `drf-spectacular` 설치
- [ ] Swagger UI 설정
- [ ] 각 엔드포인트 docstring 작성
- [ ] 예시 요청/응답 추가
- **예상 시간**: 2시간
- **완료 조건**: `/api/docs/` 에서 API 문서 확인 가능

### Task 4.9: API 통합 테스트
- [ ] `tests/test_api.py` 생성
- [ ] 각 엔드포인트별 테스트
- [ ] 권한 테스트 (로그인 필요)
- [ ] 에러 케이스 테스트 (404, 400 등)
- **예상 시간**: 3시간
- **완료 조건**: 모든 API 테스트 통과

**Story 4 완료 기준**: 모든 API 엔드포인트가 Swagger 문서화되고 테스트 통과

**Story 4 총 예상 시간**: 약 18시간

---

## Story 5: accounts 앱과 통합

**목표**: User 모델과 예방접종 데이터 연결

### Task 5.1: accounts 팀과 인터페이스 협의
- [ ] User 모델 구조 확인
- [ ] 인증 방식 확인 (JWT, Session 등)
- [ ] API 엔드포인트 정의서 공유
- **예상 시간**: 1시간
- **완료 조건**: 통합 계획서 문서화

### Task 5.2: Child 모델에 User 관계 추가
- [ ] `guardians` ManyToManyField 주석 해제
- [ ] 마이그레이션 생성 및 적용
- [ ] 기존 데이터 마이그레이션 처리
- **예상 시간**: 1.5시간
- **완료 조건**: User-Child 관계 설정 완료

### Task 5.3: 권한 관리 구현
- [ ] Permission 클래스 작성
- [ ] 본인의 아이만 조회/수정 가능하도록 설정
- [ ] ViewSet에 permission_classes 적용
- **예상 시간**: 2시간
- **완료 조건**: 다른 사용자의 데이터 접근 불가

### Task 5.4: 인증 테스트
- [ ] JWT 토큰으로 API 호출 테스트
- [ ] 권한 없는 접근 테스트 (403)
- [ ] 로그인 필수 엔드포인트 테스트 (401)
- **예상 시간**: 2시간
- **완료 조건**: 인증/권한 시스템 정상 작동

### Task 5.5: Serializer에 User 정보 추가
- [ ] `guardians` 필드 Serializer에 추가
- [ ] 읽기/쓰기 권한 분리
- **예상 시간**: 1시간
- **완료 조건**: API 응답에 보호자 정보 포함

**Story 5 완료 기준**: User와 완전히 통합되고 권한 관리가 작동함

**Story 5 총 예상 시간**: 약 7.5시간

---

## Story 6: 알림 시스템 구현

**목표**: 접종 예정일 푸시 알림 발송

### Task 6.1: Firebase Cloud Messaging (FCM) 설정
- [ ] Firebase 프로젝트 생성
- [ ] FCM 서비스 계정 키 발급
- [ ] `fcm-django` 또는 `firebase-admin` 설치
- **예상 시간**: 2시간
- **완료 조건**: FCM 연동 준비 완료

### Task 6.2: 디바이스 토큰 관리
- [ ] `DeviceToken` 모델 생성 (User별)
- [ ] 토큰 등록 API
- [ ] 토큰 갱신 로직
- **예상 시간**: 2시간
- **완료 조건**: 디바이스 토큰 저장 가능

### Task 6.3: 알림 발송 함수 작성
- [ ] `send_vaccination_reminder()` 함수
- [ ] FCM API 호출 로직
- [ ] 알림 제목/내용 템플릿
- **예상 시간**: 2시간
- **완료 조건**: 푸시 알림 수동 발송 가능

### Task 6.4: 알림 스케줄러 구현
- [ ] Celery 설치 및 설정
- [ ] Redis 설치 (Celery broker)
- [ ] Celery Beat 설정 (주기적 작업)
- [ ] 매일 알림 체크 Task 작성
- **예상 시간**: 3시간
- **완료 조건**: 매일 자동으로 알림 발송 체크

### Task 6.5: 알림 발송 이력 관리
- [ ] `VaccinationReminder` 모델의 `is_sent`, `sent_at` 활용
- [ ] 중복 발송 방지 로직
- [ ] 발송 실패 재시도 로직
- **예상 시간**: 2시간
- **완료 조건**: 알림 발송 이력 추적 가능

### Task 6.6: 알림 설정 API
- [ ] 알림 on/off 설정 API
- [ ] 알림 시간 설정 API
- [ ] 알림 미리 받기 설정 (7일 전, 3일 전 등)
- **예상 시간**: 2시간
- **완료 조건**: 사용자가 알림 설정 변경 가능

### Task 6.7: 알림 테스트
- [ ] 테스트 알림 발송 API
- [ ] 실제 디바이스에서 알림 수신 확인
- [ ] 다양한 시나리오 테스트
- **예상 시간**: 2시간
- **완료 조건**: 실제 디바이스에서 알림 수신 성공

**Story 6 완료 기준**: 접종 예정일 알림이 자동으로 발송됨

**Story 6 총 예상 시간**: 약 15시간

---

## Story 7: 추가 기능 및 최적화

**목표**: 사용성 개선 및 성능 최적화

### Task 7.1: 접종 증명서 이미지 처리
- [ ] 이미지 업로드 최적화 (리사이징)
- [ ] 이미지 미리보기 API
- [ ] 이미지 삭제 기능
- **예상 시간**: 2시간
- **완료 조건**: 이미지 업로드/조회/삭제 가능

### Task 7.2: 통계 API
- [ ] 접종 완료율 계산 API
- [ ] 연령대별 접종 현황
- [ ] 차트용 데이터 제공
- **예상 시간**: 2시간
- **완료 조건**: 통계 데이터 조회 가능

### Task 7.3: 검색 및 필터링
- [ ] 백신명으로 검색
- [ ] 접종 완료/미완료 필터
- [ ] 날짜 범위 필터
- **예상 시간**: 2시간
- **완료 조건**: 다양한 조건으로 필터링 가능

### Task 7.4: 캐싱 적용
- [ ] 백신 목록 캐싱 (자주 변하지 않음)
- [ ] Redis 캐시 설정
- [ ] 캐시 무효화 전략
- **예상 시간**: 2시간
- **완료 조건**: API 응답 속도 개선

### Task 7.5: 로깅 설정
- [ ] 에러 로깅 설정
- [ ] API 호출 로깅
- [ ] 알림 발송 로깅
- **예상 시간**: 1.5시간
- **완료 조건**: 로그 파일 생성 및 모니터링 가능

### Task 7.6: 성능 테스트 및 최적화
- [ ] 쿼리 최적화 (N+1 문제 해결)
- [ ] 인덱스 추가
- [ ] 대량 데이터 테스트
- **예상 시간**: 3시간
- **완료 조건**: 성능 병목 지점 해결

**Story 7 완료 기준**: 모든 추가 기능이 정상 작동하고 성능이 최적화됨

**Story 7 총 예상 시간**: 약 12.5시간

---

## Story 8: 배포 준비

**목표**: 프로덕션 환경 배포 준비

### Task 8.1: 환경 변수 분리
- [ ] `.env` 파일 생성
- [ ] `django-environ` 설치
- [ ] SECRET_KEY, DB 설정 환경변수화
- **예상 시간**: 1시간
- **완료 조건**: 민감 정보가 코드에서 분리됨

### Task 8.2: 프로덕션 설정 분리
- [ ] `settings/base.py`, `settings/dev.py`, `settings/prod.py` 분리
- [ ] DEBUG 모드 분리
- [ ] ALLOWED_HOSTS 설정
- **예상 시간**: 1.5시간
- **완료 조건**: 개발/프로덕션 환경 분리

### Task 8.3: Static/Media 파일 처리
- [ ] `STATIC_ROOT` 설정
- [ ] `collectstatic` 실행
- [ ] S3 또는 CDN 연동 (선택)
- **예상 시간**: 2시간
- **완료 조건**: Static 파일 서빙 가능

### Task 8.4: Docker 설정 (선택)
- [ ] `Dockerfile` 작성
- [ ] `docker-compose.yml` 작성
- [ ] 로컬에서 Docker 테스트
- **예상 시간**: 3시간
- **완료 조건**: Docker로 앱 실행 가능

### Task 8.5: CI/CD 파이프라인 구축
- [ ] GitHub Actions 워크플로우 작성
- [ ] 테스트 자동 실행
- [ ] 배포 자동화 스크립트
- **예상 시간**: 3시간
- **완료 조건**: Push 시 자동 테스트 실행

### Task 8.6: 프로덕션 배포
- [ ] EC2, Heroku, Railway 등 배포
- [ ] PostgreSQL 연결
- [ ] Gunicorn, Nginx 설정
- [ ] HTTPS 설정
- **예상 시간**: 4시간
- **완료 조건**: 실제 서버에서 앱 실행

**Story 8 완료 기준**: 프로덕션 환경에 앱이 정상 배포됨

**Story 8 총 예상 시간**: 약 14.5시간

---

## 전체 일정 요약

### Sprint 1 (1주차) - 총 20시간
- Story 1: 기본 데이터 모델 구축 (10시간)
- Story 2: 유틸리티 함수 개발 (10시간)

### Sprint 2 (2주차) - 총 21.5시간
- Story 3: Signal 및 자동화 (3.5시간)
- Story 4: REST API 개발 (18시간)

### Sprint 3 (3주차) - 총 22.5시간
- Story 5: accounts 통합 (7.5시간)
- Story 6: 알림 시스템 (15시간)

### Sprint 4 (4주차) - 총 27시간
- Story 7: 추가 기능 및 최적화 (12.5시간)
- Story 8: 배포 준비 (14.5시간)

**전체 예상 시간**: 약 91시간 (버퍼 시간 제외)

---

## GitHub Projects 설정 가이드

### Kanban 보드 컬럼
1. 📋 **Backlog** - 아직 시작하지 않은 작업
2. 🔜 **To Do** - 다음에 할 작업
3. 🏗️ **In Progress** - 현재 진행 중
4. 👀 **In Review** - 리뷰/테스트 중
5. ✅ **Done** - 완료된 작업

### Issue 라벨
- `epic`: Epic 수준의 큰 작업
- `story`: Story 수준의 중간 작업
- `task`: Task 수준의 작은 작업
- `bug`: 버그 수정
- `documentation`: 문서화 작업
- `enhancement`: 기능 개선
- `priority-high`: 높은 우선순위
- `priority-medium`: 중간 우선순위
- `priority-low`: 낮은 우선순위

### GitHub Issue 템플릿 예시

```markdown
**Story**: [Story 번호 및 제목]
**예상 시간**: X시간

## 작업 내용
- [ ] 체크리스트 항목 1
- [ ] 체크리스트 항목 2
- [ ] 체크리스트 항목 3

## 완료 조건
- 구체적인 완료 조건 명시

## 참고 사항
- 관련 문서나 링크
```

---

## 추가 권장 사항

### 개발 환경 설정
```bash
# .env.example 파일 생성 (GitHub에 커밋)
SECRET_KEY=your-secret-key-here
DEBUG=True
DATABASE_URL=postgresql://user:password@localhost/dbname
ALLOWED_HOSTS=localhost,127.0.0.1

# .env 파일은 .gitignore에 추가
```

### requirements.txt 예시
```
Django==4.2.0
djangorestframework==3.14.0
python-dateutil==2.8.2
Pillow==10.0.0
drf-yasg==1.21.7
django-environ==0.11.0
psycopg2-binary==2.9.9
celery==5.3.4
redis==5.0.1
firebase-admin==6.2.0
```

### 문서화 권장
- README.md: 프로젝트 소개, 설치 방법
- API_DOCS.md: API 엔드포인트 명세
- DEVELOPMENT.md: 개발 환경 설정 가이드
- DEPLOYMENT.md: 배포 가이드

---

## 체크포인트

### Sprint 1 종료 시 확인 사항
- [ ] 모든 모델이 정의되고 마이그레이션 완료
- [ ] 백신 데이터가 DB에 로드됨
- [ ] Admin에서 모든 데이터 CRUD 가능
- [ ] 유틸리티 함수 테스트 통과

### Sprint 2 종료 시 확인 사항
- [ ] 아이 등록 시 접종 일정 자동 생성
- [ ] 모든 API 엔드포인트 작동
- [ ] Swagger 문서화 완료
- [ ] API 테스트 통과

### Sprint 3 종료 시 확인 사항
- [ ] User 인증/권한 시스템 작동
- [ ] 푸시 알림 발송 성공
- [ ] 알림 스케줄러 정상 작동

### Sprint 4 종료 시 확인 사항
- [ ] 프로덕션 환경 배포 완료
- [ ] 모든 기능 통합 테스트 통과
- [ ] 문서화 완료

---

## 참고 자료

### 공식 문서
- Django: https://docs.djangoproject.com/
- Django REST Framework: https://www.django-rest-framework.org/
- python-dateutil: https://dateutil.readthedocs.io/
- Firebase Admin SDK: https://firebase.google.com/docs/admin/setup

### 질병관리청 자료
- 예방접종도우미: https://nip.kdca.go.kr
- 표준 예방접종 일정표: https://nip.kdca.go.kr/irgd/reference.do?MnLv1=3&MnLv2=1

### 협업 도구
- GitHub Projects: https://docs.github.com/en/issues/planning-and-tracking-with-projects
- Notion: https://www.notion.so/
- Figma (API 디자인): https://www.figma.com/

---

**마지막 업데이트**: 2024년 11월 20일
**작성자**: Mamy 프로젝트 - 예방접종 관리 팀
