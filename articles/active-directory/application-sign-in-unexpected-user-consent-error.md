---
title: 응용 프로그램에 대한 동의를 수행할 때 예기치 않은 오류 | Microsoft Docs
description: 응용 프로그램에 대한 동의 프로세스 도중 발생할 수 있는 오류 및 사용자가 할 수 있는 조치를 설명합니다.
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
ms.openlocfilehash: bbc0cee8a44773c025c6174eaf7eccaba81b8d1b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>응용 프로그램에 대한 동의를 수행할 때 예기치 않은 오류

이 문서에서는 응용 프로그램에 대한 동의 프로세스 도중 발생할 수 있는 오류에 대해 설명합니다. 오류 메시지를 포함하지 않는 예기치 않은 동의 프롬프트의 문제를 해결하려는 경우 [Azure AD의 인증 시나리오](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios)를 참조하세요.

Azure Active Directory와 통합되는 많은 응용 프로그램을 작동시키기 위해 다른 리소스에 대한 사용 권한이 필요합니다. 또한 이러한 리소스가 Azure Active Directory와 통합되면 종종 일반적인 동의 프레임워크를 사용하여 액세스하기 위한 사용 권한을 요청합니다. 

이로 인해 일반적으로 응용 프로그램을 처음 사용하는 경우 발생하지만 응용 프로그램의 후속 사용에도 발생할 수 있는 동의 확인 프롬프트가 표시됩니다.

응용 프로그램에 필요한 사용 권한에 대해 동의한 사용자에게는 특정 조건이 true여야 합니다. 이러한 조건이 충족되지 않으면 다양한 오류가 발생할 수 있습니다. 내용은 다음과 같습니다.

## <a name="requesting-not-authorized-permissions-error"></a>권한 없는 사용 권한 요청 오류
* **AADSTS90093:** &lt;clientAppDisplayName&gt;은 사용 권한이 부여되지 않은 하나 이상의 사용 권한을 요청합니다. 사용자 대신 이 응용 프로그램에 동의할 수 있는 관리자에게 문의합니다.

이 오류는 회사 관리자가 아닌 사용자가 관리자만이 부여할 수 있는 사용 권한을 요청하는 응용 프로그램을 사용하려고 할 때 발생합니다. 해당 조직을 대신하여 응용 프로그램에 대한 액세스 권한을 부여하는 관리자가 이 오류를 해결할 수 있습니다.

## <a name="policy-prevents-granting-permissions-error"></a>사용 권한 부여를 방지하는 정책 오류
* **AADSTS90093:** &lt;tenantDisplayName&gt;의 관리자는 사용 권한이 요청하는 &lt;앱의 이름&gt;을 부여하지 못하도록 방지하는 정책을 설정했습니다. 사용자 대신 이 앱에 대한 권한을 부여할 수 있는 &lt;tenantDisplayName&gt;의 관리자에게 문의하세요.

회사 관리자가 응용 프로그램에 대해 동의하는 사용자의 기능을 해제한 다음 관리자가 아닌 사용자가 동의를 필요로 하는 응용 프로그램을 사용하려는 경우에 이 오류가 발생합니다. 해당 조직을 대신하여 응용 프로그램에 대한 액세스 권한을 부여하는 관리자가 이 오류를 해결할 수 있습니다.

## <a name="intermittent-problem-error"></a>일시적인 문제 오류
* **AADSTS90090:** &lt;clientAppDisplayName&gt;에 권한을 부여하려고 시도하는 사용 권한을 기록하는 일시적인 문제가 발생했습니다. 나중에 다시 시도하세요.

이 오류는 일시적인 서비스 쪽 문제가 발생했음을 나타냅니다. 응용 프로그램에 대한 동의하도록 다시 시도하여 문제를 해결할 수 있습니다.

## <a name="resource-not-available-error"></a>리소스 사용할 수 없음 오류
* **AADSTS65005:** &lt;clientAppDisplayName&gt; 앱은 사용할 수 없는 &lt;resourceAppDisplayName&gt; 리소스에 액세스하는 사용 권한을 요청했습니다. 

응용 프로그램 개발자에게 문의하세요.

##  <a name="resource-not-available-in-tenant-error"></a>테넌트에서 리소스 사용할 수 없음 오류
* **AADSTS65005:** &lt;clientAppDisplayName&gt;은 &lt;tenantDisplayName&gt; 조직에서 사용할 수 없는 &lt;resourceAppDisplayName&gt; 리소스에 대한 액세스 권한을 요청합니다. 

이 리소스를 사용할 수 있도록 하거나 &lt;tenantDisplayName&gt;의 관리자에게 문의합니다.

## <a name="permissions-mismatch-error"></a>권한 불일치 오류
* **AADSTS65005:** 앱이 &lt;resourceAppDisplayName&gt; 리소스에 액세스하기 위한 동의를 요청했습니다. 앱 등록 중에 앱이 미리 구성된 방법과 일치하지 않기 때문에 이 요청에 실패했습니다. 앱 공급업체에게 문의하세요.**

사용자가 동의하려는 응용 프로그램이 조직의 디렉터리(테넌트)에서 찾을 수 없는 리소스 응용 프로그램에 액세스하는 사용 권한을 요청하는 경우에 이러한 오류가 발생합니다. 이 오류는 다음과 같은 이유로 발생할 수 있습니다.

-   클라이언트 응용 프로그램 개발자가 해당 응용 프로그램을 잘못 구성하여 잘못된 리소스에 대한 액세스 권한을 요청했습니다. 이 경우에 응용 프로그램 개발자는 이 문제를 해결하기 위해 클라이언트 응용 프로그램의 구성을 업데이트해야 합니다.

-   대상 리소스 응용 프로그램을 나타내는 서비스 주체는 조직에서 존재하지 않거나 이전에 있었지만 제거되었습니다. 이 문제를 해결하려면 클라이언트 응용 프로그램이 이에 대한 권한을 요청할 수 있도록 조직에서 리소스 응용 프로그램의 서비스 주체를 제공해야 합니다. 다음을 비롯한 다양한 방법으로 응용 프로그램의 종류에 따라 발생할 수 있습니다.

    -   리소스 응용 프로그램의 구독 구입(게시된 Microsoft 응용 프로그램)

    -   리소스 응용 프로그램에 대한 동의

    -   Azure Portal을 통해 응용 프로그램 사용 권한 부여

    -   Azure AD 응용 프로그램 갤러리의 응용 프로그램 추가

## <a name="next-steps"></a>다음 단계 

[Azure Active Directory에서 앱, 사용 권한 및 동의(v1 끝점)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Azure Active Directory의 범위, 사용 권한 및 동의(v2.0 끝점)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


