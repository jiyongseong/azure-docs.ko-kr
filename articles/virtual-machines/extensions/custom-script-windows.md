---
title: Windows용 Azure 사용자 지정 스크립트 확장 | Microsoft Azure
description: 사용자 지정 스크립트 확장을 사용하여 Windows VM 구성 작업 자동화
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: danis
ms.openlocfilehash: 34c16b686a50994862bef14cefec1a4799a343c4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944945"
---
# <a name="custom-script-extension-for-windows"></a>Windows용 사용자 지정 스크립트 확장

사용자 지정 스크립트 확장은 Azure 가상 머신에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 스크립트를 Azure Storage 또는 GitHub에서 다운로드하거나 확장 런타임에서 Azure Portal에 제공할 수 있습니다. 사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합되고, Azure CLI, PowerShell, Azure Portal 또는 Azure Virtual Machine REST API를 사용하여 실행할 수도 있습니다.

이 문서에서는 Azure PowerShell 모듈, Azure Resource Manager 템플릿을 사용하는 사용자 지정 스크립트 확장을 사용하는 방법과 Windows 시스템에서 문제 해결 단계를 자세히 설명하고 있습니다.

## <a name="prerequisites"></a>필수 조건

> [!NOTE]  
> 해당 매개 변수와 동일한 VM을 사용하여 Update-AzureRmVM을 실행하려는 경우에는 사용자 지정 스크립트 확장을 사용하지 마세요. 대기 시간이 길어집니다.  
>   
> 

### <a name="operating-system"></a>운영 체제

Linux용 사용자 지정 스크립트 확장은 지원되는 확장 OS의 확장에서 실행됩니다. 자세한 내용은 [이 문서](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems)를 참조하세요.

### <a name="script-location"></a>스크립트 위치

확장은 Azure Blob 저장소 자격 증명을 사용하여 Azure Blob 저장소에 액세스하는 데 사용할 수 있습니다. 또는 스크립트 위치가 VM에서 해당 엔드포인트(예: GitHub, 내부 파일 서버 등)로 라우팅할 수 있는 모든 위치가 될 수 있습니다.


### <a name="internet-connectivity"></a>인터넷 연결
외부 스크립트(예: GitHub 또는 Azure Storage)를 다운로드해야 하는 경우 추가 방화벽/네트워크 보안 그룹 포트를 열어야 합니다. 예를 들어 스크립트가 Azure Storage에 있으면 [Storage](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags)에 Azure NSG 서비스 태그를 사용하여 액세스하도록 허용할 수 있습니다.

스크립트가 로컬 서버에 있으면 추가 방화벽/네트워크 보안 그룹 포트도 열어야 합니다.

### <a name="tips-and-tricks"></a>팁과 요령
* 이 확장에 대한 가장 큰 실패율은 스크립트의 구문 오류 때문이며, 오류 없이 스크립트가 실행되는지 테스트하고 실패한 위치를 쉽게 찾을 수 있도록 하는 스크립트에 추가 로깅을 배치합니다.
* idempotent(멱등원)인 스크립트를 작성합니다. 이렇게 하면 스크립트를 실수로 두 번 이상 다시 실행하더라도 시스템이 변경되지 않습니다.
* 스크립트를 실행할 때 사용자 입력이 필요하지 않도록 합니다.
* 스크립트를 실행하는 데 허용되는 시간은 90분입니다. 더 오래 걸리면 확장을 프로비전하는 데 실패합니다.
* 스크립트 내에 재부팅을 배치하지 않습니다. 그렇지 않으면 설치되는 다른 확장에 문제가 발생하고 재부팅 후 다시 시작하면 확장이 계속 실행되지 않습니다. 
* 재부팅을 유발하는 스크립트가 있으면 응용 프로그램을 설치하고 스크립트를 실행합니다. Windows 예약된 작업을 사용하거나 DSC 또는 Chef, Puppet 확장과 같은 도구를 사용하여 재부팅을 예약해야 합니다.
* 확장은 스크립트를 한 번만 실행합니다. 부팅할 때마다 스크립트를 실행하려면 확장을 사용하여 Windows 예약된 작업을 만들어야 합니다.
* 스크립트를 실행할 시기를 예약하려면 확장을 사용하여 Windows 예약된 작업을 만들어야 합니다. 
* 스크립트가 실행 중인 경우 Azure Portal 또는 CLI에서 '전환 중' 확장 상태만 표시됩니다. 실행 중인 스크립트의 상태 업데이트를 자주 수행하려면 사용자 고유의 솔루션을 만들어야 합니다.
* 사용자 지정 스크립트 확장은 기본적으로 프록시 서버를 지원하지 않지만, 스크립트 내에서 프록시 서버를 지원하는 파일 전송 도구(예: *Curl*)를 사용할 수 있습니다. 
* 스크립트 또는 명령에서 사용할 수 있는 기본 디렉터리가 아닌 위치를 알고 있어야 하고, 이를 처리할 논리가 있어야 합니다.


