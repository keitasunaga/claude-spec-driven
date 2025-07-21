--------------------------------------------------------------------------------
# Project Design: Stock Analysis Application / プロジェクト設計: 株価分析アプリケーション

## 1. Overview / 概要
This document provides the technical design specification for the Stock Analysis Application based on the approved requirements. It defines the system architecture, component design, data flow, and implementation guidelines.

本文書は、承認された要件に基づく株価分析アプリケーションの技術設計仕様を提供します。システムアーキテクチャ、コンポーネント設計、データフロー、および実装ガイドラインを定義します。

## 2. Architecture Overview / アーキテクチャ概要

### 2.1 Technology Stack / 技術スタック
Detailed technology choices with specific versions:

- **Language / 言語**: 
  - Primary: Python 3.9.16
  - Type Checking: mypy 1.7.0
  
- **Framework / フレームワーク**:
  - Web UI: Streamlit 1.28.2
  - API (future): FastAPI 0.104.1
  - Testing: pytest 7.4.3, pytest-cov 4.1.0
  
- **Core Libraries / コアライブラリ**:
  - Data Fetching: yfinance 0.2.32 (Yahoo Finance API wrapper)
  - Data Analysis: pandas 2.1.3, numpy 1.26.2
  - Technical Indicators: ta-lib 0.4.28, pandas-ta 0.3.14b
  - Visualization: plotly 5.18.0 (interactive charts)
  - Database: SQLAlchemy 2.0.23 (ORM)
  
- **Development Tools / 開発ツール**:
  - Package Manager: pip 23.3.1
  - Code Formatter: black 23.11.0
  - Linter: flake8 6.1.0, pylint 3.0.2
  - Type Checker: mypy 1.7.0
  - Pre-commit: pre-commit 3.5.0
  
- **Infrastructure / インフラ**:
  - Database: SQLite 3.43.2 (embedded)
  - Container: Docker 24.0.6, docker-compose 2.23.0
  - Cache: In-memory + SQLite
  - Web Server: Streamlit built-in (Tornado-based)

### 2.2 System Architecture Diagram / システムアーキテクチャ図

```
┌─────────────────────────────────────────────────────────────────┐
│                        Presentation Layer                         │
│                   Streamlit Web Application                       │
│  ┌────────────┐ ┌──────────────┐ ┌────────────┐ ┌────────────┐ │
│  │  Dashboard │ │ Chart Viewer │ │ Portfolio  │ │  Reports   │ │
│  │    View    │ │   Component  │ │  Manager   │ │ Generator  │ │
│  └────────────┘ └──────────────┘ └────────────┘ └────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                         Service Layer                            │
│  ┌────────────┐ ┌──────────────┐ ┌────────────┐ ┌────────────┐ │
│  │   Market   │ │  Technical   │ │ Portfolio  │ │   Export   │ │
│  │   Data     │ │  Analysis    │ │  Analysis  │ │  Service   │ │
│  │  Service   │ │   Service    │ │  Service   │ │            │ │
│  └────────────┘ └──────────────┘ └────────────┘ └────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                          Data Layer                              │
│  ┌────────────┐ ┌──────────────┐ ┌────────────┐ ┌────────────┐ │
│  │   Cache    │ │     API      │ │   SQLite   │ │    User    │ │
│  │  Manager   │ │   Clients    │ │  Database  │ │ Preferences│ │
│  └────────────┘ └──────────────┘ └────────────┘ └────────────┘ │
│         │              │                │                │        │
│         └──────────────┴────────────────┴────────────────┘       │
│                              │                                    │
│                     External APIs                                 │
│                 (yfinance, Alpha Vantage)                        │
└─────────────────────────────────────────────────────────────────┘
```

### 2.3 Component Interactions / コンポーネント相互作用
- **Layered Architecture**: Clear separation of concerns between presentation, service, and data layers
- **レイヤードアーキテクチャ**: プレゼンテーション、サービス、データ層間の明確な関心の分離
- **Service-Oriented**: Each service handles specific domain logic independently
- **サービス指向**: 各サービスが特定のドメインロジックを独立して処理

