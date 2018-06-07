---
title: PowerShell을 사용 하 여 Azure 스택의 주요 자격 증명 모음 관리 | Microsoft Docs
description: PowerShell을 사용 하 여 Azure 스택의 주요 자격 증명 모음을 관리 하는 방법에 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 5e9de401f64a835c286c226bfac88caf5168b96e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>PowerShell을 사용 하 여 Azure 스택의 주요 자격 증명 모음 관리

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

PowerShell을 사용 하 여 Azure 스택의 주요 자격 증명 모음을 관리할 수 있습니다. 키 자격 증명 모음 PowerShell cmdlet을 사용 하는 방법에 알아봅니다.

* 키 자격 증명 모음을 만듭니다.
* 저장 하 고 암호화 키 및 암호를 관리 합니다.
* 사용자 또는 자격 증명 모음에서 작업을 호출 응용 프로그램에 권한을 부여 합니다.

>[!NOTE]
>이 문서에 설명 된 위치 키 자격 증명 모음 PowerShell cmdlet은 Azure PowerShell SDK에 제공 됩니다.

## <a name="prerequisites"></a>필수 조건

* Azure 키 자격 증명 모음 서비스를 포함 하는 제품에 등록 해야 합니다.
* [Azure 스택에 대 한 PowerShell을 설치](azure-stack-powershell-install.md)합니다.
* [Azure 스택 사용자의 PowerShell 환경을 구성](azure-stack-powershell-configure-user.md)합니다.

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>주요 자격 증명 모음 작업에 대 한 테 넌 트 구독을 사용 하도록 설정

주요 자격 증명 모음에 대 한 모든 작업을 실행 하려면 테 넌 트 구독 자격 증명 모음 작업에 대해 설정 되어 있는지 확인 해야 합니다. 자격 증명 모음 작업 활성화 되어 있는지를 확인 하려면 다음 명령을 실행 합니다.

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**출력**

자격 증명 모음 작업에 구독을 사용할 경우 출력에 "RegistrationState"가 "등록 됨"는 키 자격 증명 모음에 있는 모든 리소스 종류에 대 한 표시.

![주요 자격 증명 모음 등록 상태](media/azure-stack-kv-manage-powershell/image1.png)

자격 증명 모음 작업을 사용할 경우에 구독에서 키 자격 증명 모음 서비스를 등록 하려면 다음 명령을 호출 합니다.

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**출력**

등록에 성공 하면 다음 출력이 표시 됩니다.

