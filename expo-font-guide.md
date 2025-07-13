# Expo에서 폰트 처리하기: 완벽 가이드

## 목차
- [개요](#개요)
- [폰트를 사용하는 두 가지 방법](#폰트를-사용하는-두-가지-방법)
- [지원되는 폰트 포맷](#지원되는-폰트-포맷)
- [방법 1: expo-font Config Plugin (권장)](#방법-1-expo-font-config-plugin-권장)
- [방법 2: useFonts Hook (런타임 로딩)](#방법-2-usefonts-hook-런타임-로딩)
- [Google Fonts 사용하기](#google-fonts-사용하기)
- [플랫폼별 고려사항](#플랫폼별-고려사항)
- [성능 최적화](#성능-최적화)
- [베스트 프랙티스](#베스트-프랙티스)
- [문제 해결](#문제-해결)
- [Todo List](#todo-list)

## 개요

Expo에서 폰트를 처리하는 것은 앱의 브랜딩과 사용자 경험에 중요한 요소입니다. Android와 iOS는 각각 고유한 플랫폼 폰트를 제공하지만, 일관된 사용자 경험과 브랜딩 강화를 위해 커스텀 폰트를 사용할 수 있습니다.

### 주요 특징
- **크로스 플랫폼 지원**: Android, iOS, Web 모두 지원
- **다양한 폰트 소스**: 로컬 파일, Google Fonts, 웹 URL
- **성능 최적화**: 여러 로딩 전략 제공
- **TypeScript 지원**: 완전한 타입 안전성

## 폰트를 사용하는 두 가지 방법

Expo에서는 두 가지 주요 방법으로 폰트를 사용할 수 있습니다:

### 1. **expo-font Config Plugin** (권장)
- 폰트를 네이티브 코드에 임베딩
- 앱 시작 시 즉시 사용 가능
- 일관된 성능과 안정성

### 2. **useFonts Hook**
- 런타임에 비동기적으로 폰트 로딩
- Expo Go와 호환
- 동적 폰트 로딩 가능

## 지원되는 폰트 포맷

### 공식 지원 포맷
- **OTF** (OpenType Font) - 권장
- **TTF** (TrueType Font)

### 포맷 선택 가이드
- OTF와 TTF 둘 다 있는 경우 **OTF 선택**
- OTF가 TTF보다 파일 크기가 작음
- 특정 상황에서 OTF가 더 나은 렌더링 품질 제공

### Variable Fonts
- 모든 플랫폼에서 완전 지원되지 않음
- 완전한 플랫폼 지원을 위해서는 static fonts 사용 권장
- 필요시 fontTools로 특정 axis 구성을 추출하여 별도 파일로 저장

## 방법 1: expo-font Config Plugin (권장)

### 장점
- ✅ 앱 시작 시 폰트 즉시 사용 가능
- ✅ 비동기 로딩 코드 불필요
- ✅ 모든 디바이스에서 일관된 가용성
- ✅ 네이티브 성능 최적화

### 단점
- ❌ Expo Go에서 작동하지 않음 (Development Build 필요)
- ❌ 폰트 변경 시 새로운 빌드 필요

### 설정 방법

#### 1단계: 폰트 파일 준비
```bash
# 프로젝트 루트에 폰트 디렉토리 생성
mkdir -p assets/fonts

# 폰트 파일들을 assets/fonts/ 디렉토리에 복사
# 예: Inter-Bold.ttf, Inter-Regular.ttf
```

#### 2단계: expo-font 라이브러리 설치
```bash
npx expo install expo-font
```

#### 3단계: app.json 설정
```json
{
  "expo": {
    "plugins": [
      [
        "expo-font",
        {
          "fonts": [
            "./assets/fonts/FiraSans-MediumItalic.ttf"
          ],
          "android": {
            "fonts": [
              {
                "fontFamily": "Inter",
                "fontDefinitions": [
                  {
                    "path": "./assets/fonts/Inter-BoldItalic.ttf",
                    "weight": 700,
                    "style": "italic"
                  },
                  {
                    "path": "./assets/fonts/Inter-Bold.ttf",
                    "weight": 700
                  }
                ]
              }
            ]
          },
          "ios": {
            "fonts": [
              "./assets/fonts/Inter-Bold.ttf",
              "./assets/fonts/Inter-BoldItalic.ttf"
            ]
          }
        }
      ]
    ]
  }
}
```

#### 4단계: Development Build 생성
```bash
# Development Build 생성 및 설치
npx expo run:ios
# 또는
npx expo run:android
```

#### 5단계: 코드에서 사용
```tsx
import React from 'react';
import { Text, View } from 'react-native';

export default function App() {
  return (
    <View>
      <Text style={{ fontFamily: 'Inter', fontWeight: '700' }}>
        Inter Bold
      </Text>
      <Text style={{ 
        fontFamily: 'Inter', 
        fontWeight: '700', 
        fontStyle: 'italic' 
      }}>
        Inter Bold Italic
      </Text>
      <Text style={{ fontFamily: 'FiraSans-MediumItalic' }}>
        Fira Sans Medium Italic
      </Text>
    </View>
  );
}
```

### 폰트 패밀리 이름 결정하기

#### PostScript 이름이란?
- Adobe PostScript 표준을 따르는 폰트 고유 식별자
- 운영체제와 앱에서 폰트를 참조하는 데 사용
- 폰트의 display name과는 다름

#### 플랫폼별 차이점
- **파일 경로 배열 방식**: Android에서는 파일명(확장자 제외)이 폰트 패밀리명이 됨, iOS에서는 폰트 파일 자체에서 읽음
- **객체 문법 사용 시**: "Family Name" 제공 (Font Book app, fontdrop.info 등에서 확인 가능)

## 방법 2: useFonts Hook (런타임 로딩)

### 장점
- ✅ 모든 Expo SDK 버전과 호환
- ✅ Expo Go에서 작동
- ✅ 동적 폰트 로딩 가능
- ✅ 웹 URL에서 직접 로딩 가능

### 단점
- ❌ 로딩 시간 필요
- ❌ 네트워크 연결 상태에 따른 가용성
- ❌ 추가적인 로딩 상태 관리 필요

### 설정 방법

#### 1단계: 라이브러리 설치
```bash
npx expo install expo-font expo-splash-screen
```

#### 2단계: 루트 레이아웃에서 폰트 로딩
```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';
import * as SplashScreen from 'expo-splash-screen';
import { useEffect } from 'react';

// 자동 숨김 방지
SplashScreen.preventAutoHideAsync();

export default function RootLayout() {
  const [loaded, error] = useFonts({
    'Inter-Black': require('./assets/fonts/Inter-Black.otf'),
    'CustomFont-Regular': require('./assets/fonts/CustomFont-Regular.ttf'),
  });

  useEffect(() => {
    if (loaded || error) {
      SplashScreen.hideAsync();
    }
  }, [loaded, error]);

  if (!loaded && !error) {
    return null;
  }

  return (
    // 앱 콘텐츠
  );
}
```

#### 3단계: 컴포넌트에서 사용
```tsx
import React from 'react';
import { Text, StyleSheet } from 'react-native';

export default function MyComponent() {
  return (
    <Text style={styles.text}>
      Custom Font Text
    </Text>
  );
}

const styles = StyleSheet.create({
  text: {
    fontFamily: 'Inter-Black',
    fontSize: 24,
  },
});
```

### 로딩 상태 처리 (Advanced)
```tsx
import { useFonts } from 'expo-font';
import { ActivityIndicator, View, Text } from 'react-native';

export default function App() {
  const [fontsLoaded] = useFonts({
    'CustomFont': require('./assets/fonts/CustomFont.ttf'),
  });

  if (!fontsLoaded) {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <ActivityIndicator size="large" color="#0000ff" />
        <Text>폰트 로딩 중...</Text>
      </View>
    );
  }

  return (
    // 메인 앱 UI
  );
}
```

## Google Fonts 사용하기

Expo는 Google Fonts에 대한 일급 지원을 제공합니다.

### Config Plugin과 함께 사용

#### 1단계: 패키지 설치
```bash
npx expo install expo-font @expo-google-fonts/inter
```

#### 2단계: app.json 설정
```json
{
  "plugins": [
    [
      "expo-font",
      {
        "fonts": [
          "node_modules/@expo-google-fonts/inter/900Black/Inter_900Black.ttf"
        ]
      }
    ]
  ]
}
```

#### 3단계: 플랫폼별 사용
```tsx
import { Platform, Text } from 'react-native';

export default function GoogleFontText() {
  return (
    <Text
      style={{
        fontFamily: Platform.select({
          android: 'Inter_900Black',
          ios: 'Inter-Black',
        }),
        fontSize: 24,
      }}>
      Google Font Text
    </Text>
  );
}
```

### useFonts Hook과 함께 사용

#### 1단계: 패키지 설치
```bash
npx expo install @expo-google-fonts/inter expo-font expo-splash-screen
```

#### 2단계: 폰트 로딩
```tsx
import { Inter_900Black, useFonts } from '@expo-google-fonts/inter';
import * as SplashScreen from 'expo-splash-screen';
import { useEffect } from 'react';

SplashScreen.preventAutoHideAsync();

export default function RootLayout() {
  const [loaded, error] = useFonts({
    Inter_900Black,
  });

  useEffect(() => {
    if (loaded || error) {
      SplashScreen.hideAsync();
    }
  }, [loaded, error]);

  if (!loaded && !error) {
    return null;
  }

  return (
    // 앱 콘텐츠
  );
}
```

#### 3단계: 사용
```tsx
<Text style={{ fontFamily: 'Inter_900Black' }}>
  Google Font Text
</Text>
```

## 플랫폼별 고려사항

### 기본 플랫폼 폰트
- **Android**: Roboto
- **iOS**: SF Pro
- **Web**: 브라우저 기본 폰트

### 플랫폼별 폰트 선택
```tsx
import { Platform, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  text: {
    fontFamily: Platform.select({
      ios: 'System',
      android: 'Roboto',
      web: 'System',
      default: 'System',
    }),
    fontWeight: '400',
  },
});
```

### Web에서의 폰트 처리
```tsx
// 웹에서만 특별한 처리가 필요한 경우
import { Platform } from 'react-native';

const fontFamily = Platform.select({
  web: 'Inter, -apple-system, BlinkMacSystemFont, sans-serif',
  default: 'Inter',
});
```

## 성능 최적화

### 1. 폰트 파일 크기 최적화
- 필요한 글리프만 포함하는 서브셋 생성
- 불필요한 폰트 weight 제거
- OTF 형식 사용 (TTF보다 작은 크기)

### 2. 로딩 전략
```tsx
// 시스템 폰트를 기본값으로 사용
const styles = StyleSheet.create({
  fallbackText: {
    fontFamily: Platform.select({
      ios: 'System',
      android: 'Roboto',
      default: 'System',
    }),
  },
  customText: {
    fontFamily: 'CustomFont-Regular',
    // 폰트 로딩 실패 시 시스템 폰트로 fallback
  },
});
```

### 3. 메모리 관리
```tsx
// 폰트 프리로딩
import { useFonts } from 'expo-font';

const fontMap = {
  'Inter-Regular': require('./assets/fonts/Inter-Regular.otf'),
  'Inter-Bold': require('./assets/fonts/Inter-Bold.otf'),
  // 필요한 폰트만 로드
};

export default function App() {
  const [loaded] = useFonts(fontMap);
  
  // ...
}
```

### 4. Vector Icons 최적화
```tsx
// @expo/vector-icons 초기 로딩 처리
import { useFonts } from 'expo-font';
import Ionicons from '@expo/vector-icons/Ionicons';

export default function RootLayout() {
  useFonts([
    require('./assets/fonts/Inter-Black.otf'), 
    Ionicons.font
  ]);

  return (
    // 이제 Ionicons를 즉시 사용 가능
  );
}
```

## 베스트 프랙티스

### 1. 폰트 파일 네이밍
```
// 권장 네이밍 규칙
Inter-Regular.otf
Inter-Bold.otf
Inter-BoldItalic.otf
Roboto-Light.ttf
Roboto-Medium.ttf
```

### 2. 타입 안전성
```tsx
// 폰트 상수 정의
export const FONTS = {
  regular: 'Inter-Regular',
  bold: 'Inter-Bold',
  light: 'Inter-Light',
} as const;

// 타입 정의
type FontFamily = typeof FONTS[keyof typeof FONTS];

// 사용
const styles = StyleSheet.create({
  text: {
    fontFamily: FONTS.regular,
  },
});
```

### 3. 중앙화된 폰트 관리
```tsx
// fonts.ts
export const fontConfig = {
  weights: {
    light: '300',
    regular: '400',
    medium: '500',
    bold: '700',
  },
  families: {
    primary: 'Inter',
    secondary: 'Roboto',
  },
} as const;

// 사용
<Text style={{
  fontFamily: fontConfig.families.primary,
  fontWeight: fontConfig.weights.bold,
}}>
  Bold Text
</Text>
```

### 4. 접근성 고려
```tsx
import { useWindowDimensions } from 'react-native';

export default function AccessibleText({ children }) {
  const { fontScale } = useWindowDimensions();
  
  return (
    <Text style={[
      styles.text, 
      { fontSize: 16 * fontScale }
    ]}>
      {children}
    </Text>
  );
}
```

### 5. 다크 모드 지원
```tsx
import { useColorScheme } from 'react-native';

export default function ThemedText({ children }) {
  const colorScheme = useColorScheme();
  
  return (
    <Text style={[
      styles.text,
      {
        color: colorScheme === 'dark' ? '#fff' : '#000',
        fontFamily: 'Inter-Regular',
      }
    ]}>
      {children}
    </Text>
  );
}
```

## 문제 해결

### 1. 폰트가 렌더링되지 않는 경우

**원인 및 해결책:**
- **폰트 패밀리명 불일치**: PostScript 이름 확인
- **파일 경로 오류**: 상대 경로 재확인
- **지원되지 않는 포맷**: OTF/TTF 확인
- **로딩 완료 전 렌더링**: 로딩 상태 체크

```tsx
// 디버깅용 폰트 체크
const [loaded, error] = useFonts({
  'CustomFont': require('./assets/fonts/CustomFont.otf'),
});

if (error) {
  console.error('Font loading error:', error);
}

if (!loaded) {
  console.log('Fonts not loaded yet');
  return <Text>Loading fonts...</Text>;
}
```

### 2. 성능 문제

**원인 및 해결책:**
- **큰 폰트 파일**: 서브셋 생성 또는 압축
- **너무 많은 폰트**: 필요한 것만 로드
- **동기 로딩**: 비동기 로딩으로 변경

```tsx
// 폰트 파일 크기 최적화
const fontMap = useMemo(() => ({
  // 필요한 폰트만 포함
  'Inter-Regular': require('./assets/fonts/Inter-Regular.otf'),
  // 'Inter-Light': require('./assets/fonts/Inter-Light.otf'), // 사용하지 않으면 제거
}), []);
```

### 3. 플랫폼별 차이

**Android/iOS 차이점 해결:**
```tsx
// 플랫폼별 폰트 이름 매핑
const getFontFamily = (weight: string) => {
  return Platform.select({
    ios: `Inter-${weight}`,
    android: `Inter_${weight}`,
    web: 'Inter',
  });
};
```

### 4. Development Build vs Production 차이

**해결책:**
```json
// app.json에 production 빌드 설정 추가
{
  "expo": {
    "plugins": [
      [
        "expo-font",
        {
          "fonts": ["./assets/fonts/Inter-Black.otf"]
        }
      ]
    ]
  }
}
```

### 5. Web에서의 CORS 문제

**원인:** 원격 폰트 로딩 시 CORS 설정 부족

**해결책:**
```tsx
// 로컬 에셋 사용 (권장)
const [loaded] = useFonts({
  'CustomFont': require('./assets/fonts/CustomFont.otf'),
});

// 또는 CORS가 설정된 URL 사용
const [loaded] = useFonts({
  'Inter-SemiBoldItalic': 'https://rsms.me/inter/font-files/Inter-SemiBoldItalic.otf?v=3.12',
});
```

### 6. Metro 번들러 설정

**OTF/TTF 외 포맷 지원:**
```js
// metro.config.js
const { getDefaultConfig } = require('expo/metro-config');

const config = getDefaultConfig(__dirname);

// 추가 에셋 타입 지원
config.resolver.assetExts.push('woff', 'woff2');

module.exports = config;
```

## Todo List

### 🚀 프로젝트 시작 단계
- [ ] 프로젝트 요구사항 분석
  - [ ] 필요한 폰트 종류 및 weight 결정
  - [ ] 타겟 플랫폼 확인 (iOS, Android, Web)
  - [ ] 브랜딩 가이드라인 검토
- [ ] 폰트 파일 확보
  - [ ] 디자이너로부터 폰트 파일 받기 (.otf/.ttf)
  - [ ] 라이센스 확인 (상업적 사용 가능 여부)
  - [ ] Google Fonts 사용 여부 결정

### 📁 폰트 파일 준비
- [ ] 폰트 파일 정리
  - [ ] `assets/fonts/` 디렉토리 생성
  - [ ] 폰트 파일을 적절한 이름으로 정리
  - [ ] 파일 크기 최적화 (서브셋 생성 고려)
- [ ] 폰트 메타데이터 확인
  - [ ] PostScript 이름 확인 (Font Book 등 사용)
  - [ ] 지원되는 weight 및 style 파악
  - [ ] 글리프 지원 범위 확인

### ⚙️ 기본 설정
- [ ] 라이브러리 설치
  - [ ] `npx expo install expo-font` 실행
  - [ ] Google Fonts 사용 시: `npx expo install @expo-google-fonts/[font-name]`
  - [ ] Splash Screen: `npx expo install expo-splash-screen`
- [ ] 폰트 로딩 방법 선택
  - [ ] Config Plugin vs useFonts Hook 결정
  - [ ] Development Build 생성 여부 결정

### 🔧 Config Plugin 설정 (권장)
- [ ] app.json 설정
  - [ ] expo-font plugin 추가
  - [ ] 폰트 파일 경로 설정
  - [ ] Android/iOS별 설정 구성
- [ ] Development Build 생성
  - [ ] `npx expo run:ios` 실행
  - [ ] `npx expo run:android` 실행
  - [ ] 디바이스/시뮬레이터에서 테스트

### 🔄 Runtime Loading 설정 (useFonts)
- [ ] 루트 레이아웃 설정
  - [ ] `app/_layout.tsx`에 useFonts 구현
  - [ ] SplashScreen 설정
  - [ ] 로딩 상태 처리
- [ ] 에러 핸들링
  - [ ] 폰트 로딩 실패 시 fallback 처리
  - [ ] 네트워크 에러 처리

### 🎨 스타일링 구현
- [ ] 폰트 상수 정의
  - [ ] `constants/fonts.ts` 파일 생성
  - [ ] 타입 안전한 폰트 정의
  - [ ] Weight 및 스타일 매핑
- [ ] 스타일시트 작성
  - [ ] 공통 텍스트 스타일 정의
  - [ ] 플랫폼별 스타일 처리
  - [ ] 반응형 폰트 크기 설정

### 📱 플랫폼별 최적화
- [ ] iOS 최적화
  - [ ] PostScript 이름 확인
  - [ ] Info.plist 설정 (필요시)
  - [ ] SF Symbols 호환성 체크
- [ ] Android 최적화
  - [ ] 폰트 패밀리명 확인
  - [ ] Material Design 가이드라인 준수
  - [ ] 시스템 폰트 fallback 설정
- [ ] Web 최적화
  - [ ] CORS 설정 확인
  - [ ] 웹 폰트 로딩 전략
  - [ ] CSS fallback 설정

### 🧪 테스트 및 검증
- [ ] 기능 테스트
  - [ ] 모든 타겟 디바이스에서 폰트 렌더링 확인
  - [ ] 다양한 화면 크기에서 테스트
  - [ ] 다크 모드 지원 확인
- [ ] 성능 테스트
  - [ ] 앱 시작 시간 측정
  - [ ] 메모리 사용량 확인
  - [ ] 폰트 로딩 시간 체크
- [ ] 접근성 테스트
  - [ ] 시스템 폰트 크기 설정 대응
  - [ ] 스크린 리더 호환성
  - [ ] 고대비 모드 지원

### 🔍 문제 해결 및 디버깅
- [ ] 디버깅 도구 설정
  - [ ] React Native Debugger 활용
  - [ ] 폰트 로딩 상태 로깅
  - [ ] Flipper를 통한 성능 모니터링
- [ ] 일반적인 문제 해결
  - [ ] 폰트가 표시되지 않는 문제
  - [ ] 잘못된 폰트 weight 표시
  - [ ] 플랫폼별 렌더링 차이

### 📚 문서화 및 가이드라인
- [ ] 개발팀 가이드 작성
  - [ ] 폰트 사용 가이드라인 문서
  - [ ] 코드 스타일 가이드 업데이트
  - [ ] 디자인 시스템에 폰트 정보 추가
- [ ] 유지보수 계획
  - [ ] 폰트 업데이트 프로세스 정의
  - [ ] 라이센스 갱신 일정 관리
  - [ ] 성능 모니터링 계획

### 🚀 배포 및 운영
- [ ] Production 빌드 준비
  - [ ] 폰트 임베딩 확인
  - [ ] 번들 크기 최적화
  - [ ] 에셋 압축 설정
- [ ] 앱 스토어 제출
  - [ ] iOS App Store 가이드라인 준수
  - [ ] Google Play Store 요구사항 확인
  - [ ] 폰트 라이센스 정보 포함
- [ ] 모니터링 및 분석
  - [ ] 폰트 로딩 실패율 추적
  - [ ] 사용자 피드백 수집
  - [ ] 성능 지표 모니터링

### 🔄 지속적인 개선
- [ ] 정기적인 검토
  - [ ] 폰트 사용 패턴 분석
  - [ ] 새로운 폰트 요구사항 검토
  - [ ] Expo SDK 업데이트에 따른 변경사항 적용
- [ ] 최적화 작업
  - [ ] 불필요한 폰트 제거
  - [ ] 캐싱 전략 개선
  - [ ] 번들 크기 지속적 모니터링

---

이 가이드를 따라하면 Expo에서 폰트를 효과적으로 관리하고 사용할 수 있습니다. 각 단계를 체크하면서 프로젝트에 맞는 폰트 전략을 구축해보세요!