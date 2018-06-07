---
title: Azure Media Services로 비디오 분석 | Microsoft Docs
description: Azure Media Services를 사용하여 비디오를 분석하려면 이 자습서의 단계를 따르십시오.
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
ms.openlocfilehash: 0fdc8c6dc9fae96a79e2ab2b05b7db3012834c1e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362297"
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>자습서: Azure Media Services로 비디오 분석 

이 자습서는 Azure Media Services로 비디오를 분석하는 방법을 보여줍니다. 녹음/녹화한 비디오나 오디오 콘텐츠를 심층적으로 파악해야 하는 다양한 경우가 있을 수 있습니다. 예를 들어, 고객 만족도를 높이 달성하기 위해 음성을 텍스트로 변환하는 프로세스를 실행하여 고객 지원 기록을 인덱스 및 대시보드와 함께 검색 가능한 카탈로그로 변환할 수 있습니다. 그런 다음, 일반적인 불만 사항 목록 및 불만의 출처 등과 같은 비즈니스에 대한 인사이트를 파악할 수 있습니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.    

> [!div class="checklist"]
> * Azure Cloud Shell 시작
> * Media Services 계정 만들기
> * Media Services API 액세스
> * 샘플 앱 구성
> * 샘플 코드 자세히 검사
> * 앱 실행
> * 출력 내용 검사
> * 리소스 정리

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

Visual Studio가 설치되지 않은 경우 [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)을 사용할 수 있습니다.

## <a name="download-the-sample"></a>샘플 다운로드

다음 명령을 사용하여 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>샘플 코드 자세히 검사

이 섹션에서는 *AnalyzeVideos* 프로젝트의 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) 파일에 정의된 함수를 살펴봅니다.

### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK로 Media Services API 사용하기

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만들어야 합니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 먼저 토큰을 가져온 다음, 반환된 토큰에서 **ClientCredential** 개체를 만들어야 합니다. 문서의 시작 부분에서 복제한 코드에서 **ArmClientCredential** 개체가 토큰을 가져오는 데 사용됩니다.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>작업 결과를 저장할 출력 자산 만들기 

