# Steam Value Insights (SVI) - 개발 튜토리얼

**프로젝트:** Steam Value Insights  
**문서 버전:** 1.0  
**작성일:** 2025-12-23  
**난이도:** 중급 ~ 고급

---

## 📚 목차

1. [시작하기 전에](#시작하기-전에)
2. [Phase 1: MVP 개발](#phase-1-mvp-개발)
   - [Week 1: 프로젝트 초기 설정](#week-1-프로젝트-초기-설정)
   - [Week 2: 데이터 파이프라인 구축](#week-2-데이터-파이프라인-구축)
   - [Week 3: 백엔드 API 개발](#week-3-백엔드-api-개발)
   - [Week 4: 프론트엔드 UI 개발](#week-4-프론트엔드-ui-개발)
3. [Phase 2: 고도화](#phase-2-고도화)
4. [Phase 3: 최적화 및 안정화](#phase-3-최적화-및-안정화)
5. [배포 가이드](#배포-가이드)
6. [문제 해결](#문제-해결)

---

## 시작하기 전에

### 필수 요구사항

**개발 환경:**
- Python 3.11 이상
- Node.js 18 이상
- PostgreSQL 14 이상
- Git
- 코드 에디터 (VS Code 권장)

**필요한 지식:**
- Python (FastAPI, Pandas)
- JavaScript/TypeScript
- React 기본
- SQL 기본
- REST API 개념
- Git 기본 사용법

**외부 서비스 계정:**
- GitHub 계정 (코드 저장소)
- 호스팅 플랫폼 계정 (선택 사항)

---

## Phase 1: MVP 개발

### Week 1: 프로젝트 초기 설정

#### 1단계: Git 저장소 초기화

```bash
# 프로젝트 디렉토리 생성
mkdir steam-value-insights
cd steam-value-insights

# Git 초기화
git init

# .gitignore 파일 생성
```

**.gitignore 파일 내용:**
```gitignore
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
venv/
env/
.env

# Node
node_modules/
dist/
build/
.DS_Store

# IDE
.vscode/
.idea/

# Database
*.db
*.sqlite

# Logs
*.log
```

**README.md 작성:**
```markdown
# Steam Value Insights (SVI)

스팀 게임의 가성비를 분석하여 최적의 구매 시점을 제안하는 데이터 분석 대시보드

## 주요 기능
- VPI (가성비 지수) 계산
- 스마트 구매 신호등
- Hidden Gem 발굴
- 가격 변동 그래프

## 기술 스택
- Backend: Python, FastAPI, PostgreSQL
- Frontend: React, TypeScript, Tailwind CSS
- Data: CheapShark API, HowLongToBeat, SteamSpy API

## 설치 방법
(추후 작성)
```

---

#### 2단계: 백엔드 프로젝트 구조 설정

```bash
# 백엔드 디렉토리 생성
mkdir -p backend/app/{api,core,models,schemas,services,utils}
mkdir backend/tests

# __init__.py 파일 생성
touch backend/app/__init__.py
touch backend/app/api/__init__.py
touch backend/app/core/__init__.py
touch backend/app/models/__init__.py
touch backend/app/schemas/__init__.py
touch backend/app/services/__init__.py
touch backend/app/utils/__init__.py
```

**requirements.txt 작성:**
```txt
fastapi==0.104.1
uvicorn[standard]==0.24.0
pandas==2.1.3
apscheduler==3.10.4
psycopg2-binary==2.9.9
sqlalchemy==2.0.23
alembic==1.12.1
requests==2.31.0
python-dotenv==1.0.0
pydantic==2.5.0
pydantic-settings==2.1.0
```

**Python 가상환경 생성 및 패키지 설치:**
```bash
# 가상환경 생성
cd backend
python -m venv venv

# 가상환경 활성화 (Windows)
venv\Scripts\activate

# 가상환경 활성화 (Mac/Linux)
source venv/bin/activate

# 패키지 설치
pip install -r requirements.txt
```

---

#### 3단계: FastAPI 기본 서버 설정

**backend/app/main.py:**
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI(
    title="Steam Value Insights API",
    description="스팀 게임 가성비 분석 API",
    version="1.0.0"
)

# CORS 설정
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173"],  # Vite 기본 포트
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/")
async def root():
    return {"message": "Steam Value Insights API"}

@app.get("/health")
async def health_check():
    return {
        "status": "healthy",
        "version": "1.0.0"
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

**서버 실행 테스트:**
```bash
# backend 디렉토리에서
python -m app.main

# 브라우저에서 확인
# http://localhost:8000
# http://localhost:8000/docs (Swagger UI)
```

---

#### 4단계: 프론트엔드 프로젝트 설정

```bash
# 프로젝트 루트로 이동
cd ..

# Vite로 React + TypeScript 프로젝트 생성
npm create vite@latest frontend -- --template react-ts

# 프론트엔드 디렉토리로 이동
cd frontend

# 패키지 설치
npm install

# Tailwind CSS 설치
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

**tailwind.config.js 설정:**
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

**src/index.css 수정:**
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

**개발 서버 실행:**
```bash
npm run dev
# http://localhost:5173 에서 확인
```

---

#### 5단계: 데이터베이스 설정

**PostgreSQL 설치 (Docker 사용):**
```bash
# docker-compose.yml 파일 생성 (프로젝트 루트)
```

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    container_name: svi-postgres
    environment:
      POSTGRES_USER: svi_user
      POSTGRES_PASSWORD: svi_password
      POSTGRES_DB: steam_value_insights
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

**데이터베이스 시작:**
```bash
docker-compose up -d
```

**backend/.env 파일 생성:**
```env
DATABASE_URL=postgresql://svi_user:svi_password@localhost:5432/steam_value_insights
SECRET_KEY=your-secret-key-here
DEBUG=True
```

---

#### 6단계: SQLAlchemy 모델 정의

**backend/app/core/database.py:**
```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from dotenv import load_dotenv
import os

load_dotenv()

DATABASE_URL = os.getenv("DATABASE_URL")

engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

**backend/app/models/game.py:**
```python
from sqlalchemy import Column, Integer, String, Float, DateTime, Boolean
from sqlalchemy.sql import func
from app.core.database import Base

class Game(Base):
    __tablename__ = "games"

    id = Column(Integer, primary_key=True, index=True)
    steam_id = Column(String, unique=True, index=True)
    title = Column(String, index=True)
    thumbnail_url = Column(String)
    current_price = Column(Float)
    original_price = Column(Float)
    discount_percent = Column(Float)
    all_time_low = Column(Float)
    positive_reviews = Column(Integer)
    total_reviews = Column(Integer)
    playtime_main = Column(Float)  # 메인 스토리 플레이 시간
    playtime_complete = Column(Float)  # 컴플리트 플레이 시간
    metacritic_score = Column(Integer, nullable=True)
    release_date = Column(String)
    developer = Column(String)
    publisher = Column(String)
    genre = Column(String)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), onupdate=func.now())
```

**데이터베이스 테이블 생성:**
```python
# backend/app/core/init_db.py
from app.core.database import engine, Base
from app.models.game import Game

def init_db():
    Base.metadata.create_all(bind=engine)

if __name__ == "__main__":
    init_db()
    print("Database tables created successfully!")
```

```bash
# 실행
python -m app.core.init_db
```

---

### Week 2: 데이터 파이프라인 구축

#### 7단계: CheapShark API 연동

**backend/app/services/cheapshark.py:**
```python
import requests
from typing import List, Dict, Optional
import time

class CheapSharkAPI:
    BASE_URL = "https://www.cheapshark.com/api/1.0"
    
    def __init__(self):
        self.session = requests.Session()
    
    def get_deals(self, 
                  store_id: int = 1,  # 1 = Steam
                  page_number: int = 0,
                  page_size: int = 60,
                  on_sale: bool = True) -> List[Dict]:
        """할인 중인 게임 목록 조회"""
        try:
            params = {
                "storeID": store_id,
                "pageNumber": page_number,
                "pageSize": page_size,
                "onSale": 1 if on_sale else 0
            }
            
            response = self.session.get(
                f"{self.BASE_URL}/deals",
                params=params,
                timeout=10
            )
            response.raise_for_status()
            return response.json()
        
        except requests.exceptions.RequestException as e:
            print(f"CheapShark API 오류: {e}")
            return []
    
    def get_game_info(self, game_id: str) -> Optional[Dict]:
        """게임 상세 정보 조회"""
        try:
            response = self.session.get(
                f"{self.BASE_URL}/games",
                params={"id": game_id},
                timeout=10
            )
            response.raise_for_status()
            return response.json()
        
        except requests.exceptions.RequestException as e:
            print(f"게임 정보 조회 오류: {e}")
            return None
    
    def get_price_history(self, game_id: str) -> List[Dict]:
        """가격 히스토리 조회"""
        game_info = self.get_game_info(game_id)
        if game_info and "deals" in game_info:
            return game_info["deals"]
        return []

# 사용 예시
if __name__ == "__main__":
    api = CheapSharkAPI()
    deals = api.get_deals(page_size=10)
    
    for deal in deals[:5]:
        print(f"게임: {deal['title']}")
        print(f"현재 가격: ${deal['salePrice']}")
        print(f"할인율: {deal['savings']:.1f}%")
        print("---")
```

**테스트:**
```bash
python -m app.services.cheapshark
```

---

#### 8단계: HowLongToBeat 연동

**backend/app/services/hltb.py:**
```python
import requests
from typing import Optional, Dict

class HowLongToBeatAPI:
    """
    HowLongToBeat는 공식 API가 없으므로,
    비공식 API 또는 스크래핑 라이브러리 사용
    """
    
    def __init__(self):
        # howlongtobeatpy 라이브러리 사용 권장
        # pip install howlongtobeatpy
        try:
            from howlongtobeatpy import HowLongToBeat
            self.hltb = HowLongToBeat()
        except ImportError:
            print("howlongtobeatpy 라이브러리를 설치하세요: pip install howlongtobeatpy")
            self.hltb = None
    
    def get_playtime(self, game_title: str) -> Optional[Dict]:
        """게임 플레이 시간 조회"""
        if not self.hltb:
            return None
        
        try:
            results = self.hltb.search(game_title)
            
            if results and len(results) > 0:
                best_match = results[0]
                return {
                    "title": best_match.game_name,
                    "main_story": best_match.main_story,
                    "main_extra": best_match.main_extra,
                    "completionist": best_match.completionist,
                    "similarity": best_match.similarity
                }
        
        except Exception as e:
            print(f"HLTB 조회 오류: {e}")
        
        return None

# 사용 예시
if __name__ == "__main__":
    api = HowLongToBeatAPI()
    playtime = api.get_playtime("The Witcher 3")
    
    if playtime:
        print(f"게임: {playtime['title']}")
        print(f"메인 스토리: {playtime['main_story']}시간")
        print(f"컴플리트: {playtime['completionist']}시간")
```

**필요한 패키지 설치:**
```bash
pip install howlongtobeatpy
# requirements.txt에도 추가
```

---

#### 9단계: SteamSpy API 연동

**backend/app/services/steamspy.py:**
```python
import requests
from typing import Optional, Dict
import time

class SteamSpyAPI:
    BASE_URL = "https://steamspy.com/api.php"
    
    def __init__(self):
        self.session = requests.Session()
    
    def get_game_details(self, app_id: int) -> Optional[Dict]:
        """게임 상세 정보 조회"""
        try:
            params = {
                "request": "appdetails",
                "appid": app_id
            }
            
            response = self.session.get(
                self.BASE_URL,
                params=params,
                timeout=10
            )
            response.raise_for_status()
            
            # Rate limiting 준수
            time.sleep(1)
            
            return response.json()
        
        except requests.exceptions.RequestException as e:
            print(f"SteamSpy API 오류: {e}")
            return None
    
    def get_positive_ratio(self, app_id: int) -> Optional[float]:
        """긍정적 평가 비율 조회"""
        details = self.get_game_details(app_id)
        
        if details and "positive" in details and "negative" in details:
            positive = details["positive"]
            negative = details["negative"]
            total = positive + negative
            
            if total > 0:
                return positive / total
        
        return None

# 사용 예시
if __name__ == "__main__":
    api = SteamSpyAPI()
    # The Witcher 3 App ID: 292030
    details = api.get_game_details(292030)
    
    if details:
        print(f"게임: {details.get('name')}")
        print(f"소유자: {details.get('owners')}")
        print(f"긍정 리뷰: {details.get('positive')}")
        print(f"부정 리뷰: {details.get('negative')}")
```

---

#### 10단계: 핵심 비즈니스 로직 구현

**backend/app/utils/calculations.py:**
```python
from typing import Optional

def calculate_vpi(playtime: float, price: float) -> float:
    """
    VPI (Value-to-Price Index) 계산
    VPI = (예상 플레이 시간 / 현재 가격) × 1000
    
    Args:
        playtime: 예상 플레이 시간 (시간)
        price: 현재 가격 (원 또는 달러)
    
    Returns:
        VPI 지수 (1,000원당 플레이 시간)
    """
    if price <= 0:
        return 0.0
    
    if playtime <= 0:
        return 0.0
    
    return (playtime / price) * 1000


def calculate_purchase_signal(
    current_price: float, 
    all_time_low: float
) -> str:
    """
    스마트 구매 신호등 계산
    
    Args:
        current_price: 현재 가격
        all_time_low: 역대 최저가
    
    Returns:
        "BEST", "GOOD", "WAIT" 중 하나
    """
    if all_time_low <= 0:
        return "WAIT"
    
    # 역대 최저가 경신 또는 동일
    if current_price <= all_time_low:
        return "BEST"
    
    # 역대 최저가와 10% 이내 차이
    price_diff_ratio = (current_price - all_time_low) / all_time_low
    if price_diff_ratio <= 0.1:
        return "GOOD"
    
    # 그 외
    return "WAIT"


def get_signal_message(signal: str) -> str:
    """신호에 따른 메시지 반환"""
    messages = {
        "BEST": "🟢 지금이 최적 구매 시점입니다",
        "GOOD": "🟡 좋은 가격입니다",
        "WAIT": "🔴 더 기다리는 것을 권장합니다"
    }
    return messages.get(signal, "알 수 없음")


def is_hidden_gem(
    positive_ratio: float,
    review_count: int,
    discount: float
) -> bool:
    """
    Hidden Gem 판별
    
    조건:
    - 긍정적 평가 비율 >= 90%
    - 총 리뷰 수 <= 1,000개
    - 현재 할인율 >= 50%
    """
    return (
        positive_ratio >= 0.9 and
        review_count <= 1000 and
        discount >= 0.5
    )


# 단위 테스트
if __name__ == "__main__":
    # VPI 테스트
    vpi = calculate_vpi(playtime=50, price=20000)
    print(f"VPI: {vpi:.2f} (1,000원당 {vpi:.2f}시간)")
    
    # 구매 신호 테스트
    signal = calculate_purchase_signal(current_price=15000, all_time_low=15000)
    print(f"신호: {signal} - {get_signal_message(signal)}")
    
    # Hidden Gem 테스트
    is_gem = is_hidden_gem(positive_ratio=0.95, review_count=500, discount=0.6)
    print(f"Hidden Gem: {is_gem}")
```

---

#### 11단계: 데이터 수집 스케줄러 구현

**backend/app/services/scheduler.py:**
```python
from apscheduler.schedulers.background import BackgroundScheduler
from apscheduler.triggers.interval import IntervalTrigger
from datetime import datetime
from app.services.cheapshark import CheapSharkAPI
from app.services.steamspy import SteamSpyAPI
from app.services.hltb import HowLongToBeatAPI
from app.core.database import SessionLocal
from app.models.game import Game
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

class DataCollectionScheduler:
    def __init__(self):
        self.scheduler = BackgroundScheduler()
        self.cheapshark = CheapSharkAPI()
        self.steamspy = SteamSpyAPI()
        self.hltb = HowLongToBeatAPI()
    
    def collect_price_data(self):
        """가격 데이터 수집 (1시간마다)"""
        logger.info("가격 데이터 수집 시작...")
        
        try:
            deals = self.cheapshark.get_deals(page_size=100)
            db = SessionLocal()
            
            for deal in deals:
                # 게임 정보 저장 또는 업데이트
                game = db.query(Game).filter(
                    Game.steam_id == deal.get("steamAppID")
                ).first()
                
                if game:
                    # 업데이트
                    game.current_price = float(deal.get("salePrice", 0))
                    game.discount_percent = float(deal.get("savings", 0))
                else:
                    # 새로 추가
                    game = Game(
                        steam_id=deal.get("steamAppID"),
                        title=deal.get("title"),
                        thumbnail_url=deal.get("thumb"),
                        current_price=float(deal.get("salePrice", 0)),
                        original_price=float(deal.get("normalPrice", 0)),
                        discount_percent=float(deal.get("savings", 0))
                    )
                    db.add(game)
            
            db.commit()
            logger.info(f"가격 데이터 수집 완료: {len(deals)}개 게임")
        
        except Exception as e:
            logger.error(f"가격 데이터 수집 오류: {e}")
        
        finally:
            db.close()
    
    def collect_review_data(self):
        """리뷰 데이터 수집 (24시간마다)"""
        logger.info("리뷰 데이터 수집 시작...")
        # 구현 생략 (위와 유사한 패턴)
    
    def collect_playtime_data(self):
        """플레이 시간 데이터 수집 (7일마다)"""
        logger.info("플레이 시간 데이터 수집 시작...")
        # 구현 생략
    
    def start(self):
        """스케줄러 시작"""
        # 가격 데이터: 1시간마다
        self.scheduler.add_job(
            self.collect_price_data,
            trigger=IntervalTrigger(hours=1),
            id="collect_price_data",
            name="가격 데이터 수집",
            replace_existing=True
        )
        
        # 리뷰 데이터: 24시간마다
        self.scheduler.add_job(
            self.collect_review_data,
            trigger=IntervalTrigger(hours=24),
            id="collect_review_data",
            name="리뷰 데이터 수집",
            replace_existing=True
        )
        
        # 플레이 시간 데이터: 7일마다
        self.scheduler.add_job(
            self.collect_playtime_data,
            trigger=IntervalTrigger(days=7),
            id="collect_playtime_data",
            name="플레이 시간 데이터 수집",
            replace_existing=True
        )
        
        self.scheduler.start()
        logger.info("데이터 수집 스케줄러 시작됨")
    
    def shutdown(self):
        """스케줄러 종료"""
        self.scheduler.shutdown()
        logger.info("데이터 수집 스케줄러 종료됨")

# main.py에 통합
scheduler = DataCollectionScheduler()
```

**main.py 수정:**
```python
from fastapi import FastAPI
from app.services.scheduler import DataCollectionScheduler

app = FastAPI(title="Steam Value Insights API")

scheduler = DataCollectionScheduler()

@app.on_event("startup")
async def startup_event():
    scheduler.start()

@app.on_event("shutdown")
async def shutdown_event():
    scheduler.shutdown()
```

---

### Week 3: 백엔드 API 개발

#### 12단계: Pydantic 스키마 정의

**backend/app/schemas/game.py:**
```python
from pydantic import BaseModel
from typing import Optional
from datetime import datetime

class GameBase(BaseModel):
    title: str
    steam_id: str
    thumbnail_url: Optional[str] = None
    current_price: float
    original_price: float
    discount_percent: float

class GameCreate(GameBase):
    pass

class GameResponse(GameBase):
    id: int
    vpi: float
    purchase_signal: str
    signal_message: str
    positive_ratio: Optional[float] = None
    total_reviews: int = 0
    playtime_main: Optional[float] = None
    is_hidden_gem: bool = False
    created_at: datetime
    
    class Config:
        from_attributes = True

class GameDetail(GameResponse):
    all_time_low: Optional[float] = None
    playtime_complete: Optional[float] = None
    metacritic_score: Optional[int] = None
    release_date: Optional[str] = None
    developer: Optional[str] = None
    publisher: Optional[str] = None
    genre: Optional[str] = None

class GameListResponse(BaseModel):
    total: int
    page: int
    page_size: int
    games: list[GameResponse]
```

---

#### 13단계: API 엔드포인트 구현

**backend/app/api/games.py:**
```python
from fastapi import APIRouter, Depends, Query
from sqlalchemy.orm import Session
from typing import List, Optional
from app.core.database import get_db
from app.models.game import Game
from app.schemas.game import GameResponse, GameDetail, GameListResponse
from app.utils.calculations import (
    calculate_vpi, 
    calculate_purchase_signal,
    get_signal_message,
    is_hidden_gem
)

router = APIRouter(prefix="/api/games", tags=["games"])

@router.get("", response_model=GameListResponse)
async def get_games(
    page: int = Query(1, ge=1),
    page_size: int = Query(20, ge=1, le=100),
    sort_by: str = Query("vpi", regex="^(vpi|discount|price|rating)$"),
    genre: Optional[str] = None,
    min_price: Optional[float] = None,
    max_price: Optional[float] = None,
    min_discount: Optional[float] = None,
    hidden_gem: bool = False,
    db: Session = Depends(get_db)
):
    """게임 리스트 조회"""
    
    # 기본 쿼리
    query = db.query(Game)
    
    # 필터 적용
    if genre:
        query = query.filter(Game.genre.contains(genre))
    
    if min_price is not None:
        query = query.filter(Game.current_price >= min_price)
    
    if max_price is not None:
        query = query.filter(Game.current_price <= max_price)
    
    if min_discount is not None:
        query = query.filter(Game.discount_percent >= min_discount)
    
    # 전체 개수
    total = query.count()
    
    # 페이지네이션
    offset = (page - 1) * page_size
    games = query.offset(offset).limit(page_size).all()
    
    # VPI 및 구매 신호 계산
    game_responses = []
    for game in games:
        vpi = calculate_vpi(
            playtime=game.playtime_main or 0,
            price=game.current_price
        )
        
        signal = calculate_purchase_signal(
            current_price=game.current_price,
            all_time_low=game.all_time_low or game.current_price
        )
        
        positive_ratio = 0
        if game.total_reviews > 0:
            positive_ratio = game.positive_reviews / game.total_reviews
        
        is_gem = is_hidden_gem(
            positive_ratio=positive_ratio,
            review_count=game.total_reviews,
            discount=game.discount_percent / 100
        )
        
        # Hidden Gem 필터
        if hidden_gem and not is_gem:
            continue
        
        game_response = GameResponse(
            id=game.id,
            title=game.title,
            steam_id=game.steam_id,
            thumbnail_url=game.thumbnail_url,
            current_price=game.current_price,
            original_price=game.original_price,
            discount_percent=game.discount_percent,
            vpi=vpi,
            purchase_signal=signal,
            signal_message=get_signal_message(signal),
            positive_ratio=positive_ratio,
            total_reviews=game.total_reviews,
            playtime_main=game.playtime_main,
            is_hidden_gem=is_gem,
            created_at=game.created_at
        )
        game_responses.append(game_response)
    
    # 정렬
    if sort_by == "vpi":
        game_responses.sort(key=lambda x: x.vpi, reverse=True)
    elif sort_by == "discount":
        game_responses.sort(key=lambda x: x.discount_percent, reverse=True)
    elif sort_by == "price":
        game_responses.sort(key=lambda x: x.current_price)
    
    return GameListResponse(
        total=total,
        page=page,
        page_size=page_size,
        games=game_responses
    )


@router.get("/{game_id}", response_model=GameDetail)
async def get_game_detail(
    game_id: int,
    db: Session = Depends(get_db)
):
    """게임 상세 정보 조회"""
    
    game = db.query(Game).filter(Game.id == game_id).first()
    
    if not game:
        from fastapi import HTTPException
        raise HTTPException(status_code=404, detail="게임을 찾을 수 없습니다")
    
    # VPI 및 구매 신호 계산
    vpi = calculate_vpi(
        playtime=game.playtime_main or 0,
        price=game.current_price
    )
    
    signal = calculate_purchase_signal(
        current_price=game.current_price,
        all_time_low=game.all_time_low or game.current_price
    )
    
    positive_ratio = 0
    if game.total_reviews > 0:
        positive_ratio = game.positive_reviews / game.total_reviews
    
    is_gem = is_hidden_gem(
        positive_ratio=positive_ratio,
        review_count=game.total_reviews,
        discount=game.discount_percent / 100
    )
    
    return GameDetail(
        id=game.id,
        title=game.title,
        steam_id=game.steam_id,
        thumbnail_url=game.thumbnail_url,
        current_price=game.current_price,
        original_price=game.original_price,
        discount_percent=game.discount_percent,
        all_time_low=game.all_time_low,
        vpi=vpi,
        purchase_signal=signal,
        signal_message=get_signal_message(signal),
        positive_ratio=positive_ratio,
        total_reviews=game.total_reviews,
        playtime_main=game.playtime_main,
        playtime_complete=game.playtime_complete,
        metacritic_score=game.metacritic_score,
        release_date=game.release_date,
        developer=game.developer,
        publisher=game.publisher,
        genre=game.genre,
        is_hidden_gem=is_gem,
        created_at=game.created_at
    )
```

**main.py에 라우터 등록:**
```python
from app.api import games

app.include_router(games.router)
```

---

### Week 4: 프론트엔드 UI 개발

#### 14단계: TypeScript 타입 정의

**frontend/src/types/game.ts:**
```typescript
export interface Game {
  id: number;
  title: string;
  steam_id: string;
  thumbnail_url: string | null;
  current_price: number;
  original_price: number;
  discount_percent: number;
  vpi: number;
  purchase_signal: 'BEST' | 'GOOD' | 'WAIT';
  signal_message: string;
  positive_ratio: number | null;
  total_reviews: number;
  playtime_main: number | null;
  is_hidden_gem: boolean;
  created_at: string;
}

export interface GameDetail extends Game {
  all_time_low: number | null;
  playtime_complete: number | null;
  metacritic_score: number | null;
  release_date: string | null;
  developer: string | null;
  publisher: string | null;
  genre: string | null;
}

export interface GameListResponse {
  total: number;
  page: number;
  page_size: number;
  games: Game[];
}
```

---

#### 15단계: API 서비스 구현

**frontend/src/services/api.ts:**
```typescript
import { Game, GameDetail, GameListResponse } from '../types/game';

const API_BASE_URL = 'http://localhost:8000';

export const gameAPI = {
  async getGames(params: {
    page?: number;
    page_size?: number;
    sort_by?: string;
    genre?: string;
    min_price?: number;
    max_price?: number;
    min_discount?: number;
    hidden_gem?: boolean;
  } = {}): Promise<GameListResponse> {
    const queryParams = new URLSearchParams();
    
    Object.entries(params).forEach(([key, value]) => {
      if (value !== undefined && value !== null) {
        queryParams.append(key, value.toString());
      }
    });
    
    const response = await fetch(
      `${API_BASE_URL}/api/games?${queryParams.toString()}`
    );
    
    if (!response.ok) {
      throw new Error('게임 목록을 불러오는데 실패했습니다');
    }
    
    return response.json();
  },
  
  async getGameDetail(gameId: number): Promise<GameDetail> {
    const response = await fetch(`${API_BASE_URL}/api/games/${gameId}`);
    
    if (!response.ok) {
      throw new Error('게임 정보를 불러오는데 실패했습니다');
    }
    
    return response.json();
  }
};
```

---

#### 16단계: 공통 컴포넌트 개발

**frontend/src/components/GameCard.tsx:**
```typescript
import React from 'react';
import { Game } from '../types/game';

interface GameCardProps {
  game: Game;
  onClick?: () => void;
}

export const GameCard: React.FC<GameCardProps> = ({ game, onClick }) => {
  const getSignalColor = (signal: string) => {
    switch (signal) {
      case 'BEST': return 'bg-green-500';
      case 'GOOD': return 'bg-yellow-500';
      case 'WAIT': return 'bg-red-500';
      default: return 'bg-gray-500';
    }
  };
  
  return (
    <div 
      className="bg-white rounded-lg shadow-md overflow-hidden hover:shadow-xl transition-shadow cursor-pointer"
      onClick={onClick}
    >
      {/* 썸네일 */}
      <div className="relative">
        <img 
          src={game.thumbnail_url || '/placeholder.jpg'} 
          alt={game.title}
          className="w-full h-48 object-cover"
        />
        
        {/* 할인율 배지 */}
        {game.discount_percent > 0 && (
          <div className="absolute top-2 right-2 bg-red-600 text-white px-2 py-1 rounded font-bold">
            -{game.discount_percent.toFixed(0)}%
          </div>
        )}
        
        {/* Hidden Gem 배지 */}
        {game.is_hidden_gem && (
          <div className="absolute top-2 left-2 bg-purple-600 text-white px-2 py-1 rounded text-sm">
            💎 Hidden Gem
          </div>
        )}
      </div>
      
      {/* 게임 정보 */}
      <div className="p-4">
        <h3 className="font-bold text-lg mb-2 truncate">{game.title}</h3>
        
        {/* 가격 정보 */}
        <div className="flex items-center gap-2 mb-2">
          {game.discount_percent > 0 && (
            <span className="text-gray-400 line-through">
              ₩{game.original_price.toLocaleString()}
            </span>
          )}
          <span className="text-xl font-bold text-blue-600">
            ₩{game.current_price.toLocaleString()}
          </span>
        </div>
        
        {/* VPI 지수 */}
        <div className="mb-2">
          <span className="text-sm text-gray-600">VPI: </span>
          <span className="font-bold text-green-600">
            {game.vpi.toFixed(1)} 시간/천원
          </span>
        </div>
        
        {/* 구매 신호등 */}
        <div className="flex items-center gap-2">
          <div className={`w-3 h-3 rounded-full ${getSignalColor(game.purchase_signal)}`} />
          <span className="text-sm text-gray-700">{game.signal_message}</span>
        </div>
        
        {/* 평가 점수 */}
        {game.positive_ratio !== null && (
          <div className="mt-2 text-sm text-gray-600">
            👍 {(game.positive_ratio * 100).toFixed(0)}% ({game.total_reviews.toLocaleString()} 리뷰)
          </div>
        )}
      </div>
    </div>
  );
};
```

---

#### 17단계: 메인 대시보드 페이지

**frontend/src/pages/Dashboard.tsx:**
```typescript
import React, { useState, useEffect } from 'react';
import { GameCard } from '../components/GameCard';
import { Game } from '../types/game';
import { gameAPI } from '../services/api';

export const Dashboard: React.FC = () => {
  const [games, setGames] = useState<Game[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  
  // 필터 상태
  const [sortBy, setSortBy] = useState('vpi');
  const [hiddenGemOnly, setHiddenGemOnly] = useState(false);
  
  useEffect(() => {
    loadGames();
  }, [sortBy, hiddenGemOnly]);
  
  const loadGames = async () => {
    try {
      setLoading(true);
      const response = await gameAPI.getGames({
        page: 1,
        page_size: 20,
        sort_by: sortBy,
        hidden_gem: hiddenGemOnly
      });
      setGames(response.games);
      setError(null);
    } catch (err) {
      setError('게임 목록을 불러오는데 실패했습니다');
      console.error(err);
    } finally {
      setLoading(false);
    }
  };
  
  if (loading) {
    return (
      <div className="flex justify-center items-center h-screen">
        <div className="text-xl">로딩 중...</div>
      </div>
    );
  }
  
  if (error) {
    return (
      <div className="flex justify-center items-center h-screen">
        <div className="text-xl text-red-600">{error}</div>
      </div>
    );
  }
  
  return (
    <div className="container mx-auto px-4 py-8">
      {/* 헤더 */}
      <header className="mb-8">
        <h1 className="text-4xl font-bold mb-2">Steam Value Insights</h1>
        <p className="text-gray-600">스팀 게임 가성비 분석 대시보드</p>
      </header>
      
      {/* 필터 및 정렬 */}
      <div className="mb-6 flex gap-4 items-center">
        <div>
          <label className="mr-2">정렬:</label>
          <select 
            value={sortBy}
            onChange={(e) => setSortBy(e.target.value)}
            className="border rounded px-3 py-2"
          >
            <option value="vpi">VPI 높은 순</option>
            <option value="discount">할인율 높은 순</option>
            <option value="price">가격 낮은 순</option>
            <option value="rating">평가 높은 순</option>
          </select>
        </div>
        
        <div>
          <label className="flex items-center gap-2">
            <input 
              type="checkbox"
              checked={hiddenGemOnly}
              onChange={(e) => setHiddenGemOnly(e.target.checked)}
            />
            <span>Hidden Gem만 보기</span>
          </label>
        </div>
      </div>
      
      {/* 게임 그리드 */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
        {games.map(game => (
          <GameCard 
            key={game.id} 
            game={game}
            onClick={() => {
              // 상세 페이지로 이동 (나중에 구현)
              console.log('게임 클릭:', game.title);
            }}
          />
        ))}
      </div>
      
      {games.length === 0 && (
        <div className="text-center text-gray-600 mt-8">
          게임이 없습니다
        </div>
      )}
    </div>
  );
};
```

**App.tsx 수정:**
```typescript
import { Dashboard } from './pages/Dashboard';

function App() {
  return <Dashboard />;
}

export default App;
```

---

## Phase 2: 고도화

### Week 5-8: 고급 기능 구현

이 섹션에서는 다음 기능들을 구현합니다:

1. **Hidden Gem 필터** (Week 5)
2. **가격 변동 그래프** (Week 5-6)
3. **게임 상세 페이지** (Week 6-7)
4. **메타 정보 통합** (Week 7)
5. **반응형 디자인 고도화** (Week 8)

각 기능의 상세 구현은 TASKS.md를 참조하세요.

---

## Phase 3: 최적화 및 안정화

### Week 9-10: 성능 최적화 및 배포 준비

주요 작업:
- 데이터베이스 쿼리 최적화
- Redis 캐싱 구현
- 프론트엔드 코드 스플리팅
- 모니터링 시스템 구축
- 사용자 테스트 및 피드백 반영

---

## 배포 가이드

### Docker를 사용한 배포

**Dockerfile (백엔드):**
```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Dockerfile (프론트엔드):**
```dockerfile
FROM node:18-alpine as build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## 문제 해결

### 자주 발생하는 문제

**1. CORS 오류**
- 백엔드 main.py에서 CORS 설정 확인
- 프론트엔드 URL이 allow_origins에 포함되어 있는지 확인

**2. 데이터베이스 연결 오류**
- .env 파일의 DATABASE_URL 확인
- PostgreSQL이 실행 중인지 확인

**3. API 호출 실패**
- 외부 API의 Rate Limit 확인
- 네트워크 연결 확인

---

## 다음 단계

이 튜토리얼을 완료한 후:

1. **기능 확장**: 개인화 추천, 가격 알림 등
2. **플랫폼 확장**: 모바일 앱, 브라우저 확장
3. **데이터 확장**: Epic Games, GOG.com 통합

---

**축하합니다! 🎉**

Steam Value Insights 프로젝트의 기본 구조를 완성했습니다. 
계속해서 기능을 추가하고 개선해 나가세요!
