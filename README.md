# 안녕하세요, 권순현입니다 👋

> 헬스케어 도메인 백엔드 개발자 | FastAPI · Python · MySQL

식품영양학 전공 배경을 가진 백엔드 개발자입니다.  
헬스케어 서비스에서 **도메인 지식 + 백엔드 기술**을 연결하는 것에 관심이 있습니다.

---

## 🛠 기술 스택

**Backend**  
![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat&logo=redis&logoColor=white)
![Celery](https://img.shields.io/badge/Celery-37814A?style=flat)

**Infra**  
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![AWS EC2](https://img.shields.io/badge/AWS_EC2-FF9900?style=flat&logo=amazonaws&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat&logo=githubactions&logoColor=white)

**AI / 외부 API 연동**  
![OpenAI](https://img.shields.io/badge/OpenAI-412991?style=flat&logo=openai&logoColor=white)
![Google Maps](https://img.shields.io/badge/Google_Maps_API-4285F4?style=flat&logo=googlemaps&logoColor=white)

---

## 🚀 프로젝트

### MediGuide — AI 기반 헬스케어 관리 서비스
> 만성·자가면역질환자를 위한 헬스케어 앱 (6주, 3인 팀)

🔗 [팀 레포](https://github.com/AI-HealthCare-03/AH_03_07) · [배포 URL](https://mediguide7.duckdns.org)

**기술 스택**: FastAPI · Tortoise ORM · MySQL · Celery · Redis · Docker · Next.js 15  
**담당**: 일반 모드 백엔드 전체 + 프론트엔드 일부 (8개 기능)

---

#### 담당 기능 상세 (PR 링크 포함)

**1. 식이정보 시스템** · [#156](https://github.com/AI-HealthCare-03/AH_03_07/pull/156)
- 13개 질환별 추천·제한 음식 및 영양학적 근거 제공
- 식품영양학 전공 지식 직접 활용 — 나트륨·퓨린 함량, GI지수 기반 분류 기준 설계
- 936건 시드 데이터 직접 설계 (13개 질환 × 6개 식품 카테고리 × 추천/제한)
- 의료법 §27 준수: 섭취량 수치·식단 처방 미제공, 면책 문구 필수 포함

**2. OCR 문서관리** · [#174](https://github.com/AI-HealthCare-03/AH_03_07/pull/174)
- 처방전·검사결과 사진 업로드 → CLOVA OCR 텍스트 추출 → OpenAI GPT 구조화
- Celery + Redis 비동기 처리 (PENDING → PROCESSING → COMPLETED)
- 프론트 3초 interval polling으로 완료 감지

**3. 응급카드 · SOS** · [#31](https://github.com/AI-HealthCare-03/AH_03_07/pull/31) · [#83](https://github.com/AI-HealthCare-03/AH_03_07/pull/83)
- 응급 상황 시 구급대원이 환자 정보 즉시 확인
- 혈액형 / 기저질환 / 복용약물 / 알레르기 / 보호자 최대 3인 관리
- `POST /emergency/trigger` → 보호자 알림 생성 + AuditLog 기록 (법적 증거 보존)

**4. 건강수치 기록** · [#32](https://github.com/AI-HealthCare-03/AH_03_07/pull/32) · [#183](https://github.com/AI-HealthCare-03/AH_03_07/pull/183)
- 혈압·혈당·체중 매일 기록 및 추이 시각화
- 혈압: 수축기/이완기 분리 저장 → "120/80 mmHg" 합산 표시
- Decimal 타입 변환 안전장치 (InvalidOperation 방어)

**5. 건강일기** · [#30](https://github.com/AI-HealthCare-03/AH_03_07/pull/30)
- 매일 컨디션·증상·복약체크 기록
- 아픈 부위 9종 / 느낌 5종 복수 선택, 복약(아침·점심·저녁) JSON 저장
- WeasyPrint + Jinja2로 진료용 PDF 생성

**6. 약국 찾기** · [#180](https://github.com/AI-HealthCare-03/AH_03_07/pull/180)
- Google Maps & Places API로 현재 위치 기반 주변 약국 검색
- 마커 표시, 영업시간·전화번호·거리 표시, 길찾기 연동

**7. 복약 알림** · [#97](https://github.com/AI-HealthCare-03/AH_03_07/pull/97)
- Celery Beat으로 처방 종료일 D-7·D-3·D-1 자동 알림
- 직접 등록 또는 OCR 처방전 → LLM 자동 구조화 등록

**8. 약품 인식** · [#179](https://github.com/AI-HealthCare-03/AH_03_07/pull/179)
- OpenAI Vision API로 약품 이미지 → 약품명 자동 인식

---

#### 🐛 디버깅 프로세스

오류 유형에 따라 일정한 패턴으로 접근했습니다.

**422 에러 (데이터 형식 불일치)**
```
1. docker logs fastapi --tail 20  →  어떤 요청이 실패했는지 확인
2. grep -n "필드명" app/models/...  →  백엔드 DTO/모델의 필드명·타입 확인
3. 프론트가 보내는 값과 비교  →  불일치 지점 특정
4. 주요 원인: 한글↔영문 enum 불일치, 필드명 오타, int↔string 타입 차이
```

**500 에러 (서버 내부 오류)**
```
1. docker logs fastapi --tail 20  →  스택 트레이스 확인
2. DB 컬럼 누락 여부 확인
   → 컬럼 없으면 ALTER TABLE ... ADD COLUMN ... 직접 실행
3. grep -rn "필드명" app/  →  model·DTO·service·repository 중 누락 레이어 추적
4. 전 레이어 동시 수정 후 재기동
```

**공통 원칙**
- 에러 코드로 원인 범위 먼저 좁히기 (422=데이터형식, 404/405=라우팅, 500=DB·서버)
- `grep -rn "키워드" app/` 으로 코드 전체에서 해당 필드 위치 추적
- 로컬에서 재현 → 수정 → PR 올리고 배포 반영

---

#### 🔥 주요 트러블슈팅

**1. FormData 업로드 시 빈 객체로 전송** · [#174](https://github.com/AI-HealthCare-03/AH_03_07/pull/174)
- 원인: 공통 `apiFetch`가 모든 body를 `JSON.stringify()`로 직렬화
- 해결: `FormData` 타입 감지 → `Content-Type` 미설정 + body 그대로 전달

**2. OCR 태스크 NotImplementedError** · [#174](https://github.com/AI-HealthCare-03/AH_03_07/pull/174)
- 원인: `process_ocr_task` 함수가 구현 없이 `raise NotImplementedError` 상태
- 해결: CLOVA OCR API + OpenAI GPT 구조화 파이프라인 직접 구현

**3. Docker 소켓 연결 끊김 (Mac 반복 발생)**
- 원인: Docker Desktop 재시작 시 `/var/run/docker.sock` symlink 끊김
- 해결: `sudo ln -sf ~/.docker/run/docker.sock /var/run/docker.sock`

**4. aerich 마이그레이션 MODELS_STATE 오류**
- 원인: 마이그레이션 파일 중복 + MODELS_STATE 불일치
- 해결: 중복 파일 삭제 후 aerich 재생성으로 phantom MODIFY 해결

**5. 발표 당일 422 에러** · [#214](https://github.com/AI-HealthCare-03/AH_03_07/pull/214)
- 원인: 프론트에서 `document_type`을 한글("검사결과")로 전송, 백엔드는 영문 enum("lab_result") 기대
- 해결: 위 디버깅 패턴으로 원인 추적 → 영문 enum 값으로 통일

**6. pytest PermissionError (CI 전체 실패)**
- 원인: `/app/static` 하드코딩 경로 → GitHub Actions runner 권한 없음으로 37개 테스트 전부 실패
- 해결: `STATIC_ROOT` 환경변수로 교체, CI는 상대경로 `"static"` 사용

---

## 📊 GitHub Stats

![tnsgus375's GitHub stats](https://github-readme-stats.vercel.app/api?username=tnsgus375&show_icons=true&theme=default&hide_border=true)

---

## 📫 연락처

- GitHub: [@tnsgus375](https://github.com/tnsgus375)
