---
title: 새 구독 또는 리소스 그룹으로 Azure 리소스 이동 | Microsoft Docs
description: Azure Resource Manager를 사용하여 리소스를 새 리소스 그룹 또는 구독으로 이동합니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 6c0e9c96840995c7d5a067e60264c66ce987af93
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360090"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>새 리소스 그룹 또는 구독으로 리소스 이동

이 문서에서는 리소스를 새 구독 또는 동일한 구독의 새 리소스 그룹으로 이동하는 방법을 보여 줍니다. 포털, PowerShell, Azure CLI 또는 REST API를 사용하여 리소스를 이동할 수 있습니다. 이 문서의 이동 작업은 Azure 지원의 도움 없이 사용할 수 있습니다.

리소스를 이동할 때 원본 그룹과 대상 그룹은 모두 작업 중에 잠겨 있습니다. 쓰기 및 삭제 작업은 이동이 완료될 때까지 리소스 그룹에서 차단됩니다. 이 잠금은 리소스 그룹에서 리소스를 추가, 업데이트, 삭제할 수 없음을 의미하지만 리소스가 고정되었음을 의미하지는 않습니다. 예를 들어, SQL Server와 해당 데이터베이스를 새 리소스 그룹으로 이동하는 경우 해당 데이터베이스를 사용하는 응용 프로그램에는 가동 중지 시간이 발생하지 않습니다. 데이터베이스에 계속해서 읽고 쓸 수 있습니다.

리소스의 위치는 변경할 수 없습니다. 리소스를 이동할 때는 새 리소스 그룹으로만 이동됩니다. 새 리소스 그룹은 다른 위치를 가질 수 있지만 리소스의 위치는 변경되지 않습니다.

> [!NOTE]
> 이 문서에서는 기존 Azure 계정 제품 내에서 리소스를 이동하는 방법을 설명합니다. 기존 리소스를 계속 사용하면서 실제로 Azure 계정 제품을 변경하려는 경우(예: 종량제 요금에서 선불로 업그레이드) [Azure 구독을 다른 제품으로 전환](../billing/billing-how-to-switch-azure-offer.md)을 참조하세요.
>
>

## <a name="checklist-before-moving-resources"></a>리소스를 이동하기 전의 검사 목록

리소스를 이동하기 전에 몇 가지 중요한 단계가 있습니다. 이러한 조건을 확인하면 오류를 방지할 수 있습니다.

1. 원본 및 대상 구독은 동일한 [Azure Active Directory 테넌트](../active-directory/active-directory-howto-tenant.md) 내에 있어야 합니다. 두 구독이 모두 동일한 테넌트 ID를 갖는지 확인하려면 Azure PowerShell 또는 Azure CLI를 사용합니다.

  Azure PowerShell의 경우 다음을 사용합니다.

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Azure CLI의 경우 

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  원본 및 대상 구독에 대한 테넌트 ID가 다른 경우 다음 메서드를 사용하여 테넌트 ID를 조정합니다.

  * [Azure 구독의 소유권을 다른 계정으로 이전](../billing/billing-subscription-transfer.md)
  * [Azure Active Directory에 Azure 구독을 연결하거나 추가하는 방법](../active-directory/active-directory-how-subscriptions-associated-directory.md)

2. 서비스는 리소스 이동 기능을 사용하도록 설정해야 합니다. 이 문서에서는 리소스 이동이 가능한 서비스와 그렇지 않은 서비스를 나열합니다.
3. 이동되는 리소스의 리소스 공급자가 대상 구독에 등록되어야 합니다. 그러지 않으면 **구독이 리소스 형식에 대해 등록되지 않았음**을 알리는 오류 메시지가 표시됩니다. 해당 리소스 종류와 함께 사용된 적이 없는 새 구독으로 리소스를 이동할 때 이 문제가 발생할 수 있습니다.

  PowerShell의 경우 다음 명령을 사용하여 등록 상태를 가져옵니다.

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  리소스 공급자를 등록하려면 다음을 사용합니다.

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Azure CLI의 경우 다음 명령을 사용하여 등록 상태를 가져옵니다.

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  리소스 공급자를 등록하려면 다음을 사용합니다.

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

4. 리소스를 이동시키는 계정에는 적어도 다음과 같은 권한이 있어야 합니다.

   * 원본 리소스 그룹에 대한 **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action**
   * 대상 리소스 그룹에 대한 **Microsoft.Resources/subscriptions/resourceGroups/write**

