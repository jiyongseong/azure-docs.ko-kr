---
title: Azure App Service Environment에서 내부 부하 분산 장치 만들기 및 사용
description: 인터넷에 연결되지 않은 Azure App Service Environment를 만들고 사용하는 방법에 대한 세부 정보
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/20/2018
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 6e09bdc336821720c970f8b8daf13f52b0a69ed0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355375"
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>App Service Environment에서 내부 부하 분산 장치 만들기 및 사용 #

 Azure App Service Environment는 Azure App Service를 Azure VNet(Virtual Network)의 서브넷에 배포한 것입니다. ASE(App Service Environment)에는 두 가지 배포 방법이 있습니다. 

- 외부 ASE라고도 하는 외부 IP 주소의 VIP 사용
- 내부 끝점이 ILB(내부 부하 분산 장치)이기 때문에 ILB ASE라고도 하는 내부 IP 주소의 VIP 사용 

이 문서에서는 ILB ASE를 만드는 방법을 보여 줍니다. ASE의 개요는 [App Service Environment에 대한 소개][Intro]를 참조하세요. 외부 ASE를 만드는 방법을 알아보려면 [외부 ASE 만들기][MakeExternalASE]를 참조하세요.

## <a name="overview"></a>개요 ##

VNet의 인터넷 액세스 가능 끝점 또는 IP 주소를 사용하여 ASE를 배포할 수 있습니다. IP 주소를 VNet 주소로 설정하려면 ASE는 ILB를 사용하여 배포되어야 합니다. ILB를 사용하여 ASE를 배포하는 경우 다음과 같은 항목을 제공해야 합니다.

-   앱을 만들 때 사용하는 고유한 도메인
-   HTTPS에 사용되는 인증서
-   도메인에 대한 DNS 관리

결과적으로 다음과 같은 작업을 수행할 수 있습니다.

-   사이트 간 또는 Azure ExpressRoute VPN을 통해 액세스하는 클라우드에서 인트라넷 응용 프로그램을 안전하게 호스트
-   공용 DNS 서버에 나열되지 않은 클라우드에 앱 호스트
-   프런트 엔드 앱이 안전하게 통합될 수 있는 인터넷 격리 백 엔드 앱 만들기

### <a name="disabled-functionality"></a>사용되지 않도록 설정된 기능 ###

ILB ASE를 사용하는 경우 수행할 수 없는 작업도 있습니다.

-   IP 기반 SSL 사용
-   특정 앱에 IP 주소 할당
-   Azure Portal을 통해 앱에서 인증서 구매 및 사용 직접 인증 기관에서 직접 인증서를 가져와서 앱에서 사용할 수 있습니다. Azure Portal을 통해 가져올 수 없습니다.

## <a name="create-an-ilb-ase"></a>ILB ASE 만들기 ##

ILB ASE를 만들려면

1. Azure Portal에서 **리소스 만들기** > **웹 + 모바일** > **App Service Environment**를 선택합니다.

2. 사용 중인 구독을 선택합니다.

3. 리소스 그룹을 선택하거나 만듭니다.

4. VNet을 선택하거나 만듭니다.

    * 새 VNet을 선택하면 이름 및 위치를 지정할 수 있습니다. 이러한 ASE에서 Linux 앱을 호스트하려는 경우 현재 **미국 서부, 미국 동부, 유럽 서부, 유럽 북부, 오스트레일리아 동부, 동남아시아**의 6개 지역만 지원됩니다. 

5. 기존 VNet을 선택한 경우 ASE를 보유하는 서브넷을 만들어야 합니다. ASE의 향후 증가에 맞게 충분히 큰 서브넷 크기를 설정해야 합니다. `/25` 크기를 사용하는 것이 좋습니다. 그러면 128개의 주소가 있고 최대 크기의 ASE를 다룰 수 있습니다. 선택할 수 있는 최소 크기는 `/28`입니다. 인프라에서 요구하면 이 크기는 최대 3개의 인스턴스로 확장될 수 있습니다.

    * App Service 계획에서 기본 최대 100개를 초과합니다.

    * 더 신속한 프런트 엔드 확장으로 100개 가까이 크기를 조정합니다.

