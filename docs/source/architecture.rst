프로젝트 아키텍처
================

전체 구조 개요
--------------

ReviewEat 프로젝트는 프론트엔드, 백엔드, 데이터베이스로 구성된 3계층 아키텍처를 채택하고 있습니다.

디렉토리 구조
------------

.. code-block:: text

    Revieweat/
    ├── backend/                 
    │   ├── app/                   
    │   │   ├── __init__.py      
    │   │   ├── auth.py          
    │   │   ├── config.py          
    │   │   ├── crud.py             
    │   │   ├── database.py         
    │   │   ├── dependencies.py    
    │   │   ├── main.py             
    │   │   ├── models.py           
    │   │   └── schemas.py         
    │   ├── Dockerfile             
    │   └── requirements.txt        
    │
    ├── database/                   
    │   └── init.sql                
    │
    ├── frontend/                  
    │   ├── lib/                    
    │   │   ├── main.dart          
    │   │   └── screens/           
    │   │       ├── calendar_screen.dart      
    │   │       ├── home_screen.dart           
    │   │       ├── login_screen.dart         
    │   │       ├── profile_screen.dart       
    │   │       ├── register_screen.dart       
    │   │       ├── review_detail_screen.dart  
    │   │       ├── review_final_screen.dart   
    │   │       ├── review_place_search_screen.dart 
    │   │       ├── review_second_screen.dart 
    │   │       ├── review_success_screen.dart 
    │   │       ├── search_result_screen.dart 
    │   │       ├── search_screen.dart       
    │   │       └── splash_screen.dart         
    │   ├── pubspec.yaml           
    │   └── android/               
    │
    ├── docker-compose.yml     
    ├── .env                      
    └── README.md                

백엔드 아키텍처
--------------

FastAPI 기반 모듈 구조
~~~~~~~~~~~~~~~~~~~~~

**main.py**
  애플리케이션의 진입점으로, FastAPI 인스턴스 생성 및 라우터 등록을 담당합니다.

**auth.py**
  JWT 토큰 기반 인증 시스템과 사용자 권한 관리 로직을 포함합니다.

**models.py**
  SQLAlchemy ORM을 사용한 데이터베이스 테이블 모델 정의가 포함되어 있습니다.

**schemas.py**
  Pydantic을 사용한 API 요청/응답 데이터 검증 스키마를 정의합니다.

**crud.py**
  데이터베이스 CRUD(Create, Read, Update, Delete) 작업을 처리하는 함수들이 정의되어 있습니다.

프론트엔드 아키텍처
-----------------

Flutter 화면 구조
~~~~~~~~~~~~~~~~

**인증 관련 화면**
  * ``login_screen.dart``: 사용자 로그인
  * ``register_screen.dart``: 신규 사용자 회원가입
  * ``splash_screen.dart``: 앱 시작 화면

**메인 기능 화면**
  * ``home_screen.dart``: 지도 기반 메인 화면
  * ``search_screen.dart``: 음식점 검색 인터페이스
  * ``search_result_screen.dart``: 검색 결과 표시

**리뷰 관련 화면**
  * ``review_place_search_screen.dart``: 리뷰 작성을 위한 장소 선택
  * ``review_second_screen.dart``: 리뷰 기본 정보 입력
  * ``review_final_screen.dart``: 리뷰 상세 내용 작성
  * ``review_success_screen.dart``: 리뷰 작성 완료 확인
  * ``review_detail_screen.dart``: 작성된 리뷰 상세 보기

**개인 관리 화면**
  * ``calendar_screen.dart``: 달력형 리뷰 관리
  * ``profile_screen.dart``: 사용자 프로필 및 설정

데이터 흐름
----------

.. code-block:: text

    [Flutter App] ←→ [FastAPI Backend] ←→ [PostgreSQL Database]
         ↓                    ↓
    [Google Maps API]    [JWT Authentication]

1. **사용자 인터페이스**: Flutter 앱에서 사용자 입력 수집
2. **API 통신**: HTTP 요청을 통해 백엔드 서버와 통신
3. **인증 처리**: JWT 토큰을 통한 사용자 인증 및 권한 확인
4. **데이터 처리**: FastAPI에서 비즈니스 로직 처리
5. **데이터 저장**: PostgreSQL 데이터베이스에 정보 영구 저장
6. **지도 서비스**: Google Maps API를 통한 위치 정보 처리