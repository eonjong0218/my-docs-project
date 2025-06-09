기술 스택
=========

시스템 아키텍처 개요
------------------

ReviewEat은 마이크로서비스 아키텍처 패턴을 기반으로 한 현대적 기술 스택으로 구성되어 있습니다. 각 구성 요소는 독립적으로 확장 가능하며, 컨테이너화를 통해 일관된 배포 환경을 보장합니다.

.. code-block:: text

    ┌─────────────────┐    ┌─────────────────┐
    │   Flutter App   │    │  Google Maps    │
    │   (Frontend)    │◄──►│      API        │
    └─────────────────┘    └─────────────────┘
             │
             ▼ HTTP/REST
    ┌─────────────────┐
    │   FastAPI       │
    │   (Backend)     │
    └─────────────────┘
             │
             ▼ SQL/ORM
    ┌─────────────────┐
    │  PostgreSQL     │
    │   (Database)    │
    └─────────────────┘

프론트엔드 기술 스택
------------------

Flutter Framework
~~~~~~~~~~~~~~~~~

**기술적 선택 근거:**

* **크로스 플랫폼 개발 효율성**: 단일 코드베이스로 iOS, Android 플랫폼 동시 지원
* **고성능 렌더링**: Dart 언어 기반의 네이티브 컴파일로 60fps 성능 보장
* **풍부한 UI 생태계**: Material Design과 Cupertino 위젯을 통한 플랫폼별 네이티브 UX 구현
* **지속적인 기술 지원**: Google의 장기적 투자와 활발한 커뮤니티 생태계

**핵심 의존성 패키지:**

.. code-block:: dart

    dependencies:
      google_maps_flutter: ^2.5.0    # 지도 렌더링 및 상호작용
      http: ^0.13.5                  # RESTful API 통신
      shared_preferences: ^2.0.15    # 로컬 데이터 영속성
      image_picker: ^1.0.0           # 멀티미디어 파일 처리
      geolocator: ^9.0.2             # 위치 기반 서비스
      permission_handler: ^11.0.1    # 시스템 권한 관리

지도 서비스 통합
--------------

Google Maps Platform
~~~~~~~~~~~~~~~~~~~

**서비스 구성 요소:**

* **Places API**: 음식점 검색, 상세 정보 조회, 자동완성 기능
* **Maps SDK for Flutter**: 인터랙티브 지도 렌더링 및 커스텀 마커 표시
* **Geocoding API**: 주소-좌표 양방향 변환 서비스

**기술적 장점:**

* **데이터 정확성**: 실시간 업데이트되는 글로벌 POI 데이터베이스
* **확장성**: 전 세계 지역에 대한 일관된 서비스 품질
* **메타데이터 풍부성**: 영업시간, 사용자 리뷰, 사진, 연락처 정보 등 포괄적 데이터

백엔드 시스템 아키텍처
--------------------

FastAPI Framework
~~~~~~~~~~~~~~~~~

**아키텍처 설계 원칙:**

* **고성능 비동기 처리**: ASGI 기반의 비동기 I/O로 높은 동시성 처리 능력
* **자동 문서화**: OpenAPI 3.0 표준 기반의 실시간 API 문서 자동 생성
* **타입 안전성**: Python 타입 힌트를 활용한 컴파일 타임 오류 검출
* **의존성 주입**: 모듈화된 컴포넌트 구조로 테스트 용이성 및 유지보수성 향상

**핵심 시스템 기능:**

* **인증 및 권한 관리**: JWT 토큰 기반의 stateless 인증 시스템
* **파일 처리**: 멀티파트 업로드를 통한 이미지 파일 관리
* **데이터 검증**: Pydantic 모델을 통한 입출력 데이터 스키마 검증
* **API 버전 관리**: RESTful 설계 원칙에 따른 버전별 엔드포인트 관리

**의존성 라이브러리:**

.. code-block:: python

    # 핵심 프레임워크
    fastapi==0.104.1
    uvicorn[standard]==0.24.0
    
    # 데이터베이스 ORM
    sqlalchemy==2.0.23
    alembic==1.12.1
    psycopg2-binary==2.9.7
    
    # 인증 및 보안
    python-jose[cryptography]==3.3.0
    passlib[bcrypt]==1.7.4
    
    # 데이터 검증
    pydantic==2.4.2
    pydantic-settings==2.0.3
    email-validator==2.1.0
    
    # 파일 처리
    python-multipart==0.0.6

데이터베이스 시스템
-----------------

PostgreSQL
~~~~~~~~~~

**선택 기준:**

* **ACID 트랜잭션**: 데이터 일관성과 무결성 보장
* **JSON 지원**: 반구조화된 데이터 저장 및 쿼리 최적화
* **확장성**: PostGIS를 통한 지리정보시스템 확장 가능
* **성능**: 복잡한 쿼리와 대용량 데이터 처리에 최적화

컨테이너화 및 배포
-----------------

Docker Compose 구성
~~~~~~~~~~~~~~~~~~~

**인프라스트럭처 설계:**

.. code-block:: yaml

    version: '3.8'

    services:
      db:
        image: postgres:15-alpine
        container_name: revieweat_db
        restart: unless-stopped
        ports:
          - "5432:5432"
        environment:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: revieweat
        volumes:
          - pgdata:/var/lib/postgresql/data
          - ./database/init.sql:/docker-entrypoint-initdb.d/init.sql
        healthcheck:
          test: ["CMD-SHELL", "pg_isready -U postgres -d revieweat"]
          interval: 10s
          timeout: 5s
          retries: 5

      backend:
        build:
          context: ./backend
          dockerfile: Dockerfile
        container_name: revieweat_backend
        restart: unless-stopped
        ports:
          - "8000:8000"
        depends_on:
          db:
            condition: service_healthy
        volumes:
          - ./backend:/app
        environment:
          - DATABASE_URL=postgresql://postgres:postgres@db:5432/revieweat
          - SECRET_KEY=your-secure-secret-key-change-in-production
          - ACCESS_TOKEN_EXPIRE_MINUTES=30
        working_dir: /app
        command: uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload

    volumes:
      pgdata:

**컨테이너화 이점:**

* **환경 일관성**: 개발, 테스트, 운영 환경 간의 완전한 동일성 보장
* **확장성**: 수평적 스케일링과 로드 밸런싱 지원
* **격리성**: 서비스 간 의존성 분리를 통한 안정성 향상
* **배포 효율성**: 원클릭 배포와 롤백 기능