# n8n 환경 변수 설정

## 🔧 환경 변수 추가 방법

### 1. docker-compose.yml 파일 수정
다음 환경 변수들을 추가하세요:

```yaml
environment:
  # 기존 설정들...
  - GENERIC_TIMEZONE=Asia/Seoul
  
  # 카카오톡 API 설정
  - KAKAO_REST_API_KEY=여기에_REST_API_키_입력
  - KAKAO_CHANNEL_ID=여기에_채널_ID_입력
```

### 2. 실제 설정 예시
```yaml
environment:
  - N8N_BASIC_AUTH_ACTIVE=true
  - N8N_BASIC_AUTH_USER=admin
  - N8N_BASIC_AUTH_PASSWORD=Qwer1234
  - N8N_HOST=localhost
  - WEBHOOK_TUNNEL_URL=http://localhost:5678
  - NODE_TLS_REJECT_UNAUTHORIZED=0
  - GENERIC_TIMEZONE=Asia/Seoul
  
  # 카카오톡 API 설정
  - KAKAO_REST_API_KEY=1234567890abcdef1234567890abcdef
  - KAKAO_CHANNEL_ID=@1234567890
  
  # PostgreSQL 데이터베이스 설정
  - DB_TYPE=postgresdb
  - DB_POSTGRESDB_HOST=postgres
  - DB_POSTGRESDB_PORT=5432
  - DB_POSTGRESDB_DATABASE=n8n
  - DB_POSTGRESDB_USER=n8n
  - DB_POSTGRESDB_PASSWORD=Qwer1234
```

## 🔄 서비스 재시작

환경 변수 추가 후 다음 명령어로 서비스를 재시작하세요:

```bash
# 서비스 중지
docker-compose down

# 서비스 재시작
docker-compose up -d

# 로그 확인
docker-compose logs -f n8n
```

## 📝 환경 변수 설명

| 변수명 | 설명 | 예시 |
|--------|------|------|
| `KAKAO_REST_API_KEY` | 카카오 개발자 앱의 REST API 키 | `1234567890abcdef...` |
| `KAKAO_CHANNEL_ID` | 카카오톡 채널 ID (@ 포함) | `@1234567890` |

## ⚠️ 보안 주의사항

- 환경 변수에 실제 API 키를 입력할 때는 따옴표 없이 입력하세요
- API 키는 절대 공개하지 마세요
- 프로덕션 환경에서는 별도의 환경 변수 파일(.env) 사용을 권장합니다
