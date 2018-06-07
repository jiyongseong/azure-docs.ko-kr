지속적인 개발로 인해 Android Studio에 설치된 Android SDK가 코드의 버전과 일치하지 않을 수도 있습니다. 이 자습서에서 언급한 Android SDK 버전은 이 문서 작성 시 최신 버전인 26입니다. 버전 번호는 SDK의 새 릴리스가 공개되면 높아질 수 있으며, 사용 가능한 최신 버전을 사용하는 것이 좋습니다.

버전 불일치의 두 가지 증상은 다음과 같습니다.

- 프로젝트를 빌드하거나 다시 빌드할 때 `Gradle sync failed: Failed to find target with hash string 'android-XX'`와 같은 Gradle 오류 메시지가 나타날 수 있습니다.
- `import` 문을 기반으로 확인되는 코드의 표준 Android 개체에서 오류 메시지를 생성할 수 있습니다.

위와 같은 증상 중 하나가 나타나는 경우 Android Studio에 설치된 Android SDK의 버전이 다운로드한 프로젝트의 SDK 대상과 일치하지 않을 수 있습니다. 버전을 확인하려면 다음을 변경하십시오.

1. Android Studio에서 **Tools** > **Android** > **SDK Manager**를 클릭합니다. 최신 버전의 SDK 플랫폼을 설치하지 않은 경우 클릭하여 설치합니다. 버전 번호를 기록해 둡니다.

2. **프로젝트 탐색기** 탭의 **Gradle 스크립트**에서 **build.gradle (Module: app)** 파일을 엽니다. **compileSdkVersion** 및 **targetSdkVersion**이 설치된 최신 SDK 버전으로 설정되어 있는지 확인합니다. `build.gradle`이 다음과 같이 표시될 수 있습니다.

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
