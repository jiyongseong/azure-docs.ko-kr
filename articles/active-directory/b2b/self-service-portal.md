---
title: Azure Active Directory B2B 공동 작업을 위한 셀프 서비스 등록 포털 | Microsoft Docs
description: Azure Active Directory B2B 공동 작업은 비즈니스 파트너가 선택적으로 회사 응용 프로그램에 액세스할 수 있게 함으로써 회사 간 관계를 지원합니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5ee55034e84fe09484a2f7613cc2224be70fdebb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259737"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Azure AD B2B 공동 작업 등록을 위한 셀프 서비스 포털

고객은 최종 사용자를 위한 [Azure Portal](https://portal.azure.com) 및 [응용 프로그램 액세스 패널](https://myapps.microsoft.com)을 통해 제공되는 기본 제공 기능으로 많은 작업을 수행할 수 있습니다. 하지만 B2B 사용자가 조직의 요구에 맞출 수 있도록 온보딩 워크플로를 사용자 지정해야 할 수도 있습니다. 이러한 작업은 [초대 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)를 사용하여 수행할 수 있습니다.

초대하는 조직에서는 개별적인 외부 협력자가 누구인지, 리소스에 액세스해야 하는 사람이 누구인지를 미리 알지 못할 수 있습니다. 초대하는 조직이 제어하는 일련의 정책을 통해 파트너 회사의 사용자가 스스로 등록할 수 있는 방법이 필요합니다. 이 시나리오는 API를 통해 가능합니다. 바로 이 기능을 구현하는 [샘플 프로젝트가 GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web)에 있습니다.

이 GitHub 프로젝트는 조직에서 API를 사용하여 액세스할 수 있는 앱을 결정하는 규칙을 통해 신뢰할 수 있는 파트너에게 정책 기반의 셀프 서비스 등록 기능을 제공하는 방법을 보여줍니다. 파트너 사용자는 필요할 때 리소스에 대한 액세스 권한을 얻을 수 있습니다. 초대하는 조직이 수동으로 등록하지 않아도 이 작업을 안전하게 수행할 수 있습니다. 사용자가 선택한 Azure 구독에 프로젝트를 쉽게 게시할 수 있습니다.

## <a name="as-is-code"></a>원본 코드

이 코드는 Azure Active Directory B2B 초대 API의 사용법을 보여 주기 위해 샘플로 제공됩니다. 따라서 개발 팀 또는 파트너가 코드를 사용자 지정해야 하고 프로덕션 시나리오에서 배포하기 전에 검토해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
* [Azure AD B2B 공동 작업 라이선스](licensing-guidance.md)
* [Azure Active Directory B2B 공동 작업 자주 묻는 질문(FAQ)](faq.md)