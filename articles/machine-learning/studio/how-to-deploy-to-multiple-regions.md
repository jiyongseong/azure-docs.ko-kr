---
title: 여러 지역에 Web Service를 배포하는 방법 | Microsoft Docs
description: 새 웹 서비스를 다른 영역을 배포(복사)하는 단계입니다.
services: machine-learning
documentationcenter: ''
author: aashishb
manager: hjerez
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: aashishb
ms.openlocfilehash: 68ebd388b46199710cbe8f314b705e4733e2440e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>여러 지역에 웹 서비스를 배포하는 방법
새 Azure 웹 서비스를 사용하면 여러 구독 또는 작업 영역 없이 여러 지역에 웹 서비스를 쉽게 배포할 수 있습니다. 

가격 책정은 지역별로 이루어집니다. 따라서 웹 서비스를 배포할 각 지역에 대한 청구 계획을 정의해야 합니다.

## <a name="to-create-a-plan-in-another-region"></a>다른 지역에 계획을 만들려면
1. [Microsoft Azure Machine Learning 웹 서비스](https://services.azureml.net/)에 로그인합니다.
2. **계획** 메뉴 옵션을 클릭합니다.
3. 보기 페이지의 계획에서 **새로 만들기**를 클릭합니다.
4. **구독** 드롭다운에서 새 계획이 상주할 구독을 선택합니다.
5. **지역** 드롭다운에서 새 계획에 대한 지역을 선택합니다. 선택한 영역에 대한 계획 옵션은 페이지의 **계획 옵션** 섹션에 표시됩니다.
6. **리소스 그룹** 드롭다운에서 계획에 대한 리소스 그룹을 선택합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.
7. **계획 이름** 에 계획의 이름을 입력합니다.
8. **계획 옵션**에서 새 계획에 대한 청구 수준을 클릭합니다.
9. **만들기**를 클릭합니다.

## <a name="deploying-the-web-service-to-another-region"></a>다른 지역에 웹 서비스 배포
1. **웹 서비스** 메뉴 옵션을 클릭합니다.
2. 새 지역에 배포하는 웹 서비스를 선택합니다.
3. **복사**를 클릭합니다.
4. **웹 서비스 이름**에 웹 서비스의 새 이름을 입력합니다.
5. **웹 서비스 설명**에 웹 서비스에 대한 설명을 입력합니다.
6. **구독** 드롭다운에서 새 웹 서비스가 상주할 구독을 선택합니다.
7. **리소스 그룹** 드롭다운에서 웹 서비스에 대한 리소스 그룹을 선택합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.
8. **지역** 드롭다운에서 웹 서비스를 배포할 지역을 선택합니다.
9. **Storage 계정** 드롭다운에서 웹 서비스에 저장할 Storage 계정을 선택합니다.
10. **가격 계획** 드롭다운에서 8단계에서 선택한 영역에서 계획을 선택합니다.
11. **복사**를 클릭합니다.