출력 [Asset](https://docs.microsoft.com/rest/api/media/assets)은 작업의 결과를 저장합니다. 프로젝트는 출력 자산의 결과를 "output" 폴더로 저장하는 **DownloadResults** 함수를 정의합니다. 따라서 무엇을 다운로드했는지 볼 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>비디오를 분석하는 Transform 및 Job 만들기

Media Services에서 콘텐츠를 인코딩하거나 처리할 때 인코딩 설정을 레시피로 설정하는 것이 일반적인 패턴입니다. 그런 다음, 이 레시피가 비디오에 적용되도록 **Job**을 제출합니다. 새로운 비디오에 대해 새 Job을 제출하면 라이브러리의 모든 비디오에 레시피가 적용됩니다. Media Services의 레시피를 **Transform**이라고 합니다. 자세한 내용은 [Transform 및 Job](transform-concept.md)을 참조하세요. 이 자습서에 설명된 샘플은 지정된 비디오를 분석하는 레시피를 정의합니다. 

#### <a name="transform"></a>변환

새로운 [Transform](https://docs.microsoft.com/rest/api/media/transforms) 인스턴스를 만드는 경우 이 인스턴스를 통해 출력하려는 것이 무엇인지 지정해야 합니다. 필수 매개 변수는 위 코드와 같이 **TransformOutput** 개체입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **Preset**은 원하는 **TransformOutput**을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 이 예제에서는 **VideoAnalyzerPreset** preset이 사용되며 해당 생성자에게 언어("en-US")가 전달됩니다. 이 preset을 통해 비디오에서 여러 개의 오디오 및 비디오 인사이트를 추출할 수 있습니다. 비디오에서 여러 오디오 인사이트를 추출해야 하는 경우 **AudioAnalyzerPreset** preset을 사용할 수 있습니다. 

**Transform**을 만드는 경우 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인해야 합니다. 아래 코드를 참조하세요.  Media Services v3의 경우, 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드는 **null**을 반환합니다(이름의 대/소문자를 구분하지 않음).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>작업

위에서 언급했듯이 [Transform](https://docs.microsoft.com/rest/api/media/transforms) 개체는 레시피이며 [Job](https://docs.microsoft.com/en-us/rest/api/media/jobs)은 주어진 입력 비디오 또는 오디오 콘텐츠에 **Transform**을 적용하라는 Media Services에 대한 실제 요청입니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다. 비디오의 위치는 HTTPS URL, SAS URL 또는 Media Service 계정에 있는 자산을 사용하여 지정할 수 있습니다. 

이 예에서 작업 입력은 로컬 비디오입니다.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>작업이 완료될 때까지 대기

작업을 완료하는 데 시간이 다소 걸리기 때문에 완료되면 알림을 받는 것이 좋습니다. [Job](https://docs.microsoft.com/en-us/rest/api/media/jobs) 완료에 대한 알림을 받는 옵션은 여러 가지입니다. 가장 간단한 옵션(여기에 표시됨)은 폴링을 사용하는 것입니다. 

폴링은 잠재적인 대기 시간 때문에 프로덕션 응용 프로그램에는 권장되지 않습니다. 폴링이 계정에서 초과 사용되면 정체될 수 있습니다. 대신 Event Grid를 사용해야 합니다.

Event Grid는 고가용성, 일관된 성능 및 동적 확장을 위해 설계되었습니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 대응할 수 있습니다. 간단한 HTTP 기반 반응형 이벤트 처리는 이벤트의 지능형 필터링 및 라우팅을 통해 효율적인 솔루션을 구축하는 데 도움이 됩니다. [이벤트를 사용자 지정 웹 엔드포인트로 라우팅](job-state-events-cli-how-to.md)을 참조하세요.

**작업**은 일반적으로 **예약됨**, **큐에 대기됨**, **처리 중**, **마침**(최종 상태) 상태를 거칩니다. 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중**이 표시되고 완료되면 **취소됨**이 표시됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="download-the-result-of-the-job"></a>작업 결과 다운로드

다음 함수는 출력 [Asset](https://docs.microsoft.com/rest/api/media/assets)의 결과를 "output" 폴더로 다운로드하기 때문에 작업의 결과를 검사할 수 있습니다. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

일반적으로 재사용할 개체를 제외하고 모두 정리해야 합니다. (일반적으로 Transform를 재사용하고 StreamingLocator 등을 유지합니다.) 실험 후 계정이 정리되도록 하려면 재사용하지 않을 리소스는 삭제해야 합니다. 예를 들어 다음 코드는 Job을 삭제합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>샘플 앱 실행

Ctrl+F5 키를 눌러 *AnalyzeVideos* 응용 프로그램을 실행합니다.

프로그램을 실행하면 Job은 동영상에서 찾은 각 얼굴의 썸네일을 생성합니다. insights.json 파일도 생성합니다.

## <a name="examine-the-output"></a>출력 내용 검사

분석 비디오의 출력 파일은 insights.json입니다. 이 파일에는 비디오에 대한 인사이트가 포함됩니다. json 파일에 있는 요소에 대한 설명은 [미디어 인텔리전스](intelligence-concept.md) 문서를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Media Services 및 저장소 계정을 포함하여 리소스 그룹의 리소스가 더 이상 필요하지 않으면, 앞서 만든 리소스 그룹을 삭제합니다. **CloudShell** 도구를 사용하면 됩니다.

**CloudShell**에서 다음 명령을 실행합니다.

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>다중 스레딩

Azure Media Services v3 SDK는 스레드로부터 안전하지 않습니다. 다중 스레드 응용 프로그램으로 작업하는 경우, 스레드마다 새로운 AzureMediaServicesClient 개체를 생성해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