## <a name="when-to-call-support"></a>지원을 호출해야 하는 경우

대부분의 리소스는 이 문서에 나오는 셀프 서비스 작업을 통해 이동할 수 있습니다. 다음에 대해 셀프 서비스 작업을 사용합니다.

* Resource Manager 리소스를 이동합니다.
* [클래식 배포 제한 사항](#classic-deployment-limitations)에 따라 클래식 리소스를 이동합니다.

다음을 수행해야 하는 경우 [지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하세요.

* 새 Azure 계정(및 Azure Active Directory 테넌트)로 리소스를 이동하며 앞의 섹션의 지침을 수행하는 데 지원이 필요합니다.
* 클래식 리소스를 이동하지만 제한 사항으로 문제가 발생합니다.

## <a name="services-that-can-be-moved"></a>이동할 수 있는 서비스

새 리소스 그룹 및 구독으로 이동할 수 있게 하는 서비스는 다음과 같습니다.

* API Management
* App Service 앱(웹앱) - [App Service 제한](#app-service-limitations)
* App Service Certificates
* Application Insights
* Automation
* Azure Cosmos DB
* Azure Relay
* Batch
* Bing 지도
* CDN
* Cloud Services - [클래식 배포 제한 사항](#classic-deployment-limitations)
* Cognitive Services
* Content Moderator
* Data Catalog
* Data Factory - V1은 이동될 수 있지만 V2 이동(미리 보기)은 지원되지 않습니다.
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Hubs
* HDInsight 클러스터 - [HDInsight 제한 사항](#hdinsight-limitations) 참조
* IoT Hub
* Key Vault
* 부하 분산 장치 - [부하 분산 장치 제한 사항](#lb-limitations) 참조
* Log Analytics
* Logic Apps
* 기계 학습 - Machine Learning Studio 웹 서비스는 동일한 구독의 리소스 그룹으로 이동할 수 있지만 다른 구독으로 이동할 수는 없습니다. 다른 Machine Learning 리소스는 구독 간에 이동할 수 있습니다.
* Media Services
* 모바일 고객 관리
* Notification Hubs
* Operational Insights
* 운영 관리
* Power BI - Power BI Embedded 및 Power BI Workspace Collection 모두
* 공용 IP - [공용 IP 제한 사항](#pip-limitations) 참조
* Redis Cache
* Scheduler
* 검색
* 서버 관리
* Service Bus
* Service Fabric
* Storage
* 저장소(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations)
* Stream Analytics - 실행 중 상태일 때는 Stream Analytics 작업을 이동할 수 없습니다.
* SQL Database 서버 - 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure SQL Data Warehouse 데이터베이스에 적용됩니다. 
* Traffic Manager
* Virtual Machines - 관리 디스크가 있는 VM은 이동할 수 없습니다. [Virtual Machines 제한 사항](#virtual-machines-limitations) 참조
* Virtual Machines(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations)
* Virtual Machine Scale Sets - [Virtual Machines 제한 사항](#virtual-machines-limitations) 참조
* Virtual Networks - [Virtual Networks 제한 사항](#virtual-networks-limitations) 참조
* Visual Studio Team Services - Microsoft가 아닌 확장 구매가 있는 VSTS 계정은 [구매를 취소](https://go.microsoft.com/fwlink/?linkid=871160)해야만 구독 간에 계정을 이동할 수 있습니다.
* VPN Gateway

## <a name="services-that-cannot-be-moved"></a>이동할 수 없는 서비스

현재 리소스 이동을 사용하지 않는 서비스는 다음과 같습니다.

* AD Domain Services
* AD 하이브리드 상태 관리 서비스
* Application Gateway
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure Migrate
* BizTalk Services
* 인증서 - App Service Certificate를 이동할 수 있지만 업로드된 인증서에는 [제한](#app-service-limitations)이 있습니다.
* DevTest Labs - 동일한 구독에서 새 리소스 그룹으로 이동이 가능하지만, 구독 간 이동은 사용 가능하지 않습니다.
* Dynamics LCS
* Express 경로
* Kubernetes 서비스
* 부하 분산 장치 - [부하 분산 장치 제한 사항](#lb-limitations) 참조
* Managed Applications
* Managed Disks - [Virtual Machines 제한 사항](#virtual-machines-limitations) 참조
* 공용 IP - [공용 IP 제한 사항](#pip-limitations) 참조
* Recovery Services 자격 증명 모음 - Recovery Services 자격 증명 모음과 연결된 Compute, Network 및 Storage 리소스도 이동하지 않습니다. [Recovery Services 제한 사항](#recovery-services-limitations)을 참조하세요.
* 보안
* StorSimple 장치 관리자
* Virtual Networks(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Virtual Machines 제한 사항

관리 디스크는 이동을 지원하지 않습니다. 이 제한 사항은 여러 관련 리소스도 이동할 수 없음을 의미합니다. 이동할 수 없는 디스크:

* 관리 디스크
* 관리 디스크가 있는 가상 머신
* 관리 디스크에서 만든 이미지
* 관리 디스크에서 만든 스냅숏
* 관리 디스크가 있는 가상 머신이 포함된 가용성 집합

관리 디스크를 이동할 수는 없지만 복사본을 만든 후 기존 관리 디스크에서 새 가상 시스템을 작성할 수 있습니다. 자세한 내용은 다음을 참조하세요.

* [PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md) 또는 [Azure CLI](../virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md)를 사용하여 같은 구독 또는 다른 구독에 관리 디스크를 복사합니다.
* [PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md) 또는 [Azure CLI](../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md)와 함께 기존 관리 OS 디스크를 사용하여 가상 머신을 만듭니다.

연결된 계획이 있는 Marketplace 리소스에서 만든 가상 머신은 리소스 그룹 또는 구독 간에 이동할 수 없습니다. 현재 구독의 가상 머신을 프로비전 해제하고 새 구독에 다시 배포합니다.

Key Vault에 저장된 인증서가 있는 Virtual Machines는 동일한 구독에서 새 리소스 그룹으로 이동할 수 있지만 구독 간에는 이동할 수 없습니다.

## <a name="virtual-networks-limitations"></a>Virtual Networks 제한 사항

가상 네트워크를 이동할 때는 해당 종속 리소스도 함께 이동해야 합니다. 예를 들어, 가상 네트워크와 함께 게이트웨이를 이동해야 합니다.

피어링된 가상 네트워크를 이동하려면 먼저 가상 네트워크 피어링을 사용하지 않도록 설정해야 합니다. 사용하지 않도록 설정되면 가상 네트워크를 이동할 수 있습니다. 이동 후에는 가상 네트워크 피어링을 사용하도록 다시 설정합니다.

리소스 탐색 링크가 있는 서브넷이 가상 네트워크에 있는 경우 가상 네트워크를 다른 구독으로 이동할 수 없습니다. 예를 들어 Redis Cache 리소스가 서브넷에 배포된 경우 해당 서브넷에는 리소스 탐색 링크가 있습니다.

사용자 지정 DNS 서버가 가상 네트워크에 있는 경우 가상 네트워크를 다른 구독으로 이동할 수 없습니다. 가상 네트워크를 이동하려면 Default(Azure 제공) DNS 서버로 설정합니다. 이동 후 사용자 지정 DNS 서버를 다시 구성합니다.

## <a name="app-service-limitations"></a>App Service 제한

App Service 리소스를 구독 내에서 이동할지 또는 새 구독으로 이동할지에 따라 리소스 이동에 대한 제한 사항이 다릅니다.

이 섹션에서 설명한 제한 사항은 App Service Certificates가 아닌 업로드된 인증서에 적용됩니다. 아무런 제한 없이 App Service Certificate를 새 리소스 그룹 또는 구독으로 이동시킬 수 있습니다. 동일한 App Service Certificate를 사용하는 여러 웹앱이 있는 경우 먼저 모든 웹앱을 이동한 다음, 인증서를 이동합니다.

### <a name="moving-within-the-same-subscription"></a>동일한 구독 내에서 이동

_동일한 구독 내에서_ Web App을 이동할 때 업로드된 SSL 인증서는 이동할 수 없습니다. 그러나 업로드된 SSL 인증서를 이동하지 않고 Web App을 새 리소스 그룹으로 이동할 수 있으며 앱의 SSL 기능도 계속 작동합니다.

Web App을 사용하여 SSL 인증서를 이동하려면 다음 단계를 수행합니다.

1.  Web App에서 업로드된 인증서를 삭제합니다.
2.  Web App을 이동합니다.
3.  이동된 Web App에 인증서를 업로드합니다.

### <a name="moving-across-subscriptions"></a>구독 간 이동

_구독 간에_ Web App을 이동할 때 적용되는 제한 사항은 다음과 같습니다.

- 대상 리소스 그룹에 기존 App Service 리소스가 없어야 합니다. App Service 리소스에는 다음이 포함됩니다.
    - Web Apps
    - App Service 계획
    - 업로드되거나 가져온 SSL 인증서
    - App Service Environment
- 리소스 그룹의 모든 App Service 리소스는 함께 이동해야 합니다.
- App Service 리소스는 처음 만들었던 리소스 그룹에서만 이동할 수 있습니다. App Service 리소스가 원래의 리소스 그룹에 더 이상 없으면 먼저 원래의 리소스 그룹으로 다시 이동해야 합니다. 그런 다음 구독 간에 App Service 리소스를 이동하면 됩니다.

## <a name="classic-deployment-limitations"></a>클래식 배포 제한 사항

구독 내 또는 새 구독으로 리소스를 이동할지 여부에 따라 클래식 모델을 통해 배포된 리소스의 이동 옵션은 다릅니다.

### <a name="same-subscription"></a>동일한 구독

한 리소스 그룹에서 같은 구독 내 다른 리소스 그룹으로 리소스를 이동할 경우 다음 제한 사항이 적용됩니다.

* 가상 네트워크(클래식)은 이동할 수 없습니다.
* 가상 머신(클래식)은 클라우드 서비스로 이동해야 합니다.
* 클라우드 서비스는 이동에 모든 가상 머신이 포함된 경우에만 이동할 수 있습니다.
* 한 번에 하나의 클라우드 서비스만 이동할 수 있습니다.
* 한 번에 하나의 저장소 계정(클래식)만 이동할 수 있습니다.
* Storage 계정(클래식)은 가상 머신 또는 클라우드 서비스와 같은 작업으로 이동할 수 없습니다.

클래식 리소스를 동일한 구독 내의 새 리소스 그룹으로 이동하려면, [포털](#use-portal), [Azure PowerShell](#use-powershell), [Azure CLI](#use-azure-cli) 또는 [REST API](#use-rest-api)를 통해 표준 이동 작업을 사용합니다. Resource Manager 리소스 이동을 위해 사용하는 동일한 작업을 사용합니다.

### <a name="new-subscription"></a>새 구독

새 구독으로 리소스 이동 시 다음 제한 사항이 적용됩니다.

* 구독의 모든 클래식 리소스는 동일한 작업에서 이동해야 합니다.
* 대상 구독은 다른 어떠한 클래식 리소스도 포함할 수 없습니다.
* 이동은 클래식 이동에 대한 별도의 REST API를 통해서만 요청할 수 있습니다. 클래식 리소스를 새 구독으로 이동할 경우 표준 Resource Manager 이동 명령은 작동하지 않습니다.

클래식 리소스를 새 구독으로 이동하려면 클래식 리소스와 관련된 REST 작업을 사용합니다. REST를 사용하려면 다음 단계를 수행합니다.

1. 원본 구독이 구독 간 이동에 참여할 수 있는지 확인합니다. 다음 작업을 사용합니다.

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     요청 본문에 다음을 포함합니다.

  ```json
  {
    "role": "source"
  }
  ```

     유효성 검사 작업에 대한 응답은 다음 형식입니다.

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. 대상 구독이 구독 간 이동에 참여할 수 있는지 확인합니다. 다음 작업을 사용합니다.

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     요청 본문에 다음을 포함합니다.

  ```json
  {
    "role": "target"
  }
  ```

     응답이 원본 구독 유효성 검사와 동일한 형식입니다.
3. 두 구독이 유효성 검사를 통과하면 다음 작업으로 한 구독에서 다른 구독으로 모든 클래식 리소스를 이동합니다.

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    요청 본문에 다음을 포함합니다.

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

이 작업은 몇 분 정도 실행될 수 있습니다.

## <a name="recovery-services-limitations"></a>Recovery Services 제한 사항

Azure Site Recovery로 재해 복구를 설정하는 데 사용된 Storage, Network 또는 Compute 리소스에 대해서는 이동이 사용되지 않습니다.

예를 들어, 온-프레미스 컴퓨터에서 저장소 계정(Storage1)으로 복제를 설정했고 Azure에 장애 조치(failover) 후 가상 네트워크(Network1)에 연결된 가상 머신(VM1)로 보호되는 컴퓨터를 실행하려고 한다고 가정합니다. 같은 구독 내에 있거나 여러 구독에 있는 리소스 그룹에 대해 이러한 Azure 리소스(Storage1, VM1, Network1) 작업 중 어떠한 것도 이동할 수 없습니다.

리소스 그룹 간에 **Azure Backup**에 등록된 VM을 이동하려면 다음을 수행합니다.
 1. 일시적으로 백업을 중지하고 백업 데이터를 보존합니다.
 2. VM을 대상 리소스 그룹으로 이동합니다.
 3. 사용자가 이동 작업 이전에 만든 사용 가능한 복원 지점에서 복원할 수 있는 VM을 동일하거나 새로운 자격 증명 모음으로 다시 보호합니다.
사용자가 백업된 VM을 구독 간에 이동하는 경우 1단계와 2단계는 동일합니다. 3단계에서, 대상 구독에 있는/만들어진 새 자격 증명 모음에서 VM을 보호해야 합니다. Recovery Services 자격 증명 모음은 구독 간 백업을 지원하지 않습니다.

## <a name="hdinsight-limitations"></a>HDInsight 제한 사항

새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC, 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.

HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 저장소 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

## <a name="search-limitations"></a>검색 제한 사항

Search 리소스를 이동하여 한 번에 모두 다른 지역에 배치할 수 없습니다.
이러한 경우에는 개별적으로 이동해야 합니다.

## <a name="lb-limitations"></a> 부하 분산 장치 제한 사항

기본 SKU 부하 분산 장치는 이동할 수 있습니다.
표준 SKU 부하 분산 장치는 이동할 수 없습니다.

## <a name="pip-limitations"></a> 공용 IP 제한 사항

기본 SKU 공용 IP는 이동할 수 있습니다.
표준 SKU 공용 IP는 이동할 수 없습니다.

## <a name="use-portal"></a>포털 사용

리소스를 이동하려면 해당 리소스가 포함된 리소스 그룹을 선택한 후 **이동** 단추를 선택합니다.

![리소스 이동](./media/resource-group-move-resources/select-move.png)

리소스를 새 리소스 그룹으로 이동할지 또는 새 구독으로 이동할지를 선택합니다.

이동할 리소스와 대상 리소스 그룹을 선택합니다. 이러한 리소스에 대해 스크립트를 업데이트해야 함을 승인하고 **확인**을 선택합니다. 이전 단계에서 구독 편집 아이콘을 선택한 경우 대상 구독도 선택해야 합니다.

![대상 선택](./media/resource-group-move-resources/select-destination.png)

**알림**에서 이동 작업이 실행 중임을 볼 수 있습니다.

![이동 상태 표시](./media/resource-group-move-resources/show-status.png)

완료되면 결과를 알려 줍니다.

![이동 결과 표시](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>PowerShell 사용

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) 명령을 사용합니다. 다음 예제에서는 여러 리소스를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

새 구독으로 이동하려면 `DestinationSubscriptionId` 매개 변수 값을 포함합니다.

## <a name="use-azure-cli"></a>Azure CLI 사용

기존 리소스를 다른 리소스 그룹 또는 구독으로 이동하려면 [az resource move](/cli/azure/resource?view=azure-cli-latest#az_resource_move) 명령을 사용합니다. 이동할 리소스에 대한 리소스 ID를 제공합니다. 다음 예제에서는 여러 리소스를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다. `--ids` 매개 변수에서 이동할 리소스 ID를 쉼표로 구분한 목록을 제공합니다.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

새 구독으로 이동하려면 `--destination-subscription-id` 매개 변수를 제공합니다.

## <a name="use-rest-api"></a>REST API 사용

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 다음을 실행합니다.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

요청 본문에서 대상 리소스 그룹 및 이동할 리소스를 지정합니다. 이동 REST 작업에 대한 자세한 내용은 [리소스 이동](/rest/api/resources/Resources/MoveResources)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 구독을 관리하기 위한 PowerShell cmdlet에 대한 자세한 내용은 [Resource Manager에서 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.
* 구독을 관리하기 위한 Azure CLI 명령에 대한 자세한 내용은 [Resource Manager에서 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.
* 구독을 관리하기 위한 포털 기능에 대한 자세한 내용은 [Azure 포털을 사용하여 리소스 관리](resource-group-portal.md)를 참조하세요.
* 리소스를 논리적으로 구성하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](resource-group-using-tags.md)을 참조하세요.
