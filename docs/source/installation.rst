설치 및 실행 가이드
==================

시스템 요구사항
--------------

개발 환경 구성을 위해 다음 소프트웨어들이 필요합니다:

**필수 요구사항:**

* **Git**: 버전 관리 및 소스코드 클론
* **Docker & Docker Compose**: 백엔드 및 데이터베이스 컨테이너 실행
* **Flutter SDK**: 3.0 이상 (모바일 앱 개발)
* **Android Studio**: Android 개발 및 에뮬레이터 실행
* **VS Code** (권장): 통합 개발 환경

**운영체제 지원:**

* Windows 10/11
* macOS 10.14 이상
* Ubuntu 18.04 이상

백엔드 환경 구성
--------------

1. 저장소 클론
~~~~~~~~~~~~~

.. code-block:: bash

   git clone https://github.com/eonjong0218/revieweat.git
   cd revieweat

2. Docker 설치 확인
~~~~~~~~~~~~~~~~~

Docker와 Docker Compose가 설치되어 있는지 확인합니다:

.. code-block:: bash

   docker --version
   docker-compose --version

**Docker 설치가 필요한 경우:**

* **Windows/Mac**: `Docker Desktop <https://www.docker.com/products/docker-desktop>`_ 설치
* **Linux**: 패키지 매니저를 통해 설치

.. code-block:: bash

   # Ubuntu/Debian
   sudo apt-get update
   sudo apt-get install docker.io docker-compose

3. 환경 변수 설정
~~~~~~~~~~~~~~~

프로젝트 루트에 ``.env`` 파일을 생성하고 다음 내용을 추가합니다:

.. code-block:: bash

   # .env 파일
   DATABASE_URL=postgresql://postgres:postgres@localhost:5432/revieweat
   SECRET_KEY=your-super-secret-key-change-in-production
   ACCESS_TOKEN_EXPIRE_MINUTES=30
   GOOGLE_MAPS_API_KEY=your-google-maps-api-key

**Google Maps API 키 발급:**

1. `Google Cloud Console <https://console.cloud.google.com/>`_ 접속
2. 새 프로젝트 생성 또는 기존 프로젝트 선택
3. "APIs & Services" → "Credentials" 이동
4. "Create Credentials" → "API key" 선택
5. 생성된 API 키를 ``.env`` 파일에 추가

4. 백엔드 서비스 실행
~~~~~~~~~~~~~~~~~~

Docker Compose를 사용하여 백엔드와 데이터베이스를 실행합니다:

.. code-block:: bash

   # 백그라운드에서 서비스 실행
   docker-compose up -d

   # 로그 확인
   docker-compose logs -f

**서비스 상태 확인:**

.. code-block:: bash

   # 컨테이너 상태 확인
   docker-compose ps

   # API 서버 동작 확인
   curl http://localhost:8000/docs

성공적으로 실행되면 다음 서비스들이 사용 가능합니다:

* **API 서버**: http://localhost:8000
* **API 문서**: http://localhost:8000/docs
* **PostgreSQL**: localhost:5432

프론트엔드 환경 구성
------------------

1. Flutter SDK 설치
~~~~~~~~~~~~~~~~~~

**Windows:**

1. `Flutter SDK <https://docs.flutter.dev/get-started/install/windows>`_ 다운로드
2. 압축 해제 후 PATH 환경변수에 ``flutter/bin`` 추가
3. 명령 프롬프트에서 ``flutter doctor`` 실행

**macOS:**

.. code-block:: bash

   # Homebrew 사용
   brew install flutter

**Linux:**

.. code-block:: bash

   # Snap 사용
   sudo snap install flutter --classic

2. Android Studio 설치 및 설정
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Android Studio 설치:**

1. `Android Studio <https://developer.android.com/studio>`_ 다운로드 및 설치
2. 초기 설정 마법사를 통해 Android SDK 설치
3. "Tools" → "AVD Manager"에서 가상 디바이스 생성

**권장 가상 디바이스 설정:**

* **디바이스**: Pixel 4 또는 Pixel 6
* **시스템 이미지**: API 30 (Android 11) 이상
* **RAM**: 4GB 이상

3. Flutter 개발 환경 검증
~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   # Flutter 환경 진단
   flutter doctor

   # 연결된 디바이스 확인
   flutter devices

**예상 출력:**

.. code-block:: text

   Doctor summary (to see all details, run flutter doctor -v):
   [✓] Flutter (Channel stable, 3.16.0)
   [✓] Android toolchain - develop for Android devices
   [✓] Chrome - develop for the web
   [✓] Android Studio (version 2023.1)
   [✓] VS Code (version 1.84.0)

4. 프론트엔드 의존성 설치
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   # frontend 디렉토리로 이동
   cd frontend

   # Flutter 패키지 설치
   flutter pub get

   # 코드 생성 (필요한 경우)
   flutter packages pub run build_runner build

5. 앱 실행
~~~~~~~~~

**Android 에뮬레이터에서 실행:**

.. code-block:: bash

   # 에뮬레이터 실행 (Android Studio에서 미리 실행)
   # 또는 명령줄에서:
   emulator -avd Pixel_4_API_30

   # Flutter 앱 실행
   flutter run

**실제 디바이스에서 실행:**

1. Android 디바이스에서 "개발자 옵션" 활성화
2. "USB 디버깅" 활성화
3. USB로 컴퓨터와 연결

.. code-block:: bash

   # 연결된 디바이스 확인
   flutter devices

   # 앱 실행
   flutter run

전체 시스템 실행
--------------

모든 구성요소를 실행하는 순서:

1. **백엔드 서비스 시작:**

.. code-block:: bash

   docker-compose up -d

2. **Android 에뮬레이터 실행:**

   Android Studio → AVD Manager → 가상 디바이스 실행

3. **Flutter 앱 실행:**

.. code-block:: bash

   cd frontend
   flutter run

4. **동작 확인:**

   * 앱에서 회원가입/로그인 테스트
   * 지도 기능 동작 확인
   * 리뷰 작성 기능 테스트

문제 해결
--------

자주 발생하는 문제와 해결 방법:

**Flutter 관련 문제:**

.. code-block:: bash

   # 캐시 정리
   flutter clean
   flutter pub get

   # 빌드 문제 해결
   flutter pub deps
   flutter doctor -v

**Docker 관련 문제:**

.. code-block:: bash

   # 컨테이너 재시작
   docker-compose down
   docker-compose up -d

   # 로그 확인
   docker-compose logs backend
   docker-compose logs db

**Android 에뮬레이터 문제:**

* **느린 성능**: AVD 설정에서 RAM을 4GB 이상으로 증가
* **연결 실패**: "Cold Boot Now"로 에뮬레이터 재시작
* **API 키 오류**: ``.env`` 파일의 Google Maps API 키 확인

**포트 충돌 문제:**

.. code-block:: bash

   # 사용 중인 포트 확인
   netstat -tulpn | grep :8000
   netstat -tulpn | grep :5432

   # 프로세스 종료 후 재시작
   docker-compose down
   docker-compose up -d

개발 환경 권장사항
----------------

**VS Code 확장 프로그램:**

* Flutter
* Dart
* Docker
* Python
* PostgreSQL

**유용한 명령어:**

.. code-block:: bash

   # 핫 리로드로 개발
   flutter run

   # 릴리즈 빌드
   flutter build apk --release

   # 백엔드 로그 실시간 확인
   docker-compose logs -f backend

   # 데이터베이스 접속
   docker-compose exec db psql -U postgres -d revieweat
