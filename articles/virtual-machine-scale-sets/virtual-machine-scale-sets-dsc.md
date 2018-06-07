---
title: Virtual Machine Scale Sets에 필요한 상태 구성 사용 | Microsoft Docs
description: Azure DSC 확장에 Virtual Machine Scale Sets 사용
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: a68a5f31952d636c054b66dc0bb6ec0579cd7192
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Azure DSC 확장에 Virtual Machine Scale Sets 사용
[Azure DSC(필요한 상태 구성)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 확장 처리기에 [Virtual Machine Scale Sets](virtual-machine-scale-sets-overview.md)를 사용할 수 있습니다. 가상 머신 확장 집합은 많은 수의 가상 머신을 배포 및 관리하는 방법을 제공하며 부하에 따라 탄력적으로 확장 및 축소될 수 있습니다. DSC는 VM이 온라인으로 전환되어 프로덕션 소프트웨어를 실행하도록 VM을 구성하는 데 사용합니다.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Virtual Machines 및 Virtual Machine Scale Sets에 대한 배포 간의 차이점
가상 머신 확장 집합에 대한 기본 템플릿 구조는 단일 VM과 약간 다릅니다. 특히, 단일 VM은 확장을 "virtualMachines" 노드 아래에 배포합니다. DSC가 템플릿에 추가된 "extensions" 유형의 항목이 있습니다.

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

가상 머신 확장 집합 노드에는 "VirtualMachineProfile", "extensionProfile" 특성을 포함하는 "properties" 섹션이 있습니다. "extensions" 아래에 DSC가 추가됩니다.

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Virtual Machine Scale Set의 동작
가상 머신 확장 집합의 동작은 단일 VM의 동작과 동일합니다. 새 VM을 만들 때 DSC 확장으로 자동으로 프로비전됩니다. 확장에 최신 버전의 WMF가 필요한 경우 확장을 온라인으로 전환하려면 VM을 다시 부팅합니다. 온라인 상태이면 DSC 구성.zip을 다운로드하고 VM에 프로비전합니다. 자세한 내용은 [Azure DSC 확장 개요](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[DSC 확장에 대한 Azure Resource Manager 템플릿](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 검토합니다.

[DSC 확장이 자격 증명을 안전하게 처리](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하는 방법을 알아봅니다. 

Azure DSC 확장 처리기에 대한 자세한 내용은 [Azure 필요한 상태 구성 확장 처리기 소개](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. 

PowerShell DSC에 대한 자세한 내용은 [PowerShell 설명서 센터를 방문하세요](https://msdn.microsoft.com/powershell/dsc/overview). 

