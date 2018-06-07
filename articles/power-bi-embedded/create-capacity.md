---
title: Azure Portal에서 Power BI Embedded 용량 만들기 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure에서 Power BI Embedded 용량을 만드는 방법을 안내합니다.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: 2fadfde13aee3aaf965c6ba30188544a8a075b9d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>Azure Portal에서 Power BI Embedded 용량 만들기

이 문서에서는 Microsoft Azure에서 Power BI Embedded 용량을 만드는 방법을 안내합니다. Power BI Embedded는 멋진 시각적 개체, 보고서 및 대시보드를 앱에 빠르게 추가할 수 있도록 하여 Power BI 기능을 간소화합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>시작하기 전에

이 빠른 시작을 완료하려면 다음이 필요합니다.

* **Azure 구독**: [Azure 평가판](https://azure.microsoft.com/free/)으로 이동하여 계정을 만들 수 있습니다.
* **Azure Active Directory**: 구독이 AAD(Azure Active Directory) 테넌트와 연결되어야 합니다. 또한 ***해당 테넌트의 계정으로 Azure에 로그인해야 합니다***. Microsoft 계정은 지원되지 않습니다. 자세한 내용은 [인증 및 사용자 권한](../analysis-services/analysis-services-manage-users.md)을 참조하세요.
* **Power BI 테넌트:** AAD 테넌트의 계정 하나 이상이 Power BI에 등록되어야 합니다.
* **리소스 그룹**: 기존 리소스 그룹을 사용하거나 [새 리소스 그룹을 만듭니다](../azure-resource-manager/resource-group-overview.md).

## <a name="create-a-capacity"></a>용량 만들기

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. **리소스 만들기** > **데이터 + 분석**을 선택합니다.

3. 검색 상자에서 *Power BI Embedded*를 검색합니다.

4. Power BI Embedded 내에서 **만들기**를 선택합니다.

5. 필수 정보를 입력한 다음 **만들기**를 선택합니다.

    ![새 용량을 만들기 위해 채울 필드](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |설정 |설명 |
    |---------|---------|
    |**리소스 이름**|용량을 식별하는 이름입니다. 리소스 이름은 Azure Portal 외에 Power BI 관리 포털 내에도 표시됩니다.|
    |**구독**|용량을 만들려는 구독입니다.|
    |**리소스 그룹**|이러한 새 기능을 포함하는 리소스 그룹입니다. 기존 리소스 그룹을 선택하거나 다른 리소스 그룹을 만듭니다. 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.|
    |**Power BI 용량 관리자**|Power BI 용량 관리자는 Power BI 관리 포털에서 용량을 보고, 다른 사용자에게 할당 권한을 부여할 수 있습니다. 기본적으로 용량 관리자는 사용자의 계정입니다. 용량 관리자는 Power BI 테넌트 내에 있어야 합니다.|
    |**위치**:|테넌트에 대해 Power BI가 호스트되는 위치입니다. 이 설정은 자동으로 확인되며 다른 위치를 선택할 수 없습니다.|
    |**가격 책정 계층**|요구 사항을 충족하는 SKU(V 코어 수 및 메모리 크기)를 선택합니다.  자세한 내용은 [Power BI Embedded 가격 책정](https://azure.microsoft.com/pricing/details/power-bi-embedded/)을 참조하세요.|

6. **만들기**를 선택합니다.

만드는 데 1분 이내, 대개 몇 초가 걸립니다. **대시보드에 고정**을 선택한 경우 대시보드로 이동하여 새 용량을 볼 수 있습니다. 또는 **모든 서비스** > **Power BI Embedded**로 이동하여 용량이 준비되었는지 확인합니다.

![Power BI Embedded 용량을 포함하는 Azure Portal 대시보드](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>다음 단계

새 Power BI Embedded 용량을 사용하려면 Power BI 관리 포털로 이동하여 작업 영역을 할당합니다. 자세한 내용은 [Power BI Premium 및 Power BI Embedded 내 용량 관리](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/)를 참조하세요.

이 용량을 사용할 필요가 없으면 일시 중지하여 청구되지 않도록 합니다. 자세한 내용은 [Azure Portal에서 Power BI Embedded 용량 일시 중지 및 시작](pause-start.md)을 참조하세요.

응용 프로그램 내에 Power BI 콘텐츠를 포함하려면 [Power BI 대시보드, 보고서 및 타일을 포함하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)을 참조하세요.

궁금한 점이 더 있나요? [Power BI 커뮤니티에 질문하세요.](http://community.powerbi.com/)