6. **Virtual Network/위치** > **Virtual Network 구성**을 선택합니다. **VIP 형식**을 **내부**로 설정합니다.

7. 도메인 이름을 입력합니다. 이 도메인은 이 ASE에서 만든 앱에 사용되는 하위 도메인입니다. 몇 가지 제한 사항이 있습니다. 다음 항목을 사용할 수 없습니다.

    * net   

    * azurewebsites.net

    * p.azurewebsites.net

    * &lt;asename&gt;.p.azurewebsites.net

   웹앱에 기존 DNS 이름을 매핑할 수 있는 사용자 지정 도메인 이름이라는 기능이 있습니다. 해당 기능은 [웹앱에 기존 DNS 이름 매핑][customdomain] 문서에서 자세히 알아볼 수 있습니다. 앱의 사용자 지정 도메인 이름 및 ASE에서 사용하는 도메인 이름은 겹칠 수 없습니다. _contoso.com_이라는 도메인 이름의 ILB ASE의 경우 앱에 다음과 비슷한 사용자 지정 도메인 이름을 사용할 수 없습니다.

    * www.contoso.com

    * abcd.def.contoso.com

    * abcd.contoso.com

   앱의 사용자 지정 도메인 이름을 알고 있는 경우 해당 사용자 지정 도메인 이름과 충돌하지 않는 ILB ASE의 도메인을 선택합니다. 이 예제에서는 *.contoso.com*으로 끝나는 사용자 지정 도메인 이름과 충돌하지 않는 *contoso-internal.com*과 같은 이름을 ASE의 도메인에 사용할 수 있습니다.

8. **확인**을 선택하고 **만들기**를 선택합니다.

    ![ASE 만들기][1]

**Virtual Network** 블레이드에는 **Virtual Network 구성** 옵션이 있습니다. 이 옵션을 사용하여 외부 VIP 또는 내부 VIP를 선택할 수 있습니다. 기본값은 **외부**입니다. **외부**를 선택한 경우 ASE는 인터넷 액세스 가능 VIP를 사용합니다. **내부**를 선택하는 경우 ASE가 VNet 내의 IP 주소에 있는 ILB로 구성됩니다.

**내부**를 선택한 후에 ASE에 더 많은 IP 주소를 추가하는 기능이 제거됩니다. 대신 ASE의 도메인을 입력해야 합니다. 외부 VIP가 있는 ASE에서 ASE의 이름은 해당 ASE에서 만든 앱에 대한 도메인에 사용됩니다.

**VIP 형식**을 **내부**로 설정한 경우 ASE 이름은 해당 ASE의 도메인에서 사용되지 않습니다. 도메인을 명시적으로 지정해야 합니다. 도메인이 *contoso.corp.net*이고 해당 ASE에서 *timereporting*이라는 앱을 만들었으면 해당 앱의 URL은 timereporting.contoso.corp.net이 됩니다.


## <a name="create-an-app-in-an-ilb-ase"></a>ILB ASE에 앱 만들기 ##

일반적으로 ASE에서 앱을 만드는 것과 동일한 방식으로 ILB ASE에서 앱을 만듭니다.

1. Azure Portal에서 **리소스 만들기** > **웹 + 모바일** > **웹앱**을 선택합니다.

2. 앱의 이름을 입력합니다.

3. 구독을 선택합니다.

4. 리소스 그룹을 선택하거나 만듭니다.

5. OS를 선택합니다. 

    * 사용자 지정 Docker 컨테이너를 사용하여 Linux 앱을 만들려는 경우 여기에 나오는 지침을 사용하여 사용자 고유의 컨테이너를 가져올 수 있습니다. 

