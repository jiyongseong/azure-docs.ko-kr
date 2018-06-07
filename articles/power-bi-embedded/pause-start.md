---
title: Azure Portal에서 Power BI Embedded 용량을 일시 중지 및 시작 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure에서 Power BI Embedded 용량을 일시 중지하고 시작하는 방법을 안내합니다.
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
ms.openlocfilehash: f96c62ea1d4fea6a724cd50cce9b54c02ddebdf5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>Azure Portal에서 Power BI Embedded 용량을 일시 중지 및 시작

이 문서에서는 Microsoft Azure에서 Power BI Embedded 용량을 일시 중지하고 시작하는 방법을 안내합니다. 여기서는 Power BI Embedded 용량을 만들었다고 가정합니다. [Azure Portal에서 Power BI Embedded 용량 만들기](create-capacity.md)를 아직 참조하지 않은 경우 먼저 참조한 후 시작하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="pause-your-capacity"></a>용량 일시 중지

용량을 일시 중지하면 비용이 청구되지 않습니다. 일정 기간 동안 용량을 사용할 필요가 없다면 용량을 일시 중지하는 것이 좋습니다. 용량을 일시 중지하려면 다음 단계를 사용하십시오.

> [!NOTE]
> 용량을 일시 중지하면 Power BI에서 콘텐츠를 사용하지 못할 수 있습니다. 중단을 방지하려면 일시 중지하기 전에 용량에서 작업 영역을 할당 해제해야 합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. **모든 서비스** > **Power BI Embedded**를 선택하여 용량을 확인합니다.

    ![Azure Portal 내에 있는 모든 서비스](media/pause-start/azure-portal-more-services.png)

3. 일시 중지할 용량을 선택합니다.

    ![Azure Portal 내의 Power BI Embedded 용량 목록](media/pause-start/azure-portal-capacity-list.png)

4. 용량 세부 정보에서 **일시 중지**를 선택합니다.

    ![용량 일시 중지](media/pause-start/azure-portal-pause-capacity.png)

5. **예**를 선택하면 용량을 일시 중지 하는 것이 확인됩니다.

    ![일시 중지 확인](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>용량 시작

용량을 시작하여 사용을 다시 시작합니다. 용량을 시작하면 청구도 다시 시작됩니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. **모든 서비스** > **Power BI Embedded**를 선택하여 용량을 확인합니다.

    ![Azure Portal 내에 있는 모든 서비스](media/pause-start/azure-portal-more-services.png)

3. 시작할 용량을 선택합니다.

    ![Azure Portal 내의 Power BI Embedded 용량 목록](media/pause-start/azure-portal-capacity-list.png)

4. 용량 세부 정보에서 **시작**을 선택합니다.

    ![용량 시작](media/pause-start/azure-portal-start-capacity.png)

5. **예**를 선택하면 용량을 시작하는 것이 확인됩니다.

    ![시작 확인](media/pause-start/azure-portal-confirm-start.png)

이 용량에 할당된 콘텐츠가 있으면 시작한 후에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

용량을 늘리거나 줄이려면 [Power BI Embedded 용량 크기 조정](scale-capacity.md)을 참조하세요.

응용 프로그램 내에 Power BI 콘텐츠를 포함하려면 [Power BI 대시보드, 보고서 및 타일을 포함하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)을 참조하세요.

궁금한 점이 더 있나요? [Power BI 커뮤니티에 질문하세요.](http://community.powerbi.com/)