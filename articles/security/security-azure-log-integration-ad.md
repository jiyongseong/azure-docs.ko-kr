---
title: "Azure 로그 통합 및 Azure Active Directory 감사 로그 | Microsoft Docs"
description: "Azure 로그 통합 서비스를 설치하고 Azure 감사 로그의 로그를 통합하는 방법에 대해 알아봅니다."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 02/16/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 0f45f43a0296a7d90a68b0526f805ea50a1ce6c6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Azure Active Directory 감사 로그 통합

Azure AD(Azure Active Directory) 감사 이벤트를 통해 Azure Active Directory에서 발생한 권한 있는 작업을 식별할 수 있습니다. [Azure Active Directory 감사 보고서 이벤트](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md)를 검토하여 추적할 수 있는 이벤트 유형을 확인할 수 있습니다.


> [!NOTE]
> 이 문서의 단계를 시도하기 전에 먼저 [시작하기](security-azure-log-integration-get-started.md) 문서를 검토하고 해당 단계를 완료해야 합니다.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Azure Active Directory 감사 로그 통합 단계

1. 명령 프롬프트를 열고 이 명령을 실행합니다.

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. 다음 명령을 실행합니다. 
 
   ``azlog createazureid``

   이 명령은 사용자에게 Azure 로그인을 요구합니다. 그런 다음 로그인한 사용자가 관리자, 공동 관리자 또는 소유자인 Azure 구독을 호스트하는 Azure AD 테넌트에 Azure Active Directory 서비스 주체를 만듭니다. 로그인한 사용자가 Azure AD 테넌트의 게스트 사용자이면 명령이 실패합니다. Azure에 대한 인증은 Azure AD를 통해 수행됩니다. Azure 로그 통합에 대한 서비스 주체를 만들면 Azure 구독을 읽을 수 있는 Azure AD ID가 생성됩니다.

3. 다음 명령을 실행하여 테넌트 ID를 제공합니다. 이 명령을 실행하려면 테넌트 관리자 역할의 구성원이어야 합니다.

   ``Azlog.exe authorizedirectoryreader tenantId``

   예:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. 다음 폴더에서 Azure Active Directory 감사 로그 JSON 파일을 만들었는지 확인합니다.

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

다음 비디오는 이 문서에서 설명하는 단계를 보여 줍니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> SIEM(보안 정보 및 이벤트 관리) 시스템으로 JSON 파일에서 정보를 가져오는 것과 관련한 특정 지침은 해당 SIEM 공급업체에 문의하세요.

[Azure 로그 통합 MSDN 포럼](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)을 통해 커뮤니티의 지원을 받을 수 있습니다. 이 포럼을 통해 Azure 로그 통합 커뮤니티의 사람들은 질문, 답변, 팁, 요령 등을 통해 서로 지원할 수 있습니다. 또한 Azure 로그 통합 팀이 이 포럼을 모니터링하며 가능한 한 언제든지 도움을 드릴 것입니다.

[지원 요청](../azure-supportability/how-to-create-azure-support-request.md)을 열 수도 있습니다. 지원을 요청하려면 서비스로 **로그 통합**을 선택합니다.

## <a name="next-steps"></a>다음 단계
Azure 로그 통합에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 로그에 대한 Microsoft Azure 로그 통합](https://www.microsoft.com/download/details.aspx?id=53324): 이 다운로드 센터 페이지는 Azure 로그 통합에 대한 세부 정보, 시스템 요구 사항 및 설치 지침을 제공합니다.
* [Azure 로그 통합 소개](security-azure-log-integration-overview.md): 이 문서에서는 Azure 로그 통합, 주요 기능 및 작동 원리를 소개합니다.
* [Azure 로그 통합 FAQ](security-azure-log-integration-faq.md): 이 문서는 Azure 로그 통합에 대한 질문에 답변합니다.
* [Azure 진단 및 Azure 감사 로그를 위한 새 기능](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): 이 블로그 게시물에서는 Azure 리소스 운영에 대한 정보 수집에 도움이 되는 Azure 감사 로그 및 기타 기능에 대해 소개합니다.
