---
title: Azure Media Services를 사용하여 업로드, 인코딩 및 스트리밍 | Microsoft Docs
description: 이 자습서의 단계에 따라서 Azure Media Services를 사용하여 파일을 업로드하고 비디오를 인코딩하고 콘텐츠를 스트리밍할 수 있습니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: eefe59da69eb60f2ac9e266389fa7f68e6139215
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362210"
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>자습서: API를 사용하여 비디오 업로드, 인코딩 및 스트리밍

이 자습서는 Azure Media Services를 사용하여 비디오 파일을 업로드, 인코딩 및 스트리밍하는 방법을 보여줍니다. 다양한 브라우저 및 장치에서 재생할 수 있도록 Apple의 HLS, MPEG DASH 또는 CMAF 형식으로 콘텐츠를 스트리밍할 수 있습니다. 비디오를 스트리밍하려면 그 전에 비디오를 적절히 인코딩하고 패키징해야 합니다.

자습서에서 비디오를 업로드하는 단계를 안내하지만 HTTPS URL을 통해 Media Services 계정에 액세스할 수 있도록 콘텐츠를 인코딩할 수도 있습니다.

![비디오 재생](./media/stream-files-tutorial-with-api/final-video.png)

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.    

> [!div class="checklist"]
> * Azure Cloud Shell 시작
> * Media Services 계정 만들기
> * Media Services API 액세스
> * 샘플 앱 구성
> * 코드 자세히 검사
> * 앱 실행
> * 스트리밍 URL 테스트
> * 리소스 정리

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

Visual Studio가 설치되지 않은 경우 [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)을 사용할 수 있습니다.

## <a name="download-the-sample"></a>샘플 다운로드

다음 명령을 사용하여 스트리밍 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code"></a>코드 검사

이 섹션에서는 *UploadEncodeAndStreamFiles* 프로젝트의 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) 파일에 정의된 함수를 살펴봅니다.

### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK로 Media Services API 사용하기

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만들어야 합니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 먼저 토큰을 가져온 다음, 반환된 토큰에서 **ClientCredential** 개체를 만들어야 합니다. 문서의 시작 부분에서 복제한 코드에서 **ArmClientCredential** 개체가 토큰을 가져오는 데 사용됩니다.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>입력 자산을 만들고 여기에 로컬 파일 업로드 

