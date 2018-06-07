---
title: Azure CLI를 사용하여 사용자 할당 MSI(관리 서비스 ID) 를 관리하는 방법
description: Azure CLI를 사용하여 사용자 할당 관리 서비스 ID를 만들고 나열하고 삭제하는 방법에 대한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 5262914e469bdc07921c3b82e990d544349b5fd4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930669"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Azure CLI를 사용하여 사용자 할당 ID 생성, 나열 또는 삭제

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

관리 서비스 ID는 Azure Active Directory에서 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure CLI를 사용하여 사용자 할당 ID 만들고 나열하고 삭제하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

- CLI 스크립트 예제는 다음의 세 가지 옵션 중 하나로 실행할 수 있습니다.

    - Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다(다음 섹션 참조).
    - 각 코드 블록의 오른쪽 위에 있는 "사용해 보세요." 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
    - 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.13 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다. 사용자 할당 ID를 배포하려는 Azure 구독과 연결된 계정으로 `az login`을 사용하여 Azure에 로그인합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 ID를 만들려면 [az identity create](/cli/azure/identity#az-identity-create) 명령을 사용합니다. `-g` 매개 변수는 사용자 할당 ID가 만들어질 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

> [!IMPORTANT]
> 사용자 할당 ID 만들기는 영숫자와 하이픈(0-9 또는 a-z 또는 A-Z 또는 -) 문자만 지원합니다. 또한 VM/VMSS에 대한 할당이 제대로 작동하려면 이름의 길이가 24자로 제한되어야 합니다. 업데이트를 다시 확인하십시오. 자세한 내용은 [FAQ 및 알려진 문제](known-issues.md)를 참조하세요.

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>사용자 할당 ID 나열

사용자 할당 ID를 나열하려면 [az identity list](/cli/azure/identity#az-identity-list) 명령을 사용합니다.  `-g` 매개 변수는 사용자 할당 ID가 만들어지는 리소스 그룹을 지정합니다.  `<RESOURCE GROUP>`을 원하는 값으로 바꿉니다.

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
JSON 응답에서 사용자 ID에는 키 `type`에 대해 반환된 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 값이 있습니다.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>사용자 할당 ID 삭제

사용자 할당 ID를 삭제하려면 [az identity delete](/cli/azure/identity#az-identity-delete) 명령을 사용합니다.  -n 매개 변수는 그 이름을 지정하고 -g 매개 변수는 사용자 할당 ID가 만들어진 리소스 그룹을 지정합니다.  `<USER ASSIGNED IDENTITY NAME>` 및 `<RESOURCE GROUP>` 매개 변수 값을 원하는 값으로 바꿉니다.

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> 사용자 할당 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. 이러한 항목을 VM/VMSS에서 제거하려면 `az vm/vmss identity remove` 명령을 사용합니다.

## <a name="related-content"></a>관련 콘텐츠

Azure CLI identity 명령의 전체 목록을 보려면 [az identity](/cli/azure/identity)를 참조하세요.

Azure VM에 사용자 할당 ID를 할당하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 MSI(관리 서비스 ID) 구성](qs-configure-cli-windows-vm.md#user-assigned-identity)을 참조하세요.


 
