---
title: Azure Blockchain Workbench의 데이터베이스 뷰
description: Azure Blockchain Workbench SQL DB 데이터베이스 뷰에 대한 개요입니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: b1e12da5a9c83f06cdc6f541b521b4feb1a73231
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="database-views-in-azure-blockchain-workbench"></a>Azure Blockchain Workbench의 데이터베이스 뷰

Azure Blockchain Workbench는 분산 원장에서 오프체인(*off-chain*) SQL DB 데이터베이스로 데이터를 전달합니다. 이를 통해 SQL 및 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)와 같은 기존 도구를 사용하여 블록체인 데이터와 상호 작용할 수 있습니다.

Azure Blockchain Workbench는 쿼리를 수행할 때 도움이 되는 데이터에 액세스를 제공하는 데이터베이스 뷰 집합을 제공합니다. 이러한 뷰는 고도로 비정규화되어 보고서, 분석 등을 쉽고 빠르게 작성할 수 있으며, 그렇지 않으면 기존 도구에서 블록체인 데이터가 사용되며, 데이터베이스 담당자를 재교육할 필요가 없습니다.

이 섹션은 데이터베이스 뷰와 여기에 포함된 데이터에 대한 개요를 포함합니다.

> [!NOTE]
> 이러한 뷰 외부의 데이터베이스에 있는 데이터베이스 테이블을 직접적으로 사용하는 것은 가능하지만 지원되지 않습니다.
> 

## <a name="vwapplication"></a>vwApplication

이 뷰는 Azure Blockchain Workbench에 업로드된 **응용 프로그램**에 대한 세부 정보를 제공합니다.

| Name                             | type          | Null이 될 수 있음 | 설명                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | 아니오          | 응용 프로그램에 대한 고유 식별자입니다.                                                                                                                                                                                                                      |
| ApplicationName                  | nvarchar(50)  | 아니오          | 응용 프로그램 이름입니다.                                                                                                                                                                                                                                  |
| ApplicationDescription           | nvarchar(255) | 예         | 응용 프로그램에 대한 설명                                                                                                                                                                                                                              |
| ApplicationDisplayName           | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름입니다.                                                                                                                                                                                                                 |
| ApplicationEnabled               | bit           | 아니오          | 응용 프로그램을 사용하도록 현재 설정되어 있는지 식별합니다.</br> **참고:** 데이터베이스에 응용 프로그램이 비활성화된 것으로 반영될 수 있지만, 블록체인에 관련 계약이 유지되고 계약에 대한 데이터도 데이터베이스에 남습니다. |
| UploadedDtTm                     | datetime2(7)  | 아니오          | 계약이 업로드된 날짜와 시간입니다.                                                                                                                                                                                                                    |
| UploadedByUserId                 | int           | 아니오          | 응용 프로그램을 업로드한 사용자의 ID입니다.                                                                                                                                                                                                              |
| UploadedByUserExternalId         | nvarchar(255) | 아니오          | 응용 프로그램을 업로드한 사용자의 외부 식별자입니다. 기본적으로 컨소시엄을 위한 Azure Active Directory의 사용자 ID입니다.                                                                                                |
| UploadedByUserProvisioningStatus | int           | 아니오          | 사용자에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다.</br>0 – API에 의해 사용자가 생성됨<br>1 – 키가 데이터베이스의 사용자와 연결됨</br>2 – 사용자가 완전히 프로비전됨                         |
| UploadedByUserFirstName          | nvarchar(50)  | 예         | 계약을 업로드한 사용자의 이름입니다.                                                                                                                                                                                                         |
| UploadedByUserLastName           | nvarchar(50)  | 예         | 계약을 업로드한 사용자의 성입니다.                                                                                                                                                                                                          |
| UploadedByUserEmailAddress       | nvarchar(255) | 예         | 계약을 업로드한 사용자의 이메일 주소입니다.                                                                                                                                                                                                      |

## <a name="vwapplicationrole"></a>vwApplicationRole

이 뷰는 Azure Blockchain Workbench 응용 프로그램에 정의된 역할에 대한 세부 정보를 제공합니다.

자산 전송(*Asset Transfer*) 응용 프로그램에서 구매자(*Buyer*) 및 판매자(*Seller*) 역할과 같은 역할을 정의할 수 있습니다.

| Name                   | type             | Null이 될 수 있음 | 설명                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | 아니오          | 응용 프로그램에 대한 고유 식별자           |
| ApplicationName        | nvarchar(50)     | 아니오          | 응용 프로그램의 이름                       |
| ApplicationDescription | nvarchar(255)    | 예         | 응용 프로그램에 대한 설명                  |
| ApplicationDisplayName | nvarchar(255)    | 아니오          | 사용자 인터페이스에 표시할 이름      |
| RoleId                 | int              | 아니오          | 응용 프로그램의 역할에 대한 고유 식별자 |
| RoleName               | nvarchar50)      | 아니오          | 역할의 이름                              |
| RoleDescription        | description(255) | 예         | 역할에 대한 설명                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

이 뷰는 Azure Blockchain Workbench 응용 프로그램에 정의된 역할 및 이와 연결된 사용자에 대한 세부 정보를 제공합니다.

자산 전송(*Asset Transfer*) 응용 프로그램에서, 예를 들면 *John Smith*가 구매자(*Buyer*) 역할과 연결될 수 있습니다.

