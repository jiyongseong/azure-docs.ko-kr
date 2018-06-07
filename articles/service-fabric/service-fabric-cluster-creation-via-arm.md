---
title: 템플릿에서 Azure Service Fabric 클러스터 만들기 | Microsoft Docs
description: 이 문서에서는 클라이언트 인증에 Azure Resource Manager, Azure Key Vault, Azure Active Directory(Azure AD)를 사용하여 Azure에 보안 Service Fabric 클러스터를 설정하는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: aljo
ms.openlocfilehash: 60b447148c5cef24c061274a84620a8221efc430
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207947"
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기 
> [!div class="op_single_selector"]
> * [Azure 리소스 관리자](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Azure Resource Manager를 사용하여 Azure에 보안 Azure Service Fabric 클러스터를 설정하는 단계를 안내하는 단계별 가이드입니다. 이 문서는 깁니다. 그렇지만 아직 이 내용에 익숙하지 않다면 각 단계를 세심히 따라보시기 바랍니다. 

이 가이드에서는 다음 절차를 다룹니다.

* Service Fabric 클러스터를 배포하기 전에 알아야 할 주요 개념
* Service Fabric Resource Manager 모듈을 사용하여 Azure에서 클러스터 만들기
* 클러스터에서 관리 작업을 수행하는 사용자를 인증하기 위해 Azure AD(Azure Active Directory) 설정
* 클러스터를 배포하기 위해 사용자 지정Azure Resource Manager 템플릿 작성

## <a name="key-concepts-to-be-aware-of"></a>알아야 할 주요 개념
Azure에서 Service Fabric은 클러스터 및 해당 끝점을 보호하기 위해 x509 인증서를 사용할 수 있습니다. 인증서는 서비스 패브릭에서 클러스터 및 해당 응용 프로그램의 다양한 측면을 보호하기 위해 인증 및 암호화를 제공하는 데 사용됩니다. 응용 프로그램, 서비스 및 포함된 데이터를 배포하고, 업그레이드하고 삭제하는 작업을 포함하여 클러스터에 대한 관리 작업을 액세스/수행하는 클라이언트의 경우 인증서 또는 Azure Active Directory 자격 증명을 사용할 수 있습니다. 클라이언트에서 인증서를 공유하지 않도록 방지하는 유일한 방법이므로 Azure Active Directory를 사용하는 것이 좋습니다.  Service Fabric에서 인증서가 사용되는 방식에 대한 자세한 내용은 [Service Fabric 클러스터 보안 시나리오][service-fabric-cluster-security]를 참조하세요.

서비스 패브릭은 클러스터에 보안 적용을 하고 응용 프로그램 보안 기능을 제공하기 위해 X.509 인증서를 사용합니다. [Key Vault][key-vault-get-started]를 사용하여 Azure에서 Service Fabric 클러스터에 대한 인증서를 관리합니다. 


### <a name="cluster-and-server-certificate-required"></a>클러스터 및 서버 인증서(필수)
클러스터를 보호하고 무단 액세스를 방지하기 위해 이러한 인증서(기본 및 필요에 따라 보조)가 필요합니다. 다음 두 방법으로 클러스터 보안을 제공합니다.

* **클러스터 인증:** 클러스터 페더레이션에 대한 노드 간 통신을 인증합니다. 이 인증서로 자신의 신분을 증명할 수 있는 노드만 클러스터에 가입할 수 있습니다.
* **서버 인증:** 관리 클라이언트에 대한 클러스터 관리 끝점을 인증하여 관리 클라이언트가 '메시지 가로채기(man in the middle)'가 아닌 실제 클러스터와 통신하는지 알 수 있도록 합니다. 이 인증은 HTTPS 관리 API 및 HTTPS를 통한 Service Fabric Explorer용 SSL도 제공합니다.

이를 위해 인증서는 다음 요구 사항을 충족해야 합니다.

* 인증서에 개인 키가 포함되어 있어야 합니다. 이러한 인증서의 확장명은 일반적으로 .pfx 또는 .pem입니다.  
* 키 교환을 위해 인증서를 만들어야 합니다. 이 인증서는 개인 정보 교환(.pfx) 파일로 내보낼 수 있습니다.
* **인증서의 주체 이름은 Service Fabric 클러스터에 액세스하는 데 사용하는 도메인과 일치해야 합니다**. 클러스터의 HTTPS 관리 끝점 및 Service Fabric Explorer에 SSL을 제공하려면 이렇게 일치해야 합니다. *.cloudapp.azure.com 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다. 클러스터에 대한 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>클라이언트 인증에 대한 Azure Active Directory 설정(선택 사항이지만 권장되지 않음)

조직(테넌트)에서는 Azure AD를 사용하여 응용 프로그램에 대한 사용자 액세스를 관리할 수 있습니다. 응용 프로그램은 웹 기반 로그인 UI를 갖는 항목과 네이티브 클라이언트 환경을 갖는 항목으로 나뉩니다. 이 문서에서는 이미 테넌트를 만들었다고 가정합니다. 그러지 않은 경우 [Azure Active Directory 테넌트를 얻는 방법][active-directory-howto-tenant]을 참조하세요.

Service Fabric 클러스터는 웹 기반 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 및 [Visual Studio][service-fabric-manage-application-in-visual-studio]를 포함하여 관리 기능에 대한 여러 진입점을 제공합니다. 결과적으로 두 개의 Azure AD 응용 프로그램(웹 응용 프로그램과 네이티브 응용 프로그램)을 만들어 클러스터에 대한 액세스를 제어합니다.

이 문서의 뒷부분에서 설정하는 방법을 자세히 살펴보겠습니다.

### <a name="application-certificates-optional"></a>응용 프로그램 인증서(선택 사항)
응용 프로그램 보안을 위해 클러스터에 제한 없는 수의 인증서를 추가로 설치할 수 있습니다. 클러스터를 만들기 전에, 다음과 같이 노드에 인증서를 설치하도록 요구하는 응용 프로그램 보안 시나리오를 고려해 보세요.

* 응용 프로그램 구성 값의 암호화 및 암호 해독
* 복제 중에 노드 간 데이터 암호화

Linux나 Windows 클러스터 모두 보안 클러스터를 만드는 개념은 같습니다. 

### <a name="client-authentication-certificates-optional"></a>클라이언트 인증 인증서(선택 사항)
관리자 또는 사용자 클라이언트 작업에 임의 개수의 추가 인증서를 지정할 수 있습니다. 기본적으로 클러스터 인증서에는 관리자 클라이언트 권한이 있습니다. 이러한 추가 클라이언트 인증서를 클러스터에 설치하지 않고 클러스터 구성에서 허용되도록 지정해야 합니다. 하지만 클라이언트 컴퓨터에 설치하여 클러스터에 연결하고 모든 관리 작업을 수행해야 합니다.


## <a name="prerequisites"></a>필수 조건 
Linux나 Windows 클러스터 모두 보안 클러스터를 만드는 개념은 같습니다. 이 가이드에서는 Azure PowerShell 또는 Azure CLI를 사용하여 새 클러스터를 만드는 방법을 다룹니다. 필수 조건은 다음과 같습니다. 

-  [Azure PowerShell 4.1 이상][azure-powershell] 또는 [Azure CLI 2.0 이상][azure-CLI]
-  [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) 및 [az SF CLI module](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)에서 Service Fabric 모듈에 대한 세부 정보를 찾을 수 있습니다.


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Service Fabric RM 모듈을 사용하여 클러스터 배포

이 문서에서는 Service Fabric RM PowerShell 및 CLI 모듈을 사용하여 클러스터를 배포하고, PowerShell 및 CLI 모듈 명령을 여러 시나리오에서 허용합니다. 각각에 대해 알아보겠습니다. 요구 사항을 충족하는 시나리오를 선택합니다. 

- 시스템 생성 자체 서명된 인증서를 사용하여 새 클러스터 만들기
    - 기본 클러스터 템플릿 사용
    - 이미 있는 템플릿 사용
- 이미 소유한 인증서를 사용하여 새 클러스터 만들기
    - 기본 클러스터 템플릿 사용
    - 이미 있는 템플릿 사용

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>시스템 생성 자체 서명된 인증서를 사용하여 새 클러스터 만들기

시스템에서 자체 서명된 인증서를 생성하고 클러스터를 보호하는 데 사용하려는 경우 다음 명령을 사용하여 클러스터를 만듭니다. 이 명령은 클러스터 보안을 위해 사용되는 기본 클러스터 인증서를 설정하고, 해당 인증서를 사용하여 관리 작업을 수행할 관리자 액세스를 설정합니다.

### <a name="login-in-to-azure"></a>Azure에 로그인합니다.

```Powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
```

```CLI
azure login
az account set --subscription $subscriptionId
```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>모듈에서 제공되는 기본 5개 노드 1 nodetype 템플릿을 사용하여 클러스터 설정

최소 매개 변수를 지정하여 클러스터를 신속하게 만드는 다음 명령 사용

사용되는 템플릿은 [Azure Service Fabric 템플릿 샘플: Windows 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) 및 [Ubuntu 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)에서 사용할 수 있습니다.

아래 명령은 Windows 및 Linux 클러스터를 만드는 경우에도 작동합니다. 해당하는 OS를 지정하면 됩니다. 또한 PowerShell/CLI 명령은 CertificateOutputFolder에 지정된 인증서를 출력하지만 인증서 폴더가 이미 만들어졌는지 확인합니다. 이 명령은 VM SKU와 같은 다른 매개 변수를 사용합니다.

```Powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

```CLI
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

#### <a name="use-the-custom-template-that-you-already-have"></a>이미 있는 사용자 지정 템플릿 사용 

필요에 맞게 사용자 지정 템플릿을 작성해야 하는 경우 [Azure Service Fabric 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)에서 사용할 수 있는 템플릿 중 하나로 시작하는 것이 좋습니다. 아래 [클러스터 템플릿 사용자 지정][customize-your-cluster-template] 섹션의 설명 및 지침에 따릅니다.

이미 사용자 지정 템플릿이 있는 경우 템플릿 및 매개 변수 파일에 있는 모든 세 개의 인증서 관련 매개 변수 이름이 다음과 같고 값에는 다음과 같이 Null인지 두 번 확인하도록 합니다.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

동일한 작업을 수행하는 해당 CLI 명령은 다음과 같습니다. 선언 문의 값을 적절한 값으로 변경합니다. CLI는 위의 PowerShell 명령이 지원하는 다른 모든 매개 변수를 지원합니다.

```CLI
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>CA에서 구입하거나 이미 있는 인증서를 사용하여 새 클러스터 만들기

클러스터를 보호하는 데 사용할 인증서가 있는 경우 다음 명령을 사용하여 클러스터를 만듭니다.

결국 다른 용도로 사용할 CA 서명 인증서인 경우 키 자격 증명 모음에 특별히 고유한 리소스 그룹을 제공하는 것이 좋습니다. Key Vault를 자체 리소스 그룹에 배치하는 것이 좋습니다. 이렇게 하면 키 및 암호는 유실하지 않고 Service Fabric 클러스터가 있는 리소스 그룹과 같은 계산 및 저장소 리소스 그룹을 제거할 수 있습니다. **키 자격 증명 모음을 포함하는 리소스 그룹은 해당 그룹을 사용하는 클러스터와 _동일한 지역에 있어야_ 합니다.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>모듈에서 제공되는 기본 5개 노드 1 nodetype 템플릿 사용
사용되는 템플릿은 [Azure 샘플: Windows 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) 및 [Ubuntu 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)에서 사용할 수 있습니다.

```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

```CLI
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

#### <a name="use-the-custom-template-that-you-have"></a>기존의 사용자 지정 템플릿 사용 
필요에 맞게 사용자 지정 템플릿을 작성해야 하는 경우 [Azure Service Fabric 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)에서 사용할 수 있는 템플릿 중 하나로 시작하는 것이 좋습니다. 아래 [클러스터 템플릿 사용자 지정][customize-your-cluster-template] 섹션의 설명 및 지침에 따릅니다.

이미 사용자 지정 템플릿이 있는 경우 템플릿 및 매개 변수 파일에 있는 모든 세 개의 인증서 관련 매개 변수 이름이 다음과 같고 값에는 다음과 같이 null인지 두 번 확인하도록 합니다.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

동일한 작업을 수행하는 해당 CLI 명령은 다음과 같습니다. 선언 문의 값을 적절한 값으로 변경합니다.

```CLI
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-key-vault"></a>키 자격 증명 모음에 이미 업로드된 암호에 포인터 사용

기존 Key Vault를 사용하려면 연산 리소스 공급자가 인증서를 가져와 클러스터 노드에 설치할 수 있도록 Key Vault를 _배포에 대해 사용하도록 설정해야 합니다_.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```
동일한 작업을 수행하는 해당 CLI 명령은 다음과 같습니다. 선언 문의 값을 적절한 값으로 변경합니다.

```CLI
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>클라이언트 인증에 대한 Azure Active Directory 설정

조직(테넌트)에서는 Azure AD를 사용하여 응용 프로그램에 대한 사용자 액세스를 관리할 수 있습니다. 응용 프로그램은 웹 기반 로그인 UI를 갖는 항목과 네이티브 클라이언트 환경을 갖는 항목으로 나뉩니다. 이 문서에서는 이미 테넌트를 만들었다고 가정합니다. 그러지 않은 경우 [Azure Active Directory 테넌트를 얻는 방법][active-directory-howto-tenant]을 참조하세요.

Service Fabric 클러스터는 웹 기반 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 및 [Visual Studio][service-fabric-manage-application-in-visual-studio]를 포함하여 관리 기능에 대한 여러 진입점을 제공합니다. 결과적으로 두 개의 Azure AD 응용 프로그램(웹 응용 프로그램과 네이티브 응용 프로그램)을 만들어 클러스터에 대한 액세스를 제어합니다.

Service Fabric 클러스터로 Azure AD를 구성하는 데 포함되는 일부 단계를 단순화하기 위해 Windows PowerShell 스크립트 집합을 만들었습니다.

> [!NOTE]
> 클러스터를 만들기 전에 다음 단계를 완료해야 합니다. 스크립트는 클러스터 이름과 끈점을 예상하므로 이 값을 계획해야 하며, 이 값은 이미 만든 값이 아닙니다.

1. 컴퓨터에 [스크립트를 다운로드][sf-aad-ps-script-download]합니다.
2. zip 파일을 마우스 오른쪽 단추로 클릭하고 **속성**, **차단 해제** 확인란을 차례로 선택한 다음 **적용**을 클릭합니다.
3. zip 파일의 압축을 풉니다.
4. `SetupApplications.ps1`을 실행하고 TenantId, ClusterName 및 WebApplicationReplyUrl을 매개 변수로 제공합니다. 예: 

```powershell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
```

PowerShell 명령 `Get-AzureSubscription`을 실행하여 TenantId를 찾을 수 있습니다. 이 명령을 실행하면 모든 구독에 대한 TenantId가 표시됩니다.

ClusterName은 스크립트로 만든 Azure AD 응용 프로그램에 접두사를 지정하는 데 사용됩니다. 실제 클러스터 이름과 정확히 일치할 필요는 없습니다. 단순히 Azure AD 아티팩트를, 함께 사용할 Service Fabric 패브릭 클러스터에 쉽게 매핑하기 위한 것입니다.

WebApplicationReplyUrl은 로그인을 마친 후 Azure AD가 사용자를 돌려보낼 기본 끝점입니다. 이 끝점을 기본적으로 다음과 같은 클러스터에 대한 Service Fabric Explorer 끝점으로 설정합니다.

https://&lt;cluster_domain&gt;:19080/Explorer

Azure AD 테넌트에 대한 관리자 권한이 있는 계정으로 로그인하라는 메시지가 표시됩니다. 로그인한 후에는 스크립트가 Service Fabric 클러스터를 나타내는 웹 및 네이티브 응용 프로그램을 만듭니다. [Azure Portal][azure-portal]에서 테넌트의 응용 프로그램을 보면 두 개의 새 항목이 표시됩니다.

   * *ClusterName*\_클러스터
   * *ClusterName*\_클라이언트

다음 섹션에서 클러스터를 만들 때 스크립트는 Azure Resource Manager 템플릿에서 필요한 JSON을 인쇄하므로 PowerShell 창을 계속 열어 두는 것이 좋습니다.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>서비스 패브릭 클러스터 Resource Manager 템플릿 만들기
Service Fabric 클러스터 Resource Manager 템플릿을 사용자 지정 작성하려는 사용자를 위한 섹션입니다. 템플릿이 있다면 다시 돌아가서 PowerShell 또는 CLI 모듈을 사용하여 배포합니다. 

샘플 Resource Manager 템플릿은 [GitHub의 Azure 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates)에서 사용할 수 있습니다. 이러한 템플릿은 클러스터 템플릿의 시작점으로 사용할 수 있습니다.

### <a name="create-the-resource-manager-template"></a>리소스 관리자 템플릿 만들기
이 가이드에서는 [5 노드 보안 클러스터][service-fabric-secure-cluster-5-node-1-nodetype] 예제 템플릿과 템플릿 매개 변수를 사용합니다. `azuredeploy.json`와 `azuredeploy.parameters.json`을 컴퓨터에 다운로드하고 선호하는 텍스트 편집기로 두 파일을 엽니다.

### <a name="add-certificates"></a>인증서 추가
인증서 키를 포함하는 Key Vault 참조하여 Cluster Resource Manager 템플릿에 인증서를 추가합니다. Resource Manager 템플릿 매개 변수 파일(azuredeploy.parameters.json)에 해당 키 자격 증명 모음 매개 변수 및 값을 추가합니다. 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>가상 머신 확장 집합 osProfile에 모든 인증서 추가
클러스터에 설치된 모든 인증서는 확장 집합 리소스(Microsoft.Compute/virtualMachineScaleSets)의 osProfile 섹션에 구성되어야 합니다. 이 작업은 리소스 공급자에게 인증서를 VM에 설치하도록 지시합니다. 이 설치에는 클러스터 인증서와, 응용 프로그램에 사용하려는 모든 응용 프로그램 보안 인증서가 포함됩니다.

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Service Fabric 클러스터 인증서 구성
또한 클러스터 인증 인증서는 서비스 패브릭 클러스터 리소스(Microsoft.ServiceFabric/clusters)와, 가상 머신 확장 집합 리소스의 가상 머신 확장 집합에 대해 Service Fabric 확장에서 모두 구성되어야 합니다. 이렇게 해야 Service Fabric 리소스 공급자는 클러스터 인증에 대한 사용 및 관리 끝점에 대한 서버 인증을 위해 그것을 구성할 수 있습니다.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>인증서 정보를 가상 머신 확장 집합 리소스에 추가합니다.
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>인증서 정보를 Service Fabric 클러스터 리소스에 추가합니다.
```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD 구성을 추가하여 클라이언트 액세스를 위해 Azure AD 사용

인증서 키를 포함하는 Key Vault를 참조하여 Cluster Resource Manager 템플릿에 Azure AD 구성을 추가합니다. Resource Manager 템플릿 매개 변수 파일(azuredeploy.parameters.json)에 해당 Azure AD 매개 변수 및 값을 추가합니다.

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="populate-the-parameter-file-with-the-values"></a>이 값으로 매개 변수 파일을 채웁니다.
마지막으로, Key Vault 및 Azure AD PowerShell 명령의 출력 값을 매개 변수 파일을 채우는 데 사용합니다.

Azure Service Fabric RM PowerShell 모듈을 사용하려는 경우 클러스터 인증서 정보를 채우지 않아도 됩니다. 시스템에서 클러스터 보안을 위해 자체 서명된 인증서를 생성하려는 경우 Null로만 유지합니다. 

> [!NOTE]
> 이러한 빈 매개 변수 값을 선택하고 채우는 RM 모듈의 경우 매개 변수 이름은 아래 이름과 일치해야 합니다.
>

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

응용 프로그램 인증서를 사용하거나 키 자격 증명 모음에 업로드한 기존 클러스터를 사용하는 경우 이 정보를 가져와서 채워야 합니다. 

RM 모듈에는 사용자에게 Azure AD 구성을 생성하는 기능이 없습니다. 따라서 클라이언트 액세스를 위해 Azure AD를 사용하려는 경우 사용자가 입력해야 합니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

### <a name="test-your-template"></a>템플릿 테스트  
매개 변수 파일로 Resource Manager 템플릿을 테스트하려면 다음 PowerShell 명령을 사용합니다.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

문제가 발생하고 복잡한 메시지가 표시된 경우에 "-Debug"를 옵션으로 사용합니다.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

다음 다이어그램은 Key Vault 및 Azure AD 구성이 Resource Manager 템플릿 어디에 적합한지 보여 줍니다.

![리소스 관리자 종속성 맵][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Azure 리소스 템플릿을 사용하여 클러스터 만들기 

이제 문서의 앞부분에서 설명한 단계를 사용하여 클러스터를 배포할 수 있습니다. 또는 매개 변수 파일의 값이 채워진 경우 [Azure 리소스 템플릿 배포][resource-group-template-deploy]를 직접 사용하여 클러스터를 만들 준비가 되었습니다.

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

문제가 발생하고 복잡한 메시지가 표시된 경우에 "-Debug"를 옵션으로 사용합니다.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>역할에 사용자 할당
클러스터를 나타내는 응용 프로그램을 만들었으면 사용자를 Service Fabric에서 지원하는 역할(읽기 전용 및 관리자)에 할당합니다. [Azure Portal][azure-portal]을 사용하여 역할을 할당할 수 있습니다.

1. Azure Portal의 상단 오른쪽 모서리에서 테넌트를 선택합니다.

    ![테넌트 단추 선택][select-tenant-button]
2. 왼쪽 탭에서 **Azure Active Directory**를 선택한 후 “엔터프라이즈 응용 프로그램”을 선택합니다.
3. “모든 응용 프로그램”을 선택한 다음, 이름이 `myTestCluster_Cluster`인 웹 응용 프로그램을 찾아 선택합니다.
4. **사용자 및 그룹** 탭을 클릭합니다.

    ![사용자 및 그룹 탭][users-and-groups-tab]
5. 새 페이지에서 **사용자 추가** 단추를 클릭하고, 할당할 사용자 및 역할을 선택한 다음, 페이지의 맨 아래에서 **선택** 단추를 클릭합니다.

    ![사용자를 역할에 할당 페이지][assign-users-to-roles-page]
6. 페이지 맨 아래에 있는 **할당** 단추를 클릭합니다.

    ![할당 추가 확인][assign-users-to-roles-confirm]

> [!NOTE]
> 서비스 패브릭의 역할에 대한 자세한 내용은 [서비스 패브릭 클라이언트의 역할 기반 액세스 제어](service-fabric-cluster-security-roles.md)를 참조하세요.
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Azure Active Directory 설정 관련 문제 해결 도움말
Azure AD를 설정하고 사용하는 작업은 매우 어려울 수 있습니다. 문제를 디버깅하기 위해 수행할 수 있는 작업에 대한 일부 포인터는 다음과 같습니다.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer에 인증서를 선택하라는 메시지가 표시 
#### <a name="problem"></a>문제
Service Fabric Explorer에서 Azure AD에 로그인한 후 브라우저가 홈 페이지로 돌아가지만 인증서를 선택하라는 메시지가 표시됩니다.

![SFX 인증서 대화 상자][sfx-select-certificate-dialog]

#### <a name="reason"></a>이유
Azure AD 클러스터 응용 프로그램에서 사용자에게 역할이 할당되지 않았습니다. 이 때문에 Service Fabric 클러스터에서 Azure AD 인증이 실패합니다. Service Fabric Explorer는 인증서 인증으로 대체됩니다.

#### <a name="solution"></a>해결 방법
Azure AD 설정 지침을 따르고 사용자 역할을 할당합니다. 또한 `SetupApplications.ps1`에서처럼 “앱에 액세스하려면 사용자 할당 필요”를 살펴보는 것이 좋습니다.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>“지정된 자격 증명이 올바르지 않다”는 오류가 표시되면서 PowerShell 연결 실패
#### <a name="problem"></a>문제
PowerShell을 사용하여 “AzureActiveDirectory”보안 모드로 클러스터에 연결할 때, Azure AD 로그인 후 연결이 실패하고 “지정된 자격 증명이 올바르지 않다"는 오류가 표시됨

#### <a name="solution"></a>해결 방법
이 솔루션은 이전과 동일합니다.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>로그인할 때 Service Fabric Explorer가 실패를 반환함: "AADSTS50011"
#### <a name="problem"></a>문제
Service Fabric Explorer에서 Azure AD에 로그인할 때 페이지가 "AADSTS50011: 회신 주소 &lt;url&gt;이 응용 프로그램 &lt;guid&gt;에 대해 구성된 회신 주소와 일치하지 않습니다.”라는 실패를 반환합니다.

![SFX 회신 주소가 일치하지 않습니다.][sfx-reply-address-not-match]

#### <a name="reason"></a>이유
Service Fabric Explorer를 나타내는 클러스터(웹) 응용 프로그램이 Azure AD에 대해 인증을 시도하며, 해당 요청의 일부로 리디렉션 반환 URL을 제공합니다. 그렇지만 Azure AD 응용 프로그램 **REPLY URL** 목록에 표시되지 않습니다.

#### <a name="solution"></a>해결 방법
AAD 페이지에서 “앱 등록”을 선택하고, 클러스터 응용 프로그램을 선택한 다음, **회신 URL** 단추를 선택합니다. “회신 URL” 페이지에서 Service Fabric Explorer URL을 목록에 추가하거나 목록의 항목 중 하나를 대체합니다. 마친 후 변경 사항을 저장합니다.

![웹 응용 프로그램 회신 URL][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>PowerShell 통해 Azure AD 인증을 사용하여 클러스터 연결
Service Fabric 클러스터에 연결하려면 다음 PowerShell 명령 예제를 사용합니다.

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Connect-ServiceFabricCluster cmdlet에 대해 알아보려면 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)를 참조하세요.

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>여러 클러스터에서 동일한 Azure AD테넌트를 다시 사용할 수 있나요?
예. 그렇지만 Service Fabric Explorer의 URL을 클러스터(웹) 응용 프로그램에 추가해야 합니다. 그러지 않으면 Service Fabric Explorer가 작동하지 않습니다.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Azure AD가 사용되도록 설정된 경우에도 서버 인증서가 계속 필요한 이유는 무엇인가요?
FabricClient와 FabricGateway는 상호 인증을 수행합니다. Azure AD 인증 중에는 Azure AD 통합은 서버에 클라이언트 ID를 제공하고 서버 인증서가 서버 ID를 확인하는 데 사용됩니다. Service Fabric 인증서에 대한 자세한 내용은 [X.509 인증서 및 Service Fabric][x509-certificates-and-service-fabric]을 참조하세요.

## <a name="next-steps"></a>다음 단계
이제 Azure Active Directory를 사용하여 관리 인증을 제공하는 보안 클러스터가 생겨났습니다. 다음으로, [클러스터에 연결](service-fabric-connect-to-secure-cluster.md)하고 [응용 프로그램 암호를 관리](service-fabric-application-secret-management.md)하는 방법을 알아봅니다.


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

