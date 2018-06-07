---
title: Azure Active Directory 테넌트에서 로그인 페이지 사용자 지정 | Microsoft Docs
description: 회사 브랜딩을 Azure 로그인 페이지 에 추가하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/19/2018
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: ac8562b77ba43dbb07e886dfa235586198027796
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764447"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>빠른 시작: Azure AD에서 로그인 페이지에 회사 브랜딩 추가
혼동을 피하기 위해 대부분의 회사는 관리하는 모든 웹 사이트 및 서비스에 일관된 모양과 느낌을 적용하고자 합니다. Azure AD(Active Directory)는 회사 로고 및 사용자 지정 색 구성표를 포함하도록 로그인 페이지의 외관을 사용자 지정하는 방식으로 이 기능을 제공합니다. 로그인 페이지는 Azure AD를 ID 공급자로 사용하는 Office 365와 같은 웹 기반 응용 프로그램에 로그인할 경우에 표시됩니다. 자격 증명을 입력하려면 이 페이지와 상호 작용합니다.

> [!NOTE]
> * 회사 브랜딩은 Azure AD의 Premium 또는 Basic 라이선스를 구매했거나 Office 365 라이선스가 있는 경우에만 사용할 수 있습니다. 기능이 라이선스 형식별로 지원되는지 알아보려면 [Azure Active Directory 가격 책정 정보 페이지](https://azure.microsoft.com/pricing/details/active-directory/)를 확인하세요.
> 
> * Azure AD Premium 및 Basic 버전은 Azure Active Directory의 전 세계 인스턴스를 사용하여 중국의 고객에게 제공됩니다. Azure AD Premium 및 Basic 버전은 현재 중국 21Vianet이 운영하는 Azure 서비스에서 지원되지 않습니다. 자세한 내용은 [Azure Active Directory 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/)에 문의하세요.

## <a name="customizing-the-sign-in-page"></a>로그인 페이지 사용자 지정

<!--You can customize the following elements on the sign-in page: <attach image>-->

사용자가 [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com)과 같은 테넌트 관련 URL에 액세스할 때 회사 브랜딩 사용자 지정이 Azure AD 로그인 페이지에 표시됩니다.

예를 들어 사용자가 www.office.com를 방문하는 경우 사용자가 아직 자격 증명을 입력하지 않았기 때문에 로그인 페이지에 회사 브랜딩 사용자 지정이 표시되지 않습니다. 사용자가 자신의 사용자 ID를 입력하거나 사용자 타일을 선택하면 회사 브랜딩이 표시됩니다.

> [!NOTE]
> * 도메인 이름은 브랜딩을 구성한 Azure Portal의 **도메인** 부분에서 "활성"으로 표시되어야 합니다. 자세한 내용은 [사용자 지정 도메인 이름 추가](add-custom-domain.md)를 참조하세요.
> * 로그인 페이지 브랜딩은 개인 Microsoft 계정의 로그인 페이지에 적용되지 않습니다. 직원 또는 비즈니스 게스트가 개인 Microsoft 계정으로 로그인하는 경우 조직의 브랜딩이 로그인 페이지에 반영되지 않습니다.


### <a name="banner-logo"></a>배너 로고 

설명 | 제약 조건 | 권장 사항
------- | ------- | ----------
배너 로고는 로그인 및 액세스 패널 페이지에 표시됩니다.<br>로그인 페이지에서 로고는 사용자 이름을 입력한 후 표시됩니다. | 투명 JPG 또는 PNG<br>최대 높이: 36px<br>최대 너비: 245px | 여기에서 조직의 로고를 사용합니다.<br>투명 이미지를 사용합니다. 배경이 흰색이 된다고 가정하지 마십시오.<br>이미지에서 로고 주위에 안쪽 여백을 추가하지 마십시오. 그렇지 않으면 로고가 불균형적으로 작게 보입니다.

### <a name="username-hint"></a>사용자 이름 힌트   
설명 | 제약 조건 | 권장 사항
------- | ------- | ----------
이 옵션은 사용자 이름 필드의 힌트 텍스트를 지정합니다. | 유니코드 텍스트 최대 64자<br>일반 텍스트 전용 | 조직 외부의 게스트 사용자가 앱에 로그인하도록 하려면 이 옵션은 설정하지 않는 것이 좋습니다.
            
### <a name="sign-in-page-text"></a>로그인 페이지 텍스트   
설명 | 제약 조건 | 권장 사항
------- | ------- | ----------
이 옵션은 로그인 양식의 맨 아래에 표시되고 지원 센터에 대한 전화 번호 또는 법적 설명과 같은 추가 정보를 통신하는 데 사용할 수 있습니다. | 유니코드 텍스트 최대 256자<br>일반 텍스트 전용(링크 또는 HTML 태그 없음)    

### <a name="sign-in-page-image"></a>로그인 페이지 이미지  
설명 | 제약 조건 | 권장 사항
------- | ------- | ----------
이 옵션은 로그인 페이지의 배경에 표시되며 볼 수 있는 공간의 가운데에 고정되고 브라우저 창에 맞도록 크기 조정하고 자릅니다.    <br>전화와 같은 좁은 화면에서는 이 이미지가 표시되지 않습니다.<br>0.55 불투명도의 검은색 마스크는 페이지가 로드될 때 이 이미지 위에 적용됩니다. | JPG 또는 PNG<br>이미지 크기: 1920x1080px<br>파일 크기: &lt; 300KB | <br>강력한 제목 포커스가 없는 곳에 이미지를 사용합니다. 불투명 로그인 양식이 이 이미지의 가운데 위로 표시되고 브라우저 창 크기에 따라 이미지의 모든 부분을 덮을 수 있습니다.<br>빠른 로드 시간을 보장하려면 파일 크기를 작게 유지합니다. 

### <a name="sign-in-page-background-color"></a>로그인 페이지 배경색
설명 | 제약 조건 | 권장 사항
------- | ------- | ----------
낮은 대역폭 연결에서는 이 색이 배경 이미지 대신 사용됩니다. | 16진수(예: #FFFFFF)의 RGB 색 | 배너 로고 또는 조직 색의 기본 색을 사용하는 것이 좋습니다.

### <a name="square-logo-image"></a>사각형 로고 이미지
설명 | 제약 조건 | 권장 사항
------- | ------- | ----------
이 이미지는 새 Enterprise Windows 10 PC에 설치하는 동안 나타납니다. 새 작업 PC를 설정할 때 직원에게 컨텍스트를 제공합니다. 이미지는 다른 Windows 10 환경의 암호 항목 페이지에서 자신의 작업 장치를 배포하기 위해 [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97)을 사용하는 테넌트에 대해 표시됩니다. | 투명 PNG(기본 설정) 또는 JPG<br>이미지 크기: 240 x 240 픽셀<br>파일 크기: &lt; 10KB | 여기에서 조직의 로고를 사용합니다.<br> 투명 이미지를 사용합니다.<br>배경이 흰색이 된다고 가정하지 마십시오.<br>이미지의 로고에 안쪽 여백을 추가하지 마십시오. 그렇지 않으면 로고가 불균형적으로 작게 보입니다.

### <a name="show-option-to-remain-signed-in"></a>로그인 상태를 유지하는 옵션 표시
설명 | 제약 조건 | 권장 사항
------- | ------- | ----------
Azure AD 로그인은 사용자가 브라우저를 닫았다가 다시 열 때 로그인 상태를 유지할 수 있는 옵션을 제공합니다. 이 설정은 해당 옵션을 숨깁니다.<br>사용자로부터 이 옵션을 숨기려면 **아니요**로 설정합니다. | &nbsp; | 옵션 숨기기는 세션 수명에는 영향을 미치지 않습니다.<br>SharePoint Online과 Office 2010의 일부 기능은 로그인 상태를 유지하도록 선택할 수 있는 사용자에 따라 달라집니다. 이 옵션을 **아니요**로 설정하는 경우 사용자에게 로그인을 요청하는 예상치 못한 메시지가 추가로 표시될 수 있습니다.

> [!NOTE]
> 모든 요소는 선택 사항입니다. 예를 들어 배경 이미지가 없는 배너 로고를 지정하는 경우 로그인 페이지는 대상 사이트(예: Office 365)에 대한 로고 및 배경 이미지를 표시합니다.

## <a name="add-company-branding-to-your-directory"></a>디렉터리에 회사 브랜딩 추가

1. 테넌트에 대한 전역 관리자인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. **사용자 및 그룹** > **회사 브랜딩** > **편집**을 선택합니다.
  
  ![사용자 지정 브랜딩 열기](./media/customize-branding/navigation-to-branding.png)
3. 사용자 지정할 요소를 수정합니다. 모든 요소는 선택 사항입니다.
  
  ![사용자 지정 브랜딩 편집](./media/customize-branding/edit-branding.png)
5. 완료되면 **저장**을 선택합니다.

로그인 페이지 브랜딩의 변경 내용을 보려면 최대 1시간이 걸릴 수 있습니다.

## <a name="add-language-specific-company-branding-to-your-directory"></a>디렉터리에 언어별 회사 브랜딩 추가

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. **사용자 및 그룹** > **회사 브랜딩** > **새 언어**를 선택합니다.
  
  ![언어별 브랜딩 요소 추가](./media/customize-branding/add-language.png)
5. 사용자 지정할 요소를 수정합니다. 모든 요소는 선택 사항입니다.
6. 완료되면 **저장**을 선택합니다.

로그인 페이지 브랜딩의 변경 내용을 보려면 최대 1시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서 Azure AD 디렉터리에 회사 브랜딩을 추가하는 방법을 배웠습니다. 

다음 링크를 통해 Azure Portal에서 Azure AD에 회사 브랜딩을 구성할 수 있습니다.

> [!div class="nextstepaction"]
> [회사 브랜딩 구성](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 