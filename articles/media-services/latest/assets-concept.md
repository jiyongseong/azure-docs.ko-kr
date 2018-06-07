---
title: Azure Media Services의 자산 | Microsoft Docs
description: 이 문서에서는 자산이 무엇이고 Azure Media Services가 어떤 자산을 사용하는지 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 76ed74f2df62d478b83e109a492977ec2d580198
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305092"
---
# <a name="assets"></a>자산

**자산** 에는 디지털 파일(비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 캡션 파일 포함)과 이러한 파일에 대한 메타데이터가 포함됩니다. 디지털 파일은 자산에 업로드한 후 Media Services 인코딩 및 스트리밍 워크플로에서 사용할 수 있습니다.

자산은 [Azure Storage 계정](storage-account-concept.md)의 BLOB 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너에 블록 BLOB으로 저장됩니다. Storage SDK 클라이언트를 사용하여 컨테이너의 자산 파일과 상호 작용할 수 있습니다.

Azure Media Services는 계정이 범용 v2(GPv2) 저장소를 사용할 때 Blob 계층을 지원합니다. GPv2를 사용하는 경우 파일을 서늘하거나 추운 저장소로 이동할 수 있습니다. 콜드 저장소는 더 이상 필요 없는 원본 파일을 보관하는 데 적합합니다(예: 인코딩된 후).

Media Services v3에서 작업 입력은 자산 또는 HTTP URL에서 만들 수 있습니다. 작업에 대한 입력으로 사용할 수 있는 자산을 만들려면 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조하세요.

또한 [Media Services에서 저장소 계정](storage-account-concept.md)과 [변환 및 작업](transform-concept.md)에 대해 읽어보세요.

## <a name="asset-definition"></a>자산 정의

다음 표에서는 자산의 속성을 표시하고 해당 정의를 제공합니다.

|Name|type|설명|
|---|---|---|
|Id|string|리소스에 대한 정규화된 리소스 ID입니다.|
|이름|string|리소스의 이름입니다.|
|properties.alternateId |string|자산의 대체 ID입니다.|
|properties.assetId |string|자산 ID입니다.|
|properties.container |string|자산 Blob 컨테이너의 이름입니다.|
|properties.created |string|자산의 생성 날짜입니다.|
|properties.description |string|자산 설명입니다.|
|properties.lastModified |string|사잔을 마지막으로 수정한 날짜입니다.|
|properties.storageAccountName |string|저장소 계정 이름입니다.|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |자산 암호화 형식입니다. 없음 또는 MediaStorageEncryption 중 하나입니다.|
|형식|string|리소스 형식입니다.|

전체 정의는 [자산](https://docs.microsoft.com/rest/api/media/assets)을 참조하세요.

## <a name="filtering-ordering-and-paging-support"></a>필터링, 정렬 및 페이징 지원

Media Services는 자산에 대한 다음 OData 쿼리 옵션을 지원합니다. 

* $filter 
* $orderby 
* $top 
* $skiptoken 

### <a name="filteringordering"></a>필터링/순서

다음 표에서는 자산 속성에 이러한 옵션을 적용하는 방법을 보여줍니다. 

|Name|Filter|순서|
|---|---|---|
|Id|지원:<br/>같음<br/>다음보다 큼<br/>미만|지원:<br/>오름차순<br/>내림차순|
|이름|||
|properties.alternateId |지원:<br/>같음||
|properties.assetId |지원:<br/>같음||
|properties.container |||
|properties.created|지원:<br/>같음<br/>다음보다 큼<br/>미만|지원:<br/>오름차순<br/>내림차순|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|형식|||

다음 C# 예제에서는 만든 날짜로 필터링합니다.

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>페이지 매김

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 

쿼리 응답이 여러 항목(현재 1000개 이상)을 포함하는 경우 서비스는 결과의 다음 페이지를 가져오는 "@odata.nextLink" 속성을 반환합니다. 전체 결과 집합을 통해 페이지에 사용할 수 있습니다. 사용자가 페이지 크기를 구성할 수 없습니다. 

(해당 변경 내용이 다운로드되지 않은 컬렉션의 일부인 경우)컬렉션을 통해 페이징하는 동안 자산이 생성되거나 삭제되면 변경 내용이 반환된 결과에 반영됩니다. 

다음 C# 예제에서는 계정의 모든 자산을 통해 열거하는 방법을 보여줍니다.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

나머지 예제는 [자산 - 목록](https://docs.microsoft.com/rest/api/media/assets/list)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [파일 스트리밍](stream-files-dotnet-quickstart.md)
