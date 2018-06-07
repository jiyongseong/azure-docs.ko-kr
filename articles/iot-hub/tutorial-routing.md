---
title: Azure IoT Hub(.NET)로 메시지 라우팅 구성 | Microsoft Docs
description: Azure IoT Hub로 메시지 라우팅 구성
services: iot-hub
documentationcenter: .net
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 0674ed033f77d7d2eca319d0b1e82171dfa4256d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>자습서: IoT Hub로 메시지 라우팅 구성

메시지 라우팅을 사용하면 원격 분석 데이터를 IoT 장치에서 기본 제공된 Event Hub 호환 엔드포인트 또는 blob 저장소, Service Bus 큐, Service Bus 항목 및 Event Hubs와 같은 사용자 지정 엔드포인트에 전송할 수 있습니다. 사용자는 메시지 라우팅을 구성하는 동안 특정 규칙에 일치하는 경로를 사용자 지정할 수 있도록 라우팅 규칙을 만들 수 있습니다. 한 번 설정하면 들어오는 데이터가 자동으로 IoT Hub에 의해 엔드포인트로 라우팅됩니다. 

이 자습서에서는 IoT Hub로 라우팅 규칙을 설정하고 사용하는 방법에 대해 알아봅니다. IoT 장치에서 Blob 저장소 및 Service Bus 큐를 포함한 여러 서비스 중 하나로 메시지를 라우팅합니다. Service Bus 큐로 보내는 메시지는 Logic App에 의해 선택되며 이메일을 통해 전송됩니다. 특별히 설정된 라우팅이 없는 메시지는 기본 엔드포인트에 전송되고 PowerBI 시각화에 표시됩니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure CLI 또는 PowerShell을 사용하여 IoT Hub, 저장소 계정, Service Bus 큐 및 시뮬레이션된 장치와 같은 기본 리소스를 설정합니다.
> * 저장소 계정 및 Service Bus 큐에 대한 IoT Hub에서 엔드포인트 및 경로를 구성합니다.
> * 메시지가 Service Bus 큐에 추가될 때 트리거되어 이메일을 전송하는 Logic App을 만듭니다.
> * 다양한 라우팅 옵션에 대한 허브로 메시지를 전송하는 IoT 장치를 시뮬레이션하는 앱을 다운로드하여 실행합니다.
> * 기본 엔드포인트에 전송된 데이터에 대한 PowerBI 시각화를 만듭니다.
> * 다음에서 결과를 확인합니다.
> * ...Service Bus 큐 및 이메일에서
> * ...저장소 계정에서
> * ...PowerBI 시각화에서

## <a name="prerequisites"></a>필수 조건

- Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- [Windows용 Visual Studio](https://www.visualstudio.com/)를 설치합니다. 

- 기본 엔드포인트의 스트림 분석을 분석하기 위한 PowerBI 계정. ([PowerBI를 무료로 사용해 보기](https://app.powerbi.com/signupredirect?pbi_source=web))

- 알림 이메일을 보내는 Office 365 계정. 

이 자습서를 위한 설치 단계를 수행하려면 Azure CLI 또는 Azure PowerShell이 필요합니다. 

Azure CLI를 사용하기 위해서는 Azure CLI를 로컬로 설치할 수도 있으나, Azure Cloud Shell을 사용하는 것이 좋습니다. Azure Cloud Shell은 Azure CLI 스크립트를 실행하는 데 사용할 수 있는 체험용 대화형 셸입니다. 계정에서 사용할 수 있도록 공용 Azure 도구가 Cloud Shell에 사전 설치 및 구성되어 있으므로 로컬로 설치할 필요가 없습니다. 

PowerShell을 사용하려면 아래 지침을 사용하여 로컬로 설치합니다. 

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Cloud Shell을 여는 몇 가지 방법이 있습니다.

|  |   |
|-----------------------------------------------|---|
| 코드 블록의 오른쪽 위 모서리에서 **사용**을 선택합니다. | ![이 문서의 Cloud Shell](./media/tutorial-routing/cli-try-it.png) |
| 브라우저에서 Cloud Shell을 엽니다. | [![https://shell.azure.com/bash](./media/tutorial-routing/launchcloudshell.png)](https://shell.azure.com) |
| [Azure Portal](https://portal.azure.com) 오른쪽 위에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다. |    ![포털의 Cloud Shell](./media/tutorial-routing/cloud-shell-menu.png) |
|  |  |

### <a name="using-azure-cli-locally"></a>Azure CLI 로컬로 사용

Cloud Shell보다 CLI를 로컬로 사용하려는 경우 Azure CLI 모듈 버전 2.0.30.0 이상이 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

### <a name="using-powershell-locally"></a>PowerShell 로컬로 사용

이 자습서에서는 Azure PowerShell 모듈 버전 5.7 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="set-up-resources"></a>리소스 설치

이 자습서에서는 IoT Hub, 저장소 계정 및 Service Bus 큐가 필요합니다. 이러한 리소스는 Azure CLI 또는 Azure PowerShell을 사용하여 만들 수 있습니다. 모든 리소스에 대해 동일한 리소스 그룹 및 위치를 사용합니다. 그런 다음, 마지막에 리소스 그룹을 삭제하면 모든 내용을 한 번에 삭제할 수 있습니다.

다음 섹션에서는 이를 위해 필요한 단계를 설명합니다. CLI *또는* PowerShell 지침을 따르세요.

1. [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 

    <!-- When they add the Basic tier, change this to use Basic instead of Standard. -->

2. S1 계층에서 IoT Hub를 만듭니다. IoT Hub에 소비자 그룹을 추가합니다. 소비자 그룹은 Azure Stream Analytics에서 데이터를 검색할 때 사용됩니다.

3. Standard_LRS 복제본을 사용하여 표준 V1 저장소 계정을 만듭니다.

4. Service Bus 네임스페이스 및 큐를 만듭니다. 

5. 허브에 메시지를 보내는 시뮬레이션된 장치에 사용할 장치 ID를 만듭니다. 테스트 단계에 대한 키를 저장합니다.

### <a name="azure-cli-instructions"></a>Azure CLI 지침

이 스크립트를 사용하는 가장 쉬운 방법은 복사하여 Cloud Shell에 붙여넣는 것입니다. 이미 로그인했다고 가정하면 한 번에 한 줄씩 스크립트가 실행됩니다. 

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# These resource names must be globally unique.
# You might need to change these if they are already in use by someone else.
iotHubName=ContosoTestHub 
storageAccountName=contosoresultsstorage 
sbNameSpace=ContosoSBNamespace 
sbQueueName=ContosoSBQueue

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location
    
# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="powershell-instructions"></a>PowerShell 지침

이 스크립트를 사용하는 가장 쉬운 방법은 [PowerShell ISE](/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise.md)를 열고, 스크립트를 클립보드에 복사한 다음, 전체 스크립트를 스크립트 창에 붙여넣습니다. 그런 다음, 리소스 이름에 대한 값을 변경하고(원하는 경우) 전체 스크립트를 실행할 수 있습니다. 

```azurepowershell-interactive
# Log into Azure account.
Login-AzureRMAccount

# Set the values for the resource names.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# These resource names must be globally unique.
# You might need to change these if they are already in use by someone else.
$iotHubName = "ContosoTestHub"
$storageAccountName = "contosoresultsstorage"
$serviceBusNamespace = "ContosoSBNamespace"
$serviceBusQueueName  = "ContosoSBQueue"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create the IoT hub.
New-AzureRmIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzureRmIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzureStorageContainer -Name $containerName `
    -Context $storageContext

# Create the Service Bus namespace.
New-AzureRmServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# Create the Service Bus queue to be used as a routing destination.
New-AzureRmServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

다음으로 장치 ID를 만들고 나중에 사용할 키를 저장합니다. 이 장치 ID는 IoT Hub에 메시지를 보내는 시뮬레이션 응용 프로그램에서 사용됩니다. 이 기능은 PowerShell에서는 사용할 수 없지만, [Azure Portal](https://portal.azure.com)에서 장치를 만들 수는 있습니다.

1. [Azure Portal](https://portal.azure.com)을 열고 사용자의 Azure 계정에 로그인합니다.

2. **리소스 그룹**을 클릭하고 사용자의 리소스 그룹을 선택합니다. 이 자습서에서는 **ContosoResources**를 사용합니다.

3. 리소스 목록에서 사용자의 IoT Hub를 클릭합니다. 이 자습서에서는 **ContosoTestHub**를 사용합니다. 허브 창에서 **IoT 장치**를 선택합니다.

4. **+ 추가**를 클릭합니다. 추가 장치 창에서 장치 ID를 입력합니다. 이 자습서에서는 **Contoso-Test-Device**를 사용합니다. 키를 비워 두고 **키 자동 생성**을 선택합니다. **IoT Hub에 장치 연결**이 사용하도록 설정되어 있는지 확인합니다. **저장**을 클릭합니다.

   ![장치 추가 화면을 보여주는 스크린샷.](./media/tutorial-routing/add-device.png)

5. 만든 후 장치를 클릭하여 생성된 키를 확인합니다. 기본 키에서 복사 아이콘을 클릭하고 이 자습서의 테스트 단계를 위해 메모장 등에 저장합니다.

   ![키를 포함한 장치 세부 정보를 보여주는 스크린샷.](./media/tutorial-routing/device-details.png)



## <a name="set-up-message-routing"></a>메시지 라우팅 설정

시뮬레이션된 장치에 의해 메시지에 연결된 속성에 따라 다양한 리소스에 메시지를 라우팅합니다. 사용자 지정 라우팅되지 않은 메시지는 기본 엔드포인트로 전송됩니다(메시지/이벤트). 

|값 |결과|
|------|------|
|level="storage" |Azure Storage에 씁니다.|
|level="critical" |Service Bus 큐에 씁니다. Logic App은 큐의 메시지를 검색하고 Office 365를 사용하여 메시지를 이메일로 전송합니다.|
|기본값 |PowerBI를 사용하여 이 데이터를 표시합니다.|

### <a name="routing-to-a-storage-account"></a>저장소 계정으로 라우팅 

이제 저장소 계정에 대한 라우팅을 설정합니다. 엔드포인트를 정의한 다음, 해당 엔드포인트에 대한 경로를 설정합니다. **수준** 속성이 **저장소**로 설정된 메시지는 저장소 계정에 자동으로 작성됩니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹**을 클릭한 다음, 사용자의 리소스 그룹을 선택합니다. 이 자습서에서는 **ContosoResources**를 사용합니다. 리소스 목록에서 IoT Hub를 클릭합니다. 이 자습서에서는 **ContosoTestHub**를 사용합니다. **Endpoints**를 클릭합니다. **엔드포인트** 창에서 **+추가**를 클릭합니다. 다음 정보를 입력합니다.

   **이름**: 엔드포인트에 사용할 이름을 입력합니다. 이 자습서에서는 **StorageContainer**를 사용합니다.
   
   **엔드포인트 유형**: 드롭다운 목록에서 **Azure Storage 컨테이너**를 선택합니다.

   **컨테이너 선택**을 클릭하여 저장소 계정 목록을 표시합니다. 사용자의 저장소 계정을 선택합니다. 이 자습서에서는 **contosoresultsstorage**를 사용합니다. 다음으로 컨테이너를 선택합니다. 이 자습서에서는 **contosoresults**를 사용합니다. **선택**을 클릭하여 엔드포인트 추가 창으로 돌아갑니다. 
   
   ![엔드포인트 추가를 보여주는 스크린샷.](./media/tutorial-routing/add-endpoint-storage-account.png)
   
   **확인**을 클릭하여 엔드포인트 추가 작업을 마칩니다.
   
2. IoT Hub에서 **경로**를 클릭합니다. 방금 엔드포인트로 추가한 저장소 컨테이너에 메시지를 라우팅하는 라우팅 규칙을 만들고자 합니다. 경로 창 위쪽에서 **+추가**를 클릭합니다. 화면에 나온 필드를 채웁니다. 

   **이름**: 라우팅 규칙에 사용할 이름을 입력합니다. 이 자습서에서는 **StorageRule**을 사용합니다.

   **데이터 원본**: 드롭다운 목록에서 **장치 메시지**를 선택합니다.

   **엔드포인트**: 앞에서 설정한 엔드포인트를 선택합니다. 이 자습서에서는 **StorageContainer**를 사용합니다. 
   
   **쿼리 문자열**: 쿼리 문자열로 `level="storage"`을 입력합니다. 

   ![저장소 계정에 대한 라우팅 규칙 만들기를 보여주는 스크린샷.](./media/tutorial-routing/create-a-new-routing-rule-storage.png)
   
   **저장**을 클릭합니다. 완료되면 저장소에 대한 새 라우팅 규칙을 볼 수 있는 경로 창으로 돌아갑니다. 경로 창을 닫아 리소스 그룹 페이지로 돌아갑니다.

### <a name="routing-to-a-service-bus-queue"></a>Service Bus 큐로 라우팅 

이제 Service Bus 큐에 대한 라우팅을 설정합니다. 엔드포인트를 정의한 다음, 해당 엔드포인트에 대한 경로를 설정합니다. **수준** 속성이 **중요**로 설정된 메시지는 Logic App을 트리거한 다음, 정보가 포함된 이메일을 전송하는 Service Bus 큐에 작성됩니다. 

1. 리소스 그룹 페이지에서 IoT Hub를 클릭한 다음, **엔드포인트**를 클릭합니다. **엔드포인트** 창에서 **+추가**를 클릭합니다. 다음 정보를 입력합니다.

   **이름**: 엔드포인트에 사용할 이름을 입력합니다. 이 자습서에서는 **CriticalQueue**를 사용합니다. 

   **엔드포인트 유형**: 드롭다운 목록에서 **Service Bus 큐**를 선택합니다.

   **Service Bus 네임스페이스**: 드롭다운 목록에서 이 자습서에 대한 Service Bus 네임스페이스를 선택합니다. 이 자습서에서는 **ContosoSBNamespace**를 사용합니다.

   **Service Bus 큐**: 드롭다운 목록에서 Service Bus 큐를 선택합니다. 이 자습서에서는 **contososbqueue**를 사용합니다.

   ![Service Bus 큐에 대한 엔드포인트 추가를 보여주는 스크린샷.](./media/tutorial-routing/add-endpoint-sb-queue.png)

   **확인**을 클릭하여 엔드포인트를 저장합니다. 완료되면 엔드포인트 창을 닫습니다. 
    
2. IoT Hub에서 **경로**를 클릭합니다. 방금 엔드포인트로 추가한 Service Bus 큐에 메시지를 라우팅하는 라우팅 규칙을 만들고자 합니다. 경로 창 위쪽에서 **+추가**를 클릭합니다. 화면에 나온 필드를 채웁니다. 

   **이름**: 라우팅 규칙에 사용할 이름을 입력합니다. 이 자습서에서는 **SBQueueRule**을 사용합니다. 

   **데이터 원본**: 드롭다운 목록에서 **장치 메시지**를 선택합니다.

   **엔드포인트**: 앞에서 설정한 엔드포인트 **CriticalQueue**를 선택합니다.

   **쿼리 문자열**: 쿼리 문자열로 `level="critical"`을 입력합니다. 

   ![Service Bus 큐에 대한 라우팅 규칙 만들기를 보여주는 스크린샷.](./media/tutorial-routing/create-a-new-routing-rule-sbqueue.png)
   
   **저장**을 클릭합니다. 경로 창으로 돌아가면 여기에 표시된 대로 새 라우팅 규칙을 모두 확인할 수 있습니다.

   ![앞에서 설정한 경로를 보여주는 스크린샷.](./media/tutorial-routing/show-routing-rules-for-hub.png)

   경로 창을 닫아 리소스 그룹 페이지로 돌아갑니다.

## <a name="create-a-logic-app"></a>논리 앱 만들기  

Service Bus 큐는 중요한 것으로 지정된 메시지를 받는 데 사용됩니다. Service Bus 큐를 모니터링하고 메시지가 큐에 추가될 때 이메일을 전송하는 Logic App을 설정합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **+리소스 만들기**를 클릭합니다. 검색 상자에 **논리 앱**을 입력하고 Enter 키를 클릭합니다. 표시되는 검색 결과에서 Logic App을 선택한 다음, **만들기**를 클릭하여 **논리 앱 만들기** 창으로 계속 진행합니다. 필드를 입력합니다. 

   **이름**: 이 필드는 논리 앱의 이름입니다. 이 자습서에서는 **ContosoLogicApp**을 사용합니다. 

   **구독**: Azure 구독을 선택합니다.

   **리소스 그룹**: **기존 항목 사용**을 클릭하고 사용자의 리소스 그룹을 선택합니다. 이 자습서에서는 **ContosoResources**를 사용합니다. 

   **위치**: 사용자의 위치를 사용합니다. 이 자습서에서는 **미국 서부**를 사용합니다. 

   **Log Analytics**: 이 토글은 해제해야 합니다. 

   ![Logic App 만들기 화면을 보여 주는 스크린샷.](./media/tutorial-routing/create-logic-app.png)

   **만들기**를 클릭합니다.

4. 이제 Logic App으로 이동합니다. Logic App을 가져오는 가장 쉬운 방법은 **리소스 그룹**을 클릭하고, 사용자의 리소스 그룹을 선택(이 자습서에서는 **ContosoResources** 사용)한 다음, 리소스의 목록에서 Logic App을 선택합니다. Logic Apps 디자이너 페이지가 표시됩니다(전체 페이지를 표시하려면 오른쪽으로 스크롤해야 함). Logic Apps 디자이너 페이지에서 **빈 Logic App +** 이라는 타일이 표시될 때까지 아래로 스크롤한 후 클릭합니다. 

5. 커넥터의 목록이 표시됩니다. **Service Bus**를 선택합니다. 

   ![커넥터의 목록을 보여주는 스크린샷.](./media/tutorial-routing/logic-app-connectors.png)

6. 트리거의 목록이 표시됩니다. **Service Bus - 큐에 메시지가 수신될 때**(자동 완성)를 선택합니다. 

   ![Service Bus에 대한 트리거의 목록을 보여주는 스크린샷.](./media/tutorial-routing/logic-app-triggers.png)

6. 다음 화면에서 연결 이름을 입력합니다. 이 자습서에서는 **ContosoConnection**을 사용합니다. 

   ![Service Bus 큐에 대한 연결 설정을 보여주는 스크린샷.](./media/tutorial-routing/logic-app-define-connection.png)

   Service Bus 네임스페이스를 클릭합니다. 이 자습서에서는 **ContosoSBNamespace**를 사용합니다. 네임스페이스를 선택하면 포털이 Service Bus 네임스페이스를 쿼리하여 키를 검색합니다. **RootManageSharedAccessKey**를 선택하고 **만들기**를 클릭합니다. 
   
   ![연결 설정을 마치는 중인 스크린샷.](./media/tutorial-routing/logic-app-finish-connection.png)

7. 다음 화면에서 큐의 이름을 드롭다운 목록에서 선택합니다(이 자습서에서는 **contososbqueue** 사용). 필드의 나머지 부분에 기본값을 사용할 수 있습니다. 

   ![큐 옵션을 보여주는 스크린샷.](./media/tutorial-routing/logic-app-queue-options.png)

7. 이제 메시지가 큐에 수신되면 이메일을 보내는 동작을 설정합니다. Logic Apps 디자이너에서 **+새 단계**를 클릭하여 단계를 추가한 다음, **작업 추가**를 클릭합니다. **동작 선택** 창에서 **Office 365 Outlook**을 찾아 클릭합니다. 트리거 화면에서 **Office 365 Outlook - 이메일 보내기**를 선택합니다.  

   ![Office365 옵션을 보여주는 스크린샷.](./media/tutorial-routing/logic-app-select-outlook.png)

8. 다음으로 연결을 설정할 Office 365 계정에 로그인합니다. 이메일을 받는 사람에 대한 이메일 주소를 지정합니다. 또한 제목을 지정하고 본문에 받는 사람이 확인할 메시지를 입력합니다. 테스트를 위해 받는 사람으로 사용자의 이메일 주소를 입력합니다.

   사용자가 포함시킬 수 있는 메시지의 콘텐츠를 표시하려면 **동적 콘텐츠 추가**를 클릭합니다. **콘텐츠**를 선택합니다. 이메일에 메시지가 포함됩니다. 

   ![논리 앱에 대한 이메일 옵션을 보여주는 스크린샷.](./media/tutorial-routing/logic-app-send-email.png)

9. **저장**을 클릭합니다. 그런 다음, Logic Apps 디자이너를 닫습니다.

## <a name="set-up-azure-stream-analytics"></a>Azure Stream Analytics 설정

PowerBI 시각화에서 데이터를 보려면 먼저 데이터를 검색하는 Stream Analytics 작업을 설정합니다. **수준**이 **일반**인 메시지만 기본 엔드포인트에 전송되고, PowerBI 시각화에 대한 Stream Analytics 작업에 의해 검색됩니다.

### <a name="create-the-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업**을 차례로 클릭합니다.

2. 작업에 대한 다음 정보를 입력합니다.

   **작업 이름**: 작업의 이름입니다. 이름은 전역적으로 고유해야 합니다. 이 자습서에서는 **contosoJob**을 사용합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다. 이 자습서에서는 **ContosoResources**를 사용합니다. 

   **위치**: 설치 스크립트에 사용되는 동일한 위치를 사용합니다. 이 자습서에서는 **미국 서부**를 사용합니다. 

   ![스트림 분석 작업을 만드는 방법을 보여주는 스크린샷.](./media/tutorial-routing/stream-analytics-create-job.png)

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics 작업에 입력 추가

1. **작업 토폴로지**에서 **입력**을 클릭합니다.

2. **입력** 창에서 **스트림 입력 추가**를 클릭하고 IoT Hub를 선택합니다. 나타난 화면에서 다음 필드를 입력합니다.

   **입력 별칭**: 이 자습서에서는 **contosoinputs**를 사용합니다.

   **구독**: 사용자의 구독을 선택합니다.

   **IoT Hub**: IoT Hub를 선택합니다. 이 자습서에서는 **ContosoTestHub**를 사용합니다.

   **엔드포인트**: **메시징**을 선택합니다. (작업 모니터링을 선택하는 경우 전송하는 데이터가 아닌 IoT Hub에 대한 원격 분석 데이터를 얻게 됩니다.) 

   **공유 액세스 정책 이름**: **iothubowner**를 선택합니다. 포털에서 사용자를 위한 공유 액세스 정책 키가 채워집니다.

   **소비자 그룹**: 사용자가 이전에 만든 소비자 그룹을 선택합니다. 이 자습서에서는 **contosoconsumers**를 사용합니다.
   
   나머지 필드는 기본값을 그대로 사용합니다. 

   ![스트림 분석 작업을 위해 입력을 설정하는 방법을 보여주는 스크린샷.](./media/tutorial-routing/stream-analytics-job-inputs.png)

5. **저장**을 클릭합니다.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics 작업에 출력 추가

1. **작업 토폴로지**에서 **출력**을 클릭합니다.

2. **출력** 창에서 **추가**를 클릭한 다음, **PowerBI**를 선택합니다. 나타난 화면에서 다음 필드를 입력합니다.

   **출력 별칭**: 출력에 대한 고유 별칭입니다. 이 자습서에서는 **contosooutputs**를 사용합니다. 

   **데이터 집합 이름**: PowerBI에 사용할 데이터 집합의 이름입니다. 이 자습서에서는 **contosodataset**을 사용합니다. 

   **테이블 이름**: PowerBI에 사용할 테이블의 이름입니다. 이 자습서에서는 **contosotable**을 사용합니다.

   나머지 필드는 기본값을 그대로 사용합니다.

3. **권한 부여**를 클릭하고 사용자의 PowerBI 계정에 로그인합니다.

   ![스트림 분석 작업을 위해 출력을 설정하는 방법을 보여주는 스크린샷.](./media/tutorial-routing/stream-analytics-job-outputs.png)

4. **저장**을 클릭합니다.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics 작업의 쿼리 구성

1. **작업 토폴로지**에서 **쿼리**를 클릭합니다.

2. `[YourInputAlias]`를 작업의 입력 별칭으로 바꿉니다. 이 자습서에서는 **contosoinputs**를 사용합니다.

3. `[YourOutputAlias]`를 작업의 출력 별칭으로 바꿉니다. 이 자습서에서는 **contosooutputs**를 사용합니다.

   ![스트림 분석 작업을 위해 쿼리를 설정하는 방법을 보여주는 스크린샷.](./media/tutorial-routing/stream-analytics-job-query.png)

4. **저장**을 클릭합니다.

5. 쿼리 창을 닫습니다.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics 작업 실행

Stream Analytics 작업에서 **시작** > **지금 시작** > **시작**을 차례로 클릭합니다. 작업이 성공적으로 시작되면 작업 상태가 **중지됨**에서 **실행 중**으로 변경됩니다.

PowerBI 보고서를 설정하려면 장치를 만들고 장치 시뮬레이션 응용 프로그램을 실행한 후 PowerBI를 설정하기 위한 데이터가 필요합니다.

## <a name="run-simulated-device-app"></a>시뮬레이션된 장치 앱 실행

앞서 스크립트 설정 섹션에서 IoT 장치를 사용하여 시뮬레이션하도록 장치를 설정했습니다. 이 섹션에서는 IoT Hub로 장치-클라우드 메시지를 전송하는 장치를 시뮬레이션하는 .NET 콘솔 앱을 다운로드합니다. 이 응용 프로그램은 다양한 라우팅 메서드에 대해 메시지를 보냅니다. 

[IoT 장치 시뮬레이션](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)에 대한 솔루션을 다운로드합니다. 그러면 여러 응용 프로그램을 사용한 리포지토리가 다운로드됩니다. 원하는 솔루션은 Tutorials/Routing/SimulatedDevice/에 있습니다.

솔루션 파일(SimulatedDevice.sln)을 두 번 클릭하여 Visual Studio에서 코드를 연 다음, Program.cs를 엽니다. `{iot hub hostname}`을 IoT Hub 호스트 이름으로 대체합니다. IoT Hub 호스트 이름의 형식은 **{iot-hub-name}.azure-devices.net**입니다. 이 자습서의 경우 허브 호스트 이름은 **ContosoTestHub.azure-devices.net**입니다. 다음으로, `{device key}`를 이전에 시뮬레이션된 장치를 설정할 때 저장했던 장치 키로 대체합니다. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>실행 및 테스트 

콘솔 응용 프로그램을 실행합니다. 잠시 기다립니다. 응용 프로그램의 콘솔 화면에서 전송되는 메시지를 볼 수 있습니다.

앱은 1초마다 새로운 장치-클라우드 메시지를 IoT Hub에 보냅니다. 메시지에는 장치 ID, 온도, 습도 및 메시지 수준(기본값이 `normal`)과 함께 JSON 직렬화된 개체가 포함됩니다. `critical` 또는 `storage` 수준을 임의로 할당하므로 메시지는 저장소 계정 또는 Service Bus 큐로 라우팅됩니다(이메일을 보내도록 Logic App을 트리거). 기본값(`normal`) 판독값이 다음에 설정하는 BI 보고서에 표시됩니다.

모든 설정이 올바르면 다음과 같은 결과가 표시됩니다.

1. 중요한 메시지에 대한 이메일을 가져오기 시작합니다. 

   ![결과 이메일을 보여주는 스크린샷.](./media/tutorial-routing/results-in-email.png)

   이는 다음을 의미합니다.

   * Service Bus 큐에 대한 라우팅이 제대로 작동합니다.
   * Service Bus 큐의 메시지를 검색하는 Logic App이 제대로 작동합니다.
   * Outlook에 대한 Logic App 커넥터가 제대로 작동합니다. 

2. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹**을 클릭하고 사용자의 리소스 그룹을 선택합니다. 이 자습서에서는 **ContosoResources**를 사용합니다. 저장소 계정을 선택하고 **Blob**을 클릭한 다음, 컨테이너를 선택합니다. 이 자습서에서는 **contosoresults**를 사용합니다. 폴더를 확인하고 하나 이상의 파일이 표시될 때까지 디렉터리를 드릴다운할 수 있습니다. 이러한 파일 중 하나를 열면 저장소 계정으로 라우팅된 항목이 포함되어 있습니다. 

   ![저장소의 결과 파일을 보여주는 스크린샷.](./media/tutorial-routing/results-in-storage.png)

이는 다음을 의미합니다.

   * 저장소 계정에 대한 라우팅이 제대로 작동합니다.

이제 응용 프로그램이 계속 실행 중인 상태에서 기본 라우팅을 통해 들어오는 메시지를 표시하도록 PowerBI 시각화를 설정합니다. 

## <a name="set-up-the-powerbi-visualizations"></a>PowerBI 시각화 설정

1. [PowerBI](https://powerbi.microsoft.com/) 계정에 로그인합니다.

2. **작업 영역**으로 이동하고 Stream Analytics 작업에 대한 출력을 만들 때 설정한 작업 영역을 선택합니다. 이 자습서에서는 **My Workspace**를 사용합니다. 

3. **데이터 집합**을 클릭합니다.

   Stream Analytics 작업에 대한 출력을 만들 때 지정한 나열된 데이터 집합이 표시됩니다. 이 자습서에서는 **contosodataset**을 사용합니다. (데이터 집합을 처음 표시할 때는 5-10분이 걸릴 수 있습니다.)

4. **작업**에서 첫 번째 아이콘을 클릭하여 보고서를 만듭니다.

   ![강조 표시된 작업 및 보고서 아이콘이 있는 PowerBI 작업 영역을 보여주는 스크린샷.](./media/tutorial-routing/power-bi-actions.png)

5. 시간이 지남에 따라 실시간 온도를 표시하는 꺾은선형 차트를 만듭니다.

   a. 보고서 만들기 페이지에서 꺾은선형 차트를 클릭하여 꺾은선형 차트를 추가합니다.

   ![시각화 및 필드를 보여주는 스크린샷.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   나. **필드** 창에서 Stream Analytics 작업의 출력을 만들 때 지정한 테이블을 확장합니다. 이 자습서에서는 **contosotable**을 사용합니다.

   다. **시각화** 창에서 **EventEnqueuedUtcTime**을 **축**으로 끌어갑니다.

   d. **온도**를 **값**으로 끌어갑니다.

   꺾은선형 차트가 만들어집니다. x축은 UTC 표준 시간대의 날짜와 시간을 표시하고, y축은 센서의 온도를 표시합니다.

7. 시간이 지남에 따라 실시간 습도를 표시하는 꺾은선형 차트를 만듭니다. 두 번째 차트를 설정하려면 위와 동일한 단계를 수행하고 **EventEnqueuedUtcTime**을 x축에, **습도**를 y축에 배치합니다.

   ![두 개의 차트가 있는 최종 PowerBI 보고서를 보여주는 스크린샷.](./media/tutorial-routing/power-bi-report.png)

8. **저장**을 클릭하여 보고서를 저장합니다.

두 차트 모두에서 데이터를 볼 수 있습니다. 이는 다음을 의미합니다.

   * 기본 엔드포인트에 대한 라우팅이 제대로 작동합니다.
   * Azure Stream Analytics 작업이 제대로 스트리밍됩니다.
   * PowerBI 시각화가 제대로 설정되어 있습니다.

PowerBI 창 위쪽의 새로 고침 단추를 클릭하면 차트를 새로 고쳐서 가장 최근 데이터를 볼 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리 

사용자가 만든 리소스를 모두 제거하려면 리소스 그룹을 삭제합니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 경우 IoT Hub, Service Bus 네임스페이스 및 큐, Logic App, 저장소 계정 및 리소스 그룹 자체가 제거됩니다. 

### <a name="clean-up-resources-in-the-powerbi-visualization"></a>PowerBI 시각화에서 리소스 정리

[PowerBI](https://powerbi.microsoft.com/) 계정에 로그인합니다. 작업 영역으로 이동합니다. 이 자습서에서는 **My Workspace**를 사용합니다. PowerBI 시각화를 제거하려면 데이터 집합으로 이동하고 휴지통 아이콘을 클릭하여 데이터 집합을 삭제합니다. 이 자습서에서는 **contosodataset**을 사용합니다. 데이터 집합을 제거하면 보고서도 제거됩니다.

### <a name="clean-up-resources-using-azure-cli"></a>Azure CLI를 사용하여 리소스 정리

리소스 그룹을 제거하려면 [az group delete](https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령을 사용합니다.

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>PowerShell을 사용하여 리소스 정리

리소스 그룹을 제거하려면 [Remove-AzureRmResourceGroup](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용합니다. $resourceGroup은 이 자습서의 시작 부분에서 **ContosoIoTRG1**로 다시 설정되었습니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행하여 여러 대상에 IoT Hub 메시지를 라우팅하는 메시지 라우팅을 사용하는 방법을 배웠습니다.  

> [!div class="checklist"]
> * Azure CLI 또는 PowerShell을 사용하여 IoT Hub, 저장소 계정, Service Bus 큐 및 시뮬레이션된 장치와 같은 기본 리소스를 설정합니다.
> * 저장소 계정 및 Service Bus 큐에 대한 IoT Hub에서 엔드포인트 및 경로를 구성합니다.
> * 메시지가 Service Bus 큐에 추가될 때 트리거되어 이메일을 전송하는 Logic App을 만듭니다.
> * 다양한 라우팅 옵션에 대한 허브로 메시지를 전송하는 IoT 장치를 시뮬레이션하는 앱을 다운로드하여 실행합니다.
> * 기본 엔드포인트에 전송된 데이터에 대한 PowerBI 시각화를 만듭니다.
> * 다음에서 결과를 확인합니다.
> * ...Service Bus 큐 및 이메일에서
> * ...저장소 계정에서
> * ...PowerBI 시각화에서

IoT 장치의 상태를 관리하는 방법에 대해 알아보려면 다음 자습서로 이동합니다. 

> [!div class="nextstepaction"]
[Azure IoT Hub 장치 쌍 시작](iot-hub-node-node-twin-getstarted.md)

 <!--  [Manage the state of a device](./tutorial-manage-state.md) -->