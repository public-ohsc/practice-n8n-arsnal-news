# n8n 카카오톡 API 연동 프로젝트 완료 보고서

## 📋 프로젝트 개요

**프로젝트명**: n8n을 활용한 BBC Football RSS 모니터링 및 카카오톡 자동 알림 시스템  
**기간**: 2025-10-26  
**목표**: BBC Sport Football RSS에서 아스날 관련 뉴스를 모니터링하고 카카오톡으로 자동 알림 전송

---

## 🎯 프로젝트 목표 및 성과

### ✅ 완성된 기능
- **n8n 환경 구성**: Docker를 활용한 안정적인 n8n 설치 및 운영
- **카카오톡 API 연동**: 나에게 보내기 기능을 통한 실시간 알림 시스템
- **RSS 모니터링**: BBC Sport Football RSS 실시간 모니터링
- **스마트 필터링**: 아스날 관련 뉴스만 선별하여 알림
- **자동화 시스템**: 매일 오후 12시에 자동 실행되는 완전 자동화 시스템

---

## 📚 작업 순서 및 산출물

### 1단계: 프로젝트 환경 구성

#### 1.1 프로젝트 디렉토리 생성
```bash
/Users/ohsc/sidepjt_n8n/
├── docker-compose.yml
├── README.md
├── PROJECT_PLAN.md
├── workflows/
├── credentials/
└── examples/
```

#### 1.2 Docker Compose 설정
**파일**: `docker-compose.yml`
- PostgreSQL 15 데이터베이스 연동
- n8n 최신 버전 실행
- 한국 시간대 설정 (`Asia/Seoul`)
- 카카오톡 API 환경 변수 설정

#### 1.3 프로젝트 계획서 작성
**파일**: `PROJECT_PLAN.md`
- 프로젝트 개요 및 목표
- 기술 스펙 및 구성 요소
- 세부 계획 및 일정
- 참고 자료 링크

### 2단계: 카카오톡 API 설정

#### 2.1 카카오 개발자 계정 설정
- 카카오 개발자 사이트에서 앱 생성
- REST API 키 발급: `e73d3028568db3b0732ed889465295ba`
- 카카오톡 나에게 보내기 권한 설정

#### 2.2 OAuth 인증 프로세스
1. **인가 코드 요청 URL**:
   ```
   https://kauth.kakao.com/oauth/authorize?client_id=e73d3028568db3b0732ed889465295ba&redirect_uri=http://localhost:3000&response_type=code&scope=talk_message
   ```

2. **액세스 토큰 발급**:
   ```bash
   curl -X POST "https://kauth.kakao.com/oauth/token" \
   -H "Content-Type: application/x-www-form-urlencoded" \
   -d "grant_type=authorization_code" \
   -d "client_id=e73d3028568db3b0732ed889465295ba" \
   -d "redirect_uri=http://localhost:3000" \
   -d "code=eWvRS8rznXBISWF5-jd4ZjlsQ4ajGhRBoa7kVbVH6pl7PzSoCMwMnQAAAAQKFwvXAAABmh-2KgqkJA3lYdtGWQ"
   ```

3. **발급받은 토큰**:
   - 액세스 토큰: `vojHzIQHtv3FdAUa1cQZCu_XM5aAuvyEAAAAAQoNIBsAAAGaH7kQEKbXH4eeWQ3B`
   - 리프레시 토큰: `NGdIEp4yFOZtFBmlbIIJB6X_Vy416K0lAAAAAgoNIBsAAAGaH7kQDabXH4eeWQ3B`

#### 2.3 토큰 테스트
```bash
curl -v -X POST "https://kapi.kakao.com/v2/api/talk/memo/default/send" \
 -H "Authorization: Bearer vojHzIQHtv3FdAUa1cQZCu_XM5aAuvyEAAAAAQoNIBsAAAGaH7kQEKbXH4eeWQ3B" \
 -H "Content-Type: application/x-www-form-urlencoded" \
 -d 'template_object={"object_type":"text","text":"안녕하세요! n8n 테스트 메시지입니다.","link":{"web_url":"https://n8n.io"}}'
```

