---
title: 응용 프로그램 프록시 응용 프로그램을 구성하는 방법 | Microsoft 문서
description: 몇 가지 간단한 단계로 응용 프로그램 프록시 응용 프로그램을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 299d3df1c9ae82bb9f184e9ffb1dd922013e5e65
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-configure-an-application-proxy-application"></a>응용 프로그램 프록시 응용 프로그램을 구성하는 방법

이 문서에서는 온-프레미스 응용 프로그램을 클라우드에 노출하도록 Azure AD 내의 응용 프로그램 프록시 응용 프로그램을 구성하는 방법에 대해 설명합니다.

## <a name="recommended-documents"></a>권장되는 문서 

관리 포털을 통한 응용 프로그램 프록시 응용 프로그램의 초기 구성 및 생성에 대해 배우려면 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](manage-apps/application-proxy-publish-azure-portal.md)를 따릅니다.

커넥터를 구성하는 방법에 대한 자세한 내용은 [Azure Portal에서 응용 프로그램 프록시 사용](manage-apps/application-proxy-enable.md)을 참조하세요.

인증서 업로드 및 사용자 지정 도메인 사용에 대한 자세한 내용은 [Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 작업](manage-apps/application-proxy-configure-custom-domain.md)을 참조하세요.

## <a name="create-the-applicationsetting-the-urls"></a>응용 프로그램 만들기/URL 설정

[Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](manage-apps/application-proxy-publish-azure-portal.md) 문서의 단계에 따라 응용 프로그램을 만드는 중 오류가 발생한 경우 오류 세부 정보에서 응용 프로그램 문제 해결 방법에 대한 정보 및 제안 사항을 참조하세요. 대부분의 오류 메시지에는 제안 수정이 포함되어 있습니다. 일반적인 오류를 방지하려면 다음을 확인합니다.

-   응용 프로그램 프록시 응용 프로그램을 만들 수 있는 권한을 가진 관리자여야 합니다.

-   내부 URL이 고유해야 합니다.

-   외부 URL이 고유해야 합니다.

-   URL이 http 또는 https로 시작하고 "/"로 끝나야 합니다.

-   URL은 IP 주소가 아닌 도메인 이름이어야 합니다.

응용 프로그램을 만들 때 오류 메시지가 오른쪽 상단 모서리에 표시됩니다. 알림 아이콘을 선택하여 오류 메시지를 볼 수도 있습니다.

   ![알림 프롬프트](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>커넥터/커넥터 그룹 구성

커넥터 및 커넥터 그룹에 대한 경고로 인해 응용 프로그램을 구성하는 데 어려움이 있는 경우 응용 프로그램 프록시 사용에 대한 지침에서 커넥터 다운로드 방법에 대한 자세한 내용을 참조하세요. 커넥터에 대한 자세한 내용은 [커넥터 설명서](manage-apps/application-proxy-connectors.md)를 참조하세요.

커넥터가 비활성 상태이면 서비스에 연결할 수 없음을 의미합니다. 필요한 모든 포트가 열려 있지 않은 경우 이러한 오류가 종종 발생합니다. 필요한 포트 목록을 보려면 응용 프로그램 프록시 사용 설명서의 필수 조건 섹션을 참조하세요.

## <a name="upload-certificates-for-custom-domains"></a>사용자 지정 도메인에 대한 인증서 업로드

사용자 지정 도메인을 사용하면 외부 URL의 도메인을 지정할 수 있습니다. 사용자 지정 도메인을 사용하려면 해당 도메인의 인증서를 업로드해야 합니다. 사용자 지정 도메인 및 인증서 사용에 대한 자세한 내용은 [Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 작업](manage-apps/application-proxy-configure-custom-domain.md)을 참조하세요. 

인증서를 업로드하는 데 문제가 발생하는 경우 포털의 오류 메시지에서 인증서 문제에 대한 추가 정보를 참조하세요. 인증서의 일반적인 문제는 다음과 같습니다.

-   만료된 인증서

-   자체 서명된 인증서

-   개인 키가 없는 인증서

인증서를 업로드하려고 할 때 오류 메시지가 오른쪽 상단 모서리에 표시됩니다. 알림 아이콘을 선택하여 오류 메시지를 볼 수도 있습니다.

   ![알림 프롬프트](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>다음 단계
[Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](manage-apps/application-proxy-publish-azure-portal.md)
