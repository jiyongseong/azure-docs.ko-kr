---
title: 비갤러리 응용 프로그램에 대해 비갤러리 Single Sign-On 구성 문제 | Microsoft Docs
description: Azure AD 응용 프로그램 갤러리에 나열되지 않은 사용자 지정 SAML 응용 프로그램에 페더레이션된 Single Sign-On을 구성할 때 발생하는 일반적인 문제 몇 가지를 해결
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 1990f6595be86d7f3a50f6c764a855aedf2cbcbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>비갤러리 응용 프로그램에 대해 비갤러리 Single Sign-On 구성 문제

응용 프로그램을 구성할 때 문제가 발생 할 경우. [Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery) 문서에 있는 단계를 모두 수행했는지 확인합니다.

## <a name="cant-add-another-instance-of-the-application"></a>응용 프로그램의 다른 인스턴스를 추가할 수 없음

응용 프로그램의 두 번째 인스턴스를 추가하려면 다음을 수행할 수 있어야 합니다.

-   두 번째 인스턴스에 대한 고유 식별자를 구성합니다. 첫 번째 인스턴스에 대해 사용한 것과 동일한 식별자를 구성할 수 없습니다.

-   첫 번째 인스턴스에 대해 사용한 것과 다른 인증서를 구성합니다.

응용 프로그램이 이전 항목을 지원하지 않으면 두 번째 인스턴스를 구성할 수 없습니다.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>EntityID(사용자 식별자) 형식을 설정하는 위치

사용자 인증 후에 Azure AD에서 응답을 통해 응용 프로그램으로 보내는 EntityID(사용자 ID) 형식은 선택할 수 없습니다.

Azure AD에서는 선택한 값 또는 SAML AuthRequest에서 응용 프로그램이 요청한 형식을 기반으로 NameID 특성(사용자 ID)의 형식을 선택합니다. 자세한 내용은 NameIDPolicy 섹션 아래의 [Single Sign-On SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) 문서에서 확인할 수 있습니다.

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Azure AD에서 응용 프로그램 메타데이터 또는 인증서를 가져오는 위치

Azure AD에서 응용 프로그램 메타데이터 또는 인증서를 다운로드하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성한 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **SAML 서명 인증서** 섹션으로 이동한 다음 **다운로드** 열 값을 클릭합니다. Single Sign-On을 구성해야 할 응용 프로그램에 따라 메타데이터 XML이나 인증서를 다운로드하는 옵션이 표시됩니다.

Azure AD에서는 메타데이터를 가져오는 URL을 제공하지 않습니다. 메타데이터는 XML 파일로만 검색할 수 있습니다.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>응용 프로그램에 전송된 SAML 클레임을 사용자 지정하는 방법을 알 수 없음

응용 프로그램에 전송된 SAML 특성 클레임을 사용자 지정하는 방법을 알아보려면 [Azure Active Directory의 클레임 매핑](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 응용 프로그램 관리](manage-apps/what-is-application-management.md)