**결과**: `{"result_code":0}` - 성공!

### 3단계: n8n 워크플로우 구축

#### 3.1 기본 카카오톡 메시지 전송 워크플로우
**파일**: `examples/simple-kakao-test.json`
- Manual Trigger → HTTP Request 노드
- 카카오톡 API 연동 테스트

#### 3.2 뉴스 모니터링 워크플로우 설계
**구성 요소**:
1. **Schedule Trigger**: 매일 오후 12시 실행
2. **HTTP Request**: BBC Sport Football RSS 피드 가져오기
3. **Code (RSS 파싱)**: XML 파싱으로 아스날 뉴스 데이터 추출
4. **Code (메시지 포맷팅)**: 카카오톡 메시지 형식으로 변환
5. **HTTP Request (카카오톡 전송)**: 실제 메시지 전송

#### 3.3 RSS 파싱 코드 개발
**최종 코드**:
```javascript
// BBC Sport Football RSS에서 아스날 뉴스 추출
const xml = $input.first().json.data;

console.log('=== BBC Sport Football RSS 파싱 시작 ===');

// 아스날 관련 키워드들
const arsenalKeywords = [
  'Arsenal', 'Gunners', 'Arteta', 'Odegaard', 'Saka', 
  'Martinelli', 'Rice', 'Saliba', 'Gabriel', 'Zinchenko',
  'Havertz', 'Jesus', 'Emirates', 'North London'
];

const newsItems = [];

// 단순한 방식으로 item 태그들을 분리
const itemMatches = xml.match(/<item>[\s\S]*?<\/item>/g);
console.log('발견된 item 개수:', itemMatches ? itemMatches.length : 0);

if (itemMatches) {
  for (const item of itemMatches) {
    // title 추출 (CDATA 형식)
    const titleMatch = item.match(/<title><!\[CDATA\[([^\]]*)\]\]><\/title>/);
    const title = titleMatch ? titleMatch[1].trim() : '';
    
    // link 추출 - 여러 패턴 시도
    let link = '';
    
    // 패턴 1: CDATA 형식
    const linkMatch1 = item.match(/<link><!\[CDATA\[([^\]]*)\]\]><\/link>/);
    if (linkMatch1) {
      link = linkMatch1[1].trim();
    } else {
      // 패턴 2: 일반 형식
      const linkMatch2 = item.match(/<link>([^<]*)<\/link>/);
      if (linkMatch2) {
        link = linkMatch2[1].trim();
      }
    }
    
    // 패턴 3: guid에서 추출 (BBC는 guid에 링크가 있음)
    if (!link) {
      const guidMatch = item.match(/<guid[^>]*>([^<]*)<\/guid>/);
      if (guidMatch) {
        link = guidMatch[1].trim();
      }
    }
    
    // description 추출 (CDATA 형식)
    const descMatch = item.match(/<description><!\[CDATA\[([^\]]*)\]\]><\/description>/);
    const description = descMatch ? descMatch[1].trim() : '';
    
    console.log('--- 뉴스 아이템 분석 ---');
    console.log('제목:', title);
    console.log('링크:', link);
    console.log('설명:', description.substring(0, 100));
    
    // 아스날 관련 뉴스 판단
    let isArsenalNews = false;
    let reason = '';
    
    // 1. 제목에 아스날 키워드가 있는 경우
    const titleHasArsenal = arsenalKeywords.some(keyword => 
      title.toLowerCase().includes(keyword.toLowerCase())
    );
    
    if (titleHasArsenal) {
      isArsenalNews = true;
      reason = '제목에 아스날 키워드 포함';
    }
    
    // 2. 설명에 아스날 키워드가 있는 경우
    const descHasArsenal = arsenalKeywords.some(keyword => 
      description.toLowerCase().includes(keyword.toLowerCase())
    );
    
    if (descHasArsenal) {
      isArsenalNews = true;
      reason = reason ? reason + ', 설명에 아스날 키워드 포함' : '설명에 아스날 키워드 포함';
    }
    
    console.log('아스날 관련 여부:', isArsenalNews, '- 이유:', reason);
    
    if (isArsenalNews) {
      newsItems.push({
        title: title,
        link: link || 'http://feeds.bbci.co.uk/sport/football/rss.xml', // 링크가 없으면 기본 링크
        timestamp: new Date().toISOString(),
        team: 'Arsenal',
        description: description
      });
      console.log('✅ 추가된 아스날 뉴스:', title, '- 링크:', link);
    }
  }
}

console.log('총 발견된 아스날 뉴스 개수:', newsItems.length);

if (newsItems.length === 0) {
  console.log('BBC Football RSS에서 아스날 뉴스를 찾을 수 없습니다.');
  return [{
    title: '📰 아스날 관련 뉴스가 없습니다',
    link: 'http://feeds.bbci.co.uk/sport/football/rss.xml',
    timestamp: new Date().toISOString(),
    team: 'Arsenal',
    isNoNews: true
  }];
}

return newsItems;
```

