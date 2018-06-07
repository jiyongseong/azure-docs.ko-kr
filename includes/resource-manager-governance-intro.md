---
title: "포함 파일"
description: "포함 파일"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2c16e82ccf259a4cc5ae8fcf35b2dd6b5d50ee2d
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
리소스를 Azure에 배포할 때 배포할 리소스의 형식, 위치 및 설정 방법을 결정하는 데 놀라운 유연성을 경험할 수 있습니다. 단, 그러한 유연성으로 인해 조직에서 허용하려는 것보다 더 많은 선택 사항이 있을 수도 있습니다. 리소스를 Azure에 배포할 때 다음에 대해 궁금하실 수도 있습니다.

* 특정 국가에서 데이터 독립성에 대한 법률 요구 사항을 충족하려면 어떻게 해야 하나요?
* 비용은 어떻게 관리할 수 있나요?
* 누군가가 중요한 시스템을 실수로 변경하지 않도록 보장하려면 어떻게 해야 하나요?
* 리소스 비용을 추적하고 정확하게 청구하려면 어떻게 해야 하나요?

이 문서에서는 그러한 질문을 해결합니다. 특히,

> [!div class="checklist"]
* 사용자가 추가 동작이 아닌 예상된 동작을 수행할 수 있는 사용 권한을 갖도록 사용자를 역할에 할당하고 역할을 범위에 할당합니다.
* 구독에서 리소스에 대한 규칙을 규정하는 정책을 적용합니다.
* 시스템에 중요한 리소스를 잠급니다.
* 조직에 의미 있는 값으로 추적할 수 있도록 리소스에 태그를 지정합니다.

이 문서에서는 거버넌스를 구현하기 위해 수행하는 작업에 중점을 둡니다. 개념보다 더 광범위한 논의는 [Azure에서 거버넌스](../articles/security/governance-in-azure.md)를 참조하세요. 
