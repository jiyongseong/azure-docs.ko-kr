---
title: "Azure에서 Terraform 사용"
description: "Terraform을 사용하여 Azure 인프라를 프로비전하고 배포하는 방법을 소개합니다."
ms.service: virtual-machines-linux
keywords: "terraform, devops, 개요, 계획, 적용, 자동화"
author: binderjoe
ms.author: jbinder
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 667752d8830cdac5e2338fd3ed7904917123be94
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2017
---
# <a name="terraform-with-azure"></a>Azure의 Terraform

[Hashicorp Terraform](https://www.terraform.io/)은 클라우드 인프라를 프로비전하고 관리하기 위한 오픈 소스 도구입니다. 가상 컴퓨터, 저장소 계정 및 네트워킹 인터페이스와 같은 클라우드 리소스의 토폴로지를 설명하는 구성 파일의 인프라를 체계적으로 정리합니다. Terraform의 CLI(명령 줄 인터페이스)는 Azure 또는 기타 지원되는 클라우드에 구성 파일을 배포하고 프로비전하는 간단한 메커니즘을 제공합니다.

이 문서에서는 Terraform을 사용하여 Azure 인프라를 관리하는 이점에 대해 설명합니다.

## <a name="automate-infrastructure-management"></a>인프라 관리 자동화

Terraform의 템플릿 기반 구성 파일을 사용하면 반복 가능하고 예측 가능한 방식으로 Azure 리소스를 정의하고, 프로비전하고 구성할 수 있습니다. 인프라 자동화에는 다음과 같은 몇 가지 이점이 있습니다.

- 인프라를 배포하고 관리하는 동안 사람의 실수 가능성을 줄입니다.
- 동일한 템플릿을 여러 번 배포하여 동일한 개발, 테스트 및 프로덕션 환경을 만듭니다.
- 개발 및 테스트 환경을 요청 시 만들어서 비용을 줄입니다.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>인프라 변경 사항이 적용되기 전에 이해 

리소스 토폴로지가 복잡해지면서 인프라 변경의 의미와 영향을 이해하는 것이 어려울 수 있습니다.

Terraform은 사용자가 인프라 변경 사항이 배포되기 전에 유효성을 검사하고 미리 볼 수 있는 CLI(명령줄 인터페이스)를 제공합니다. 인프라 변경 사항을 안전하고 생산적인 방식으로 미리 보면 몇 가지 이점이 있습니다.
- 제안된 변경 사항과 그 영향을 팀 멤버가 신속하게 이해하기 때문에 보다 효과적으로 협업할 수 있습니다.
- 의도하지 않은 변경을 개발 프로세스 초기에 파악할 수 있습니다.


## <a name="deploy-infrastructure-to-multiple-clouds"></a>여러 클라우드에 인프라 배포

Terraform은 유사한 인프라가 Azure 및 추가 클라우드 공급자 또는 온-프레미스 데이터 센터에 배포되는 다중 클라우드 시나리오에서 널리 사용되는 도구입니다. 개발자는 동일한 도구와 구성 파일을 사용하여 여러 클라우드 공급자의 인프라를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Terraform에 대한 개요와 이점에 대해 간략히 살펴본 후 권장되는 다음 단계는 아래와 같습니다.

- [Terraform을 설치하고 Azure를 사용하도록 구성](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)하여 시작
- [Terraform을 사용하여 Azure 가상 컴퓨터 만들기](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- [Terraform용 Azure Resource Manager 모듈](https://www.terraform.io/docs/providers/azurerm/) 탐색 