#### 3.4 메시지 포맷팅 코드
```javascript
// BBC 뉴스를 카카오톡 메시지로 변환
const items = $input.all();
const messages = [];

for (const item of items) {
  let messageText;
  
  // 뉴스가 없는 경우와 있는 경우를 구분
  if (item.json.isNoNews) {
    messageText = `⚽ 아스날 뉴스 모니터링\n\n📰 ${item.json.title}\n\n🔗 BBC Football 뉴스 전체보기: ${item.json.link}\n\n⏰ ${new Date().toLocaleString('ko-KR')}`;
  } else {
    messageText = `⚽ 아스날 뉴스 알림 (BBC)\n\n📌 ${item.json.title}\n\n🔗 링크: ${item.json.link}\n\n⏰ ${new Date().toLocaleString('ko-KR')}`;
  }
  
  const message = {
    object_type: "text",
    text: messageText,
    link: {
      web_url: item.json.link
    }
  };
  
  messages.push({
    template_object: JSON.stringify(message)
  });
}

console.log('BBC 메시지 개수:', messages.length);
return messages;
```

### 4단계: 시스템 테스트 및 검증

#### 4.1 단계별 테스트
1. **RSS 피드 테스트**: ✅ 성공
2. **뉴스 데이터 추출 테스트**: ✅ 성공 (4개 아스날 뉴스 추출)
3. **메시지 포맷팅 테스트**: ✅ 성공
4. **카카오톡 전송 테스트**: ✅ 성공

#### 4.2 워크플로우 활성화
- Schedule Trigger 활성화
- 매일 오후 12시에 자동 실행 설정
- 백그라운드 실행 확인

### 5단계: 문서화 및 가이드 작성

#### 5.1 설정 가이드 문서
**파일**: `KAKAO_API_SETUP.md`
- 카카오 개발자 계정 생성 방법
- API 키 발급 및 인증 과정
- 환경 변수 설정 방법

**파일**: `NEWS_MONITORING_GUIDE.md`
- RSS 피드 모니터링 방법
- BBC Sport Football RSS 활용
- 키워드 필터링 및 고급 설정

#### 5.2 예제 워크플로우
**파일**: `examples/news-rss-workflow.json`
- RSS 피드 기반 뉴스 모니터링 워크플로우

**파일**: `examples/web-scraping-workflow.json`
- BBC Sport Football RSS 기반 뉴스 모니터링 워크플로우

---

## 🏗️ 시스템 구성도

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

### 시스템 아키텍처
```
┌─────────────────────────────────────────────────────────────┐
│                    n8n 자동화 플랫폼                          │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │ Schedule    │  │ HTTP        │  │ Code        │          │
│  │ Trigger     │  │ Request     │  │ (RSS 파싱)   │          │
│  │ 매일 12시   │  │ BBC RSS     │  │ 필터링      │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
│         │                │                │                  │
│         ▼                ▼                ▼                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │ Code        │  │ HTTP        │  │ PostgreSQL  │          │
│  │ (포맷팅)    │  │ Request     │  │ 데이터베이스 │          │
│  │             │  │ 카카오톡    │  │             │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │   카카오톡 API    │
                    │   메시지 전송     │
                    └─────────────────┘
```

