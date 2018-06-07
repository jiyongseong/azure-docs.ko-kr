---
title: 시작 및 중지 Azure 스택 | Microsoft Docs
description: Azure 스택 종료 및 시작 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 53015ba5c282bbe9c7b8185b080ffb6d834b6c75
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="start-and-stop-azure-stack"></a>시작 및 중지 Azure 스택
올바르게 종료 하 고 Azure 스택 서비스를 다시 시작이 문서의 절차를 따라야 합니다. 

## <a name="stop-azure-stack"></a>Azure 스택 중지 

다음 단계를 통해 Azure 스택을 종료 합니다.

1. Azure 스택 ERCS Vm에 대 한 네트워크 액세스 된 컴퓨터에서 권한 있는 끝점 세션 (PEP)를 엽니다. 자세한 내용은 [권한 있는 끝점을 사용 하 여 Azure 스택의](azure-stack-privileged-endpoint.md)합니다.

2. PEP에서 실행 합니다.

    ```powershell
      Stop-AzureStack
    ```

3. 오프 전원을 모든 실제 Azure 스택 노드를 기다립니다.

> [!Note]  
> 제조업체 (OEM) Azure 스택 하드웨어를 제공 하는 지침에 따라 실제 노드 전원 상태를 확인할 수 있습니다. 

## <a name="start-azure-stack"></a>Azure 스택 시작 

Azure 스택 다음 단계를 시작 합니다. Azure 스택 중지 하는 방법에 관계 없이 다음이 단계를 수행 합니다.

1. 각 실제 노드에서 Azure 스택 환경에서 전원을 켭니다. 제조업체 (OEM) Azure 스택에 대 한 하드웨어를 제공 하는 지침에 따라 실제 노드에 대 한 명령에 따라 전원을 확인 합니다.

2. Azure 스택 인프라 서비스를 시작 될 때까지 기다립니다. Azure 스택 인프라 서비스는 시작 프로세스를 완료 하는 데 두 개의 시간이 필요할 수 있습니다. Azure 스택을의 시작 상태를 확인할 수는 [ **Get ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack)합니다.


## <a name="get-the-startup-status-for-azure-stack"></a>Azure 스택에 대 한 시작 상태를 가져옵니다.

다음 단계를 통해 Azure 스택 시작 루틴에 대 한 시작을 가져옵니다.

1. Azure 스택 ERCS Vm에 대 한 네트워크 액세스 된 컴퓨터에서 권한 있는 끝점 세션을 엽니다.

2. PEP에서 실행 합니다.

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Azure 스택 요소의 시작 및 종료 문제 해결

인프라 및 테 넌 트 서비스 Azure 스택 환경에서 전원 이후 2 시간을 성공적으로 시작 하지 않는 다음 단계를 수행 합니다. 

1. Azure 스택 ERCS Vm에 대 한 네트워크 액세스 된 컴퓨터에서 권한 있는 끝점 세션을 엽니다.

2. 다음을 실행합니다. 

    ```powershell
      Test-AzureStack
      ```

3. 출력을 검토 하 고 모든 상태 오류를 해결 합니다. 자세한 내용은 참조 [Azure 스택의 유효성 검사 테스트를 수행](azure-stack-diagnostic-test.md)합니다.

4. 다음을 실행합니다.

    ```powershell
      Start-AzureStack
    ```

5. 실행 하는 경우 **시작 AzureStack** 결과 오류를 Microsoft 고객 서비스 지원에 문의 합니다. 

## <a name="next-steps"></a>다음 단계 

Azure 스택 진단 도구에 대 한 자세한 정보 및 로깅 문제점, 참조 [Azure 스택 진단 도구](azure-stack-diagnostics.md)합니다.
