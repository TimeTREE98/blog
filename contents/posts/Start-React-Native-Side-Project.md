---
title: "React-Native 사이드 프로젝트를 시작하다."
description:
date: 2021-08-21
update: 2021-08-21
tags:
  - react-native
series:
---

## React-Native?

메인 개발 Stack은 NestJS Back-End인데, 요즘 그냥 Front에 꽂혔다.

하던 게 JavaScript 니까 자연스레 React > React Native로 넘어왔다.

React Native는 문서화도 되게 잘 되어있고, 많은 사람들이 사용하고 있어 에러 발생 시 바로 정보를 얻을 수 있다는 점이 최고의 장점인 것 같다. 단점이라면 크로스 플랫폼이 그렇듯 성능 최적화 이슈 정도...? 기본은 JavaScript 베이스지만, Native에 대해 이해도가 있어야 한다는 거 정도?

아무튼 그래서 부담 없이 배우면서 할 수 있는 React Native Side Project를 구하게 되어 해당 프로젝트를 진행하면서 여기에 포스팅을 한번 남겨보고자 한다.

초기 설정
초기 보일러 플레이트는 별거 없다. 공식 문서 보고 따라 하면 된다. (환경 세팅이 다되어있다는 전제 하에)

```bash
npx react-native init '만들 앱 이름'
```

여기서 나는 보통 바로 사용하지 않고 npm package update, code formatter(eslint), package name... 등을 먼저 세팅하고 사용하기 시작한다.

### Npm Package Update

https://www.npmjs.com/package/npm-check-updates

package.json 내 package를 한방에 업데이트해주는 유용한 라이브러리

### eslint 설정

https://github.com/vasilestefirta/react-native-eslint-prettier-example

NodeJS 쓰면서 이런 설정을 안한다는 건 이단아 그 자체

### Bundle Identifier 설정

보통 Bundle Identifier는 com."회사 이름"."앱 이름" 식으로 구성하는 것 같은데, IOS의 경우 Xcode 열어서 Display Name, Bundle Identifier 만 변경해주면 되나, Android는 위와 같이 변경할 경우 폴더 구조를 'app/src/main/res/java/com/회사 이름/앱 이름' 식으로 변경해줘야 하는 귀찮은 작업이 있다. 이유는 왜 그런지 모른다. 궁금하긴 한데 그렇게 또 알고 싶진 않다.

하지만 우리는 누구인가. "라이브러리의 민족"이다.

https://www.npmjs.com/package/react-native-rename

이 라이브러리 사용 시, 설정한 Bundle Identifier에 따라 Android 폴더 구조를 다 맞춰준다.

참고로 앱 이름에 한글은 쓰지 않는 걸 추천한다. 앱 이름 한글로 지정하고 사용하니 IOS 폴더가 다 한글로 변경되는 문제점이 있었다. 그래서 나는 이 라이브러리 사용 시 앱 이름은 react-native init 할 때 사용했던 이름으로 하고, 뒤에 Bundle Identifier 만 변경해서 사용한다.

- 22-10-09 내용 추가
  - 필자가 외주를 가끔 하다 보니, 동일한 기능 혹은 초기 세팅 템플릿을 미리 구축해둔 뒤, 그 템플릿을 복사하여 작업에 활용하는 방식으로 사용한다. 그래서 해당 라이브러리를 애용하는 것이다.

### Package.json Script 설정

나는 작업하면서 자주 쓰는 스크립트를 package.json에 다 등록해두는 편이다.

아래는 내가 쓰는 스크립트니까 참고해서 사용하실 분은 사용하면 좋겠다.

```javascript
{
  ...
  "scripts" : {
    ...
    // 안드로이드 기존 빌드 잔여물 및 찌꺼기 파일들 초기화
    "android:clear": "cd android && ./gradlew clean",

    // 안드로이드 bundle 생성
    "android:bundle": "react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle",

    // 안드로이드 APK 생성
    "android:apk": "yarn run android:bundle && cd android && ./gradlew clean && ./gradlew assembleRelease && cd ..",

    // 안드로이드 구글 개발자 콘솔 센터에 업로드할 AAB 파일 생성
    "android:aab": "yarn run android:bundle && cd android && ./gradlew clean && ./gradlew bundleRelease && cd ..",

    // 빌드 에러가 나 짜증 날 때 한방 치료해주는 모세의 기적과도 같은 스크립트 (스택 오버 플로에서 본거같은데, 추후 발견하면 출저 업데이트 하겠음)
    // Mac 기준이며, m1 맥 사용 시 'pod install' > 'arch -x86_64 pod install' 로 변경해서 사용할 것
    "reset": "watchman watch-del-all; rm -rf /tmp/metro-bundler-cache-*; rm -rf ./android/build; rm -rf ./android/.gradle; rm -rf ./android/app/build; rm -rf ~/Library/Developer/Xcode/DerivedData; rm -rf /tmp/haste-map-react-native-packager-*; rm -rf ./ios/build; jest --clearCache; cd ios; pod install --repo-update; cd ..; npx jetify; cd android; ./gradlew clean; cd ..; react-native start --reset-cache"
  }
}
```

이렇게 초기 프로젝트 설정이 끝났다.

이제 React Native로 App 만들기 첫 발자국을 막 떼었다.

앞으로 다양한 에러가 나를 맞이 하겠지만, 이 에러를 고치는 과정을 포스팅해야겠다.
