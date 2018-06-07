---
title: "Office 365 신뢰 당사자 트러스트에 대한 서명 해시 알고리즘 변경 | Microsoft Docs"
description: "이 페이지에서는 Office 365와의 페더레이션 트러스트에 대한 SHA 알고리즘을 변경하는 방법에 대한 지침을 제공합니다."
keywords: "SHA1,SHA256,O365,페더레이션,aadconnect,adfs,ad fs,sha 변경,페더레이션 트러스트,신뢰 당사자 트러스트"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: mtillman
editor: 
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
ms.openlocfilehash: ec7eee36888d825d65335db590731103aabbf5c2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Office 365 신뢰 당사자 트러스트에 대한 서명 해시 알고리즘 변경
## <a name="overview"></a>개요
AD FS(Active Directory Federation Services)는 변조될 수 없음을 확인하도록 해당 토큰을 Microsoft Azure Active Directory에 서명합니다. 이 서명은 SHA1 또는 SHA256을 기반으로 할 수 있습니다. 이제 Azure Active Directory에서는 SHA256 알고리즘으로 서명된 토큰을 지원하며, 최고 수준의 보안을 위해 토큰 서명 알고리즘을 SHA256으로 설정하도록 권장합니다. 이 문서에서는 토큰 서명 알고리즘을 보다 안전한 SHA256 수준으로 설정하는 데 필요한 단계를 설명합니다.

>[!NOTE]
>Microsoft에서는 SHA1보다 더 안전하지만 SHA1이 여전히 지원되는 옵션이므로 토큰을 서명하는 알고리즘으로 SHA256을 사용하는 것이 좋습니다.

## <a name="change-the-token-signing-algorithm"></a>토큰 서명 알고리즘 변경
아래의 두 절차 중 하나를 사용하여 서명 알고리즘을 설정한 후 AD FS는 SHA256으로 Office 365 신뢰 당사자 트러스트에 대한 토큰을 서명합니다. 추가 구성을 변경할 필요가 없으며 이 변경은 Office 365 또는 다른 Azure AD 응용 프로그램에 액세스하는 기능에 영향을 주지 않습니다.

### <a name="ad-fs-management-console"></a>AD FS 관리 콘솔
1. 기본 AD FS 서버에서 AD FS 관리 콘솔을 엽니다.
2. AD FS 노드를 확장하고 **신뢰 당사자 트러스트**를 클릭합니다.
3. Office 365/Azure 신뢰 당사자 트러스트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
4. **고급** 탭을 선택하고 보안 해시 알고리즘 SHA256을 선택합니다.
5. **확인**을 클릭합니다.

![SHA256 서명 알고리즘--MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell cmdlet
1. AD FS 서버에서 관리자 권한으로 PowerShell을 엽니다.
2. **Set-AdfsRelyingPartyTrust** cmdlet을 사용하여 보안 해시 알고리즘을 설정합니다.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>참조 항목
* [Azure AD Connect를 사용하여 Office 365 트러스트 복구](connect/active-directory-aadconnect-federation-management.md#repairthetrust)

