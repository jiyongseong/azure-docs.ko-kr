---
title: Azure ML의 계산 대상으로 DSVM 및 HDI를 만드는 방법
description: Azure ML 실험을 위한 계산 대상으로 DSVM 및 HDI Spark 클러스터를 만듭니다.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 1229a66ec84b4272337a5dd1e17942e46b25e9a0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>계산 대상으로 DSVM 및 HDI Spark 클러스터 만들기

Ubuntu 기반 DSVM(Data Science Virtual Machine) 및 Azure HDInsight 클러스터용 Apache Spark와 같은 추가 계산 대상을 추가하여 기계 학습 실험을 쉽게 강화하거나 확장할 수 있습니다. 이 문서는 Azure에서 이러한 계산 대상을 만드는 단계를 안내합니다. Azure ML 계산 대상에 대한 자세한 내용은 [Azure Machine Learning 실험 서비스 개요](experimentation-service-configuration.md)를 참조하세요.

>[!NOTE]
>진행하기 전에 Azure에서 VM 및 HDI 클러스터와 같은 리소스를 만들 수 있는 적절한 권한이 있는지 확인해야 합니다. 또한 이러한 리소스는 모두 구성에 따라 많은 계산 코어를 사용할 수 있습니다. 구독의 가상 CPU 코어에 대한 용량이 충분한지 확인하십시오. 구독에 허용되는 최대 코어 수를 늘리려면 Azure 지원에 언제든지 문의하세요.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Azure Portal에서 Ubuntu DSVM 만들기

Azure Portal에서 DSVM을 만들 수 있습니다. 

1. https://portal.azure.com에서 Azure Portal에 로그온
2. **+새로 만들기** 링크를 클릭하고 "Linux용 데이터 과학 가상 머신"를 검색합니다.
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. 목록에서 **Linux(Ubuntu)용 데이터 과학 Virtual Machine**을 선택하고 화면의 지침에 따라 DSVM을 만듭니다.

>[!IMPORTANT]
>_인증 유형_으로 **암호**를 선택해야 합니다.

