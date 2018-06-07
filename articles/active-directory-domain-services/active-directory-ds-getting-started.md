---
title: "Azure Active Directory Domain Services: 시작 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 97803d62ee42d777336dc87c34a16eff426d24d0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화
이 문서에서는 Azure Portal을 사용하여 Azure AD DS(Azure Active Directory Domain Services)를 사용하도록 설정하는 방법을 설명합니다.

**Azure AD Domain Services** 마법사를 시작하려면 다음 단계를 완료합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 왼쪽 창에서 **리소스 만들기**를 클릭합니다.
3. **새로 만들기** 페이지의 검색 창에 **Domain Services**를 입력합니다.

    ![Domain Services 검색](./media/getting-started/search-domain-services.png)

4. 검색 제안 목록에서 **Azure AD Domain Services**를 클릭하여 선택합니다. **Azure AD Domain Services** 페이지에서 **만들기** 단추를 클릭합니다.

    ![Domain Services 보기](./media/getting-started/domain-services-blade.png)

5. **Azure AD Domain Services 사용** 마법사가 시작됩니다.


## <a name="task-1-configure-basic-settings"></a>작업 1: 기본 설정 구성
마법사의 **기본 사항** 페이지에서 관리되는 도메인에 대한 DNS 도메인 이름을 지정할 수 있습니다. 관리되는 도메인이 배포되어야 하는 Azure 위치 및 리소스 그룹을 선택할 수도 있습니다.

![기본 사항 구성](./media/getting-started/domain-services-blade-basics.png)

1. 관리되는 도메인에 대한 **DNS 도메인 이름**을 선택합니다.

   > [!NOTE]
   > **DNS 도메인 이름 선택 지침**
   > * **기본 제공 도메인 이름:** 마법사에서 기본적으로 디렉터리의 기본/기본 제공 도메인 이름(**.onmicrosoft.com** 접미사 포함)을 지정합니다. 인터넷을 통해 관리되는 도메인에 보안 LDAP 액세스를 사용하도록 선택하는 경우 공용 DNS 레코드를 만들거나 이 도메인 이름에 대한 공용 CA에서 보안 LDAP 인증서를 가져오는 데 문제가 있을 수 있습니다. Microsoft는 *.onmicrosoft.com* 도메인을 소유하고 있으며, CA는 이 도메인에 대해 보증하는 인증서를 발급하지 않습니다.
   * **사용자 지정 도메인 이름:** 사용자 지정 도메인 이름을 입력할 수도 있습니다. 이 예에서 사용자 지정 도메인 이름은 *contoso100.com*입니다.
   * **라우팅할 수 없는 도메인 접미사:** 일반적으로 라우팅할 수 없는 도메인 이름 접미사는 사용하지 않는 것이 좋습니다. 예를 들어 'contoso.local' DNS 도메인 이름을 사용하여 도메인을 만들지 않는 것이 좋습니다. '.local' DNS 접미사는 라우팅할 수 없으며, DNS 확인에서 문제가 발생할 수 있습니다.
   * **도메인 접두사 제한:** 지정한 도메인 이름의 접두사(예: *contoso100.com* 도메인 이름의 *contoso100*)는 15자 이하의 문자를 포함해야 합니다. 15자보다 긴 접두사로 관리되는 도메인을 만들 수 없습니다.
   * **네트워크 이름 충돌:** 관리되는 도메인에 대해 선택한 DNS 도메인 이름이 가상 네트워크에 아직 존재하지 않는지 확인합니다. 특히,
       * 가상 네트워크에 동일한 DNS 도메인 이름을 가진 Active Directory 도메인이 이미 있습니다.
       * 관리되는 도메인을 사용하도록 설정할 가상 네트워크에 온-프레미스 네트워크와의 VPN 연결이 있는지 확인합니다. 이 시나리오에서는 온-프레미스 네트워크에 동일한 DNS 도메인 이름을 가진 도메인이 없는지 확인합니다.
       * 가상 네트워크에 해당 이름을 가진 기존 클라우드 서비스가 있는 경우
    >

2. 관리되는 도메인을 만들려는 Azure **구독**을 선택합니다.

3. 관리되는 도메인이 속해야 하는 **리소스 그룹**을 선택합니다. **새로 만들기** 또는 **기존 항목 사용** 옵션을 선택하여 리소스 그룹을 선택할 수 있습니다.

4. 관리되는 도메인을 만들어야 하는 Azure **위치**를 선택합니다. 마법사의 **네트워크** 페이지에는 선택한 위치에 속하는 가상 네트워크만 표시됩니다.

5. 완료되면 **확인**을 클릭하여 마법사의 **네트워크** 페이지로 이동합니다.


## <a name="next-step"></a>다음 단계
[작업 2: 네트워크 설정 구성](active-directory-ds-getting-started-network.md)
