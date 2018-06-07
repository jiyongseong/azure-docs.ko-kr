---
title: Power BI Embedded 용량 크기 조정 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure에서 Power BI Embedded 용량 크기를 조정하는 방법을 안내합니다.
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
ms.date: 01/19/2018
ms.author: maghan
ms.openlocfilehash: a089d34b3f49188f3037ad57aab4d7bcc8c660f2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="scale-your-power-bi-embedded-capacity"></a>Power BI Embedded 용량 크기 조정

이 문서에서는 Microsoft Azure에서 Power BI Embedded 용량 크기를 조정하는 방법을 안내합니다. 크기 조정을 통해 용량을 늘리거나 줄일 수 있습니다.

여기서는 Power BI Embedded 용량을 늘렸다고 가정합니다. [Azure Portal에서 Power BI Embedded 용량 만들기](create-capacity.md)를 아직 참조하지 않은 경우 먼저 참조한 후 시작하세요.

> [!NOTE]
> 크기 조정 작업에 약 1분이 걸릴 수 있으며, 이 시간 동안에는 용량을 사용할 수 없습니다. 따라서 Embedded 콘텐츠가 로드되지 않을 수 있습니다.

## <a name="scale-a-capacity"></a>용량 크기 조정

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. **모든 서비스** > **Power BI Embedded**를 선택하여 용량을 확인합니다.

    ![Azure Portal 내에 있는 모든 서비스](media/scale-capacity/azure-portal-more-services.png)

3. 크기를 조정할 용량을 선택합니다.

    ![Azure Portal 내의 Power BI Embedded 용량 목록](media/scale-capacity/azure-portal-capacity-list.png)

4. 용량 내의 **규모**에서 **가격 책정 계층**을 선택합니다.

    ![규모의 가격 책정 계층 옵션](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    현재 가격 책정 계층은 파란색으로 윤곽이 표시되어 있습니다.

    ![파란색으로 윤곽이 표시된 현재 가격 책정 계층](media/scale-capacity/azure-portal-current-tier.png)

5. 확장 또는 축소하려면 이동할 새 계층을 선택합니다. 새 계층을 선택하면 선택 영역 주위에 파란색 점선이 그려집니다. 새 계층으로 규모를 조정하려면 **선택**을 선택합니다.

    ![새 계층 선택](media/scale-capacity/azure-portal-select-new-tier.png)

    용량 크기 조정을 완료하는 데 1~2분 정도 걸릴 수 있습니다.

6. 개요 탭에서 사용자 계층을 확인합니다. 현재 가격 책정 계층이 표시됩니다.

    ![현재 계층 확인](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>다음 단계

용량을 일시 중지하거나 시작하려면 [Azure Portal에서 Power BI Embedded 용량 일시 중지 및 시작](pause-start.md)을 참조하세요.

응용 프로그램 내에 Power BI 콘텐츠를 포함하려면 [Power BI 대시보드, 보고서 및 타일을 포함하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)을 참조하세요.

궁금한 점이 더 있나요? [Power BI 커뮤니티에 질문하세요.](http://community.powerbi.com/)
