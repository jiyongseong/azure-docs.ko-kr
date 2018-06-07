---
title: "Azure Import/Export 작업의 상태 정보 검색 | Microsoft Docs"
description: "Microsoft Azure Import/Export 서비스 작업에 대한 상태 정보를 얻는 방법에 대해 알아봅니다."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 22d7e5f0-94da-49b4-a1ac-dd4c14a423c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.openlocfilehash: 13169716c47cf9389c8f2651393ac744441bdd6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Import/Export 작업에 대한 상태 정보 검색
[작업 가져오기](/rest/api/storageimportexport/jobs#Jobs_Get)(Get Job) 작업을 호출하여 가져오기 및 내보내기 작업에 대한 정보를 검색할 수 있습니다. 반환되는 정보는 다음과 같습니다.

-   작업의 현재 상태

-   각 작업이 완료된 대략적인 백분율

-   각 드라이브의 현재 상태

-   오류 로그 및 자세한 로깅 정보를 포함한 Blob에 대한 URI(사용 가능한 경우)

다음 섹션에서는 `Get Job` 작업에서 반환하는 정보에 대해 설명합니다.

## <a name="job-states"></a>작업 상태
아래 표와 상태 다이어그램에서는 작업이 수명 주기 동안 전환되는 상태를 설명합니다. 작업의 현재 상태는 `Get Job` 작업을 호출하여 확인할 수 있습니다.

![작업 상태](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "작업 상태")

다음 표에서는 작업이 통과할 수 있는 각각의 상태를 설명합니다.

|작업 상태|설명|
|---------------|-----------------|
|`Creating`|작업 배치(Put Job) 작업을 호출하면 작업이 만들어지고 상태는 `Creating`으로 설정됩니다. 작업이 `Creating` 상태로 있는 동안에는 Import/Export 서비스에서 드라이브를 데이터 센터로 운송하지 않았다고 가정합니다. 직업은 최대 2주 동안 `Creating` 상태로 남아 있을 수 있으며 그 이후에는 서비스에서 자동으로 삭제합니다.<br /><br /> 작업이 `Creating` 상태로 있는 동안 작업 속성 업데이트(Update Job Properties) 작업을 호출하면 작업은 `Creating` 상태로 유지되고 시간 제한 간격은 2주로 다시 설정됩니다.|
|`Shipping`|패키지를 운송한 후에는 작업 속성 업데이트 작업을 호출하여 작업 상태를 `Shipping`으로 업데이트해야 합니다. 운송 중(Shipping) 상태는 `DeliveryPackage`(우편물 운송업체 및 추적 번호) 및 `ReturnAddress` 속성이 작업에 대해 설정된 경우에만 설정할 수 있습니다.<br /><br /> 작업은 최대 2주 동안 '운송 중' 상태로 유지됩니다. 2주가 경과하고 드라이브를 받지 못하면 Import/Export 서비스 운영자에게 알립니다.|
|`Received`|데이터 센터에서 모든 드라이브를 받은 후에는 작업 상태가 수신됨(Received) 상태로 설정됩니다.|
|`Transferring`|데이터 센터에서 드라이브를 수신하고 하나 이상의 드라이브가 처리를 시작하면 작업 상태가 `Transferring` 상태로 설정됩니다. 자세한 내용은 아래 `Drive States` 섹션을 참조하세요.|
|`Packaging`|모든 드라이브에서 처리를 완료한 후에 고객에게 드라이브를 반송할 때까지 작업은 `Packaging` 상태가 됩니다.|
|`Completed`|고객에게 모든 드라이브를 반송한 후에 작업이 오류 없이 완료되면 작업은 `Completed` 상태로 설정됩니다. 작업은 `Completed` 상태에서 90일이 경과하면 자동으로 삭제됩니다.|
|`Closed`|고객에게 모든 드라이브를 반송한 후 작업 처리 중에 오류가 발생하면 작업이 `Closed` 상태로 설정됩니다. 작업은 `Closed` 상태에서 90일이 경과하면 자동으로 삭제됩니다.|

작업은 특정 상태에서만 취소할 수 있습니다. 취소된 작업은 데이터 복사 단계를 건너뛰지만 취소되지 않은 작업과 동일한 상태 전환을 수행합니다.

다음 표에서는 각 작업 상태에서 발생할 수 있는 오류와 오류 발생시 작업에 미치는 영향을 설명합니다.

|작업 상태|이벤트|해결 방법/다음 단계|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|한 작업에 대한 드라이브가 하나 이상 도착했지만 작업이 `Shipping` 상태가 아니거나 작업 레코드가 서비스에 없습니다.|Import/Export 서비스 운영 팀은 작업을 진행하는 데 필요한 정보로 해당 작업을 만들거나 업데이트하도록 고객에게 연락하려고 시도합니다.<br /><br /> 운영 팀이 2주 이내에 고객에게 연락할 수 없는 경우에는 드라이브를 반환하려고 시도합니다.<br /><br /> 드라이브를 반환할 수 없고 고객에게 연락할 수 없는 경우 드라이브는 90일 후에 안전하게 소멸됩니다.<br /><br /> 작업은 상태를 `Shipping`으로 업데이트할 때까지 처리되지 않습니다.|
|`Shipping`|작업 패키지가 2주 이상 도착하지 않았습니다.|운영 팀이 고객에게 누락된 패키지를 알립니다. 고객의 응답에 따라 운영 팀은 패키지 도착을 기다리거나 작업을 취소할 간격을 연장합니다.<br /><br /> 고객이 30일 이내에 연락할 수 없거나 응답하지 않는 경우 운영 팀은 작업을 `Shipping` 상태에서 `Closed` 상태로 직접 전환하는 작업을 시작합니다.|
|`Completed/Closed`|드라이브가 반송 주소에 도착하지 않았거나 운송 시 손상되었습니다(내보내기 작업에만 해당).|드라이브가 반송 주소에 도착하지 않은 경우 고객은 먼저 작업 가져오기 작업을 호출하거나 포털에서 작업 상태를 확인하여 드라이브가 운송되었는지 확인해야 합니다. 드라이브가 운송되었으면 고객이 운송업체에 문의하여 드라이브를 찾아야 합니다.<br /><br /> 운송 중에 드라이브가 손상된 경우 고객이 다른 내보내기 작업을 요청하거나 누락된 Bolb을 다운로드하려고 할 수 있습니다.|
|`Transferring/Packaging`|작업의 반송 주소가 잘못되었거나 누락되었습니다.|운영 팀이 작업 담당자에게 문의하여 올바른 주소를 얻습니다.<br /><br /> 고객에게 연락할 수 없는 경우 90일 이내에 드라이브가 안전하게 소멸됩니다.|
|`Creating / Shipping/ Transferring`|가져올 드라이브 목록에 나타나지 않는 드라이브가 운송 패키지에 포함되어 있습니다.|추가 드라이브는 처리되지 않으며 작업이 완료되면 고객에게 반환됩니다.|

## <a name="drive-states"></a>드라이브 상태
아래 표와 다이어그램에서는 개별 드라이브가 가져오기 또는 내보내기 작업을 통해 전환할 때의 수명 주기를 설명합니다. `Get Job` 작업을 호출하고 `DriveList` 속성의 `State` 요소를 검사하여 현재 드라이브 상태를 검색할 수 있습니다.

![드라이브 상태](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "드라이브 상태")

다음 표에서는 드라이브가 통과할 수 있는 각각의 상태를 설명합니다.

|드라이브 상태|설명|
|-----------------|-----------------|
|`Specified`|가져오기 작업의 경우 작업 배치 작업을 통해 작업을 만들 때 드라이브의 초기 상태는 `Specified` 상태입니다. 내보내기 작업의 경우 작업을 만들 때 드라이브가 지정되지 않으므로 초기 드라이브 상태는 `Received` 상태입니다.|
|`Received`|Import/Export 서비스 운영자가 가져오기 작업을 위해 운송 회사로부터 받은 드라이브를 처리할 때 드라이브가 `Received` 상태로 전환합니다. 내보내기 작업의 경우 초기 드라이브 상태는 `Received` 상태입니다.|
|`NeverReceived`|작업 패키지가 도착했지만 패키지에 드라이브가 포함되어 있지 않으면 해당 드라이브가 `NeverReceived` 상태로 전환합니다. 또한 서비스에서 운송 정보를 받은 후 2주가 경과했지만 패키지가 데이터 센터에 아직 도착하지 않은 경우에도 드라이브가 이 상태로 전환할 수 있습니다.|
|`Transferring`|서비스에서 드라이브로부터 Microsoft Azure Storage로 데이터를 전송하기 시작하면 드라이브가 `Transferring` 상태로 전환합니다.|
|`Completed`|서비스에서 모든 데이터를 오류 없이 성공적으로 전송하면 드라이브가 `Completed` 상태로 전환합니다.|
|`CompletedMoreInfo`|데이터를 드라이브에(서) 복사하는 동안 서비스에서 몇 가지 문제가 발생하면 드라이브가 `CompletedMoreInfo` 상태로 전환합니다. 정보에는 Blob 덮어쓰기에 대한 오류, 경고 또는 정보 메시지가 포함될 수 있습니다.|
|`ShippedBack`|드라이브가 데이터 센터에서 반송 주소로 반송되면 `ShippedBack` 상태로 전환합니다.|

다음 표에서는 드라이브 오류 상태 및 각 상태에 대해 수행하는 작업을 설명합니다.

|드라이브 상태|이벤트|해결 방법/다음 단계|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|작업 운송물의 일부로 받지 못하여 `NeverReceived`로 표시된 드라이브가 다른 운송을 통해 도착했습니다.|운영 팀이 드라이브를 `Received` 상태로 전환합니다.|
|`N/A`|작업에 포함되지 않은 드라이브가 다른 작업의 일부로 데이터 센터에 도착했습니다.|드라이브가 추가 드라이브로 표시되며 원래 패키지와 관련된 작업이 완료되면 고객에게 반송됩니다.|

## <a name="faulted-states"></a>오류(Faulted) 상태
작업 또는 드라이브가 예상 수명 주기 동안 정상적으로 진행되지 않으면 해당 작업 또는 드라이브가 `Faulted` 상태로 전환합니다. 운영 팀이 해당 시점에서 고객에게 전자 메일 또는 전화로 연락합니다. 문제가 해결되면 오류가 발생한 작업 또는 드라이브가 `Faulted` 상태에서 적절한 상태로 전환합니다.

## <a name="next-steps"></a>다음 단계

* [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)
