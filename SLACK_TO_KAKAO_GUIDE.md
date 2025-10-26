# Slack → 카카오톡 연동 설정 가이드

## 🎯 개요
Slack에서 메시지를 받으면 자동으로 카카오톡으로 전달하는 워크플로우 구축

## 📋 사전 준비사항

### 1. Slack App 생성
1. [Slack API 콘솔](https://api.slack.com/apps) 접속
2. **"Create New App"** → **"From scratch"** 선택
3. 앱 이름: `n8n-kakao-bridge` (또는 원하는 이름)
4. 워크스페이스 선택 후 **"Create App"**

### 2. Bot Token Scopes 설정
**OAuth & Permissions** 메뉴에서 다음 권한 추가:
- `chat:write` - 메시지 전송
- `channels:read` - 채널 정보 읽기
- `users:read` - 사용자 정보 읽기

### 3. Event Subscriptions 설정
1. **Event Subscriptions** 메뉴 클릭
2. **"Enable Events"** 토글 ON
3. **Subscribe to bot events**에 다음 이벤트 추가:
   - `message.channels` - 채널 메시지
   - `message.groups` - 그룹 메시지
   - `message.im` - 다이렉트 메시지

### 4. Webhook URL 설정
1. **Event Subscriptions**에서 **Request URL** 설정
2. n8n 워크플로우의 Webhook URL 입력:
   ```
   http://localhost:5678/webhook/slack-webhook
   ```
3. **"Verify"** 버튼 클릭하여 인증

## 🔧 n8n 워크플로우 설정

### 1. 워크플로우 가져오기
1. n8n에서 **"Import from File"** 선택
2. `slack-to-kakao-workflow.json` 파일 업로드

### 2. 환경 변수 확인
`docker-compose.yml`에 다음이 설정되어 있는지 확인:
```yaml
- KAKAO_ACCESS_TOKEN=your_access_token_here
```

### 3. 워크플로우 활성화
1. 워크플로우 편집 화면에서 **"Active"** 토글 ON
2. Webhook URL 복사: `http://localhost:5678/webhook/slack-webhook`

## 🚀 테스트 방법

### 1. Slack에서 테스트
1. 설정한 채널에서 메시지 전송
2. 카카오톡으로 알림 확인

### 2. 예상 카카오톡 메시지 형식
```
📱 Slack 알림

👤 사용자명
💬 메시지 내용

⏰ 2025-10-26 오후 6:00:00
```

## 🔧 고급 설정

### 1. 특정 채널만 필터링
**메시지 필터** 노드에 조건 추가:
```javascript
// 특정 채널만 처리
const channelId = $json.channel;
const allowedChannels = ['C1234567890', 'C0987654321']; // 허용할 채널 ID

if (!allowedChannels.includes(channelId)) {
  return false;
}
```

### 2. 키워드 필터링
```javascript
// 특정 키워드가 포함된 메시지만 처리
const messageText = $json.text.toLowerCase();
const keywords = ['urgent', 'important', '긴급', '중요'];

if (!keywords.some(keyword => messageText.includes(keyword))) {
  return false;
}
```

### 3. 사용자 필터링
```javascript
// 특정 사용자의 메시지만 처리
const userId = $json.user;
const allowedUsers = ['U1234567890', 'U0987654321'];

if (!allowedUsers.includes(userId)) {
  return false;
}
```

## 🛠️ 트러블슈팅

### 일반적인 문제들

1. **Webhook 인증 실패**
   - n8n 워크플로우가 활성화되어 있는지 확인
   - 포트 5678이 열려있는지 확인

2. **메시지가 전송되지 않음**
   - 카카오톡 액세스 토큰 만료 확인
   - Slack 이벤트 구독 설정 확인

3. **권한 오류**
   - Slack Bot Token Scopes 확인
   - 앱 재설치 필요할 수 있음

## 📚 참고 자료

- [Slack API 공식 문서](https://api.slack.com/)
- [카카오톡 메시지 API](https://developers.kakao.com/docs/latest/ko/kakaotalk-api/send-message)
- [n8n Webhook 노드 문서](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/)
