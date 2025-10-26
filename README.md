# n8n BBC Football RSS 모니터링 및 카카오톡 알림 시스템

## 프로젝트 개요
n8n을 활용한 BBC Sport Football RSS 모니터링 시스템과 카카오톡 나에게 보내기 API를 연동한 아스날 뉴스 자동 알림 시스템

## 설치 및 실행

### 1. Docker Desktop 설치
- [Docker Desktop 다운로드](https://www.docker.com/products/docker-desktop/)
- 설치 후 Docker Desktop 실행

### 2. n8n 실행
```bash
# 프로젝트 디렉토리로 이동
cd /Users/ohsc/sidepjt_n8n

# Docker Compose로 n8n 실행
docker-compose up -d

# 로그 확인
docker-compose logs -f n8n
```

### 3. n8n 접속
- 브라우저에서 `http://localhost:5678` 접속
- 로그인 정보:
  - 사용자명: `admin`
  - 비밀번호: `Qwer1234`

## 카카오톡 API 설정

### 1. 카카오 개발자 계정 생성
- [카카오 개발자 사이트](https://developers.kakao.com/) 접속
- 애플리케이션 등록

### 2. REST API 키 확인
- 내 애플리케이션 > 앱 설정 > 앱 키에서 REST API 키 복사

### 3. 카카오톡 나에게 보내기 설정
- 내 애플리케이션 > 제품 설정 > 카카오톡 채널
- 카카오톡 채널 생성 및 연결
- 채널 ID 확인

## 워크플로우 구성

### 아스날 뉴스 모니터링 워크플로우
1. **Schedule Trigger**: 매일 오후 12시 자동 실행
2. **HTTP Request**: BBC Sport Football RSS 피드 가져오기
3. **Code (RSS 파싱)**: XML 파싱으로 아스날 뉴스 추출
4. **Code (메시지 포맷팅)**: 카카오톡 메시지 형식으로 변환
5. **HTTP Request (카카오톡 전송)**: 실제 메시지 전송

### 주요 기능
- **자동 모니터링**: 매일 오후 12시에 BBC Football RSS 체크
- **스마트 필터링**: 아스날 관련 뉴스만 선별
- **실시간 알림**: 카카오톡으로 즉시 알림 전송
- **완전 자동화**: 사용자 개입 없이 24/7 운영

## 디렉토리 구조
```
sidepjt_n8n/
├── docker-compose.yml          # Docker 설정
├── README.md                   # 프로젝트 문서
├── PROJECT_COMPLETION_REPORT.md # 프로젝트 완료 보고서
├── SYSTEM_DIAGRAMS.md         # 시스템 구성도 및 결과화면
├── KAKAO_API_SETUP.md         # 카카오톡 API 설정 가이드
├── workflows/                  # 워크플로우 백업
├── credentials/                # 인증 정보 백업
└── examples/                   # 예제 워크플로우
```

## 참고 자료
- [n8n 노코드 자동화 한글 가이드북](https://wikidocs.net/290881)
- [카카오톡 나에게 보내기 API 가이드](https://wikidocs.net/290905)
- [n8n 공식 문서](https://docs.n8n.io/)
- [카카오 개발자 문서](https://developers.kakao.com/docs)
