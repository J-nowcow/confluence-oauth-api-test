# Confluence API OAuth 인증 및 REST API 사용 가이드

## 📋 개요
이 가이드는 Atlassian Confluence API에 OAuth 2.0 인증을 통해 접근하여 페이지 CRUD 작업을 수행하는 방법을 설명합니다.

## 🛠️ 사전 준비

### 1. OAuth 앱 설정
1. [Atlassian Console](https://developer.atlassian.com/console/myapps/)에 접속
2. 새 OAuth 앱 생성
3. **필수 스코프** 활성화:
   - `read:page:confluence` - 페이지 읽기
   - `write:page:confluence` - 페이지 생성/수정
   - `delete:page:confluence` - 페이지 삭제
   - `read:content:confluence` - 콘텐츠 읽기
   - `write:content:confluence` - 콘텐츠 수정
   - `read:content-details:confluence` - 콘텐츠 상세 정보
   - `read:space:confluence` - 스페이스 읽기
   - `read:me` - 사용자 정보
   - `offline_access` - 오프라인 접근

### 2. 환경 변수 설정
`.env` 파일 생성:
```env
# OAuth 설정
CLIENT_ID=your_client_id_here
CLIENT_SECRET=your_client_secret_here
ATLASSIAN_SITE=https://your-site.atlassian.net

# Confluence 설정
CLOUD_ID=your_cloud_id_here
SPACE_ID=your_space_id_here
SPACE_KEY=YourSpaceKey
PARENT_ID=your_parent_page_id_here
```

## 🔐 OAuth 2.0 인증 과정

### 1. OAuth 인증 URL 생성
**URL**: `https://auth.atlassian.com/authorize`

**필수 파라미터**:
- `audience`: `api.atlassian.com`
- `client_id`: OAuth 앱의 Client ID
- `scope`: 요청할 권한 스코프들 (공백으로 구분)
- `redirect_uri`: 콜백 URL (예: `http://localhost:8000/callback`)
- `state`: 보안을 위한 랜덤 문자열
- `response_type`: `code`
- `prompt`: `consent` (권한 재확인)

**예시**:
```
https://auth.atlassian.com/authorize?audience=api.atlassian.com&client_id=YOUR_CLIENT_ID&scope=read:page:confluence+write:page:confluence&redirect_uri=http://localhost:8000/callback&state=random_string&response_type=code&prompt=consent
```

### 2. 콜백 서버 설정
**포트**: `8000` (기본값)
**경로**: `/callback`
**역할**: 인증 코드(`code`) 파라미터 수신

### 3. 액세스 토큰 발급
**URL**: `https://auth.atlassian.com/oauth/token`
**Method**: `POST`
**Content-Type**: `application/x-www-form-urlencoded`

**필수 파라미터**:
- `grant_type`: `authorization_code`
- `client_id`: OAuth 앱의 Client ID
- `client_secret`: OAuth 앱의 Client Secret
- `code`: 콜백에서 받은 인증 코드
- `redirect_uri`: 콜백 URL

**응답**:
```json
{
  "access_token": "eyJraWQ...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "read:page:confluence write:page:confluence ..."
}
```

## 🌐 Confluence REST API 사용법

### 1. 기본 인증 헤더
```http
Authorization: Bearer {access_token}
Content-Type: application/json
Accept: application/json
```

### 2. Cloud ID 조회
**URL**: `https://api.atlassian.com/oauth/token/accessible-resources`
**Method**: `GET`
**목적**: Confluence 사이트의 Cloud ID 획득

### 3. 사용자 정보 조회
**URL**: `https://api.atlassian.com/me`
**Method**: `GET`
**목적**: 현재 사용자 정보 확인

### 4. 스페이스 조회
**URL**: `https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/spaces`
**Method**: `GET`
**목적**: 접근 가능한 스페이스 목록 조회

**특정 스페이스 조회**:
```
https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/spaces?keys={space_key}
```

## 📄 페이지 CRUD 작업

### 1. 페이지 생성 (CREATE)
**URL**: `https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/pages?body-format=storage`
**Method**: `POST`

**필수 파라미터**:
```json
{
  "spaceId": "스페이스_ID",
  "title": "페이지_제목",
  "body": {
    "representation": "storage",
    "value": "<p>페이지 내용 (HTML)</p>"
  }
}
```

**선택 파라미터**:
- `parentId`: 부모 페이지/폴더 ID

### 2. 페이지 읽기 (READ)
**URL**: `https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/pages/{page_id}`
**Method**: `GET`

**쿼리 파라미터**:
- `body-format`: `storage` (HTML 형식) 또는 `atlas_doc_format`

### 3. 페이지 업데이트 (UPDATE)
**URL**: `https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/pages/{page_id}`
**Method**: `PUT`

**필수 파라미터**:
```json
{
  "id": "페이지_ID",
  "status": "current",
  "version": {
    "number": 현재버전 + 1
  },
  "title": "페이지_제목",
  "body": {
    "representation": "storage",
    "value": "<p>새로운 내용</p>"
  }
}
```

### 4. 페이지 삭제 (DELETE)
**URL**: `https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/pages/{page_id}`
**Method**: `DELETE`

## 🔧 주요 API 엔드포인트 정리

| 작업 | Method | URL | 설명 |
|------|--------|-----|------|
| **인증** | GET | `https://auth.atlassian.com/authorize` | OAuth 인증 URL |
| **토큰** | POST | `https://auth.atlassian.com/oauth/token` | 액세스 토큰 발급 |
| **사용자** | GET | `https://api.atlassian.com/me` | 사용자 정보 |
| **리소스** | GET | `https://api.atlassian.com/oauth/token/accessible-resources` | Cloud ID 조회 |
| **스페이스** | GET | `https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/spaces` | 스페이스 목록 |
| **페이지 생성** | POST | `https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/pages` | 새 페이지 생성 |
| **페이지 읽기** | GET | `https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/pages/{page_id}` | 페이지 내용 조회 |
| **페이지 수정** | PUT | `https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/pages/{page_id}` | 페이지 내용 수정 |
| **페이지 삭제** | DELETE | `https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/pages/{page_id}` | 페이지 삭제 |

## ⚠️ 주요 오류 및 해결방법

### 1. 인증 관련 오류
- **401 Unauthorized**: OAuth 스코프 부족 또는 토큰 만료
- **403 Forbidden**: Confluence 권한 부족
- **해결**: 스코프 추가, 토큰 재발급, Confluence 권한 확인

### 2. API 관련 오류
- **404 Not Found**: 잘못된 페이지 ID 또는 스페이스 ID
- **400 Bad Request**: 필수 필드 누락 (id, status 등)
- **410 Gone**: deprecated된 v1 API 사용
- **해결**: ID 값 확인, v2 API 사용, 요청 데이터 구조 확인

### 3. SSL 인증서 문제
- **SSLCertVerificationError**: 인증서 검증 실패
- **해결**: 개발 환경에서는 `verify=False` 사용

## 📋 필수 정보 수집 방법

### 1. Cloud ID 찾기
```http
GET https://api.atlassian.com/oauth/token/accessible-resources
Authorization: Bearer {access_token}
```

### 2. 스페이스 ID 찾기
```http
GET https://api.atlassian.com/ex/confluence/{cloud_id}/wiki/api/v2/spaces?keys={space_key}
Authorization: Bearer {access_token}
```

### 3. 부모 페이지 ID 찾기
- Confluence 웹에서 해당 폴더/페이지 URL 확인
- URL에서 페이지 ID 추출: `/pages/{page_id}`

## 🎯 실제 사용 시나리오

### 1. 문서 자동 생성
- 프로젝트 완료 시 자동으로 회고 문서 생성
- API를 통해 일관된 형식의 문서 작성

### 2. 문서 동기화
- 외부 시스템과 Confluence 문서 동기화
- 정기적인 업데이트 자동화

### 3. 대량 문서 처리
- 여러 페이지를 한 번에 생성/수정
- 템플릿 기반 문서 생성

## 📚 참고 자료

- [Atlassian OAuth 2.0 가이드](https://developer.atlassian.com/cloud/oauth-2-0/)
- [Confluence REST API v2 문서](https://developer.atlassian.com/cloud/confluence/rest/v2/)
- [Confluence API 스코프 가이드](https://developer.atlassian.com/cloud/confluence/scopes/)
