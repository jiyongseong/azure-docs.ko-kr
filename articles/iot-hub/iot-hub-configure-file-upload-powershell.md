---
title: Azure PowerShell을 사용하여 파일 업로드 구성 | Microsoft Docs
description: Azure PowerShell cmdlet을 사용하여 연결된 장치에서 파일 업로드를 사용하도록 IoT Hub를 구성하는 방법입니다. 대상 Azure Storage 계정 구성에 대한 정보가 포함됩니다.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 22d331342504aa7cc3d4fe86777ef9c2595a27ac
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Azure PowerShell을 사용하여 IoT Hub 파일 업로드 구성

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

[IoT Hub의 파일 업로드 기능][lnk-upload]을 사용하려면 먼저 Azure 저장소 계정을 IoT Hub에 연결해야 합니다. 기존 저장소 계정을 사용하거나 새 저장소 계정을 만들 수 있습니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 내에 [계정][lnk-free-trial]을 만들 수 있습니다.
* [Azure PowerShell cmdlet][lnk-powershell-install]
* Azure IoT Hub - IoT Hub가 없는 경우 [New-AzureRmIoTHub cmdlet][lnk-powershell-iothub]을 사용하여 IoT Hub를 만들거나, 포털을 사용하여[IoT Hub를 만들][lnk-portal-hub] 수 있습니다.
* Azure 저장소 계정. Azure 저장소 계정이 없는 경우 [Azure Storage PowerShell cmdlet][lnk-powershell-storage]을 사용하여 저장소 계정을 만들거나, 포털을 사용하여[저장소 계정을 만들][lnk-portal-storage] 수 있습니다.

## <a name="sign-in-and-set-your-azure-account"></a>Azure 계정 로그인 및 설정

Azure 계정에 로그인하고 구독을 선택합니다.

1. PowerShell 프롬프트에서 **Connect-AzureRmAccount** cmdlet을 실행합니다.

    ```powershell
    Connect-AzureRmAccount
    ```

1. Azure 구독이 여러 개 있는 경우 Azure에 로그인하면 자격 증명과 연결된 모든 Azure 구독에 액세스할 수 있습니다. 다음 명령을 사용하여 사용할 수 있는 Azure 구독을 나열합니다.

    ```powershell
    Get-AzureRMSubscription
    ```

    다음 명령을 사용하여 IoT Hub를 관리하는 명령을 실행하는 데 사용할 구독을 선택합니다. 이전 명령의 출력에서 구독 이름 또는 ID를 사용할 수 있습니다.

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>저장소 계정 세부 정보 검색

다음 단계에서는 **클래식** 배포 모델이 아니라 **Resource Manager** 배포 모델을 사용하여 저장소 계정을 만들었다고 가정합니다.

장치에서 파일 업로드를 구성하려면 Azure 저장소 계정에 대한 연결 문자열이 필요합니다. 저장소 계정은 IoT Hub와 동일한 구독 내에 있어야 합니다. 또한 저장소 계정에 Blob 컨테이너의 이름도 필요합니다. 다음 명령을 사용하여 저장소 계정 키를 검색합니다.

```powershell
Get-AzureRmStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

**key1** 저장소 계정 키 값을 기록해 둡니다. 다음 단계에서 필요합니다.

파일 업로드에 기존 Blob 컨테이너를 사용하거나 새 Blob 컨테이너를 만들 수 있습니다.

* 저장소 계정의 기존 Blob 컨테이너를 나열하려면 다음 명령을 사용합니다.

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzureStorageContainer -Context $ctx
    ```

* 저장소 계정에 Blob 컨테이너를 만들려면 다음 명령을 사용합니다.

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzureStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>IoT Hub 구성

이제 저장소 계정 세부 정보를 사용하여 [파일 업로드 기능][lnk-upload]을 사용하도록 IoT Hub를 구성할 수 있습니다.

구성에는 다음 값이 필요합니다.

**저장소 컨테이너**: 현재 Azure 구독에 있는 Azure Storage 계정의 Blob 컨테이너로 IoT Hub와 연결됩니다. 이전 섹션에서 필요한 저장소 계정 정보를 검색했습니다. IoT Hub는 파일을 업로드하는 경우에 사용할 장치에 대한 이 Blob 컨테이너에 쓰기 권한이 있는 SAS URI를 자동으로 생성합니다.

**업로드된 파일에 대한 알림 받기**: 파일 업로드 알림을 사용하거나 사용하지 않도록 설정합니다.

**SAS TTL**: 이 설정은 IoT Hub에서 장치로 반환하는 SAS URI의 TTL(Time-to-Live)입니다. 기본적으로 1시간으로 설정합니다.

**파일 알림 설정 기본 TTL**: 만료되기 전의 파일 업로드 알림 TTL입니다. 기본적으로 1일로 설정합니다.

**파일 알림 최대 배달 횟수**: IoT Hub가 파일 업로드 알림 배달을 시도하는 횟수입니다. 기본적으로 10으로 설정합니다.

다음 PowerShell cmdlet을 사용하여 IoT Hub의 파일 업로드 설정을 구성합니다.

```powershell
Set-AzureRmIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>다음 단계

IoT Hub의 파일 업로드 기능에 대한 자세한 내용은 [장치에서 파일 업로드][lnk-upload]를 참조하세요.

Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [IoT 장치 대량 관리][lnk-bulk]
* [IoT Hub 메트릭][lnk-metrics]
* [작업 모니터링][lnk-monitor]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포][lnk-iotedge]
* [처음부터 IoT 솔루션 보안 유지][lnk-securing]

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-powershell-storage]: https://docs.microsoft.com/powershell/module/azurerm.storage/
[lnk-powershell-iothub]: https://docs.microsoft.com/powershell/module/azurerm.iothub/new-azurermiothub
[lnk-portal-hub]: iot-hub-create-through-portal.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
