MS 공식 URL

- https://docs.microsoft.com/ko-kr/appcenter/distribution/codepush/

# CodePush 설명

- CodePush는 Apache Cordova 및 React Native 개발자가 사용자의 디바이스에 모바일 앱 업데이트를 직접 배포할 수 있도록 하는 App Center 클라우드 서비스입니다.

# App Center CLI

```shell
# install
npm install -g appcenter-cli

# login
appcenter login
# ...
# 로그인 성공 시 인증 토큰이 발급됨.
# 발급된 토큰을 등록하면 로그인 완료!
# ...

# 앱 생성하기
appcenter apps create -d <appDisplayName> -o <operatingSystem>  -p <platform>
# 예시
appcenter apps create -d RNCodePushSample-Android -o Android -p React-Native
appcenter apps create -d RNCodePushSample-iOS -o iOS -p React-Native

# 생성된 앱 확인하기
appcenter apps list

# deployment key 발급받기
appcenter codepush deployment add -a <ownerName>/<appName> Staging
appcenter codepush deployment add -a <ownerName>/<appName> Production

# deployment key 확인하기
appcenter codepush deployment list -a <ownerName>/<appname> -k
```

# React Native SDK를 사용하여 시작

```shell
npm install react-native-code-push
```

## iOS 설정

1. CocoaPods 설치
   - `cd ios && pod install && cd ..`
2. `AppDelegate.m`에 CodePush import 추가
   ```object-c
   #import <CodePush/CodePush.h>
   ```
3. 앱의 최신 버전의 JS 번들을 항상 로드하도록 앱을 구성함
   ```object-c
   // 프로덕션 릴리즈에 대한 bridge의 원본 URL을 설정하는 코드 주석 처리
   // return [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
   // 추가
   return [CodePush bundleURL];
   ```
4. `info.plist`에 `CodePushDeploymentKey` 추가
   ```
   <key>CodePushDeploymentKey</key>
   <string>yro7XXZhaZXoYzMbgAhsmUqfAr7nuIH7ABntg</string>
   ```

## Android 설정

1. android/settings.gradle 수정
   ```
   // 수정
   include ':app', ':react-native-code-push'
   ...
   // 추가
   project(':react-native-code-push').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-code-push/android/app')
   ```
2. android/app/build.gradle 파일 수정
   ```
   apply from: "../../node_modules/react-native/react.gradle"
   // 추가
   apply from: "../../node_modules/react-native-code-push/android/codepush.gradle"
   ```
3. `MainApplication.java`에 CodePush import 추가
   ```
   import com.microsoft.codepush.react.CodePush;
   ```
4. ReactNativeHost 설정에 getJSBundleFile() 추가
   ```
   @Override
   protected String getJSMainModuleName() {
       return "index";
   }
   // 추가
   @Override
   protected String getJSBundleFile() {
       return CodePush.getJSBundleFile();
   }
   ```
5. `res/values/string.xml`에 `CodePushDeploymentKey` 추가
   ```xml
   <string moduleConfig="true" name="CodePushDeploymentKey">DeploymentKey</string>
   ```

# AppCenter 테스트

- appCenter: https://appcenter.ms/apps
- 시뮬레이터의 Fast Refresh 기능을 끄고 진행해야 함

1. 시뮬레이터 실행
2. code 변경
3. appcenter JSbundle 배포
4. 시뮬레이터 앱 Background -> Foreground

```
appcenter codepush release-react -a <ownerName>/<appName> -d <deploymentName> -t <targetBinaryVersion>
[-t|--target-binary-version <targetBinaryVersion>]
[-o|--output-dir]
[-s|--sourcemap-output]
[-c|--build-configuration-name <arg>]
[--plist-file-prefix]
[-p|--plist-file]
[-g|--gradle-file]
[-e|--entry-file]
[--development]
[-b|--bundle-name <bundleName>]
[-r|--rollout <rolloutPercentage>]
[--disable-duplicate-release-error]
[-k|--private-key-path <privateKeyPath>]
[-m|--mandatory]
[-x|--disabled]
[--description <description>]
[-d|--deployment-name <deploymentName>]
[-a|--app <ownerName>/<appName>]
[--disable-telemetry]
[-v|--version]
```
