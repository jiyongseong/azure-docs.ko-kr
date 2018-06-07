---
title: "첫 번째 실행 동안 Azure AD에 새 Windows 10 장치 조인 | Microsoft Docs"
description: "첫 실행 경험 동안 사용자가 Azure AD 조인을 설정하는 방법에 대해 설명하는 항목입니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 0e71df2333dee9c4eb9935d3397d343be246be65
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>첫 번째 실행 동안 Azure AD에 새 Windows 10 장치 조인

Azure AD(Active Directory)의 장치 관리를 사용하면 보안 및 규정 준수에 대한 표준을 충족하는 장치에서 사용자 리소스에 액세스할 수 있습니다. 자세한 내용은 [Azure Active Directory의 장치 관리 소개](device-management-introduction.md)를 참조하세요.

Windows 10을 사용할 경우 FRX(첫 실행 경험) 동안 Azure AD에 새 장치를 조인할 수 있습니다.  
이렇게 하면 축소 래핑된 장치를 직원이나 학생에게 배포할 수 있습니다.

Windows 10 Professional 또는 Windows 10 Enterprise가 장치에 설치된 경우 환경은 기본적으로 회사 소유의 장치에 대한 설정 프로세스로 지정됩니다.

Windows *기본 환경*에서 온-프레미스 AD(Active Directory) 도메인 조인은 지원되지 않습니다. 컴퓨터를 AD 도메인에 조인하려는 경우 **로컬 계정을 사용하여 Windows 설정** 링크를 선택해야 합니다. 그런 다음 컴퓨터의 설정에서 도메인에 조인할 수 있습니다.
 


## <a name="before-you-begin"></a>시작하기 전에

Windows 10 장치를 조인하려면 장치를 등록할 수 있도록 장치 등록 서비스를 구성해야 합니다. Azure AD 테넌트에 장치를 조인할 수 있는 권한이 필요한 것 외에도, 구성된 장치 최대 수보다 적은 수의 장치를 등록해야 합니다. 자세한 내용은 [장치 설정 구성](device-management-azure-portal.md#configure-device-settings)을 참조하세요.

## <a name="joining-a-device"></a>장치 조인

**FRX 동안 Windows 10 장치를 Azure AD에 조인하려면**


1. 새 장치를 켜고 설정 프로세스를 시작하면 **준비 중** 메시지가 표시됩니다. 프롬프트에 따라 장치를 설정합니다.

2. 국가 및 언어를 사용자 지정하는 작업부터 시작합니다. 그런 다음 Microsoft 소프트웨어 사용 조건에 동의합니다.
 
    ![해당 지역에 대한 사용자 지정](./media/device-management-azuread-joined-devices-frx/01.png)

3. 인터넷에 연결하는 데 사용할 네트워크를 선택합니다.

4. **이 장치는 내 조직에서 소유한 장치입니다**를 클릭합니다. 

    ![이 PC를 누가 소유하고 있나요? 화면](./media/device-management-azuread-joined-devices-frx/02.png)

5. 조직에서 제공한 자격 증명을 입력하고 **로그인**을 클릭합니다.

    ![로그인 화면](./media/device-management-azuread-joined-devices-frx/03.png)

6. 장치는 Azure AD에서 일치하는 테넌트를 찾습니다. 페더레이션된 도메인의 사용자인 경우 온-프레미스 STS(보안 토큰 서비스)가 서버(예: AD FS(Active Directory Federation Services))로 리디렉션됩니다.

7. 페더레이션되지 않은 도메인의 사용자의 경우 Azure AD 호스팅 페이지에 직접 자격 증명을 입력해야 합니다. 

8. 다단계 인증 질문에 대한 메시지가 표시됩니다. 
 
9. Azure AD는 모바일 장치 관리에 등록해야 하는지 여부를 확인합니다.

10. Windows에서 조직의 디렉터리에 있는 장치를 Azure AD에 등록하고 해당하는 경우 모바일 장치 관리에 등록합니다.

11. 다음 사용자인 경우 해당 작업이 수행됩니다.
    - 관리되는 사용자인 경우 자동 로그인 프로세스를 통해 데스크톱으로 이동됩니다.

    - 페더레이션 사용자인 경우 자격 증명을 입력하도록 Windows 로그인 화면으로 이동됩니다.

## <a name="verification"></a>확인

장치가 Azure AD에 조인되었는지 여부를 확인하려면 Windows 장치에서 **회사 또는 학교 액세스** 대화 상자를 검토합니다. 이 대화 상자에는 사용자가 Azure AD 디렉터리에 연결되어 있다고 표시됩니다.

![회사 또는 학교 액세스](./media/device-management-azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Azure Active Directory의 장치 관리 소개](device-management-introduction.md)를 참조하세요.

- Azure AD 포털에서 장치를 관리하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 장치 관리](device-management-azure-portal.md)를 참조하세요.
