---
title: Desired State Configuration 확장과 Azure Resource Manager 템플릿
description: Azure의 DSC(Desired State Configuration) 확장에 대한 Resource Manager 템플릿 정의에 대해 자세히 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: d19d33d7d8d19923e814b8685c3e56553f85850d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012481"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Desired State Configuration 확장과 Azure Resource Manager 템플릿

이 문서에서는 [DSC(Desired State Configuration) 확장 처리기](dsc-overview.md)를 위한 Azure Resource Manager 템플릿에 대해 설명합니다.

> [!NOTE]
> 약간 다른 스키마 예제가 제공될 수 있습니다. 스키마 변경은 2016년 10월 릴리스에서 수행되었습니다. 자세한 내용은 [이전 형식에서 업데이트](#update-from-the-previous-format)를 참조하세요.

## <a name="template-example-for-a-windows-vm"></a>Windows VM의 템플릿 예제

다음 코드 조각은 템플릿의 **리소스** 섹션에 대한 코드입니다.
DSC 확장은 기본 확장 속성을 상속합니다.
자세한 내용은 [VirtualMachineExtension 클래스](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.)를 참조하세요.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.76",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "protectedSettings": {
                "Items": {
                    "registrationKeyPrivate": "registrationKey"
                }
            },
            "publicSettings": {
                "configurationArguments": [{
                        "Name": "RegistrationKey",
                        "Value": {
                            "UserName": "PLACEHOLDER_DONOTUSE",
                            "Password": "PrivateSettingsRef:registrationKeyPrivate"
                        }
                    },
                    {
                        "RegistrationUrl": "registrationUrl",
                    },
                    {
                        "NodeConfigurationName": "nodeConfigurationName"
                    }
                ]
            }
        }
    }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Windows Virtual Machine Scale Sets에 대한 템플릿 예제

가상 머신 확장 집합 노드에는 **VirtualMachineProfile, extensionProfile** 특성을 갖는 **속성** 섹션이 있습니다.
**확장** 아래에 DSC 확장의 세부 정보를 추가합니다.

DSC 확장은 기본 확장 속성을 상속합니다.
자세한 내용은 [VirtualMachineScaleSetExtension 클래스](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet)를 참조하세요.

```json
"extensionProfile": {
    "extensions": [{
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.Powershell",
            "type": "DSC",
            "typeHandlerVersion": "2.76",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "protectedSettings": {
                    "Items": {
                        "registrationKeyPrivate": "registrationKey"
                    }
                },
                "publicSettings": {
                    "configurationArguments": [{
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        {
                            "RegistrationUrl": "registrationUrl",
                        },
                        {
                            "NodeConfigurationName": "nodeConfigurationName"
                        }
                    ]
                }
            },
        }
    }]
}
```

## <a name="detailed-settings-information"></a>자세한 설정 정보

Resource Manager 템플릿에 있는 Azure DSC 확장의 **설정** 섹션에서 다음 스키마를 사용합니다.