| Name                       | type          | Null이 될 수 있음 | 설명                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | 아니오          | 응용 프로그램에 대한 고유 식별자                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | 아니오          | 응용 프로그램의 이름                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | 예         | 응용 프로그램에 대한 설명                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름                                                                                                                                                                                          |
| ApplicationRoleId          | int           | 아니오          | 응용 프로그램의 역할에 대한 고유 식별자                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | 아니오          | 역할의 이름                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | 예         | 역할에 대한 설명                                                                                                                                                                                                             |
| UserId                     | int           | 아니오          | 역할과 연결된 사용자의 ID입니다.                                                                                                                                                                                          |
| UserExternalId             | nvarchar(255) | 아니오          | 역할과 연결된 사용자의 외부 식별자입니다. 기본적으로 컨소시엄을 위한 Azure Active Directory의 사용자 ID입니다.                                                                     |
| UserProvisioningStatus     | int           | 아니오          | 사용자에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다.</br>0 – API에 의해 사용자가 생성됨</br>1 – 키가 데이터베이스의 사용자와 연결됨<br>2 – 사용자가 완전히 프로비전됨 |
| UserFirstName              | nvarchar(50)  | 예         | 역할과 연결된 사용자의 이름입니다.                                                                                                                                                                           |
| UserLastName               | nvarchar(255) | 예         | 역할과 연결된 사용자의 성입니다.                                                                                                                                                                            |
| UserEmailAddress           | nvarchar(255) | 예         | 역할과 연결된 사용자의 이메일 주소입니다.                                                                                                                                                                        |

## <a name="vwconnectionuser"></a>vwConnectionUser

이 뷰는 Azure Blockchain Workbench에 정의된 연결 및 이와 연결된 사용자에 대한 세부 정보를 제공합니다. 각 연결에 대해, 이 뷰에는 다음 데이터가 포함됩니다.

-   연결된 원장 세부 정보
-   연결된 사용자 정보

| Name                     | type          | Null이 될 수 있음 | 설명                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | 아니오          | Azure Blockchain Workbench의 연결에 대한 고유 식별자입니다.                                                                                                                                                                 |
| ConnectionEndpointUrl    | nvarchar(50)  | 아니오          | 연결에 대한 엔드포인트 URL입니다.                                                                                                                                                                                                    |
| ConnectionFundingAccount | nvarchar(255) | 예         | 해당되는 경우 연결과 관련된 자금 계정입니다.                                                                                                                                                                      |
| LedgerId                 | int           | 아니오          | 원장에 대한 고유 식별자입니다.                                                                                                                                                                                                   |
| LedgerName               | nvarchar(50)  | 아니오          | 원장의 이름입니다.                                                                                                                                                                                                               |
| LedgerDisplayName        | nvarchar(255) | 아니오          | UI에 표시할 원장의 이름입니다.                                                                                                                                                                                          |
| UserId                   | int           | 아니오          | 연결과 관련된 사용자의 ID입니다.                                                                                                                                                                                    |
| UserExternalId           | nvarchar(255) | 아니오          | 연결과 관련된 사용자의 외부 식별자입니다. 기본적으로 컨소시엄을 위한 Azure Active Directory의 사용자 ID입니다.                                                               |
| UserProvisioningStatus   | int           | 아니오          |사용자에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다.</br>0 – API에 의해 사용자가 생성됨</br>1 – 키가 데이터베이스의 사용자와 연결됨<br>2 – 사용자가 완전히 프로비전됨 |
| UserFirstName            | nvarchar(50)  | 예         | 연결과 관련된 사용자의 이름입니다.                                                                                                                                                                     |
| UserLastName             | nvarchar(255) | 예         | 연결과 관련된 사용자의 성입니다.                                                                                                                                                                      |
| UserEmailAddress         | nvarchar(255) | 예         | 연결과 관련된 사용자의 이메일 주소입니다.                                                                                                                                                                  |

## <a name="vwcontract"></a>vwContract

이 뷰는 배포된 계약에 대한 세부 정보를 제공합니다. 각 계약에 대해, 이 뷰에는 다음 데이터가 포함됩니다.

-   연결된 응용 프로그램 정의
-   연결된 워크플로 정의
-   함수에 대한 연결된 원장 구현
-   작업을 시작한 사용자의 세부 정보
-   블록체인 블록 및 트랜잭션과 관련된 세부 정보

