---
title: 파트너 ID에 Azure 계정 연결 | Microsoft Docs
description: 고객의 리소스를 관리하기 위해 사용하는 사용자 계정에 파트너 ID를 연결하여 Azure 고객 관리를 추적합니다.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a5b6bb683538bd5359ebcbbe2640216e574b3e00
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164621"
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Azure 계정에 파트너 ID 연결

파트너로써 고객의 리소스를 관리하기 위해 사용하는 계정에 파트너 ID를 연결하여 고객 관리에 대한 영향을 추적할 수 있습니다.

이 기능은 공개 미리 보기 상태로 지원됩니다.

## <a name="get-access-from-your-customer"></a>고객의 액세스 권한 가져오기

파트너 ID를 연결하려면 먼저 고객이 다음 옵션 중 하나를 사용하여 해당 Azure 리소스에 대한 액세스 권한을 사용자에게 부여해야 합니다.

- **게스트 사용자:** 고객은 사용자를 게스트 사용자로 추가하고 RBAC 역할을 할당할 수 있습니다. 자세한 내용은 [다른 디렉터리에서 게스트 사용자 추가](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)를 참조하세요.

- **디렉터리 계정:** 고객이 해당 디렉터리에서 조직의 새 사용자를 만들고 RBAC 역할을 할당할 수 있습니다.

- **서비스 주체:** 고객이 해당 디렉터리에서 조직의 앱 또는 스크립트를 추가하고 RBAC 역할을 할당할 수 있습니다. 앱 또는 스크립트의 ID는 서비스 주체라고 알려집니다.

## <a name="link-partner-id"></a>파트너 ID 연결

고객의 리소스에 액세스할 수 있는 경우 Azure Portal, PowerShell 또는 CLI를 사용하여 MPN ID(Microsoft 파트너 네트워크 ID)를 사용자 ID 또는 서비스 주체에 연결합니다. 각 고객 테넌트에서 파트너 ID를 연결해야 합니다.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Azure Portal을 사용하여 새 파트너 ID 연결

1. Azure Portal에서 [파트너 ID에 연결](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade)로 이동합니다.

2. Azure 포털에 로그인합니다.

3. Microsoft 파트너 ID를 입력합니다. 파트너 ID는 조직의 [MPN(Microsoft 파트너 네트워크)](https://partner.microsoft.com/) ID입니다.

  ![파트너 ID 연결을 보여 주는 스크린샷](./media/billing-link-partner-id/link-partner-ID.PNG)

4. 다른 고객의 파트너 ID를 연결하려면 디렉터리 전환기를 사용합니다. [디렉터리 전환] 아래에서 디렉터리를 선택합니다.

  ![파트너 ID 연결을 보여 주는 스크린샷](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>PowerShell을 사용하여 새 파트너 ID 연결

1. [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview) PowerShell 모듈을 설치합니다.

2. 사용자 계정 또는 서비스 주체를 사용하여 고객의 테넌트에 로그인합니다. 자세한 정보는 [PowerShell을 사용하여 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0)을 참조하세요.
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. 새 파트너 ID를 연결합니다. 파트너 ID는 조직의 [MPN(Microsoft 파트너 네트워크)](https://partner.microsoft.com/) ID입니다.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>연결된 파트너 ID 가져오기
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>연결된 파트너 ID 업데이트
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>연결된 파트너 ID 삭제
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>CLI를 사용하여 새 파트너 ID 연결
1.  CLI 확장을 설치합니다.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  사용자 계정 또는 서비스 주체를 사용하여 고객의 테넌트에 로그인합니다. 자세한 내용은 [Azure CLI 2.0을 사용하여 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 참조하세요.

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  새 파트너 ID를 연결합니다. 파트너 ID는 조직의 [MPN(Microsoft 파트너 네트워크)](https://partner.microsoft.com/) ID입니다.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>연결된 파트너 ID 가져오기
```azurecli-interactive
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>연결된 파트너 ID 업데이트
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>연결된 파트너 ID 삭제
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
``` 

## <a name="next-steps"></a>다음 단계

[Microsoft 파트너 커뮤니티](https://aka.ms/PALdiscussion)의 토론에 참여하여 업데이트를 받거나 의견을 보냅니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**파트너 ID를 연결할 수 있는 사용자는 누구인가요?**

고객의 리소스를 관리하고 있는 파트너 조직의 모든 사용자는 계정에 파트너 ID를 연결할 수 있습니다.

**파트너 ID가 연결되면 변경할 수 있나요?**

예, 연결된 파트너 ID를 변경하거나, 추가하거나, 제거할 수 있습니다.

**사용자가 여러 고객 테넌트에 계정을 보유한 경우에 어떻게 되나요?**

파트너 ID와 계정 간의 연결은 고객 테넌트 각각에 수행됩니다.  각 고객 테넌트에서 파트너 ID를 연결해야 합니다.

**다른 파트너 또는 고객이 파트너 ID에 대한 연결을 편집하거나 제거할 수 있나요?**

연결은 계정 수준에 관련됩니다. 사용자만이 파트너 ID에 대한 연결을 편집하고 제거할 수 있습니다. 고객 및 다른 파트너는 파트너 ID에 대한 연결을 변경할 수 없습니다. 
