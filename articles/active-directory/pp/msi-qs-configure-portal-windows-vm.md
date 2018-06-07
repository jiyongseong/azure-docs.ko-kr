---
title: "Azure Portal을 사용하여 Azure VM에서 MSI를 구성하는 방법"
description: "Azure Portal을 사용하여 Azure VM에서 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침을 제공합니다."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 37710015904c8112e5d2de504ed5b42895ffb809
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Azure Portal을 사용하여 VM MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure Portal을 사용하여 Azure VM에 대해 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM 생성 중에 MSI를 사용하도록 설정

이 문서 작성 당시에는 Azure Portal에서 VM을 생성하는 중에 MSI를 사용하도록 설정할 수 없었습니다. 대신에 VM을 처음 만들려면 다음 VM 만들기 퀵 스타트 문서 중 하나를 참조하세요.

- [Azure Portal을 사용하여 Windows 가상 머신 만들기](~/articles/virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Azure Portal을 사용하여 Linux 가상 머신 만들기](~/articles/virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

그런 후에 다음 섹션으로 진행하여 VM에서 MSI를 사용하도록 설정하는 방법을 자세히 확인하세요.

## <a name="enable-msi-on-an-existing-azure-vm"></a>기존 Azure VM에서 MSI를 사용하도록 설정

원래 MSI 없이 프로비전된 VM이 있는 경우 다음을 수행합니다.

1. VM을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 참여자”.

2. 원하는 Virtual Machine으로 이동합니다.

2. "구성" 페이지를 클릭하고 "관리 서비스 ID"에서 "예"를 선택하여 VM에서 MSI를 사용하도록 설정한 후에 **저장**을 클릭합니다. 이 작업을 완료하려면 60초 이상 걸릴 수 있습니다.

   ![구성 페이지 스크린샷](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM에서 MSI 제거

MSI가 더 이상 필요하지 않은 Virtual Machine이 있는 경우 다음을 수행합니다.

1. VM을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 참여자”.

2. 원하는 Virtual Machine으로 이동합니다.

3. "구성" 페이지를 클릭하고 "관리 서비스 ID"에서 "아니요"를 선택하여 VM에서 MSI를 제거한 후에 **저장**을 클릭합니다. 이 작업을 완료하려면 60초 이상 걸릴 수 있습니다.

   ![구성 페이지 스크린샷](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>관련 콘텐츠

- MSI의 개요는 [관리 서비스 ID 개요](msi-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 Azure VM MSI에 [다른 Azure 리소스 액세스 권한](msi-howto-assign-access-portal.md)을 제공합니다.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.
