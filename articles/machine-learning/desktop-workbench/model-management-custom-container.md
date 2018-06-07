---
title: Azure ML 모델을 배포하는 데 사용되는 컨테이너 이미지 사용자 지정 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 모델에 대한 컨테이너 이미지를 사용자 지정하는 방법을 설명합니다.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: mldocs, raymondl
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: 8ff2bca405bbd8faeaa4527f493804950fced6ce
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Azure ML 모델에 사용되는 컨테이너 이미지 사용자 지정

이 문서에서는 Azure Machine Learning 모델에 대한 컨테이너 이미지를 사용자 지정하는 방법을 설명합니다.  Azure ML Workbench는 Machine Learning 모델을 배포하는 데 컨테이너를 사용합니다. 모델은 종속성과 함께 배포되며 Azure ML은 모델, 종속성 및 관련 파일로부터 이미지를 만듭니다.

## <a name="how-to-customize-the-docker-image"></a>Docker 이미지를 사용자 지정하는 방법
Azure ML이 배포하는 Docker 이미지를 사용자 지정하는 경우 다음을 사용합니다.

1. `dependencies.yml` 파일: [PyPi]( https://pypi.python.org/pypi)에서 설치할 수 있는 종속성을 관리하기 위해 Workbench 프로젝트의 `conda_dependencies.yml` 파일을 사용하거나 직접 만들 수 있습니다. 이 방법은 pip 설치 가능 Python 종속성을 설치하는 데 권장되는 방법입니다.

   예제 CLI 명령:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   예제 conda_dependencies 파일: 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. Docker 단계 파일: 이 옵션을 사용하면 PyPi에서 설치할 수 없는 종속성을 설치하여 배포된 이미지를 사용자 지정할 수 있습니다. 

   파일에는 DockerFile과 같은 Docker 설치 단계가 포함되어야 합니다. 다음 명령을 파일에 사용할 수 있습니다. 

    RUN, ENV, ARG, LABEL, EXPOSE

   예제 CLI 명령:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   이미지, 매니페스트 및 서비스 명령은 docker-file 플래그를 허용합니다.

   예제 Docker 단계 파일:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Azure ML 컨테이너의 기본 이미지는 Ubuntu이며 변경할 수 없습니다. 다른 기본 이미지를 지정하면 무시됩니다.

## <a name="next-steps"></a>다음 단계
컨테이너 이미지를 사용자 지정했으므로, 대규모 사용을 위해 클러스터에 배치할 수 있습니다.  웹 서비스 배포를 위한 클러스터 설정에 대한 자세한 내용은 [모델 관리 구성](deployment-setup-configuration.md)을 참조하세요. 
