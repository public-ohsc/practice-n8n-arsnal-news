# 웹사이트 뉴스 모니터링 → 카카오톡 알림 시스템

## 🎯 개요
특정 웹사이트에 새로운 뉴스가 올라오면 자동으로 카카오톡으로 링크와 함께 알림을 보내는 시스템

## 📋 지원하는 방법들

### 1. RSS 피드 모니터링 (추천)
- **장점**: 안정적, API 기반, 빠름
- **단점**: RSS 피드가 있어야 함
- **적합한 사이트**: CNN, BBC, 뉴욕타임스 등

### 2. 웹 스크래핑
- **장점**: 모든 웹사이트 가능
- **단점**: 사이트 구조 변경 시 수정 필요
- **적합한 사이트**: 네이버 뉴스, 다음 뉴스 등

## 🚀 방법 1: RSS 피드 모니터링

### 지원하는 RSS 피드들

#### 해외 뉴스
- **CNN**: `https://rss.cnn.com/rss/edition.rss`
- **BBC**: `http://feeds.bbci.co.uk/news/rss.xml`
- **Reuters**: `https://feeds.reuters.com/reuters/topNews`
- **AP News**: `https://feeds.apnews.com/rss/ap/topnews`

#### 국내 뉴스
- **연합뉴스**: `https://rss.yna.co.kr/rss/economy.xml`
- **뉴스1**: `https://news1.kr/rss/`
- **YTN**: `https://www.ytn.co.kr/rss/news.xml`

### 설정 방법

1. **n8n에서 워크플로우 생성**
2. **RSS Feed Read 노드 추가**
3. **RSS URL 입력**
4. **스케줄 트리거 설정** (10분마다 체크)
5. **카카오톡 전송 노드 연결**

## 🔧 방법 2: 웹 스크래핑

### 지원하는 사이트들

#### 국내 사이트
- **네이버 뉴스**: `https://news.naver.com/main/main.naver?mode=LSD&mid=shm&sid1=100`
- **다음 뉴스**: `https://news.daum.net/breakingnews`
- **조선일보**: `https://www.chosun.com/`
- **중앙일보**: `https://www.joongang.co.kr/`

### 설정 방법

1. **HTTP Request 노드로 웹페이지 가져오기**
2. **Code 노드로 HTML 파싱**
3. **뉴스 제목과 링크 추출**
4. **카카오톡 메시지로 포맷팅**

## 📱 예상 카카오톡 메시지 형식

```
📰 새로운 뉴스 알림

📌 뉴스 제목
📝 뉴스 요약 (100자)

🔗 링크: https://news.example.com/article

⏰ 2025-10-26 오후 7:00:00
```

## ⚙️ 고급 설정

### 1. 키워드 필터링

특정 키워드가 포함된 뉴스만 알림:

```javascript
// Code 노드에서 키워드 필터링
const keywords = ['코로나', '경제', '정치', '기술'];
const title = item.json.title.toLowerCase();

if (!keywords.some(keyword => title.includes(keyword))) {
  return null; // 필터링
}
```

### 2. 중복 방지

이미 전송한 뉴스는 제외:

```javascript
// 이전에 전송한 뉴스 ID 저장
const sentNewsIds = $('Set').first().json.sentIds || [];
const currentNewsId = item.json.guid;

if (sentNewsIds.includes(currentNewsId)) {
  return null; // 이미 전송한 뉴스
}
```

### 3. 여러 사이트 모니터링

여러 RSS 피드를 동시에 모니터링:

```javascript
// 여러 RSS 피드 URL
const rssFeeds = [
  'https://rss.cnn.com/rss/edition.rss',
  'https://feeds.bbci.co.uk/news/rss.xml',
  'https://rss.yna.co.kr/rss/economy.xml'
];
```

## 🛠️ 트러블슈팅

### 일반적인 문제들

1. **RSS 피드 접근 불가**
   - URL 확인
   - CORS 정책 확인
   - 프록시 사용 고려

2. **웹 스크래핑 실패**
   - 사이트 구조 변경 확인
   - User-Agent 헤더 추가
   - 요청 간격 조절

3. **중복 알림**
   - 중복 방지 로직 추가
   - 데이터베이스 사용 고려

## 📚 참고 자료

- [RSS 피드 목록](https://rss.com/blog/rss-feeds/)
- [웹 스크래핑 가이드](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.code/)
- [카카오톡 메시지 API](https://developers.kakao.com/docs/latest/ko/kakaotalk-api/send-message)

## 🎯 추천 설정

### 초보자용 (RSS)
1. CNN RSS 피드 사용
2. 10분마다 체크
3. 최신 3개 뉴스만 전송

### 고급 사용자용 (웹 스크래핑)
1. 네이버 뉴스 스크래핑
2. 키워드 필터링 적용
3. 중복 방지 로직 추가
