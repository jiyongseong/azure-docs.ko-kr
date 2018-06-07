---
title: Azure Machine Learning 및 FPGA
description: FPGA로 모델 및 심층 신경망을 가속화하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>FPGA(Field-programmable Gate Array)란?

FPGA(Field-programmable Gate Array)는 필요에 따라 다시 구성할 수 있는 집적 회로입니다. 새로운 논리를 구현하기 위해 필요에 따라 FPGA를 변경할 수 있습니다. Azure Machine Learning 하드웨어 가속 모델을 사용하면 Azure 클라우드에서 학습된 모델을 FPGA에 배포할 수 있습니다.

이 기능은 DNN(심층 신경망)을 FPGA 프로그램으로 변환하는 과정을 처리하는 Project Brainwave에 의해 제공됩니다. 

## <a name="why-use-an-fpga"></a>FPGA를 사용하는 이유

FPGA는 대기 시간이 엄청나게 짧으며 일괄 처리 크기 1에서 데이터를 채점하는 데 특히 효과적입니다(큰 일괄 처리 크기에 대한 요구 사항은 없음).  비용 효율적이며 Azure에서 사용할 수 있습니다.  Project Brainwave는 미리 학습된 DNN을 FPGA 전반에서 병렬 처리하여 서비스 규모를 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[모델을 FPGA에 웹 서비스로 배포](how-to-deploy-fpga-web-service.md)

[FPGA SDK 설치 방법 알아보기](reference-fpga-package-overview.md)