| Name                                     | type           | Null이 될 수 있음 | 설명                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | 아니오          | Azure Blockchain Workbench의 연결에 대한 고유 식별자입니다.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | 아니오          | 연결에 대한 엔드포인트 URL입니다.                                                                                                                                                                                                                            |
| ConnectionFundingAccount                 | nvarchar(255)  | 예         | 해당되는 경우 연결과 관련된 자금 계정입니다.                                                                                                                                                                                              |
| LedgerId                                 | int            | 아니오          | 원장에 대한 고유 식별자입니다.                                                                                                                                                                                                                           |
| LedgerName                               | nvarchar(50)   | 아니오          | 원장의 이름입니다.                                                                                                                                                                                                                                       |
| LedgerDisplayName                        | nvarchar(255)  | 아니오          | UI에 표시할 원장의 이름입니다.                                                                                                                                                                                                                  |
| ApplicationId                            | int            | 아니오          | 응용 프로그램에 대한 고유 식별자입니다.                                                                                                                                                                                                                      |
| ApplicationName                          | nvarchar(50)  | 아니오          | 응용 프로그램 이름입니다.                                                                                                                                                                                                                                  |
| ApplicationDisplayName                   | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름입니다.                                                                                                                                                                                                                 |
| ApplicationEnabled                       | bit            | 아니오          | 응용 프로그램을 사용하도록 현재 설정되어 있는지 식별합니다.</br> **참고:** 데이터베이스에 응용 프로그램이 비활성화된 것으로 반영될 수 있지만, 블록체인에 관련 계약이 유지되고 계약에 대한 데이터도 데이터베이스에 남습니다.  |
| WorkflowId                               | int            | 아니오          | 계약과 연결된 워크플로에 대한 고유 식별자입니다.                                                                                                                                                                                              |
| WorkflowName                             | nvarchar(50)   | 아니오          | 계약과 연결된 워크플로의 이름입니다.                                                                                                                                                                                                          |
| WorkflowDisplayName                      | nvarchar(255)  | 아니오          | 사용자 인터페이스에 표시할 계약과 연결된 워크플로의 이름입니다.                                                                                                                                                                     |
| WorkflowDescription                      | nvarchar(255)  | 예         | 계약과 연결된 워크플로에 대한 설명입니다.                                                                                                                                                                                                   |
| ContractCodeId                           | int            | 아니오          | 계약과 연결된 계약 코드에 대한 고유 식별자입니다.                                                                                                                                                                                       |
| ContractFileName                         | int            | 아니오          | 워크플로에 대한 스마트 계약 코드가 포함된 파일의 이름입니다.                                                                                                                                                                                    |
| ContractUploadedDtTm                     | int            | 아니오          | 계약 코드가 업로드된 날짜와 시간입니다.                                                                                                                                                                                                             |
| ContractId                               | int            | 아니오          | 계약에 대한 고유 식별자입니다.                                                                                                                                                                                                                       |
| ContractProvisioningStatus               | int            | 아니오          | 계약을 배포한 사용자의 성입니다.                                                                                                                                                                                                          |
| ContractLedgerIdentifier                 | nvarchar(255) |             | 계약을 배포한 사용자의 이메일 주소입니다.                                                                                                                                                                                                      |
| ContractDeployedByUserId                 | int            | 아니오          | 계약을 배포한 사용자의 외부 식별자입니다. 기본적으로 사용자의 Azure Active Directory ID를 나타내는 GUID입니다.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | 아니오          | 계약을 배포한 사용자의 외부 식별자입니다. 기본적으로 사용자의 Azure Active Directory ID를 나타내는 GUID입니다.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | 아니오          | 사용자에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다.</br>0 – API에 의해 사용자가 생성됨</br>1 – 키가 데이터베이스의 사용자와 연결됨 </br>2 – 사용자가 완전히 프로비전됨                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | 예         | 계약을 배포한 사용자의 이름입니다.                                                                                                                                                                                                         |
| ContractDeployedByUserLastName           | nvarchar(255)  | 예         | 계약을 배포한 사용자의 성입니다.                                                                                                                                                                                                          |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | 예         | 계약을 배포한 사용자의 이메일 주소입니다.                                                                                                                                                                                                      |

## <a name="vwcontractaction"></a>vwContractAction

이 뷰는 계약에 대해 수행한 작업과 관련된 대부분의 정보를 나타내며 일반적인 보고 시나리오를 쉽게 활용하도록 설계되었습니다. 수행한 각 작업에 대해 이 뷰에는 다음 데이터가 포함됩니다.

-   연결된 응용 프로그램 정의
-   연결된 워크플로 정의
-   연결된 스마트 계약 함수 및 매개 변수 정의
-   함수에 대한 연결된 원장 구현
-   매개 변수에 제공되는 특정 인스턴스 값
-   작업을 시작한 사용자의 세부 정보
-   블록체인 블록 및 트랜잭션과 관련된 세부 정보

