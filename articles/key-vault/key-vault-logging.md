---
title: Azure Key Vault 로깅 | Microsoft Docs
description: 이 자습서를 사용하여 Azure Key Vault 로깅을 시작할 수 있습니다.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/16/2017
ms.author: barclayn
ms.openlocfilehash: e7dcb3778de31258f4aa3c946ffa214d87cb858a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 로깅
Azure Key Vault는 대부분 지역에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.

## <a name="introduction"></a>소개
하나 이상의 키 자격 증명 모음을 만든 후 키 자격 증명 모음이 액세스되는 방법, 시기 및 사용자를 모니터링하려는 경우가 있습니다. Azure 저장소 계정에 제공하는 정보를 저장하는 키 자격 증명 모음에 대한 로깅을 사용하여 이를 수행할 수 있습니다. **insights-logs-auditevent** 라는 새 컨테이너가 지정된 저장소 계정에 대해 자동으로 만들어지고 여러 키 자격 증명 모음에 대한 로그를 수집하기 위해 이 저장소 계정을 사용할 수 있습니다.

키 자격 증명 모음 작업 후 최대 10분 후에 로깅 정보에 액세스할 수 있습니다. 대부분의 경우 이것보다 빠릅니다.  저장소 계정의 로그 관리에 따라 다릅니다.

* 표준 Azure 액세스 제어 메서드를 사용하여 액세스할 수 있는 사용자를 제한하여 로그를 보호합니다.
* 더 이상 저장소 계정에 유지하지 않으려는 로그를 삭제합니다.

이 자습서를 사용하면 저장소 계정을 만들고 로깅을 활성화하고 수집되는 로깅 정보를 해석하는 Azure Key Vault를 시작하는 데 도움이 됩니다.  

> [!NOTE]
> 이 자습서는 키 자격 증명 모음, 키 또는 암호를 만드는 방법에 대한 지침을 다루지 않습니다. 이에 대한 설명은 [Azure Key Vault 시작](key-vault-get-started.md)을 참조하세요. 또는 플랫폼 간 명령줄 인터페이스 지침에 대한 참조는 [이 해당 자습서](key-vault-manage-with-cli2.md)를 참조하세요.
>
> 현재는 Azure Portal에서 Azure Key Vault를 구성할 수 없습니다. 대신, 이 Azure PowerShell 지침을 사용합니다.
>
>

Azure Key Vault에 대한 개요는 [Azure Key Vault란?](key-vault-whatis.md)

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

* 사용하고 있는 기존 키 자격 증명 모음  
* Azure PowerShell, **최소 버전 1.0.1**. Azure PowerShell을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요. 이미 Azure PowerShell가 설치되어 있고 버전을 알 수 없는 경우, Azure PowerShell 콘솔에서 `(Get-Module azure -ListAvailable).Version`을 입력합니다.  
* 키 자격 증명 모음 로그에 대한 Azure의 충분한 저장소.

## <a id="connect"></a>구독에 연결
Azure PowerShell 세션을 시작하고 다음 명령 사용하여 Azure 계정에 로그인합니다.  

    Connect-AzureRmAccount

팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. Azure PowerShell은 이 계정과 연관된 모든 구독을 받고 기본적으로 첫 번째 구독을 사용합니다.

구독이 여러 개인 경우 Azure Key Vault을 만드는 데 사용된 특정된 하나를 지정해야 합니다. 계정에 대한 구독을 보려면 다음을 입력합니다.

    Get-AzureRmSubscription

그런 다음 로깅하려는 키 자격 증명 모음과 연결된 구독을 지정하려면 다음을 입력합니다.

    Set-AzureRmContext -SubscriptionId <subscription ID>

> [!NOTE]
> 이 과정은 중요한 단계이며 사용자 계정에 여러 구독이 연결된 경우 특히 유용합니다. 이 단계를 건너뛰면 Microsoft.Insights를 등록하는 데 오류가 발생할 수 있습니다.
>   
>