**CreateInputAsset** 함수는 새로운 입력 [Asset](https://docs.microsoft.com/rest/api/media/assets)을 만들고 이 자산에 지정된 로컬 비디오 파일을 업로드합니다. 이 Asset은 인코딩 Job에 대한 입력으로 사용됩니다. Media Services v3에서 Job에 대한 입력은 Asset이거나 HTTPS URL을 통해 Media Services 계정에서 사용할 수 있는 콘텐츠일 수도 있습니다. HTTPS URL에서 인코딩하는 방법을 알아보려면 [이](job-input-from-http-how-to.md) 문서를 참조하세요.  

Media Services v3에서는 Azure Storage API를 사용하여 파일을 업로드합니다. 다음 .NET 코드 조각에서 방법을 참조하세요.

다음 함수는 아래와 같은 작업을 수행합니다.

* Asset 만들기 
* [저장소에 있는 자산 컨테이너](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)에 대해 쓰기가 가능한 [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 가져오기
* SAS URL을 사용하여 저장소의 컨테이너에 파일 업로드

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>작업 결과를 저장할 출력 자산 만들기 

출력 [Asset](https://docs.microsoft.com/rest/api/media/assets)은 인코딩 작업의 결과를 저장합니다. 프로젝트는 출력 자산의 결과를 "output" 폴더로 저장하는 **DownloadResults** 함수를 정의합니다. 따라서 무엇을 다운로드했는지 볼 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>로드된 파일을 인코딩하는 Transform 및 Job 만들기
Media Services에서 콘텐츠를 인코딩하거나 처리할 때 인코딩 설정을 레시피로 설정하는 것이 일반적인 패턴입니다. 그런 다음, 이 레시피가 비디오에 적용되도록 **Job**을 제출합니다. 새로운 비디오에 대해 새 Job을 제출하면 라이브러리의 모든 비디오에 레시피가 적용됩니다. Media Services의 레시피를 **Transform**이라고 합니다. 자세한 내용은 [Transform 및 Job](transform-concept.md)을 참조하세요. 이 자습서에서 설명하는 샘플은 다양한 iOS 및 Android 장치로 스트리밍하기 위해 비디오를 인코딩하는 레시피를 정의합니다. 

#### <a name="transform"></a>변환

새로운 [Transform](https://docs.microsoft.com/rest/api/media/transforms) 인스턴스를 만드는 경우 이 인스턴스를 통해 출력하려는 것이 무엇인지 지정해야 합니다. 필수 매개 변수는 아래 코드와 같이 **TransformOutput** 개체입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **Preset**은 원하는 **TransformOutput**을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 이 자습서에서 설명하는 샘플은 **AdaptiveStreaming**이라는 기본 제공 Preset을 사용합니다. Preset은 입력 해상도 및 비트 전송률을 기반으로 자동 생성된 비트 전송률 사다리(비트 전송률-해상도 쌍)에 입력 비디오를 인코딩하고 각 비트 전송률-해상도 쌍에 해당하는 H.264 비디오 및 AAC 오디오가 포함된 ISO MP4 파일을 생성합니다. 이 Preset에 대한 정보는 [자동 생성된 비트 전송률 사다리](autogen-bitrate-ladder.md)를 참조하세요.

기본 제공 EncoderNamedPreset을 사용하거나 사용자 지정 사전 설정을 사용할 수 있습니다. 

[Transform](https://docs.microsoft.com/rest/api/media/transforms)을 만드는 경우 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인해야 합니다. 아래 코드를 참조하세요.  Media Services v3의 경우, 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드는 **null**을 반환합니다(이름의 대/소문자를 구분하지 않음).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>작업

위에서 언급했듯이 [Transform](https://docs.microsoft.com/rest/api/media/transforms) 개체는 레시피이며 [Job](https://docs.microsoft.com/rest/api/media/jobs)은 주어진 입력 비디오 또는 오디오 콘텐츠에 **Transform**을 적용하라는 Media Services에 대한 실제 요청입니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다.

이 예제에서는 로컬 컴퓨터에서 입력 비디오가 업로드되었습니다. HTTPS URL에서 인코딩하는 방법을 알아보려면 [이](job-input-from-http-how-to.md) 문서를 참조하세요.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>작업이 완료될 때까지 대기

아래 코드 샘플은 [Job](https://docs.microsoft.com/rest/api/media/jobs)의 상태에 대한 서비스를 폴링하는 방법을 보여줍니다. 폴링은 잠재적인 대기 시간 때문에 프로덕션 응용 프로그램에는 권장되지 않습니다. 폴링이 계정에서 초과 사용되면 정체될 수 있습니다. 대신 Event Grid를 사용해야 합니다.

Event Grid는 고가용성, 일관된 성능 및 동적 확장을 위해 설계되었습니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 대응할 수 있습니다. 간단한 HTTP 기반 반응형 이벤트 처리는 이벤트의 지능형 필터링 및 라우팅을 통해 효율적인 솔루션을 구축하는 데 도움이 됩니다.  [이벤트를 사용자 지정 웹 엔드포인트로 라우팅](job-state-events-cli-how-to.md)을 참조하세요.

**작업**은 일반적으로 **예약됨**, **큐에 대기됨**, **처리 중**, **마침**(최종 상태) 상태를 거칩니다. 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중**이 표시되고 완료되면 **취소됨**이 표시됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="get-a-streaminglocator"></a>StreamingLocator 가져오기

인코딩이 완료되면 다음 단계는 출력 Asset의 비디오를 클라이언트가 재생할 수 있도록 만드는 것입니다. 이 작업은 두 단계로 수행할 수 있습니다. 첫째, [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들고 둘째, 클라이언트가 사용할 수 있는 스트리밍 URL을 작성합니다. 

**StreamingLocator**를 만드는 과정을 게시라고 합니다. 기본적으로 **StreamingLocator**는 API 호출을 수행한 직후부터 유효하며, 선택적인 시작 및 종료 시간을 구성하지 않는 한 삭제될 때까지 지속됩니다. 

[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들 때 원하는 **StreamingPolicyName**을 지정해야 합니다. 이 예제에서는 미리 정의된 Clear 스트리밍 정책인 **PredefinedStreamingPolicy.ClearStreamingOnly**가 사용될 수 있도록 암호화되지 않은 콘텐츠(in-the-clear)를 스트리밍합니다.

> [!IMPORTANT]
> 사용자 지정 [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies)를 사용하는 경우 Media Service 계정에 대해 이러한 정책을 제한적으로 설계하고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 StreamingLocator에 다시 사용해야 합니다. Media Service 계정에는 StreamingPolicy 항목의 수에 대한 할당량이 있습니다. 각 StreamingLocator에 대해 새 StreamingPolicy를 만들지 말아야 합니다.

다음 코드에서는 고유한 locatorName을 사용하여 함수를 호출한다고 가정합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

이 항목의 샘플에서는 스트리밍에 대해 설명하지만 동일한 호출을 사용하여 점진적 다운로드를 통해 비디오를 전달하는 StreamingLocator를 만들 수 있습니다.

### <a name="get-streaming-urls"></a>스트리밍 URL 얻기

[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)가 생성되었으니 **GetStreamingURLs**에 표시된 것처럼 스트리밍 URL을 얻을 수 있습니다. URL을 작성하려면 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 호스트 이름과 **StreamingLocator** 경로를 연결해야 합니다. 이 샘플에서는 *기본* **StreamingEndpoint**가 사용됩니다. Media Service 계정을 처음 만들 때 *기본* **StreamingEndpoint**는 중지된 상태이므로 **Start**를 호출해야 합니다.

> [!NOTE]
> 이 메서드에서는 출력 Asset에 대해 **StreamingLocator**를 만들 때 사용된 locatorName이 필요합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

일반적으로 재사용할 개체를 제외하고 모두 정리해야 합니다. (일반적으로 Transform를 재사용하고 StreamingLocator 등을 유지합니다.) 실험 후 계정이 정리되도록 하려면 재사용하지 않을 리소스는 삭제해야 합니다.  예를 들어 다음 코드는 Job을 삭제합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>샘플 앱 실행

1. Ctrl+F5를 눌러서 *EncodeAndStreamFiles* 응용 프로그램을 실행합니다.
2. 콘솔에서 스트리밍 URL 중 하나를 복사합니다.

아래 예제는 다른 프로토콜을 사용하여 비디오를 재생하는 데 사용할 수 있는 URL입니다.

![출력](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>스트리밍 URL 테스트

이 문서에서는 스트림을 테스트하기 위해 Azure Media Player를 사용합니다. 

> [!NOTE]
> 플레이어가 https 사이트에 호스트 될 경우 URL을 "https"로 업데이트해야 합니다.

1. 웹 브라우저를 열고 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)로 이동합니다.
2. 응용 프로그램을 실행할 때 얻은 URL 값 중 하나를 **URL:** 상자에 붙여넣습니다. 
3. **플레이어 업데이트**를 누릅니다.

Azure Media Player는 테스트용으로 사용할 수 있지만 프로덕션 환경에서는 사용할 수 없습니다. 

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Media Services 및 저장소 계정을 포함하여 리소스 그룹의 리소스가 더 이상 필요하지 않으면, 앞서 만든 리소스 그룹을 삭제합니다. **CloudShell** 도구를 사용하면 됩니다.

**CloudShell**에서 다음 명령을 실행합니다.

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>다중 스레딩

Azure Media Services v3 SDK는 스레드로부터 안전하지 않습니다. 다중 스레드 응용 프로그램으로 개발하는 경우, 스레드마다 새로운 AzureMediaServicesClient 개체를 생성하고 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

비디오를 업로드, 인코딩 및 스트리밍하는 방법을 알아보았으니 이제 다음 문서를 참조하십시오. 

> [!div class="nextstepaction"]
> [비디오 분석](analyze-videos-tutorial-with-api.md)
