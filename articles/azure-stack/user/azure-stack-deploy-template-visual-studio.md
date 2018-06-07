---
title: Visual Studio를 사용하여 Azure Stack에 템플릿 배포 | Microsoft Docs
description: Azure Stack에서 Visual Studio를 사용하여 템플릿을 배포하는 방법에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 6cd722fedc0483e37ce6ee491d74a7c985111353
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605126"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Visual Studio를 사용하여 Azure Stack의 템플릿 배포

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택에 Azure 리소스 관리자 템플릿을 배포 하려면 Visual Studio를 사용할 수 있습니다.

## <a name="to-deploy-a-template"></a>서식 파일을 배포 하려면

1. Visual Studio를 사용하여 Azure Stack에 [설치하고 연결합니다](azure-stack-install-visual-studio.md).
2. Visual Studio를 엽니다.
3. 선택 **파일**를 선택한 후 **새로**합니다. **새 프로젝트**선택, **Azure 리소스 그룹**합니다.
4. 입력 한 **이름** 는 새 프로젝트를 선택한 후에 대 한 **확인**합니다.
5. **Azure 템플릿 선택**, 선택 **Azure 스택 퀵 스타트** 드롭 다운 목록에서 합니다.
6. 선택 **101--저장소-계정 만들기**를 선택한 후 **확인**합니다.
7. 새 프로젝트에서 확장는 **템플릿** 노드에서 **솔루션 탐색기** 사용 가능한 템플릿을 볼 수 있습니다.
8. **솔루션 탐색기**, 프로젝트의 이름을 선택 하 고 다음 선택 **배포**합니다. 선택 **새 배포**합니다.
9. **리소스 그룹에 배포**를 사용 하 여는 **구독** Microsoft Azure 스택 구독을 선택 하려면 드롭다운 목록입니다.
10. **리소스 그룹** 목록, 기존 리소스 그룹을 선택 하거나 새로 만듭니다.
11. **리소스 그룹 위치** 목록에서 위치를 선택한 다음 선택 **배포**합니다.
12. **매개 변수 편집**, (있음 서식 파일에 따라 다름) 매개 변수에 대 한 값을 제공 합니다. 선택한 후 **저장**합니다.

## <a name="next-steps"></a>다음 단계

* [명령줄을 사용하여 템플릿 배포](azure-stack-deploy-template-command-line.md)
* [Azure Stack용 템플릿 개발](azure-stack-develop-templates.md)
