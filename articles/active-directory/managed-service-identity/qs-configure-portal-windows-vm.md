---
title: Azure Portal을 사용하여 Azure VM에서 MSI를 구성하는 방법
description: Azure Portal을 사용하여 Azure VM에서 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침을 제공합니다.
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
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 62b8504f5c10f338539d263bb231cf96eb405ba6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930343"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Azure Portal을 사용하여 VM MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure Portal을 사용하여 Azure VM에 시스템 할당 ID를 사용하거나 사용하지 않도록 설정하는 방법을 알아봅니다. Azure VM에서 사용자 할당 ID를 할당 및 제거하는 기능은 현재 Azure Portal을 통해 지원되지 않습니다.

> [!NOTE]
> 현재 사용자 할당 ID 작업은 Azure Portal을 통해 지원되지 않습니다. 업데이트를 다시 확인하십시오. 

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Azure VM 생성 시 관리 서비스 ID

현재 Azure Portal을 통한 VM 만들기는 관리 서비스 ID 작업을 지원되지 않습니다. 대신에 VM을 처음 만들려면 다음 VM 만들기 퀵 스타트 문서 중 하나를 참조하세요.

- [Azure Portal을 사용하여 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Azure Portal을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

그런 후에 다음 섹션으로 진행하여 VM에서 관리 서비스 ID를 사용하도록 설정하는 방법에 대한 자세한 내용을 확인하세요.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>기존 Azure VM에서 관리 서비스 ID를 사용하도록 설정

원래 시스템 할당 ID 없이 프로비전된 VM에 시스템 할당 ID를 사용하도록 설정하려면:

1. VM을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 참여자”.

2. 원하는 가상 머신으로 이동하여 "구성" 페이지를 선택합니다.

3. "관리 서비스 ID"에서 "예"를 선택하여 VM에서 시스템 할당 ID를 사용하도록 설정한 다음, **저장**을 클릭합니다. 이 작업을 완료하려면 60초 이상 걸릴 수 있습니다.

    > [!NOTE]
    > VM에 사용자 할당 ID를 추가하는 작업은 현재 Azure Portal을 통해 지원되지 않습니다.

   ![구성 페이지 스크린샷](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Azure VM에서 관리 서비스 ID 제거

시스템 할당 ID가 더 이상 필요하지 않은 가상 머신이 있는 경우:

1. VM을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 참여자”.

2. 원하는 가상 머신으로 이동하여 "구성" 페이지를 선택합니다.

3. "관리 서비스 ID"에서 "아니오"를 선택하여 VM에서 시스템 할당 ID를 사용하지 않도록 설정한 다음, 저장을 클릭합니다. 이 작업을 완료하려면 60초 이상 걸릴 수 있습니다.

    > [!NOTE]
    > VM에 사용자 할당 ID를 추가하는 작업은 현재 Azure Portal을 통해 지원되지 않습니다.

   ![구성 페이지 스크린샷](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>관련 콘텐츠

- 관리 서비스 ID에 대한 개요는 [개요](overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 Azure VM MSI에 [다른 Azure 리소스 액세스 권한](howto-assign-access-portal.md)을 제공합니다.