6. App Service 계획을 선택하거나 만듭니다. 새 App Service 계획을 만들려는 경우 ASE를 위치로 선택합니다. App Service 계획을 만들려는 작업자 풀을 선택합니다. App Service 계획을 만들 때 위치 및 작업자 풀로 ASE를 선택합니다. 앱의 이름을 지정하면 앱 이름 아래의 도메인을 ASE에 대한 도메인으로 바꿉니다.

7. **만들기**를 선택합니다. 앱을 대시보드에 표시하려면 **대시보드에 고정** 확인란을 선택합니다.

    ![App Service 계획 생성][2]

    **앱** 이름 아래에서 도메인 이름이 ASE의 도메인을 반영하도록 업데이트됩니다.

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE 생성 후 유효성 검사 ##

ILB ASE는 비 ILB ASE와는 약간 다릅니다. 이미 설명한 대로 고유의 DNS을 관리해야 합니다. 또한 HTTPS 연결에 고유한 인증서를 제공해야 합니다.

ASE를 만든 후에 도메인 이름은 지정된 도메인을 표시합니다. **ILB 인증서**라는 **설정** 메뉴에서 새 항목이 표시됩니다. ILB ASE 도메인을 지정하지 않는 인증서로 ASE가 만들어집니다. 해당 인증서로 ASE를 사용하는 경우 브라우저에서 올바르지 않음을 알려줍니다. 이 인증서를 사용하면 쉽게 HTTPS를 테스트할 수 있지만 ILB ASE 도메인에 연결된 고유한 인증서를 업로드해야 합니다. 인증서가 자체 서명되었는지 인증 기관에서 얻었는지에 관계없이 이 단계를 수행해야 합니다.

![ILB ASE 도메인 이름][3]

ILB ASE에는 유효한 SSL 인증서가 필요합니다. 내부 인증 기관을 사용하거나, 외부 발급자로부터 인증서를 구입하거나, 자체 서명된 인증서를 사용합니다. SSL 인증서의 소스에 관계 없이 다음과 같은 인증서 특성을 올바르게 구성해야 합니다.

* **주체**: 이 특성은 *.your-root-domain-here로 설정되어야 합니다.
* **주체 대체 이름**: 이 특성에는 **.your-root-domain-here* 및 **.scm.your-root-domain-here* 둘 다 포함되어야 합니다. 각 앱과 연결된 SCM/Kudu 사이트에 대한 SSL 연결은 *your-app-name.scm.your-root-domain-here* 양식의 주소를 사용합니다.

SSL 인증서를 .pfx 파일로 변환/저장합니다. .pfx 파일에는 모든 중간 인증서와 루트 인증서가 포함되어야 합니다. 암호로 인증서를 보호합니다.

자체 서명된 인증서를 만들려는 경우 여기에서 PowerShell 명령을 사용할 수 있습니다. *internal.contoso.com* 대신 ILB ASE 도메인 이름을 사용해야 합니다. 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

다음과 같은 PowerShell 명령이 생성한 인증서는 브라우저의 신뢰 체인에 있는 인증 기관에서 만들어지지 않았기 때문에 브라우저에서 플래그 지정됩니다. 브라우저에서 신뢰하는 인증서를 가져오려면 브라우저의 신뢰 체인에 있는 상용 인증 기관으로부터 확보합니다. 

![ILB 인증서 설정][4]

고유한 인증서를 업로드하고 액세스를 테스트하려면:

1. ASE를 만든 후에 ASE UI로 이동합니다. **ASE** > **설정** > **ILB 인증서**를 선택합니다.

2. ILB 인증서를 설정하려면 인증서 pfx 파일을 선택하고 암호를 입력합니다. 이 단계를 처리하는 데 시간이 걸립니다. 업로드 작업이 진행 중이라는 메시지가 나타납니다.

3. ASE에 ILB 주소를 가져옵니다. **ASE** > **속성** > **가상 IP 주소**를 선택합니다.

