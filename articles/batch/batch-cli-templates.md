---
title: 코드를 작성하지 않고 종단 간 Azure Batch 작업 실행(미리 보기) | Microsoft Docs
description: Azure CLI에 템플릿 파일을 만들어서 Batch 풀, 작업 및 태스크를 만듭니다.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/18/2017
ms.author: markscu
ms.openlocfilehash: 0a6e355d8f16fed9022cc2cf55dc09781364f0b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Azure Batch CLI 템플릿 및 파일 전송 사용(미리 보기)

Azure CLI를 사용하여 코드를 작성하지 않고 Batch 작업을 실행할 수 있습니다.

Azure CLI로 템플릿 파일을 만들어서 Batch 풀, 작업 및 태스크 만들기에 사용합니다. Batch 계정과 연결된 저장소 계정에 작업 입력 파일을 쉽게 업로드하고 작업 출력 파일을 다운로드합니다.

## <a name="overview"></a>개요

Azure CLI로 확장하면 개발자가 아닌 사용자가 종단 간 Batch를 사용할 수 있습니다. CLI 명령만을 사용하여 풀을 만들고, 입력 데이터를 업로드하고, 작업 및 관련된 작업을 만들고, 결과 출력 데이터를 다운로드할 수 있습니다. 추가 코드가 필요하지 않습니다. CLI 명령을 직접 실행하거나 스크립트에 통합합니다.