| Name                                     | type          | Null이 될 수 있음 | 설명                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | 아니오          | 응용 프로그램에 대한 고유 식별자입니다.                                                                                                                                                                                                                                                                       |
| ApplicationName                          | nvarchar(50)  | 아니오          | 응용 프로그램 이름입니다.                                                                                                                                                                                                                                                                                   |
| ApplicationDisplayName                   | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름입니다.                                                                                                                                                                                                                                                                  |
| ApplicationEnabled                       | bit           | 아니오          | 이 필드는 응용 프로그램을 사용하도록 현재 설정되어 있는지 식별합니다. 참고 – 데이터베이스에 응용 프로그램이 비활성화된 것으로 반영될 수 있지만, 블록체인에 관련 계약이 유지되고 계약에 대한 데이터도 데이터베이스에 남습니다.                                                  |
| WorkflowId                               | int           | 아니오          | 워크플로에 대한 고유 식별자입니다.                                                                                                                                                                                                                                                                            |
| WorkflowName                             | nvarchar(50)  | 아니오          | 워크플로의 이름입니다.                                                                                                                                                                                                                                                                                      |
| WorkflowDisplayName                      | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 워크플로의 이름입니다.                                                                                                                                                                                                                                                       |
| WorkflowDescription                      | nvarchar(255) | 예         | 워크플로에 대한 설명입니다.                                                                                                                                                                                                                                                                               |
| ContractId                               | int           | 아니오          | 계약에 대한 고유 식별자입니다.                                                                                                                                                                                                                                                                          |
| ContractProvisioningStatus               | int           | 아니오          | 계약에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다.</br>0 – API에 의해 데이터베이스에 계약이 생성됨</br>1 – 계약이 원장에 전송됨</br>2 – 계약이 원장에 성공적으로 배포됨                             |
| ContractCodeId                           | int           | 아니오          | 계약의 코드 구현에 대한 고유 식별자입니다.                                                                                                                                                                                                                                               |
| ContractLedgerIdentifier                 | nvarchar(255) | 예         | 특정 분산 원장에 대한 스마트 계약의 배포 버전과 연결된 고유 식별자입니다. 예: Ethereum.                                                                                                                                                                                 |
| ContractDeployedByUserId                 | int           | 아니오          | 계약을 배포한 사용자의 고유 식별자입니다.                                                                                                                                                                                                                                                  |
| ContractDeployedByUserFirstName          | nvarchar(50)  | 예         | 계약을 배포한 사용자의 이름입니다.                                                                                                                                                                                                                                                              |
| ContractDeployedByUserLastName           | nvarchar(255) | 예         | 계약을 배포한 사용자의 성입니다.                                                                                                                                                                                                                                                               |
| ContractDeployedByUserExternalId         | nvarchar(255) | 아니오          | 계약을 배포한 사용자의 외부 식별자입니다. 기본적으로 컨소시엄 Azure Active Directory에서 사용자의 ID를 나타내는 GUID입니다.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | 예         | 계약을 배포한 사용자의 이메일 주소입니다.                                                                                                                                                                                                                                                       |
| WorkflowFunctionId                       | int           | 아니오          | 워크플로 함수에 대한 고유 식별자입니다.                                                                                                                                                                                                                                                                   |
| WorkflowFunctionName                     | nvarchar(50)  | 아니오          | 함수의 이름입니다.                                                                                                                                                                                                                                                                                      |
| WorkflowFunctionDisplayName              | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 함수의 이름입니다.                                                                                                                                                                                                                                                  |
| WorkflowFunctionDescription              | nvarchar(255) | 아니오          | 함수에 대한 설명입니다.                                                                                                                                                                                                                                                                               |
| ContractActionId                         | int           | 아니오          | 계약 작업에 대한 고유 식별자입니다.                                                                                                                                                                                                                                                                   |
| ContractActionProvisioningStatus         | int           | 아니오          | 계약 작업에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다.</br>0 – API에 의해 데이터베이스에 계약 작업이 생성됨</br>1 – 계약 작업이 원장에 전송됨</br>2 – 계약 작업이 원장에 성공적으로 배포됨 |
| ContractActionTimestamp                  | datetime(2,7) | 아니오          | 계약 작업의 타임스탬프입니다.                                                                                                                                                                                                                                                                          |
| ContractActionExecutedByUserId           | int           | 아니오          | 계약 작업을 실행한 사용자의 고유 식별자입니다.                                                                                                                                                                                                                                               |
| ContractActionExecutedByUserFirstName    | int           | 예         | 계약 조치를 실행한 사용자의 이름입니다.                                                                                                                                                                                                                                                       |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | 예         | 계약 조치를 실행한 사용자의 성입니다.                                                                                                                                                                                                                                                        |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | 예         | 계약 작업을 실행한 사용자의 외부 식별자입니다.  기본적으로 컨소시엄 Azure Active Directory에서 사용자의 ID를 나타내는 GUID입니다.                                                                                                                                                                                                            |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | 예         | 계약 작업을 실행한 사용자의 이메일 주소입니다.                                                                                                                                                                                                                                                |
| WorkflowFunctionParameterId              | int           | 아니오          | 함수의 매개 변수에 대한 고유 식별자입니다.                                                                                                                                                                                                                                                           |
| WorkflowFunctionParameterName            | nvarchar(50)  | 아니오          | 함수의 매개 변수 이름입니다.                                                                                                                                                                                                                                                                       |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 함수 매개 변수의 이름입니다.                                                                                                                                                                                                                                        |
| WorkflowFunctionParameterDataTypeId      | int           | 아니오          | 워크플로 함수 매개 변수와 연결된 데이터 형식의 고유 식별자입니다.                                                                                                                                                                                                                         |
| WorkflowParameterDataTypeName            | nvarchar(50)  | 아니오          | 워크플로 함수 매개 변수와 연결된 데이터 형식의 이름입니다.                                                                                                                                                                                                                                         |
| ContractActionParameterValue             | nvarchar(255) | 아니오          | 스마트 계약에 저장된 매개 변수에 대한 값입니다.                                                                                                                                                                                                                                                      |
| BlockHash                                | nvarchar(255) | 예         | 블록의 해시입니다.                                                                                                                                                                                                                                                                                         |
| BlockNumber                              | int           | 예         | 원장에 있는 블록의 수입니다.                                                                                                                                                                                                                                                                         |
| BlockTimestamp                           | datetime(2,7) | 예         | 블록의 타임스탬프입니다.                                                                                                                                                                                                                                                                                   |
| TransactionId                            | int           | 아니오          | 트랜잭션에 대한 고유 식별자입니다.                                                                                                                                                                                                                                                                       |
| TransactionFrom                          | nvarchar(255) | 예         | 트랜잭션이 시작된 파티입니다.                                                                                                                                                                                                                                                                     |
| TransactionTo                            | nvarchar(255) | 예         | 트랜잭션이 처리된 파티입니다.                                                                                                                                                                                                                                                                            |
| TransactionHash                          | nvarchar(255) | 예         | 트랜잭션의 해시입니다.                                                                                                                                                                                                                                                                                     |
| TransactionIsWorkbenchTransaction        | bit           | 예         | 트랜잭션이 Azure Blockchain Workbench 트랜잭션인지 식별하는 비트입니다.                                                                                                                                                                                                                          |
| TransactionProvisioningStatus            | int           | 예         | 트랜잭션에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다.</br>0 – API에 의해 데이터베이스에 트랜잭션이 생성됨</br>1 – 트랜잭션이 원장에 전송됨</br>2 – 트랜잭션이 원장에 성공적으로 배포됨                 |
| TransactionValue                         | decimal(32,2) | 예         | 트랜잭션 값입니다.                                                                                                                                                                                                                                                                                  |

## <a name="vwcontractproperty"></a>vwContractProperty

이 뷰는 계약과 연결된 속성과 관련된 대부분의 정보를 나타내며 일반적인 보고 시나리오를 쉽게 활용하도록 설계되었습니다. 수행되는 각 속성에 대해 이 뷰에는 다음 데이터가 포함됩니다.

-   연결된 응용 프로그램 정의
-   연결된 워크플로 정의
-   워크플로를 배포한 사용자에 대한 세부 정보
-   연결된 스마트 계약 속성 정의
-   속성에 대한 특정 인스턴스 값
-   계약의 상태 속성에 대한 세부 정보