4. ASE를 만든 후에 해당 ASE에서 웹앱을 만듭니다.

5. VM이 VNet에 없는 경우 VM을 만듭니다.

    > [!NOTE] 
    > 실패하거나 문제를 일으키기 때문에 이 VM를 ASE와 동일한 서브넷에 만들려고 하지 않습니다.
    >
    >

6. ASE 도메인에 DNS를 설정합니다. DNS에 있는 도메인에서 와일드 카드를 사용할 수 있습니다. 몇 가지 간단한 테스트를 수행하려면 VIP IP 주소에 웹앱 이름을 설정하도록 VM에 대한 호스트 파일을 편집합니다.

    a. ASE의 도메인 이름이 _.ilbase.com_이고 _mytestapp_이라는 웹앱을 만든 경우 _mytestapp.ilbase.com_으로 주소가 지정됩니다. 그런 다음 _mytestapp.ilbase.com_을 설정하여 ILB 주소로 확인할 수 있습니다. (Windows에서 호스트 파일은 _C:\Windows\System32\drivers\etc\_에 있습니다.)

    나. 웹 배포 게시를 테스트하거나 고급 콘솔에 액세스하려면 _mytestapp.scm.ilbase.com_의 레코드를 만듭니다.

7. 해당 VM에서 브라우저를 사용하여 http://mytestapp.ilbase.com로 이동합니다. (또는 도메인에서 웹앱 이름으로 이동합니다.)

