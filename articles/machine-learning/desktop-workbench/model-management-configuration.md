---
title: Azure Machine Learning 모델 관리 설치 및 구성 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning에서 모델 관리를 설치 및 구성할 때 수행되는 단계 및 개념을 설명합니다.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: 45ddd4dc6fb5559c020706e2784158b1319f9b52
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="model-management-setup"></a>모델 관리 설치

## <a name="overview"></a>개요
이 문서는 Azure ML 모델 관리를 사용하여 Machine Learning 모델을 웹 서비스로 배포 및 관리하는 방법을 설명합니다. 

Azure ML 모델 관리를 사용하면 SparkML, Keras, TensorFlow, Microsoft Cognitive Toolkit 또는 Python을 비롯한 다양한 프레임워크를 사용하여 빌드한 Machine Learning 모델을 효율적으로 배포 및 관리할 수 있습니다. 

이 문서를 마치면 모델 관리 환경을 설치하고 Machine Learning 모델을 배포할 준비를 갖추게 됩니다.

## <a name="what-you-need-to-get-started"></a>시작에 필요한 항목
이 가이드를 최대한 활용하려면 모델을 배포할 수 있는 Azure 구독에 대해 소유자 권한이 있어야 합니다.
CLI는 Azure Machine Learning Workbench 및 [Azure DSVM](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview)이 미리 설치되어 있습니다.

## <a name="using-the-cli"></a>CLI 사용
Workbench에서 CLI(명령줄 인터페이스)를 사용하려면 **파일** -] **명령줄 인터페이스 열기**를 클릭합니다. 

데이터 과학 Virtual Machine에서 명령 프롬프트를 연결하고 엽니다. `az ml -h`를 입력하여 옵션을 표시합니다. 명령에 대한 자세한 내용을 보려면 --help 플래그를 사용합니다.

다른 모든 시스템에는 CLI를 설치해야 합니다.

### <a name="installing-or-updating-on-windows"></a>Windows에서 설치(또는 업데이트)

https://www.python.org/에서 Python을 설치합니다. pip를 설치하도록 선택했는지 확인합니다.

관리자 권한으로 실행을 사용하여 명령 프롬프트를 열고 다음 명령을 실행합니다.

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>이전 버전이 있는 경우 먼저 다음 명령을 사용하여 제거합니다.
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Linux에서 설치(또는 업데이트)
명령줄에서 다음 명령을 실행하고 프롬프트를 따라 진행합니다.

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

설치가 완료되면 다음 명령을 실행합니다.

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>SSH 세션에서 로그아웃했다가 다시 로그인하여 변경 내용을 적용합니다.
>앞에 나온 명령을 사용하여 DSVM의 이전 버전 CLI를 업데이트할 수 있습니다.
>

## <a name="deploying-your-model"></a>모델 배포
CLI를 사용하여 모델을 웹 서비스로 배포합니다. 웹 서비스는 로컬로 또는 클러스터에 배포할 수 있습니다.

로컬 배포를 시작하고, 모델 및 코드가 작동하는지 확인한 후 프로덕션 규모로 사용할 수 있게 클러스터로 배포합니다.

시작하려면 배포 환경을 설치해야 합니다. 환경 설치는 일회성 작업입니다. 설치가 완료되면 후속 배포에서 해당 환경을 다시 사용할 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.

환경 설치를 완료한 경우:
- Azure에 로그인하라는 메시지가 표시됩니다. 로그인하려면 웹 브라우저를 사용하여 https://aka.ms/devicelogin 페이지를 열고 제공된 코드를 입력하여 인증하세요.
- 인증 프로세스 중에 인증하기 위한 계정을 입력하라는 메시지가 표시됩니다. 중요: 유효한 Azure 구독 및 계정에서 리소스를 만들 수 있는 충분한 권한이 있는 계정을 선택합니다. 로그인이 완료되면 구독 정보가 표시되며 선택한 계정으로 계속 작업할지 묻는 메시지가 표시됩니다.

### <a name="environment-setup"></a>환경 설정
설치 프로세스를 시작하려면 다음 명령을 입력하여 환경 공급자를 등록해야 합니다.

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>로컬 배포
로컬 컴퓨터에서 웹 서비스를 배포 및 테스트하려면 다음 명령을 사용하여 로컬 환경을 설치합니다.

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>로컬 웹 서비스 배포에서는 로컬 컴퓨터에 Docker를 설치해야 합니다. 
>

로컬 환경 설치 명령은 구독에 다음 리소스를 만듭니다.
- 리소스 그룹(제공되지 않은 경우)
- 저장소 계정
- ACR(Azure Container Registry)
- Application insights

설치가 성공적으로 완료된 후 다음 명령을 사용하여 사용할 환경을 설치합니다.

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>클러스터 배포
확장성이 뛰어난 프로덕션 시나리오에는 클러스터 배포를 사용합니다. 오케스트레이터로 Kubernetes를 사용하여 ACS 클러스터를 설치합니다. ACS 클러스터는 웹 서비스 호출의 더 큰 처리량을 처리하도록 확장될 수 있습니다.

웹 서비스를 프로덕션 환경에 배포하려면 먼저 다음 명령을 사용하여 환경을 설치합니다.

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

클러스터 환경 설치 명령은 구독에 다음 리소스를 만듭니다.
- 리소스 그룹(제공되지 않은 경우)
- 저장소 계정
- ACR(Azure Container Registry)
- ACS(Azure Container Service) 클러스터의 Kubernetes 배포
- Application insights

리소스 그룹, 저장소 계정 및 ACR은 빠르게 생성됩니다. ACS 배포에는 20분까지 걸릴 수 있습니다. 

설치가 완료된 후 이 배포에 사용할 환경을 설정해야 합니다. 다음 명령을 사용합니다.

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> 환경이 생성된 후에는 이후 배포에서 위의 set 명령을 사용하여 다시 사용하면 됩니다.
>

>[!NOTE] 
>HTTPS 끝점을 만들려면 az ml env setup에서 --cert-name 및 --cert-pem 옵션을 사용하여 클러스터를 만들 때 SSL 인증서를 지정합니다. 이렇게 하면 제공된 인증서를 사용하여 보호된 https에서 요청을 처리하도록 클러스터가 설정됩니다. 설정이 완료되면 클러스터의 FQDN을 가리키는 CNAME DNS 레코드를 만듭니다.

### <a name="create-an-account"></a>계정 만들기
계정은 모델 배포를 위해 반드시 필요합니다. 이 작업은 계정당 한 번만 수행하면 되며, 여러 배포에서 동일한 계정을 다시 사용할 수 있습니다.

새 계정을 만들려면 다음 명령을 사용합니다.

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

기존 계정을 사용하려면 다음 명령을 사용합니다.
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>모델 배포
이제 저장된 모델을 웹 서비스로 배포할 준비가 되었습니다. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

### <a name="next-steps"></a>다음 단계
갤러리의 많은 샘플 중 하나를 수행합니다.
