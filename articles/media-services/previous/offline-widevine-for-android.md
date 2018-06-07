---
title: Widevine 보호 콘텐츠의 오프라인 스트리밍 계정 구성 - Azure
description: 이 항목에서는 Widevine 보호 콘텐츠의 오프라인 스트리밍에 대한 Azure Media Services 계정을 구성하는 방법을 보여 줍니다.
services: media-services
keywords: DASH, DRM, Widevine 오프라인 모드, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: 158b58c13aee4d6241900db4a5e2b3fe8a45cc3c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="offline-widevine-streaming-for-android"></a>Android용 오프라인 Widevine 스트리밍

온라인 스트리밍을 위해 콘텐츠를 보호하는 것 외에도, 미디어 콘텐츠 구독 및 임대 서비스는 인터넷에 연결되지 않았을 때 작동하는 다운로드 가능한 콘텐츠를 제공합니다. 네트워크에서 연결이 끊긴 상태로 비행하는 비행기 모드에서 재생하려면 휴대폰이나 태블릿에 콘텐츠를 다운로드해야 할 수 있습니다. 콘텐츠를 다운로드하려는 추가적인 시나리오는 다음과 같습니다.

- 일부 콘텐츠 공급자는 해당 국가의 국경 너머로 DRM 라이선스 배달을 허용하지 않을 수도 있습니다. 사용자를 해외 여행을 하면서 콘텐츠를 보려 할 경우 오프 라인 다운로드가 필요합니다.
- 일부 국가에서는 인터넷 가용성 및/또는 대역폭이 제한됩니다. 사용자가 만족스러운 보기 환경을 위해 충분히 높은 해상도로 콘텐츠를 볼 수 있도록 콘텐츠를 다운로드하도록 선택할 수도 있습니다.

이 문서에서는 Android 장치에서 Widevine에 의해 보호되는 DASH 콘텐츠에 대해 오프라인 모드 재생을 구현하는 방법을 설명합니다. 오프라인 DRM은 콘텐츠에 대해 구독, 임대 및 구매 모델을 제공하여, 서비스 고객이 인터넷에 연결되어 있지 않을 때도 콘텐츠를 쉽게 사용하도록 할 수 있습니다.

Android 플레이어 앱을 빌드하는 경우 다음 세 가지 옵션을 사용할 수 있습니다.

> [!div class="checklist"]
> * ExoPlayer SDK의 Java API를 사용하여 플레이어 빌드
> * ExoPlayer SDK의 Xamarin 바인딩을 사용하여 플레이어 빌드
> * Chrome 모바일 브라우저 v62 이상에서 EME(Encrypted Media Extension) 및 MSE(Media Source Extension)를 사용하여 플레이어 빌드

또한 Widevine 보호 콘텐츠의 오프라인 스트리밍과 관련된 몇 가지 일반적인 질문에 대한 답변도 제공되어 있습니다.

## <a name="requirements"></a>요구 사항 

Android 장치에서 Widevine에 대한 오프라인 DRM을 구현하기 전에 먼저 다음을 수행해야 합니다.

