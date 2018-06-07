---
title: Azure Portal에서 역할 기반 Access Control | Microsoft Docs
description: Azure Portal에서 역할 기반 Access Control을 통해 액세스 관리를 시작합니다. 리소스에 대한 사용 권한을 할당하기 위해 역할 할당을 사용합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 4ac7fda78f456a233c8dba90a6a50e19774991df
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203655"
---
# <a name="use-role-based-access-control-to-manage-access-to-your-azure-subscription-resources"></a>역할 기반 Access Control을 사용하여 Azure 구독 리소스에 대한 액세스 관리
> [!div class="op_single_selector"]
> * [사용자 또는 그룹에 따른 액세스 관리](role-assignments-users.md)
> * [리소스에 따른 액세스 관리](role-assignments-portal.md)

Azure 역할 기반 Access Control(RBAC)을 통해 Azure에 대한 세밀한 액세스 관리가 가능합니다. RBAC를 사용하여 사용자가 해당 작업을 수행하는 데 필요한 액세스 만큼 권한을 부여할 수 있습니다. 이 문서에서는 Azure 포털에 RBAC를 준비하고 실행하도록 도와줍니다. RBAC를 사용하여 액세스를 관리하는 방법에 대한 자세한 정보를 원하는 경우 [역할 기반 Access Control란](overview.md)을 참조하세요.

각 구독 내에서 최대 2000개의 역할 할당을 부여할 수 있습니다. 

## <a name="view-access"></a>액세스 보기
[Azure 포털](https://portal.azure.com)의 기본 블레이드에서 리소스, 리소스 그룹 또는 구독에 대한 액세스 권한이 있는 사용자를 볼 수 있습니다. 예를 들어 리소스 그룹 중 하나에 대한 액세스 권한이 있는 사용자를 확인하려는 경우 다음과 같이 합니다.

1. 왼쪽 탐색 모음에서 **리소스 그룹** 을 선택합니다.  
    ![리소스 그룹 - 아이콘](./media/role-assignments-portal/resourcegroups_icon.png)
2. **리소스 그룹** 블레이드에서 리소스 그룹의 이름을 선택합니다.
3. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.  
4. 액세스 제어 블레이드에 리소스 그룹에 대한 액세스가 부여된 모든 사용자, 그룹 및 응용 프로그램이 나열됩니다.  
   
    ![사용자 블레이드 - 상속된 및 할당된 액세스 스크린샷](./media/role-assignments-portal/view-access.png)

일부 역할의 범위는 **이 리소스**이지만 나머지는 다른 범위에서 **상속**됩니다. 액세스는 리소스 그룹에 특별히 할당되거나 부모 구독에 할당된 내용에서 상속됩니다.

> [!NOTE]
> 클래식 구독 관리자와 공동 관리자는 새 RBAC 모델에서 구독의 소유자로 간주됩니다.

## <a name="add-access"></a>액세스 추가
역할 할당의 범위인 리소스, 리소스 그룹 또는 구독 내에서 액세스 권한을 부여합니다.

1. 액세스 제어 블레이드에서 **추가**를 선택합니다.  
2. **역할 선택** 블레이드에서 할당하고자 하는 역할을 선택합니다.
3. 액세스 권한을 부여할 디렉터리에서 사용자, 그룹 또는 응용 프로그램을 선택합니다. 표시 이름, 전자 메일 주소 및 개체 식별자를 사용하여 디렉터리를 검색할 수 있습니다.  
   
    ![사용자 추가 블레이드 - 검색 스크린샷](./media/role-assignments-portal/grant-access2.png)
4. **확인** 을 선택하여 할당을 만듭니다. **사용자 추가** 팝업은 진행 상태를 추적합니다.  
    ![사용자 진행률 표시줄 추가 - 스크린샷](./media/role-assignments-portal/addinguser_popup.png)

역할 할당을 성공적으로 추가한 후에 **사용자** 블레이드가 나타납니다.

## <a name="remove-access"></a>액세스 제거
1. 제거하려는 할당의 이름 위로 마우스를 가져갑니다. 이름 옆에 확인란이 나타납니다.
2. 확인란을 사용하여 하나 이상의 역할 할당을 선택합니다.
2. **제거**를 선택합니다.  
3. **예**를 선택하여 제거를 확인합니다.

상속된 할당을 제거할 수 있습니다. 상속된 할당을 제거하려면 역할 할당이 만들어진 범위에서 할당을 제거해야 합니다. **범위** 열에서 **상속됨** 옆쪽을 이 역할이 할당된 리소스로 이동하는 링크가 있습니다. 여기에 나열된 리소스로 이동하여 역할 할당을 제거합니다.

![사용자 블레이드 - 상속된 액세스는 제거 단추를 사용할 수 없도록 설정함 스크린샷](./media/role-assignments-portal/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>액세스를 관리하는 기타 도구
Azure 포털 이외의 도구에서 Azure RBAC 명령을 사용하여 역할을 할당하고 액세스를 관리할 수 있습니다.  필수 조건 및 Azure RBAC 명령을 시작하는 방법에 대한 링크를 따라가세요.

* [Azure PowerShell](role-assignments-powershell.md)
* [Azure 명령줄 인터페이스](role-assignments-cli.md)
* [REST API](role-assignments-rest.md)

## <a name="next-steps"></a>다음 단계
* [액세스 변경 기록 보고서 만들기](change-history-report.md)
* [RBAC 기본 제공 역할](built-in-roles.md)
* [Azure RBAC에서 사용자 지정 역할](custom-roles.md)

