---
title: Azure Key Vault 시작 | Microsoft Docs
description: 이 자습서를 사용하면 Azure Key Vault로 시작하여 확정된 컨테이너를 Azure에 만들고 Azure에서 암호화 키와 비밀을 저장하고 관리하는 데 도움이 됩니다.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: 58a283f1ce5bd2fd78c4fa2038c3998aea1598c9
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="get-started-with-azure-key-vault"></a>Azure Key Vault 시작
이 문서는 PowerShell을 사용하여 Azure Key Vault를 시작할 수 있도록 하고 다음 활동을 따르는 과정을 안내합니다.
- Azure에서 확정된 컨테이너(자격 증명 모음)를 만드는 방법.
- Azure에서 암호화 키 및 비밀을 저장하고 관리하기 위해 KeyVault를 사용하는 방법.
- 응용 프로그램이 해당 키 또는 암호를 사용할 수 있는 방법.

Azure Key Vault는 대부분 지역에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.

> [!NOTE]
> 이 문서는 Azure 응용 프로그램을 작성하는 방법에 대한 지침을 다루지 않습니다. 이 단계에 대한 [Azure Key Vault 샘플 응용 프로그램](https://www.microsoft.com/download/details.aspx?id=45343)을 사용할 수 있습니다.

플랫폼 간 명령줄 인터페이스 지침은 [이 해당 자습서](key-vault-manage-with-cli2.md)를 참조하세요.

## <a name="requirements"></a>요구 사항
문서를 진행하기 전에 다음이 있는지 확인합니다.

- **Azure 구독**. 아직 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
- **Microsoft Azure PowerShell**, **최소 버전 1.1.0**. Azure PowerShell을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요. 이미 Azure PowerShell가 설치되어 있고 버전을 알 수 없는 경우, Azure PowerShell 콘솔에서 `(Get-Module azure -ListAvailable).Version`을 입력합니다. Azure PowerShell 버전 0.9.1 ~ 0.9.8이 설치된 경우, 일부 약간의 변경이 있지만 이 자습서를 계속 사용할 수 있습니다. 예를 들어, `Switch-AzureMode AzureResourceManager` 명령을 사용해야 하고 Azure Key Vault 명령의 일부가 변경되었습니다. 버전 0.9.1 ~ 0.9.8에 대한 키 자격 증명 모음 목록은 [Azure Key Vault Cmdlets](/powershell/module/azurerm.keyvault/#key_vault)을 참조하세요.
- **Key Vault를 사용하도록 구성할 수 있는 응용 프로그램**. 샘플 응용 프로그램은 [Microsoft 다운로드 센터](http://www.microsoft.com/download/details.aspx?id=45343)에서 사용할 수 있습니다. 자세한 내용은 해당 **추가 정보** 파일을 참조하세요.

>[!NOTE]
이 문서에서는 PowerShell 및 Azure에 대한 기본적으로 이해하고 있다고 가정합니다. PowerShell에 대한 자세한 내용은 [Windows PowerShell 시작](https://technet.microsoft.com/library/hh857337.aspx)을 참조하세요.

이 자습서에 나오는 cmdlet에 대한 자세한 도움말을 보려면 **Get-Help** cmdlet을 사용합니다.

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
예를 들어 **Connect-AzureRmAccount** cmdlet에 대한 도움말을 보려면 다음과 같이 입력합니다.

```PowerShell
Get-Help Connect-AzureRmAccount -Detailed
```

Microsoft Azure PowerShell에서 Azure Resource Manager 배포 모델과 친숙해지도록 다음 문서도 확인하세요.

* [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview)
* [리소스 관리자로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)

## <a id="connect"></a>구독에 연결
Azure PowerShell 세션을 시작하고 다음 명령 사용하여 Azure 계정에 로그인합니다.  

```PowerShell
Connect-AzureRmAccount
```

>[!NOTE]
 Azure의 특정 인스턴스를 사용할 경우 환경 매개 변수를 사용합니다. 예:  
 ```powershell
 Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. Azure PowerShell은 이 계정과 연관된 모든 구독을 가져와서 기본적으로 첫 번째 구독을 사용합니다.

여러 구독이 있는 경우 Azure Key Vault에 사용할 특정 하나를 지정하려면, 다음을 입력하여 계정에 대한 구독을 표시합니다.

```powershell
Get-AzureRmSubscription
```

그런 후, 사용할 구독을 지정하려면 다음을 입력합니다.

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

Azure PowerShell 구성에 관한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

## <a id="resource"></a>새 리소스 그룹 만들기
Azure 리소스 관리자를 사용하면 관련된 모든 리소스는 리소스 그룹의 내부에 만들어집니다. 이 자습서에서는 **ContosoResourceGroup** 이라는 새 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>키 자격 증명 모음 만들기
[New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) cmdlet을 사용하여 키 자격 증명 모음을 만듭니다. 이 cmdlet에는 3개의 필수 매개 변수인 **리소스 그룹 이름**, **키 자격 증명 모음 이름** 및 **지리적 위치**가 있습니다.

예를 들어, 다음을 사용하는 경우:
- **ContosoKeyVault**의 자격 증명 모음 이름.
- **ContosoResourceGroup**의 리소스 그룹 이름.
- **미국 동부**의 위치.

다음을 입력합니다.

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![Key Vault 만들기 명령이 완료된 후 출력](./media/key-vault-get-started/output-after-creating-keyvault.png)

이 cmdlet의 출력에서는 만든 자격 증명 모음의 속성을 보여줍니다. 가장 중요한 두 개의 속성은 다음과 같습니다.

* **자격 증명 모음 이름**: 예제에서는 **ContosoKeyVault**입니다. 다른 키 자격 증명 모음 cmdlet에 대해 이 이름을 사용합니다.
* **자격 증명 모음 URI**: 이 예에서는 https://contosokeyvault.vault.azure.net/입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 응용 프로그램은 URI를 사용해야 합니다.

Azure 계정은 이제 이 키 자격 증명 모음에서 모든 작업을 수행할 권한을 가지게 됩니다. 아직 다른 사람은 권한이 없습니다.

> [!NOTE]
> 새로운 키 자격 증명 모음을 만들려고 할 때 **구독은 'Microsoft.KeyVault' 네임스페이스를 사용하도록 등록되지 않았습니다**라는 오류가 나타날 수 있습니다. 해당 메시지가 나타나면 `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`을 실행합니다. 등록을 성공적으로 완료한 후 New-AzureRmKeyVault 명령을 다시 실행할 수 있습니다. 자세한 내용은 [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider)를 참조하세요.
>
>

## <a id="add"></a>키 또는 비밀을 암호 키 자격 증명 모음에 추가
Key Vault 및 키 또는 비밀과 상호 작용해야 하는 몇 가지 다른 방법이 있습니다.

### <a name="azure-key-vault-generates-a-software-protected-key"></a>Azure Key Vault는 소프트웨어 보호된 키를 생성합니다.

소프트웨어 보호 키를 만들도록 Azure Key Vault를 사용하려면, [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) cmdlet을 사용하여 다음을 입력합니다.

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
이 키에 대한 URI를 보려면 다음을 입력합니다.
```powershell
$key.id
```

해당 URI를 사용하여 Azure Key Vault에 만들거나 업로드한 키를 참조할 수 있습니다. 현재 버전을 얻으려면 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**를 사용하고 이 특정 버전을 얻으려면 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**을 사용합니다.  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>Azure Key Vault로 기존 PFX 파일 가져오기

Azure Key Vault에 업로드 하려는 pfx 파일에 저장된 기존 키의 경우 단계가 다릅니다. 예: 
- PFX 파일에 기존 소프트웨어 보호된 키가 있다면
- Pfx 파일은 softkey.pfx라는 이름이 지정됩니다. 
- 파일은 C 드라이브에 저장됩니다.

다음을 입력할 수 있습니다.

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

키를 .PFX 파일에서 가져오려면 다음을 입력합니다. 키 자격 증명 모음 서비스의 소프트웨어에서 키를 보호합니다.

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

이 키에 대한 URI를 표시하려면 다음을 입력합니다.

```powershell
$Key.id
```
사용자의 키를 보려면 다음을 입력 합니다. 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
포털에서 PFX 파일의 속성을 보려 한다면 아래에 표시된 이미지와 비슷한 내용이 표시됩니다.

![포털에서 인증서를 보여주는 방법](./media/key-vault-get-started/imported-pfx.png)
### <a name="to-add-a-secret-to-azure-key-vault"></a>Azure Key Vault에 비밀을 추가하려면

암호가 SQLPassword이고 Azure Key Vault에 대한 Pa$$w0rd 값이 있는 비밀을 자격 증명 모음에 추가하려면 먼저 다음을 입력하여 Pa$$w0rd 값을 비밀 문자열로 변환합니다.

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

그런 다음 입력합니다.

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


이제 해당 URI를 사용하여 Azure Key Vault에 추가한 이 암호를 참조할 수 있습니다. 항상 현재 버전을 얻으려면 **https://ContosoVault.vault.azure.net/secrets/SQLPassword**를 사용하고 이 특정 버전을 얻으려면 **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**를 사용합니다.

이 비밀에 대한 URI를 표시하려면 다음을 입력합니다.

```powershell
$secret.Id
```
비밀을 보려면 `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`를 입력하거나 또는 포털에서 비밀을 볼 수 있습니다.

![secret](./media/key-vault-get-started/secret-value.png)

비밀에 들어 있는 값을 일반 텍스트로 보려면:
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
이제, 사용자 키 자격 증명 모음 또는 비밀은 응용 프로그램에서 사용할 준비가 되었습니다. 응용 프로그램을 사용하여 권한을 부여해야 합니다.  

## <a id="register"></a>Azure Active Directory에 응용 프로그램 등록
이 단계는 일반적으로 별도의 컴퓨터에서 개발자가 수행할 수 있습니다. Azure Key Vault에 국한되지 않습니다. Azure Active Directory를 사용하여 응용 프로그램을 등록하는 자세한 단계를 알아보려면 [Azure Active Directory를 사용하여 응용 프로그램 통합](../active-directory/develop/active-directory-integrating-applications.md) 또는 [포털을 사용하여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 및 서비스 주체 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md)라는 문서를 검토해야 합니다.

> [!IMPORTANT]
> 이 단계에서 등록된 자습서, 계정, 해당 자격 증명 모음 및 응용 프로그램을 완료하려면, 모두 동일한 Azure 디렉터리에 있어야 합니다.


자격 증명 모음 키를 사용하는 응용 프로그램은 Azure Active Directory에서 토큰을 사용하여 인증해야 합니다. 이렇게 하려면 응용 프로그램의 소유자가 Azure Active Directory에 먼저 응용 프로그램을 등록해야 합니다. 등록 끝에 응용 프로그램 소유자는 다음 값을 가져옵니다.

- **응용 프로그램 ID** 
- **인증 키**(또한 공유 암호라고도 함) 

응용 프로그램은 토큰을 가져올 Azure Active Directory에 이 두 값 모두가 있어야 합니다. 응용 프로그램 구성 방식은 응용 프로그램에 따라 달라집니다. [Key Vault 샘플 응용 프로그램](https://www.microsoft.com/download/details.aspx?id=45343)의 경우, 응용 프로그램 소유자는 app.config 파일에서 이러한 값을 설정합니다.


Azure Active Directory에 응용 프로그램을 등록하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **앱 등록**을 클릭합니다. 앱 등록을 볼 수 없다면 **더 많은 서비스**를 클릭하여 찾습니다.  
>[!NOTE]
주요 자격 증명 모음을 만든 Azure 구독을 포함하는 동일한 디렉터리를 선택해야 합니다. 
3. **새 응용 프로그램 등록**을 클릭합니다.
4. **만들기** 블레이드에 응용 프로그램의 이름을 제공한 다음 **웹 응용 프로그램 및/또는 Web API**(기본값)를 선택하고 웹 응용 프로그램에 대한 **로그온 URL**을 지정합니다. 현재 이 정보가 없다면 이 단계에 대해 만들 수 있습니다(예를 들어 http://test1.contoso.com을 지정할 수 있습니다). 이러한 사이트가 존재하는 경우 중요하지 않습니다. 

    ![새 응용 프로그램 등록](./media/key-vault-get-started/new-application-registration.png)
    >[!WARNING]
    **웹 응용 프로그램 및/또는 웹 API**를 선택했는지 확인합니다. 선택하지 않았다면 설정 아래에 **키** 옵션이 표시되지 않습니다.

5. **만들기** 단추를 클릭합니다.
6. 앱 등록을 완료하면 등록된 앱 목록을 볼 수 있습니다. 방금 등록한 앱을 찾아 클릭합니다.
7. **등록된 앱** 블레이드를 클릭하고 **응용 프로그램 ID**를 복사합니다.
8. **모든 설정**을 클릭합니다.
9. **설정** 블레이드에서 **키**를 클릭합니다.
9. **키 설명** 상자에 설명을 입력한 다음 기간을 선택하고 **저장**을 클릭합니다. 페이지가 새로 고쳐지고 이제 키 값을 표시합니다. 
10. 자격 증명 모음에 사용 권한을 설정하려면 다음 단계에서 **응용 프로그램 ID** 및 **키** 정보를 사용합니다.

## <a id="authorize"></a>키 또는 비밀을 사용하여 응용 프로그램 권한 부여
자격 증명 모음에서 키 또는 비밀에 액세스하도록 응용 프로그램을 인증하는 방법은 두 가지입니다.

### <a name="using-powershell"></a>PowerShell 사용
PowerShell을 사용하려면 [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet을 사용합니다.

예를 들어 자격 증명 모음 이름이 **ContosoKeyVault** 이고 권한을 부여하려는 응용 프로그램의 클라이언트 ID가 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed이며 자격 증명 모음에 있는 키로 서명하고 암호 해독하도록 응용 프로그램에 권한을 부여하려면 다음을 실행합니다.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

자격 증명 모음에서 기밀 정보를 읽기 위해 동일한 응용 프로그램에 권한을 부여하려면 다음을 실행합니다.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```
### <a name="using-the-azure-portal"></a>Azure Portal 사용
키 또는 암호를 사용하도록 응용 프로그램의 권한을 변경하려면:
1. 키 자격 증명 모음 리소스 블레이드에서 **액세스 정책**을 선택합니다.
2. 블레이드 맨 위에서 [+ 새로 추가] 단추를 클릭합니다.
3. **주체 선택**을 클릭하여 앞에서 만든 응용 프로그램을 선택합니다.
4. **키 권한** 드롭다운에서 "암호 해독" 및 "서명"을 선택하여 응용 프로그램이 자격 증명 모음의 키를 해독하고 서명하도록 권한을 부여합니다.
5. **비밀 권한** 드롭다운에서 "가져오기"를 선택하여 응용 프로그램이 자격 증명 모음에서 비밀을 읽도록 허용합니다.

## <a id="HSM"></a>하드웨어 보안 모듈(HSM)로 작업함
추가된 보증을 위해, HSM 경계를 절대로 떠나지 않는 하드웨어 보안 모듈(HSMs)에서 키를 가져오거나 생성할 수 있습니다. HSM은 FIPS 140-2 Level 2 유효성 검사가 적용됩니다. 이 요구 사항이 사용자에게 적용되지 않는 경우, 이 섹션을 건너뛰고 [키 자격 증명 모음 및 연결된 키와 암호 삭제](#delete)로 이동합니다.

이러한 HSM 보호 키를 만들려면 [HSM 보호 키를 지원하는 Azure Key Vault 프리미엄 서비스 계층](https://azure.microsoft.com/pricing/free-trial/)을 사용해야 합니다. 또한 이 기능은 Azure 중국에 사용할 수 없습니다.

주요 자격 증명 모음을 만들 때 **-SKU** 매개 변수를 추가합니다.

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```


이 주요 자격 증명 모음에 소프트웨어 보호 키(이전에 표시된 대로)와 HSM 보호되는 키를 추가할 수 있습니다. HSM으로 보호되는 키를 만들려면 **-Destination** 매개 변수를 'HSM'으로 설정합니다.

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

다음 명령을 사용하여 사용자 컴퓨터의 .PFX 파일에서 키를 가져올 수 있습니다. 이 명령은 키 자격 증명 모음 서비스에서 HSM으로 키를 가져옵니다.

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

다음 명령은 "bring your own key"(BYOK) 패키지를 가져옵니다. 이 시나리오를 사용하면 로컬 HSM에 키를 생성하고 키가 HSM 경계를 벗어나지 않고 이를 주요 자격 증명 모음 서비스의 HSM에 전달합니다.

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

이 BYOK 패키지를 생성하는 방법에 대한 자세한 내용은 [Azure Key Vault용으로 HSM 보호 키를 생성하여 전송하는 방법](key-vault-hsm-protected-keys.md)을 참조하세요.

## <a id="delete"></a>키 자격 증명 모음 및 연결된 키와 비밀 삭제
주요 자격 증명 모음 및 이를 포함하는 키나 암호가 더 이상 필요하지 않은 경우 [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault) cmdlet을 사용하여 주요 자격 증명 모음을 삭제할 수 있습니다.

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

또는 키 자격 증명 모음 및 해당 그룹에 포함된 다른 모든 리소스 및 키를 포함하는 전체 Azure 리소스 그룹을 삭제할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>기타 Azure PowerShell Cmdlet
Azure Key Vault를 관리하는 데 유용한 기타 명령은 다음과 같습니다.

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: 이 명령은 모든 키와 선택한 속성을 테이블 형식으로 가져옵니다.
- `$Keys[0]`:이 명령은 지정된 키에 대한 속성의 전체 목록을 표시합니다.
- `Get-AzureKeyVaultSecret`: 이 명령은 모든 비밀 이름과 선택한 속성을 테이블 형식으로 표시합니다.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: 특정 키를 제거하는 방법 예
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: 특정 비밀을 제거하는 방법 예

## <a name="next-steps"></a>다음 단계

- Azure Key Vault에 대한 개요는 [Azure Key Vault란?](key-vault-whatis.md)
- 주요 자격 증명 모음이 사용되는 방식을 보려면 [Azure Key Vault 로깅](key-vault-logging.md)을 참조하세요.
- 웹 응용 프로그램에서 Azure Key Vault를 사용하는 후속 자습서는 [웹 응용 프로그램에서 Azure Key Vault 사용](key-vault-use-from-web-application.md)을 참조하세요.
- 프로그래밍 참조는 [Azure Key Vault 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.
- Azure Key Vault에 대한 최신 Azure PowerShell cmdlet의 목록은 [Azure Key Vault Cmdlet](/powershell/module/azurerm.keyvault/#key_vault)을 참조하세요.
