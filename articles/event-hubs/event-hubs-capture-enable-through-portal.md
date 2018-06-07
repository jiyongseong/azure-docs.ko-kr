---
title: "포털을 통해 Azure Event Hubs를 사용하도록 설정 | Microsoft Docs"
description: "Azure Portal을 사용하여 Event Hubs 캡처 기능을 사용하도록 설정합니다."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 39fbdba404bda9383c9164dd1ecd9cb23bfb5cd7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Azure Portal을 사용하여 Event Hubs 캡처를 사용하도록 설정

Azure [Event Hubs 캡처][capture-overview]를 사용하면 Event Hubs의 스트리밍 데이터를 선택한 [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) 또는 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 계정에 자동으로 전달할 수 있습니다.

이벤트 허브 생성 시 [Azure Portal](https://portal.azure.com)을 사용하여 캡처를 구성할 수 있습니다. Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 컨테이너 또는 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 계정으로 데이터를 캡처할 수 있습니다.

자세한 내용은 [Event Hubs 캡처 개요][capture-overview]를 참조하세요.

## <a name="capture-data-to-an-azure-storage-account"></a>Azure Storage 계정에 데이터 캡처  

이벤트 허브를 만드는 경우 **Event Hub 만들기** 포털 화면에서 **켜기** 단추를 클릭하여 캡처를 사용하도록 설정합니다. 그런 다음 **캡처 공급자** 상자에서 **Azure Storage**를 클릭하여 저장소 계정 및 컨테이너를 지정합니다. Event Hubs 캡처는 저장소에서 서비스 간 인증을 사용하므로 저장소 연결 문자열을 지정할 필요가 없습니다. 리소스 선택기가 저장소 계정에 대한 리소스 URI를 자동으로 선택합니다. Azure Resource Manager를 사용하는 경우 이 URI를 문자열로 명백히 제공해야 합니다.

기본 시간은 5분입니다. 최소값은 1, 최대값은 15입니다. **크기**의 범위는 10-500MB입니다.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Azure Data Lake Store 계정에 데이터를 캡처합니다.

Azure Data Lake Store에 데이터를 캡처하려면 Data Lake Store 계정 및 이벤트 허브를 만듭니다.

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Azure Data Lake Store 계정 및 폴더 만들기

1. Data Lake Store 계정을 만들려면 [Azure Portal을 사용하여 Azure Data Lake Store 시작](../data-lake-store/data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다.
2. [Event Hubs에 사용 권한 할당](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) 섹션의 지침을 따라 Data Lake Store 계정에 데이터를 쓸 수 있도록 Event Hubs에서 데이터를 캡처하고 Event Hubs에 사용 권한을 할당하려는 Data Lake Store 계정 내에 폴더를 만듭니다.  

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

1. 이벤트 허브는 방금 만든 Azure Data Lake Store와 동일한 Azure 구독에 있어야 합니다. **Event Hub 만들기** 포털 페이지의 **캡처** 아래에서 **켜기** 단추를 클릭하여 이벤트 허브를 만듭니다. 
2. **Event Hub 만들기** 포털 페이지의 **캡처 공급자** 상자에서 **Azure Data Lake Store**를 선택합니다.
3. **Data Lake Store 선택**에서 이전에 만든 Data Lake Store 계정을 지정하고 **Data Lake 경로** 필드에서 만든 데이터 폴더에 대한 경로를 입력합니다.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>기존 이벤트 허브에서 캡처 추가 및 구성

Event Hubs 네임스페이스에 있는 기존 이벤트 허브에 캡처를 구성할 수 있습니다. 기존 이벤트 허브에서 캡처를 사용하거나 캡처 설정을 변경하려면 네임스페이스를 클릭하여 개요 화면을 로드한 다음 캡처 설정을 사용하도록 설정하거나 변경할 이벤트 허브를 클릭합니다. 마지막으로 다음 그림과 같이 열린 페이지의 왼쪽에서 **캡처** 옵션을 클릭한 후 설정을 편집합니다.

### <a name="azure-blob-storage"></a>Azure Blob Storage

![][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>다음 단계

- [Event Hubs 캡처 개요][capture-overview]를 참조하여 Event Hubs 캡처에 대해 자세히 알아봅니다.
- Azure Resource Manager 템플릿을 사용하여 Event Hubs 캡처를 구성할 수도 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 캡처를 사용하도록 설정](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)을 참조하세요.
- [Azure Portal을 사용하여 Azure Data Lake Store 시작](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md