| Name                               | type          | Null이 될 수 있음 | 설명                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | 아니오          | 응용 프로그램에 대한 고유 식별자입니다.                                                                                                                                                                                                                                           |
| ApplicationName                    | nvarchar(50)  | 아니오          | 응용 프로그램 이름입니다.                                                                                                                                                                                                                                                       |
| ApplicationDisplayName             | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름입니다.                                                                                                                                                                                                                                      |
| ApplicationEnabled                 | bit           | 아니오          | 응용 프로그램을 사용하도록 현재 설정되어 있는지 식별합니다.</br>**참고:** 데이터베이스에 응용 프로그램이 비활성화된 것으로 반영될 수 있지만, 블록체인에 관련 계약이 유지되고 계약에 대한 데이터도 데이터베이스에 남습니다.                      |
| WorkflowId                         | int           | 아니오          | 워크플로에 대한 고유 식별자입니다.                                                                                                                                                                                                                                            |
| WorkflowName                       | nvarchar(50)  | 아니오          | 워크플로의 이름입니다.                                                                                                                                                                                                                                                          |
| WorkflowDisplayName                | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 워크플로의 이름입니다.                                                                                                                                                                                                                       |
| WorkflowDescription                | nvarchar(255) | 예         | 워크플로에 대한 설명입니다.                                                                                                                                                                                                                                                   |
| ContractId                         | int           | 아니오          | 계약에 대한 고유 식별자입니다.                                                                                                                                                                                                                                            |
| ContractProvisioningStatus         | int           | 아니오          | 계약에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다.</br>0 – API에 의해 데이터베이스에 계약이 생성됨</br>1 – 계약이 원장에 전송됨</br>2 – 계약이 원장에 성공적으로 배포됨 |
| ContractCodeId                     | int           | 아니오          | 계약의 코드 구현에 대한 고유 식별자입니다.                                                                                                                                                                                                                   |
| ContractLedgerIdentifier           | nvarchar(255) | 예         | 특정 분산 원장에 대한 스마트 계약의 배포 버전과 연결된 고유 식별자입니다. 예: Ethereum.                                                                                                                                                     |
| ContractDeployedByUserId           | int           | 아니오          | 계약을 배포한 사용자의 고유 식별자입니다.                                                                                                                                                                                                                      |
| ContractDeployedByUserFirstName    | nvarchar(50)  | 예         | 계약을 배포한 사용자의 이름입니다.                                                                                                                                                                                                                                  |
| ContractDeployedByUserLastName     | nvarchar(255) | 예         | 계약을 배포한 사용자의 성입니다.                                                                                                                                                                                                                                   |
| ContractDeployedByUserExternalId   | nvarchar(255) | 아니오          | 계약을 배포한 사용자의 외부 식별자입니다. 기본적으로 컨소시엄 Azure Active Directory에서 사용자의 ID를 나타내는 GUID입니다.                                                                                                                     |
| ContractDeployedByUserEmailAddress | nvarchar(255) | 예         | 계약을 배포한 사용자의 이메일 주소입니다.                                                                                                                                                                                                                           |
| WorkflowPropertyId                 | int           |             | 워크플로의 속성에 대한 고유 식별자입니다.                                                                                                                                                                                                                                  |
| WorkflowPropertyDataTypeId         | int           | 아니오          | 속성의 데이터 형식 ID입니다.                                                                                                                                                                                                                                           |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | 아니오          | 속성의 데이터 형식 이름입니다.                                                                                                                                                                                                                                         |
| WorkflowPropertyName               | nvarchar(50)  | 아니오          | 워크플로 속성의 이름입니다.                                                                                                                                                                                                                                                 |
| WorkflowPropertyDisplayName        | nvarchar(255) | 아니오          | 워크플로 속성의 표시 이름입니다.                                                                                                                                                                                                                                         |
| WorkflowPropertyDescription        | nvarchar(255) | 예         | 속성에 대한 설명입니다.                                                                                                                                                                                                                                                     |
| ContractPropertyValue              | nvarchar(255) | 아니오          | 계약의 속성에 대한 값입니다.                                                                                                                                                                                                                                          |
| StateName                          | nvarchar(50)  | 예         | 이 속성에 계약의 상태가 포함된 경우 상태에 대한 표시 이름입니다. 상태와 연결되어 있지 않으면 값은 null이 됩니다.                                                                                                                                                                                                                    |
| StateDisplayName                   | nvarchar(255) | 아니오          | 이 속성에 상태가 포함되어있는 경우 상태의 표시 이름입니다. 상태와 연결되어 있지 않으면 값은 null이 됩니다.  |
| StateValue                         | nvarchar(255) | 예         | 이 속성에 상태가 포함되어있는 경우 상태 값입니다. 상태와 연결되어 있지 않으면 값은 null이 됩니다.                          |

## <a name="vwcontractstate"></a>vwContractState

이 뷰는 특정 계약의 상태와 관련된 대부분의 정보를 나타내며 일반적인 보고 시나리오를 쉽게 활용하도록 설계되었습니다. 이 뷰의 각 레코드에는 다음 데이터가 포함됩니다.

-   연결된 응용 프로그램 정의
-   연결된 워크플로 정의
-   워크플로를 배포한 사용자에 대한 세부 정보
-   연결된 스마트 계약 속성 정의
-   계약의 상태 속성에 대한 세부 정보

