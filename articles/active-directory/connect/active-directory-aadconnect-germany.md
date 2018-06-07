---
title: "Microsoft Cloud 독일의 Azure AD Connect"
description: "Azure AD Connect는 온-프레미스 디렉터리와 Azure Active Directory를 통합니다. 이렇게 하면 Azure AD와 통합된 Office 365, Azure, 및 SaaS 응용 프로그램에 대한 공통 ID를 제공할 수 있습니다."
keywords: "Azure AD Connect 소개, Azure AD Connect 개요, Azure AD Connect 정의, Active Directory 설치, 독일, Black Forest"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 2b0c05b12877fdda11b941d822c265d34d5c04ca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Microsoft Cloud 독일의 Azure AD Connect - 공개 미리 보기
## <a name="introduction"></a>소개
Azure AD Connect는 온-프레미스 Active Directory와 Azure Active Directory 간의 동기화를 제공합니다.
현재 [Microsoft Cloud 독일](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) 에 있는 대부분의 시나리오는 연산자에 의해 수행되어야 합니다. Microsoft Cloud 독일을 사용할 경우 다음을 고려해야 합니다.

* 동기화가 성공적으로 수행되려면 다음 URL은 프록시 서버에 대해 열려 있어야 합니다.
  
  * *.microsoftonline.de
  * *.windows.net
  * * 인증서 해지 목록
* Azure AD 디렉터리에 로그인할 경우 onmicrosoft.de 도메인의 계정을 사용해야 합니다.
* 사용할 수 없는 기능은 다음과 같습니다.
  * Azure AD Connect Health
  * 자동 업데이트
 
## <a name="download"></a>다운로드
포털 내의 Azure AD Connect 블레이드에서 Azure AD Connect를 다운로드할 수 있습니다.  아래 지침을 사용하여 Azure AD Connect 블레이드를 찾을 수 있습니다.

### <a name="the-azure-ad-connect-blade"></a>Azure AD Connect 블레이드
Azure Portal에 로그인하면 다음을 수행합니다.

1. 찾아보기로 이동
2. Azure Active Directory 선택
3. 그런 다음 Azure AD Connect 선택

다음이 표시되어야 합니다.

![Azure AD Connect 블레이드](media/active-directory-aadconnect-germany/germany1.png)

다음 테이블에서는 블레이드에 표시된 기능을 설명합니다.

| 제목 | 설명 |
| --- | --- |
| 동기화 상태 |동기화가 활성화되었는지 아니면 비활성화되었는지 여부를 알려줍니다. |
| 최신 동기화 |마지막으로 성공한 동기화가 완료된 시점입니다. |
| 페더레이션된 도메인 |현재 구성된 페더레이션된 도메인의 수를 보여 줍니다. |

## <a name="installation"></a>설치
Azure AD Connect를 설치하려면 [여기](active-directory-aadconnect.md#install-azure-ad-connect)에서 설명서를 사용할 수 있습니다.

## <a name="advanced-features-and-additional-information"></a>고급 기능 및 추가 정보
사용자 지정 설정이나 고급 구성에 대한 추가 정보 및 지침은 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 시작하세요.  이 페이지는 추가 지침에 대한 정보 및 링크를 제공합니다.