## <a name="extension-schema"></a>확장 스키마

사용자 지정 스크립트 확장 구성은 스크립트 위치 및 실행할 명령 등을 지정합니다. 이 구성을 구성 파일에 저장하거나, 명령줄에 지정하거나, Azure Resource Manager 템플릿에 지정할 수 있습니다. 

중요한 데이터는 보호된 구성에 저장하면 암호화된 후 가상 머신 내에서만 해독됩니다. 보호된 구성은 실행 명령에 암호와 같은 기밀 정보가 포함될 때 유용합니다.

이러한 항목은 중요한 데이터로 처리하고 확장으로 보호되는 설정 구성에 지정되어야 합니다. Azure VM 확장으로 보호되는 설정 데이터는 암호화되어 대상 가상 머신에서만 해독됩니다.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```
**참고** - 한 시점에 하나의 확장 버전만 VM에 설치할 수 있으며, 동일한 VM에 대한 동일한 Resource Manager 템플릿에서 사용자 지정 스크립트를 두 번 지정하면 오류가 발생합니다. 

### <a name="property-values"></a>속성 값

| Name | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | string |
| 형식 | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris(예) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| commandToExecute(예) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName(예) | examplestorageacct | string |
| storageAccountKey(예) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>이러한 속성 이름은 대/소문자를 구분합니다. 배포 문제를 방지하려면 다음과 같이 이름을 사용합니다.

#### <a name="property-value-details"></a>속성 값 세부 정보
 * `commandToExecute`: (**필수**, 문자열) 실행할 진입점 스크립트입니다. 명령에 암호와 같은 비밀이 포함되어 있거나 fileUris가 중요한 경우 이 필드를 대신 사용합니다.
* `fileUris`: (옵션, 문자열 배열) 다운로드할 파일에 대한 URL입니다.
* `storageAccountName`: (옵션, 문자열) 저장소 계정에 대한 이름입니다. 저장소 자격 증명을 지정하는 경우 모든 `fileUris`는 Azure Blob에 대한 URL이어야 합니다.
* `storageAccountKey`: (옵션, 문자열) 저장소 계정에 대한 액세스 키입니다.

다음 값은 공용 또는 보호된 설정 중 하나에서 설정할 수 있습니다. 확장은 공용 및 보호된 설정 모두에 아래 값이 설정된 모든 구성을 거부합니다.
* `commandToExecute`

공용 설정을 사용하면 디버깅에 유용할 수 있지만 보호된 설정을 사용하는 것이 좋습니다.

공용 설정은 스크립트를 실행할 VM에 일반 텍스트로 보내집니다.  보호된 설정은 Azure 및 VM에만 알려진 키를 사용하여 암호화됩니다. 설정은 보내진 대로 VM에 저장됩니다. 즉, 설정이 암호화된 경우 VM에서 암호화되어 저장됩니다. 암호화된 값의 암호를 해독하는 데 사용되는 인증서는 VM에 저장되고, 필요한 경우 런타임 시 설정의 암호를 해독하는 데 사용됩니다.

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명되어 있는 JSON 스키마는 Azure Resource Manager 템플릿에서 사용하여 Azure Resource Manager 템플릿 배포 중 사용자 지정 스크립트 확장을 실행할 수 있습니다. 사용자 지정 스크립트 확장이 포함된 샘플 템플릿은 여기 [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)에서 확인할 수 있습니다.

## <a name="powershell-deployment"></a>PowerShell 배포

`Set-AzureRmVMCustomScriptExtension` 명령을 사용하여 사용자 지정 스크립트 확장을 기존 가상 머신에 추가할 수 있습니다. 자세한 내용은 [Set-AzureRmVMCustomScriptExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension)을 참조하세요.
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>추가 예제

### <a name="using-multiple-script"></a>여러 스크립트 사용
다음 예제에는 서버를 빌드하는 데 사용되는 세 개의 스크립트가 있습니다. 'commandToExecute'에서 첫 번째 스크립트를 호출한 다음, 다른 스크립트를 호출하는 방법에 대한 옵션이 제공됩니다. 예를 들어 실행을 제어하는 마스터 스크립트, 올바른 오류 처리, 로깅 및 상태 관리를 제공합니다.

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzureRmVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>로컬 공유에서 스크립트 실행
다음 예제에서는 스크립트 위치에 로컬 SMB 서버를 사용하려고 할 수 있습니다. 참고: *commandToExecute*를 제외하고는 다른 설정에 전달할 필요가 없습니다.

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzureRmVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>CLI를 사용하여 두 번 이상 사용자 지정 스크립트를 실행하는 방법
사용자 지정 스크립트 확장을 두 번 이상 실행하려면 다음 조건에서만 이 작업을 수행할 수 있습니다.
1. 'Name' 확장 매개 변수는 확장의 이전 배포와 같습니다.
2. 구성을 업데이트해야 합니다. 그렇지 않으면 명령이 다시 실행되지 않습니다. 예를 들어 명령에 타임스탬프와 같은 동적 속성을 추가할 수 있습니다. 

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure PowerShell 모듈을 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 다음 명령을 실행합니다.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

확장 실행 출력은 대상 가상 머신의 다음 디렉터리 아래에 기록됩니다.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

지정된 파일은 대상 가상 머신의 다음 디렉터리에 다운로드됩니다.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
여기서 `<n>`은 확장의 실행 간에 변경될 수 있는 10진수 정수입니다.  `1.*` 값은 확장의 현재 실제 `typeHandlerVersion` 값과 일치합니다.  예를 들어, 실제 디렉터리는 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`일 수 있습니다.  

`commandToExecute` 명령을 실행하는 경우 확장에서 이 디렉터리(예: `...\Downloads\2`)를 현재 작업 디렉터리로 설정합니다. 그러면 `fileURIs` 속성을 통해 다운로드된 파일을 배치하는 상대 경로를 사용할 수 있습니다. 예제는 아래 테이블을 참조하세요.

시간이 지남에 따라 절대 다운로드 경로가 달라질 수 있으므로 가능한 경우 `commandToExecute` 문자열에서 상대 스크립트/파일 경로를 옵트인하는 것이 좋습니다. 예: 
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

첫 번째 URI 세그먼트 뒤의 경로 정보는 `fileUris` 속성 목록을 통해 다운로드된 파일에 유지됩니다.  아래 테이블에 표시된 대로 다운로드된 파일은 다운로드 하위 디렉터리에 매핑되어 `fileUris` 값의 구조를 반영합니다.  

#### <a name="examples-of-downloaded-files"></a>다운로드된 파일의 예

| fileUris의 URI | 다운로드된 상대 위치 | 다운로드된 절대 위치* |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* 위와 같이 절대 디렉터리 경로는 VM의 수명 동안 변경되지만, CustomScript 확장의 단일 실행 내에서는 변경되지 않습니다.

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