| Name                               | type          | Null이 될 수 있음 | 설명                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | 아니오          | 응용 프로그램에 대한 고유 식별자입니다.                                                                                                                                                                                                                                           |
| ApplicationName                    | nvarchar(50)  | 아니오          | 응용 프로그램 이름입니다.                                                                                                                                                                                                                                                       |
| ApplicationDisplayName             | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름입니다.                                                                                                                                                                                                                                      |
| ApplicationEnabled                 | bit           | 아니오          | 응용 프로그램을 사용하도록 현재 설정되어 있는지 식별합니다.</br>**참고:** 데이터베이스에 응용 프로그램이 비활성화된 것으로 반영될 수 있지만, 블록체인에 관련 계약이 유지되고 계약에 대한 데이터도 데이터베이스에 남습니다.                     |
| WorkflowId                         | int           | 아니오          | 워크플로에 대한 고유 식별자입니다.                                                                                                                                                                                                                                              |
| WorkflowName                       | nvarchar(50)  | 아니오          | 워크플로의 이름입니다.                                                                                                                                                                                                                                                          |
| WorkflowDisplayName                | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름입니다.                                                                                                                                                                                                                                       |
| WorkflowDescription                | nvarchar(255) | 예         | 워크플로에 대한 설명입니다.                                                                                                                                                                                                                                                   |
| ContractLedgerImplementationId     | nvarchar(255) | 예         | 특정 분산 원장에 대한 스마트 계약의 배포 버전과 연결된 고유 식별자입니다. 예: Ethereum.                                                                                                                                                     |
| ContractId                         | int           | 아니오          | 계약에 대한 고유 식별자입니다.                                                                                                                                                                                                                                              |
| ContractProvisioningStatus         | int           | 아니오          |계약에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다.</br>0 – API에 의해 데이터베이스에 계약이 생성됨</br>1 – 계약이 원장에 전송됨</br>2 – 계약이 원장에 성공적으로 배포됨 |
| ConnectionId                       | int           | 아니오          | 워크플로가 배포된 블록체인 인스턴스의 고유 식별자입니다.                                                                                                                                                                                                        |
| ContractCodeId                     | int           | 아니오          | 계약의 코드 구현에 대한 고유 식별자입니다.                                                                                                                                                                                                                   |
| ContractDeployedByUserId           | int           | 아니오          | 계약을 배포한 사용자의 고유 식별자입니다.                                                                                                                                                                                                                          |
| ContractDeployedByUserExternalId   | nvarchar(255) | 아니오          | 계약을 배포한 사용자의 외부 식별자입니다. 기본적으로 컨소시엄 Azure Active Directory에서 사용자의 ID를 나타내는 GUID입니다.                                                                                                                    |
| ContractDeployedByUserFirstName    | nvarchar(50)  | 예         | 계약을 배포한 사용자의 이름입니다.                                                                                                                                                                                                                                  |
| ContractDeployedByUserLastName     | nvarchar(255) | 예         | 계약을 배포한 사용자의 성입니다.                                                                                                                                                                                                                                   |
| ContractDeployedByUserEmailAddress | nvarchar(255) | 예         | 계약을 배포한 사용자의 이메일 주소입니다.                                                                                                                                                                                                                           |
| WorkflowPropertyId                 | int           | 아니오          | 워크플로 속성에 대한 고유 식별자입니다.                                                                                                                                                                                                                                       |
| WorkflowPropertyDataTypeId         | int           | 아니오          | 워크플로 속성의 데이터 형식 ID입니다.                                                                                                                                                                                                                                  |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | 아니오          | 워크플로 속성의 데이터 형식 이름입니다.                                                                                                                                                                                                                                |
| WorkflowPropertyName               | nvarchar(50)  | 아니오          | 워크플로 속성의 이름입니다.                                                                                                                                                                                                                                                 |
| WorkflowPropertyDisplayName        | nvarchar(255) | 아니오          | UI에 표시할 속성의 표시 이름입니다.                                                                                                                                                                                                                                  |
| WorkflowPropertyDescription        | nvarchar(255) | 예         | 속성에 대한 설명입니다.                                                                                                                                                                                                                                                   |
| ContractPropertyValue              | nvarchar(255) | 아니오          | 계약에 저장된 속성에 대한 값입니다.                                                                                                                                                                                                                                   |
| StateName                          | nvarchar(50)  | 예         | 이 속성에 상태가 포함되어있는 경우 상태의 표시 이름입니다. 상태와 연결되어 있지 않으면 값은 null이 됩니다.                                                                                                                                       |
| StateDisplayName                   | nvarchar(255) | 아니오          | 이 속성에 상태가 포함되어있는 경우 상태의 표시 이름입니다. 상태와 연결되어 있지 않으면 값은 null이 됩니다.                                                                                                                                       |
| StateValue                         | nvarchar(255) | 예         | 이 속성에 상태가 포함되어있는 경우 상태 값입니다. 상태와 연결되어 있지 않으면 값은 null이 됩니다.                                                                                                                                                      |

## <a name="vwuser"></a>vwUser

이 뷰는 Azure Blockchain Workbench를 사용하도록 프로비전된 컨소시엄 멤버에 대한 세부 정보를 제공합니다. 기본적으로, 데이터는 사용자의 초기 프로비전을 통해 채워집니다.

| Name               | type          | Null이 될 수 있음 | 설명                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | 아니오          | 사용자에 대한 고유 식별자입니다.                                                                                                                                                                                                           |
| ExternalID         | nvarchar(255) | 아니오          | 사용자에 대한 외부 식별자입니다. 기본적으로 사용자의 Azure Active Directory ID를 나타내는 GUID입니다.                                                                                                                  |
| ProvisioningStatus | int           | 아니오          |사용자에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다.</br>0 – API에 의해 사용자가 생성됨</br>1 – 키가 데이터베이스의 사용자와 연결됨<br>2 – 사용자가 완전히 프로비전됨 |
| FirstName          | nvarchar(50)  | 예         | 사용자의 이름입니다.                                                                                                                                                                                                               |
| LastName           | nvarchar(50)  | 예         | 사용자의 성입니다.                                                                                                                                                                                                                |
| EmailAddress       | nvarchar(255) | 예         | 사용자의 이메일 주소입니다.                                                                                                                                                                                                            |