Batch 템플릿은 풀, 작업, 태스크 및 기타 항목을 생성할 때 속성 값을 지정하는 JSON 파일에 대한 [Azure CLI의 기존 Batch 지원](batch-cli-get-started.md#json-files-for-resource-creation)에 기반하여 빌드됩니다. Batch 템플릿에서는 JSON 파일에 가능한 기능을 넘어 다음과 같은 기능이 추가되었습니다.

-   매개 변수를 정의할 수 있습니다. 템플릿을 사용하는 경우 템플릿 본문에 지정된 다른 항목 속성 값을 사용하여 해당 항목을 만들도록 매개 변수 값을 지정합니다. Batch 및 Batch에서 실행하는 응용 프로그램을 이해하는 사용자는 템플릿을 만들고 풀, 작업 및 태스크 속성 값을 지정할 수 있습니다. Batch 및/또는 응용 프로그램에 덜 익숙한 사용자는 정의된 매개 변수에 대한 값을 지정해야 합니다.

-   작업 태스크 팩터리는 작업과 연관된 하나 이상의 태스크를 만들고, 많은 작업 정의를 만들 필요없이 작업 제출 크게 간소화합니다.


작업은 일반적으로 입력 데이터 파일을 사용하고 출력 데이터 파일을 생성합니다. 저장소 계정은 기본적으로 각 Bach 계정을 사용하여 연결됩니다. 코딩 및 저장소 자격 증명 없이 CLI를 사용하여 이 저장소 계정 간 파일을 전송합니다.

예를 들어 [ffmpeg](http://ffmpeg.org/)는 오디오 및 비디오 파일을 처리하는 인기 있는 응용 프로그램입니다. 다음은 Azure Batch CLI를 사용하여 원본 비디오 파일을 다른 해상도로 코드 변환하기 위해 ffmpeg를 호출하는 단계입니다.

-   풀 템플릿을 만듭니다. 템플릿을 만드는 사용자는 ffmpeg 응용 프로그램 및 요구 사항을 호출하는 방법을 알고 있습니다. 적절한 OS, VM 크기, ffmpeg 설치 방법(예: 응용 프로그램 패키지에서 또는 패키지 관리자를 사용하여) 및 기타 풀 속성 값을 지정하면 됩니다. 매개 변수를 만들었으므로 템플릿이 사용될 경우 풀 ID와 VM의 수만을 지정하면 됩니다.

-   작업 템플릿을 만듭니다. 템플릿을 만드는 사용자는 ffmpeg를 호출하여 소스 비디오를 다른 해상도로 코드 변환하는 방법을 알고, 태스크 명령줄을 지정합니다. 또한 입력 파일당 필요한 태스크가 담긴 소스 비디오 파일을 포함하는 폴더가 있다는 것을 알고 있습니다.

-   코드를 변환할 비디오 파일 집합이 있는 최종 사용자가 먼저 풀 템플릿을 사용하여 풀을 만들고 풀 ID와 필요한 VM 수를 지정합니다. 코드 변환에 원본 파일을 업로드할 수 있습니다. 작업 템플릿을 사용하여 작업을 제출하고 풀 ID 및 업로드된 원본 파일의 위치를 지정할 수 있습니다. 입력 파일당 하나의 태스크가 있는 Batch 작업이 생성됩니다. 마지막으로 코드 변환 출력 파일을 다운로드할 수 있습니다.

## <a name="installation"></a>설치

Azure Batch CLI 확장을 설치하여 템플릿 및 파일 전송 기능을 사용합니다.

Azure CLI를 설치하는 방법에 대한 지침은 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

Azure CLI를 설치한 후 다음 CLI 명령을 사용하여 최신 버전의 Batch 확장을 설치합니다.

```azurecli
az extension add --name azure-batch-cli-extensions
```

Batch 확장에 대한 자세한 내용은 [ Windows, Mac 및 Linux용 Microsoft Azure Batch CLI 확장](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux)을 참조하세요.

## <a name="templates"></a>템플릿

Azure Batch CLI를 통해 속성 이름 및 값을 포함하는 JSON 파일을 지정하여 풀, 작업 및 태스크와 같은 항목을 만들 수 있습니다. 예: 

```azurecli
az batch pool create –-json-file AppPool.json
```

Azure Batch 템플릿은 Azure Resource Manager 템플릿과 기능 및 구문 면에서 비슷합니다. 항목 속성 이름 및 값을 포함하는 JSON 파일이지만 다음과 같은 주요 개념이 추가됩니다.

-   **매개 변수**

    -   템플릿이 사용될 때 제공해야 하는 매개 변수 값으로 속성 값을 본문 섹션에서 지정할 수 있습니다. 예를 들어 풀에 대한 전체 정의를 본문에 배치하고 하나의 매개 변수만을 풀 ID에 정의할 수 있습니다. 따라서 풀을 만드는 데 풀 ID 문자열을 제공해야 합니다.
        
    -   Batch 및 Batch에서 실행할 응용 프로그램에 대한 지식이 있는 사용자가 템플릿 본문을 작성할 수 있습니다. 템플릿이 사용될 때 작성자 정의 매개 변수에 대한 값만을 제공해야 합니다. 따라서 깊이 있는 Batch 및/또는 응용 프로그램 지식이 없는 사용자는 템플릿을 사용할 수 있습니다.

-   **변수**

    -   간단하거나 복잡한 매개 변수 값을 한 곳에서 지정하거나 템플릿 본문에 있는 하나 이상의 위치에서 사용할 수 있습니다. 변수는 템플릿의 크기를 단순화하고 줄일 뿐만 아니라 속성을 변경하도록 하나의 위치를 가지고 관리할 수 있도록 합니다.

-   **더 높은 수준의 구문**

    -   일부 높은 수준의 구문은 아직 Batch API에서 사용할 수 없는 템플릿에서 제공됩니다. 예를 들어 일반 태스크 정의를 사용하여 작업에 여러 태스크를 만드는 작업 템플릿에서 태스크 팩터리를 정의할 수 있습니다. 이러한 구문은 패키지 관리자를 통해 응용 프로그램을 설치하는 스크립트 파일을 만들 뿐만 아니라 태스크당 하나의 파일과 같이 여러 JSON 파일을 동적으로 만들도록 코딩할 필요가 없습니다.

    -   어느 시점에서 이러한 구문은 Batch 서비스에 추가되고 Batch API, UI 등에서 사용할 수 있습니다.

### <a name="pool-templates"></a>풀 템플릿

풀 템플릿은 매개 변수 및 변수의 표준 템플릿 기능을 지원합니다. 또한 다음과 같은 높은 수준의 구문을 지원합니다.

-   **패키지 참조**

    -   필요에 따라 패키지 관리자를 사용하여 소프트웨어를 풀 노드에 복사할 수 있습니다. 패키지 관리자 및 패키지 ID가 지정됩니다. 하나 이상의 패키지를 선언하여 필요한 패키지를 가져오는 스크립트를 만들고, 스크립트를 설치하고, 각 풀 노드에서 스크립트를 실행할 필요가 없습니다.

다음은 ffmpeg가 설치된 Linux VM의 풀을 만드는 템플릿의 예입니다. 이를 사용하려면 풀 ID 문자열 및 풀에 있는 VM의 수를 제공합니다.

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04.0-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

템플릿 파일의 이름이 _pool-ffmpeg.json_으로 지정되면 다음과 같이 템플릿을 호출합니다.

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>작업 템플릿

작업 템플릿은 매개 변수 및 변수의 표준 템플릿 기능을 지원합니다. 또한 다음과 같은 높은 수준의 구문을 지원합니다.

-   **태스크 팩터리**

    -   하나의 태스크 정의에서 작업에 여러 태스크를 만듭니다. 파라메트릭 스윕, 파일당 태스크 및 태스크 컬렉션이라는 세 가지 형식의 태스크 팩터리가 지원됩니다.

ffmpeg의 MP4 비디오 파일을 두 개 중 더 낮은 해상도로 코드 변환하는 작업을 만드는 템플릿의 예제는 다음과 같습니다. 원본 비디오 파일당 하나의 작업을 만듭니다.

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

템플릿 파일의 이름이 _job-ffmpeg.json_으로 지정되면 다음과 같이 템플릿을 호출합니다.

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>파일 그룹 및 파일 전송

대부분의 작업 및 태스크에는 입력 파일이 필요하고 결과로 출력 파일을 생성합니다. 일반적으로 입력 파일 및 출력 파일은 클라이언트에서 노드로, 또는 노드에서 클라이언트로 전송됩니다. Azure Batch CLI 확장은 파일 전송을 추상화하고 각 Batch 계정에 기본적으로 만든 저장소 계정을 사용합니다.

파일 그룹은 Azure Storage 계정에서 생성된 컨테이너와 동등합니다. 파일 그룹에는 하위 폴더가 있을 수 있습니다.

Batch CLI 확장은 파일을 클라이언트에서 지정된 파일 그룹으로 업로드하고 지정된 파일 그룹에서 클라이언트로 다운로드하는 명령을 제공합니다.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

풀 및 작업 템플릿을 통해 파일 그룹에 저장된 파일을 풀 노드에 복사하도록 지정하거나 풀 노드에서 파일 그룹에 지정할 수 있습니다. 예를 들어 이전에 지정한 작업 템플릿에서 파일 그룹 "ffmpeg-input"은 코드 변환을 위해 노드로 복사되는 원본 비디오 파일의 위치인 태스크 팩터리에 대해 지정됩니다. 파일 그룹 "ffmpeg-output"은 각 태스크를 실행하는 노드에서 코드 변환된 출력 파일이 복사되는 위치입니다.

## <a name="summary"></a>요약

템플릿 및 파일 전송 지원은 현재 Azure CLI에만 추가되었습니다. 목표는 연구원, IT 사용자와 같이 Batch API를 사용하여 코드를 개발할 필요가 없는 사용자로 Batch를 사용할 수 있는 대상 그룹을 확장하는 것입니다. Azure, Batch 및 Batch에서 실행하는 응용 프로그램에 대한 지식이 사용자는 코딩 없이 풀 및 작업 생성을 위해 템플릿을 만들 수 있습니다. Batch 및 응용 프로그램에 대한 세부 정보가 없는 사용자는 템플릿 매개 변수를 사용하여 템플릿을 사용할 수 있습니다.

Azure CLI에 Batch 확장을 사용해 보고 이 문서의 주석이나 [Azure Batch 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)을 통해 피드백이나 의견을 제공해주세요.

## <a name="next-steps"></a>다음 단계

- Batch 템플릿 블로그 게시물인 [Azure CLI를 사용하여 Azure Batch 작업 실행 - 코드가 필요하지 않음](https://azure.microsoft.com/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/)을 참조하세요.
- 자세한 설치 및 사용 설명서, 샘플 및 소스 코드는 [Azure GitHub 리포지토리](https://github.com/Azure/azure-batch-cli-extensions)에서 사용할 수 있습니다.
