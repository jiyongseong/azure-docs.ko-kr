---
title: Azure SQL Database 연결 아키텍처 | Microsoft Docs
description: 이 문서에서는 Azure 내부 또는 Azure 외부의 Azure SQLDB 연결 아키텍처에 대해 설명합니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 01/24/2018
ms.author: carlrab
ms.openlocfilehash: 76af1ead671a6d6b279202eaf7886463d4cd8bb7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-sql-database-connectivity-architecture"></a>Azure SQL Database 연결 아키텍처 

이 문서에서는 Azure SQL Database 연결 아키텍처에 대해 설명하고 다양한 구성 요소가 Azure SQL Database의 인스턴스에 트래픽을 전달하는 방법에 대해 설명합니다. 이러한 Azure SQL Database 연결 구성 요소는 Azure 내에서 연결하는 클라이언트와 Azure 외부에서 연결하는 클라이언트를 사용하여 Azure 데이터베이스에 네트워크 트래픽을 전달합니다. 또한 이 문서에서는 연결되는 방법을 변경하는 스크립트 샘플 및 기본 연결 설정을 변경하는 데 관련된 고려 사항을 제공합니다. 

## <a name="connectivity-architecture"></a>연결 아키텍처

다음 다이어그램은 Azure SQL Database 연결 아키텍처의 대략적인 개요를 제공합니다.

![아키텍처 개요](./media/sql-database-connectivity-architecture/architecture-overview.png)


다음 단계는 Azure SQL Database SLB(소프트웨어 부하 분산 장치) 및 Azure SQL Database 게이트웨이를 통해 Azure SQL Database에 연결이 설정되는 방법을 설명합니다.

- Azure 내부 또는 Azure 외부의 클라이언트는 공용 IP 주소를 포함하고 포트 1433에서 수신 대기하는 SLB에 연결합니다.
- SLB는 Azure SQL Database 게이트웨이에 트래픽을 전달합니다.
- 게이트웨이는 트래픽을 올바른 프록시 미들웨어로 리디렉션합니다.
- 프록시 미들웨어는 적절한 Azure SQL Database에 트래픽을 리디렉션합니다.

> [!IMPORTANT]
> 이러한 각 구성 요소는 보호 네트워크 및 앱 계층에서 기본 제공되는 DDoS(distributed denial of service)를 배포합니다.
>

## <a name="connectivity-from-within-azure"></a>Azure 내부에서 연결

