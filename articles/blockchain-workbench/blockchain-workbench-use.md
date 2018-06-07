---
title: Azure Blockchain Workbench에서 응용 프로그램 사용
description: Azure Blockchain Workbench에서 응용 프로그램 계약을 사용하는 방법.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b15d4980875b805b49f7dc1cdb941e607232f3cb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258042"
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Azure Blockchain Workbench에서 응용 프로그램 사용

Blockchain Workbench를 사용하여 계약에서 만들고 작업을 수행할 수 있습니다. 또한 상태와 트랜잭션 기록 같은 계약 세부 정보를 볼 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Blockchain Workbench 배포. 자세한 내용은 배포에 대한 세부 사항을 위한 [Azure Blockchain Workbench 배포](blockchain-workbench-deploy.md)를 참조하세요.
* Blockchain Workbench에 배포된 블록체인 응용 프로그램. [Azure Blockchain Workbench에서 블록체인 응용 프로그램 만들기]()를 참조하세요.

브라우저에서 [Blockchain Workbench를 엽니다](blockchain-workbench-deploy.md#blockchain-workbench-web-url).

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Blockchain Workbench의 구성원으로 로그인해야 합니다. 나열된 응용 프로그램이 없는 경우 Blockchain Workbench의 구성원이지만 모든 응용 프로그램의 구성원은 아닙니다. Blockchain Workbench 관리자는 응용 프로그램에 구성원을 할당할 수 있습니다.

## <a name="create-new-contract"></a>새 계약 만들기 

새 계약을 만들려면 계약 **초기자**로 지정된 구성원여야 합니다. 계약에 대한 응용 프로그램 역할 및 초기자 정의에 대한 내용은 [구성 개요의 워크플로](blockchain-workbench-configuration-overview.md#workflows)를 참조하세요. 응용 프로그램 역할에 구성원 할당에 대한 내용은 [응용 프로그램에 구성원 추가](blockchain-workbench-manage-users.md#add-member-to-application)를 참조하세요.

1. Blockchain Workbench 응용 프로그램 섹션에서 만들 계약이 포함된 응용 프로그램 타일을 선택합니다. 활성 계약 목록이 표시됩니다.

2. 새 계약을 만들려면 **새 계약**을 선택합니다.

    ![새 계약 단추](media/blockchain-workbench-use/contract-list.png)

3. **새 계약** 창이 표시됩니다. 초기 매개 변수 값을 지정합니다. **만들기**를 선택합니다.

    ![새 계약 창](media/blockchain-workbench-use/new-contract.png)

    새로 만든 계약이 다른 활성 계약이 포함된 목록에 표시됩니다.

    ![활성 계약 목록](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>계약에서 작업 수행

계약의 상태에 따라 구성원은 계약의 다음 상태로 전환하는 작업을 수행할 수 있습니다. 작업은 [상태](blockchain-workbench-configuration-overview.md#states) 내에서 [전환](blockchain-workbench-configuration-overview.md#transitions)으로 정의됩니다. 전환에 대해 허용되는 응용 프로그램 또는 인스턴스 역할에 속하는 구성원은 작업을 수행할 수 있습니다. 

1. Blockchain Workbench 응용 프로그램 섹션에서 작업을 수행할 계약이 포함된 응용 프로그램 타일을 선택합니다.
2. 목록에서 계약을 선택합니다. 계약에 대한 세부 정보는 서로 다른 섹션에 표시됩니다. 

    ![계약 세부 정보](media/blockchain-workbench-use/contract-details.png)

    | 섹션  | 설명  |
    |---------|---------|
    | 상태 | 계약 단계 내에서 현재 진행 상태를 나열합니다. |
    | 세부 정보 | 계약의 현재 값 |
    | 조치 | 마지막 동작에 대한 세부 정보 |
    | 작업 | 계약의 트랜잭션 기록 |
    
3. **동작** 섹션에서 **작업 수행**을 선택합니다.

4. 계약의 현재 상태에 대한 세부 정보가 창에 표시됩니다. 드롭다운 목록에서 수행하려는 작업을 선택합니다. 

    ![작업 선택](media/blockchain-workbench-use/choose-action.png)

5. **작업 수행**을 선택하여 작업을 시작합니다.
6. 작업에 매개 변수가 필요한 경우 작업에 대한 값을 지정합니다.

    ![작업 수행](media/blockchain-workbench-use/take-action.png)

7. **작업 수행**을 선택하여 작업을 실행합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 문제 해결 방법](blockchain-workbench-troubleshooting.md)