## 3. Module and Class Design / モジュールとクラス設計

### 3.1 Data Layer Components / データ層コンポーネント

#### 3.1.1 Market Data Client (`src/data/market_data_client.py`)
```python
class MarketDataClient:
    """Abstract base class for market data providers"""
    @abstractmethod
    def fetch_historical_data(self, symbol: str, start_date: datetime, 
                            end_date: datetime) -> pd.DataFrame
    
    @abstractmethod
    def fetch_realtime_quote(self, symbol: str) -> Dict[str, Any]

class YFinanceClient(MarketDataClient):
    """yfinance API implementation"""
    def __init__(self, cache_manager: CacheManager)
    def fetch_historical_data(self, symbol: str, start_date: datetime, 
                            end_date: datetime) -> pd.DataFrame
    def fetch_realtime_quote(self, symbol: str) -> Dict[str, Any]
    def _handle_rate_limit(self, retry_count: int) -> None

class AlphaVantageClient(MarketDataClient):
    """Alpha Vantage API implementation (fallback)"""
    def __init__(self, api_key: str, cache_manager: CacheManager)
    def fetch_historical_data(self, symbol: str, start_date: datetime,
                            end_date: datetime) -> pd.DataFrame
    def fetch_realtime_quote(self, symbol: str) -> Dict[str, Any]
```

#### 3.1.2 Cache Manager (`src/data/cache_manager.py`)
```python
class CacheManager:
    """Manages local data caching with SQLite"""
    def __init__(self, db_path: str = "data/cache.db")
    def get_cached_data(self, key: str) -> Optional[pd.DataFrame]
    def set_cached_data(self, key: str, data: pd.DataFrame, 
                       ttl_seconds: int = 3600) -> None
    def clear_expired_cache(self) -> None
    def _generate_cache_key(self, symbol: str, start: datetime, 
                           end: datetime) -> str
```

### 3.2 Service Layer Components / サービス層コンポーネント

#### 3.2.1 Market Data Service (`src/services/market_data_service.py`)
```python
class MarketDataService:
    """Orchestrates data retrieval from multiple sources"""
    def __init__(self, primary_client: MarketDataClient, 
                fallback_client: Optional[MarketDataClient] = None)
    def get_stock_data(self, symbols: List[str], start_date: datetime,
                      end_date: datetime) -> Dict[str, pd.DataFrame]
    def get_realtime_quotes(self, symbols: List[str]) -> Dict[str, Dict]
    async def get_stock_data_async(self, symbols: List[str], 
                                  start_date: datetime, 
                                  end_date: datetime) -> Dict[str, pd.DataFrame]
```

#### 3.2.2 Technical Analysis Service (`src/services/technical_analysis_service.py`)
```python
class TechnicalAnalysisService:
    """Calculates technical indicators"""
    def __init__(self)
    
    # Moving Averages
    def calculate_sma(self, data: pd.DataFrame, periods: List[int]) -> pd.DataFrame
    def calculate_ema(self, data: pd.DataFrame, periods: List[int]) -> pd.DataFrame
    
    # Momentum Indicators
    def calculate_rsi(self, data: pd.DataFrame, period: int = 14) -> pd.Series
    def calculate_macd(self, data: pd.DataFrame) -> pd.DataFrame
    def calculate_stochastic(self, data: pd.DataFrame, period: int = 14) -> pd.DataFrame
    
    # Volatility Indicators
    def calculate_bollinger_bands(self, data: pd.DataFrame, period: int = 20,
                                 std_dev: float = 2) -> pd.DataFrame
    def calculate_atr(self, data: pd.DataFrame, period: int = 14) -> pd.Series
    
    # Pattern Recognition
    def identify_support_resistance(self, data: pd.DataFrame) -> Dict[str, List[float]]
    def detect_chart_patterns(self, data: pd.DataFrame) -> List[ChartPattern]
```

