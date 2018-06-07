---
title: Azure Media Services를 사용한 비디오 파일 스트림 - .NET | Microsoft Docs
description: 이 빠른 시작의 단계에 따라 새로운 Azure Media Services 계정을 만들고, 파일을 인코딩한 다음, Azure Media Player로 스트리밍합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
keywords: Azure Media Services, 스트림
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/08/2018
ms.author: juliako
ms.openlocfilehash: 40759fc65caa181651de68756f4374f879fd9c9c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-stream-video-files---net"></a>빠른 시작: 비디오 파일 스트리밍 - .NET

> [!NOTE]
> Azure Media Services의 최신 버전은 미리보기에 있으며 v3라고도 합니다. v3 API를 사용하기 시작하려면 이 빠른 시작에서 설명한 대로 새 Media Services 계정을 만들어야 합니다. 

이 빠른 시작은 Azure Media Services를 사용하여 다양한 브라우저 및 장치에서 비디오 스트리밍을 시작하는 것이 얼마나 쉬운지 보여줍니다. 

빠른 시작이 끝나면 비디오를 스트리밍 할 수 있습니다.  

![비디오 재생](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

Visual Studio가 설치되지 않은 경우 [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)을 사용할 수 있습니다.

## <a name="download-the-sample"></a>샘플 다운로드

다음 명령을 사용하여 스트리밍 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

샘플의 각 기능이 무엇을 하는지에 관한 설명은 코드를 검토하고 [이 소스 파일 ](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)의 주석을 확인합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

[Azure 포털](http://portal.azure.com) 에 로그인합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="run-the-sample-app"></a>샘플 앱 실행

앱을 실행하면 다른 프로토콜을 사용하여 비디오를 재생하는 데 사용할 수 있는 URL이 표시됩니다. 

1. Ctrl+F5를 눌러서 *EncodeAndStreamFiles* 응용 프로그램을 실행합니다.
2. Apple의 **HLS** 프로토콜(*manifest(format=m3u8-aapl)* 로 끝남)을 선택하고 콘솔에서 스트리밍 URL을 복사합니다.

![출력](./media/stream-files-tutorial-with-api/output.png)

샘플의 [소스 코드](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)에서 URL이 빌드된 방법을 볼 수 있습니다. 그것을 빌드하려면 스트리밍 엔드 포인트의 호스트 이름 및 스트리밍 로케이터 경로를 연결해야 합니다.  

## <a name="test-with-azure-media-player"></a>Azure Media Player로 테스트

이 문서에서는 스트림을 테스트하기 위해 Azure Media Player를 사용합니다. 

> [!NOTE]
> 플레이어가 https 사이트에 호스트 될 경우 URL을 "https"로 업데이트해야 합니다.

1. 웹 브라우저를 열고 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)로 이동합니다.
2. 응용 프로그램을 실행할 때 얻은 URL 값 중 하나를 **URL:** 상자에 붙여넣습니다. 
3. **플레이어 업데이트**를 누릅니다.

Azure Media Player는 테스트용으로 사용할 수 있지만 프로덕션 환경에서는 사용할 수 없습니다. 

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 위해 만든 Media Services 및 저장소 계정을 비롯하여 리소스 그룹의 어떠한 리소스도 더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. **CloudShell** 도구를 사용하면 됩니다.

**CloudShell**에서 다음 명령을 실행합니다.

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>코드 검사

샘플의 각 기능이 무엇을 하는지에 관한 설명은 코드를 검토하고 [이 소스 파일 ](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)의 주석을 확인합니다.

[파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md) 자습서는 고급 스트리밍 예제를 자세한 설명을 곁들여 제공합니다. 

## <a name="multithreading"></a>다중 스레딩

Azure Media Services v3 SDK는 스레드로부터 안전하지 않습니다. 다중 스레드 응용 프로그램으로 작업하는 경우, 스레드마다 새로운 AzureMediaServicesClient 개체를 생성해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
