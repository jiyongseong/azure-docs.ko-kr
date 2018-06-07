---
title: Azure AD Domain Services에서 보안 LDAP(LDAPS) 구성 | Microsoft Docs
description: Azure AD 도메인 서비스 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: maheshu
ms.openlocfilehash: 8da03990ace37b527553b0fe3ff0032515e1b812
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성

## <a name="before-you-begin"></a>시작하기 전에
[작업 2 - 보안 LDAP 인증서를 .PFX 파일로 내보내기](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)를 완료했는지 확인합니다.


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>작업 3 - Azure Portal을 사용하여 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정
보안 LDAP를 사용하도록 설정하려면 다음 구성 단계를 수행합니다.

1. **[Azure Portal](https://portal.azure.com)** 로 이동합니다.

2. **리소스 검색** 검색 상자에서 'domain services'를 검색합니다. 검색 결과에서 **Azure AD Domain Services**를 선택합니다. **Azure AD Domain Services** 페이지에 관리되는 도메인이 나열됩니다.

    ![프로비전되는 관리되는 도메인 찾기](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. 도메인에 대한 자세한 내용을 보려면 관리되는 도메인의 이름(예: 'contoso100.com')을 클릭합니다.

    ![Domain Services - 프로비저닝 상태](./media/getting-started/domain-services-provisioning-state.png)

3. 탐색 창에서 **보안 LDAP**를 클릭합니다.

    ![Domain Services - 보안 LDAP 페이지](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. 기본적으로 관리되는 도메인에 대한 보안 LDAP 액세스는 사용하지 않도록 설정되어 있습니다. **보안 LDAP**를 **사용**으로 토글합니다.

    ![보안 LDAP를 사용하도록 설정](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. 기본적으로 인터넷에서 관리되는 도메인에 대한 보안 LDAP 액세스는 사용하지 않도록 설정되어 있습니다. 필요한 경우 **Allow secure LDAP access over the internet**(인터넷을 통한 보안 LDAP 액세스 허용)을 **사용**으로 토글합니다. 

    > [!WARNING]
    > 인터넷을 통해 보안 LDAP 액세스를 사용하도록 설정하면 도메인이 인터넷을 통한 무차별 암호 대입 공격(brute force attack)에 취약해질 수 있습니다. 따라서 필요한 원본 IP 주소 범위에 대한 액세스를 잠그도록 NSG를 설정하는 것이 좋습니다. [인터넷을 통해 관리되는 도메인에 대한 LDAPS 액세스 잠금](#task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet)에 대한 지침을 참조하세요.
    >

6. **보안 LDAP 인증서가 포함된 .PFX 파일** 뒤의 폴더 아이콘을 클릭합니다. 관리되는 도메인에 대한 보안 LDAP 액세스를 위해 인증서를 사용하여 PFX 파일의 경로를 지정합니다.

7. **.PFX 파일의 암호를 해독하도록 암호를** 지정합니다. 인증서를 PFX 파일로 내보낼 때 사용한 것과 동일한 암호를 입력합니다.

8. 완료되면 **저장** 단추를 클릭합니다.

9. 관리되는 도메인에 대해 보안 LDAP가 구성되었음을 알려 주는 알림이 표시됩니다. 이 작업이 완료될 때까지 도메인에 대한 다른 설정을 수정할 수 없습니다.

    ![관리되는 도메인에 대해 보안 LDAP 구성](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정하는 데 약 10-15분이 소요됩니다. 제공된 보안 LDAP 인증서가 필수 조건을 만족하지 않으면 보안 LDAP는 디렉터리에 사용할 수 없으며 오류가 표시됩니다. 도메인 이름이 올바르지 않거나 인증서가 만료되었거나 곧 만료된다는 오류 등이 여기에 해당합니다. 이 경우 유효한 인증서로 다시 시도해 보세요.
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>작업 4 - 인터넷에서 관리되는 도메인에 액세스할 DNS 구성
> [!NOTE]
> **선택적 태스크** - 인터넷을 통해 LDAPS를 사용하여 관리되는 도메인에 액세스하지 않으려면 이 구성 태스크를 건너뜁니다.
>
>

이 태스크를 시작하기 전에 [태스크 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview)에 나와 있는 단계를 완료해야 합니다.

관리되는 도메인에 대한 인터넷을 통한 보안 LDAP 액세스를 사용하도록 설정했으면 클라이언트 컴퓨터가 관리되는 도메인을 찾을 수 있도록 DNS를 업데이트 야 합니다. 작업 3이 완료되면 외부 IP 주소가 **LDAPS 액세스를 위한 외부 IP 주소**의 **속성** 탭에 표시됩니다.

관리되는 도메인의 DNS 이름(예: 'ldaps.contoso100.com')이 이 외부 IP 주소를 가리키도록 외부 DNS 공급자를 구성합니다. 예를 들어 다음과 같은 DNS 항목을 만듭니다.

    ldaps.contoso100.com  -> 52.165.38.113

이것으로 끝입니다. 이제 인터넷을 통해 보안 LDAP를 사용하여 관리되는 도메인에 연결할 준비가 되었습니다.

> [!WARNING]
> 클라이언트 컴퓨터가 LDAPS를 사용하여 관리되는 도메인에 성공적으로 연결하려면 LDAPS 인증서의 발급자를 신뢰해야 한다는 점에 유의하세요. 공개적으로 신뢰할 수 있는 인증 기관을 사용하는 경우 클라이언트 컴퓨터가 이러한 인증서 발급자를 신뢰하므로 문제가 되지 않습니다. 자체 서명된 인증서를 사용하는 경우 자체 서명된 인증서의 공개 부분을 클라이언트 컴퓨터의 신뢰할 수 있는 인증서 저장소에 설치합니다.
>
>


## <a name="task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>작업 5 - 인터넷을 통해 관리되는 도메인에 대한 보안 LDAPS 액세스 잠금
> [!NOTE]
> 인터넷을 통해 관리되는 도메인에 대한 LDAPS 액세스를 사용하지 않도록 설정한 경우 이 구성 작업을 건너뜁니다.
>
>

이 태스크를 시작하기 전에 [태스크 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview)에 나와 있는 단계를 완료해야 합니다.

인터넷을 통해 LDAPS 액세스가 가능하도록 관리되는 도메인을 노출하면 보안 위협이 됩니다. 관리되는 도메인은 보안 LDAP에 사용되는 포트(즉, 포트 636)에서 인터넷을 통해 접속할 수 있습니다. 따라서 관리되는 도메인에 대한 액세스를 알려진 특정 IP 주소로 제한할 수 있습니다. 보안이 향상되도록 NSG(네트워크 보안 그룹)를 만들고 Azure AD Domain Services를 사용하도록 설정된 서브넷과 연결합니다.

다음 표에서는 구성 가능한 예제 NSG를 통해 인터넷을 통한 보안 LDAP 액세스를 잠그는 방법을 보여 줍니다. 이 NSG에는 지정된 IP 주소 집합에서만 TCP 포트 636을 통해 인바운드 보안 LDAP 액세스를 허용하는 규칙 집합이 포함되어 있습니다. 기본 'DenyAll' 규칙은 인터넷의 다른 모든 인바운드 트래픽에 적용됩니다. 지정된 IP 주소에서 인터넷을 통해 LDAPS 액세스를 허용하는 NSG 규칙은 DenyAll NSG 규칙보다 우선 순위가 높습니다.

![인터넷을 통해 LDAPS 액세스를 보안하는 예제 NSG](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**자세한 정보** - [네트워크 보안 그룹](../virtual-network/security-overview.md).

<br>


## <a name="troubleshooting"></a>문제 해결
보안 LDAP를 사용하여 관리되는 도메인에 연결하는 데 문제가 있으면 다음 문제 해결 단계를 수행하세요.
* 보안 LDAP 인증서의 발급자 체인을 클라이언트에서 신뢰할 수 있는지 확인합니다. 트러스트를 설정하려면 클라이언트의 신뢰할 수 있는 루트 인증서 저장소에 루트 인증 기관을 추가할 수 있습니다.
* LDAP 클라이언트(예: ldp.exe)가 IP 주소가 아닌 DNS 이름을 사용하여 보안 LDAP 끝점에 연결되는지 확인합니다.
* LDAP 클라이언트가 연결할 DNS 이름이 관리되는 도메인의 보안 LDAP에 대한 공용 IP 주소로 확인되는지 확인합니다.
* 관리되는 도메인의 보안 LDAP 인증서의 주체 또는 주체 대체 이름 특성에 DNS 이름이 있는지 확인합니다.
* 인터넷을 통해 보안 LDAP에 연결할 경우 가상 네트워크에 대한 NSG 설정에서 인터넷으로부터 포트 636으로 가는 트래픽을 허용하는지 확인합니다.

보안 LDAP를 사용하여 관리되는 도메인에 연결하는 데 계속 문제가 발생하면 [제품 팀에 도움을 요청](active-directory-ds-contact-us.md)하세요. 문제를 진단하는 데 도움이 되도록 다음 정보를 포함합니다.
* 연결을 만들고 실패하는 ldp.exe의 스크린샷
* Azure AD 테넌트 ID 및 관리되는 도메인의 DNS 도메인 이름
* 바인딩하려는 정확한 사용자 이름


## <a name="related-content"></a>관련 콘텐츠
* [Azure AD Domain Services - 시작 가이드](active-directory-ds-getting-started.md)
* [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)
* [Azure AD Domain Services 관리되는 도메인에서 그룹 정책 관리](active-directory-ds-admin-guide-administer-group-policy.md)
* [네트워크 보안 그룹](../virtual-network/security-overview.md)
* [네트워크 보안 그룹 만들기](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
