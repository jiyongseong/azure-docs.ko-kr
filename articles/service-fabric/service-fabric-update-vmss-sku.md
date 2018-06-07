---
title: PrimaryNodeType용 SKU를 더 높은 SKU로 업그레이드/마이그레이션하는 절차 | Microsoft Docs
description: PowerShell 명령 및 CLI 명령을 사용하여 PrimaryNodeType용 SKU를 더 높은 SKU로 업그레이드/마이그레이션하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 8d5b560068a9e0bc0169bfdb98c5e939e34a3b8c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34274032"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>기본 노드 형식용 SKU를 더 높은 SKU로 업그레이드/마이그레이션

이 문서에서는 Azure PowerShell을 사용하여 서비스 패브릭 클러스터의 기본 노드 형식의 SKU를 더 높은 SKU로 업그레이드/마이그레이션하는 방법 설명

## <a name="add-a-new-virtual-machine-scale-set"></a>새 가상 머신 확장 집합 추가 

새 가상 머신 확장 집합 및 부하 분산 장치를 배포합니다. 새 가상 머신 확장 집합의 Service Fabric 확장 구성(특히 노드 유형)은 업그레이드 하려고 하는 이전 확장 집합과 동일해야 합니다. SF 탐색기에서 새 노드를 사용할 수 있는지 확인합니다. 

### <a name="azure-powershell"></a>Azure PowerShell
다음 예제에서는 *myResourceGroup*이라는 리소스 그룹을 사용하여 업데이트된 Resource Manager 템플릿 *template.json*을 배포하려면 Azure PowerShell을 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile \template\template.json 
```

기존 클러스터에서 기본 노드 유형을 사용하여 새 가상 머신 확장 집합 리소스를 추가하려면 json 템플릿을 수정하는 예제를 참조

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                        "storageAccountEndPoint": "https://core.windows.net/"
                                    },
                                    "publisher": "Microsoft.Azure.Diagnostics",
                                    "settings": {
                                        "WadCfg": {
                                            "DiagnosticMonitorConfiguration": {
                                                "overallQuotaInMB": "50000",
                                                "EtwProviders": {
                                                    "EtwEventSourceProviderConfiguration": [
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Actors",
                                                            "scheduledTransferKeywordFilter": "1",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableActorEventTable"
                                                            }
                                                        },
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Services",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                            }
                                                        }
                                                    ],
                                                    "EtwManifestProviderConfiguration": [
                                                        {
                                                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                            "scheduledTransferLogLevelFilter": "Information",
                                                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricSystemEventTable"
                                                            }
                                                        }
                                                    ]
                                                }
                                            }
                                        },
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```


## <a name="remove-old-virtual-machine-scale-set"></a>이전 가상 머신 확장 집합 제거

노드를 제거할 의도로 기존 가상 머신 확장 집합의 VM 인스턴스를 사용하지 않도록 설정합니다. 이 작업은 완료하는 데 시간이 오래 걸릴 수 있습니다. 한 번에 모두를 사용하지 않을 수 있으며 이들은 큐 대기 중입니다. 모든 노드를 사용하지 않을 때까지 기다립니다. 

### <a name="azure-powershell"></a>Azure PowerShell
다음 예제에서는 Azure Service Fabric PowerShell을 사용하여 *NTvm1*이라는 이전 가상 머신 확장 집합에서 *NTvm1_0*이라는 노드 인스턴스를 사용하지 않도록 설정합니다.

```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode 
```

전체 확장 집합을 제거합니다. 확장 집합 프로비저닝 상태가 성공할 때까지 대기 

### <a name="azure-powershell"></a>Azure PowerShell
다음 예제에서는 Azure PowerShell을 사용하여 *myResourceGroup*이라는 리소스 그룹에서 *NTvm1*이라는 전체 확장 집합을 제거합니다.

```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>이전 확장 집합과 관련된 부하 분산 장치 제거

이전 확장 집합과 관련된 부하 분산 장치를 제거합니다. 이 단계에서는 클러스터에 대한 짧은 가동 중지 시간이 발생

### <a name="azure-powershell"></a>Azure PowerShell
다음 예제에서는 Azure PowerShell을 사용하여 *myResourceGroup*이라는 리소스 그룹에서 이전 확장 집합과 관련된 *LB-myCluster-NTvm1*이라는 부하 분산 장치를 제거합니다.

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>이전 확장 집합과 관련된 공용 IP 제거

이전 확장 집합과 관련된 공용 IP 주소의 DNS 설정을 변수에 저장한 다음, 공용 IP 주소 제거

### <a name="azure-powershell"></a>Azure PowerShell
다음 예제에서는 Azure PowerShell을 사용하여 *LBIP-myCluster-NTvm1*이라는 공용 IP 주소의 DNS 설정을 변수에 저장하고 IP 주소를 제거합니다.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>새 확장 집합과 관련된 공용 IP 주소 업데이트

이전 확장 집합과 관련된 공용 IP 주소의 DNS 설정으로 새 확장 집합과 관련된 공용 IP 주소의 DNS 설정을 업데이트

### <a name="azure-powershell"></a>Azure PowerShell
다음 예제에서는 Azure PowerShell을 사용하여 *LBIP-myCluster-NTvm3*이라는 공용 IP 주소의 DNS 설정을 이전 단계에서 변수에 저장된 DNS 설정으로 업데이트합니다.

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP 
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>서비스 패브릭 노드 정보를 FM에서 제거

Service Fabric에 다운된 노드를 클러스터에서 제거했다고 알립니다. (이전 가상 머신 확장 집합의 모든 VM 인스턴스에 대해 이 명령을 실행) (이전 가상 머신 확장 집합의 내구성 수준이 실버 또는 골드인 경우 이 단계가 필요하지 않을 수 있습니다. 시스템에서 해당 단계를 자동으로 수행하기 때문입니다.)

### <a name="azure-powershell"></a>Azure PowerShell
다음 예제에서는 Azure Service Fabric PowerShell을 사용하여 *NTvm1_0*이라는 노드를 제거했다고 서비스 패브릭에 알립니다.

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```