## <a name="setting-up-your-ios-application"></a>iOS 응용 프로그램 설정

이 섹션에서는 토큰이 필요한 Web API를 쿼리할 수 있도록 iOS 응용 프로그램(Swift)을 *Microsoft에 로그인*과 통합하는 방식을 설명하기 위해 새 프로젝트를 만드는 방법에 대한 단계별 지침을 제공합니다.

> 이 예제의 XCode 프로젝트를 다운로드하고 싶으세요? [프로젝트를 다운로드](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)하면 실행 전 코드 예제를 구성하는 [구성 단계](#register-your-application)를 건너뛸 수 있습니다.


## <a name="install-carthage-to-download-and-build-msal"></a>Carthage를 설치하여 MSAL 다운로드 및 빌드
Carthage 패키지 관리자는 MSAL 미리 보기 기간 동안 사용됩니다. XCode와 통합되면서도 Microsoft의 라이브러리 변경 기능을 유지 관리합니다.

- Carthage의 최신 릴리스는 [여기](https://github.com/Carthage/Carthage/releases "Carthage 다운로드 URL")에서 다운로드하여 설치합니다.

## <a name="creating-your-application"></a>응용 프로그램 만들기

1.  Xcode를 열고 `Create a new Xcode project`를 선택합니다.
2.  `iOS` > `Single view Application`을 선택하고 *다음*을 클릭합니다.
3.  제품 이름을 지정하고 *다음*을 클릭합니다.
4.  앱을 만들 폴더를 선택하고 *만들기*를 클릭합니다.

## <a name="build-the-msal-framework"></a>MSAL 프레임워크 빌드

아래 지침에 따라 Carthage를 사용하여 MSAL 라이브러리의 최신 버전을 끌어와서 빌드합니다.

1.  Bash 터미널을 열고 앱의 루트 폴더로 이동합니다.
2.  아래 내용을 복사하여 bash 터미널에 붙여넣고 'Cartfile' 파일을 만듭니다.

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
아래 내용을 복사하여 붙여넣습니다. 이 명령은 Carthage/Checkouts 폴더로 종속성을 페치한 다음, MSAL 라이브러리를 빌드합니다.
</li>
</ol>

```bash
carthage update
```

> 위 프로세스는 MSAL(Microsoft Authentication Library)을 다운로드하고 빌드하는 데 사용됩니다. MSAL은 Azure Active Directory v2로 보호되는 API에 액세스하는 데 사용되는 사용자 토큰의 획득, 캐싱 및 새로 고침을 처리합니다.

## <a name="add-the-msal-framework-to-your-application"></a>응용 프로그램에 MSAL 프레임워크 추가
1.  Xcode에서 `General` 탭을 엽니다.
2.  `Linked Frameworks and Libraries` 섹션으로 이동하여 클릭`+`를 클릭합니다.
3.  `Add other…`을(를) 선택합니다.
4.  `Carthage` > `Build` > `iOS` > `MSAL.framework`를 선택하고 *열기*를 클릭합니다. `MSAL.framework`가 목록에 추가되어 표시되어야 합니다.
5.  `Build Phases` 탭으로 이동하여 `+` 아이콘을 클릭하고 `New Run Script Phase`를 선택합니다.
6.  다음 콘텐츠를 *스크립트 영역*에 추가합니다.

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
<code>+</code>를 클릭하여 <code>Input Files</code>에 다음을 추가합니다.
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>응용 프로그램 UI 만들기
Main.storyboard 파일은 프로젝트 템플릿의 일부로 자동으로 만들어져야 합니다. 앱 UI를 만들려면 아래 지침을 따르세요.

1.  Ctrl 키를 누른 채로 `Main.storyboard`를 클릭하여 상황에 맞는 메뉴를 표시한 후 `Open As` > `Source Code`를 클릭합니다.
2.  `<scenes>` 노드를 아래 코드로 바꿉니다.

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign-Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```
