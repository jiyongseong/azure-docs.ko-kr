---
title: Resource Manager 템플릿을 사용하여 Azure App Service Environment 만들기
description: Resource Manager 템플릿을 사용하여 외부 또는 ILB Azure App Service Environment를 만드는 방법을 설명합니다.
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 69ead9e6dae400ce16cb2442c7b1c13e348d1572
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 ASE 만들기

## <a name="overview"></a>개요
Azure ASE(App Service Environment)는 인터넷에 액세스할 수 있는 끝점 또는 Azure VNet(Virtual Network)의 내부 주소에 있는 끝점을 사용하여 만들 수 있습니다. 끝점이 내부 끝점을 사용하여 만들어진 경우 ILB(내부 부하 분산 장치)를 호출하는 Azure구성 요소에서 해당 끝점을 제공합니다. 내부 IP 주소의 ASE를 ILB ASE라고 합니다. 공용 끝점이 있는 ASE를 외부 ASE라고 합니다. 

Azure Portal 또는 Azure Resource Manager 템플릿을 사용하여 ASE를 만들 수 있습니다. 이 문서에서는 Resource Manager 템플릿으로 외부 ASE 또는 ILB ASE를 만드는 데 필요한 단계와 구문을 안내합니다. Azure Portal에서 ASE를 만드는 방법에 대해 알아보려면 [외부 ASE 만들기][MakeExternalASE] 또는 [ILB ASE 만들기][MakeILBASE]를 참조하세요.

Azure Portal에서 ASE를 만들 때는 동시에 VNet를 만들거나 ASE를 배포할 기존 VNet를 선택할 수 있습니다. 템플릿에서 ASE를 만들 경우 다음 항목을 미리 준비해야 합니다. 

* Resource Manager VNet
* 해당 VNet의 서브넷 향후 확장이 가능하도록 128개의 주소를 포함할 수 있는 `/25` 크기의 ASE 서브넷을 사용하는 것이 좋습니다. ASE를 만든 후에는 크기를 변경할 수 없습니다.
* VNet의 리소스 ID. Azure Portal의 가상 네트워크 속성에서 이 정보를 가져올 수 있습니다.
* ASE를 배포할 구독
* ASE를 배포할 위치

ASE 만들기를 자동화하려면:

1. 템플릿에서 ASE를 만듭니다. 외부 ASE를 만드는 경우에는 이 단계를 수행하면 만들기가 완료됩니다. ILB ASE를 만드는 경우에는 몇 가지 추가 작업을 수행합니다.

2. ILB ASE를 만든 후에 ILB ASE 도메인과 일치하는 SSL 인증서를 업로드합니다.