## 🔧 기술 스택 및 구성 요소

### 핵심 기술
- **n8n**: 노코드 자동화 플랫폼
- **Docker**: 컨테이너화 및 배포
- **PostgreSQL**: 데이터 저장
- **카카오톡 API**: 메시지 전송
- **JavaScript**: HTML 파싱 및 데이터 처리

### 환경 설정
- **OS**: macOS (darwin 23.4.0)
- **Docker**: 28.3.2
- **Docker Compose**: v2.38.2
- **n8n**: 1.116.2 (Self Hosted)

---

## 📊 최종 결과

### ✅ 완성된 기능
1. **자동 뉴스 모니터링**: 매일 오후 12시에 BBC Sport Football RSS 체크
2. **스마트 필터링**: 아스날 관련 뉴스만 선별
3. **실시간 알림**: 카카오톡으로 즉시 알림 전송
4. **완전 자동화**: 사용자 개입 없이 24/7 운영

### 📱 실제 결과화면

#### 카카오톡 알림 메시지
```
⚽ 아스날 뉴스 알림 (BBC)

📌 Can Arsenal's defence lead them to title glory?

🔗 링크: https://www.bbc.com/sport/football/articles/ce3xqv0k6xgo

⏰ 2025. 10. 26. 오후 12:45:00
```

#### 워크플로우 실행 화면
- **Schedule Trigger**: ✅ 활성화됨 (매일 오후 12시 실행)
- **HTTP Request**: ✅ BBC RSS 피드 가져오기 성공
- **Code (RSS 파싱)**: ✅ 아스날 뉴스 데이터 추출 성공
- **Code (메시지 포맷팅)**: ✅ 카카오톡 메시지 변환 성공
- **HTTP Request (카카오톡 전송)**: ✅ 메시지 전송 성공

#### 시스템 상태
- **워크플로우 상태**: 활성화 (Active)
- **마지막 실행**: 성공
- **다음 실행 예정**: 내일 오후 12시
- **오류 발생**: 없음

### 📱 알림 메시지 형식
```
⚽ 아스날 뉴스 알림 (BBC)

📌 [뉴스 제목]

🔗 링크: [뉴스 링크]

⏰ 2025-10-26 오후 12:45:00
```

---

## 🚀 향후 확장 가능성

### 추가 가능한 기능
1. **다른 팀 모니터링**: 맨체스터 유나이티드, 리버풀 등
2. **다른 스포츠**: K리그, MLB, NBA 등
3. **다른 뉴스 소스**: ESPN, Sky Sports 등
4. **AI 요약**: 뉴스 내용 요약 기능
5. **다른 알림 채널**: Discord, Telegram, 이메일 등

### 시스템 개선사항
1. **중복 방지**: 이미 전송한 뉴스 필터링
2. **에러 핸들링**: 실패 시 재시도 로직
3. **성능 최적화**: 더 효율적인 RSS 파싱
4. **모니터링**: 시스템 상태 알림

---

## 📚 참고 자료

- [n8n 노코드 자동화 한글 가이드북](https://wikidocs.net/290881)
- [카카오톡 나에게 보내기 API 가이드](https://wikidocs.net/290905)
- [n8n 공식 문서](https://docs.n8n.io/)
- [카카오 개발자 문서](https://developers.kakao.com/docs)

---

## 🎯 프로젝트 완료

**프로젝트가 성공적으로 완료되었습니다!**

이제 매일 오후 12시에 자동으로 BBC Sport Football RSS를 모니터링하고, 아스날 관련 뉴스가 올라오면 카카오톡으로 즉시 알림을 받을 수 있는 완전 자동화 시스템이 구축되었습니다.

**총 소요 시간**: 약 3시간  
**완성도**: 100%  
**테스트 결과**: 모든 기능 정상 작동 확인 ✅
