---
title: "Azure 하이브리드 ID 솔루션 선택 | Microsoft Docs"
description: "조직에 가장 효율적인 ID 거버넌스 결정을 내리기 위해 사용 가능한 하이브리드 ID 솔루션 및 권장 사항을 기본적으로 이해합니다."
keywords: 
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: billmath
ms.date: 03/02/2018
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 9f9099c0ebd65ba84e171314e6f04d858648a805
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="microsoft-hybrid-identity-solutions"></a>Microsoft 하이브리드 ID 솔루션
[Microsoft Azure AD(Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 하이브리드 ID 솔루션을 사용하면 온-프레미스의 사용자를 계속 관리하면서 온-프레미스 디렉터리 개체를 Azure AD와 동기화할 수 있습니다. 온-프레미스 Windows Server Active Directory와 Azure AD와의 동기화를 계획할 때는 맨 먼저 동기화된 ID를 사용할지 또는 페더레이션 ID를 사용할지를 결정해야 합니다. 동기화된 ID 및 선택적으로 암호 해시를 사용하여 사용자는 같은 암호로 온-프레미스 및 클라우드 기반 조직 리소스에 모두 액세스할 수 있습니다. SSO(Single Sign-On) 또는 온-프레미스 MFA와 같은 고급 시나리오 요구 사항의 경우 AD FS(Active Directory Federation Services)를 배포하여 ID를 페더레이션해야 합니다. 

하이브리드 ID를 구성하는 데 사용할 수 있는 옵션은 몇 가지가 있습니다. 이 문서에서는 배포 용이성과 특정 ID 및 액세스 관리 요구 사항에 따라 조직에 가장 적합한 옵션을 선택하는 데 도움이 되는 정보를 제공합니다. 조직의 요구 사항에 가장 잘 맞는 ID 모델을 고려할 때는 시간, 기존 인프라, 복잡성 및 비용에 대해서도 생각해야 합니다. 이러한 요소는 조직마다 다르며 시간이 흐름에 따라 변경될 수 있습니다. 그러나 요구 사항이 변경되면 다른 ID 모델로 전환할 유연성도 있습니다.

> [!TIP]
> 이러한 솔루션은 모두 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)에서 제공됩니다.

## <a name="synchronized-identity"></a>동기화된 ID 
동기화된 ID는 온-프레미스 디렉터리 개체(사용자 및 그룹)를 Azure AD와 통합하는 가장 간단한 방법입니다. 

![동기화된 하이브리드 ID](./media/choose-hybrid-identity-solution/synchronized-identity.png)

동기화된 ID는 가장 쉽고 빠른 방법이지만, 사용자가 여전히 클라우드 기반 리소스에 대한 별도의 암호를 유지 관리해야 합니다. 이를 방지하려면 선택적으로 Azure AD 디렉터리에 대한 [사용자 암호 해시를 동기화](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization)할 수도 있습니다. 암호 해시를 동기화하면 사용자가 온-프레미스에서 사용하는 것과 같은 사용자 이름 및 암호로 클라우드 기반 조직 리소스에 로그인할 수 있습니다. Azure AD Connect는 온-프레미스 디렉터리에 변경 내용이 있는지 주기적으로 확인하고 Azure AD 디렉터리를 동기화된 상태로 유지합니다. 온-프레미스 Active Directory에서 사용자 특성 또는 암호가 변경되면 Azure AD에서 자동으로 업데이트됩니다. 

Office 365, SaaS 응용 프로그램 및 기타 Azure AD 기반 리소스에 사용자가 로그인할 수 있도록 하기만 하면 되는 대부분 조직의 경우 기본 암호 동기화 옵션을 사용하는 것이 좋습니다. 이 옵션이 적합하지 않은 경우 통과 인증과 AD FS 중에 결정해야 합니다.

> [!TIP]
> 사용자 암호는 실제 사용자 암호를 나타내는 해시 값의 형태로 온-프레미스 Windows Server Active Directory에 저장됩니다. 해시 값은 단방향 수학 함수(해시 알고리즘)의 결과입니다. 암호의 일반 텍스트 버전에 대한 함수의 결과를 되돌릴 수 있는 방법이 없습니다. 암호 해시를 사용하여 온-프레미스 네트워크에 로그인할 수 없습니다. 암호를 동기화하도록 선택하면 Azure AD Connect가 온-프레미스 Active Directory에서 암호 해시를 추출하고 암호 해시에 추가 보안 처리를 적용한 후 암호 해시가 Azure AD로 동기화됩니다. 암호 동기화를 암호 쓰기 저장과 함께 사용하여 Azure AD에서 셀프 서비스 암호 재설정을 사용하도록 설정할 수 있습니다. 또한 회사 네트워크에 연결된 도메인에 연결된 컴퓨터의 사용자에 대해 SSO(Single Sign-On)를 사용하도록 설정할 수 있습니다. Single Sign-On으로 활성화된 사용자는 클라우드 리소스에 안전하게 액세스하기 위해 사용자 이름을 입력하기만 하면 됩니다. 

## <a name="pass-through-authentication"></a>통과 인증
[Azure AD 통과 인증](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)은 온-프레미스 Active Directory를 사용하여 Azure AD 기반 서비스에 대한 간단한 암호 유효성 검사 솔루션을 제공합니다. 조직의 보안 및 규정 준수 정책에서 사용자의 암호(해시된 형태로도) 전송을 허용하지 않고 도메인에 연결된 장치에 대해 데스크톱 SSO를 지원하기만 하면 되는 경우 통과 인증 사용을 평가하는 것이 좋습니다. 통과 인증은 DMZ에서 어떠한 배포도 요구하지 않으므로, AD FS와 비교할 경우 배포 인프라가 간소화됩니다. 사용자가 Azure AD를 사용하여 로그인할 때 이 인증 방법은 온-프레미스 Active Directory에 대해 직접 사용자 암호의 유효성을 검사합니다.

![통과 인증](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

통과 인증을 사용하면 복잡한 네트워크 인프라가 필요하지 않으며 클라우드에 온-프레미스 암호를 저장할 필요가 없습니다. 통과 인증이 Single Sign-On과 결합되면 Azure AD 또는 다른 클라우드 서비스에 로그인할 때 완전히 통합된 환경이 제공됩니다.

통과 인증은 암호 유효성 검사 요청을 수신 대기하는 간단한 온-프레미스 에이전트를 사용하는 Azure AD Connect를 통해 구성됩니다. 에이전트는 고가용성 및 부하 분산을 제공하도록 여러 컴퓨터에 손쉽게 배포될 수 있습니다. 모든 통신이 아웃바운드이므로 커넥터를 DMZ에 설치하기 위한 요구 사항이 없습니다. 커넥터에 대한 서버 컴퓨터 요구 사항은 다음과 같습니다.

- Windows Server 2012 R2 이상
- 사용자의 유효성이 검사되는 포리스트의 도메인에 연결

## <a name="federated-identity-ad-fs"></a>페더레이션 ID(AD FS)
사용자가 Office 365 및 다른 클라우드 서비스에 액세스하는 방식을 더 세밀하게 제어하려면 [AD FS(Active Directory Federation Services)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016)를 사용하여 SSO(Single Sign-On)와의 디렉터리 동기화를 설정하면 됩니다. 사용자의 로그인을 AD FS와 페더레이션하면 사용자 자격 증명의 유효성을 검사하는 인증이 온-프레미스 서버에 위임됩니다. 이 모델에서는 온-프레미스 Active Directory 자격 증명이 Azure AD에 전달되지 않습니다.

![페더레이션 ID](./media/choose-hybrid-identity-solution/federated-identity.png)

ID 페더레이션이라고도 하는 이 로그인 방법은 모든 사용자 인증이 온-프레미스에서 제어되도록 하며 관리자가 더 엄격한 수준의 액세스 제어를 구현할 수 있게 해줍니다. AD FS와의 ID 페더레이션은 가장 복잡한 옵션이며 온-프레미스 환경에 추가 서버를 배포해야 합니다. ID 페더레이션은 Active Directory 및 AD FS 인프라에 대한 연중무휴 지원 제공도 약속합니다. 온-프레미스 인터넷 액세스, 도메인 컨트롤러 또는 AD FS 서버를 사용할 수 없는 경우 사용자가 클라우드 서비스에 로그인할 수 없으므로 이러한 높은 수준의 지원이 필요합니다.

> [!TIP]
> AD FS(Active Directory Federation Services)와 페더레이션을 사용하도록 선택하는 경우 필요에 따라 AD FS 인프라에 장애가 발생할 경우 백업으로 암호 동기화를 설정할 수 있습니다.


## <a name="common-scenarios-and-recommendations"></a>일반적인 시나리오 및 권장 사항
다음은 몇 가지 일반적인 하이브리드 ID 및 액세스 관리 시나리오와 각 시나리오에 적합한 하이브리드 ID 옵션(하나 또는 여러 개)에 대한 권장 사항입니다.

|수행 작업:|PWS 및 SSO<sup>1</sup>| PTA 및 SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|온-프레미스 Active Directory에서 만든 새 사용자, 연락처 및 그룹 계정을 클라우드에 자동으로 동기화|![권장](./media/choose-hybrid-identity-solution/ic195031.png)| ![권장](./media/choose-hybrid-identity-solution/ic195031.png) |![권장](./media/choose-hybrid-identity-solution/ic195031.png)|
|Office 365 하이브리드 시나리오에 대한 테넌트 설정|![권장](./media/choose-hybrid-identity-solution/ic195031.png)| ![권장](./media/choose-hybrid-identity-solution/ic195031.png) |![권장](./media/choose-hybrid-identity-solution/ic195031.png)|
|사용자가 온-프레미스 암호를 사용하여 로그인하고 Cloud Services에 액세스할 수 있도록 함|![권장](./media/choose-hybrid-identity-solution/ic195031.png)| ![권장](./media/choose-hybrid-identity-solution/ic195031.png) |![권장](./media/choose-hybrid-identity-solution/ic195031.png)|
|회사 자격 증명을 사용하여 Single Sign-On 구현|![권장](./media/choose-hybrid-identity-solution/ic195031.png)| ![권장](./media/choose-hybrid-identity-solution/ic195031.png) |![권장](./media/choose-hybrid-identity-solution/ic195031.png)|
|클라우드에 암호 해시가 저장되지 않도록 함| |![권장](./media/choose-hybrid-identity-solution/ic195031.png)|![권장](./media/choose-hybrid-identity-solution/ic195031.png)|
|온-프레미스 다단계 인증 솔루션 사용| | |![권장](./media/choose-hybrid-identity-solution/ic195031.png)|
|사용자에 대한 스마트 카드 인증 지원<sup>4</sup>| | |![권장](./media/choose-hybrid-identity-solution/ic195031.png)|
|Office 포털 및 Windows 10 Desktop에 암호 만료 알림 표시| | |![권장](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> Single Sign-On을 사용하여 암호 동기화 

> <sup>2</sup> 통과 인증 및 Single Sign-On 

> <sup>3</sup> AD FS를 통해 페더레이션된 Single Sign-On

> <sup>4</sup> AD FS는 엔터프라이즈 PKI와 통합되어 인증서를 사용한 로그인을 허용할 수 있습니다. 이러한 인증서는 MDM 또는 GPO와 같은 신뢰할 수 있는 프로비전 채널이나 스마트 카드 인증서(PIV/CAC 카드 포함) 또는 비즈니스용 Hello(cert-trust)를 통해 배포되는 소프트 인증서일 수 있습니다. 스마트 카드 인증 지원에 대한 자세한 내용은 [이 블로그](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)를 참조하세요.


## <a name="next-steps"></a>다음 단계
[개념 환경의 Azure 증명에 대한 자세한 정보](https://aka.ms/aad-poc)

[Azure AD Connect 설치](http://go.microsoft.com/fwlink/?LinkId=615771)

[하이브리드 ID 동기화 모니터링](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

