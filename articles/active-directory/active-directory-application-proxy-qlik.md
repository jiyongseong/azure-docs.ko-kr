---
title: Azure AD 앱 프록시 및 Qlik Sense | Microsoft Docs
description: Azure Portal에서 응용 프로그램 프록시를 설정하고 역방향 프록시에 커넥터를 설치합니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/03/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: ab029420ab9aa81b585c9b0315537bff2d9aaa6d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="application-proxy-and-qlik-sense"></a>응용 프로그램 프록시 및 Qlik Sense 
Azure Active Directory 응용 프로그램 프록시 및 Qlik Sense를 함께 사용하여 Qlik Sense 배포에 대한 원격 액세스를 제공하기 위해 응용 프로그램 프록시를 쉽게 사용할 수 있도록 합니다.  

## <a name="prerequisites"></a>필수 조건 
이 시나리오의 나머지 부분에서는 다음 작업을 완료했다고 가정합니다.
 
- [Qlik Sense](https://community.qlik.com/docs/DOC-19822) 구성 
- [응용 프로그램 프록시 커넥터 설치](manage-apps/application-proxy-enable.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Azure에 응용 프로그램 게시 
QlikSense를 게시하려면 Azure에서 두 개의 응용 프로그램을 게시해야 합니다.  

### <a name="application-1"></a>응용 프로그램 #1: 
앱을 게시하려면 다음 단계를 수행합니다. 1 ~ 8 단계에 대한 자세한 연습은 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](manage-apps/application-proxy-publish-azure-portal.md)를 참조하세요. 


1. 전역 관리자 권한으로 Azure Portal에 로그인합니다. 
2. **Azure Active Directory** > **Enterprise 응용 프로그램**을 선택합니다. 
3. 블레이드의 위쪽에서 **추가**를 선택합니다. 
4. **온-프레미스 응용 프로그램**을 선택합니다. 
5.       새 앱에 대한 정보로 필수 필드를 작성합니다. 설정에 대해 다음 지침을 사용합니다. 
    - **내부 URL**: 이 응용 프로그램에는 자체 QlikSense URL인 내부 URL이 있어야 합니다. 예: **https&#58;//demo.qlikemm.com:4244** 
    - **사전 인증 방법**: Azure Active Directory(권장되지만 필수 아님) 
1.       블레이드의 아래쪽에서 **추가**를 선택합니다. 응용 프로그램이 추가되고 빠른 시작 메뉴가 열립니다. 
2.       빠른 시작 메뉴에서 **테스트할 사용자 지정**을 선택하고 응용 프로그램에 하나 이상의 사용자를 추가합니다. 이 테스트 계정에 온-프레미스 응용 프로그램에 대한 액세스 권한이 있는지 확인합니다. 
3.       **할당**을 선택하여 테스트 사용자 할당을 저장합니다. 
4.       (선택 사항) 앱 관리 블레이드에서 Single Sign-On을 선택합니다. 드롭다운 메뉴에서 **Kerberos 제한된 위임**을 선택하고 Qlik 구성에 따라 필수 필드를 입력합니다. **저장**을 선택합니다. 

### <a name="application-2"></a>응용 프로그램 #2: 
다음과 같은 예외가 포함된 응용 프로그램 #1과 동일한 단계를 따릅니다. 

**5단계**: 내부 URL은 응용 프로그램에서 사용하는 인증 포트를 사용하는 QlikSense URL이어야 합니다. 기본값은 HTTPS의 경우 **4244**이고 HTTP의 경우 4248입니다. 예: **https&#58;//demo.qlik.com:4244**</br></br> 
**10단계:** SSO를 설정하지 않고 **Single Sign-On을 설정하지 않고** 그대로 둡니다.
 
 
## <a name="testing"></a>테스트 
응용 프로그램을 테스트할 준비가 되었습니다. 응용 프로그램 #1에 QlikSense를 게시하는 데 사용되는 외부 URL에 액세스하고 두 응용 프로그램에 할당된 사용자 권한으로 로그인합니다.  

## <a name="next-steps"></a>다음 단계

- [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](manage-apps/application-proxy-publish-azure-portal.md)
- [응용 프로그램 프록시 커넥터 작업](manage-apps/application-proxy-connector-groups.md)