Azure PowerShell 구성에 관한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

## <a id="storage"></a>로그에 대한 새 저장소 계정 만들기
로그에 대해 기존 저장소 계정을 사용할 수 있지만 키 자격 증명 모음 로그 전용 새 저장소 계정을 만듭니다. 나중에 이를 지정해야 하는 경우 편의를 위해 **sa**라는 변수로 정보를 저장합니다.

추가적인 관리 편이성을 위해 키 자격 증명 모음을 포함하는 것과 동일한 리소스 그룹을 사용합니다. [시작 자습서](key-vault-get-started.md)에서 이 리소스 그룹은 **ContosoResourceGroup** 이며 동아시아 위치를 계속해서 사용합니다. 이 값을 적절하게 사용자 고유 값으로 대체합니다.

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'


> [!NOTE]
> 기존 저장소 계정을 사용하려는 경우 사용자 키 자격 증명 모음과 동일한 구독을 사용하고 클래식 배포 모델보다는 Resource Manager 배포 모델을 사용해야 합니다.
>
>

## <a id="identify"></a>로그에 대한 주요 자격 증명 모음 식별
시작 자습서에서 주요 자격 증명 모음 이름은 **ContosoKeyVault**이었으므로 해당 이름을 계속해서 사용하고 **kv**라는 변수에 세부 정보를 저장합니다.

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>로깅 사용
키 자격 증명 모음에 대한 로깅을 사용하려면 새 저장소 계정 및 키 자격 증명 모음용으로 만든 변수와 함께 Set-AzureRmDiagnosticSetting cmdlet을 사용합니다. **-Enabled** 플래그를 **$true**로 설정하고 범주를 AuditEvent(Key Vault 로깅에 대한 유일한 범주)로 설정합니다.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

이에 대한 출력에는 다음이 포함됩니다.

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


저장소 계정에 정보를 저장하는 사용자 키 자격 증명 모음에 로깅을 사용할 수 있는지 확인합니다.

선택적으로 오래된 로그를 자동으로 삭제하는 로그에 대한 보존 정책을 설정할 수도 있습니다. 예를 들어 **-RetentionEnabled** 플래그를 사용하여 보존 정책을 **$true**로 설정하고 **-RetentionInDays** 매개 변수를 **90**으로 설정하므로 90일보다 오래된 로그는 자동으로 삭제됩니다.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

다음이 로깅됩니다.

* 액세스 권한, 시스템 오류 또는 잘못된 요청으로 인해 실패한 요청을 포함하는 모든 인증된 REST API 요청이 로깅됩니다.
* 키 자격 증명 모음 액세스 정책 만들기, 삭제, 설정 및 태그와 같은 키 자격 증명 모음 특성 업데이트를 포함하는 키 자격 증명 모음 자체에 대한 작업.
* 이러한 키 또는 암호 만들기, 수정 또는 삭제를 포함하는 키 자격 증명 모음의 키 및 암호에 대한 작업, 키 서명, 확인, 암호화, 해독, 래핑 및 래핑 해제, 암호 가져오기, 키, 암호 및 버전 나열과 같은 작업.
* 401 응답이 발생하는 인증되지 않은 요청. 예를 들어 전달자 토큰이 없거나 형식이 잘못되거나 만료되거나 토큰이 잘못된 요청이 해당합니다.  

## <a id="access"></a>로그에 액세스
주요 자격 증명 모음 로그는 사용자가 제공한 저장소 계정의 **insights-logs-auditevent** 컨테이너에 저장됩니다. 이 컨테이너에 있는 모든 Blob을 나열하려면 다음을 입력합니다.

먼저, 컨테이너 이름에 대한 변수를 만듭니다. 이 이름은 연습의 나머지 전체에서 사용됩니다.

    $container = 'insights-logs-auditevent'

이 컨테이너에 있는 모든 Blob을 나열하려면 다음을 입력합니다.

    Get-AzureStorageBlob -Container $container -Context $sa.Context
출력은 다음과 유사합니다.