기본 구성 스크립트에서 사용할 수 있는 인수 목록은 [기본 구성 스크립트](#default-configuration-script) 섹션을 참조하세요.

```json
"settings": {
    "wmfVersion": "latest",
    "configuration": {
        "url": "http://validURLToConfigLocation",
        "script": "ConfigurationScript.ps1",
        "function": "ConfigurationFunction"
    },
    "configurationArguments": {
        "argument1": "Value1",
        "argument2": "Value2"
    },
    "configurationData": {
        "url": "https://foo.psd1"
    },
    "privacy": {
        "dataCollection": "enable"
    },
    "advancedOptions": {
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
        }
    }
},
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        },
        "parameterOfTypePSCredential2": {
            "userName": "UsernameValue2",
            "password": "PasswordValue2"
        }
    },
    "configurationUrlSasToken": "?g!bber1sht0k3n",
    "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}
```

## <a name="details"></a>세부 정보

| 속성 이름 | type | 설명 |
| --- | --- | --- |
| settings.wmfVersion |string |VM에 설치해야 하는 WMF(Windows Management Framework)의 버전을 지정합니다. 이 속성을 **latest**'로 설정하면 WMF의 가장 최신 버전이 설치됩니다. 현재, 이 속성에 대해 사용할 수 있는 값은 **4.0**, **5.0**, **5.0PP** 및 **latest**뿐입니다. 가능한 값은 업데이트에 따라 달라집니다. 기본값은 **latest**입니다. |
| settings.configuration.url |string |DSC 구성 .zip 파일을 다운로드할 URL 위치를 지정합니다. 제공된 URL에서 액세스를 위해 SAS 토큰을 요구하는 경우 **protectedSettings.configurationUrlSasToken** 속성을 SAS 토큰 값으로 설정합니다. **settings.configuration.script** 또는 **settings.configuration.function**이 정의된 경우 이 속성이 필요합니다. 이러한 속성에 대한 값을 지정하지 않으면 확장은 기본 구성 스크립트를 호출하여 LCM(위치 구성 관리자) 메타데이터를 설정하며, 인수를 지정해야 합니다. |
| settings.configuration.script |string |DSC 구성의 정의를 포함하는 스크립트의 파일 이름을 지정합니다. 이 스크립트는 **configuration.url** 속성에 지정된 URL에서 다운로드된 zip 파일의 루트 폴더에 있어야 합니다. **settings.configuration.url** 또는 **settings.configuration.script**가 정의된 경우 이 속성이 필요합니다. 이러한 속성에 대한 값이 없는 경우 확장은 기본 구성 스크립트를 호출하여 제공해야 할 LCM 메타데이터 및 인수를 설정합니다. |
| settings.configuration.function |string |DSC 구성의 이름을 지정합니다. 명명된 구성은 **configuration.script**에서 정의하는 스크립트에 포함되어야 합니다. **settings.configuration.url** 또는 **settings.configuration.function**이 정의된 경우 이 속성이 필요합니다. 이러한 속성에 대한 값이 없는 경우 확장은 기본 구성 스크립트를 호출하여 제공해야 할 LCM 메타데이터 및 인수를 설정합니다. |
| settings.configurationArguments |컬렉션 |DSC 구성에 전달하려는 매개 변수를 정의합니다. 이 속성은 암호화되지 않습니다. |
| settings.configurationData.url |string |DSC 구성에 대한 입력으로 사용할 구성 데이터(.psd1) 파일을 다운로드할 URL을 지정합니다. 제공된 URL에서 액세스를 위해 SAS 토큰을 요구하는 경우 **protectedSettings.configurationDataUrlSasToken** 속성을 SAS 토큰 값으로 설정합니다. |
| settings.privacy.dataEnabled |string |원격 분석 수집을 사용하거나 사용하지 않도록 설정합니다. 이 속성에 사용할 수 있는 값은 **Enable**, **Disable**, **''** 또는 **$null**뿐입니다. 이 속성을 비워 두거나 null로 설정하면 원격 분석이 사용됩니다. 기본값은 **''** 입니다. 자세한 내용은 [Azure DSC 확장 데이터 컬렉션](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/)을 참조하세요. |
| settings.advancedOptions.downloadMappings |컬렉션 |WMF를 다운로드할 대체 위치를 정의합니다. 자세한 내용은 [Azure DSC extension 2.8 and how to map downloads of the extension dependencies to your own location](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx)(Azure DSC 확장 2.8 및 확장 종속성의 다운로드를 사용자 고유의 위치에 매핑하는 방법)을 참조하세요. |
| protectedSettings.configurationArguments |컬렉션 |DSC 구성에 전달하려는 매개 변수를 정의합니다. 이 속성은 암호화됩니다. |
| protectedSettings.configurationUrlSasToken |string |**configuration.url**에서 정의한 URL에 액세스하기 위해 사용할 SAS 토큰을 지정합니다. 이 속성은 암호화됩니다. |
| protectedSettings.configurationDataUrlSasToken |string |**configurationData.url**에서 정의한 URL에 액세스하기 위해 사용할 SAS 토큰을 지정합니다. 이 속성은 암호화됩니다. |

## <a name="default-configuration-script"></a>기본 구성 스크립트

다음 값에 대한 자세한 내용은 [로컬 구성 관리자 기본 설정](/powershell/dsc/metaconfig#basic-settings)을 참조하세요.
DSC 확장 기본 구성 스크립트를 사용하여 다음 표에 나열되어 있는 LCM 속성만 구성할 수 있습니다.

| 속성 이름 | type | 설명 |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |securestring |필수 속성입니다. 노드에서 Azure Automation 서비스에 등록하는 데 PowerShell 자격 증명 개체의 암호로 사용되는 키를 지정합니다. 이 값은 Automation 계정에 대해 **listkeys** 메서드를 사용하여 자동으로 검색할 수 있습니다. 값을 보호 설정으로 보호해야 합니다. |
| settings.configurationArguments.RegistrationUrl |string |필수 속성입니다. 노드가 등록하려는 Automation 끝점의 URL을 지정합니다. 이 값은 Automation 계정에 대해 **reference** 메서드를 사용하여 자동으로 검색될 수 있습니다. |
| settings.configurationArguments.NodeConfigurationName |string |필수 속성입니다. 노드를 할당할 Automation 계정의 노드 구성을 지정합니다. |
| settings.configurationArguments.ConfigurationMode |string |LCM의 모드를 지정합니다. 올바른 옵션에는 **ApplyOnly**, **ApplyandMonitor** 및 **ApplyandAutoCorrect**가 포함됩니다.  기본값은 **ApplyandMonitor**입니다. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | LCM이 업데이트를 위해 Automation 계정을 확인하는 빈도를 지정합니다.  기본값은 **30**입니다.  최소값은 **15**입니다. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | LCM이 현재 구성의 유효성을 검사하는 빈도를 지정합니다. 기본값은 **15**입니다. 최소값은 **15**입니다. |
| settings.configurationArguments.RebootNodeIfNeeded | 부울 | DSC 작업에서 요청할 경우 노드를 자동으로 다시 부팅할 수 있는지 여부를 지정합니다. 기본값은 **false**입니다. |
| settings.configurationArguments.ActionAfterReboot | string | 구성을 적용하는 경우 다시 부팅 후 수행할 작업을 지정합니다. 유효한 옵션은 **ContinueConfiguration** 및 **StopConfiguration**입니다. 기본값은 **ContinueConfiguration**입니다. |
| settings.configurationArguments.AllowModuleOverwrite | 부울 | LCM이 노드의 기존 모듈을 덮어쓸지 여부를 지정합니다. 기본값은 **false**입니다. |

## <a name="settings-vs-protectedsettings"></a>Settings 및 ProtectedSettings

모든 설정은 VM의 설정 텍스트 파일에 저장됩니다.
**설정** 아래에 나열된 속성은 공용 속성입니다.
공용 속성은 설정 텍스트 파일에서 암호화되지 않습니다.
**protectedSettings** 아래에 나열된 속성은 인증서를 사용하여 암호화되므로 VM에서 설정 파일에 일반 텍스트로 표시되지 않습니다.

구성에 자격 증명이 필요한 경우 **protectedSettings**에 자격 증명을 포함할 수 있습니다.

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>예제 구성 스크립트

다음 예제는 DSC 확장의 기본 동작으로, LCM에 메타데이터 설정을 제공하고 Azure Automation DSC 서비스에 등록하는 것을 보여줍니다.
구성 인수가 필요합니다.
구성 인수는 LCM 메타데이터를 설정하기 위해 기본 구성 스크립트에 전달됩니다.

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl": "[parameters('registrationUrl1')]",
        "NodeConfigurationName": "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "Items": {
        "registrationKeyPrivate": "[parameters('registrationKey1']"
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Azure Storage의 구성 스크립트 사용 예제

다음 예제는 [DSC 확장 처리기 개요](dsc-overview.md)에서 가져온 것입니다.
이 예제에서는 cmdlet 대신 Resource Manager 템플릿을 사용하여 확장을 배포합니다.
IisInstall.ps1 구성을 저장하고 .zip 파일에 배치한 다음 파일을 액세스 가능한 URL에 업로드합니다.
이 예제에서는 Azure Blob 저장소를 사용하지만 임의의 위치에서 .zip 파일을 다운로드할 수 있습니다.

Resource Manager 템플릿에서 다음 코드는 VM에 올바른 파일을 다운로드하고 적절한 PowerShell 함수를 실행하도록 지시합니다.

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="update-from-a-previous-format"></a>이전 형식에서 업데이트

이전 형식의 모든 설정(공용 속성 **ModulesUrl**, **ConfigurationFunction**, **SasToken** 또는 **Properties** 포함)은 확장의 현재 형식으로 자동 조정됩니다.
이전과 마찬가지로 실행됩니다.

다음 스키마는 이전 설정 스키마를 보여줍니다.

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

다음은 현재 형식에 맞게 이전 형식을 조정하는 방법입니다.

| 속성 이름 | 이전 스키마에 해당 |
| --- | --- |
| settings.wmfVersion |settings.WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |settings.ConfigurationFunction의 첫 번째 부분(\\\\ 이전) |
| settings.configuration.function |settings.ConfigurationFunction의 두 번째 부분(\\\\ 이후) |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri(SAS 토큰 없이) |
| settings.privacy.dataEnabled |settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |protectedSettings.DataBlobUri의 SAS 토큰 |

## <a name="troubleshooting---error-code-1100"></a>문제 해결 - 오류 코드 1100

오류 코드 1100은 DSC 확장에 대한 사용자 입력에 문제가 있다는 것을 나타냅니다.
이러한 오류의 텍스트는 다르며 변경될 수 있습니다.
다음은 발생할 수 있는 일부 오류와 해결 방법입니다.

### <a name="invalid-values"></a>잘못된 값

"Privacy.dataCollection이 '{0}'입니다.
유일하게 가능한 값은 '', 'Enable' 및 'Disable'"입니다.
"WmfVersion이 '{0}'입니다.
유일하게 가능한 값은 … 및 'latest'"입니다.

**문제점**: 제공된 값이 허용되지 않습니다.

**해결 방법**: 잘못된 값을 올바른 값으로 변경합니다.
자세한 내용은 [세부 정보](#details)의 표를 참조하세요.

### <a name="invalid-url"></a>잘못된 URL

"ConfigurationData.url은 '{0}'입니다. 유효한 URL이 아닙니다." "DataBlobUri는 '{0}'입니다. 유효한 URL이 아닙니다." "Configuration.url이 '{0}'입니다. 유효한 URL이 아닙니다."

**문제점**: 제공된 URL이 유효하지 않습니다.

**해결 방법**: 제공된 모든 URL을 확인합니다.
모든 URL이 원격 컴퓨터의 확장 기능에서 액세스할 수 있는 올바른 위치인지 확인합니다.

### <a name="invalid-configurationargument-type"></a>잘못된 ConfigurationArgument 형식

"잘못된 configurationArguments 형식 {0}입니다."

**문제점**: *ConfigurationArguments* 속성을 **Hashtable** 개체로 확인할 수 없습니다.

**해결 방법**: *ConfigurationArguments* 속성을 **Hashtable**로 지정합니다.
위의 예제에 제공된 형식을 따릅니다. 따옴표, 쉼표 및 중괄호를 확인합니다.

### <a name="duplicate-configurationarguments"></a>중복 ConfigurationArguments

"공용 및 보호된 configurationArguments에 중복 인수 '{0}'이 있습니다."

**문제점**: 공용 설정의 *ConfigurationArguments* 및 보호된 설정의 *ConfigurationArguments*에 동일한 이름의 속성이 포함되어 있습니다.

**해결 방법**: 중복된 속성 중 하나를 제거합니다.

### <a name="missing-properties"></a>누락된 속성

"Configuration.function을 사용하려면 configuration.url 또는 configuration.module을 지정해야 합니다."

"Configuration.url을 사용하려면 configuration.script를 지정해야 합니다."

"Configuration.script를 사용하려면 configuration.url을 지정해야 합니다."

"Configuration.url을 사용하려면 configuration.function을 지정해야 합니다."

"ConfigurationUrlSasToken을 사용하려면 configuration.url을 지정해야 합니다."

"ConfigurationDataUrlSasToken을 사용하려면 configurationData.url을 지정해야 합니다."

**문제점**: 정의된 속성에 누락된 다른 속성이 필요합니다.

**해결 방법**:

- 누락된 속성을 제공합니다.
- 누락된 속성을 요구하는 속성을 제거합니다.

## <a name="next-steps"></a>다음 단계

- [Azure DSC 확장에 Virtual Machine Scale Sets 사용](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)에 대해 자세히 알아봅니다.
- [DSC의 보안 자격 증명 관리](dsc-credentials.md)에 대해 자세히 알아봅니다.
- [Azure DSC 확장 처리기에 대한 소개](dsc-overview.md)를 참조하세요.
- PowerShell DSC에 대한 자세한 내용은 [PowerShell 설명서 센터](/powershell/dsc/overview)를 참조하세요.