#### 3.2.3 Portfolio Analysis Service (`src/services/portfolio_service.py`)
```python
class PortfolioService:
    """Manages portfolio analysis and calculations"""
    def __init__(self, market_data_service: MarketDataService)
    
    def calculate_portfolio_value(self, holdings: Dict[str, float],
                                 prices: Dict[str, float]) -> float
    def calculate_returns(self, portfolio: Portfolio, 
                         period: str = "1Y") -> pd.Series
    def calculate_correlation_matrix(self, symbols: List[str],
                                   period: str = "1Y") -> pd.DataFrame
    def calculate_risk_metrics(self, portfolio: Portfolio) -> RiskMetrics
    def optimize_portfolio(self, symbols: List[str], 
                          constraints: Dict) -> OptimizedPortfolio
```

### 3.3 Presentation Layer Components / プレゼンテーション層コンポーネント

#### 3.3.1 Streamlit Application (`src/app/main.py`)
```python
class StockAnalysisApp:
    """Main Streamlit application"""
    def __init__(self)
    def run(self) -> None
    def _render_sidebar(self) -> Dict[str, Any]
    def _render_dashboard(self, params: Dict[str, Any]) -> None
    def _render_technical_analysis(self, params: Dict[str, Any]) -> None
    def _render_portfolio_view(self, params: Dict[str, Any]) -> None
    def _handle_language_switch(self) -> None
```

#### 3.3.2 Chart Components (`src/app/components/charts.py`)
```python
class ChartRenderer:
    """Handles all chart rendering with Plotly"""
    def render_candlestick_chart(self, data: pd.DataFrame, 
                               indicators: Optional[Dict] = None) -> go.Figure
    def render_volume_chart(self, data: pd.DataFrame) -> go.Figure
    def render_correlation_heatmap(self, correlation: pd.DataFrame) -> go.Figure
    def render_portfolio_pie_chart(self, holdings: Dict[str, float]) -> go.Figure
    def _apply_theme(self, fig: go.Figure, theme: str = "dark") -> go.Figure
```

## 4. Data Models / データモデル

### 4.1 Core Data Models (`src/models/`)

```python
@dataclass
class StockData:
    symbol: str
    timestamp: datetime
    open: float
    high: float
    low: float
    close: float
    volume: int
    adjusted_close: Optional[float] = None

@dataclass
class Portfolio:
    name: str
    holdings: Dict[str, float]  # symbol -> shares
    cash_balance: float
    created_at: datetime
    updated_at: datetime

@dataclass
class TechnicalIndicator:
    name: str
    value: Union[float, pd.Series]
    parameters: Dict[str, Any]
    timestamp: datetime

@dataclass
class RiskMetrics:
    beta: float
    standard_deviation: float
    sharpe_ratio: float
    max_drawdown: float
    var_95: float  # Value at Risk at 95% confidence
```

### 4.2 Database Schema / データベーススキーマ

```sql
-- Cache table for historical data
CREATE TABLE market_data_cache (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    cache_key TEXT UNIQUE NOT NULL,
    symbol TEXT NOT NULL,
    data BLOB NOT NULL,  -- Serialized DataFrame
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    INDEX idx_symbol (symbol),
    INDEX idx_expires (expires_at)
);

-- User watchlists
CREATE TABLE watchlists (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    symbols TEXT NOT NULL,  -- JSON array
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- User preferences
CREATE TABLE preferences (
    key TEXT PRIMARY KEY,
    value TEXT NOT NULL,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 5. API Design / API設計

### 5.1 Internal Service APIs

```python
# Market Data API
GET /api/stocks/{symbol}/historical
    Query params: start_date, end_date, interval
    Response: StockData[]

GET /api/stocks/{symbol}/quote
    Response: RealtimeQuote

POST /api/stocks/bulk/historical
    Body: { symbols: [], start_date, end_date }
    Response: { symbol: StockData[] }

# Technical Analysis API  
POST /api/analysis/indicators
    Body: { symbol, indicators: [], period }
    Response: { indicator_name: TechnicalIndicator }

# Portfolio API
GET /api/portfolio/{id}
    Response: Portfolio