## <a name="vwworkflow"></a>vwWorkflow

이 뷰는 워크플로의 함수 및 매개 변수는 물론 코어 워크플로 메타데이터를 나타냅니다. 보고용으로 설계되었으며 여기에는 워크플로와 연결된 응용 프로그램에 대한 메타데이터도 포함됩니다. 이 뷰에는 워크플로에 대한 보고를 용이하게 하는 다수의 기본 테이블의 데이터가 포함됩니다. 각 워크플로에 대해, 이 뷰에는 다음 데이터가 포함됩니다.

-   연결된 응용 프로그램 정의
-   연결된 워크플로 정의
-   연관된 워크플로 시작 상태 정보

| Name                              | type          | Null이 될 수 있음 | 설명                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | 아니오          | 응용 프로그램에 대한 고유 식별자                                                                                                    |
| ApplicationName                   | nvarchar(50)  | 아니오          | 응용 프로그램의 이름                                                                                                                |
| ApplicationDisplayName            | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름                                                                                               |
| ApplicationEnabled                | bit           | 아니오          | 응용 프로그램을 사용하도록 설정되어 있는지 식별                                                                                                   |
| WorkflowId                        | int           | 예         | 워크플로에 대한 고유 식별자                                                                                                         |
| WorkflowName                      | nvarchar(50)  | 아니오          | 워크플로의 이름                                                                                                                   |
| WorkflowDisplayName               | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름                                                                                                |
| WorkflowDescription               | nvarchar(255) | 예         | 워크플로에 대한 설명입니다.                                                                                                           |
| WorkflowConstructorFunctionId     | int           | 아니오          | 워크플로에 대한 생성자로 사용되는 워크플로 함수의 식별자입니다.                                                   |
| WorkflowStartStateId              | int           | 아니오          | 상태에 대한 고유 식별자입니다.                                                                                                         |
| WorkflowStartStateName            | nvarchar(50)  | 아니오          | 상태의 이름입니다.                                                                                                                     |
| WorkflowStartStateDisplayName     | nvarchar(255) | 아니오          | 상태에 대해 사용자 인터페이스에 표시할 이름입니다.                                                                              |
| WorkflowStartStateDescription     | nvarchar(255) | 예         | 워크플로 상태에 대한 설명입니다.                                                                                                       |
| WorkflowStartStateStyle           | nvarchar(50)  | 예         | 이 값은 워크플로가 이 상태일 때의 완료율을 식별합니다.                                                     |
| WorkflowStartStateValue           | int           | 아니오          | 상태 값입니다.                                                                                                            |
| WorkflowStartStatePercentComplete | int           | 아니오          | 상태를 UI에 렌더링하는 방법에 대한 힌트를 클라이언트에 제공하는 텍스트 설명입니다. 지원되는 상태에는 성공(*Success*)과 실패(*Failure*)가 포함됩니다. |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

이 뷰는 워크플로의 함수 및 매개 변수는 물론 코어 워크플로 메타데이터를 나타냅니다. 보고용으로 설계되었으며 여기에는 워크플로와 연결된 응용 프로그램에 대한 메타데이터도 포함됩니다. 이 뷰에는 워크플로에 대한 보고를 용이하게 하는 다수의 기본 테이블의 데이터가 포함됩니다.  각 워크플로 함수에 대해, 이 뷰에는 다음 데이터가 포함됩니다.

-   연결된 응용 프로그램 정의
-   연결된 워크플로 정의
-   워크플로 함수 정보

| Name                                 | type          | Null이 될 수 있음 | 설명                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | 아니오          | 응용 프로그램에 대한 고유 식별자                                              |
| ApplicationName                      | nvarchar(50)  | 아니오          | 응용 프로그램의 이름                                                          |
| ApplicationDisplayName               | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름                                         |
| ApplicationEnabled                   | bit           | 아니오          | 응용 프로그램을 사용하도록 설정되어 있는지 식별                                             |
| WorkflowId                           | int           | 아니오          | 워크플로에 대한 고유 식별자입니다.                                                  |
| WorkflowName                         | nvarchar(50)  | 아니오          | 워크플로의 이름입니다.                                                            |
| WorkflowDisplayName                  | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 워크플로의 이름입니다.                         |
| WorkflowDescription                  | nvarchar(255) | 예         | 워크플로에 대한 설명입니다.                                                     |
| WorkflowFunctionId                   | int           | 아니오          | 함수에 대한 고유 식별자입니다.                                                  |
| WorkflowFunctionName                 | nvarchar(50)  | 예         | 함수의 이름입니다.                                                            |
| WorkflowFunctionDisplayName          | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 함수의 이름                         |
| WorkflowFunctionDescription          | nvarchar(255) | 예         | 워크플로 함수에 대한 설명입니다.                                            |
| WorkflowFunctionIsConstructor        | bit           | 아니오          | 워크플로 함수가 워크플로의 생성자인지 식별합니다.        |
| WorkflowFunctionParameterId          | int           | 아니오          | 함수의 매개 변수에 대한 고유 식별자입니다.                                   |
| WorkflowFunctionParameterName        | nvarchar(50)  | 아니오          | 함수의 매개 변수 이름입니다.                                             |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 함수 매개 변수의 이름               |
| WorkflowFunctionParameterDataTypeId  | int           | 아니오          | 워크플로 함수 매개 변수와 연결된 데이터 형식의 고유 식별자입니다. |
| WorkflowParameterDataTypeName        | nvarchar(50)  | 아니오          | 워크플로 함수 매개 변수와 연결된 데이터 형식의 이름입니다.               |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

이 뷰는 워크플로에 대해 정의된 속성을 나타냅니다. 각 속성에 대해, 이 뷰에는 다음 데이터가 포함됩니다.