- Widevine DRM을 사용하는 온라인 콘텐츠 보호를 위해 도입된 개념을 숙지합니다. 이 내용은 다음 문서/샘플에서 자세히 다룹니다.
    - [Azure Media Services를 사용하여 DRM 라이선스 또는 AES 키 제공](media-services-deliver-keys-and-licenses.md)
    - [다중 DRM 및 Access Control이 포함된 CENC: Azure 및 Azure Media Services에서 참조 디자인 및 구현](media-services-cenc-with-multidrm-access-control.md)
    - [.NET에서 PlayReady 및/또는 Widevine 동적 일반 암호화 사용](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Azure Media Services를 사용하여 .NET에서 PlayReady 및/또는 Widevine 라이선스 제공](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- 오프라인 Widevine DRM 재생을 지원할 수 있는 오픈 소스 비디오 플레이어 SDK인 Android용 Google ExoPlayer SDK에 익숙해집니다. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer 개발자 가이드](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer 개발자 블로그](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Azure Media Services의 콘텐츠 보호 구성

Media Services에서 자산의 Widevine 보호를 구성할 때, 다음 세 가지 사항을 지정하는 ContentKeyAuthorizationPolicyOption을 만들어야 합니다.

1. DRM 시스템(Widevine)
2. 라이선스 배달 서비스에서 콘텐츠 키 배달이 승인되는 방식(개방형 또는 토큰 인증)을 지정하는 ContentKeyAuthorizationPolicyRestriction
3. DRM(Widevine) 라이선스 템플릿

Widevine 라이선스에 대해 **오프라인** 모드를 사용하도록 설정하려면 [Widevine 라이선스 템플릿](media-services-widevine-license-template-overview.md)을 구성해야 합니다. **policy_overrides** 개체에서 **can_persist** 속성을 **true**(기본값은 false)로 설정합니다. 

다음 코드 샘플에서는 .NET을 사용하여 Widevine 라이선스에 대해 **오프라인** 모드를 사용하도록 설정합니다. 이 코드는 [.NET에서 PlayReady 및/또는 Widevine 동적 일반 암호화 사용](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) 샘플을 기준으로 합니다. 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                             //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),   //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>오프라인 재생을 위한 Android 플레이어 구성

Android 장치용 네이티브 플레이어 앱을 개발하는 가장 쉬운 방법은 오픈 소스 비디오 플레이어 SDK인 [Google ExoPlayer SDK](https://github.com/google/ExoPlayer)를 사용하는 것입니다. ExoPlayer는 MPEG-DASH 및 Microsoft 부드러운 스트리밍 배달 프로토콜을 포함하여 Android의 네이티브 MediaPlayer API에서 현재 지원하지 않는 기능을 지원합니다.

ExoPlayer 버전 2.6 이상에는 오프라인 Widevine DRM 재생을 지원하는 여러 클래스가 포함되어 있습니다. 특히, OfflineLicenseHelper 클래스는 DefaultDrmSessionManager를 사용하여 오프라인 라이선스 다운로드, 갱신 및 해제를 용이하게 수행하도록 하는 유틸리티 함수를 제공합니다. SDK 폴더 "library/core/src/main/java/com/google/android/exoplayer2/offline/"에 제공되는 클래스는 오프라인 비디오 콘텐츠 다운로드를 지원합니다.

다음과 같은 클래스 목록은 Android용 ExoPlayer SDK에서 오프라인 모드를 지원합니다.

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

개발자는 응용 프로그램 개발 중에 [ExoPlayer 개발자 가이드](https://google.github.io/ExoPlayer/guide.html) 및 해당 [개발자 블로그](https://medium.com/google-exoplayer)를 참조하는 것이 좋습니다. Google은 현재, Widevine 오프라인을 지원하는 ExoPlayer 앱의 완전히 문서화된 참조 구현 또는 샘플 코드를 출시하지 않았으므로, 정보가 개발자 가이드 및 블로그로 제한됩니다. 

### <a name="working-with-older-android-devices"></a>이전 Android 장치 사용

일부 이전 Android 장치의 경우, 다음 **policy_overrides** 속성([Widevine 라이선스 템플릿](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** 및 **license_duration_seconds**)에 대한 값을 설정해야 합니다. 또는 무한/무제한 기간을 의미하는 0으로 설정할 수도 있습니다.  

정수 오버플로 버그를 방지하도록 해당 값을 설정해야 합니다. 문제에 대한 자세한 설명은 https://github.com/google/ExoPlayer/issues/3150 및 https://github.com/google/ExoPlayer/issues/3112를 참조하세요. <br/>값을 명시적으로 설정하지 않으면 **PlaybackDurationRemaining** 및 **LicenseDurationRemaining** 값으로 매우 큰 값(예: 64비트 정수에 대한 최대 양수인 9223372036854775807)이 할당됩니다. 결과적으로, Widevine 라이선스는 만료된 것으로 나타나며, 암호 해독은 발생하지 않습니다. 

Android 4.4 KitKat은 원래부터 다른 이전 Android 버전과 마찬가지로 ARMv7 및 32비트 플랫폼을 지원하도록 디자인되어 있지만, Android 5.0은 ARMv8([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) 및 64비트 플랫폼을 완전하게 지원하도록 디자인된 최초의 Android 버전이므로 Android 5.0 Lollipop 이상에서는 이 문제가 발생하지 않습니다.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Xamarin을 사용하여 Android 재생 앱 빌드

다음 링크를 사용하여 ExoPlayer에 대한 Xamarin 바인딩을 찾을 수 있습니다.

- [Google ExoPlayer 라이브러리에 대한 Xamarin 바인딩 라이브러리](https://github.com/martijn00/ExoPlayerXamarin)
- [ExoPlayer NuGet에 대한 Xamarin 바인딩](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

스레드 [Xamarin 바인딩](https://github.com/martijn00/ExoPlayerXamarin/pull/57)도 참조하세요. 

## <a name="chrome-player-apps-for-android"></a>Android용 Chrome 플레이어 앱

[Android용 Chrome v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates) 릴리스부터, EME의 영구 라이선스가 지원됩니다. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1)은 이제 Android용 Chrome에서도 지원됩니다. 따라서, 최종 사용자에게 이 버전 이상의 Chrome이 있기만 하면 Chrome에서 오프라인 재생 응용 프로그램을 만들 수 있습니다. 

또한 Google은 PWA(Progressive Web App) 샘플을 생성하고 오픈 소스로 설정했습니다. 

- [소스 코드](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google 호스티드 버전](https://biograf-155113.appspot.com/ttt/episode-2/)(Android 장치의 Chrome v 62 이상에서만 작동)

Android 휴대폰에서 모바일 Chrome 브라우저를 v62(또는 이상)로 업그레이드하고 위의 호스티드 샘플 앱을 테스트하는 경우, 온라인 스트리밍 및 오프라인 재생이 둘 다 작동되는 것을 볼 수 있습니다.

위의 오픈 소스 PWA 앱은 Node.js에서 작성됩니다. Ubuntu 서버에 고유한 버전을 호스트하려는 경우, 재생을 방해할 수 있는 다음과 같은 일반적인 문제가 발생한다는 점에 유의합니다.

1. CORS 문제: 샘플 앱의 샘플 비디오는 https://storage.googleapis.com/biograf-video-files/videos/에서 호스팅됩니다. Google은 Google 클라우드 저장소 버킷에 호스트되는 모든 테스트 샘플에 대해 CORS를 설정했습니다. CORS 항목 https://biograf-155113.appspot.com(Google에서 해당 샘플을 호스트하는 도메인)을 명시적으로 지정하는 CORS 헤더가 사용되어 다른 사이트의 액세스를 방지합니다. 작업을 시도하면 다음 HTTP 오류가 표시됩니다. https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd:를 로드하지 못했습니다. 요청된 리소스에 'Access-Control-Allow-Origin' 헤더가 없습니다. 따라서 원본 'https://13.85.80.81:8080' 액세스가 허용되지 않습니다. 사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.
2. 인증서 문제: Chrome v 58부터, Widevine용 EME에는 HTTPS가 필요합니다. 따라서 X509 인증서를 사용해서 HTTPS를 통해 샘플 앱을 호스트해야 합니다. 일반적인 테스트 인증서는 다음 요구 사항 때문에 작동하지 않습니다. 즉, 다음과 같은 최소 요구 사항을 충족하는 인증서를 가져와야 합니다.
    - Chrome 및 Firefox에서는 SAN 주체 대체 이름 설정이 인증서에 있어야 합니다.
    - 인증서는 신뢰할 수 있는 CA에서 발급한 것이어야 하고, 자체 서명된 인증서가 작동하지 않습니다.
    - 인증서는 웹 서버 또는 게이트웨이의 DNS 이름과 일치하는 CN이 있어야 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="question"></a>질문

일부 클라이언트/사용자에게는 영구 라이선스(오프라인 사용 가능)를 제공하고 일부에게는 비영구 라이선스(오프라인 사용 불가)를 제공하려면 어떻게 해야 하나요? 콘텐츠를 복제하고 별도 콘텐츠 키를 사용해야 하나요?

### <a name="answer"></a>응답
콘텐츠를 복제할 필요는 없습니다. 콘텐츠의 단일 복사본과 단일 ContentKeyAuthorizationPolicy만 사용하면 되지만 다음과 같이 ContentKeyAuthorizationPolicyOption은 2개 중에서 구분해서 사용해야 합니다.

1. IContentKeyAuthorizationPolicyOption 1: 영구 라이선스를 사용하고, license_type = “Persistent”와 같은 클레임을 포함하는 ContentKeyAuthorizationPolicyRestriction 1을 사용합니다.
2. IContentKeyAuthorizationPolicyOption 2: 비영구 라이선스를 사용하고, license_type = “Nonpersistent”와 같은 클레임을 포함하는 ContentKeyAuthorizationPolicyRestriction 2를 사용합니다.

이러한 방식으로, 라이선스 요청이 클라이언트 앱에서 들어올 경우 라이선스 요청과 차이가 없습니다. 그러나 다른 최종 사용자/장치의 경우, STS에는 다른 클레임(위의 두 license_type 중 하나)을 포함하는 다른 JWT 토큰을 발급하는 비즈니스 논리가 있어야 합니다. JWT 토큰의 클레임 값은 라이선스 서비스에서 발급할 라이선스 유형(영구 또는 비영구)을 결정하는 데 사용됩니다.

즉, STS(보안 토큰 서비스)는 토큰에 해당 클레임 값을 추가하기 위해 비즈니스 논리 및 클라이언트/장치 정보가 있어야 합니다.

### <a name="question"></a>질문

Widevine 보안 수준에 대해 Google의 [Widevine DRM 아키텍처 개요 문서](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) 설명서에서는 세 가지 다른 보안 수준을 정의하고 있습니다. 그러나 [Widevine 라이선스 템플릿에 대한 Azure Media Services 설명서](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)에는 5개의 보안 수준이 설명되어 있습니다. 두 가지 다른 보안 수준 집합 간에는 어떤 관계 또는 매핑이 있나요?

### <a name="answer"></a>응답

Google의 [Widevine DRM 아키텍처 개요](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)에서는 다음과 같은 세 가지 보안 수준을 정의하고 있습니다.

1.  보안 수준 1: 모든 콘텐츠 처리, 암호화 및 제어가 TEE(신뢰할 수 있는 실행 환경)에서 수행됩니다. 일부 구현 모델에서 보안 처리는 다른 칩에서 수행될 수 있습니다.
2.  보안 수준 2: TEE 내에서 암호화(비디오 처리는 아님)를 수행합니다. 암호 해독된 버퍼는 응용 프로그램 도메인으로 반환되고 별도의 비디오 하드웨어 또는 소프트웨어를 통해 처리됩니다. 그러나 수준 2에서 암호화 정보는 여전히 TEE 내에서만 처리됩니다.
3.  보안 수준 3: 장치에 TEE가 없습니다. 호스트 운영 체제의 암호화 정보 및 암호 해독된 콘텐츠를 보호하기 위해 적절한 조치가 수행될 수 있습니다. 수준 3 구현에도 하드웨어 암호화 엔진이 포함될 수 있지만 보안이 아닌 성능만 향상시킵니다.

동시에, [Widevine 라이선스 템플릿에 대한 Azure Media Services 설명서](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)에서 content_key_specs의 security_level 속성은 다음과 같은 5가지 값(재생을 위한 클라이언트 견고성 요구 사항)을 가질 수 있습니다.

1.  소프트웨어 기반 화이트 박스 암호화가 필요합니다.
2.  소프트웨어 암호화 및 난독 처리된 디코더가 필요합니다.
3.  하드웨어 기반의 TEE에서 키 자료 및 암호화 작업을 수행해야 합니다.
4.  하드웨어 기반의 TEE에서 콘텐츠의 암호화 및 디코딩을 수행해야 합니다.
5.  하드웨어 기반의 TEE에서 미디어의 암호화, 디코딩 및 모든 처리(압축 및 압축 해제)를 수행해야 합니다.

두 보안 수준은 Google Widevine에 의해 정의됩니다. 차이점은 사용 수준이 아키텍처 수준인지 또는 API 수준인지 입니다. 5가지 보안 수준은 Widevine API에서 사용됩니다. security_level을 포함하는 content_key_specs 개체는 Azure Media Services Widevine 라이선스 서비스에 의해 역직렬화된 후 Widevine 전역 배달 서비스로 전달됩니다. 아래 표에서는 두 보안 수준 집합 간의 매핑을 보여 줍니다.

| **Widevine 아키텍처에 정의되는 보안 수준** |**Widevine API에서 사용되는 보안 수준**|
|---|---| 
| **보안 수준 1**: 모든 콘텐츠 처리, 암호화 및 제어가 TEE(신뢰할 수 있는 실행 환경)에서 수행됩니다. 일부 구현 모델에서 보안 처리는 다른 칩에서 수행될 수 있습니다.|**보안 수준 5**: 하드웨어 기반의 신뢰할 수 있는 실행 환경에서 미디어의 암호화, 디코딩 및 모든 처리(압축 및 압축 해제)를 수행해야 합니다.<br/><br/>**보안 수준 4**: 하드웨어 기반의 신뢰할 수 있는 실행 환경에서 콘텐츠의 암호화 및 디코딩을 수행해야 합니다.|
**보안 수준 2**: TEE 내에서 암호화(비디오 처리는 아님)를 수행합니다. 암호 해독된 버퍼는 응용 프로그램 도메인으로 반환되고 별도의 비디오 하드웨어 또는 소프트웨어를 통해 처리됩니다. 그러나 수준 2에서 암호화 정보는 여전히 TEE 내에서만 처리됩니다.| **보안 수준 3**: 하드웨어 기반의 신뢰할 수 있는 실행 환경에서 키 자료 및 암호화 작업을 수행해야 합니다. |
| **보안 수준 3**: 장치에 TEE가 없습니다. 호스트 운영 체제의 암호화 정보 및 암호 해독된 콘텐츠를 보호하기 위해 적절한 조치가 수행될 수 있습니다. 수준 3 구현에도 하드웨어 암호화 엔진이 포함될 수 있지만 보안이 아닌 성능만 향상시킵니다. | **security_level=2**: 소프트웨어 암호화 및 난독 처리된 디코더가 필요합니다.<br/><br/>**security_level=1**: 소프트웨어 기반 화이트 박스 암호화가 필요합니다.|

### <a name="question"></a>질문

콘텐츠 다운로드 시간이 너무 오래 걸리는 이유는 무엇인가요?

### <a name="answer"></a>응답

다운로드 속도를 개선하는 방법으로는 다음 두 가지가 있습니다.

1.  최종 사용자가 콘텐츠 다운로드를 위해 원본/스트리밍 끝점 대신, CDN에 연결하기 쉽도록 CDN을 사용하도록 설정합니다. 사용자가 스트리밍 끝점에 도달하는 경우 각 HLS 세그먼트 또는 DASH 조각이 동적으로 패키지되고 암호화됩니다. 이러한 대기 시간이 각 세그먼트/조각에 대해 밀리초 규모에 불과하지만, 1시간짜리 비디오가 있는 경우 누적된 대기 시간 때문에 다운로드가 더 오래 걸릴 수 있습니다.
2.  최종 사용자에게 모든 콘텐츠가 아닌, 비디오 화질 계층 및 오디오 트랙만 선택적으로 다운로드하는 옵션을 제공합니다. 오프라인 모드에서는 모든 화질 계층을 다운로드할 수 있는 시점이 없습니다. 두 가지 방법으로 이 작업을 수행할 수 있습니다.
    1.  클라이언트 제어: 비디오 화질 계층 및 다운로드할 오디오 트랙을 플레이어 앱이 자동으로 선택하도록 하거나 사용자가 선택하도록 합니다.
    2.  서비스 제어: Azure Media Services의 동적 매니페스트 기능으로 (전역) 필터를 만들어, HLS 재생 목록 또는 DASH MPD를 단일 비디오 화질 계층 및 선택한 오디오 트랙으로 제한할 수 있습니다. 그러면 최종 사용자에게 표시되는 다운로드 URL에 이 필터가 포함됩니다.

## <a name="summary"></a>요약

이 문서에서는 Android 장치에서 Widevine에 의해 보호되는 DASH 콘텐츠에 대해 오프라인 모드 재생을 구현하는 방법을 설명합니다.  Widevine 보호 콘텐츠의 오프라인 스트리밍과 관련된 몇 가지 일반적인 질문에 대한 답변도 제공되어 있습니다.
