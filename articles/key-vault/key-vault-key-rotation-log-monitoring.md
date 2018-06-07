---
title: 종단 간 키 회전 및 감사를 사용하여 Azure Key Vault 설정 | Microsoft Docs
description: 키 회전 및 Key Vault 로그의 모니터링을 통해 설정을 가져오는 데 이 방법을 사용합니다.
services: key-vault
documentationcenter: ''
author: swgriffith
manager: mbaldwin
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: stgriffi
ms.openlocfilehash: 01f1f719545b554b22ef79b38f95087341c65e83
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>종단 간 키 회전 및 감사를 사용하여 Azure Key Vault 설정
## <a name="introduction"></a>소개
Key Vault를 만든 후에는 키와 비밀을 저장하는 데 Key Vault를 사용할 수 있게 됩니다. 사용자 응용 프로그램에서는 키 또는 암호 정보를 더 이상 유지할 필요가 없으며 대신, 필요에 따라 주요 자격 증명 모음에서 요청합니다. 이렇게 하면 응용 프로그램의 동작에 영향을 주지 않고 키 및 비밀을 업데이트할 수 있으며 키 및 비밀 관리 동작에 대한 다양한 가능성이 열립니다.

>[!IMPORTANT]
> 이 문서의 예제는 설명 용도로만 제공됩니다. 프로덕션 용도로는 사용하지 말아야 합니다. 

이 문서에서는 Azure Key Vault를 사용하여 비밀(이 예에서는 응용 프로그램에서 액세스하는 Azure Storage 계정 키)을 저장하는 예제를 안내합니다. 또한 해당 저장소 계정 키의 예약된 회전 구현에 대해서도 살펴봅니다. 마지막으로, 예기치 않은 요청이 있을 때 Key Vault 감사 로그를 모니터하고 경고를 생성하는 방법도 살펴봅니다.

> [!NOTE]
> 이 자습서는 Key Vault의 초기 설정에 대해서는 자세히 다루지 않습니다. 이에 대한 설명은 [Azure Key Vault 시작](key-vault-get-started.md)을 참조하세요. 플랫폼 간 명령줄 인터페이스 지침은 [CLI를 사용하여 Key Vault 관리](key-vault-manage-with-cli2.md)를 참조하세요.
>
>

## <a name="set-up-key-vault"></a>주요 자격 증명 모음 설정
응용 프로그램을 통해 Azure Key Vault에서 비밀을 검색하려면 먼저 비밀을 만들어 Key Vault에 업로드해야 합니다. 이렇게 하려면 Azure PowerShell 세션을 시작하고 다음 명령 사용하여 Azure 계정에 로그인합니다.

```powershell
Connect-AzureRmAccount
```

팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. PowerShell이 이 계정과 연결된 모든 구독을 가져옵니다. PowerShell은 기본적으로 첫 번째 구독을 사용합니다.

구독이 여러 개인 경우 Key Vault을 만드는 데 사용된 구독을 지정해야 할 수도 있습니다. 계정에 대한 구독을 보려면 다음을 입력합니다.

```powershell
Get-AzureRmSubscription
```

로깅하려는 Key Vault와 연결된 구독을 지정하려면 다음을 입력합니다.

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

이 문서에서는 저장소 계정 키를 비밀로 저장하는 방법을 설명하므로 해당 저장소 계정 키를 가져와야 합니다.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

비밀(이 예에서는 저장소 계정 키)을 검색한 후에는 보안 문자열로 변환한 후 해당 값을 사용하여 Key Vault에 비밀을 만들어야 합니다.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
그런 다음, 만든 비밀의 URI를 가져옵니다. 이 URI는 이후에 비밀을 검색하기 위해 Key Vault를 호출하는 단계에서 사용됩니다. 다음 PowerShell 명령을 실행하고 비밀 URI인 ID 값을 기록해 둡니다.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>응용 프로그램 설정
비밀이 저장되었으니 이제 코드를 사용하여 비밀을 검색하고 사용할 수 있습니다. 이 작업을 수행하려면 몇 가지 단계가 필요합니다. 가장 중요한 첫 번째 단계는 응용 프로그램을 Azure Active Directory에 등록한 후 응용 프로그램의 요청을 허용하도록 Key Vault에 응용 프로그램을 알리는 것입니다.

