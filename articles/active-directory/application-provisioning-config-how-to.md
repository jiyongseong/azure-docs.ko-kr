---
title: "Azure AD 갤러리 응용 프로그램에 대해 사용자 프로비전을 구성하는 방법 | Microsoft Docs"
description: "Azure AD 응용 프로그램 갤러리에 이미 나열된 응용 프로그램에 대해 다양한 사용자 계정 프로비전 및 프로비전 해제를 빠르게 구성하는 방법"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: da7050edae282c90c4f92c588ce8fe501ccba50d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Azure AD 갤러리 응용 프로그램에 대해 사용자 프로비전을 구성하는 방법

*사용자 계정 프로비전*은 응용 프로그램의 로컬 사용자 프로필 저장소에 사용자 계정 레코드를 생성, 업데이트 및/또는 비활성화하는 작업입니다. 대부분의 클라우드 및 SaaS 응용 프로그램은 고유한 로컬 사용자 프로필 저장소에 사용자 역할 및 권한을 저장하고, 로컬 저장소의 그러한 사용자 레코드의 존재는 Single Sign-On 및 작업에 대한 액세스를 위해 *필요*합니다.

Azure Portal에서 엔터프라이즈 앱에 대한 왼쪽 탐색 창의 **프로비전** 탭에는 해당 앱에 대해 지원되는 프로비전 모드가 표시됩니다. 다음 두 값 중 하나일 수 있습니다.

## <a name="configuring-an-application-for-manual-provisioning"></a>수동 프로비전에 대한 응용 프로그램 구성

*수동* 프로비전이란 사용자 계정을 해당 앱에서 제공하는 방법을 사용하여 수동으로 만들어야 한다는 의미입니다. 해당 앱의 관리 포털에 로그인하고 웹 기반 사용자 인터페이스를 사용하여 사용자를 추가한다는 의미일 수 있습니다. 또는 해당 응용 프로그램에서 제공하는 메커니즘을 사용하여 사용자 계정 세부 정보를 스프레드시트에 업로드한다는 의미일 수 있습니다. 앱에서 제공한 설명서를 참조하거나 앱 개발자에게 문의하여 사용할 수 있는 메커니즘을 확인하세요.

해당 응용 프로그램에 대해 수동 모드만 표시되는 경우 해당 앱에 대해 자동 Azure AD 프로비전 커넥터가 아직 생성되지 않았음을 의미합니다. 또는 앱에서 자동화된 프로비전 커넥터를 빌드하는 필수 구성 요소 사용자 관리 API를 지원하지 않음을 의미합니다.

해당 앱에 대해 자동 프로비전 지원을 요청하려면 [Azure Active Directory 응용 프로그램 요청](https://aka.ms/aadapprequest)에서 요청 양식을 작성하세요.

## <a name="configuring-an-application-for-automatic-provisioning"></a>자동 프로비전에 대한 응용 프로그램 구성

*자동*이란 Azure AD 프로비전 커넥터가 이 응용 프로그램에 대해 개발되었음을 의미합니다. Azure AD 프로비전 서비스 및 작동 방식에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비전 및 프로비전 해제](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)를 참조하세요.

특정 사용자 및 그룹을 응용 프로그램에 프로비저닝하는 방법에 대한 자세한 내용은 [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning)를 참조하세요.

자동 프로비전을 사용하도록 설정하고 구성하는 데 필요한 실제 단계는 응용 프로그램에 따라 달라집니다.

>[!NOTE]
>응용 프로그램의 프로비전 설정에 관한 설정 자습서를 찾아 해당 단계에 따라 앱 및 Azure AD를 구성하여 프로비전 연결을 생성해야 합니다. 
>
>

앱 자습서는 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)에서 찾을 수 있습니다.

프로비전을 설정할 때 고려해야 할 중요한 사항은 Azure AD에서 응용 프로그램으로 이동하는 사용자(또는 그룹)를 정의하는 특성 매핑 및 워크플로를 검토하고 구성하는 것입니다. 여기에는 두 시스템 간 사용자/그룹을 고유하게 식별하고 일치하는 데 사용되는 “일치하는 속성” 설정이 포함됩니다. 이 중요한 프로세스에 대한 자세한 정보

## <a name="next-steps"></a>다음 단계
[Azure Active Directory에서 SaaS 응용 프로그램에 대한 사용자 프로비전 특성 매핑 사용자 지정](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