**컨테이너 URI: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**

**Name**

- - -
**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****

이 출력에서 확인할 수 있듯이 Blob은 명명 규칙 **resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json**을 따릅니다.

날짜 및 시간 값은 UTC를 사용합니다.

여러 리소스에 대한 로그를 수집하는 데 동일한 저장소 계정을 사용할 수 있으므로 Blob 이름에 있는 전체 리소스 ID는 필요한 Blob에 액세스하거나 다운로드하는 데 매우 유용합니다. 하지만 그 전에 먼저 모든 Blob을 다운로드하는 방법을 다룹니다.

먼저 Blob을 다운로드할 폴더를 만듭니다. 예: 

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

그런 다음 모든 Blob 목록을 가져옵니다.  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

'Get-AzureStorageBlobContent'를 통해 이 목록을 파이프하여 Blob을 대상 폴더로 다운로드합니다.

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

이 두 번째 명령을 실행할 때 Blob 이름의 **/** 구분 기호는 대상 폴더 아래에 전체 폴더 구조를 만들고 이 구조는 Blob을 파일로 다운로드하고 저장하는 데 사용됩니다.

선택적으로 Blob을 다운로드하려면 와일드카드를 사용합니다. 예: 

* 여러 키 자격 증명 모음이 있고 CONTOSOKEYVAULT3이라는 하나의 키 자격 증명 모음에 대한 로그를 다운로드하려는 경우:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
* 리소스 그룹이 여러 개이고 하나의 리소스 그룹에 대한 로그를 다운로드하려는 경우 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`을(를) 사용합니다.

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
* 2016년 1월의 모든 로그를 다운로드하려는 경우 `-Blob '*/year=2016/m=01/*'`을(를) 사용합니다.

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

이제 로그에 있는 것을 확인할 준비가 되었습니다. 진행하기 전에 알아야 할 Get-AzureRmDiagnosticSetting에 대한 두 개 이상의 매개 변수는 다음과 같습니다.

* 주요 자격 증명 모음 리소스의 진단 설정 상태를 쿼리하려면: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
* 주요 자격 증명 모음 리소스의 로깅을 사용하지 않으려면: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`

## <a id="interpret"></a>Key Vault 로그 해석
개별 Blob은 JSON Blob 형식으로 텍스트로 저장됩니다. 다음은 `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`을 실행하는 예제 로그 항목입니다.

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


다음 표는 필드 이름 및 설명을 나열합니다.

| 필드 이름 | 설명 |
| --- | --- |
| 실시간 |날짜 및 시간(UTC) |
| ResourceId |Azure 리소스 관리자 리소스 ID. Key Vault 로그의 경우 이는 항상 Key Vault 리소스 ID입니다. |
| operationName |다음 표에 설명된 대로 작업의 이름입니다. |
| operationVersion |클라이언트에서 요청한 REST API 버전입니다. |
| 카테고리 |키 자격 증명 모음 로그의 경우 AuditEvent는 단일의 사용 가능한 값입니다. |
| resultType |REST API 요청 결과입니다. |
| resultSignature |HTTP 상태입니다. |
| resultDescription |사용 가능한 경우 결과에 대한 추가 설명입니다. |
| durationMS |밀리초 단위로 REST API 요청을 처리하는 데 걸린 시간입니다. 네트워크 대기 시간을 포함하지 않으므로 클라이언트 쪽에서 측정한 시간은 이 시간과 일치하지 않을 수 있습니다. |
| callerIpAddress |요청한 클라이언트의 IP 주소입니다. |
| CorrelationId |클라이언트가 서비스 쪽(키 자격 증명 모음) 로그를 사용하여 클라이언트 쪽 로그 상관 관계를 지정하도록 전달할 수 있는 선택적 GUID입니다. |
| ID |REST API 요청을 수행할 때 제공된 토큰의 ID입니다. Azure PowerShell cmdlet에서 발생하는 요청의 경우처럼 이는 보통 "사용자", "서비스 주체" 또는 "사용자+appId"의 조합입니다. |
| properties |이 필드는 작업(작업 이름)에 따라 다른 정보가 포함됩니다. 대부분의 경우 클라이언트 정보(클라이언트에서 전달한 useragent 문자열), 정확한 REST API 요청 URI 및 HTTP 상태 코드를 포함합니다. 또한 개체가 요청의 결과로 반환되면(예: KeyCreate 또는 VaultGet) 키 URI(“ID”로), 자격 증명 모음 URI 또는 암호 URI도 포함합니다. |

**operationName** 필드 값은 ObjectVerb 형식입니다. 예: 

* 모든 주요 자격 증명 모음 작업은 `VaultGet`, `VaultCreate` 등의 'Vault`<action>`' 형식입니다.
* 모든 주요 작업은 `KeySign`, `KeyList` 등의 'Key`<action>`' 형식입니다.
* 모든 암호 작업은 `SecretGet`, `SecretListVersions` 등의 'Secret`<action>`' 형식입니다.

다음 표는 operationName 및 해당 REST API 명령을 나열합니다.

| operationName | REST API 명령 |
| --- | --- |
| 인증 |Azure Active Directory 끝점을 통해 |
| VaultGet |[키 자격 증명 모음에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| VaultPut |[키 자격 증명 모음 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultDelete |[키 자격 증명 모음 삭제](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| VaultPatch |[키 자격 증명 모음 업데이트](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[리소스 그룹의 모든 키 자격 증명 모음 목록](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| KeyCreate |[키 만들기](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| KeyGet |[키에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| KeyImport |[자격 증명 모음으로 키 가져오기](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| KeyBackup |[키 Backup](https://msdn.microsoft.com/library/azure/dn878058.aspx). |
| KeyDelete |[키 삭제](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| KeyRestore |[키 복원](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| KeySign |[키로 서명](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| KeyVerify |[키로 확인](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| KeyWrap |[키 래핑](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| KeyUnwrap |[키 래핑 취소](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| KeyEncrypt |[키로 암호화](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| KeyDecrypt |[키로 암호 해독](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| KeyUpdate |[키 업데이트](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| KeyList |[자격 증명 모음에 키 나열](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| KeyListVersions |[키 버전 나열](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| SecretSet |[암호 만들기](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| SecretGet |[암호 가져오기](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| SecretUpdate |[암호 업데이트](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| SecretDelete |[암호 삭제](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| SecretList |[자격 증명 모음에 암호 나열](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| SecretListVersions |[암호 버전 나열](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Log Analytics 사용

Log Analytics에서 Azure Key Vault 솔루션을 사용하여 Azure Key Vault AuditEvent 로그를 검토할 수 있습니다. 설정 방법을 비롯한 자세한 내용은 [Log Analytics의 Azure Key Vault 솔루션](../log-analytics/log-analytics-azure-key-vault.md)을 참조하세요. 또한 이 문서는 먼저 Azure Storage 계정으로 로그를 라우팅하고 해당 위치에서 읽도록 Log Analytics를 구성한 Log Analytics 미리 보기 중 제공된 이전 Key Vault 솔루션에서 마이그레이션해야 할 경우 지침을 포함합니다.

## <a id="next"></a>다음 단계
웹 응용 프로그램에서 Azure Key Vault를 사용하는 자습서는 [웹 응용 프로그램에서 Azure Key Vault 사용](key-vault-use-from-web-application.md)을 참조하세요.

프로그래밍 참조는 [Azure Key Vault 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.

Azure Key Vault의 Azure PowerShell 1.0 cmdlet 목록은 [Azure Key Vault Cmdlet](/powershell/module/azurerm.keyvault/#key_vault)을 참조하세요.

Azure Key Vault을 사용하는 키 회전 및 로그 감사에 대한 자습서는 [종단 간 키 회전 및 감사를 사용하여 Key Vault를 설정하는 방법](key-vault-key-rotation-log-monitoring.md)을 참조하세요.
