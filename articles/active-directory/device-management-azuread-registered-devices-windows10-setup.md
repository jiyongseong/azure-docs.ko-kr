---
title: "Azure Active Directory 등록 장치 설정 | Microsoft Docs"
description: "Azure Active Directory 등록 장치를 설정하는 방법을 알아봅니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 2560e51d61506389e84288bf983b0ebcb5776ff2
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Azure Active Directory 등록 Windows 10 장치 설정

Azure AD(Active Directory)의 장치 관리를 사용하면 보안 및 규정 준수에 대한 표준을 충족하는 장치에서 사용자 리소스에 액세스할 수 있습니다. 자세한 내용은 [Azure Active Directory의 장치 관리 소개](device-management-introduction.md)를 참조하세요.

**BYOD(Bring Your Own Device)** 시나리오를 사용하도록 설정하려면 Azure AD 등록 장치를 구성합니다. Azure AD에서 Windows 10, iOS, Android 및 macOS용 Azure AD 등록 장치를 구성할 수 있습니다. 이 항목에서는 Windows 10 장치의 관련 단계를 제공합니다. 


## <a name="before-you-begin"></a>시작하기 전에

Windows 10 장치를 등록하려면 장치를 등록할 수 있도록 장치 등록 서비스를 구성해야 합니다. Azure AD 테넌트에 장치를 등록할 수 있는 권한이 필요한 것 외에도, 구성된 장치 최대 수보다 적은 수의 장치를 등록해야 합니다. 자세한 내용은 [장치 설정 구성](device-management-azure-portal.md#configure-device-settings)을 참조하세요.

## <a name="what-you-should-know"></a>알아야 할 사항

장치를 등록할 때 다음에 유의해야 합니다.

- Windows에서는 Azure AD에서 조직의 디렉터리에 장치를 등록합니다.

- Multi-Factor Authentication 챌린지를 통과해야 할 수 있습니다. 이 챌린지는 IT 관리자가 구성할 수 있습니다.

- Azure AD에서 장치를 모바일 장치 관리에 등록해야 하는지 여부를 확인하고 해당되는 경우 등록합니다.

- 관리되는 사용자인 경우 Windows에 자동 로그인되어 바탕 화면이 표시됩니다.

- 페더레이션 사용자의 경우, Windows 로그인 화면으로 이동하고 자격 증명을 입력해야 합니다.


## <a name="registering-a-device"></a>장치 등록

이 섹션에서는 Windows 10 장치를 Azure AD에 등록하는 단계를 제공합니다. Azure AD에 장치를 성공적으로 등록한 경우 **회사 또는 학교 액세스** 대화 상자에 해당 장치에 **회사 또는 학교 계정** 항목이 표시됩니다.

![등록](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Windows 10 장치를 등록하려면**

1. **시작** 메뉴에서 **설정**을 클릭합니다.

    ![설정](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. **계정**을 클릭합니다.

    ![계정](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. **회사 또는 학교 액세스**를 클릭합니다.

    ![회사 또는 학교 액세스](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. **회사 또는 학교 액세스** 대화 상자에서 **연결**을 클릭합니다.

    ![연결](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. **회사 또는 학교 계정 설정** 대화 상자에서 계정 이름(예: someone@example.com)을 입력하고 **다음**을 클릭합니다.

    ![연결](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. **암호 입력** 대화 상자에서 암호를 입력한 후 **다음**을 클릭합니다.

    ![연결](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. **모든 설정을 완료했습니다.** 대화 상자에서 **완료**를 클릭합니다.

    ![연결](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>확인

장치가 Azure AD에 조인되었는지 여부를 확인하려면 장치에서 **회사 또는 학교 액세스** 대화 상자를 검토할 수 있습니다.

![등록](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

또는 Azure AD 포털에서 장치 설정을 검토할 수도 있습니다.

![등록](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Azure Active Directory의 장치 관리 소개](device-management-introduction.md)를 참조하세요.

- Azure AD 포털에서 장치를 관리하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 장치 관리](device-management-azure-portal.md)를 참조하세요.