Azure 내부에서 연결하는 경우 연결에는 기본적으로 **리디렉션** 연결 정책이 있습니다. **리디렉션** 정책의 경우 TCP 세션 이후 연결이 Azure SQL Database로 설정되고 대상 가상 IP가 Azure SQL Database 게이트웨이에서 프록시 미들웨어의 대상 가상 IP로 변경되어 클라이언트 세션이 프록시 미들웨어로 리디렉션됩니다. 그런 다음 모든 후속 패킷은 Azure SQL Database 게이트웨이를 무시하고 프록시 미들웨어를 통해 직접 전달됩니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![아키텍처 개요](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Azure 외부에서 연결

Azure 외부에서 연결하는 경우 연결에는 기본적으로 **프록시** 연결 정책이 있습니다. **프록시** 정책의 경우 TCP 세션이 Azure SQL Database 게이트웨이를 통해 설정되고 모든 후속 패킷이 게이트웨이를 통합니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![아키텍처 개요](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database 게이트웨이 IP 주소

온-프레미스 리소스에서 Azure SQL Database에 연결하려면 Azure 지역의 Azure SQL Database 게이트웨이에 아웃바운드 네트워크 트래픽을 허용하도록 해야 합니다. 온-프레미스 리소스에서 연결할 때 기본 설정인 프록시 모드에서는 연결할 경우 연결은 게이트웨이를 통합니다.

다음 표에서는 모든 데이터 지역에 있는 Azure SQL Database 게이트웨이의 기본 및 보조 IP를 나열합니다. 일부 지역에는 두 개의 IP 주소가 있습니다. 이러한 지역에서 기본 IP 주소는 게이트웨이의 현재 IP 주소이고 두 번째 IP 주소는 장애 조치 IP 주소입니다. 장애 조치 주소는 높은 서비스 가용성을 유지하기 위해 서버를 이동할 수 있는 주소입니다. 이러한 지역의 경우 두 IP 주소에 아웃바운드를 허용하는 것이 좋습니다. 두 번째 IP 주소는 Microsoft가 소유하고 있으며 Azure SQL Database에서 연결을 허용하기 위해 활성화될 때까지 어떤 서비스도 수신하지 않습니다.

| 지역 이름 | 기본 IP 주소 | 보조 IP 주소 |
| --- | --- |--- |
| 오스트레일리아 동부 | 191.238.66.109 | 13.75.149.87 |
| 오스트레일리아 동남부 | 191.239.192.109 | 13.73.109.251 |
| 브라질 남부 | 104.41.11.5 | |
| 캐나다 중부 | 40.85.224.249 | |
| 캐나다 동부 | 40.86.226.166 | |
| 미국 중부 | 23.99.160.139 | 13.67.215.62 |
| 동아시아 | 191.234.2.139 | 52.175.33.150 |
| 미국 동부 1 | 191.238.6.43 | 40.121.158.30 |
| 미국 동부 2 | 191.239.224.107 | 40.79.84.180 * |
| 인도 중부 | 104.211.96.159  | |
| 인도 남부 | 104.211.224.146  | |
| 인도 서부 | 104.211.160.80 | |
| 일본 동부 | 191.237.240.43 | 13.78.61.196 |
| 일본 서부 | 191.238.68.11 | 104.214.148.156 |
| 한국 중부 | 52.231.32.42 | |
| 한국 남부 | 52.231.200.86 |  |
| 미국 중북부 | 23.98.55.75 | 23.96.178.199 |
| 북유럽 | 191.235.193.75 | 40.113.93.91 |
| 미국 중남부 | 23.98.162.75 | 13.66.62.124 |
| 동남아시아 | 23.100.117.95 | 104.43.15.0 |
| 영국 북부 | 13.87.97.210 | |
| 영국 남부 1 | 51.140.184.11 | |
| 영국 남부 2 | 13.87.34.7 | |
| 영국 서부 | 51.141.8.11  | |
| 미국 중서부 | 13.78.145.25 | |
| 서유럽 | 191.237.232.75 | 40.68.37.158 |
| 미국 서부 1 | 23.99.34.75 | 104.42.238.205 |
| 미국 서부 2 | 13.66.226.202  | |
||||

\* **참고:** *미국 동부 2*에는 `52.167.104.0`의 3차 IP 주소도 있습니다.

## <a name="change-azure-sql-database-connection-policy"></a>SQL Database 연결 정책 변경

Azure SQL Database 서버에 대한 Azure SQL Database 연결 정책을 변경하려면 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)를 사용합니다.

- 연결 정책을 **프록시**로 설정한 경우 모든 네트워크 패킷은 Azure SQL Database 게이트웨이를 통합니다. 이 설정에서 Azure SQL Database 게이트웨이 IP로만 아웃바운드를 허용해야 합니다. **프록시** 설정을 사용하면 **리디렉션** 설정보다 대기 시간이 길어집니다.
- 연결 정책을 **리디렉션**으로 설정하는 경우 모든 네트워크 패킷은 미들웨어 프록시에 직접 전달됩니다. 이 설정에서 여러 IP에 대한 아웃바운드를 허용해야 합니다.

## <a name="script-to-change-connection-settings-via-powershell"></a>PowerShell을 통해 연결 설정을 변경하는 스크립트

> [!IMPORTANT]
> 이 스크립트에는 [Azure PowerShell 모듈](/powershell/azure/install-azurerm-ps)이 필요합니다.
>

다음 PowerShell 스크립트에서는 연결 정책을 변경하는 방법을 보여줍니다.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli-20"></a>Azure CLI 2.0을 통해 연결 설정을 변경하는 스크립트

> [!IMPORTANT]
> 이 스크립트에는 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)이 필요합니다.
>

다음 CLI 스크립트에서는 연결 정책을 변경하는 방법을 보여줍니다.

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 서버에 대한 Azure SQL Database 연결 정책을 변경하는 방법에 대해서는 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)를 참조합니다.
- ADO.NET 4.5 이상 버전을 사용하는 클라이언트의 Azure SQL Database 연결 동작에 대한 자세한 정보는 [ADO.NET 4.5에 대한 1433 이외 포트](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.
- 일반 응용 프로그램 개발 개요 정보는 [SQL Database 응용 프로그램 개발 개요](sql-database-develop-overview.md)를 참조하세요.