3. 업로드된 SSL 인증서는 해당 "기본" SSL 인증서로서 ILB ASE에 명시적으로 할당됩니다.  이 SSL 인증서는 ASE에 할당된 공용 루트 도메인(예: https://someapp.mycustomrootdomain.com))을 사용할 때 ILB ASE의 앱으로 이동하는 SSL 트래픽에 사용됩니다.


## <a name="create-the-ase"></a>ASE 만들기
ASE를 만드는 Resource Manager 템플릿 및 관련 매개 변수 파일은 GitHub의 [예제][quickstartasev2create]에서 찾아볼 수 있습니다.

ILB ASE를 만들려는 경우에는 이 Resource Manager 템플릿 [예제][quickstartilbasecreate]를 사용하세요. 해당 사용 사례를 제공합니다. *azuredeploy.parameters.json* 파일에 있는 대부분의 매개 변수는 ILB ASE와 외부 ASE 둘 다를 만들 때 공통적으로 적용됩니다. 아래 목록에는 ILB ASE를 만들 때 특히 주의해야 하는 매개 변수 또는 고유한 매개 변수가 나와 있습니다.

* *internalLoadBalancingMode*: 대부분의 경우 이 속성을 3으로 설정합니다. 이것은 포트 80/443의 HTTP/HTTPS 트래픽과 ASE의 FTP 서비스에서 수신하는 컨트롤/데이터 채널 포트가 ILB 할당 가상 네트워크 내부 주소에 바인딩될 것임을 의미합니다. 이 속성을 2로 설정하면 FTP 서비스 관련 포트(컨트롤 채널과 데이터 채널 둘 다)만 ILB 주소로 바인딩됩니다. HTTP/HTTPS 트래픽은 공용 VIP에 그대로 유지됩니다.
* *dnsSuffix*: 이 매개 변수는 ASE에 할당되는 기본 루트 도메인을 정의합니다. Azure App Service의 공용 변형에서 모든 웹앱용 기본 루트 도메인은 *azurewebsites.net*입니다. ILB ASE는 고객의 가상 네트워크 내부에 있으므로 공용 서비스의 기본 루트 도메인을 사용하는 것은 적합하지 않습니다. 대신, ILB ASE에는 회사의 내부 가상 네트워크 내에서 사용하기 적합한 기본 루트 도메인이 있어야 합니다. 예를 들어 Contoso Corporation은 Contoso의 가상 네트워크 내에서만 확인 가능하고 액세스할 수 있는 앱에 기본 루트 도메인 *internal-contoso.com*을 사용할 수 있습니다. 
* *ipSslAddressCount*: 이 매개 변수는 *azuredeploy.json* 파일에서 자동으로 기본값인 0으로 지정됩니다. ILB ASE에는 ILB 주소가 하나뿐이기 때문입니다. ILB ASE용 명시적 IP-SSL 주소는 없습니다. 따라서 ILB ASE용 IP-SSL 주소 풀은 0으로 설정해야 합니다. 그렇지 않으면 프로비전 오류가 발생합니다. 

*azuredeploy.parameters.json* 파일에 매개 변수를 입력한 후 PowerShell 코드 조각을 사용하여 ASE를 만듭니다. 컴퓨터의 Resource Manager 템플릿 파일 위치와 일치하도록 파일 경로를 변경합니다. Resource Manager 배포 이름 및 리소스 그룹 이름에 대해 고유한 값을 제공해야 합니다.

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

ASE가 작성되려면 1시간 정도 걸립니다. 이 시간이 지나면 ASE가 Portal에서 배포를 트리거한 구독의 ASE 목록에 표시됩니다.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>"기본" SSL 인증서 업로드 및 구성
SSL 인증서를 앱에 대한 SSL 연결을 설정하는 데 사용되는 "기본" SSL 인증서로 ASE와 연결해야 합니다. ASE의 기본 DNS 접미사가 *internal-contoso.com*인 경우 https://some-random-app.internal-contoso.com에 연결하려면 **.internal-contoso.com*에 유효한 SSL 인증서가 필요합니다. 

내부 인증 기관을 사용하거나, 외부 발급자로부터 인증서를 구입하거나, 자체 서명된 인증서를 사용하는 등의 방법으로 유효한 SSL 인증서를 구합니다. SSL 인증서의 소스에 관계 없이 다음과 같은 인증서 특성을 올바르게 구성해야 합니다.

* **주체**: 이 특성은 **.your-root-domain-here.com*으로 설정되어야 합니다.
* **주체 대체 이름**: 이 특성에는 **.your-root-domain-here.com* 및 **.scm.your-root-domain-here.com*이 둘 다 포함되어야 합니다. 각 앱과 연결된 SCM/Kudu 사이트에 대한 SSL 연결은 *your-app-name.scm.your-root-domain-here.com* 형식의 주소를 사용합니다.

유효한 SSL 인증서가 있는 경우 두 가지 추가 준비 단계가 필요합니다. SSL 인증서를 .pfx 파일로 변환/저장합니다. .pfx 파일에는 모든 중간 인증서와 루트 인증서를 포함해야 합니다. 암호로 인증서를 보호합니다.

SSL 인증서는 Azure Resource Manager 템플릿을 사용하여 업로드되므로 .pfx 파일을 Base64 문자열로 변환해야 합니다. Resource Manager 템플릿은 텍스트 파일이므로 .pfx 파일을 Base64 문자열로 변환해야 합니다. 이렇게 해당 파일을 템플릿의 매개 변수로 포함할 수 있습니다.

아래 PowerShell 코드 조각을 사용하여 다음 작업을 수행합니다.

* 자체 서명된 인증서를 생성합니다.
* 인증서를 .pfx 파일로 내보냅니다.
* .pfx 파일을 Base64 인코딩 문자열로 변환합니다.
* Base64 인코딩 문자열을 별도의 파일로 저장합니다. 

이 Base64 인코딩용 PowerShell 코드는 [PowerShell 스크립트 블로그][examplebase64encoding]에서 가져와 수정한 것입니다.

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

SSL 인증서가 생성되고 Base64 인코딩 문자열로 변환되면 GitHub의 예제 Resource Manager 템플릿 [기본 SSL 인증서 구성][quickstartconfiguressl]을 사용하세요. 

*azuredeploy.parameters.json* 파일의 매개 변수는 다음과 같습니다.

* *appServiceEnvironmentName*: 구성하는 ILB ASE의 이름입니다.
* *existingAseLocation*: ILB ASE가 배포된 Azure 지역을 포함하는 텍스트 문자열입니다.  예를 들어 "미국 중남부"입니다.
* *pfxBlobString*: .pfx 파일의 Base64 인코딩 문자열 표현입니다. 위에 나와 있는 코드 조각을 사용하여 "exportedcert.pfx.b64"에 포함된 문자열을 복사합니다. 이 문자열을 *pfxBlobString* 특성의 값으로 붙여넣습니다.
* *password*: .pfx 파일을 보호하는 데 사용되는 암호입니다.
* *certificateThumbprint*: 인증서의 지문입니다. PowerShell에서 이 값을 검색하는 경우(예: 이전 코드 조각의 *$certificate.Thumbprint*) 값을 있는 그대로 사용할 수 있습니다. Windows 인증서 대화 상자의 값을 복사하는 경우 불필요한 공백을 제거해야 합니다. *certificateThumbprint* 는 AF3143EB61D43F6727842115BB7F17BBCECAECAE와 같이 나타납니다.
* *certificateName*: 인증서를 식별하는 데 사용되는 직접 선택한 친숙한 문자열 식별자입니다. 이 이름은 SSL 인증서를 나타내는 *Microsoft.Web/certificates* 엔터티에 대한 고유한 Resource Manager 식별자의 일부로 사용됩니다. 이름은 *반드시* \_yourASENameHere_InternalLoadBalancingASE 접미사로 끝나야 합니다. Azure Portal에서는 인증서가 ILB 지원 ASE를 보호하는 데 사용됨을 나타내는 표시기로 이 접미사를 사용합니다.

*azuredeploy.parameters.json*을 축약한 예는 다음과 같습니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

*azuredeploy.parameters.json* 파일에 매개 변수를 입력한 후 PowerShell 코드 조각을 사용하여 기본 SSL 인증서를 구성합니다. 컴퓨터에 Resource Manager 템플릿 파일이 있는 위치와 일치하도록 파일 경로를 변경합니다. Resource Manager 배포 이름 및 리소스 그룹 이름에 대해 고유한 값을 제공해야 합니다.

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

변경 내용이 적용되려면 ASE 프런트 엔드당 약 40분이 걸립니다. 예를 들어 두 개의 프런트 엔드를 사용하는 기본 크기 ASE의 경우 템플릿을 완료하는 데 약 1시간 20분이 소요됩니다. 템플릿이 실행되는 동안에는 ASE 크기를 조정할 수 없습니다.  

템플릿이 완료되면 HTTPS를 통해 ILB ASE의 앱에 액세스할 수 있습니다. 연결은 기본 SSL 인증서를 통해 보호됩니다. 응용 프로그램 이름과 기본 호스트 이름의 조합을 사용하여 ILB ASE의 앱에 주소를 지정할 때 기본 SSL 인증서가 사용됩니다. 예를 들어 https://mycustomapp.internal-contoso.com은 **.internal-contoso.com*에 기본 SSL 인증서를 사용합니다.

그러나 개발자는 공용 다중 테넌트 서비스에서 실행되는 앱과 마찬가지로 개별 앱에 대해 사용자 지정 호스트 이름을 구성할 수 있습니다. 또한 개별 앱에 대해 고유한 SNI SSL 인증서 바인딩을 구성할 수도 있습니다.

## <a name="app-service-environment-v1"></a>App Service 환경 v1 ##
App Service Environment에는 두 가지 버전(ASEv1 및 ASEv2)이 있습니다. 위의 정보는 ASEv2를 기준으로 작성된 것입니다. 이 섹션은 ASEv1과 ASEv2의 차이를 보여줍니다.

ASEv1에서는 모든 리소스를 수동으로 관리합니다. 여기에는 IP 기반 SSL에 사용되는 프런트 엔드, 작업자 및 IP 주소가 포함됩니다. App Service 계획을 규모 확장하려면 먼저 해당 App Service를 호스트할 작업자 풀을 규모 확장해야 합니다.

ASEv1은 ASEv2와는 다른 가격 책정 모델을 사용합니다. ASEv1에서는 할당된 각 vCPU에 대한 비용을 지불합니다. 여기에는 작업을 호스트하지 않는 프런트 엔드 또는 작업자에 사용되는 vCPU가 포함됩니다. ASEv1에서 ASE의 기본 최대 규모 크기는 총 55개의 호스트입니다. 여기에는 작업자 및 프런트 엔드가 포함됩니다. ASEv1의 한 가지 장점은 클래식 가상 네트워크 및 Resource Manager 가상 네트워크에 배포할 수 있다는 것입니다. ASEv1에 대해 자세히 알아보려면 [App Service Environment v1 소개][ASEv1Intro]를 참조하세요.

Resource Manager 템플릿을 사용하여 ASEv1을 만들려는 경우 [Resource Manager 템플릿을 사용하여 ILB ASE v1 만들기][ILBASEv1Template]를 참조하세요.


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
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
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
