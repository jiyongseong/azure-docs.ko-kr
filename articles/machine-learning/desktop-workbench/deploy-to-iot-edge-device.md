---
title: Azure Machine Learning 모델을 Azure IoT Edge 장치에 배포 | Microsoft Docs
description: 이 문서에서는 Azure IoT Edge 장치에 Azure Machine Learning 모델을 배포하는 방법에 대해 설명합니다.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 2/1/2018
ms.openlocfilehash: ceab96b1ef28527c8aa2692b83d3609f133f339c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Azure Machine Learning 모델을 Azure IoT Edge 장치에 배포

Docker 기반 웹 서비스로 컨테이너화된 모든 Azure Machine Learning 모델을 Azure IoT Edge 장치에서 실행할 수도 있습니다. 추가 스크립트 및 지침은 [Azure IoT Edge용 AI 도구](http://aka.ms/AI-toolkit)에서 찾을 수 있습니다.

## <a name="operationalize-the-model"></a>모델 운영
[Azure Machine Learning 모델 관리 웹 서비스 배포](model-management-service-deploy.md)의 지침에 따라 모델을 운영화하여 모델이 포함된 Docker 이미지를 만듭니다.

## <a name="deploy-to-azure-iot-edge"></a>Azure IoT Edge에 배포
Azure IoT Edge는 클라우드 분석 및 사용자 지정 비즈니스 논리를 장치로 이동합니다. 모든 Machine Learning 모델이 IoT Edge 장치에서 실행될 수 있습니다. IoT Edge 장치를 설정하고 배포를 만들기 위한 설명서는 [ataka.ms/azure-iot-edge-doc](https://aka.ms/azure-iot-edge-doc)에서 찾을 수 있습니다.

다음은 주의할 추가적인 사항입니다.

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>Edge 장치의 Edge 런타임에 레지스트리 자격 증명 추가
IoT Edge를 실행 중인 컴퓨터에서 런타임이 컨테이너를 풀하기 위한 액세스 권한을 가질 수 있도록 레지스트리의 자격 증명을 추가합니다.

Windows의 경우 다음 명령을 실행합니다.
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
Linux의 경우 다음 명령을 실행합니다.
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>Machine Learning 컨테이너 이미지 위치 찾기
Machine Learning 컨테이너 이미지의 위치가 필요합니다. 컨테이너 이미지 위치를 찾으려면:

1. [Azure Portal](http://portal.azure.com/)에 로그인합니다.
2. **Azure Container Registry**에서 검사할 레지스트리를 선택합니다.
3. 레지스트리에서 **리포지토리**를 클릭하면 모든 리포지토리와 해당 이미지 목록이 표시됩니다.