![pwd 사용](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>azure-cli를 사용하여 Ubuntu DSVM 만들기

Azure 리소스 관리 템플릿을 사용하여 DSVM을 배포할 수도 있습니다.

>[!NOTE]
>다음의 모든 명령은 Azure ML 프로젝트의 루트 폴더에서 발급된 것으로 가정합니다.

먼저 `docs` 폴더에 원하는 텍스트 편집기를 사용하여 `mydsvm.json` 파일을 만듭니다. (프로젝트 루트 폴더에 `docs` 폴더가 없으면 폴더를 만드십시오.) 이 파일을 사용하여 Azure 리소스 관리 템플릿에 대한 몇 가지 기본 매개 변수를 구성합니다. 

다음 JSON 코드 조각을 복사하여 `mydsvm.json` 파일에 붙여넣고 적절한 값을 채웁니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

_vmSize_ 필드의 경우 [Ubuntu DSVM Azure 리소스 관리 템플릿](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json)에 나열된 지원되는 VM 크기를 사용할 수 있습니다. 아래 크기 중 하나를 Azure ML의 계산 대상으로 사용하는 것이 좋습니다. 


>[!TIP]
> [딥 러닝 워크로드](how-to-use-gpu.md)의 경우 GPU 기반 VM을 배포할 수 있습니다.

- [범용 VM](/virtual-machines/linux/sizes-general.md)
  - Standard_DS2_v2 
  - Standard_DS3_v2 
  - Standard_DS4_v2 
  - Standard_DS12_v2 
  - Standard_DS13_v2 
  - Standard_DS14_v2 
- [GPU 기반 VM](/virtual-machines/linux/sizes-gpu.md)
  - Standard_NC6 
  - Standard_NC12 
  - Standard_NC24 
 

[Azure에서 Linux 가상 머신의 크기](../../virtual-machines/linux/sizes.md) 및 [가격 책정 정보](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에 대해 자세히 알아보세요.

**파일** --> **명령 프롬프트 열기** 또는 **PowerShell 열기** 메뉴 항목을 클릭하여 Azure ML Workbench 앱에서 CLI 창을 시작합니다. 

>[!NOTE]
>az-cli가 설치된 모든 명령줄 환경에서 이 작업을 수행할 수 있습니다.

명령줄 창에 다음 명령을 입력합니다.

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>DSVM 계산 대상 연결
DSVM이 만들어지고 나면 Azure ML 프로젝트에 연결할 수 있습니다.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
이제 이 DSVM에서 실험을 실행할 준비가 되었습니다.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>DSVM 할당을 취소하고 나중에 다시 시작
Azure ML의 계산 작업을 마치면 DSVM 할당을 취소할 수 있습니다. 이 작업은 VM을 종료하고 계산 리소스를 해제하지만 가상 디스크를 보존합니다. VM 할당이 취소되면 계산 비용이 청구되지 않습니다.

VM 할당을 취소하려면:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

VM을 다시 작동하려면 `az ml start` 명령을 사용합니다.

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>DSVM OS 디스크 확장
Ubuntu DSVM은 50GB OS 디스크 및 100GB 데이터 디스크가 함께 제공됩니다. Docker는 더 많은 공간을 사용할 수 있는 데이터 디스크에 이미지를 저장합니다. Azure ML의 계산 대상으로 사용할 경우 이 디스크는 Docker 엔진이 Docker 이미지를 끌어 당기고 그 위에 conda 레이어를 빌드하는 데 사용될 수 있습니다. 실행 중에 "디스크 꽉 참"오류를 피하려면 디스크를 더 큰 크기(예: 200GB)로 확장해야 할 수도 있습니다. azure-cli에서 이것을 쉽게 수행하는 방법을 알아보려면 [Azure CLI를 사용하여 Linux VM에서 가상 하드 디스크를 확장하는 방법](../../virtual-machines/linux/expand-disks.md)을 참조하세요. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Azure Portal에 Azure HDInsight용 Apache Spark 클러스터 생성

스케일 아웃 Spark 작업을 실행하려면 Azure Portal에서 Azure HDInsight용 Apache Spark 클러스터를 만들어야 합니다.

1. https://portal.azure.com에서 Azure Portal에 로그온
2. **+새로 만들기** 링크를 클릭하고 "HDInsight"를 검색합니다.

    ![hdi 찾기](media/how-to-create-dsvm-hdi/hdi.png)
    
3. 목록에서 **HDInsight**를 선택한 다음 **만들기** 단추를 클릭합니다.
4. **기본** 구성 화면의 **클러스터 유형** 설정에서 _클러스터 유형_에 **Spark**를 선택하고 _운영 체제_에 **Linux**를 선택하고 버전으로 **Spark 2.1.0(HDI 3.6)** 을 선택합니다.

    ![hdi 구성](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >위의 화면에서 클러스터에는 _클러스터 로그인 사용자 이름_ 필드와 _SSH(보안 셸) 사용자 이름_ 필드가 있습니다. 이들 항목은 편의상 두 로그인에 대해 동일한 암호를 지정할 수 있지만 두 가지 다른 사용자 ID입니다. _클러스터 로그인 사용자 이름_은 HDI 클러스터의 관리 웹 UI에 로그인하는 데 사용됩니다. _SSH 로그인 사용자 이름_은 클러스터의 헤드 노드에 로그인하는 데 사용되며 Azure ML이 Spark 작업을 발송하는 데 필요합니다.

5. 필요한 클러스터 크기 및 노드 크기를 선택하고 만들기 마법사를 완료합니다. 클러스터가 프로비저닝을 완료하는 데 최대 30분 정도 걸릴 수 있습니다. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>HDI Spark 클러스터 계산 대상 연결

Spark HDI 클러스터가 만들어지고 나면 Azure ML 프로젝트에 연결할 수 있습니다.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
이제 이 Spark 클러스터에서 실험을 실행할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.
- [Azure Machine Learning 실험 서비스 개요](experimentation-service-configuration.md)
- [Azure Machine Learning Workbench 실험 서비스 구성 파일](experimentation-service-configuration-reference.md)
- [Azure HDInsight용 Apache Spark 클러스터](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [데이터 과학 Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
