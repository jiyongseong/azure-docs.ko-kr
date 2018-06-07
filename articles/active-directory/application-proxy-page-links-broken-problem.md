---
title: 페이지의 링크가 응용 프로그램 프록시 응용 프로그램에서 작동하지 않음 | Microsoft Docs
description: Azure AD와 통합한 응용 프로그램 프록시 응용 프로그램에 대한 링크가 끊어지는 문제를 해결하는 방법
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
ms.openlocfilehash: 7c505080e6b6144ab3cf24ad89bb084efbb77c29
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155468"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>페이지의 링크가 응용 프로그램 프록시 응용 프로그램에서 작동하지 않습니다.

이 문서는 Azure Active Directory 응용 프로그램 프록시 응용 프로그램의 링크가 올바르게 작동하지 않는 문제를 해결하는 데 도움이 됩니다.

## <a name="overview"></a>개요 
응용 프로그램 프록시 앱을 게시한 후에 응용 프로그램에서 기본적으로 작동하는 유일한 링크는 게시된 루트 URL 내에 포함된 대상에 대한 링크입니다. 응용 프로그램 내에 있는 링크가 작동하지 않으면 응용 프로그램의 내부 URL은 아마도 응용 프로그램 내에서 링크의 모든 대상을 포함하지 않습니다.

**이런 문제가 발생하는 이유는 무엇인가요?** 응용 프로그램에서 링크를 클릭하면 응용 프로그램 프록시는 동일한 응용 프로그램 내에서 내부 URL 또는 외부에서 사용 가능한 URL을 확인하려고 합니다. 링크가 동일한 응용 프로그램에 속하지 않는 내부 URL을 가리키는 경우 이러한 버킷 중 하나에 속하지 않고 찾을 수 없다는 오류가 발생합니다.

## <a name="ways-you-can-resolve-broken-links"></a>끊어진 링크 문제를 해결할 수 있는 방법

이 문제를 해결하는 방법은 세 가지가 있습니다. 아래 문제는 복잡성이 높은 순으로 나열됩니다.

1.  내부 URL이 응용 프로그램의 모든 관련 링크를 포함하는 루트인지 확인합니다. 이렇게 하면 모든 링크를 동일한 응용 프로그램 내에 게시된 콘텐츠로 해결할 수 있습니다.

    내부 URL을 변경하지만 사용자의 방문 페이지를 변경하지 않으려는 경우 홈 페이지 URL을 이전에 게시된 내부 URL로 변경합니다. 이를 위해 "Azure Active Directory" -&gt; 앱 등록 -&gt; 응용 프로그램 선택 -&gt; 속성으로 이동하면 됩니다. 이 속성 탭에서 원하는 방문 페이지로 조정할 수 있는 "홈페이지 URL" 필드가 표시됩니다.

2.  응용 프로그램에서 FQDN(정규화된 도메인 이름)을 사용하는 경우 [사용자 지정 도메인](manage-apps/application-proxy-configure-custom-domain.md)을 사용하여 응용 프로그램을 게시할 수 있습니다. 이 기능을 사용하면 동일한 URL을 내부 및 외부에서 모두 사용할 수 있습니다.

    내부 URL에 대한 응용 프로그램 내의 링크도 외부에서 인식하므로 이 옵션은 응용 프로그램의 링크가 응용 프로그램 프록시를 통해 외부에서 액세스될 수 있도록 합니다. 모든 링크는 아직 게시된 응용 프로그램에 속해야 합니다. 그러나 이 옵션을 사용하면 링크가 동일한 응용 프로그램에 속하지 않고 여러 응용 프로그램에 속할 수 있습니다.

3.  이러한 옵션을 모두 실행할 수 없는 경우 URL 변환/다시 작성을 수행하는 새 기능을 미리 볼 수 있습니다. 이 기능을 사용하면 응용 프로그램의 HTML 본문에 있는 내부 URL 또는 링크가 게시된 외부 앱 프록시 URL로 변환되거나 "매핑"됩니다. 이 변환은 HTML 또는 CSS의 링크에서만 작동하고, 링크가 JS를 통해 생성되는 경우 도움이 되지 않습니다. 

결과적으로 가능하면 [사용자 지정 도메인](manage-apps/application-proxy-configure-custom-domain.md) 솔루션을 사용하는 것이 좋습니다. 미리 보기에 조인하지 않으려는 경우 applicationId를 사용하여 <aadapfeedback@microsoft.com>을 전자 메일로 보냅니다.

## <a name="next-steps"></a>다음 단계
[기존 온-프레미스 프록시 서버 작업](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

