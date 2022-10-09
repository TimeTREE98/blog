---
title: "React-Native IOS에서 RCTBridge 에러 뜰 때"
description:
date: 2021-08-22
update: 2021-08-22
tags:
  - react-native
series:
---

> required dispatch_sync to load RCTDevLoadingView. This may lead to deadlocks

React-Native init 때부터 계속 떠왔던 에러다. 아닌가...?

대충 되지도 않는 영어 실력으로 읽어보자면 RCTDevLoadingView를 로딩하기 위해선 dispatch_sync 가 필요하단 건데, RCTDevLoadingView 가 개발 환경에서 렌더링 할 때 필요한 게 아닌가 싶은데...?

릴리즈 버전에선 문제없을 것 같지만 찝찝해서 알아봤다.

https://stackoverflow.com/questions/45988103/rctbridge-required-dispatch-sync-to-load-rctdevloadingview-this-may-lead-to-dea/48903673

AppDelegate.m 파일을 열고 아래 구문을 추가해주면 된다고 한다. (RN 0.65 기준)

```swift
// 상단에 추가할 코드 - 시작
#if RCT_DEV
#import <React/RCTDevLoadingView.h>
#endif // 끝
...

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  ...
  RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:self launchOptions:launchOptions];

  // 중간에 추가할 코드 - 시작
  #if RCT_DEV
    [bridge moduleForClass:[RCTDevLoadingView class]];
  #endif // 끝

  RCTRootView *rootView = [[RCTRootView alloc] initWithBridge:bridge
                                                   moduleName:@"gamchabak"
                                            initialProperties:nil];
  ...
```
