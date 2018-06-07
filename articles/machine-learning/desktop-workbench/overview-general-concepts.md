---
title: Azure Machine Learning 미리 보기 기능에 대한 개념적 개요 | Microsoft Docs
description: 구독, 계정, 작업 영역, 프로젝트 등과 같은 Azure Machine Learning 미리 보기 기능에 대한 개념적 개요입니다.
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: ea9da6f23fd08c09f9e805519487648480816f35
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-machine-learning---concepts"></a>Azure Machine Learning - 개념

이 문서에서는 Azure Machine Learning을 사용하기 위해 알고 있어야 할 개념을 정의하고 설명합니다. 

![개념의 계층 구조](media/overview-general-concepts/hierarchy.png)

- **구독:** Azure 구독은 Azure의 리소스에 대한 액세스 권한을 부여합니다. Azure Machine Learning은 계산, 저장소 및 기타 많은 Azure 리소스 및 서비스와 밀접하게 통합되므로 Workbench에서는 각 사용자가 유효한 Azure 구독에 액세스할 수 있어야 합니다. 또한 사용자는 해당 구독 내에서 리소스를 만드는 데 충분한 권한을 가지고 있어야 합니다.


- **실험 계정:** Azure ML 및 결제 수단에 필요한 Azure 리소스입니다. 여기에는 작업 영역이 포함되며, 작업 영역에는 프로젝트가 포함됩니다. _사용자_라고 하는 여러 사용자를 실험 계정에 추가할 수 있습니다. Azure ML Workbench를 사용하여 실험을 실행하려면 실험 계정에 대한 액세스 권한 이 있어야 합니다. 


- **모델 관리 계정** Azure ML에서 모델을 관리하는 데 필요한 Azure 리소스이기도 합니다. 이를 사용하여 모델 및 매니페스트를 등록하고, 컨테이너화된 웹 서비스를 빌드하고, 로컬 또는 클라우드에 배포할 수 있습니다. Azure ML의 다른 결제 수단이기도 합니다.


- **작업 영역:** Azure ML에서 공유 및 공동 작업을 위한 기본 구성 요소입니다. 프로젝트는 작업 영역 내에서 그룹화됩니다. 그런 다음 작업 영역은 실험 계정에 추가된 여러 사용자와 공유할 수 있습니다.


- **프로젝트:** Azure Machine Learning에서 문제를 해결하기 위해 수행되는 모든 작업에 대한 논리적 컨테이너입니다. 로컬 디스크의 단일 파일 폴더에 매핑되며, 파일이나 하위 폴더가 추가될 수 있습니다. 프로젝트는 필요에 따라 소스 제어 및 공동 작업을 위한 Git 리포지토리와 연결될 수 있습니다.  

- **실험:** Azure ML의 단일 진입점에서 실행할 수 있는 하나 이상의 소스 코드 파일입니다. 데이터 수집, 기능 엔지니어링, 모델 학습 또는 모델 평가와 같은 작업을 포함할 수 있습니다. Azure ML은 현재 Python 또는 PySpark 실험만 지원합니다.


- **모델:** Azure Machine Learning에서 기계 학습 실험의 결과를 나타냅니다. 데이터에 올바르게 적용되면 예측된 값을 생성하는 방법입니다. 모델은 테스트 환경 또는 프로덕션 환경에 배포할 수 있으며, 새 데이터에 대한 점수를 매기는 데 사용됩니다. 프로덕션 환경에서 모델은 성능 및 데이터 드리프트를 모니터링하고 필요에 따라 다시 학습될 수 있습니다. 

- **계산 대상:** 실험을 실행하기 위해 구성하는 계산 리소스입니다. 로컬 컴퓨터(Windows 또는 macOS)이거나 로컬 컴퓨터, Azure의 Linux VM 또는 HDInsight Spark 클러스터에서 실행되는 Docker 컨테이너일 수 있습니다.


- **실행:** 실험 서비스는 실행을 계산 대상의 실험 실행 수명으로 정의합니다. Azure ML은 각 실행에 대한 정보를 자동으로 캡처하고, 실행 기록의 형태로 특정 실험의 전체 기록을 제공합니다.

- **환경:** Azure Machine Learning에서 모델을 배포하고 관리하는 데 사용되는 특정 컴퓨팅 리소스를 나타냅니다. 컨텍스트 및 구성에 따라 로컬 컴퓨터, Azure의 Linux VM 또는 Azure Container Service에서 실행되는 Kubernetes 클러스터일 수 있습니다. 모델은 이러한 환경에서 실행되는 Docker 컨테이너에 호스팅되며 REST API 끝점으로 공개됩니다.


- **관리되는 모델:** 모델 관리를 사용하면 모델을 웹 서비스로 배포하고, 다양한 버전의 모델을 관리하며, 해당 성능 및 메트릭을 모니터링할 수 있습니다. 관리되는 모델은 Azure Machine Learning 모델 관리 계정에 등록됩니다.

- **매니페스트:** 모델 관리 시스템에서 모델을 프로덕션 환경에 배포할 때 모델, 종속성, 점수 매기기 스크립트, 샘플 데이터 및 스키마를 포함할 수 있는 매니페스트가 포함됩니다. 매니페스트는 Docker 컨테이너 이미지를 만드는 데 사용되는 수단입니다. 모델 관리를 사용하면 매니페스트를 자동으로 생성하고, 다른 버전을 만들고, 이러한 매니페스트를 관리할 수 있습니다. 


- **이미지:** 매니페스트를 사용하여 Docker 이미지를 생성 및 다시 생성할 수 있습니다. 컨테이너화된 Docker 이미지는 클라우드, 로컬 컴퓨터 또는 IoT 장치에서 실행할 수 있는 유연성을 제공합니다. 이미지는 자체적으로 포함되며, 모델을 사용하여 새 데이터의 점수를 매기는 데 필요한 모든 종속성을 포함합니다. 

- **서비스:** 모델 관리를 사용하면 모델을 웹 서비스로 배포할 수 있습니다. 웹 서비스 논리와 종속성은 이미지로 캡슐화됩니다. 각 웹 서비스는 지정된 URL에 대한 요청을 처리할 준비가 된 이미지를 기반으로 하는 컨테이너 집합입니다. 웹 서비스는 단일 배포로 간주됩니다.
