---
title: 서비스 사용자를 사용하여 Azure Analysis Services 작업 자동화 | Microsoft Docs
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2b49b85d39f55052e112fd9f4f0e28bdc6c91637
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213282"
---
# <a name="automation-with-service-principals"></a>서비스 사용자를 사용한 자동화

서비스 사용자는 무인 리소스 및 서비스 수준 작업을 수행하도록 테넌트 내에서 만드는 Azure Active Directory 응용 프로그램 리소스입니다. 응용 프로그램 ID 및 암호 또는 인증서가 있는 고유한 형식의 *사용자 ID*입니다. 서비스 사용자는 할당된 역할 및 사용 권한으로 정의된 작업을 수행하는 데 필요한 권한만 갖습니다. 

Analysis Services에서 서비스 사용자는 일반적인 작업을 자동화하는 Azure Automation, PowerShell 무인 모드, 사용자 지정 클라이언트 응용 프로그램 및 웹앱에 사용됩니다. 예를 들어 서버 프로비전, 모델 배포, 데이터 새로 고침, 규모 확대/축소 및 일시 중지/다시 시작은 모두 서비스 사용자를 사용하여 자동화될 수 있습니다. 일반 Azure AD UPN 계정과 매우 유사하게 권한은 역할 멤버 자격을 통해 서비스 사용자에게 할당됩니다.

## <a name="create-service-principals"></a>서비스 주체 만들기
 
서비스 사용자는 Azure Portal에서 또는 PowerShell을 사용하여 만들 수 있습니다. 자세한 내용은 다음을 참조하세요.

[서비스 사용자 만들기 - Azure Portal](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[서비스 주체 만들기 - PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Azure Automation에서 자격 증명 및 인증서 자산 저장

서비스 사용자 자격 증명 및 인증서는 Runbook 작업에 대해 Azure Automation에서 안전하게 저장될 수 있습니다. 자세한 내용은 다음을 참조하세요.

[Azure Automation의 자격 증명 자산](../automation/automation-credentials.md)   
[Azure Automation의 인증서 자산](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>서버 관리자 역할에 서비스 사용자 추가

Analysis Services 서버 관리 작업에 서비스 사용자를 사용하려면 먼저 서버 관리자 역할에 추가해야 합니다. 자세히 알아보려면 [서버 관리자 역할에 서비스 사용자 추가](analysis-services-addservprinc-admins.md)를 참조하세요.

## <a name="service-principals-in-connection-strings"></a>연결 문자열의 서비스 사용자

UPN과 거의 동일하게 서비스 사용자 appID 및 암호 또는 인증서를 연결 문자열에서 사용할 수 있습니다.

### <a name="powershell"></a>PowerShell

[AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) 모듈이 있는 리소스 관리 작업에 서비스 사용자를 사용하는 경우 `Login-AzureRmAccount` cmdlet을 사용합니다. [SQLServer](https://www.powershellgallery.com/packages/SqlServer) 모듈이 있는 서버 작업에 서비스 사용자를 사용하는 경우 `Add-AzureAnalysisServicesAccount` cmdlet을 사용합니다. 

다음 예제에서는 appID 및 암호가 모델 데이터베이스 새로 고침 작업을 수행하는 데 사용됩니다.

```PowerShell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Add-AzureAnalysisServicesAccount -Credential $Credential -ServicePrincipal -TenantId $TenantId -RolloutEnvironment "westcentralus.asazure.windows.net"

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full"
```

### <a name="amo-and-adomd"></a>AMO 및 ADOMD 

클라이언트 응용 프로그램 및 웹앱에 연결할 때 NuGet의 [AMO 및 ADOMD 클라이언트 라이브러리](analysis-services-data-providers.md) 버전 15.0.2 이상 설치 가능한 패키지는 다음 구문 `app:AppID` 및 암호 또는 `cert:thumbprint`를 사용하여 연결 문자열에서 서비스 사용자를 지원합니다. 

다음 예제에서는 `appID` 및 `password`가 모델 데이터베이스 새로 고침 작업을 수행하는 데 사용됩니다.

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>다음 단계
[Azure PowerShell을 사용하여 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[서버 관리자 역할에 서비스 사용자 추가](analysis-services-addservprinc-admins.md)   