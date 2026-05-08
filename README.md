# Vuln Trade

Vuln Trade는 보안 교육과 취약점 진단 실습을 위해 만든 가상 금융 거래 플랫폼입니다. Flask 기반 웹 애플리케이션에서 모의 주식 거래, 지갑 이체, 커뮤니티, 파일 업로드, 관리자 기능을 제공합니다.

이 프로젝트는 의도적으로 취약한 구현을 포함합니다. 운영 환경에 배포하거나 실제 개인정보, 금융정보, 비밀값을 넣어 사용하지 마세요.

## 주요 기능

- 회원가입, 로그인, 로그아웃, 마이페이지
- 모의 주식 목록, 상세 차트, 매수/매도, 포트폴리오, 거래 내역
- 사용자 간 지갑 이체와 이체 내역
- 커뮤니티 게시글, 댓글, 파일 업로드/다운로드
- 관리자 대시보드, 사용자/거래/게시글 관리
- 스케줄러 컨테이너를 통한 주가 자동 갱신

## 기술 구성

- Web App: Flask, Jinja2, PyMySQL
- Database: MySQL 8.0
- Reverse Proxy: Nginx
- Scheduler: Python 주가 갱신 스크립트
- Runtime: Docker Compose

## 프로젝트 구조

```text
.
├── app/                  # Flask 애플리케이션
│   ├── run.py            # Flask 실행 진입점
│   ├── config.py         # 환경변수 기반 설정
│   └── src/
│       ├── routes/       # Blueprint 라우트
│       ├── services/     # 거래, 종목, 파일, 뉴스 로직
│       ├── templates/    # Jinja 템플릿
│       ├── static/       # CSS, JS, 업로드 파일
│       └── db.py         # DB 연결 및 런타임 시드 보정
├── db/init.sql           # MySQL 초기 스키마 및 시드 데이터
├── nginx/                # Nginx reverse proxy 설정
├── scheduler/            # 주가 자동 갱신 컨테이너
├── docker-compose.yml    # 전체 서비스 구성
└── .env.example          # 개발용 환경변수 예시
```

## 실행 방법

### 1. 환경변수 준비

처음 실행한다면 `.env.example`을 복사해 `.env`를 만듭니다.

```bash
cp .env.example .env
```

Windows PowerShell에서는 다음 명령을 사용할 수 있습니다.

```powershell
Copy-Item .env.example .env
```

### 2. Docker Compose로 실행

프로젝트 루트에서 실행합니다.

```bash
docker compose up --build
```

브라우저에서 다음 주소로 접속합니다.

```text
http://localhost
```

`docker-compose.yml` 기준으로 Nginx가 호스트의 `80` 포트를 사용합니다. 이미 80번 포트를 쓰는 서비스가 있다면 `docker-compose.yml`의 `web.ports` 값을 예를 들어 `"8080:80"`으로 바꾸고 `http://localhost:8080`으로 접속하세요.

### 3. 종료 및 초기화

컨테이너를 중지합니다.

```bash
docker compose down
```

DB 볼륨까지 삭제해 초기 시드 상태로 되돌립니다.

```bash
docker compose down -v
```

## 주요 URL

- `/`: 메인 대시보드
- `/auth/register`: 회원가입
- `/auth/login`: 사용자 로그인
- `/auth/admin_login`: 관리자 로그인
- `/stocks`: 종목 목록
- `/stocks/portfolio`: 포트폴리오
- `/stocks/history`: 주식 거래 내역
- `/wallet/transfer`: 지갑 이체
- `/wallet/history`: 이체 내역
- `/community`: 커뮤니티
- `/mypage`: 마이페이지
- `/vuln_admin`: 관리자 대시보드

## 시드 계정

초기 데이터에는 관리자 1명과 일반 사용자 200명이 생성됩니다.

- 관리자: `vuln@admin`
- 일반 사용자 예시: `user1`, `user2`, ..., `user200`
- 특수 일반 사용자: `user162` / `password1!`

일반 사용자 대부분의 기본 비밀번호는 다음 규칙으로 생성됩니다.

```text
VulnTrade!{번호}#{3자리 번호}
```

예를 들어 `user1`의 비밀번호는 `VulnTrade!1#001`입니다.

## 자주 쓰는 개발 명령

```bash
docker compose up --build
docker compose restart app web scheduler
docker compose logs -f app
docker compose logs -f scheduler
docker compose down -v
```

Flask 앱만 직접 실행할 수도 있지만, 이 경우 별도의 MySQL이 필요합니다.

```bash
python app/run.py
```

## 주의사항

- 이 저장소는 의도적으로 취약한 교육용 애플리케이션입니다.
- 보안 취약점을 임의로 제거하지 마세요. 취약점 수정은 해당 작업이 명시된 경우에만 진행합니다.
- `.env`, `db/init.sql`, `app/src/static/uploads/`는 개발용 데이터로만 취급하세요.