POST /api/portfolio/{id}/analyze
    Response: PortfolioAnalysis
```

## 6. Security and Error Handling / セキュリティとエラー処理

### 6.1 Security Measures / セキュリティ対策
- API keys stored in environment variables, never in code
- APIキーは環境変数に保存、コードには含めない
- Input validation for all user-provided data
- すべてのユーザー提供データの入力検証
- SQL injection prevention through parameterized queries
- パラメータ化クエリによるSQLインジェクション防止

### 6.2 Error Handling Strategy / エラー処理戦略
```python
class MarketDataError(Exception):
    """Base exception for market data errors"""

class APIRateLimitError(MarketDataError):
    """Raised when API rate limit is exceeded"""

class DataNotAvailableError(MarketDataError):
    """Raised when requested data is not available"""

# Graceful degradation example
try:
    data = primary_client.fetch_data(symbol)
except APIRateLimitError:
    logger.warning("Rate limit hit, using cache")
    data = cache_manager.get_cached_data(symbol)
    if not data and fallback_client:
        data = fallback_client.fetch_data(symbol)
```

## 7. Performance Optimization / パフォーマンス最適化

### 7.1 Caching Strategy / キャッシング戦略
- L1 Cache: In-memory LRU cache for frequently accessed data
- L1キャッシュ: 頻繁にアクセスされるデータ用のメモリ内LRUキャッシュ
- L2 Cache: SQLite database for persistent caching
- L2キャッシュ: 永続的キャッシング用のSQLiteデータベース
- Cache TTL: 15 minutes for quotes, 24 hours for historical data
- キャッシュTTL: 相場は15分、過去データは24時間

### 7.2 Async Operations / 非同期処理
- Concurrent data fetching for multiple symbols
- 複数シンボルの同時データ取得
- Background cache updates
- バックグラウンドでのキャッシュ更新
- Streaming updates for real-time data
- リアルタイムデータのストリーミング更新

## 8. Testing Strategy / テスト戦略

### 8.1 Unit Tests (`tests/unit/`)
- Test coverage target: 80%
- テストカバレッジ目標: 80%
- Mock external API calls
- 外部API呼び出しのモック化
- Test all calculation functions with known inputs/outputs
- 既知の入出力ですべての計算関数をテスト

### 8.2 Integration Tests (`tests/integration/`)
- Test service layer interactions
- サービス層の相互作用をテスト
- Database operations testing
- データベース操作のテスト
- API client fallback mechanisms
- APIクライアントのフォールバックメカニズム

## 9. Deployment Configuration / デプロイメント設定

### 9.1 Docker Configuration
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8501
CMD ["streamlit", "run", "src/app/main.py"]
```

### 9.2 Environment Variables
```bash
# .env.example
ALPHA_VANTAGE_API_KEY=your_api_key_here
CACHE_DB_PATH=./data/cache.db
LOG_LEVEL=INFO
LANGUAGE=en  # or ja
MAX_CONCURRENT_REQUESTS=10
CACHE_TTL_SECONDS=3600
```

## 10. Version Compatibility Notes / バージョン互換性メモ

### 10.1 Critical Dependencies / 重要な依存関係
- **Python 3.9+**: Required for type hints and modern async features
- **ta-lib**: Requires system-level installation (brew install ta-lib on macOS)
- **yfinance**: May break with Yahoo Finance API changes (monitor releases)

### 10.2 Version Constraints / バージョン制約
```
pandas>=2.0.0,<3.0.0  # Major API changes expected in 3.0
streamlit>=1.28.0     # Minimum for st.fragment support
plotly>=5.0.0         # Required for subplot features
SQLAlchemy>=2.0.0     # New query syntax
```

## 11. Revision History / 改訂履歴
- **[2024-12-XX]**: Added detailed technology stack with versions
  - 詳細な技術スタックとバージョンを追加
- **[2024-12-XX]**: Initial design generation by spec-design command
  - Based on requirement.md from stock-analysis-app
  - requirement.mdの株価分析アプリから生成

--------------------------------------------------------------------------------