![등록](media/azure-stack-kv-manage-powershell/image2.png) "구독 'Microsoft.KeyVault' 네임 스페이스를 사용 하는 등록 되지 않았습니다."와 같은 오류가, 주요 자격 증명 모음 명령을 호출 하는 경우 발생할 수 있습니다 오류가 발생 하는 경우 있는지 확인 [주요 자격 증명 모음 리소스 공급자를 사용 하도록 설정](#enable-your-tenant-subscription-for-vault-operations) 이전에 언급 된 지침에 따라 합니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

주요 자격 증명 모음 만들기 전에 리소스 그룹에 있는 주요 자격 증명 모음에 관련 된 리소스의 모든 리소스 그룹을 만듭니다. 다음 명령을 사용 하 여 새 리소스 그룹 만들기:

```PowerShell
New-AzureRmResourceGroup -Name “VaultRG” -Location local -verbose -Force

```

**출력**

![새 리소스 그룹](media/azure-stack-kv-manage-powershell/image3.png)

이제 사용 하 여는 **새로 AzureRMKeyVault** 앞에서 만든 리소스 그룹에서 주요 자격 증명 모음을 만드는 명령입니다. 이 명령은 세 가지 필수 매개 변수를 읽습니다: 리소스 그룹 이름, 주요 자격 증명 모음 이름 및 지리적 위치입니다.

주요 자격 증명 모음을 만들려면 다음 명령을 실행 합니다.

```PowerShell
New-AzureRmKeyVault -VaultName “Vault01” -ResourceGroupName “VaultRG” -Location local -verbose
```

**출력**

![새로운 Key Vault](media/azure-stack-kv-manage-powershell/image4.png)

이 명령의 출력에는 만든 키 자격 증명 모음의 속성이 표시 됩니다. 응용 프로그램에서이 자격 증명이 모음에 액세스할 때 사용 해야 합니다는 **자격 증명 모음 URI** 속성, 즉 "https://vault01.vault.local.azurestack.external"이 예에서 합니다.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Active Directory Federation Services (AD FS) 배포

AD FS 배포에서이 경고를 발생할 수 있습니다: "액세스 정책을 설정 되지 않았습니다. 사용자나 응용 프로그램에 액세스 권한이이 자격 증명이 모음을 사용 하도록 합니다. " 이 문제를 해결 하려면 자격 증명 모음에 대 한 액세스 정책을 사용 하 여 설정 된 [Set-azurermkeyvaultaccesspolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) 명령:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>키 및 암호 관리

자격 증명 모음을 만든 후 만들고 키 및 자격 증명 모음의 암호를 관리 하려면 다음 단계를 사용 합니다.

### <a name="create-a-key"></a>키 만들기

사용 하 여는 **Add-azurekeyvaultkey** 을 만들거나 키 자격 증명 모음에는 소프트웨어 보호 키를 가져오는 명령입니다.

```PowerShell
Add-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01” -verbose -Destination Software
```

**대상** 소프트웨어 보호 키 임을 지정 하려면 매개 변수를 사용 합니다. 키 성공적으로 만들어지면 명령이 만든된 키의 세부 정보를 출력 합니다.

**출력**

![새 키](media/azure-stack-kv-manage-powershell/image5.png)

이제 해당 URI를 사용 하 여 만든된 키를 참조할 수 있습니다. 을 만들거나 기존 키와 동일한 이름을 가진 키를 가져올 경우 원래 키 새 키에 지정 된 값으로 업데이트 됩니다. 키의 버전 별로 URI를 사용 하 여 이전 버전에 액세스할 수 있습니다. 예: 

* 사용 하 여 "https://vault10.vault.local.azurestack.external:443/keys/key01" 항상 현재 버전을 가져올 수 있습니다.
* 사용 하 여 "https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a"이 특정 버전을 가져올 수 있습니다.

### <a name="get-a-key"></a>키 가져오기

사용 하 여는 **Get AzureKeyVaultKey** 키와 해당 세부 정보를 읽으려면 명령입니다.

```PowerShell
Get-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01”
```

### <a name="create-a-secret"></a>비밀 만들기

사용 하 여는 **집합 AzureKeyVaultSecret** 을 만들거나 업데이트할 암호 자격 증명 모음에 명령 합니다. 암호는 존재 하지 않는 경우에 생성 됩니다. 암호의 새 버전이 이미 있는 경우에 생성 됩니다.

```PowerShell
$secretvalue = ConvertTo-SecureString “User@123” -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01” -SecretValue $secretvalue
```

**출력**

![비밀 만들기](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>암호를 받을

사용 하 여는 **Get AzureKeyVaultSecret** 명령을 키 자격 증명 모음에 암호를 읽을 수 있습니다. 이 명령은 모든 반환할 수 있습니다 또는 특정 버전의 암호입니다.

```PowerShell
Get-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01”
```

키 및 암호를 만든 후 사용 하는 외부 응용 프로그램 권한을 부여할 수 있습니다.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>키 또는 암호를 사용 하도록 응용 프로그램 권한 부여

사용 하 여는 **Set-azurermkeyvaultaccesspolicy** 키 또는 암호 키 자격 증명 모음에 액세스 하는 응용 프로그램 권한을 부여 하는 명령입니다.
다음 예제에서는 자격 증명 모음 이름은 *ContosoKeyVault* 을 인증 하려면 응용 프로그램에의 클라이언트 ID 및 *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*합니다. 응용 프로그램을 인증 하려면 다음 명령을 실행 합니다. 선택적으로 지정할 수는 **PermissionsToKeys** 매개 변수를 사용자, 응용 프로그램 또는 보안 그룹에 대 한 사용 권한을 설정 합니다.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

자격 증명 모음에서 암호를 읽을 수 동일 응용 프로그램에 권한을 부여 하려면 다음 cmdlet을 실행 합니다.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>다음 단계

* [주요 자격 증명 모음에 저장 된 암호는 VM을 배포 합니다.](azure-stack-kv-deploy-vm-with-secret.md)
* [주요 자격 증명 모음에 저장 된 인증서로는 VM을 배포 합니다.](azure-stack-kv-push-secret-into-vm.md)