-   연결된 응용 프로그램 정의
-   연결된 워크플로 정의
-   워크플로 속성 정보

| Name                         | type          | Null이 될 수 있음 | 설명                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | 아니오          | 응용 프로그램에 대한 고유 식별자입니다.                                                                                                                                                                                                                      |
| ApplicationName              | nvarchar(50)  | 아니오          | 응용 프로그램 이름입니다.                                                                                                                                                                                                                                  |
| ApplicationDisplayName       | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름입니다.                                                                                                                                                                                                                 |
| ApplicationEnabled           | bit           | 아니오          | 응용 프로그램을 사용하도록 현재 설정되어 있는지 식별합니다.</br>**참고:** 데이터베이스에 응용 프로그램이 비활성화된 것으로 반영될 수 있지만, 블록체인에 관련 계약이 유지되고 계약에 대한 데이터도 데이터베이스에 남습니다. |
| WorkflowId                   | int           | 아니오          | 워크플로에 대한 고유 식별자입니다.                                                                                                                                                                                                                         |
| WorkflowName                 | nvarchar(50)  | 아니오          | 워크플로의 이름입니다.                                                                                                                                                                                                                                     |
| WorkflowDisplayName          | nvarchar(255) | 아니오          | 사용자 인터페이스에서 워크플로에 대해 표시할 이름입니다.                                                                                                                                                                                                |
| WorkflowDescription          | nvarchar(255) | 예         | 워크플로에 대한 설명입니다.                                                                                                                                                                                                                                |
| WorkflowPropertyID           | int           | 아니오          | 워크플로의 속성에 대한 고유 식별자입니다. |
| WorkflowPropertyName         | nvarchar(50)  | 아니오          | 속성의 이름                                                                                                                                                                                                                                      |
| WorkflowPropertyDescription  | nvarchar(255) | 예         | 속성에 대한 설명                                                                                                                                                                                                                                 |
| WorkflowPropertyDisplayName  | nvarchar(255) | 아니오          | 사용자 인터페이스에 표시할 이름                                                                                                                                                                                                                  |
| WorkflowPropertyWorkflowId   | int           | 아니오          | 이 속성이 연결되는 워크플로의 ID                                                                                                                                                                                                   |
| WorkflowPropertyDataTypeId   | int           | 아니오          | 속성에 대해 정의된 데이터 형식의 ID                                                                                                                                                                                                              |
| WorkflowPropertyDataTypeName | nvarchar(50)  | 아니오          | 속성에 대해 정의된 데이터 형식의 이름                                                                                                                                                                                                            |
| WorkflowPropertyIsState      | bit           | 아니오          | 이 필드는 이 워크플로 속성에 워크플로의 상태가 포함되어 있는지 식별합니다.                                                                                                                                                                           |

## <a name="vwworkflowstate"></a>vwWorkflowState

이 뷰는 워크플로와 연결된 속성을 나타냅니다. 각 계약에 대해, 이 뷰에는 다음 데이터가 포함됩니다.

-   연결된 응용 프로그램 정의
-   연결된 워크플로 정의
-   워크플로 상태 정보

| Name                         | type          | Null이 될 수 있음 | 설명                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | 아니오          | 응용 프로그램에 대한 고유 식별자입니다.                                                                                                                                                                                                                      |
| ApplicationName              | nvarchar(50)  | 아니오          | 응용 프로그램 이름입니다.                                                                                                                                                                                                                                  |
| ApplicationDisplayName       | nvarchar(255) | 아니오          | 응용 프로그램에 대한 설명                                                                                                                                                                                                                              |
| ApplicationEnabled           | bit           | 아니오          | 응용 프로그램을 사용하도록 현재 설정되어 있는지 식별합니다.</br>**참고:** 데이터베이스에 응용 프로그램이 비활성화된 것으로 반영될 수 있지만, 블록체인에 관련 계약이 유지되고 계약에 대한 데이터도 데이터베이스에 남습니다. |
| WorkflowId                   | int           | 아니오          | 워크플로에 대한 고유 식별자입니다.                                                                                                                                                                                                                       |
| WorkflowName                 | nvarchar(50)  | 아니오          | 워크플로의 이름입니다.                                                                                                                                                                                                                                     |
| WorkflowDisplayName          | nvarchar(255) | 아니오          | 워크플로에 대해 사용자 인터페이스에 표시할 이름입니다.                                                                                                                                                                                                 |
| WorkflowDescription          | nvarchar(255) | 예         | 워크플로에 대한 설명입니다.                                                                                                                                                                                                                              |
| WorkflowStateID              | int           | 아니오          | 상태에 대한 고유 식별자입니다.                                                                                                                                                                                                                          |
| WorkflowStateName            | nvarchar(50)  | 아니오          | 상태의 이름입니다.                                                                                                                                                                                                                                        |
| WorkflowStateDisplayName     | nvarchar(255) | 아니오          | 상태에 대해 사용자 인터페이스에 표시할 이름입니다.                                                                                                                                                                                                 |
| WorkflowStateDescription     | nvarchar(255) | 예         | 워크플로 상태에 대한 설명입니다.                                                                                                                                                                                                                          |
| WorkflowStatePercentComplete | int           | 아니오          | 이 값은 워크플로가 이 상태일 때의 완료율을 식별합니다.                                                                                                                                                                        |
| WorkflowStateValue           | nvarchar(50)  | 아니오          | 상태 값입니다.                                                                                                                                                                                                                               |
| WorkflowStateStyle           | nvarchar(50)  | 아니오          | 상태를 UI에 렌더링하는 방법에 대한 힌트를 클라이언트에 제공하는 텍스트 설명입니다. 지원되는 상태에는 성공(*Success*)과 실패(*Failure*)가 포함됩니다.                                                                                                                    |
