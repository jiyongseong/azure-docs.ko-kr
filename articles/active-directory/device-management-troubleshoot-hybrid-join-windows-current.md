---
title: "Windows 10 및 Windows Server 2016 장치에 조인된 하이브리드 Azure Active Directory 문제 해결 | Microsoft Docs"
description: "Windows 10 및 Windows Server 2016 장치에 조인된 하이브리드 Azure Active Directory 문제 해결"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3b98d31efcdbd61cf12e2c905f200c1e54f68f69
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Windows 10 및 Windows Server 2016 장치에 조인된 하이브리드 Azure Active Directory 문제 해결 

이 항목은 다음 클라이언트에 적용됩니다.

-   Windows 10
-   Windows Server 2016

다른 Windows 클라이언트의 경우 [하위 수준 장치에 조인된 하이브리드 Azure Active Directory 문제 해결](device-management-troubleshoot-hybrid-join-windows-legacy.md)을 참조하세요.

이 항목에서는 다음 시나리오를 지원하도록 [장치에 조인된 하이브리드 Azure Active Directory를 구성](device-management-hybrid-azuread-joined-devices-setup.md)했다고 가정합니다.

- 장치 기반 조건부 액세스

- [엔터프라이즈 설정 로밍](active-directory-windows-enterprise-state-roaming-overview.md)

- [비즈니스용 Windows Hello](active-directory-azureadjoin-passport-deployment.md)


이 문서에서는 잠재적인 문제를 해결하는 방법에 대한 문제 해결 지침을 제공합니다. 


Windows 10 및 Windows Server 2016의 경우 하이브리드 Azure Active Directory 조인은 Windows 10 2015년 11월 업데이트 이상을 지원합니다. 1주년 업데이트를 사용하는 것이 좋습니다.

## <a name="step-1-retrieve-the-join-status"></a>1단계: 조인 상태 검색 

**조인 상태를 검색하려면:**

1. 관리자 권한으로 명령 프롬프트를 엽니다.

2. **dsregcmd /status**를 입력합니다.



    +----------------------------------------------------------------------+
    | Device State                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: NO DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: Microsoft Platform Crypto Provider TpmProtected: YES KeySignTest: : MUST Run elevated to test.
                  Idp: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn:ms-drs:enterpriseregistration.windows.net DomainJoined: YES DomainName: CONTOSO
    
    +----------------------------------------------------------------------+
    | User State                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organizations         WamDefaultId: https://login.microsoft.com       WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt: YES



## <a name="step-2-evaluate-the-join-status"></a>2단계: 조인 상태 평가 

다음 필드를 검토하고 예상 값을 갖는지 확인합니다.

### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

이 필드는 장치가 Azure AD에 조인되어 있는지 여부를 나타냅니다. 값이 **아니요**인 경우 Azure AD에 대한 조인은 아직 완료되지 않았습니다. 

**가능한 원인:**

- 조인을 위한 컴퓨터 인증이 실패했습니다.

- 조직에 컴퓨터에서 검색될 수 없는 HTTP 프록시가 있습니다.

- 컴퓨터가 인증을 위해 Azure AD에, 또는 등록을 위해 Azure DRS에 연결할 수 없습니다.

- 컴퓨터가 조직의 내부 네트워크 또는 온-프레미스 AD 도메인 컨트롤러의 직접 가시선 내에 있는 VPN에 있지 않습니다.

- 컴퓨터에 TPM이 있는 경우 잘못된 상태일 수도 있습니다.

- 이전에 문서에 설명된 것처럼 다시 확인해야 하는 잘못된 서비스 구성이 있을 수 있습니다. 일반적인 예제는 다음과 같습니다.

    - 페더레이션 서버에서 Ws-Trust 끝점이 사용되도록 설정되어 있지 않습니다.

    - 페더레이션 서버는 Windows 통합 인증을 사용한 네트워크 컴퓨터에서의 인바운드 인증을 허용하지 않습니다.

    - 컴퓨터가 속한 AD 포리스트의 Azure AD에서 확인된 도메인 이름을 가리키는 서비스 연결 지점 개체가 없습니다.

---

### <a name="domainjoined--yes"></a>DomainJoined : YES  

이 필드는 장치가 온-프레미스 Active Directory에 조인되는지 여부를 나타냅니다. 값이 **아니요**인 경우 장치는 하이브리드 Azure AD 조인을 수행할 수 없습니다.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

이 필드는 장치가 Azure AD에 개인 장치로 등록되어 있는지 여부를 나타냅니다(*작업 영역 조인*으로 표시). 이 값은 하이브리드 Azure AD 조인된 도메인에 가입된 컴퓨터에 대해 **아니요**이어야 합니다. 값이 **예**인 경우 하이브리드 Azure AD 조인을 완료하기 전에 회사 또는 학교 계정이 추가되었습니다. 이 경우 Windows 10 버전의 1주년 업데이트(1607)를 사용하는 경우 계정은 무시됩니다.

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : YES 및 AzureADPrt : YES
  
이러한 필드는 사용자가 장치에 로그인 시 Azure AD에서 성공적으로 인증되었는지 여부를 나타냅니다. 값이 **아니요**인 경우 다음의 원인 때문일 수 있습니다.

- 등록 시 장치에 연결된 TPM에 잘못된 저장소 키(STK)가 있습니다(상승된 권한으로 실행하면서 KeySignTest 확인).

- 대체 로그인 ID

- HTTP 프록시를 찾을 수 없음

## <a name="next-steps"></a>다음 단계

질문은 [장치 관리 FAQ](device-management-faq.md)를 참조하세요. 