> [!NOTE]
> 응용 프로그램은 Key Vault와 동일한 Azure Active Directory에서 만들어야 합니다.
>
>

Azure Active Directory의 응용 프로그램 탭을 엽니다.

![Azure Active Directory에서 응용 프로그램 열기](./media/keyvault-keyrotation/AzureAD_Header.png)

**추가**를 선택하여 응용 프로그램을 Azure Active Directory에 추가합니다.

![[추가] 선택](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

응용 프로그램 유형을 **웹 응용 프로그램 및/또는 웹 API**로 두고 응용 프로그램 이름을 지정합니다.

![응용 프로그램 이름 지정](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

응용 프로그램에 **로그온 URL** 및 **앱 ID URI**를 제공합니다. 이 데모에서는 사용자가 원하는 어떤 것이라도 가능하며 필요한 경우 나중에 변경할 수 있습니다.

![필요한 URI 제공](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

응용 프로그램이 Azure Active Directory에 추가되면 응용 프로그램 페이지로 이동됩니다. **구성** 탭을 클릭한 후 **클라이언트 ID** 값을 찾아 복사합니다. 이후 단계에서 클라이언트 ID를 기록해 둡니다.

다음으로, Azure Active Directory와 상호 작용할 수 있도록 응용 프로그램에 대한 키를 생성합니다. **구성** 탭의 **키** 섹션 아래에서 만들 수 있습니다. 이후 단계에서 사용할 수 있도록 Azure Active Directory 응용 프로그램에서 새로 생성된 키를 기록해 둡니다.

![Azure Active Directory 앱 키](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

응용 프로그램에서 Key Vault로 모든 호출을 설정하기 전에 Key Vault에 응용 프로그램 및 해당 권한에 대한 정보를 알려야 합니다. 다음 명령은 Azure Active Directory 앱에서 Key Vault 이름 및 클라이언트 ID를 가져와 Key Vault에 응용 프로그램에 대한 **Get** 액세스 권한을 부여합니다.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

이제 응용 프로그램 호출의 빌드를 시작할 준비가 되었습니다. 응용 프로그램에서 Azure Key Vault 및 Azure Active Directory와 상호 작용하는 데 필요한 NuGet 패키지를 먼저 설치해야 합니다. Visual Studio 패키지 관리자 콘솔에서 다음 명령을 입력합니다. 이 문서를 작성할 당시 Azure Active Directory 패키지의 최신 버전은 3.10.305231913이므로 최신 버전을 확인하고 그에 따라 업데이트해야 할 수 있습니다.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

응용 프로그램 코드에서 Azure Active Directory 인증에 대한 메서드를 보유할 클래스를 만듭니다. 이 예제에서 이 클래스는 **Utils**입니다. 다음 using 문을 추가합니다.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

다음으로, Azure Active Directory에서 JWT 토큰을 검색하는 다음 메서드를 추가합니다. 유지 관리를 위해 하드 코딩된 문자열 값을 웹 또는 응용 프로그램 구성으로 이동하려고 할 수 있습니다.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Key Vault를 호출하고 비밀 값을 검색하는 데 필요한 코드를 추가할 수 있습니다. 먼저 다음 using 문을 추가해야 합니다.

```csharp
using Microsoft.Azure.KeyVault;
```

Key Vault를 호출하고 비밀을 검색하는 메서드 호출을 추가합니다. 이 메서드에 이전 단계에서 저장한 비밀 URI를 입력합니다. 앞에서 만든 **Utils** 클래스의 **GetToken** 메서드를 사용합니다.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

응용 프로그램을 실행하면 Azure Active Directory에 인증된 후 Azure Key Vault에서 비밀 값을 검색합니다.

## <a name="key-rotation-using-azure-automation"></a>Azure Automation을 사용하여 키 회전
Azure Key Vault 암호 정보로 저장하는 값을 위한 회전 전략을 구현하는 다양한 옵션이 있습니다. 수동 프로세스의 일부로 비밀을 회전할 수 있으며 API 호출을 활용하여 프로그래밍 방식으로 회전하거나 Automation 스크립트 방식으로 회전할 수 있습니다. 이 문서의 목적에 따라 Azure Automation과 결합된 Azure PowerShell을 사용하여 Azure Storage 계정 액세스 키를 변경합니다. 그런 다음 해당 새 키를 사용하여 Key Vault 비밀을 업데이트합니다.

Azure Automation이 Key Vault의 비밀 값을 설정할 수 있도록 허용하려면 Azure Automation 인스턴스를 설정할 때 AzureRunAsConnection이라는 이름으로 생성된 연결에 대한 클라이언트 ID를 가져와야 합니다. Azure Automation 인스턴스에서 **자산**을 선택하여 이 ID를 가져올 수 있습니다. 여기에서 **연결**을 선택한 후 **AzureRunAsConnection** 서비스 사용자를 선택합니다. **응용 프로그램 ID**를 기록해 둡니다.

![Azure Automation 클라이언트 ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

**자산**에서 **모듈**을 선택합니다. **모듈**에서 **갤러리**를 선택한 후 다음 각 모듈의 업데이트된 버전을 검색하여 **가져오기**합니다.

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> 이 문서를 작성할 당시 위에 명시된 모듈만 다음 스크립트에 대해 업데이트되어야 합니다. 자동화 작업이 실패하는 것으로 확인되면 필요한 모든 모듈과 해당 종속성을 가져왔는지 확인해야 합니다.
>
>

Azure Automation 연결에 대한 응용 프로그램 ID를 검색한 후에는 이 응용 프로그램에서 Key Vault의 비밀을 업데이트할 권한이 있음을 Key Vault에 알려야 합니다. 다음 PowerShell 명령을 사용하여 이 작업을 수행할 수 있습니다.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

다음으로 Azure Automation 인스턴스 아래에서 **Runbooks**을 선택하고 **Runbook 추가**를 선택합니다. **빠른 생성**을 선택합니다. runbook의 이름을 지정하고 runbook 유형으로 **PowerShell**을 선택합니다. 설명을 추가하는 옵션이 있습니다. 마지막으로 **만들기**를 클릭합니다.

![runbook 만들기](./media/keyvault-keyrotation/Create_Runbook.png)

새 runbook의 편집기 창에 다음 PowerShell 스크립트를 붙여넣습니다.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

편집기 창에서 **테스트 창**을 선택하여 스크립트를 테스트합니다. 스크립트가 오류 없이 실행되면 **게시**를 선택한 후 runbook 구성 창에서 runbook에 대한 일정을 다시 적용할 수 있습니다.

## <a name="key-vault-auditing-pipeline"></a>Key Vault 감사 파이프라인
Key Vault를 설정할 때 감사를 켜서 Key Vault에 대해 발생하는 액세스 요청에 대한 로그를 수집할 수 있습니다. 이러한 로그는 지정된 Azure Storage 계정에 저장되며 끌어내기, 모니터링 및 분석이 가능합니다. 다음 시나리오에서는 웹앱의 앱 ID가 일치하는 앱이 Key Vault에서 검색하는 경우 Azure Functions, Azure Logic Apps 및 Key Vault를 사용하여 전자 메일을 보내는 파이프라인을 만듭니다.

먼저 Key Vault에서 로깅을 사용하도록 설정해야 합니다. 이 작업은 다음 PowerShell 명령을 통해 수행할 수 있습니다(전체 설명은 [key-vault-로깅](key-vault-logging.md)에서 확인할 수 있음).

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

로깅이 활성화되면 감사 로그가 지정된 저장소 계정으로 수집하기 시작합니다. 이러한 로그에는 Key Vault를 어떻게, 언제, 누가 액세스하는지에 대한 이벤트가 포함됩니다.

> [!NOTE]
> Key Vault 작업 후 10분 내에 로깅 정보에 액세스할 수 있습니다. 이 시간은 일반적으로 10분보다 짧습니다.
>
>

다음은 [Azure Service Bus 큐를 만드는](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)단계입니다. 여기에 Key Vault 감사 로그가 푸시됩니다. 감사 로그 메시지가 큐에 있으면 논리 앱이 감사 로그 메시지를 선택하여 작업을 수행합니다. 다음 단계를 수행하여 Service Bus를 만듭니다.

1. Service Bus 네임스페이스를 만듭니다(사용할 네임스페이스가 이미 있는 경우 2단계로 건너뜀).
2. Azure Portal에서 Service Bus를 찾아 안에 큐를 만들 네임스페이스를 선택합니다.
3. **리소스 만들기**, **엔터프라이즈 통합**, **Service Bus**를 차례로 선택한 다음, 필요한 세부 사항을 입력합니다.
4. 네임스페이스를 선택하고 **연결 정보**를 클릭하여 Service Bus 연결 정보를 선택합니다. 다음 섹션에서 이 정보가 필요합니다.

다음으로, [Azure 함수를 만들어](../azure-functions/functions-create-first-azure-function.md) 저장소 계정 내에서 Key Vault 로그를 폴링하고 새 이벤트를 선택합니다. 그러면 일정에 따라 트리거되는 함수가 됩니다.

Azure 함수를 만들려면 **리소스 만들기**를 선택하고 마켓플레이스에서 _함수 앱_을 검색한 다음, **만들기**를 클릭합니다. 만들기 중에 기존 호스팅 계획을 사용하거나 새 계획을 만들 수 있습니다. 동적 호스팅을 선택할 수도 있습니다. 함수 호스팅 옵션에 대한 자세한 내용은 [Azure Functions 크기 조정 방법](../azure-functions/functions-scale.md)에서 찾을 수 있습니다.

Azure 함수를 만들었으면 해당 함수로 이동하여 타이머 함수 및 C\#을 선택합니다. 그런 다음 **이 함수 만들기**를 클릭합니다.

![Azure Functions 시작 블레이드](./media/keyvault-keyrotation/Azure_Functions_Start.png)

**개발** 탭에서 run.csx 코드를 다음 코드로 바꿉니다.

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```


> [!NOTE]
> 위의 코드에 있는 변수를 Key Vault 로그가 기록된 저장소 계정, 이전에 생성한 Service Bus 및 Key Vault 저장소 로그의 경로를 가리키도록 바꿔야 합니다.
>
>

이 함수는 Key Vault 로그가 기록된 저장소 계정에서 최신 로그 파일을 선택하고 해당 파일에서 최신 이벤트를 가져와 Service Bus 큐에 푸시합니다. 단일 파일에 여러 이벤트가 포함될 수 있으므로 함수가 선택된 최신 이벤트의 타임스탬프를 결정하기 위해 확인하는 sync.txt 파일을 만들어야 합니다. 그러면 동일한 이벤트를 여러 번 푸시하지 않게 됩니다. 이 sync.txt 파일은 최근에 발생한 이벤트에 대한 타임스탬프를 포함합니다. 로그(로드된 경우)는 바르게 정렬되도록 타임스탬프를 기반으로 정렬해야 합니다.

이 함수에서는 Azure Functions에서 바로 사용할 수 없는 몇 가지 추가 라이브러리를 참조합니다. 이를 포함하려면 NuGet을 사용하여 끌어오기 위해 Azure Functions가 필요합니다. **보기 파일** 옵션을 선택합니다.

![보기 파일 옵션](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

project.json이라는 파일에 다음 콘텐츠를 추가합니다.

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
**저장** 시 Azure Functions가 필요한 이진 파일을 다운로드합니다.

**통합** 탭으로 전환하고 타이머 매개 변수에 함수 내에서 사용할 의미 있는 이름을 지정합니다. 위의 코드는 타이머가 *myTimer*로 호출될 것으로 예상합니다. 타이머에 대한 [CRON 식](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON)을 0 \* \* \* \* \*로 지정하면 함수가 1분에 한 번 실행됩니다.

동일한 **통합** 탭에서 **Azure Blob Storage** 형식의 입력을 추가합니다. 이렇게 하면 함수에서 확인하는 최신 이벤트의 타임스탬프를 포함하는 sync.txt 파일을 가리키게 됩니다. 그러면 함수 내에서 매개 변수 이름으로 사용할 수 있게 됩니다. 위의 코드에서 Azure Blob Storage 입력에 대한 매개 변수 이름은 *inputBlob*으로 예상됩니다. sync.txt 파일이 상주할 저장소 계정을 선택합니다(같은 저장소 계정일 수도 있고 다른 저장소 계정일 수도 있음). 경로 필드에는 파일이 {container-name}/path/to/sync.txt 형식으로 상주하는 경로를 입력합니다.

*Azure Blob Storage* 형식의 출력을 추가합니다. 이렇게 하면 입력에서 방금 정의한 sync.txt 파일을 가리키게 됩니다. 이 파일은 함수에서 확인하는 최신 이벤트의 타임스탬프를 작성하는 데 사용됩니다. 위의 코드에서는 이 매개 변수를 *outputBlob*이라고 합니다.

이제 함수가 준비되었습니다. 다시 **개발** 탭으로 전환하고 코드를 저장합니다. 출력 창에서 컴파일 오류를 확인하고 적절히 수정합니다. 코드가 컴파일되면 해당 코드는 이제 1분마다 Key Vault 로그를 확인하고 모든 새 이벤트를 정의된 Service Bus 큐에 푸시합니다. 함수가 트리거될 때마다 로깅 정보가 로그 창에 기록되는 것이 보입니다.

### <a name="azure-logic-app"></a>Azure 논리 앱
다음으로 함수가 Service Bus 큐에 푸시하는 이벤트를 선택하고 콘텐츠를 구문 분석한 후 일치하는 조건에 따라 전자 메일을 보내는 Azure 논리 앱을 만들어야 합니다.

**새로 만들기 -> 논리 앱**으로 이동하여 [논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

논리 앱을 만들었으면 해당 논리 앱으로 이동하여 **편집**을 선택합니다. 논리 앱 편집기 내에서 **Service Bus 큐**를 선택하고 큐에 연결하기 위한 Service Bus 자격 증명을 입력합니다.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

다음으로 **조건 추가**를 선택합니다. 조건에서 고급 편집기로 전환한 후 다음 코드를 입력합니다. 이때 APP_ID를 웹앱의 실제 APP_ID로 바꿉니다.

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

이 식은 기본적으로 들어오는 이벤트(Service Bus 메시지의 본문)의 *appid*가 앱의 *appid*가 아닌 경우 **false**를 반환합니다.

이제 **아니요인 경우, 아무 작업도 수행하지 않습니다** 옵션 아래에서 작업을 만듭니다.

![Azure 논리 앱 선택 작업](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

작업의 경우 **Office 365 - 전자 메일 보내기**를 선택합니다. 정의된 조건에서 **false**를 반환하는 경우 보낼 전자 메일을 작성하도록 필드를 채웁니다. Office 365가 없는 경우 같은 결과를 얻을 수 있는 대안을 살펴볼 수 있습니다.

현재는 1분마다 새로운 Key Vault 감사 로그를 확인하는 종단 간 파이프라인이 있습니다. 이 파이프라인은 새 로그가 발견되면 Service Bus 큐에 푸시합니다. 새 메시지가 큐에 도착하면 논리 앱이 트리거됩니다. 이벤트 내의 *appid*가 호출 응용 프로그램의 앱 ID와 일치하지 않으면 전자 메일이 발송됩니다.
