---
title: 응용 프로그램 프록시 응용 프로그램을 사용하는 경우 발생하는 이 회사 응용 프로그램에 액세스할 수 없습니다. 오류 | Microsoft Docs
description: Azure AD 응용 프로그램 프록시 응용 프로그램의 일반적인 액세스 문제를 해결하는 방법입니다.
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
ms.openlocfilehash: b9a643dab98fa685bb4873705c177a834af831ce
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156095"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>응용 프로그램 프록시 응용 프로그램을 사용하는 경우 발생하는 "이 회사 응용 프로그램에 액세스할 수 없습니다." 오류

이 문서는 Azure AD 응용 프로그램 프록시 응용 프로그램에서 "이 회사 앱에 액세스할 수 없습니다" 오류에 대한 일반적인 문제를 해결하는 데 도움이 됩니다.

## <a name="overview"></a>개요
이 오류가 표시되면 오류 페이지에서 상태 코드를 찾습니다. 해당 코드는 다음 상태 코드 중 하나일 수 있습니다.

-   **게이트웨이 시간 초과**: 응용 프로그램 프록시 서비스를 커넥터에 연결할 수 없습니다. 이 오류는 일반적으로 커넥터 할당, 커넥터 자체 또는 커넥터 주위의 네트워킹 규칙에 문제가 있음을 나타냅니다.

-   **잘못된 게이트웨이**: 커넥터는 백 엔드 응용 프로그램에 연결할 수 없습니다. 이 오류는 응용 프로그램이 잘못 구성되었음을 나타낼 수 있습니다.

-   **사용 권한 없음**: 사용자는 응용 프로그램에 액세스할 수 있는 권한이 없습니다. 이 오류는 사용자가 Azure Active Directory에서 응용 프로그램에 할당되지 않거나 백 엔드에서 사용자에게 응용 프로그램에 액세스할 수 있는 권한이 없는 경우에 발생할 수 있습니다.

코드를 찾으려면 "상태 코드" 필드에서 오류 메시지의 왼쪽 아래에 있는 텍스트를 확인합니다. 또한 페이지 아래쪽의 추가 팁도 찾아봅니다.

   ![게이트웨이 시간 초과 오류](./media/application-proxy/connection-problem.png)

이러한 오류의 근본 원인을 해결하는 방법에 대한 세부 정보 및 제안된 해결에 대한 자세한 내용은 아래의 해당 섹션을 참조하세요.

## <a name="gateway-timeout-errors"></a>게이트웨이 시간 초과 오류

서비스가 커넥터에 연결하려고 하지만 제한 시간 내에 연결할 수 없는 경우 게이트웨이 시간 초과가 발생합니다. 이 오류는 일반적으로 커넥터가 작동하지 않는 커넥터 그룹에 응용 프로그램이 할당되었거나 커넥터에 필요한 일부 포트가 열리지 않는 경우에 발생합니다.


## <a name="bad-gateway-errors"></a>잘못된 게이트웨이 오류

잘못된 게이트웨이는 커넥터가 백 엔드 응용 프로그램에 연결할 수 없음을 나타냅니다. 올바른 응용 프로그램을 게시했는지 확인합니다. 이 오류의 원인이 되는 일반적인 실수는 다음과 같습니다.

-   오타 또는 내부 URL의 실수

-   응용 프로그램의 루트 게시 안 함 예를 들어 <http://expenses/reimbursement>를 게시했지만 <http://expenses>에 액세스하려고 하는 경우입니다.

-   KCD(Kerberos 제한 위임) 구성 문제

-   백 엔드 응용 프로그램 문제

## <a name="forbidden-errors"></a>금지된 오류

금지된 오류가 표시되는 경우 사용자가 응용 프로그램에 할당되지 않았습니다. 이 오류는 Azure Active Directory 또는 백 엔드 응용 프로그램에서 발생할 수 있습니다.

