---
title: Event Hubs의 데이터를 Azure Data Lake Store에 캡처 | Microsoft Docs
description: Azure Data Lake Store를 사용하여 Event Hubs에서 데이터 캡처
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: cff2ca87ab2916c9d5aa8bb308325cb7a0227d75
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Azure Data Lake Store를 사용하여 Event Hubs에서 데이터 캡처

Azure Data Lake Store를 사용하여 Azure Event Hubs에서 받은 데이터를 캡처하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure 데이터 레이크 저장소 계정**. 만드는 방법에 대한 지침은 [Azure Data Lake Store 시작](data-lake-store-get-started-portal.md)을 참조하세요.

*  **Event Hubs 네임스페이스**. 자세한 내용은 [Event Hubs 네임스페이스 만들기](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)를 참조하세요. Data Lake Store 계정 및 Event Hubs 네임스페이스가 동일한 Azure 구독에 있는지 확인합니다.


## <a name="assign-permissions-to-event-hubs"></a>Event Hubs에 사용 권한 할당

이 섹션에서는 Event Hubs에서 데이터를 캡처하는 계정 내 폴더를 만듭니다. 또한 Data Lake Store 계정에 데이터를 쓸 수 있도록 Event Hubs에 사용 권한을 할당합니다. 

1. Event Hubs에서 데이터를 캡처하려는 Data Lake Store 계정을 열고 **데이터 탐색기**를 클릭합니다.

    ![Data Lake Store 데이터 탐색기](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Store 데이터 탐색기")

2.  **새 폴더**를 클릭하고 데이터를 캡처할 폴더의 이름을 입력합니다.

    ![Data Lake Store에 새 폴더 만들기](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Data Lake Store에 새 폴더 만들기")

3. Data Lake Store의 루트에서 사용 권한을 할당합니다. 

    a. **데이터 탐색기**를 클릭하고 Data Lake Store 계정의 루트를 선택한 후 **액세스**를 클릭합니다.

    ![Data Lake Store 루트에 대한 권한 할당](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Data Lake Store 루트에 대한 권한 할당")

    나. **액세스** 아래에서 **추가**를 클릭하고 **사용자 또는 그룹 선택**을 클릭한 후 `Microsoft.EventHubs`를 검색합니다. 

    ![Data Lake Store 루트에 대한 권한 할당](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Data Lake Store 루트에 대한 권한 할당")
    
    **선택**을 클릭합니다.

    다. **권한 할당**에서 **권한 선택**을 클릭합니다. **권한**을 **실행**으로 설정합니다. **추가 대상**을 **이 폴더 및 모든 하위 폴더**로 설정합니다. **다른 권한으로 추가**를 **액세스 권한 항목 및 기본 권한 항목**으로 설정합니다.

> [!IMPORTANT]
> Azure Event Hubs에서 수신된 데이터를 캡처하기 위해 새 폴더 계층 구조를 만들 경우 이렇게 하면 대상 폴더에 대한 액세스를 쉽게 보장할 수 있습니다.  그러나 많은 자식 파일 및 폴더를 포함하는 최상위 수준 폴더의 모든 자식 항목에 대해 사용 권한을 추가하는 데는 시간이 오래 걸릴 수 있습니다.  루트 폴더에 많은 수의 파일 및 폴더가 포함되어 있으면 `Microsoft.EventHubs`에 대한 **실행** 권한을 최종 대상 폴더의 경로에 있는 각 폴더에 개별적으로 추가하는 것이 더 빠를 수 있습니다. 

    ![Assign permissions for Data Lake Store root](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Assign permissions for Data Lake Store root")

    Click **OK**.

4. 데이터를 캡처하려는 Data Lake Store 계정 아래의 폴더에 대해 권한을 할당합니다.

    a. **데이터 탐색기**를 클릭하고 Data Lake Store 계정에서 폴더를 선택하고 **액세스**를 클릭합니다.

    ![Data Lake Store 폴더에 대한 권한 할당](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Data Lake Store 폴더에 대한 권한 할당")

    나. **액세스** 아래에서 **추가**를 클릭하고 **사용자 또는 그룹 선택**을 클릭한 후 `Microsoft.EventHubs`를 검색합니다. 

    ![Data Lake Store 폴더에 대한 권한 할당](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Data Lake Store 폴더에 대한 권한 할당")
    
    **선택**을 클릭합니다.

    다. **권한 할당**에서 **권한 선택**을 클릭합니다. **권한**을 **읽기, 쓰기,** 및 **실행**으로 설정합니다. **추가 대상**을 **이 폴더 및 모든 하위 폴더**로 설정합니다. 마지막으로 **다른 권한으로 추가**를 **액세스 권한 항목 및 기본 권한 항목**으로 설정합니다.

    ![Data Lake Store 폴더에 대한 권한 할당](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Data Lake Store 폴더에 대한 권한 할당")
    
    **확인**을 클릭합니다. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Data Lake Store에 데이터를 캡처하도록 Event Hubs 구성

이 섹션에서는 Event Hubs 네임스페이스 내에 Event Hubs를 만듭니다. 또한 Azure Data Lake Store 계정에 데이터를 캡처하도록 이벤트 허브를 구성합니다. 이 섹션에서는 Event Hubs 네임스페이스를 이미 만들었다고 가정합니다.

2. Event Hubs 네임스페이스의 **개요** 창에서 **+ 이벤트 허브**를 클릭합니다.

    ![이벤트 허브 만들기](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "이벤트 허브 만들기")

3. Data Lake Store에 데이터를 캡처하도록 Event Hubs를 구성하려면 다음 값을 제공하세요.

    ![이벤트 허브 만들기](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "이벤트 허브 만들기")

    a. 이벤트 허브의 이름을 제공하세요.
    
    나. 이 자습서를 위해 **파티션 수** 및 **메시지 보존**을 기본값으로 설정합니다.
    
    다. **캡처**를 **설정**으로 지정합니다. **시간 창**(캡처 빈도) 및 **크기 창**(캡처할 데이터 크기)을 설정합니다. 
    
    d. **캡처 공급자**에 대해 **Azure Data Lake Store**를 선택하고 앞에서 만든 Data Lake Store를 선택합니다. **Data Lake 경로**에 Data Lake Store 계정에서 만든 폴더의 이름을 입력합니다. 폴더에 상대 경로를 제공하기만 하면 됩니다.

    e. **샘플 캡처 파일 이름 형식**은 기본값 상태로 둡니다. 이 옵션은 캡처 폴더 아래에 생성되는 폴더 구조를 제어합니다.

    f. **만들기**를 클릭합니다.

## <a name="test-the-setup"></a>설정 테스트

이제 Azure Event Hub로 데이터를 전송하여 솔루션을 테스트할 수 있습니다. [Azure Event Hubs로 이벤트 전송](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)의 지침을 따릅니다. 데이터 전송을 시작하면 지정한 폴더 구조를 사용하여 Data Lake Store에 반영된 데이터를 볼 수 있습니다. 예를 들어 Data Lake Store에 다음 스크린샷과 같은 폴더 구조가 표시됩니다.

![Data Lake Store의 샘플 EventHub 데이터](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Data Lake Store의 샘플 EventHub 데이터")

> [!NOTE]
> Event Hubs로 들어오는 메시지가 없더라도 Event Hubs는 헤더만 있는 빈 파일을 Data Lake Store 계정에 씁니다. 파일은 Event Hubs를 만드는 동안 제공한 것과 같은 시간 간격으로 써집니다.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Data Lake 저장소의 데이터 분석

Data Lake Store에 데이터가 있으면 분석 작업을 실행하여 데이터를 처리하고 분석할 수 있습니다. Azure Data Lake Analytics를 사용하여 이 작업을 수행하는 방법에 대해서는 [USQL Avro 예제](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)를 참조하세요.
  

## <a name="see-also"></a>참고 항목
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
* [Azure Storage Blob에서 데이터 레이크 저장소로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)
