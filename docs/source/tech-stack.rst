기술 스택
=========

전체 아키텍처
-------------

ReviewEat은 모던한 기술 스택을 기반으로 구축되었습니다.

.. code-block:: text

    ┌─────────────────┐    ┌─────────────────┐
    │   Flutter App   │    │  Google Maps    │
    │   (Frontend)    │◄──►│      API        │
    └─────────────────┘    └─────────────────┘
             │
             ▼
    ┌─────────────────┐
    │   FastAPI       │
    │   (Backend)     │
    └─────────────────┘
             │
             ▼
    ┌─────────────────┐
    │  PostgreSQL     │
    │   (Database)    │
    └─────────────────┘

Frontend
--------

Flutter
~~~~~~~

**선택 이유:**

* 크로스 플랫폼 개발로 iOS, Android 동시 지원
* 네이티브 수준의 성능
* 풍부한 UI 컴포넌트와 애니메이션
* Google의 지속적인 지원과 업데이트

**주요 패키지:**

* ``google_maps_flutter``: 지도 기능 구현
* ``http``: API 통신
* ``shared_preferences``: 로컬 데이터 저장
* ``image_picker``: 이미지 선택 및 업로드
* ``geolocator``: 위치 서비스

Map API
-------

Google Maps API
~~~~~~~~~~~~~~~

**사용 서비스:**

* **Places API**: 음식점 검색 및 상세 정보
* **Maps SDK**: 지도 렌더링 및 마커 표시
* **Geocoding API**: 주소-좌표 변환

**장점:**

* 정확하고 최신의 장소 정보
* 전 세계 커버리지
* 풍부한 메타데이터 (영업시간, 리뷰, 사진 등)

Backend
-------

FastAPI
~~~~~~~

**선택 이유:**

* 높은 성능과 빠른 개발 속도
* 자동 API 문서 생성 (Swagger/OpenAPI)
* 타입 힌트 기반 검증
* 비동기 처리 지원

**주요 기능:**

* RESTful API 엔드포인트
* JWT 기반 인증
* 파일 업로드 처리
* 데이터 검증 및 직렬화

**사용 라이브러리:**

.. code-block:: python

    # 주요 의존성
    fastapi==0.104.1
    uvicorn==0.24.0
    sqlalchemy==2.0.23
    alembic==1.12.1
    python-jose==3.3.0
    passlib==1.7.4
    python-multipart==0.0.6

Database
--------

PostgreSQL
~~~~~~~~~~

**선택 이유:**

* 강력한 관계형 데이터베이스
* JSON 데이터 타입 지원
* 지리 정보 시스템 (PostGIS) 확장 가능
* 높은 안정성과 성능


Docker Compose
~~~~~~~~~~~~~~

**컨테이너 구성:**

.. code-block:: yaml

    version: '3.8'
    services:
      backend:
        build: ./backend
        ports:
          - "8000:8000"
        environment:
          - DATABASE_URL=postgresql://user:password@db:5432/revieweat
        depends_on:
          - db

      db:
        image: postgres:15
        environment:
          - POSTGRES_DB=revieweat
          - POSTGRES_USER=user
          - POSTGRES_PASSWORD=password
        volumes:
          - postgres_data:/var/lib/postgresql/data

**장점:**

* 환경 일관성 보장
* 쉬운 배포 및 스케일링
* 개발/운영 환경 통일