8. 해당 VM에서 브라우저를 사용하여 https://mytestapp.ilbase.com로 이동합니다. 자체 서명된 인증서를 사용하는 경우 보안 부족에 동의합니다.

    ILB의 IP 주소가 **IP 주소**에 나열됩니다. 이 목록에는 외부 VIP 및 인바운드 관리 트래픽에 사용하는 IP 주소도 있습니다.

    ![ILB IP 주소][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>웹 작업, 함수 및 ILB ASE ##

함수 및 웹 작업 모두 ILB ASE에서 지원되지만 포털에서 작동되려면 네트워크를 통해 SCM 사이트에 액세스할 수 있어야 합니다.  즉, 가상 네트워크에 있거나 가상 네트워크에 연결된 호스트에 브라우저가 있어야 합니다.  

ILB ASE에서 Azure Functions를 사용할 경우 다음과 같은 오류 메시지가 발생할 수 있습니다. "함수를 지금 바로 검색할 수 없습니다. 나중에 다시 시도하십시오." 이 오류는 Functions UI가 HTTPS를 통해 SCM 사이트를 사용하며 루트 인증서가 브라우저의 신뢰 체인에 없기 때문에 발생합니다. 웹 작업에도 비슷한 문제가 있습니다. 이 문제를 방지하기 위해 다음 중 하나를 수행할 수 있습니다.

- 신뢰할 수 있는 인증서 저장소에 인증서를 추가합니다. 그러면 Edge 및 Internet Explorer가 차단 해제됩니다.
- Chrome을 사용하고 먼저 SCM 사이트로 이동한 후 신뢰할 수 없는 인증서를 수락한 다음 포털로 이동합니다.
- 브라우저의 신뢰 체인에 있는 상용 인증서를 사용합니다.  이것이 최상의 옵션입니다.  

## <a name="dns-configuration"></a>DNS 구성 ##

외부 VIP를 사용하는 경우 DNS가 Azure에서 관리됩니다. ASE에서 만든 모든 앱은 공용 DNS에 해당하는 Azure DNS에 자동으로 추가됩니다. ILB ASE에서 자체 DNS를 관리해야 합니다. _contoso.net_과 지정된 도메인의 경우 다음에 대해 ILB 주소를 가리키는 DNS에서 DNS A 레코드를 만들어야 합니다.

- *.contoso.net
- *.scm.contoso.net

ILB ASE 도메인이 해당 ASE 외부에서 여러 작업에 사용되는 경우 앱 이름 기준으로 DNS에서 관리해야 합니다. 이 방법은 새 앱을 만들 때 각 앱 이름을 DNS에 추가해야 하기 때문에 더 어렵습니다. 이러한 이유로 전용 도메인을 사용하는 것이 좋습니다.

## <a name="publish-with-an-ilb-ase"></a>ILB ASE로 게시 ##

만든 모든 앱에는 두 개의 끝점이 있습니다. ILB ASE에는 *&lt;앱 이름>.&lt;ILB ASE 도메인>* 과 *&lt;앱 이름>.scm.&lt; ILB ASE 도메인>* 이 있습니다. 

SCM 사이트 이름은 Azure Portal 내에서 **고급 포털**이라고 하는 Kudu 콘솔로 이동합니다. Kudu 콘솔을 통해 환경 변수를 확인하고, 디스크를 탐색하고, 콘솔을 사용할 수 있습니다. 자세한 내용은 [Azure App Service의 Kudu 콘솔][Kudu]을 참조하세요. 

다중 테넌트 App Service 및 외부 ASE에는 Azure Portal과 Kudu 콘솔 간의 Single Sign-On이 포함됩니다. 그러나 ILB ASE의 경우 게시 자격 증명을 사용하여 Kudu 콘솔에 로그인해야 합니다.

GitHub 및 Visual Studio Team Services와 같은 인터넷 기반 CI 시스템은 빌드 에이전트가 인터넷에 액세스할 수 있고 ILB ASE와 동일한 네트워크에 있는 경우 ILB ASE와 함께 작동합니다. 따라서 Visual Studio Team Services의 경우 빌드 에이전트가 ILB ASE와 동일한 VNET에서 만들어지면(다른 서브넷은 문제 없음) VSTS git에서 코드를 끌어오고 ILB ASE에 배포할 수 있습니다. 사용자 고유의 빌드 에이전트를 만들지 않으려면 Dropbox 등의 끌어오기 모델을 사용하는 CI 시스템을 사용해야 합니다.

ILB ASE의 앱에 대한 게시 끝점에서는 ILB ASE가 만들어진 도메인을 사용합니다. 이 도메인은 앱의 게시 프로필과 앱의 포털 블레이드(**개요** > **Essentials** 및 **속성**)에서 표시됩니다. 하위 도메인이 *contoso.net*인 ILB ASE 및 *mytest*라는 앱이 있는 경우 FTP에 *mytest.contoso.net*을 사용하고 웹 배포에 *mytest.scm.contoso.net*을 사용합니다.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>WAF 장치로 ILB ASE 연결 ##

Azure App Service는 시스템을 보호하는 많은 보안 조치를 제공합니다. 앱이 해킹되었는지를 확인할 수도 있습니다. 웹 응용 프로그램을 보호하기 위해 WAF(웹 응용 프로그램 방화벽)를 사용하여 Azure App Service와 같은 호스팅 플랫폼을 결합해야 합니다. ILB ASE에 네트워크 격리 응용 프로그램 끝점이 있기 때문에 사용하기에 적합합니다.

WAF 장치를 사용하여 ILB ASE를 구성하는 방법에 대한 자세한 내용은 [App Service Environment를 사용하여 웹 응용 프로그램 방화벽 구성][ASEWAF]을 참조하세요. 이 문서에서는 ASE를 사용하여 Barracuda 가상 어플라이언스를 사용하는 방법을 보여줍니다. 다른 방법은 Azure Application Gateway를 사용하는 것입니다. Application Gateway는 이후에 배치할 모든 응용 프로그램을 보호하기 위해 OWASP 핵심 규칙을 사용합니다. Application Gateway에 대한 자세한 내용은 [Azure 웹 응용 프로그램 방화벽 소개][AppGW]를 참조하세요.

## <a name="get-started"></a>시작하기 ##

* ASE를 시작하려면 [App Service Environment 소개][Intro]를 참조하세요.
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
