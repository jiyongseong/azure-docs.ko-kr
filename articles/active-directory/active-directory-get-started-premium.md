---
title: Azure Active Directory Premium에 등록 | Microsoft Docs
description: Azure Active Directory Premium Edition에 등록하는 방법을 설명입니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro;
ms.openlocfilehash: 4048df1c5e536a4a33af39466e57b1b38c080c06
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-sign-up-for-azure-active-directory-premium"></a>빠른 시작: Azure Active Directory Premium에 등록
Azure AD(Azure Active Directory) Premium을 시작하기 위해 라이선스를 구입하고 Azure 구독에 연결할 수 있습니다. 새로운 Azure 구독을 만드는 경우 다음 절에 설명된 대로 라이선싱 계획 및 Azure AD 서비스 액세스도 활성화해야 합니다. 

## <a name="sign-up-for-active-directory-premium"></a>Active Directory Premium에 등록
Active Directory Premium에 등록하는 데는 몇 가지 옵션이 있습니다. 
* Azure 또는 Office 365 구독 사용
* Enterprise Mobility + Security 라이선싱 계획 사용
* Microsoft 볼륨 라이선싱 계획 사용

### <a name="azure-or-office-365"></a>Azure 또는 Office 365 
Azure 또는 Office 365 구독자는 Active Directory Premium을 온라인으로 구입할 수 있습니다. 

자세한 단계는 [Azure Active Directory Premium을 구입하는 방법 - 기존 고객](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) 또는 [Azure Active Directory Premium을 구입하는 방법 - 새로운 고객](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers)을 참조하세요.  

### <a name="enterprise-mobility--security"></a>Enterprise Mobility + Security
Enterprise Mobility + Security(EMS)는 조직이 Azure Active Directory Premium, Azure Information Protection 및 Microsoft Intune 서비스를 하나의 라이선싱 계획으로 함께 사용할 수 있는 비용 효율적인 방법입니다. [Enterprise Mobility + Security 웹 사이트](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)에서 EMS, 그리고 [Enterprise Mobility + Security 가격 책정 옵션](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing) 페이지에서 구매 가능한 EMS 라이선스 유형에 대해 자세히 알아볼 수 있습니다.  

다음 라이선싱 옵션 중 하나를 사용하여 EMS 라이선스를 통해 Azure AD로 시작할 수 있습니다.

- 무료 [Enterprise Mobility + Security E5 평가판 구독](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)으로 EMS 사용해 보기
- [Enterprise Mobility + Security E5 라이선스](https://signup.microsoft.com/Signup?OfferId=e6de2192-536a-4dc3-afdc-9e2602b6c790&ali=1) 구매
- [Enterprise Mobility + Security E3 라이선스](https://signup.microsoft.com/Signup?OfferId=4BBA281F-95E8-4136-8B0F-037D6062F54C&ali=1) 구매

### <a name="microsoft-volume-licensing"></a>Microsoft 볼륨 라이선스
Azure Active Directory Premium은 [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx)(250개 이상의 라이선스) 또는 [오픈 볼륨 라이선스](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx)(5–250개의 라이선스) 프로그램을 통해 사용할 수 있습니다.

[볼륨 라이선스를 통한 구매 방법](https://www.microsoft.com/licensing/how-to-buy/how-to-buy.aspx) 페이지에서 볼륨 라이선스 구매 옵션에 대해 자세히 알아볼 수 있습니다.

> [!NOTE]
> Azure Active Directory Premium 및 Basic 버전은 Azure Active Directory 전 세계 인스턴스를 사용하여 중국의 고객에게 제공됩니다. Azure Active Directory Premium 및 Basic 버전은 현재 중국 21Vianet이 운영하는 Microsoft Azure에서 지원되지 않습니다. 자세한 내용은 [Azure Active Directory 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/)을 통해 문의하세요.

이전 단계에서 사용한 것과 동일한 Azure 구독에 대해 이전에 Azure AD 라이선스를 구매하고 활성화했다면 라이선스는 동일한 디렉터리에 자동으로 활성화됩니다. 그렇지 않은 경우 이 문서의 나머지 부분에 설명된 단계를 계속합니다.

## <a name="activate-your-license-plan"></a>라이선스 계획 활성화
Microsoft에서 구매한 첫 번째 Azure AD 라이선스 계획입니까? 이 경우 구매가 완료되면 확인 전자 메일이 생성되어 전송됩니다. 첫 번째 라이선스 계획을 활성화하려면 이 전자 메일이 필요합니다.

**라이선스 계획을 활성화하려면 다음 단계 중 하나를 수행합니다.**

1. 활성화를 시작하려면 **로그인** 또는 **등록**을 클릭합니다.
   
    ![로그인][1]

    - 기존 테넌트가 있으면 **로그인**을 클릭하여 기존 관리자 계정으로 로그인합니다. 라이선스가 활성화되어야 하는 테넌트에 대한 전역 관리자 자격 증명으로 로그인합니다.

    - 라이선싱 계획을 사용하기 위해 새 Azure AD 테넌트를 만들려는 경우에는 **등록**을 클릭하고 **계정 프로필 만들기** 대화 상자를 엽니다.

        ![계정 프로필 만들기][2]

완료한 후에는 테넌트에 대한 라이선스 계획의 활성화에 대한 확인으로 다음과 같은 대화 상자가 표시됩니다.

![확인][3]

## <a name="activate-your-azure-active-directory-access"></a>Azure Active Directory 액세스 활성화
기존 구독에 새 Azure AD Premium 라이선스를 추가하는 경우 Azure AD 액세스가 이미 활성화되어 있어야 합니다. 그렇지 않으면 **환영 전자 메일**을 받은 후 Azure AD 액세스를 활성화해야 합니다.  

구매한 라이선스가 사용자의 디렉터리로 프로비전되면 **환영 전자 메일**이 사용자에게 전송됩니다. 전자 메일은 Azure Active Directory Premium 또는 Enterprise Mobility + Security 라이선스 및 기능에 대한 관리를 시작할 수 있다는 것을 확인해 줍니다. 

> [!TIP]
> 라이선스 프로비저닝 프로세스를 완료하면 자동으로 전송되는 시작 전자 메일을 사용하여 Azure AD 디렉터리 액세스를 활성화할 때까지 새 테넌트에 대한 Azure AD를 액세스할 수 없습니다. 

**Azure AD 액세스를 활성화하려면 다음 단계를 수행합니다.**

1. **환영 전자 메일**에서 **로그인**을 클릭합니다. 
   
    ![환영 전자 메일][4]
2. 성공적으로 로그인한 후에는 모바일 장치를 사용하여 두 번째 단계 인증도 완료해야 합니다.
   
    ![모바일 확인][5]

단 몇 분만 활성화되므로, 액세스하여 Azure AD를 관리할 수 있게 됩니다. 

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Azure AD Premium에 등록하여 Azure Active Directory 액세스를 활성화하는 방법을 알게 되었습니다. 

Azure 구독이 이미 있는 경우 다음 링크를 사용하여 평가판을 시작하거나 Azure Portal에서 Azure AD Premium 라이선스를 구매할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure AD Premium 라이선스 활성화](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/TryBuyProductBlade) 

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png