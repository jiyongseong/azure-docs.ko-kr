---
title: 응용 프로그램 프록시에 대한 Kerberos 제한 위임 구성 문제 해결 | Microsoft Docs
description: 응용 프로그램 프록시에 대한 Kerberos 제한 위임 구성 문제 해결
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: harshja
ms.openlocfilehash: 3ba089123198631c443a759ad62cb0ae5ca40ad3
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068271"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>응용 프로그램 프록시에 대한 Kerberos 제한 위임 구성 문제 해결

게시된 응용 프로그램에 SSO를 구축하는 데 사용할 수 있는 방법은 응용 프로그램마다 조금씩 달라질 수 있습니다. Azure 응용 프로그램 프록시에서 기본적으로 제공하는 옵션 중 하나는 KCD(Kerberos 제한된 위임)입니다. 사용자 대신 백 엔드 응용 프로그램에 대해 제한된 Kerberos 인증을 수행하도록 커넥터를 구성할 수 있습니다.

KCD를 사용하기 위한 실제 절차는 비교적 간단하며 일반적으로 SSO를 용이하게 하는 다양한 구성 요소와 인증 흐름을 이해하고 있어야 합니다. KCD SSO가 예상대로 작동하지 않는 시나리오의 문제를 해결하기 위한 유용한 정보 소스를 찾는 방법은 어려울 수 있습니다.

따라서 이 문서에서는 가장 일반적인 문제 중 일부를 해결하고 자체 수정하는 데 도움이 되는 단일 참조 지점을 제공하려고 합니다. 그와 동시에, 보다 복잡하고 문제가 있는 구현을 진단하기 위한 추가 지침을 제공합니다.

이 문서에서는 다음과 같이 가정합니다.

-   Azure 응용 프로그램 프록시가 [설명서](manage-apps/application-proxy-enable.md)에 따라 배포되었으며 비 KCD 응용 프로그램에 대한 일반 액세스가 예상대로 작동합니다.

-   게시된 대상 응용 프로그램은 IIS 및 Microsoft의 Kerberos 구현을 기반으로 합니다.

