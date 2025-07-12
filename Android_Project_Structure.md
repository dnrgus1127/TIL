# Android 프로젝트 기본 폴더 구조

## 개요
Android 프로젝트는 특정한 디렉토리 구조를 따르며, 이는 Android Studio와 Gradle 빌드 시스템에 의해 정의됩니다.

## 프로젝트 루트 레벨 구조

```
MyAndroidProject/
├── app/                          # 메인 애플리케이션 모듈
├── build.gradle                  # 프로젝트 레벨 빌드 설정
├── gradle.properties            # Gradle 프로퍼티 파일
├── gradlew                      # Gradle 래퍼 스크립트 (Unix/Linux)
├── gradlew.bat                  # Gradle 래퍼 스크립트 (Windows)
├── local.properties            # 로컬 SDK 경로 설정
├── settings.gradle             # 프로젝트 설정 파일
└── gradle/                     # Gradle 래퍼 파일들
    └── wrapper/
        ├── gradle-wrapper.jar
        └── gradle-wrapper.properties
```

## app 모듈 구조

```
app/
├── build.gradle                 # 모듈 레벨 빌드 설정
├── proguard-rules.pro          # ProGuard 난독화 규칙
├── src/                        # 소스 코드 디렉토리
│   ├── main/                   # 메인 소스 세트
│   ├── test/                   # 단위 테스트 소스 세트
│   └── androidTest/            # 인스트루먼트 테스트 소스 세트
└── build/                      # 빌드 출력 디렉토리 (자동 생성)
```

## src/main 구조

```
src/main/
├── java/                       # Java/Kotlin 소스 코드
│   └── com/                    # 패키지 구조
│       └── example/
│           └── myapp/
│               ├── MainActivity.kt
│               ├── models/
│               ├── ui/
│               ├── utils/
│               └── ...
├── res/                        # 리소스 디렉토리
│   ├── layout/                 # XML 레이아웃 파일
│   ├── drawable/               # 이미지 및 드로어블 리소스
│   ├── values/                 # 값 리소스 (문자열, 색상 등)
│   ├── mipmap/                 # 앱 아이콘
│   └── ...
├── assets/                     # 애셋 파일 (선택사항)
└── AndroidManifest.xml         # 앱 매니페스트 파일
```

## res 폴더 상세 구조

```
res/
├── layout/                     # 레이아웃 XML 파일
│   ├── activity_main.xml
│   ├── fragment_home.xml
│   └── item_list.xml
├── drawable/                   # 이미지 및 드로어블 리소스
│   ├── ic_launcher.png
│   ├── button_background.xml
│   └── ...
├── drawable-hdpi/              # 고해상도 이미지
├── drawable-mdpi/              # 중해상도 이미지
├── drawable-xhdpi/             # 초고해상도 이미지
├── drawable-xxhdpi/            # 2배 초고해상도 이미지
├── drawable-xxxhdpi/           # 3배 초고해상도 이미지
├── values/                     # 기본 값 리소스
│   ├── strings.xml             # 문자열 리소스
│   ├── colors.xml              # 색상 리소스
│   ├── styles.xml              # 스타일 리소스
│   ├── themes.xml              # 테마 리소스
│   └── dimens.xml              # 크기 리소스
├── values-ko/                  # 한국어 번역 리소스
├── values-en/                  # 영어 번역 리소스
├── values-night/               # 다크 모드 리소스
├── mipmap-hdpi/                # 앱 아이콘 (고해상도)
├── mipmap-mdpi/                # 앱 아이콘 (중해상도)
├── mipmap-xhdpi/               # 앱 아이콘 (초고해상도)
├── mipmap-xxhdpi/              # 앱 아이콘 (2배 초고해상도)
├── mipmap-xxxhdpi/             # 앱 아이콘 (3배 초고해상도)
├── menu/                       # 메뉴 XML 파일
├── xml/                        # 기타 XML 파일
├── raw/                        # 원시 리소스 파일
└── font/                       # 폰트 파일
```

## 테스트 디렉토리 구조

```
src/
├── test/                       # 단위 테스트 (JVM에서 실행)
│   └── java/
│       └── com/example/myapp/
│           └── ExampleUnitTest.kt
└── androidTest/                # 인스트루먼트 테스트 (Android 기기에서 실행)
    └── java/
        └── com/example/myapp/
            └── ExampleInstrumentedTest.kt
```

## Java/Kotlin 소스 코드 일반적인 패키지 구조

```
java/com/example/myapp/
├── MainActivity.kt              # 메인 액티비티
├── ui/                         # UI 관련 클래스
│   ├── home/
│   │   ├── HomeFragment.kt
│   │   └── HomeViewModel.kt
│   ├── dashboard/
│   │   ├── DashboardFragment.kt
│   │   └── DashboardViewModel.kt
│   └── notifications/
│       ├── NotificationsFragment.kt
│       └── NotificationsViewModel.kt
├── data/                       # 데이터 계층
│   ├── repository/
│   ├── local/
│   │   └── database/
│   └── remote/
│       └── api/
├── model/                      # 데이터 모델
│   ├── User.kt
│   └── Post.kt
├── utils/                      # 유틸리티 클래스
│   ├── Constants.kt
│   └── DateUtils.kt
├── di/                         # 의존성 주입 (DI)
│   └── AppModule.kt
└── Application.kt              # 애플리케이션 클래스
```

## 주요 파일들 설명

### 1. build.gradle (프로젝트 레벨)
- 전체 프로젝트의 빌드 설정
- 플러그인 버전 관리
- 저장소 설정

### 2. build.gradle (모듈 레벨)
- 앱 모듈의 빌드 설정
- 컴파일 SDK 버전
- 의존성 관리
- 빌드 타입 설정

### 3. AndroidManifest.xml
- 앱의 메타데이터
- 권한 선언
- 액티비티, 서비스, 브로드캐스트 리시버 등록
- 앱 아이콘 및 테마 설정

### 4. gradle.properties
- Gradle 빌드 시스템 설정
- 메모리 설정
- 프로젝트 전역 프로퍼티

### 5. local.properties
- 로컬 환경 설정
- Android SDK 경로
- NDK 경로 (필요한 경우)

## 멀티 모듈 프로젝트 구조

```
MyAndroidProject/
├── app/                        # 메인 애플리케이션 모듈
├── core/                       # 공통 라이브러리 모듈
│   ├── network/
│   ├── database/
│   └── common/
├── feature/                    # 피처 모듈들
│   ├── login/
│   ├── profile/
│   └── settings/
├── build.gradle
├── settings.gradle
└── ...
```

## 참고사항

1. **네이밍 컨벤션**: 파일명과 리소스 이름은 소문자와 언더스코어를 사용
2. **패키지 구조**: 기능별로 패키지를 구분하는 것이 좋음
3. **리소스 한정자**: 화면 밀도, 언어, 테마 등에 따라 리소스 폴더 구분
4. **빌드 설정**: 개발/테스트/릴리즈 환경에 따른 빌드 설정 분리
5. **테스트 코드**: 단위 테스트와 UI 테스트를 적절히 분리

이 구조는 Android 개발의 모범 사례를 따르며, 프로젝트의 유지보수성과 확장성을 높이는 데 도움이 됩니다.