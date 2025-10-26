# ⚽ n8n BBC Football RSS 모니터링 및 카카오톡 알림 시스템

[![n8n](https://img.shields.io/badge/n8n-1.116.2-blue.svg)](https://n8n.io/)
[![Docker](https://img.shields.io/badge/Docker-28.3.2-blue.svg)](https://www.docker.com/)
[![KakaoTalk API](https://img.shields.io/badge/KakaoTalk-API-yellow.svg)](https://developers.kakao.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> BBC Sport Football RSS를 활용한 아스날 뉴스 자동 모니터링 및 카카오톡 알림 시스템

## 📋 프로젝트 개요

이 프로젝트는 n8n을 활용하여 BBC Sport Football RSS를 모니터링하고, 아스날 관련 뉴스가 올라오면 카카오톡으로 자동 알림을 보내는 완전 자동화 시스템입니다.

### ✨ 주요 기능

- 🔄 **자동 모니터링**: 매일 오후 12시에 BBC Football RSS 체크
- 🎯 **스마트 필터링**: 아스날 관련 뉴스만 선별
- 📱 **실시간 알림**: 카카오톡으로 즉시 알림 전송
- 🤖 **완전 자동화**: 사용자 개입 없이 24/7 운영
- 🐳 **Docker 기반**: 안정적인 컨테이너 환경
- 📊 **상세 로깅**: 각 단계별 디버깅 정보 제공

## 🚀 빠른 시작

### 1. 저장소 클론
```bash
git clone https://github.com/public-ohsc/practice-n8n-arsnal-news.git
cd practice-n8n-arsnal-news
```

### 2. Docker Desktop 설치
- [Docker Desktop 다운로드](https://www.docker.com/products/docker-desktop/)
- 설치 후 Docker Desktop 실행

### 3. n8n 실행
```bash
# Docker Compose로 n8n 실행
docker-compose up -d

# 로그 확인
docker-compose logs -f n8n
```

### 4. n8n 접속
- 브라우저에서 `http://localhost:5678` 접속
- 로그인 정보:
  - 사용자명: `admin`
  - 비밀번호: `Qwer1234`

## 📱 카카오톡 API 설정

### 1. 카카오 개발자 계정 생성
- [카카오 개발자 사이트](https://developers.kakao.com/) 접속
- 애플리케이션 등록

### 2. REST API 키 확인
- 내 애플리케이션 > 앱 설정 > 앱 키에서 REST API 키 복사

### 3. 카카오톡 나에게 보내기 설정
- 내 애플리케이션 > 제품 설정 > 카카오톡 채널
- 카카오톡 채널 생성 및 연결
- 채널 ID 확인

### 4. 환경 변수 설정
`docker-compose.yml` 파일에서 다음 값들을 설정하세요:
```yaml
environment:
  - KAKAO_REST_API_KEY=your_rest_api_key
  - KAKAO_ACCESS_TOKEN=your_access_token
  - KAKAO_REFRESH_TOKEN=your_refresh_token
```

자세한 설정 방법은 [KAKAO_API_SETUP.md](KAKAO_API_SETUP.md)를 참고하세요.

## 🔧 워크플로우 구성

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

### 워크플로우 다이어그램
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Schedule       │    │  HTTP Request   │    │  Code in        │
│  Trigger        │───▶│  (BBC RSS)      │───▶│  JavaScript     │
│  (매일 12시)     │    │  GET 요청        │    │  (RSS 파싱)      │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                                         │
┌─────────────────┐    ┌─────────────────┐             │
│  HTTP Request   │◀───│  Code in        │◀────────────┘
│  (카카오톡 전송) │    │  JavaScript1    │
│  POST 요청       │    │  (메시지 포맷팅) │
└─────────────────┘    └─────────────────┘
```

## 📁 프로젝트 구조
```
practice-n8n-arsnal-news/
├── docker-compose.yml              # Docker 설정
├── README.md                       # 프로젝트 문서
├── PROJECT_COMPLETION_REPORT.md    # 프로젝트 완료 보고서
├── SYSTEM_DIAGRAMS.md              # 시스템 구성도 및 결과화면
├── KAKAO_API_SETUP.md             # 카카오톡 API 설정 가이드
├── NEWS_MONITORING_GUIDE.md       # 뉴스 모니터링 가이드
├── workflows/                      # 워크플로우 백업
├── credentials/                    # 인증 정보 백업
└── examples/                       # 예제 워크플로우
    ├── news-rss-workflow.json      # BBC RSS 기반 뉴스 모니터링
    ├── simple-kakao-test.json      # 카카오톡 기본 테스트
    └── ...
```

## 📚 참고 자료
- [n8n 노코드 자동화 한글 가이드북](https://wikidocs.net/290881)
- [카카오톡 나에게 보내기 API 가이드](https://wikidocs.net/290905)
- [n8n 공식 문서](https://docs.n8n.io/)
- [카카오 개발자 문서](https://developers.kakao.com/docs)

## 🤝 기여하기

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다. 자세한 내용은 `LICENSE` 파일을 참고하세요.

## 📞 문의

프로젝트에 대한 문의사항이 있으시면 [Issues](https://github.com/public-ohsc/practice-n8n-arsnal-news/issues)를 통해 연락해주세요.

---

⭐ 이 프로젝트가 도움이 되었다면 Star를 눌러주세요!
