---
title: Windows용 Azure Performance Diagnostics VM 확장 | Microsoft Docs
description: Windows용 Azure Performance Diagnostics VM 확장을 소개합니다.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 9ea7f4652aff07282c9c106f3894db807f341210
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Windows용 Azure Performance Diagnostics VM 확장

Azure 성능 진단 VM 확장을 통해 Windows VM의 성능 진단 데이터를 수집할 수 있습니다. 확장은 분석을 수행하고, 결과 및 권장 사항 보고서를 제공하여 가상 머신에서 성능 문제를 식별하고 해결합니다. 이 확장은 [PerfInsights](http://aka.ms/perfinsights)라는 문제 해결 도구를 설치합니다.

## <a name="prerequisites"></a>필수 조건

이 확장은 Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 및 Windows Server 2016에 설치될 수 있습니다. Windows 8.1 및 Windows 10에 설치할 수도 있습니다.

## <a name="extension-schema"></a>확장 스키마
다음 JSON은 Azure 성능 진단 VM 확장에 대한 스키마를 나타냅니다. 이 확장에는 진단 출력과 보고서를 저장하기 위해 저장소 계정의 이름과 키가 필요합니다. 이러한 값은 중요합니다. 저장소 계정 키는 보호되는 설정 구성 안에 저장되어야 합니다. Azure VM 확장으로 보호되는 설정 데이터는 암호화되어 대상 가상 머신에서만 해독됩니다. **storageAccountName** 및 **storageAccountKey**는 대소문자를 구분합니다. 다른 필수 매개 변수는 다음 섹션에 나열됩니다.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>속성 값

|   **Name**   |**값/예제**|       **설명**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|API 버전입니다.
|publisher|Microsoft.Azure.Performance.Diagnostics|확장의 게시자 네임스페이스입니다.
|형식|AzurePerformanceDiagnostics|VM 확장의 형식입니다.
|typeHandlerVersion|1.0|확장 처리기 버전입니다.
|performanceScenario|basic|데이터를 캡처할 성능 시나리오입니다. 유효한 값: **basic**, **vmslow**, **azurefiles** 및 **custom**
|traceDurationInSeconds|300|추적 옵션을 선택한 경우 추적 기간입니다.
|perfCounterTrace|p|성능 카운터 추적을 사용하는 옵션. 유효한 값은 **p** 또는 빈 값입니다. 이 추적을 캡처하지 않을 경우 빈 값으로 둡니다.
|networkTrace|n|네트워크 추적을 사용하는 옵션입니다. 유효한 값은 **n** 또는 빈 값입니다. 이 추적을 캡처하지 않을 경우 빈 값으로 둡니다.
|xperfTrace|x|XPerf 추적을 사용하는 옵션. 유효한 값은 **x** 또는 빈 값입니다. 이 추적을 캡처하지 않을 경우 빈 값으로 둡니다.
|storPortTrace|s|StorPort 추적을 사용하는 옵션. 유효한 값은 **s** 또는 빈 값입니다. 이 추적을 캡처하지 않을 경우 빈 값으로 둡니다.
|srNumber|123452016365929|사용 가능한 경우 지원 티켓 번호입니다. 값이 없으면 비워 둡니다.
|requestTimeUtc|2017-09-28T22:08:53.736Z|현재 날짜 시간(UTC). 포털을 사용하여 이 확장을 설치하는 경우 이 값을 제공하지 않아도 됩니다.
|storageAccountName|mystorageaccount|진단 로그 및 결과를 저장할 저장소 계정의 이름입니다.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|저장소 계정의 키입니다.

## <a name="install-the-extension"></a>확장 설치

Windows 가상 머신에서 확장을 설치하려면 다음 지침을 따릅니다.

1. [Azure 포털](http://portal.azure.com)에 로그인합니다.
2. 이 확장을 설치하려는 가상 컴퓨터를 선택합니다.

    ![가상 머신을 강조 표시한 Azure Portal의 스크린샷](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. **확장** 블레이드를 선택하고 **추가**를 선택합니다.

    ![추가를 강조 표시한 확장 블레이드 스크린샷](media/performance-diagnostics-vm-extension/select-extensions.png)
4. **Azure 성능 진단**을 선택하고, 사용 약관을 검토하고, **만들기**를 선택합니다.

    ![Azure 성능 진단을 강조 표시한 새 리소스 화면 스크린샷](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. 설치를 위한 매개 변수 값을 제공하고 **확인**을 선택하여 확장을 설치합니다. 지원되는 시나리오에 대한 자세한 내용은 [PerfInsights를 사용하는 방법](how-to-use-perfInsights.md#supported-troubleshooting-scenarios)을 참조하세요. 

    ![설치 확장 대화 상자 스크린샷](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. 설치에 성공하면 이 상태를 나타내는 메시지가 표시됩니다.

    ![프로비전 성공 메시지 스크린샷](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > 프로비전이 성공하는 경우 확장이 실행됩니다. 기본 시나리오에서 완료하는 데 최대 2분이 걸립니다. 다른 시나리오의 경우 설치 중 지정한 기간 동안 실행됩니다.

## <a name="remove-the-extension"></a>확장 제거
가상 머신에서 확장을 제거하려면 다음 단계를 따릅니다.

1. [Azure Portal](http://portal.azure.com)에 로그인하고, 이 확장을 제거하려는 가상 머신을 선택한 다음 **확장** 블레이드를 선택합니다. 
2. 목록에서 성능 진단 확장 항목에 대해 (**...**)를 클릭하고 **제거**를 선택합니다.

    ![제거를 강조 표시한 확장 블레이드 스크린샷](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > 확장 항목을 선택하고 **제거** 옵션을 선택할 수도 있습니다.

## <a name="template-deployment"></a>템플릿 배포
Azure Resource Manager 템플릿을 사용하여 Azure 가상 머신 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명된 JSON 스키마는 Azure Resource Manager 템플릿에서 사용할 수 있습니다. 그러면 Azure Resource Manager 템플릿을 배포하는 동안 Azure 성능 진단 VM 확장을 실행합니다. 샘플 템플릿은 다음과 같습니다.

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
        "protectedSettings": {            
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>PowerShell 배포
`Set-AzureRmVMExtension` 명령을 사용하여 Azure 성능 진단 VM 확장을 기존 가상 머신에 배포할 수 있습니다.

PowerShell

````
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
````

## <a name="information-on-the-data-captured"></a>수집된 데이터에 대한 정보
PerfInsights 도구는 선택한 시나리오에 따라 다양한 로그, 구성 및 진단 데이터를 수집합니다. 자세한 내용은 [PerfInsights 설명서](http://aka.ms/perfinsights)를 참조하세요.

## <a name="view-and-share-the-results"></a>결과 확인 및 공유

확장의 출력은 설치 중 지정된 저장소 계정에 업로드된 zip 파일에서 찾을 수 있으며 [SAS(공유 액세스 서명)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)를 사용하여 30일 동안 공유됩니다. 이 zip 파일은 진단 로그 및 결과 및 권장 사항이 있는 보고서를 포함합니다. 출력 zip 파일에 대한 SAS 링크는 폴더 **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\\\<version>** 아래의 *zipfilename*_saslink.txt라는 텍스트 파일 내에서 찾을 수 있습니다. 이 링크가 있는 모든 사람은 zip 파일을 다운로드할 수 있습니다.

Microsoft에서는 지원 티켓에서 작업하는 지원 엔지니어를 지원하기 위해 이 SAS 링크를 사용하여 진단 데이터를 다운로드할 수 있습니다.

보고서를 보려면 zip 파일의 압축을 풀고 **PerfInsights Report.html** 파일을 엽니다.

확장을 선택하여 포털에서 직접 zip 파일을 다운로드할 수도 있습니다.

![성능 진단 세부 상태 스크린샷](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> 경우에 따라 포털에 표시되는 SAS 링크가 작동하지 않을 수 있습니다. 인코딩 및 디코딩 작업 중에 형식이 잘못된 URL에서 발생할 수 있습니다. VM에서 직접 *_saslink.txt 파일의 링크를 직접 가져올 수 있습니다.

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

- 확장이 성공적으로 프로비전되어도 확장 배포 상태(알림 영역)에 "배포 진행 중"으로 표시될 수 있습니다.

    확장 상태가 확장이 성공적으로 표시되었다고 표시하는 한 이 문제는 무시해도 안전합니다.
- 확장 로그를 사용하여 설치 중 발생하는 일부 문제를 해결할 수 있습니다. 확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 **지원 받기**를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
