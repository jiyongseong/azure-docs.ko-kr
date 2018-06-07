---
title: XSLT를 사용하여 XML 변환 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용하여 XML 데이터를 변환하기 위한 XSLT 맵 추가
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4b4d626028eed09e9ce6a45fa8fa69859c082da7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="add-maps-for-xml-data-transform"></a>XML 데이터 변환을 위한 맵 추가

엔터프라이즈 통합에서는 맵을 사용하여 XML 데이터를 다른 형식으로 변환합니다. 맵은 문서에서 어떤 데이터를 다른 형식으로 변환해야 하는지를 정의하는 XML 문서입니다. 

## <a name="why-use-maps"></a>맵을 사용하는 이유

날짜에 YYYMMDD 형식을 사용하는 고객에게서 정기적으로 B2B 주문 또는 송장을 받는 경우를 가정해 보겠습니다. 그러나 조직에서 날짜를 MMDDYYY 형식으로 저장합니다. 고객 작업 데이터베이스에서 주문 또는 송장 세부 정보를 저장하기 전에 맵을 사용하여 YYYMMDD 날짜 형식을 MMDDYYY로 *변환* 할 수 있습니다.


## <a name="how-do-i-create-a-map"></a>맵을 만드는 방법

Visual Studio 2015용 [엔터프라이즈 통합 팩](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")을 사용하여 Biztalk 통합 프로젝트를 만들 수 있습니다. 그런 후 두 개의 XML 스키마 파일 간에 항목을 시각적으로 매핑할 수 있는 통합 맵 파일을 만들 수 있습니다. 이 프로젝트를 빌드하면 XSLT 문서가 생성됩니다.

맵에 외부 어셈블리에 대한 참조가 있는 경우 둘 다 통합 계정에 업로드되어야 합니다. 먼저 어셈블리, 그런 다음, 어셈블리를 참조하는 맵의 순서로 업로드되어야 합니다.


## <a name="how-do-i-add-a-map"></a>맵을 추가하는 방법

1. Azure Portal에서 **찾아보기**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. 맵을 추가할 통합 계정을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. **맵** 타일을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. 맵 페이지가 열리면 **추가**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. 맵의 **이름**을 입력합니다. 맵 파일을 업로드하려면 **맵** 텍스트 상자 오른쪽에 있는 폴더 아이콘을 선택합니다. 업로드 프로세스가 완료되면 **확인**을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Azure에서 통합 계정에 맵을 추가하면 맵 파일이 추가되었는지 여부를 보여 주는 화면 메시지가 표시됩니다. 이 메시지가 표시되면 새로 추가된 맵을 볼 수 있도록 **맵** 타일을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>어셈블리를 추가하려면 어떻게 할까요?
어셈블리를 업로드하려는 통합 계정을 엽니다.

1. **어셈블리** 타일을 선택합니다.

    ![integrationaccount-assembly-tile](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. 어셈블리 페이지가 열리면 **추가**를 선택합니다. 어셈블리의 **이름**을 입력합니다. 어셈블리 파일을 업로드하려면 **어셈블리** 텍스트 상자의 오른쪽에 있는 폴더 아이콘을 선택합니다. 업로드 프로세스가 완료되면 **확인**을 선택합니다.

    ![add-assembly](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>맵을 편집하는 방법

원하는 변경 내용을 포함한 새 맵 파일을 업로드해야 합니다. 먼저 편집을 위해 맵을 다운로드할 수 있습니다.

기존 맵을 대체하는 새 맵을 업로드하려면 다음 단계를 수행합니다.

1. **맵** 타일을 선택합니다.

2. 맵 페이지가 열리면 편집하려는 맵을 선택합니다.

3. **맵** 페이지에서 **업데이트**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. 파일 선택에서 업로드하려는 맵 파일을 선택하고 **열기**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>맵 삭제 방법

1. **맵** 타일을 선택합니다.

2. 맵 페이지가 열리면 삭제하려는 맵을 선택합니다.

3. **삭제**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. 맵을 삭제할지 확인합니다.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>다음 단계
* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")  
* [규약에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")  
* [변환에 대해 자세히 알아보기](logic-apps-enterprise-integration-transform.md "엔터프라이즈 통합 변환에 대해 알아보기")  

