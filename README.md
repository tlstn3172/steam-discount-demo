# Steam Value Insights (SVI) 🎮

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python](https://img.shields.io/badge/Python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![React](https://img.shields.io/badge/React-18+-61DAFB.svg)](https://reactjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5+-3178C6.svg)](https://www.typescriptlang.org/)

> 스팀 게임의 **진짜 가성비**를 분석하여 최적의 구매 시점을 제안하는 데이터 기반 대시보드

---

## 📋 목차

- [프로젝트 소개](#-프로젝트-소개)
- [주요 기능](#-주요-기능)
- [기술 스택](#-기술-스택)
- [시작하기](#-시작하기)
- [프로젝트 구조](#-프로젝트-구조)
- [API 문서](#-api-문서)
- [개발 로드맵](#-개발-로드맵)
- [기여하기](#-기여하기)
- [라이선스](#-라이선스)

---

## 🎯 프로젝트 소개

Steam Value Insights는 스팀의 방대한 할인 데이터 속에서 **단순한 가격 인하율이 아닌, 실제 구매 가치와 기회비용**을 분석하여 게이머들이 최적의 구매 의사결정을 할 수 있도록 돕는 데이터 분석 플랫폼입니다.

### 🤔 왜 만들었나요?

스팀의 잦은 세일과 방대한 게임 수로 인해 사용자들은 다음과 같은 문제에 직면합니다:

- ❌ **할인율이 높아도 실제로 최저가가 아닌 경우가 많음**
- ❌ **구매 후 플레이하지 않는 게임이 라이브러리에 쌓임**
- ❌ **숨겨진 명작 게임을 발견하기 어려움**
- ❌ **가격 대비 실제 플레이 가치를 판단하기 어려움**

Steam Value Insights는 이러한 문제를 **데이터 기반 분석**으로 해결합니다.

---

## ✨ 주요 기능

### 1. 🎯 VPI (가성비 지수) 산출

게임의 예상 플레이 시간 대비 현재 가격의 효율성을 수치화합니다.

```
VPI = (예상 플레이 시간 / 현재 가격) × 1000
```

**예시:** VPI 2.5 = 1,000원당 2.5시간 플레이 가능

### 2. 🚦 스마트 구매 신호등

현재 할인가가 역대 최저가(All-time Low)와 얼마나 근접한지 시각적으로 표시합니다.

- 🟢 **BEST**: 역대 최저가 경신 또는 동일 → "지금이 최적 구매 시점입니다"
- 🟡 **GOOD**: 역대 최저가와 10% 이내 차이 → "좋은 가격입니다"
- 🔴 **WAIT**: 이전 할인폭보다 낮음 → "더 기다리는 것을 권장합니다"

### 3. 💎 Hidden Gem 발굴

마케팅 자본에 가려진 고득점 게임을 자동으로 필터링합니다.

**필터링 조건:**
- 긍정적 평가 비율 ≥ 90%
- 총 리뷰 수 ≤ 1,000개
- 현재 할인율 ≥ 50%

### 4. 📊 가격 변동 그래프

최근 1년간 가격 변동 추이를 시각화하여 구매 타이밍을 분석할 수 있습니다.

### 5. 🔍 고급 필터 및 정렬

- **필터**: 장르, 가격 범위, 할인율, 평가 점수
- **정렬**: VPI 지수, 할인율, 가격, 평가 점수

---

## 🛠 기술 스택

### Backend
- **Framework**: FastAPI 0.104+
- **Language**: Python 3.11+
- **Database**: PostgreSQL 15+
- **ORM**: SQLAlchemy 2.0+
- **Scheduler**: APScheduler 3.10+
- **Cache**: Redis (Phase 3)

### Frontend
- **Framework**: React 18+
- **Language**: TypeScript 5+
- **Build Tool**: Vite 5+
- **Styling**: Tailwind CSS 3+
- **Charts**: Chart.js 4+
- **State Management**: React Query (Phase 2)

### Data Sources
- **CheapShark API**: 가격 정보 및 역대 최저가
- **HowLongToBeat**: 플레이 시간 데이터
- **SteamSpy API**: 리뷰 수 및 소유자 추정치

### DevOps
- **Containerization**: Docker
- **CI/CD**: GitHub Actions
- **Hosting**: TBD (AWS, GCP, Heroku, Vercel 등)

---

## 🚀 시작하기

### 필수 요구사항

- Python 3.11 이상
- Node.js 18 이상
- PostgreSQL 15 이상
- Docker (선택 사항)

### 설치 방법

#### 1. 저장소 클론

```bash
git clone https://github.com/yourusername/steam-value-insights.git
cd steam-value-insights
```

#### 2. 백엔드 설정

```bash
# 백엔드 디렉토리로 이동
cd backend

# 가상환경 생성 및 활성화
python -m venv venv

# Windows
venv\Scripts\activate

# Mac/Linux
source venv/bin/activate

# 패키지 설치
pip install -r requirements.txt

# 환경 변수 설정
cp .env.example .env
# .env 파일을 편집하여 데이터베이스 정보 입력

# 데이터베이스 초기화
python -m app.core.init_db

# 서버 실행
python -m app.main
```

백엔드 서버가 `http://localhost:8000`에서 실행됩니다.

#### 3. 프론트엔드 설정

```bash
# 프론트엔드 디렉토리로 이동
cd frontend

# 패키지 설치
npm install

# 개발 서버 실행
npm run dev
```

프론트엔드가 `http://localhost:5173`에서 실행됩니다.

#### 4. Docker 사용 (선택 사항)

```bash
# 프로젝트 루트에서
docker-compose up -d
```

---

## 📁 프로젝트 구조

```
steam-value-insights/
├── backend/                    # 백엔드 (FastAPI)
│   ├── app/
│   │   ├── api/               # API 엔드포인트
│   │   ├── core/              # 핵심 설정 (DB, 환경변수)
│   │   ├── models/            # SQLAlchemy 모델
│   │   ├── schemas/           # Pydantic 스키마
│   │   ├── services/          # 외부 API 연동
│   │   ├── utils/             # 유틸리티 함수
│   │   └── main.py            # FastAPI 앱
│   ├── tests/                 # 테스트
│   └── requirements.txt       # Python 패키지
│
├── frontend/                   # 프론트엔드 (React + TypeScript)
│   ├── src/
│   │   ├── components/        # React 컴포넌트
│   │   ├── pages/             # 페이지 컴포넌트
│   │   ├── services/          # API 서비스
│   │   ├── types/             # TypeScript 타입
│   │   ├── utils/             # 유틸리티 함수
│   │   ├── App.tsx            # 메인 앱
│   │   └── main.tsx           # 진입점
│   ├── public/                # 정적 파일
│   └── package.json           # npm 패키지
│
├── docs/                       # 문서
│   ├── ideation.md            # 아이디어 및 초기 기획
│   ├── PRD.md                 # 제품 요구사항 정의서
│   ├── TASKS.md               # 개발 태스크 목록
│   └── TUTORIAL.md            # 개발 튜토리얼
│
├── .gitignore                 # Git 무시 파일
├── docker-compose.yml         # Docker Compose 설정
└── README.md                  # 프로젝트 설명 (이 파일)
```

---

## 📚 API 문서

백엔드 서버 실행 후 다음 URL에서 API 문서를 확인할 수 있습니다:

- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

### 주요 엔드포인트

#### 게임 리스트 조회
```http
GET /api/games?page=1&page_size=20&sort_by=vpi
```

**쿼리 파라미터:**
- `page`: 페이지 번호 (기본값: 1)
- `page_size`: 페이지 크기 (기본값: 20, 최대: 100)
- `sort_by`: 정렬 기준 (`vpi`, `discount`, `price`, `rating`)
- `genre`: 장르 필터
- `min_price`, `max_price`: 가격 범위
- `min_discount`: 최소 할인율
- `hidden_gem`: Hidden Gem만 보기 (true/false)

#### 게임 상세 정보 조회
```http
GET /api/games/{game_id}
```

#### 가격 히스토리 조회
```http
GET /api/games/{game_id}/price-history
```

---

## 🗓 개발 로드맵

### Phase 1: MVP 개발 (4주) ✅
- [x] 프로젝트 초기 설정
- [x] 외부 API 연동 (CheapShark, HLTB, SteamSpy)
- [x] 데이터 수집 파이프라인
- [x] VPI 및 구매 신호등 로직
- [x] 백엔드 API 개발
- [x] 메인 대시보드 UI

### Phase 2: 고도화 (4주) 🚧
- [ ] Hidden Gem 필터
- [ ] 가격 변동 그래프
- [ ] 게임 상세 페이지
- [ ] 메타크리틱 정보 통합
- [ ] 반응형 디자인

### Phase 3: 최적화 및 안정화 (2주) 📅
- [ ] 성능 최적화
- [ ] Redis 캐싱
- [ ] 모니터링 시스템
- [ ] 사용자 테스트 및 피드백 반영

### Phase 4: 확장 기능 (미정) 💡
- [ ] 개인화 추천 알고리즘
- [ ] 가격 알림 기능
- [ ] 친구와 게임 비교
- [ ] 모바일 앱
- [ ] 브라우저 확장 프로그램

---

## 📊 성공 지표 (KPI)

### 기술적 KPI
- 데이터 정확도: ≥ 99%
- API 응답 시간: ≤ 2초
- 데이터 신선도: ≤ 1시간

### 사용자 경험 KPI
- 평균 세션 시간: ≥ 5분
- 세션당 페이지 뷰: ≥ 10
- 월간 재방문율: ≥ 40%

### 비즈니스 KPI
- 찜 목록 추가율: ≥ 20%
- 스팀 스토어 클릭률: ≥ 30%
- 사용자 만족도: ≥ 4.0/5.0

---

## 🤝 기여하기

프로젝트에 기여하고 싶으신가요? 환영합니다! 🎉

### 기여 방법

1. 이 저장소를 Fork 합니다
2. 새로운 브랜치를 생성합니다 (`git checkout -b feature/AmazingFeature`)
3. 변경사항을 커밋합니다 (`git commit -m 'Add some AmazingFeature'`)
4. 브랜치에 Push 합니다 (`git push origin feature/AmazingFeature`)
5. Pull Request를 생성합니다

### 코드 스타일

- **Python**: PEP 8 준수, Black 포맷터 사용
- **TypeScript**: ESLint + Prettier 사용
- **커밋 메시지**: Conventional Commits 형식 권장

### 버그 리포트 및 기능 제안

[Issues](https://github.com/yourusername/steam-value-insights/issues) 페이지에서 버그 리포트나 기능 제안을 해주세요.

---

## 📖 문서

더 자세한 정보는 다음 문서를 참조하세요:

- [PRD (제품 요구사항 정의서)](docs/PRD.md)
- [개발 태스크 목록](docs/TASKS.md)
- [개발 튜토리얼](docs/TUTORIAL.md)
- [아이디어 및 기획](docs/ideation.md)

---

## 📄 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.

---

## 👨‍💻 개발자

**Your Name**
- GitHub: [@yourusername](https://github.com/yourusername)
- Email: your.email@example.com

---

## 🙏 감사의 말

이 프로젝트는 다음 오픈소스 프로젝트와 API를 사용합니다:

- [CheapShark API](https://apidocs.cheapshark.com/)
- [HowLongToBeat](https://howlongtobeat.com/)
- [SteamSpy](https://steamspy.com/)
- [FastAPI](https://fastapi.tiangolo.com/)
- [React](https://reactjs.org/)
- [Tailwind CSS](https://tailwindcss.com/)

---

## 📞 문의

프로젝트에 대한 질문이나 제안이 있으시면 언제든지 연락주세요!

- **Issues**: [GitHub Issues](https://github.com/yourusername/steam-value-insights/issues)
- **Email**: your.email@example.com

---

<div align="center">

**⭐ 이 프로젝트가 유용하다면 Star를 눌러주세요! ⭐**

Made with ❤️ by [Your Name](https://github.com/yourusername)

</div>
