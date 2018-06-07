---
title: Azure Analysis Services로 Power BI Desktop 파일 가져오기 | Microsoft Docs
description: Azure Portal을 사용하여 Power BI Desktop 파일(pbix)을 가져오는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35bf2ba85017de43788f802b6244d61ed2bb62df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Power BI Desktop 파일 가져오기

Power BI Desktop 파일(pbix)을 가져와 Azure AS에서 새 모델을 만들 수 있습니다. 모델 메타데이터, 캐시된 데이터 및 데이터 원본 연결이 가져오기됩니다. 보고서 및 시각화는 가져오기되지 않습니다.

**제한 사항**   
- pbix 모델은 Azure SQL Database 및 Azure SQL Data Warehouse 데이터 원본에만 연결할 수 있습니다. 
- pbix 모델에는 라이브 연결이나 DirectQuery 연결이 불가능합니다. 
- pbix 데이터 모델이 Analysis Services에서 지원되지 않는 메타데이터를 포함하는 경우 가져오기에 실패할 수 있습니다.

## <a name="to-import-from-pbix"></a>pbix를 가져오려면

1. 서버의 **개요** > **웹 디자이너**에서 **열기**를 클릭합니다.

    ![Azure Portal에서 모델 만들기](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. **웹 디자이너** > **모델**에서 **+ 추가**를 클릭합니다.

    ![Azure Portal에서 모델 만들기](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. **새 모델**에서 모델 이름을 입력한 다음, Power BI Desktop 파일을 선택합니다.

    ![Azure Portal의 새 모델 대화 상자](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. **가져오기**에서 파일을 찾아 선택합니다.

     ![Azure Portal의 연결 대화 상자](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>참고 항목

[Azure Portal에서 모델 만들기](analysis-services-create-model-portal.md)   
[Azure Analysis Services에 연결](analysis-services-connect.md)  
