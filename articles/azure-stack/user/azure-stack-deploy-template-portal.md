---
title: Azure 스택 포털을 사용 하는 템플릿을 배포 | Microsoft Docs
description: Azure Stack 포털을 사용하여 템플릿을 배포하는 방법에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: eea4f568f21693764222c8fdbe3316bf6008cc05
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604236"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Azure Stack 포털을 사용하여 템플릿 배포

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 Azure 리소스 관리자 템플릿을 배포 하는 포털을 사용할 수 있습니다.

## <a name="to-deploy-a-template"></a>서식 파일을 배포 하려면

1. 선택 포털에 로그인 **새로**를 선택한 후 **사용자 지정**합니다.
2. 선택 **템플릿 배포**합니다.
3. 선택 **템플릿 편집**, 다음 JSON 템플릿 코드 코드 창에 붙여 넣습니다. **저장**을 선택합니다.
4. 선택 **매개 변수 편집**, 표시 되 고 다음을 선택 하는 매개 변수에 대 한 값을 제공 **확인**합니다.
5. 선택 **구독**합니다. 다음을 선택 하 고 사용할 하려는 구독 선택 **확인**합니다.
6. 선택 **리소스 그룹**합니다. 기존 리소스 그룹을 선택 하거나 새 대시보드를 만들고, 다음 선택 **확인**합니다.
7. **만들기**를 선택합니다. 대시보드의 새 타일은 템플릿 배포의 진행 상태를 추적합니다.

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 템플릿 배포](azure-stack-deploy-template-powershell.md)