-   서버 및 응용 프로그램 호스트는 단일 Active Directory 도메인에 상주합니다. 크로스 도메인 및 포리스트 시나리오에 대한 자세한 내용은 [KCD 백서](https://aka.ms/KCDPaper)에서 확인할 수 있습니다.

-   주체 응용 프로그램은 사전 인증을 사용하는 Azure 테넌트에 게시되며 사용자는 양식 기반 인증을 통해 Azure에 인증해야 합니다. 리치 클라이언트 인증 시나리오는 이 문서에서 다루지 않지만 향후 추가될 예정입니다.

## <a name="prerequisites"></a>필수 조건

Azure 응용 프로그램 프록시는 다양한 종류의 인프라 또는 환경에 배포할 수 있으며 아키텍처는 조직마다 다를 수 있습니다. KCD 관련 문제의 가장 일반적인 원인 중 하나는 환경 자체가 아니라 단순한 잘못된 구성 또는 일반적인 감독입니다.

따라서 언제나 문제 해결을 시작하기 전에 [KCD SSO를 응용 프로그램 프록시와 함께 사용 문서](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)에 설명된 모든 필수 조건을 충족하는지 확인하는 것이 좋습니다.

특히 이전 버전의 Windows에서 KCD를 구성할 때는 근본적으로 다른 접근 방식을 사용하기 때문에 2012R2에서 KCD를 구성하는 섹션뿐 아니라 다음과 같은 여러 가지 고려 사항을 염두에 두어야 합니다.

-   도메인 구성원 서버가 특정 도메인 컨트롤러가 있는 보안 채널 대화 상자를 여는 것은 흔한 일입니다. 그런 다음 언제든지 다른 대화 상자로 이동되므로 커넥터 호스트를 특정 로컬 사이트 DC와만 통신할 수 있도록 제한해서는 안 됩니다.

-   위와 마찬가지로 크로스 도메인 시나리오는 커넥터 호스트를 로컬 네트워크 경계 외부에 있는 DC로 연결하는 참조를 사용합니다. 이 시나리오에서는 다른 각 도메인을 나타내는 DC에 트래픽을 허용하거나 위임이 실패하는지 확인하는 것도 중요합니다.

-   가능한 경우에는 커넥터 호스트와 DC 사이에 활성 IPS/IDS 장치를 배치해서는 안 됩니다. 이러한 장치가 코어 RPC 트래픽을 방해할 수 있습니다.

가장 간단한 시나리오에서 위임을 테스트해야 합니다. 변수가 많을수록 더 많이 고민해야 할 수 있습니다. 예를 들어 테스트를 단일 커넥터로 제한하면 귀중한 시간을 절약할 수 있으며 문제가 해결된 후에 다른 커넥터를 추가할 수 있습니다.

일부 환경 요소가 문제를 일으킬 수도 있습니다. 이러한 환경 요소를 방지할 수 있도록 테스트 중에는 아키텍처를 최소화하는 것이 좋습니다. 예를 들어 내부 방화벽 ACL이 잘못 구성되는 것은 드문 일이 아니므로 가능하면 커넥터의 모든 트래픽이 DC 및 백 엔드 응용 프로그램을 통해 직접 허용될 수 있도록 설정합니다. 

실제로 커넥터를 배치하는 가장 좋은 장소는 가능한 한 대상과 가까운 위치입니다. 테스트가 진행되는 동안 방화벽을 인라인으로 배치하면 쓸데없이 복잡해져 검사가 지연될 수 있습니다.

KCD 문제가 되는 요소는 무엇인가요? KCD SSO 문제를 나타내는 몇 가지 일반적인 징후가 있으며 문제의 첫 번째 징후는 일반적으로 브라우저의 매니페스트 자체입니다.

   ![잘못된 KCD 구성 오류](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![누락된 권한으로 인해 권한 부여 실패](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

모두 SSO를 수행하지 못하는 동일한 증상을 나타내며 결과적으로 응용 프로그램에 대한 사용자 액세스가 거부됩니다.

## <a name="troubleshooting"></a>문제 해결

문제를 해결하는 방법은 문제와 관찰된 증상에 따라 달라집니다. 계속 진행하기 전에 유용한 정보가 설명되어 있는 다음 링크를 살펴보세요.

-   [응용 프로그램 프록시 문제 및 오류 메시지 문제 해결](active-directory-application-proxy-troubleshoot.md)

-   [Kerberos 오류 및 증상](active-directory-application-proxy-troubleshoot.md#kerberos-errors)

-   [온-프레미스 및 클라우드 ID가 동일하지 않은 경우 SSO를 사용하여 작업](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

여기까지 읽어 보면 주요 문제를 찾을 수 있을 것입니다. 먼저 문제 해결이 가능하도록 흐름을 세 단계로 구분합니다.

**클라이언트 사전 인증** - 외부 사용자가 브라우저를 통해 Azure에 대해 인증합니다.

KCD SSO가 작동하려면 Azure에 사전 인증할 수 있어야 합니다. 테스트하고, 문제가 발생하면 해결해야 합니다. 사전 인증 단계는 KCD 또는 게시된 응용 프로그램과 관련이 없습니다. Azure에 존재하는 주체 계정의 온전성과 비활성/차단되지 않았는지 검사하여 불일치를 수정하는 것은 매우 쉽습니다. 브라우저의 오류 응답은 대개 원인을 이해하기에 충분합니다. 확실하지 않은 경우 다른 문제 해결 문서를 확인할 수도 있습니다.

**위임 서비스** - Azure 프록시 커넥터는 사용자를 대신하여 KDC(Kerberos Distribution Center)에서 Kerberos 서비스 티켓을 얻습니다.

클라이언트와 Azure 프론트 엔드 간의 외부 통신은 작동하는지 확인하는 것 이외에는 KCD에 아무런 영향을 미치지 않아야 합니다. 이는 Kerberos 티켓을 얻는 데 사용되는 유효한 사용자 ID로 Azure 프록시 서비스를 제공할 수 있기 때문입니다. 이 KCD가 없으면 불가능하며 실패할 것입니다.

앞에서 언급했듯이 브라우저 오류 메시지는 일반적으로 실패하는 이유에 대한 단서를 제공합니다. Azure 프록시 이벤트 로그의 실제 이벤트와 동작을 연관시킬 수 있도록 응답의 작업 ID와 타임스탬프를 적어둡니다.

   ![잘못된 KCD 구성 오류](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

이벤트 로그에 표시된 해당 항목은 이벤트 13019 또는 12027로 표시됩니다. 커넥터 이벤트 로그는 **응용 프로그램 및 서비스 로그** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector**&gt;**Admin**에서 찾을 수 있습니다.

   ![응용 프로그램 프록시 이벤트 로그의 이벤트 13019](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![응용 프로그램 프록시 이벤트 로그의 이벤트 12027](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   응용 프로그램의 주소에 대해 내부 DNS의 A 레코드를 사용하고 CName은 사용하지 마세요.

-   커넥터 호스트에 지정된 대상 계정의 SPN에 위임할 수 있는 권한이 부여되었으며 **모든 인증 프로토콜 사용**이 선택되었는지 다시 확인합니다. 이 항목에 대한 자세한 내용은 [SSO 구성 문서](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)를 참조하세요.

-   도메인 구성원 호스트의 명령 프롬프트에서 `setspn -x`를 실행하여 AD에 SPN 인스턴스가 하나만 존재하는지 확인합니다.

-   도메인 정책에서 [발행된 Kerberos 토큰의 최대 크기](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/)를 제한하는지 확인합니다. 이렇게 하면 토큰 크기가 최대 크기를 초과하는 경우 커넥터에서 토큰을 가져올 수 없습니다.

문제에 대한 보다 구체적인 정보를 얻기 위한 차선책은 커넥터 호스트와 도메인 KDC 간의 교환을 캡처하는 네트워크 추적입니다. 자세한 내용은 [심층 분석 문제 해결 문서](https://aka.ms/proxytshootpaper)를 참조하세요.

티켓팅이 잘되면 응용 프로그램이 401을 반환하여 인증에 실패했음을 알리는 이벤트가 로그에 표시되어 있을 것입니다. 이는 일반적으로 대상 응용 프로그램이 티켓을 거부했음을 나타내므로 다음 단계를 진행합니다.

**대상 응용 프로그램** - 커넥터가 제공한 Kerberos 티켓의 소비자

이 단계에서 커넥터는 Kerberos 서비스 티켓을 첫 번째 응용 프로그램 요청의 헤더인 백 엔드에 보내야 합니다.

-   포털에 정의된 응용 프로그램의 내부 URL을 사용하여 커넥터 호스트의 브라우저에서 응용 프로그램에 직접 액세스할 수 있는지 확인합니다. 그런 다음 성공적으로 로그인할 수 있습니다. 이 작업에 대한 자세한 내용은 커넥터 문제 해결 페이지에서 확인할 수 있습니다.

-   커넥터 호스트에서 다음 중 하나를 수행하여 브라우저와 응용 프로그램 사이의 인증이 Kerberos를 사용하는지 확인합니다.

1.  Internet Explorer에서 Dev 도구(**F12**)를 실행하거나 커넥터 호스트에서 [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/)를 사용합니다. 내부 URL을 사용하여 응용 프로그램으로 이동한 다음 응용 프로그램의 응답에 반환된 WWW 인증 헤더를 검사하여 협상 또는 Kerberos가 있는지 확인합니다. 브라우저에서 응용 프로그램으로의 응답에 포함된 후속 Kerberos Blob은 일반적으로 **YII**로 시작하므로 이를 통해 Kerberos가 작동 중임을 알 수 있습니다. 반면에 NTLM은 항상 **TlRMTVNTUAAB**로 시작합니다. 이는 Base64에서 디코딩되면 NTLMSSP가 됩니다. Blob 시작 부분에 **TlRMTVNTUAAB**가 표시되면 이는 Kerberos를 **사용할 수 없다**는 의미입니다. TlRMTVNTUAAB가 표시되지 않으면 Kerberos를 사용할 수 있습니다.
    > [!NOTE]
    > Fiddler를 사용하는 경우 이 방법을 사용하려면 IIS에서 응용 프로그램의 구성에 대한 확장된 보호를 일시적으로 해제해야 합니다.

     ![브라우저 네트워크 검사 창](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *그림:* TIRMTVNTUAAB로 시작하지 않기 때문에 Kerberos를 사용할 수 있습니다. 이는 YII로 시작하지 않는 Kerberos Blob의 예입니다.

2.  일시적으로 IIS 사이트의 공급자 목록에서 NTLM을 제거하고 커넥터 호스트의 IE에서 직접 앱에 액세스합니다. NTLM이 더 이상 공급자 목록에 없으면 Kerberos만 사용하여 응용 프로그램에 액세스할 수 있어야 합니다. 실패하면 응용 프로그램의 구성에 문제가 있음을 나타내며 Kerberos 인증이 작동하지 않습니다.

Kerberos를 사용할 수 없는 경우 IIS에서 응용 프로그램의 인증 설정을 확인하여 Negotiate가 최상위에 나열되고 NTLM이 바로 아래에 있는지 확인합니다. (협상 안 함: Kerberos 또는 협상: PKU2U). Kerberos가 작동하는 경우에만 계속 진행합니다.

   ![Windows 인증 공급자](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Kerberos 및 NTLM을 사용하면 포털에서 응용 프로그램의 사전 인증을 일시적으로 비활성화할 수 있습니다. 외부 URL을 사용하여 인터넷에서 액세스할 수 있는지 확인합니다. 이전 단계에서 사용한 동일한 계정으로 인증하라는 메시지가 나타나야 합니다. 그렇지 않은 경우 백 엔드 응용 프로그램에 문제가 있으며 KCD가 아니라는 것을 나타냅니다.

-   이제 포털에서 사전 인증을 다시 활성화하고 외부 URL을 통해 응용 프로그램에 연결을 시도하여 Azure를 통해 인증합니다. SSO가 실패하면 브라우저에 사용할 수 없음 오류 메시지와 함께 로그에 이벤트 13022가 표시됩니다.

    *백 엔드 서버가 HTTP 401 오류와 함께 Kerberos 인증 시도에 응답하기 때문에 Microsoft AAD Application Proxy Connector에서 사용자를 인증할 수 없습니다.*

    ![HTTTP 401 사용할 수 없음 오류](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   다음 그림과 같이 IIS를 탐색하여 AD에서 SPN이 구성된 것과 동일한 계정을 사용하도록 구성된 응용 프로그램 풀이 구성되어 있는지 확인하려면 IIS 응용 프로그램을 확인합니다.

    ![IIS 응용 프로그램 구성 창](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    ID를 알고 있으면 명령 프롬프트에서 다음을 실행하여 이 계정이 해당 SPN으로 구성되어 있는지 확인합니다. 예: `setspn –q http/spn.wacketywack.com`

    ![SetSPN 명령 창](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   포털에서 응용 프로그램의 설정에 대해 정의된 SPN이 응용 프로그램의 앱 풀에서 사용 중인 대상 AD 계정에 대해 구성된 것과 동일한 SPN인지 확인합니다.

   ![Azure Portal의 SPN 구성](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   IIS로 이동하여 응용 프로그램에 대한 **구성 편집기** 옵션을 선택하고 **system.webServer/security/authentication/windowsAuthentication**으로 이동하여 **UseAppPoolCredentials** 값이 **True**로 설정되어 있는지 확인합니다.

   ![IIS 구성 앱 풀 자격 증명 옵션](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

이 값을 **True**로 변경한 후 캐시된 모든 Kerberos 티켓을 백엔드 서버에서 제거해야 합니다. 다음 명령을 실행하여 이를 수행할 수 있습니다.

```powershell
Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
``` 

자세한 내용은 [모든 세션에 대해 Kerberos 클라이언트 티켓 캐시 제거](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf)를 참조하세요.



커널 모드를 사용하면 Kerberos 작업의 성능을 향상시킬 수 있지만 요청한 서비스의 티켓이 컴퓨터 계정을 사용하여 해독됩니다. 이를 로컬 시스템이라고도 합니다. 따라서 이 설정을 true로 설정하면 팜의 여러 서버에서 응용 프로그램을 호스트할 때 KCD가 손상됩니다.

-   추가 검사 때문에 **확장** 보호를 해제할 수도 있습니다. 응용 프로그램이 기본 웹 사이트의 하위 폴더로 게시되는 특정한 구성에서 이 기능을 사용하도록 설정한 경우 KCD를 손상시키는 것으로 판명된 시나리오가 있었습니다. 이 자체는 익명 인증용으로만 구성되며 전체 대화 상자가 회색으로 표시되어 자식 개체가 활성 설정을 상속받지 않음을 나타냅니다. 그러나 가능한 경우 항상 이 기능을 사용하도록 설정하는 것이 좋습니다. 따라서 테스트 후 복원하는 것을 잊지 마세요.

이러한 추가 검사를 통해 게시된 응용 프로그램 사용 시작을 추적할 수 있습니다. 위임하도록 구성된 다른 커넥터를 계속 스핀업할 수 있지만 더 이상 없으면 심층 기술 연습 [The complete guide for Troubleshoot Azure AD Application Proxy](https://aka.ms/proxytshootpaper)(Azure AD 응용 프로그램 프록시 문제 해결에 대한 완벽한 가이드)를 읽어 보는 것이 좋습니다.

그래도 문제를 해결할 수 없는 경우 여기를 통해 지원팀에 문의해 주세요. 포털에서 직접 지원 티켓을 작성하면 엔지니어가 연락을 드릴 것입니다.

## <a name="other-scenarios"></a>기타 시나리오

-   Azure 응용 프로그램 프록시는 응용 프로그램에 요청을 보내기 전에 Kerberos 티켓을 요청합니다. Tableau Server와 같은 일부 타사 응용 프로그램은 이 인증 방법 대신 더 기본적인 협상이 이루어지는 것을 기대합니다. 첫 번째 요청은 익명으로 처리되므로 응용 프로그램이 401을 통해 지원하는 인증 유형으로 응답할 수 있습니다.

-   이중 홉 인증 - 일반적으로 SQL Reporting Services와 같이 인증이 필요한 백 엔드 및 프런트 엔드를 사용하여 응용 프로그램이 계층화된 시나리오에 사용됩니다.

## <a name="next-steps"></a>다음 단계
[관리되는 도메인에서 KCD(Kerberos 제한 위임) 구성](../active-directory-domain-services/active-directory-ds-enable-kcd.md)
