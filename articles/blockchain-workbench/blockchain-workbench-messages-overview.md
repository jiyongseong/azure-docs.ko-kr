---
title: Azure Blockchain Workbench 메시지 개요
description: Azure Blockchain Workbench의 메시지 사용에 대해 간략하게 설명합니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 4a2e85cc619d17745be9d8f72af5f99049ce7c6b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302095"
---
# <a name="azure-blockchain-workbench-messages-overview"></a>Azure Blockchain Workbench 메시지 개요

Azure Blockchain Workbench는 REST API를 제공할 뿐만 아니라 메시징 기반 통합도 제공합니다. 이 Workbench는 Azure Event Grid를 통해 원장 중심 이벤트를 게시하여 다운스트림 소비자가 데이터를 수집하거나 이러한 이벤트를 기준으로 작업을 수행할 수 있도록 합니다. 신뢰할 수 있는 메시징을 필요로 하는 클라이언트를 위해, Azure Blockchain Workbench는 Azure Service Bus 끝점에 메시지를 배달합니다.

또한 개발자는 외부 시스템이 사용자를 만들고, 계약을 만들고, 원장의 계약을 업데이트하기 위한 트랜잭션을 시작할 것을 알리는 기능도 필요할 것입니다. 이 기능은 현재 공개 미리 보기에 제공되지 않지만, 해당 기능을 제공하는 샘플을 [http://aka.ms/blockchain-workbench-integration-sample](http://aka.ms/blockchain-workbench-integration-sample)에서 찾을 수 있습니다.


## <a name="event-notifications"></a>이벤트 알림

이벤트 알림은 Workbench에서 발생하고 Workbench가 연결된 블록체인 네트워크를 사용자 및 다운스트림 시스템에 알리는 데 사용될 수 있습니다. 이벤트 알림을 코드에서 직접 사용하거나, 다운스트림 시스템으로의 데이터 흐름을 트리거하는 Logic Apps 및 Flow와 같은 도구에서 사용할 수도 있습니다.

수신될 수 있는 다양한 메시지의 세부 정보에 대해서는 [알림 메시지 참조](#notification-message-reference)를 참조하세요.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Azure Functions에서 Event Grid 이벤트 사용

사용자가 Event Grid를 사용하여 Blockchain Workbench에서 발생하는 이벤트에 대한 알림을 받기 원할 경우 Azure Functions를 사용하여 Event Grid의 이벤트를 사용할 수 있습니다.

1. Azure Portal에서 **Azure 함수 앱**을 만듭니다.
2. 새 함수를 만듭니다.
3. Event Grid에 대한 템플릿을 찾습니다. 메시지를 읽기 위한 기본 템플릿 코드가 표시됩니다. 필요에 따라 코드를 수정합니다.
4. 함수를 저장합니다. 
5. Blockchain Workbench의 리소스 그룹에서 Event Grid를 선택합니다.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Logic Apps에서 Event Grid 이벤트 사용

1.  Azure Portal에서 새 **Azure 논리 앱**을 만듭니다.
2.  포털에서 Azure 논리 앱을 열면 트리거를 선택하라는 메시지가 표시됩니다. **Azure Event Grid -- 리소스 이벤트가 발생할 때**를 선택합니다.
3. 워크플로 디자이너가 표시되면 로그인하라는 메시지가 표시됩니다.
4. 구독을 선택합니다. 리소스로 **Microsoft.EventGrid.Topics**를 지정합니다. Azure Blockchain Workbench 리소스 그룹의 리소스 이름에서 **리소스 이름**을 선택합니다.
5. Blockchain Workbench의 리소스 그룹에서 Event Grid를 선택합니다.

## <a name="using-service-bus-topics-for-notifications"></a>알림에 Service Bus 토픽 사용

Service Bus 토픽을 사용하여 Blockchain Workbench에서 발생하는 이벤트를 사용자에게 알릴 수 있습니다. 

1.  Workbench의 리소스 그룹 내에서 Service Bus로 이동합니다.
2.  **토픽**을 선택합니다.
3.  **워크벤츠-외부**를 선택합니다.
4.  이 토픽에 대한 새 구독을 만듭니다. 해당 키를 가져옵니다.
5.  이 구독에서 이벤트를 구독하는 프로그램을 만듭니다.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Logic Apps에서 Service Bus 메시지 사용

1. Azure Portal에서 새 **Azure 논리 앱**을 만듭니다.
2.  포털에서 Azure 논리 앱을 열면 트리거를 선택하라는 메시지가 표시됩니다. 검색 상자에 **Service Bus**를 입력하고 Service Bus와 수행하려는 상호 작용 유형에 적합한 트리거를 선택합니다. 예를 들어 **Service Bus -- 메시지가 항목 구독에 수신되는 경우(자동 완성)** 를 선택할 수 있습니다.
3. 워크플로 디자이너가 표시되면 Service Bus에 대한 연결 정보를 지정합니다.
4. 구독을 선택하고 **워크벤치-외부** 토픽을 지정합니다.
5. 이 트리거에서 메시지를 활용하는 응용 프로그램에 대한 논리를 개발합니다.

## <a name="notification-message-reference"></a>알림 메시지 참조

OperationName에 따라, 알림 메시지는 다음 메시지 유형 중 하나를 갖습니다.

### <a name="accountcreated"></a>AccountCreated

새 계정을 지정한 체인에 추가하도록 요청되었음을 나타냅니다.

| Name    | 설명  |
|----------|--------------|
| UserId  | 생성된 사용자의 ID입니다. |
| ChainIdentifier | 블록체인 네트워크에서 생성된 사용자의 주소입니다. Ethereum에서는 사용자의 "체인 내" 주소가 사용됩니다. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

분산 원장에서 계약을 삽입 또는 업데이트하기 위한 요청이 수행되었음을 나타냅니다.

| Name | 설명 |
|-----|--------------|
| ChainID | 이 요청과 연결된 체인에 대한 고유 식별자입니다.|
  BlockId | 원장에 블록에 대한 고유 식별자입니다.|
  ContractId | 계약에 대한 고유 식별자입니다.|
  ContractAddress |       원장의 계약 주소입니다.|
  TransactionHash  |     원장에 있는 트랜잭션의 해시입니다.|
  OriginatingAddress |   트랜잭션을 만든 사람의 주소입니다.|
  ActionName       |     작업의 이름입니다.|
  IsUpdate        |      업데이트인지 여부를 식별합니다.|
  매개 변수       |     작업에 전송된 매개 변수의 이름, 값 및 데이터 형식을 식별하는 개체 목록입니다.|
  TopLevelInputParams |  계약이 하나 이상의 다른 계약에 연결되어 있는 경우 최상위 계약의 매개 변수입니다. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

분산 원장에서 특정 계약에 대한 작업 실행 요청이 수행되었음을 나타냅니다.

| Name                     | 설명                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | 이 계약 작업에 대한 고유 식별자입니다.                                                                                                                                |
| ChainIdentifier          | 체인의 고유한 식별자입니다.                                                                                                                                           |
| ConnectionId             | 연결에 대한 고유 식별자입니다.                                                                                                                                      |
| UserChainIdentifier      | 블록체인 네트워크에서 생성된 사용자의 주소입니다. Ethereum에서는 사용자의 "체인 내" 주소가 사용됩니다.                                                     |
| ContractLedgerIdentifier | 원장의 계약 주소입니다.                                                                                                                                        |
| WorkflowFunctionName     | 워크플로 함수의 이름입니다.                                                                                                                                                |
| WorkflowName             | 워크플로의 이름입니다.                                                                                                                                                         |
| WorkflowBlobStorageURL   | Blob 저장소에 있는 계약의 URL입니다.                                                                                                                                      |
| ContractActionParameters | 계약 작업에 대한 매개 변수입니다.                                                                                                                                           |
| TransactionHash          | 원장에 있는 트랜잭션의 해시입니다.                                                                                                                                    |
| 프로비전 상태      | 작업의 현재 프로비전 상태입니다.</br>0 – 생성됨</br>1 - 진행 중</br>2 - 완료</br> 완료는 성공적으로 추가되었다는 원장의 확인을 나타냅니다.                                               |
|                          |                                                                                                                                                                               |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

특정 분산 원장에서 사용자 잔액에 대한 업데이트 요청이 수행되었음을 나타냅니다.

> [!NOTE]
> 이 메시지는 계정의 자금 지원이 필요한 원장에 대해서만 생성됩니다.
> 

| Name    | 설명                              |
|---------|------------------------------------------|
| 주소 | 자금이 지원된 사용자의 주소입니다. |
| Balance | 사용자의 잔액입니다.         |
| ChainID | 체인의 고유한 식별자입니다.     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

메시지는 분산 원장에서 블록을 추가하기 위한 요청이 수행되었음을 나타냅니다.

| Name           | 설명                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | 블록이 추가된 체인의 고유 식별자입니다.             |
| BlockId        | Azure Blockchain Workbench 내 블록에 대한 고유 식별자입니다. |
| BlockHash      | 블록의 해시입니다.                                                 |
| BlockTimeStamp | 블록의 타임스탬프입니다.                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

메시지는 분산 원장의 트랜잭션 추가 요청에 대한 세부 정보를 제공합니다.

| Name            | 설명                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | 블록이 추가된 체인의 고유 식별자입니다.             |
| BlockId         | Azure Blockchain Workbench 내 블록에 대한 고유 식별자입니다. |
| TransactionHash | 트랜잭션의 해시입니다.                                           |
| 원본            | 트랜잭션을 만든 사람의 주소입니다.                      |
| 받는 사람              | 트랜잭션의 의도된 받는 사람의 주소입니다.              |
| 값           | 트랜잭션에 포함된 값입니다.                                 |
| IsAppBuilderTx  | Blockchain Workbench 트랜잭션인지를 식별합니다.                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

계약의 체인 식별자 할당에 대한 세부 정보를 제공합니다. 예를 들어 Ethereum 블록체인에서 원장의 계약 주소입니다.

| Name            | 설명                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Azure Blockchain Workbench 내 계약에 대한 고유 식별자입니다. |
| ChainIdentifier | 체인의 계약에 대한 식별자입니다.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [스마트 계약 통합 패턴](blockchain-workbench-integration-patterns.md)