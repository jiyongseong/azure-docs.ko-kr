---
title: Azure Active Directory SSO 플러그 인에 대한 FAQ | Microsoft Docs
description: Azure Active Directory와 Jira/Confluence 간 Single Sign-On 구성에 대한 질문과 대답에 대한 답을 가져옵니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: b642d6de9fd9265d7f7ee11b827a36d20e5bcce7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Azure Active Directory SSO 플러그 인에 대한 FAQ 

## <a name="what-does-the-plug-in-do"></a>플러그 인은 무엇을 수행하나요?

플러그 인은 Atlassian Jira(Jira Core, Jira Software, Jira Service Desk 포함) 및 Confluence 온-프레미스 소프트웨어에 대한 SSO(Single Sign-On) 기능을 제공합니다. 플러그 인은 IdP(ID 공급자)로 Azure AD(Azure Active Directory)와 함께 작동합니다.

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>플러그 인과 함께 작동하는 Atlassian 제품은 무엇인가요?

플러그 인은 Jira 및 Confluence의 온-프레미스 버전과 함께 작동합니다.

## <a name="does-the-plug-in-work-on-cloud-versions"></a>플러그 인은 클라우드 버전에서 작동하나요?

번호 플러그 인은 Jira 및 Confluence의 온-프레미스 버전만을 지원합니다.

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>플러그 인에서 지원하는 Jira 및 Confluence 버전은 무엇인가요?

플러그 인에서는 이러한 버전을 지원합니다.

* Jira Core 및 소프트웨어: 6.0 ~ 7.2.2 
* Jira Service Desk: 3.0 ~ 3.2 
* Confluence: 5.0 ~ 5.10

## <a name="is-the-plug-in-free-or-paid"></a>플러그 인은 무료인가요, 유료인가요?

무료 추가 기능입니다. Atlassian Marketplace에서 설치할 수 있습니다.

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>플러그 인을 배포한 후 Jira 또는 Confluence를 다시 시작해야 하나요?

다시 시작할 필요가 없습니다. 플러그 인 사용을 즉시 시작할 수 있습니다.

## <a name="how-do-i-get-support-for-the-plug-in"></a>플러그 인에 대한 지원을 받으려면 어떻게 하나요?

[Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 연락합니다. 팀은 24-48 업무 시간 안에 응답합니다. 

또한 Azure Portal 채널을 통해 Microsoft 지원 티켓을 발급할 수 있습니다.

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>플러그 인은 Jira 및 Confluence에 설치된 Mac 또는 Ubuntu에서 작동하나요?

플러그 인은 Jira 및 Confluence에 설치된 64비트 Windows 서버에서만 테스트를 거쳤습니다.

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>플러그 인은 Azure AD 이외에 IdP와 함께 작동하나요?

번호 Azure AD와만 함께 작동합니다.

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>플러그 인과 함께 작동하는 SAML 버전은 무엇인가요?

SAML 2.0과 함께 작동합니다.

## <a name="does-the-plug-in-do-user-provisioning"></a>플러그 인은 사용자 프로비저닝을 수행하나요?

번호 플러그 인은 SAML 2.0 기반 SSO만 제공합니다. SSO 로그인 전에 응용 프로그램에서 사용자를 프로비전해야 합니다.

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>플러그 인에서 지원하는 Jira 및 Confluence 버전은 무엇인가요?

번호 플러그 인은 Jira 및 Confluence의 온-프레미스 버전과 함께 작동합니다.

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>플러그 인은 Jira 및 Confluence의 HTTP 버전과 함께 작동하나요?

번호 플러그 인은 HTTPS가 설정된 설치 버전에서만 작동합니다.