# Confluence OAuth API Test

Confluence OAuth 2.0 인증 및 REST API 테스트를 위한 Jupyter 노트북과 가이드입니다.

## 📋 개요

이 프로젝트는 Atlassian Confluence API에 OAuth 2.0 인증을 통해 접근하여 페이지 CRUD 작업을 수행하는 방법을 보여줍니다.

## 🛠️ 주요 기능

- **OAuth 2.0 인증**: Atlassian OAuth 앱을 통한 안전한 인증
- **페이지 CRUD**: 페이지 생성, 읽기, 업데이트, 삭제
- **환경변수 관리**: `.env` 파일을 통한 보안 설정
- **단계별 가이드**: Jupyter 노트북을 통한 상세한 테스트 과정

## 📁 파일 구조

```
├── confluence_oauth_test.ipynb    # OAuth 인증 및 API 테스트 노트북
├── confluence_api_test_guide.md   # API 사용법 가이드 문서
├── .env                          # 환경변수 설정 (Git에서 제외됨)
├── .gitignore                    # Git 무시 파일 설정
└── README.md                     # 프로젝트 설명서
```

## 🚀 시작하기

### 1. 사전 준비

1. [Atlassian Console](https://developer.atlassian.com/console/myapps/)에서 OAuth 앱 생성
2. 필요한 스코프 설정:
   - `read:page:confluence`
   - `write:page:confluence`
   - `delete:page:confluence`
   - `read:content:confluence`
   - `write:content:confluence`
   - `read:content-details:confluence`
   - `read:space:confluence`
   - `read:me`
   - `offline_access`

### 2. 환경 설정

`.env` 파일을 생성하고 다음 내용을 추가하세요:

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
CONFLUENCE_RESOURCE_NAME=your_resource_name
```

### 3. 패키지 설치

```bash
pip install requests python-dotenv
```

### 4. 테스트 실행

1. `confluence_oauth_test.ipynb` 노트북을 열어주세요
2. 단계별로 셀을 실행하여 OAuth 인증 및 API 테스트를 진행하세요

## 📚 문서

- **[API 테스트 가이드](confluence_api_test_guide.md)**: OAuth 인증 과정과 REST API 사용법
- **[Jupyter 노트북](confluence_oauth_test.ipynb)**: 단계별 테스트 과정

## 🔒 보안

- `.env` 파일은 Git에서 제외되어 개인정보가 보호됩니다
- 모든 민감한 정보는 환경변수로 관리됩니다
- OAuth 토큰은 로컬 변수로만 관리됩니다

## 🎯 사용 사례

- **문서 자동 생성**: 프로젝트 완료 시 자동으로 회고 문서 생성
- **문서 동기화**: 외부 시스템과 Confluence 문서 동기화
- **대량 문서 처리**: 여러 페이지를 한 번에 생성/수정

## 📖 참고 자료

- [Atlassian OAuth 2.0 가이드](https://developer.atlassian.com/cloud/oauth-2-0/)
- [Confluence REST API v2 문서](https://developer.atlassian.com/cloud/confluence/rest/v2/)
- [Confluence API 스코프 가이드](https://developer.atlassian.com/cloud/confluence/scopes/)

## 📄 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다.

## 🤝 기여하기

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

**주의**: 이 프로젝트는 교육 및 테스트 목적으로 제작되었습니다. 프로덕션 환경에서 사용하기 전에 보안 검토를 수행해주세요.