---
title: Azure Active Directory 조건부 액세스를 위한 SharePoint Online 및 Exchange Online 설정 | Microsoft Docs
description: Azure Active Directory 조건부 액세스를 위해 SharePoint Online 및 Exchange Online을 설정하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: aff37c4b440fe545ba69a409b9dcd83deabf7660
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스를 위한 SharePoint Online 및 Exchange Online 설정 

[Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있습니다. 조건부 액세스를 사용하여 SharePoint 및 Exchange Online에 대한 액세스를 제어하려는 경우 다음을 수행해야 합니다.

- 조건부 액세스 시나리오가 지원되는지 여부를 검토합니다.
- 클라이언트 앱이 조건부 액세스 정책의 적용을 우회하지 못하도록 방지합니다.   

이 문서에서는 두 경우 모두를 해결할 수 있는 방법을 설명합니다.


## <a name="what-you-need-to-know"></a>알아야 하는 작업

Azure AD 조건부 액세스를 사용하여 인증 시도가 다음에서 오는 경우 클라우드 앱을 보호할 수 있습니다.

- 웹 브라우저

- [최신 인증](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)을 사용하는 클라이언트 앱

- Exchange ActiveSync 

일부 클라우드 앱도 레거시 인증 프로토콜을 지원합니다. 이는, 예를 들어 SharePoint Online 및 Exchange Online에 적용됩니다. 클라이언트 앱이 레거시 인증 프로토콜을 사용하여 클라우드 앱에 액세스할 수 있는 경우 Azure AD는 이 액세스 시도에서 조건부 액세스 정책을 적용할 수 없습니다. 클라이언트 앱이 정책의 적용을 우회하는 것을 방지하려면 영향을 받는 클라우드 앱에서 최신 인증만을 사용할 수 있는지 확인해야 합니다. 

클라이언트 앱 조건부 액세스가 적용되지 않는 예제는 다음과 같습니다.

- Office 2010 이하

- 최신 인증을 사용하지 않는 경우 Office 2013



 
## <a name="control-access-to-sharepoint-online"></a>SharePoint Online에 대한 액세스 제어

최신 인증 외에도 SharePoint Online은 레거시 인증 프로토콜도 지원합니다. 레거시 인증 프로토콜을 사용하도록 설정한 경우 SharePoint에 대한 조건부 액세스 정책은 최신 인증을 사용하지 않는 클라이언트에 적용되지 않습니다.

**[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)** cmdlet을 사용하여 SharePoint 액세스에 대한 레거시 인증 프로토콜 사용을 중지할 수 있습니다. 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Exchange Online에 대한 액세스 제어

Exchange Online에 대한 조건부 액세스 정책을 설정할 때 다음을 검토해야 합니다.

- Exchange ActiveSync

- 레거시 인증 프로토콜



### <a name="exchange-activesync"></a>Exchange ActiveSync

Exchange Active Sync는 최신 인증을 지원하지만 조건부 액세스 시나리오에 대한 지원과 관련된 몇 가지 제한 사항이 있습니다.

- 장치 플랫폼 조건만을 구성할 수 있습니다.  

    ![장치 플랫폼](./media/active-directory-conditional-access-no-modern-authentication/05.png)

- 다단계 인증 요구 사항 설정을 지원하지 않습니다.  

    ![조건부 액세스](./media/active-directory-conditional-access-no-modern-authentication/01.png)

Exchange ActiveSync에서 Exchange Online에 대한 액세스를 효과적으로 보호하기 위해 다음을 수행할 수 있습니다.

- 다음 단계를 수행하여 지원되는 조건부 액세스 정책을 구성합니다.

    a. 클라우드 앱으로 **Office 365 Exchange Online**을 선택합니다.  

    ![조건부 액세스](./media/active-directory-conditional-access-no-modern-authentication/04.png)

    나. **클라이언트 앱**으로 **Exchange Active Sync**를 선택한 다음 **지원되는 플랫폼에만 정책 적용**을 선택합니다.  

    ![장치 플랫폼](./media/active-directory-conditional-access-no-modern-authentication/03.png)

- AD FS(Active Directory Federation Services) 규칙을 사용하여 Exchange ActiveSync를 차단합니다.

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>레거시 인증 프로토콜

최신 인증 외에도 Exchange Online은 레거시 인증 프로토콜도 지원합니다. 레거시 인증 프로토콜을 사용하도록 설정한 경우 Exchange Online에 대한 조건부 액세스 정책은 최신 인증을 사용하지 않는 클라이언트에 적용되지 않습니다.

AD FS 규칙을 설정하여 Exchange Online에 대한 레거시 인증 프로토콜을 비활성화할 수 있습니다. 다음에서 액세스를 차단합니다.

- 최신 인증을 사용할 수 없는 Office 2013과 같은 이전 Office 클라이언트 

- 이전 버전의 Office


## <a name="set-up-ad-fs-rules"></a>AD FS 규칙 설정

다음 발급 권한 부여 규칙을 사용하여 AD FS 수준에서 트래픽을 활성화하거나 차단할 수 있습니다. 

### <a name="block-legacy-traffic-from-the-extranet"></a>엑스트라넷의 레거시 트래픽 차단

다음 세 가지 규칙을 적용하여: 

- 다음에 대한 액세스를 활성화합니다.
    - Exchange ActiveSync 트래픽
    - 브라우저 트래픽
    - 최신 인증 트래픽
- 다음에 대한 액세스를 차단합니다. 
    - 엑스트라넷의 레거시 클라이언트 앱

**규칙 1:**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**규칙 2:**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**규칙 3:**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>모든 곳의 레거시 트래픽 차단

다음 세 가지 규칙을 적용하여:

- 다음에 대한 액세스를 활성화합니다. 
    - Exchange ActiveSync 트래픽
    - 브라우저 트래픽
    - 최신 인증 트래픽
- 다음에 대한 액세스를 차단합니다. 
    - 모든 위치의 레거시 앱

##### <a name="rule-1"></a>규칙 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>규칙 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>규칙 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 참조하세요.

클레임 규칙 구성에 관한 지침은 [클레임 규칙 구성](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules)을 참조하세요. 






