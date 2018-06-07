---
title: 응용 프로그램 프록시 문제 해결 | Microsoft Docs
description: Azure AD 응용 프로그램 프록시에서 오류를 해결하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 838bdccb06e5763d33f63208cb6f941a55778b32
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155816"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>응용 프로그램 프록시 문제 및 오류 메시지 문제 해결
게시된 응용 프로그램에 액세스할 때나 응용 프로그램을 게시할 때 오류가 발생한다면 다음 옵션을 확인하여 Microsoft Azure AD 응용 프로그램 프록시가 올바르게 작동하는지 확인합니다.

* Windows 서비스 콘솔을 열고 **Microsoft AAD 응용 프로그램 프록시 커넥터** 서비스가 활성화되어 있고 실행 중인지 확인합니다. 또한 다음 그림에 표시된 것처럼 응용 프로그램 프록시 서비스 속성 페이지에서 확인할 수도 있습니다.  
  ![Microsoft AAD 응용 프로그램 프록시 커넥터 속성 창 스크린샷](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)
* 이벤트 뷰어를 열고 **응용 프로그램 및 서비스 로그** > **Microsoft** > **AadApplicationProxy** > **커넥터** > **관리**에서 응용 프로그램 프록시 커넥터 이벤트를 찾습니다.
* 필요한 경우 [응용 프로그램 프록시 커넥터 세션 로그를 켜면](manage-apps/application-proxy-connectors.md#under-the-hood) 더 자세한 로그를 볼 수 있습니다.

Azure AD 문제 해결 도구에 대한 자세한 내용은 [커넥터 네트워킹 필수 조건을 검사하는 문제 해결 도구](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites)를 참조하세요.

## <a name="the-page-is-not-rendered-correctly"></a>페이지가 제대로 렌더링되지 않습니다.
특정 오류 메시지를 수신하지 않고도 응용 프로그램 렌더링 또는 기능이 제대로 이뤄지지 않는 문제가 있을 수 있습니다. 문서 경로를 게시했지만 응용 프로그램에 해당 경로 밖에 있는 콘텐츠가 필요한 경우에 이 문제가 발생할 수 있습니다.

예를 들어, https://yourapp/app 경로를 게시하는 한편 응용 프로그램에서 https://yourapp/media의 이미지를 호출하는 경우 이미지가 렌더링되지 않습니다. 모든 관련 콘텐츠를 포함해야 하는 가장 높은 수준의 경로를 사용하여 응용 프로그램을 게시하는지 확인합니다. 이 예에서는 http://yourapp/입니다.

참조된 콘텐츠를 포함하도록 경로를 변경하지만, 여전히 사용자가 경로에서 더 깊은 링크로 이동해야 하는 경우, [Azure AD 액세스 패널과 Office 365 앱 시작 관리자에서 응용 프로그램 프록시 응용 프로그램에 대한 올바른 링크 설정](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/)블로그 게시물을 참조하세요.

## <a name="connector-errors"></a>커넥터 오류

[Azure AD 응용 프로그램 프록시 커넥터 포트 테스트 도구](https://aadap-portcheck.connectorporttest.msappproxy.net/)를 사용하여 커넥터가 응용 프로그램 프록시 서비스에 연결할 수 있는지 확인합니다. 최소한 미국 중부 하위 지역 및 사용자에게 가까운 지역에는 모두 녹색 확인 표시가 있는지 확인합니다. 그 외의 항목에도 녹색 확인 표시가 있으면 복원력이 더 뛰어난 것입니다. 

커넥터 마법사를 설치하는 동안 등록에 실패하는 경우 실패한 이유를 확인하는 두 가지 방법이 있습니다. **Applications and Services Logs\Microsoft\AadApplicationProxy\Connector\Admin**에서 이벤트 로그를 확인하거나 다음 Windows PowerShell 명령을 실행합니다.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

이벤트 로그에서 커넥터 오류를 찾으면 일반적인 오류 테이블을 사용하여 문제를 해결합니다.

| 오류 | 권장되는 단계 |
| ----- | ----------------- |
| 커넥터를 등록하지 못함: Azure 관리 포털에서 응용 프로그램 프록시를 활성화했으며 Active Directory 사용자 이름과 암호를 올바르게 입력했는지 확인합니다. 오류: '하나 이상의 오류가 발생했습니다.' | Azure AD에 로그인하지 않고 등록 창을 닫은 경우 커넥터 마법사를 다시 실행하고 커넥터를 등록합니다. <br><br> 등록 창이 열리고 로그인을 허용하지 않고 즉시 창이 닫히는 경우 이 오류가 나타날 것입니다. 시스템에 네트워킹 오류가 있을 때 이 오류가 발생합니다. 브라우저에서 공용 웹사이트에 연결할 수 있으며 포트가 [응용 프로그램 프록시 사전 요구 사항](manage-apps/application-proxy-enable.md)에 지정된대로 열려 있는지 확인합니다. |
| 등록 창에 명확한 오류가 표시됩니다. 설치를 진행할 수 없습니다. | 이 오류가 표시되고 창이 닫힌 경우 잘못된 사용자 이름 또는 암호를 입력했습니다. 다시 시도하세요. |
| 커넥터를 등록하지 못함: Azure 관리 포털에서 응용 프로그램 프록시를 활성화했으며 Active Directory 사용자 이름과 암호를 올바르게 입력했는지 확인합니다. 오류: 'AADSTS50059: 테넌트를 식별하는 정보가 요청에서 찾을 수 없거나 제공된 자격 증명으로 암시되지 않으며, 서비스 주체 URI에 의한 검색이 실패했습니다. | 액세스하고자 하는 디렉터리의 조직 ID 일부인 도메인이 아닌 Microsoft 계정을 사용하여 로그인을 시도하고 있습니다. 관리자가 테넌트 도메인과 동일한 도메인 이름의 일부인지 확인하세요. 예를 들어, Azure AD 도메인이 contoso.com이라면 관리자는 admin@contoso.com여야 합니다. |
| PowerShell 스크립트의 실행을 위한 현재 실행 정책을 검색하지 못했습니다. | 커넥터 설치에 실패한다면 PowerShell 실행 정책이 비활성화되어 있지 않은지 확인하세요. <br><br>1. 그룹 정책 편집기를 엽니다.<br>2. **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소** > **Windows PowerShell**로 이동한 다음 **스크립트 실행 켜기**를 두 번 클릭합니다.<br>3. 실행 정책은 **구성 안 함** 또는 **사용**으로 설정될 수 있습니다. **사용**으로 설정했다면 옵션에 있는 실행 정책을 **로컬 스크립트 및 원격 서명된 스크립트 허용** 또는 **모든 스크립트 허용**으로 설정했는지 확인합니다. |
| 커넥터에서 구성을 다운로드하지 못했습니다. | 인증에 사용되는 커넥터의 클라이언트 인증서가 만료되었습니다. 이것은 커넥터가 프록시 뒤에 설치되어 있는 경우 발생할 수도 있습니다. 이 경우 커넥터는 인터넷에 액세스할 수 없으며 원격 사용자에게 응용 프로그램을 제공할 수 없게 됩니다. `Register-AppProxyConnector` Windows PowerShell에서 cmdlet을 사용하여 트러스트를 수동으로 갱신합니다. 커넥터가 프록시 뒤에 있는 경우 커넥터 계정 "네트워크 서비스" 및 "로컬 시스템"에 인터넷 액세스 권한을 부여해야 합니다. 이것은 프록시에 대한 액세스 권한을 부여하거나 프록시를 우회하도록 설정하여 수행할 수 있습니다. |
| 커넥터를 등록하지 못함: 커넥터를 등록하려면 Active Directory의 전역 관리자여야 합니다. 오류: '등록 요청이 거부되었습니다.' | 로그인하고자 하는 별칭이 이 도메인에서 관리자가 아닙니다. 커넥터는 항상 사용자의 도메인을 소유하는 디렉터리에 대해 설치됩니다. 로그인하고자 하는 관리자 계정이 Azure AD 테넌트에 대한 전역 사용 권한을 가지고 있는지 확인합니다. |

## <a name="kerberos-errors"></a>Kerberos 오류

이 테이블은 Kerberos 설정 및 구성에서 발생한 일반적인 오류에 대해 설명하고 해결하기 위한 제안을 포함합니다.

| 오류 | 권장되는 단계 |
| ----- | ----------------- |
| PowerShell 스크립트의 실행을 위한 현재 실행 정책을 검색하지 못했습니다. | 커넥터 설치에 실패한다면 PowerShell 실행 정책이 비활성화되어 있지 않은지 확인하세요.<br><br>1. 그룹 정책 편집기를 엽니다.<br>2. **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소** > **Windows PowerShell**로 이동한 다음 **스크립트 실행 켜기**를 두 번 클릭합니다.<br>3. 실행 정책은 **구성 안 함** 또는 **사용**으로 설정될 수 있습니다. **사용**으로 설정했다면 옵션에 있는 실행 정책을 **로컬 스크립트 및 원격 서명된 스크립트 허용** 또는 **모든 스크립트 허용**으로 설정했는지 확인합니다. |
| 12008-Azure AD가 백 엔드 서버에 허용되는 Kerberos 인증 시도의 최대 횟수를 초과합니다. | 이 오류는 Azure AD와 백 엔드 응용 프로그램 서버 간의 잘못된 구성 또는 두 컴퓨터 모두에서 날짜 및 시간 구성에 문제가 있음을 나타냅니다. 백 엔드 서버가 Azure AD에서 생성한 Kerberos 티켓을 거부했습니다. Azure AD 및 백 엔드 응용 프로그램 서버가 올바르게 구성되어 있는지 확인합니다. Azure AD 및 백 엔드 응용 프로그램 서버상의 시간 및 날짜 구성이 동기화되었는지 확인합니다. |
| 13016 - 에지 토큰이나 액세스 토큰에 UPN이 없기 때문에 Azure AD에서 사용자를 대신해서 Kerberos 티켓을 검색할 수 없습니다. | STS 구성에 문제가 있습니다. STS에서 UPN 클레임 구성을 수정합니다. |
| 13019 - 다음과 같은 일반적인 API 오류로 인해 Azure AD에서 사용자를 대신해서 Kerberos 티켓을 검색할 수 없습니다. | 이 이벤트는 Azure AD와 도메인 컨트롤러 서버 간의 잘못된 구성 또는 두 컴퓨터 모두에서 날짜 및 시간 구성에 문제가 있음을 나타냅니다. 도메인 컨트롤러가 Azure AD에서 생성한 Kerberos 티켓을 거부했습니다. Azure AD 및 백 엔드 응용 프로그램 서버, 특히 SPN 구성이 올바르게 구성되어 있는지 확인합니다. Azure AD가 도메인 컨트롤러와 동일한 도메인에 도메인 가입되어 있는지 확인하여 도메인 컨트롤러가 Azure AD와 신뢰를 구축할 수 있게 하세요. Azure AD 및 도메인 컨트롤러상의 시간 및 날짜 구성이 동기화되었는지 확인합니다. |
| 13020 - 백 엔드 서버 SPN이 정의되어 있지 않기 때문에 Azure AD에서 사용자를 대신해서 Kerberos 티켓을 검색할 수 없습니다. | 이 이벤트는 Azure AD와 도메인 컨트롤러 서버 간의 잘못된 구성 또는 두 컴퓨터 모두에서 날짜 및 시간 구성에 문제가 있음을 나타냅니다. 도메인 컨트롤러가 Azure AD에서 생성한 Kerberos 티켓을 거부했습니다. Azure AD 및 백 엔드 응용 프로그램 서버, 특히 SPN 구성이 올바르게 구성되어 있는지 확인합니다. Azure AD가 도메인 컨트롤러와 동일한 도메인에 도메인 가입되어 있는지 확인하여 도메인 컨트롤러가 Azure AD와 신뢰를 구축할 수 있게 하세요. Azure AD 및 도메인 컨트롤러상의 시간 및 날짜 구성이 동기화되었는지 확인합니다. |
| 13022 - 백 엔드 서버가 HTTP 401 오류와 함께 Kerberos 인증 시도에 응답하기 때문에 Azure AD에서 사용자를 인증할 수 없습니다. | 이 이벤트는 Azure AD와 백 엔드 응용 프로그램 서버 간의 잘못된 구성 또는 두 컴퓨터 모두에서 날짜 및 시간 구성에 문제가 있음을 나타냅니다. 백 엔드 서버가 Azure AD에서 생성한 Kerberos 티켓을 거부했습니다. Azure AD 및 백 엔드 응용 프로그램 서버가 올바르게 구성되어 있는지 확인합니다. Azure AD 및 백 엔드 응용 프로그램 서버상의 시간 및 날짜 구성이 동기화되었는지 확인합니다. 자세한 내용은 [응용 프로그램 프록시에 대한 Kerberos 제한 위임 구성 문제 해결](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)을 참조하세요.  |

## <a name="end-user-errors"></a>최종 사용자 오류

이 목록에서는 최종 사용자가 앱에 액세스하는 데 실패한 경우 발생할 수 있는 오류에 대해 설명합니다. 

| 오류 | 권장되는 단계 |
| ----- | ----------------- |
| 웹 사이트에서 페이지를 표시할 수 없습니다. | 응용 프로그램이 IWA 응용 프로그램인 경우 게시된 앱에 사용자가 액세스를 시도하는 경우 이 오류가 발생할 수 있습니다. 이 응용 프로그램에 대해 정의된 SPN이 올바르지 않을 수 있습니다. IWA 앱의 경우 이 응용 프로그램에 대해 구성된 SPN이 올바른지 확인합니다. |
| 웹 사이트에서 페이지를 표시할 수 없습니다. | 응용 프로그램이 OWA 응용 프로그램인 경우 게시된 앱에 사용자가 액세스를 시도하는 경우 이 오류가 발생할 수 있습니다. 이는 다음 중 하나 때문일 수 있습니다.<br><li>이 응용 프로그램에 대해 정의된 SPN이 올바르지 않습니다. 이 응용 프로그램에 대해 구성된 SPN이 올바른지 확인합니다.</li><li>이 응용 프로그램에 액세스하려고 하는 사용자가 적절한 기업 계정이 아닌 Microsoft 계정을 사용하거나 사용자가 게스트 사용자입니다. 사용자가 게시된 응용 프로그램의 도메인과 일치하는 기업 계정을 사용하여 로그인하는지 확인합니다. Microsoft 계정 사용자 및 게스트가 IWA 응용 프로그램에 액세스할 수 없습니다.</li><li>이 응용 프로그램에 액세스하려고 하는 사용자가 온-프레미스 쪽에서 응용 프로그램에 대해 제대로 정의되지 않았습니다. 이 사용자에게 온-프레미스 컴퓨터에서 이 백 엔드 응용 프로그램에 대해 정의된 대로 적절한 권한이 있는지 확인합니다. |
| 이 회사 앱에 액세스할 수 없습니다. 이 응용 프로그램에 액세스할 수 있는 권한이 없습니다. 권한 부여에 실패했습니다. 이 응용 프로그램에 액세스할 수 있는 사용자를 할당할 수 있는지 확인합니다. | 사용자가 자신의 회사 계정 대신 Microsoft 계정을 로그인에 사용하는 경우 게시된 앱에 액세스하려고 할 때 이 오류가 발생할 수 있습니다. 게스트 사용자에게 이 오류가 발생할 수도 있습니다. Microsoft 계정 사용자 및 게스트가 IWA 응용 프로그램에 액세스할 수 없습니다. 사용자가 게시된 응용 프로그램의 도메인과 일치하는 기업 계정을 사용하여 로그인하는지 확인합니다.<br><br>이 응용 프로그램에 사용자를 할당하지 않았을 수 있습니다. **응용 프로그램** 탭으로 이동하고 **사용자 및 그룹**에서 이 사용자나 사용자 그룹을 이 응용 프로그램에 할당합니다. |
| 현재 이 회사 앱에 액세스할 수 없습니다. 나중에 다시 시도하세요. 커넥터 제한 시간이 초과되었습니다. | 사용자가 온-프레미스 쪽에서 이 응용 프로그램에 대해 적절히 정의되지 않은 경우 게시된 앱에 액세스를 시도할 때 이 오류가 나타날 수 있습니다. 사용자에게 온-프레미스 컴퓨터에서 이 백 엔드 응용 프로그램에 대해 정의된 대로 적절한 권한이 있는지 확인합니다. |
| 이 회사 앱에 액세스할 수 없습니다. 이 응용 프로그램에 액세스할 수 있는 권한이 없습니다. 권한 부여에 실패했습니다. 사용자에게 Azure Active Directory Premium 또는 Basic에 대한 라이선스가 있는지 확인합니다. | 구독자의 관리자가 사용자에게 Premium/Basic 라이선스를 명시적으로 할당하지 않은 경우 사용자가 게시된 앱에 액세스를 시도할 때 이 오류가 발생할 수 있습니다. 구독자의 Active Directory **라이선스** 탭으로 이동하고 이 사용자나 사용자 그룹에 Premium 또는 Basic 라이선스가 할당되어 있는지 확인합니다. |

## <a name="my-error-wasnt-listed-here"></a>내 오류가 여기에 나열되지 않았습니다.

Azure AD 응용 프로그램 프록시에 이 문제 해결 가이드에 나열되지 않은 오류 또는 문제가 발생한 경우 알려주시기 바랍니다. 발생한 오류의 세부 정보를 [피드백 팀](mailto:aadapfeedback@microsoft.com)에게 전자 메일로 보내 주세요.

## <a name="see-also"></a>참고 항목
* [Azure Active Directory에 대한 응용 프로그램 프록시 사용](manage-apps/application-proxy-enable.md)
* [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](manage-apps/application-proxy-publish-azure-portal.md)
* [Single Sign-On 사용](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [조건부 액세스 사용](manage-apps/application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
