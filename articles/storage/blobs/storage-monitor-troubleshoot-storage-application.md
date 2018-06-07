---
title: Azure에서 클라우드 저장소 응용 프로그램 모니터링 및 문제 해결 | Microsoft Docs
description: 진단 도구, 메트릭 및 경고를 사용하여 클라우드 응용 프로그램 문제를 해결하고 모니터링합니다.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: eb58104309802125a8424cbbf8a1bef3d1c5e79c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>클라우드 저장소 응용 프로그램 모니터링 및 문제 해결

이 자습서는 4부로, 시리즈의 마지막 부분입니다. 여기서는 클라우드 저장소 응용 프로그램을 모니터링하고 문제를 해결하는 방법을 알아봅니다.

시리즈 4부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 로깅 및 메트릭 켜기
> * 권한 부여 오류에 대한 경고를 사용 하도록 설정
> * 잘못된 SAS 토큰을 사용하여 테스트 트래픽 실행
> * 로그 다운로드 및 분석

[Azure Storage 분석](../common/storage-analytics.md)은 저장소 계정에 대한 로깅 및 메트릭 데이터를 제공합니다. 이 데이터는 저장소 계정 상태에 대한 정보를 제공합니다. 저장소 계정에 대해 가시성을 확보하려면 먼저 데이터 컬렉션을 설정해야 합니다. 이 프로세스 동안 로깅 켜기, 메트릭 구성 및 경고 설정이 진행됩니다.

저장소 계정의 로깅 및 메트릭은 Azure Portal의 **진단** 탭에서 사용하도록 설정합니다. 다음과 같이 두 가지 유형의 메트릭이 있습니다. **집계** 메트릭은 수신/송신, 가용성, 대기 시간 및 성공 비율과 같은 메트릭을 수집합니다. 이러한 메트릭은 Blob, 큐, 테이블 및 파일 서비스에 대해 집계됩니다. **API당**은 Azure Storage 서비스 API에서 각 저장소 작업에 대해 동일한 메트릭 집합을 수집합니다. 저장소 로깅을 통해 저장소 계정의 성공한 요청 및 실패한 요청에 대한 세부 정보를 기록할 수 있습니다. 이러한 로그를 사용하여 Azure 테이블, 큐 및 Blob에 대한 읽기, 쓰기 및 삭제 작업의 세부 정보를 볼 수 있습니다. 또한 시간 초과, 제한 및 권한 부여 오류와 같은 실패한 요청의 이유를 볼 수 있습니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인

## <a name="turn-on-logging-and-metrics"></a>로깅 및 메트릭 켜기

왼쪽 메뉴에서 **리소스 그룹**을 선택하고 **myResourceGroup**을 선택한 후 리소스 목록에서 저장소 계정을 선택합니다.

**진단**에서 **상태**를 **켜기**로 설정합니다. **Blob 속성** 아래의 모든 옵션이 활성화되었는지 확인합니다.

완료되면 **저장**을 클릭합니다.

![진단 창](media/storage-monitor-troubleshoot-storage-application/contoso.png)

## <a name="enable-alerts"></a>경고 사용

경고는 관리자에게 메일을 보내거나 임계값을 위반하는 메트릭을 기준으로 웹후크를 트리거하는 방법을 제공합니다. 이 예제에서는 `SASClientOtherError` 메트릭에 대해 경고를 사용하도록 설정합니다.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Azure Portal의 저장소 계정으로 이동합니다.

왼쪽 메뉴에서 **리소스 그룹**을 선택하고 **myResourceGroup**을 선택한 후 리소스 목록에서 저장소 계정을 선택합니다.

**모니터링** 섹션에서 **경고 규칙**을 선택합니다.

**+경고 추가**를 선택하고 **경고 규칙 추가**에서 필요한 정보를 입력합니다. **메트릭** 드롭다운에서 `SASClientOtherError`를 선택합니다.

![진단 창](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>오류 시뮬레이트

올바른 경고를 시뮬레이트하려면 저장소 계정에서 존재하지 않는 Blob을 요청하려고 할 수 있습니다. 이렇게 하려면 `<incorrect-blob-name>` 값을 존재하지 않는 값으로 바꿉니다. 다음 코드 예제를 여러 번 실행하여 실패한 blob 요청을 시뮬레이트합니다.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

다음 그림은 이전 예제를 사용하여 실행된 시뮬레이트된 오류를 기반으로 하는 경고 예제입니다.

 ![경고 예제](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>로그 다운로드 및 보기

저장소 로그는 저장소 계정에 **$logs**라는 blob 컨테이너에 있는 blob의 집합에 데이터를 저장합니다. 이 컨테이너는 계정의 모든 blob 컨테이너를 나열하는 경우에는 표시되지 않지만 직접 액세스할 때는 해당 콘텐츠를 볼 수 있습니다.

이 시나리오에서는 [Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx)를 사용하여 Azure Storage 계정과 상호 작용합니다.

### <a name="download-microsoft-message-analyzer"></a>Microsoft Message Analyzer 다운로드

[Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226)를 다운로드하고 설치합니다.

이 응용 프로그램을 시작하고 **파일** > **열기** > **기타 파일 원본**을 선택합니다.

**파일 선택기** 대화 상자에서 **+Azure 연결 추가**를 선택합니다. **저장소 계정 이름** 및 **계정 키**를 입력하고 **확인**을 클릭합니다.

![Microsoft Message Analyzer - Azure Storage 연결 대화 상자 추가](media/storage-monitor-troubleshoot-storage-application/figure3.png)

연결되면 저장소 트리 보기에서 컨테이너를 확장하여 로그 blob을 표시합니다. 최신 로그를 선택하고 **확인**을 클릭합니다.

![Microsoft Message Analyzer - Azure Storage 연결 대화 상자 추가](media/storage-monitor-troubleshoot-storage-application/figure4.png)

**새 세션** 대화 상자에서 **시작**을 클릭하여 로그를 표시합니다.

로그가 열리면 저장소 이벤트를 볼 수 있습니다. 다음 그림에서 볼 수 있는 것처럼 저장소 계정에 트리거된 `SASClientOtherError`가 발생했습니다. 저장소 로깅에 대한 자세한 내용은 [저장소 분석](../common/storage-analytics.md)을 참조하세요.

![Microsoft Message Analyzer - 에이전트 보기](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[저장소 탐색기](https://azure.microsoft.com/features/storage-explorer/)는 저장소 계정과 상호 작용하는 데 사용할 수 있는 또 다른 도구로, **$logs** 컨테이너 및 해당 로그를 포함합니다.

## <a name="next-steps"></a>다음 단계

시리즈의 마지막 부분인 4부에서는 다음 방법을 비롯하여 저장소 계정을 모니터링하고 문제를 해결하는 방법을 배웠습니다.

> [!div class="checklist"]
> * 로깅 및 메트릭 켜기
> * 권한 부여 오류에 대한 경고를 사용 하도록 설정
> * 잘못된 SAS 토큰을 사용하여 테스트 트래픽 실행
> * 로그 다운로드 및 분석

미리 작성된 저장소 샘플을 보려면 이 링크를 따라 이동합니다.

> [!div class="nextstepaction"]
> [Azure Storage 스크립트 샘플](storage-samples-blobs-cli.md)