Azure의 응용 프로그램에 사용자를 할당하는 방법을 알아보려면 [구성 문서](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal#add-a-test-user)를 참조하세요.

Azure의 응용 프로그램에 사용자가 할당되었는지 확인하려면 백 엔드 응용 프로그램에서 사용자 구성을 확인합니다. Kerberos 제한 위임/Windows 통합 인증을 사용하는 경우 KCD 문제 해결 페이지에 나와 있는 지침을 참조하세요.

## <a name="check-the-applications-internal-url"></a>응용 프로그램의 내부 URL 확인

첫 번째 빠른 단계로, **엔터프라이즈 응용 프로그램**을 통해 응용 프로그램을 연 다음, **응용 프로그램 프록시** 메뉴를 선택하여 내부 URL을 다시 확인하고 수정합니다. 내부 URL이 응용 프로그램에 액세스하기 위해 온-프레미스 네트워크에서 사용된 URL인지 확인합니다.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>응용 프로그램이 작동하는 커넥터 그룹에 할당되었는지 확인합니다.

응용 프로그램이 작동하는 커넥터 그룹에 할당되었는지 확인하려면 다음을 수행합니다.

1.  **Azure Active Directory**로 이동하고 **엔터프라이즈 응용 프로그램**을 클릭한 다음 **응용 프로그램 프록시**를 클릭하여 포털에서 응용 프로그램을 엽니다. 응용 프로그램을 열고 왼쪽 메뉴에서 **응용 프로그램 프록시**를 선택합니다.

2.  커넥터 그룹 필드를 확인합니다. 그룹에 활성 커넥터가 없는 경우 경고가 표시됩니다. 경고가 표시되지 않으면 "모든 필요한 포트를 허용 목록에 추가했는지 확인"으로 이동합니다.

3.  잘못된 커넥터 그룹이 표시되면 드롭다운을 사용하여 올바른 그룹을 선택하고 경고가 더 이상 표시되지 않는지 확인합니다. 원하는 커넥터 그룹이 표시되면 경고 메시지를 클릭하여 커넥터 관리가 있는 페이지를 엽니다.

4.  여기에서부터는 자세히 다룰 몇 가지 방법이 있습니다.

  * 활성 커넥터를 그룹으로 이동: 그룹에 속해야 하며 대상 백 엔드 응용 프로그램에 직접 연결된 활성 커넥터가 있는 경우 지정된 그룹으로 커넥터를 이동할 수 있습니다. 이렇게 하려면 커넥터를 클릭합니다. "커넥터 그룹" 필드에서 드롭다운을 사용하여 올바른 그룹을 선택하고 저장을 클릭합니다.

  * 해당 그룹의 새 커넥터 다운로드: 이 페이지에서 [새 커넥터 다운로드](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download)에 대한 링크를 가져올 수 있습니다. 백 엔드 응용 프로그램에 직접 연결되는 컴퓨터에 커넥터를 설치합니다. 일반적으로 커넥터는 응용 프로그램과 동일한 서버에 설치됩니다. 다운로드 커넥터 링크를 사용하여 대상 컴퓨터에 커넥터를 다운로드합니다. 다음으로 커넥터를 클릭하고 "커넥터 그룹" 드롭다운을 사용하여 해당 그룹에 속하는지 확인합니다.

  * 비활성 커넥터 조사: 커넥터를 비활성으로 표시하는 경우 서비스에 연결할 수 없습니다. 이 오류는 일반적으로 필요한 포트 일부가 차단되어 발생합니다. 이 문제를 해결하려면 "모든 필요한 포트를 허용 목록에 추가했는지 확인"으로 이동합니다.

작동하는 커넥터를 사용하여 응용 프로그램을 그룹에 할당했는지 확인하기 위해 이러한 단계를 사용한 후에 응용 프로그램을 다시 테스트합니다. 여전히 작동하지 않는 경우 다음 섹션으로 계속합니다.

## <a name="check-all-required-ports-are-whitelisted"></a>모든 필요한 포트가 허용 목록에 포함되는지 확인

필요한 모든 포트가 열려 있는지를 확인하려면 포트 열기에 대한 설명서를 참조하세요. 모든 필요한 포트가 열려 있으면 다음 섹션으로 이동합니다.

## <a name="check-for-other-connector-errors"></a>다른 커넥터 오류에 대한 확인

이 문제를 해결하는 사항이 하는 경우 다음 단계에서는 커넥터를 사용하여 문제 또는 오류를 확인합니다. [문제 해결 문서](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors)에서 일반적인 오류를 확인할 수 있습니다. 

오류를 식별하기 위해 커넥터 로그를 직접 찾아볼 수도 있습니다. 대부분의 오류 메시지는 수정에 대한 특정 권장 사항을 공유합니다. 로그를 보려면 [커넥터 설명서](manage-apps/application-proxy-connectors.md#under-the-hood)를 참조하세요.

## <a name="additional-resolutions"></a>추가 해상도

위를 통해 문제를 수정할 수 없는 경우 몇 가지 다른 원인이 있습니다. 이 문제를 식별하려면 다음을 수행합니다.

응용 프로그램이 IWA(통합 Windows 인증)를 사용하도록 구성된 경우 Single Sign-On 없이 응용 프로그램을 테스트합니다. 그렇지 않으면 다음 단락으로 이동합니다. Single Sign-On 없이 응용 프로그램을 확인하려면 **엔터프라이즈 응용 프로그램**을 통해 응용 프로그램을 열고 **Single Sign-On** 메뉴로 이동합니다. 드롭다운을 "Windows 통합 인증"에서 "Azure AD Single Sign-On 사용 안 함"으로 변경합니다. 

브라우저를 열고 응용 프로그램에 다시 액세스를 시도합니다. 인증을 위한 메시지가 표시되고 응용 프로그램을 가져와야 합니다. 인증할 수 있는 경우 Single Sign-On을 사용할 수 있게 하는 KCD(Kerberos 제한 위임) 구성에 문제가 있습니다. 자세한 내용은 KCD 문제 해결 페이지를 참조하세요.

오류가 계속 표시되면 커넥터를 설치한 컴퓨터로 이동하여 브라우저를 열고 응용 프로그램에 사용되는 내부 URL에 대한 연결을 시도합니다. 커넥터는 동일한 컴퓨터에서 다른 클라이언트처럼 작동합니다. 응용 프로그램에 연결할 수 없는 경우 해당 컴퓨터를 응용 프로그램에 연결하거나 응용 프로그램에 액세스할 수 있는 서버에서 커넥터를 사용할 수 없는 원인을 조사합니다.

해당 컴퓨터의 응용 프로그램에 연결하면 커넥터 자체의 문제 또는 오류를 찾습니다. [문제 해결 문서](active-directory-application-proxy-troubleshoot.md#connector-errors)에서 일반적인 오류를 확인할 수 있습니다. 오류를 식별하기 위해 커넥터 로그를 직접 찾아볼 수도 있습니다. 다양한 오류 메시지는 수정하기 위해 보다 구체적인 권장 사항을 공유합니다. 로그를 보는 방법을 알아보려면 [커넥터 문서](manage-apps/application-proxy-connectors.md#under-the-hood)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure AD 응용 프로그램 프록시 커넥터 이해](manage-apps/application-proxy-connectors.md)
