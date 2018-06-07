---
title: "XML 유효성 검사용 스키마 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps 및 엔터프라이즈 통합 팩용 스키마로 XML 문서 유효성 검사"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: db2d9313e443ebc9dd32fcb905b0ae62219e4bbf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Azure Logic Apps 및 엔터프라이즈 통합 팩용 스키마로 XML 유효성 검사

스키마는 수신한 XML 문서가 유효한지와 예상되는 데이터가 미리 정의된 형식으로 문서에 포함되는지를 확인합니다. 스키마는 B2B 시나리오에서 교환되는 메시지의 유효성을 검사하는 데 도움이 됩니다.

## <a name="add-a-schema"></a>스키마 추가

1. Azure Portal에서 **모든 서비스**를 선택합니다.

    ![Azure Portal, "모든 서비스"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다.

    ![필터 검색 상자](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. 스키마를 추가할 **통합 계정**을 선택합니다.

    ![통합 계정 목록](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. **스키마** 타일을 선택합니다.

    ![예제 통합 계정, "스키마"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>2MB보다 작은 스키마 파일 추가

1. (이전 단계에서) 열린 **스키마** 블레이드에서 **추가**를 선택합니다.

    ![스키마 블레이드, "추가"](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. 스키마의 이름을 입력합니다. **스키마** 텍스트 상자 옆의 폴더 아이콘을 선택하여 스키마 파일을 업로드합니다. 업로드 프로세스가 완료되면 **확인**을 선택합니다.

    !["작은 파일"이 강조 표시된 "스키마 추가" 스크린샷](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>2MB(최대 8MB)보다 큰 스키마 파일 추가

이러한 단계는 Blob 컨테이너 액세스 수준인 **공용** 또는 **익명 액세스 없음**에 따라 달라집니다.

**이 액세스 수준을 확인하려면**

1.  **Azure Storage 탐색기**를 엽니다. 

2.  **Blob 컨테이너** 아래에서 원하는 blob 컨테이너를 선택합니다. 

3.  **보안**, **액세스 수준**을 선택합니다.

Blob 보안 액세스 수준이 **공용**인 경우 다음 단계를 수행합니다.

!["Blob 컨테이너", "보안" 및 "공용"이 강조 표시된 Azure Storage 탐색기](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. 스키마를 계정 저장소에 업로드하고 URI를 복사합니다.

    ![URI가 강조 표시된 Storage 계정](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. **스키마 추가**에서 **큰 파일**을 선택하고 **콘텐츠 URI** 텍스트 상자에 URI를 제공합니다.

    !["추가" 단추 및 " 큰 파일"이 강조 표시된 스키마](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Blob 보안 액세스 수준이 **익명 액세스 없음**인 경우 다음 단계를 수행합니다.

!["Blob 컨테이너", "보안" 및 "익명 액세스 없음"이 강조 표시된 Azure Storage 탐색기](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. 저장소 계정에 스키마를 업로드합니다.

    ![Storage 계정](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. 스키마에 대한 공유 액세스 서명을 생성합니다.

    ![공유 액세스 서명 탭이 강조 표시된 Storage 계정](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. **스키마 추가**에서 **큰 파일**을 선택하고 **콘텐츠 URI** 텍스트 상자에 공유 액세스 서명 URI를 제공합니다.

    !["추가" 단추 및 " 큰 파일"이 강조 표시된 스키마](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. 통합 계정의 **스키마** 블레이드에서 새로 추가된 스키마가 표시됩니다.

    !["스키마" 및 새 스키마가 강조 표시된 EIP 통합 계정](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>스키마 편집

1. **스키마** 타일을 선택합니다.

2. 열린 **스키마** 블레이드에서 편집하려는 스키마를 선택합니다.

3. **스키마** 블레이드에서 **편집**을 선택합니다.

    ![스키마 블레이드](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. 편집하려는 스키마 파일을 선택하고 **열기**를 선택합니다.

    ![편집할 스키마 파일 열기](media/logic-apps-enterprise-integration-schemas/edit-31.png)

스키마가 성공적으로 업로드했다는 메시지가 표시됩니다.

## <a name="delete-schemas"></a>스키마 삭제

1. **스키마** 타일을 선택합니다.

2. 열린 **스키마** 블레이드에서 삭제하려는 스키마를 선택합니다.

3. **스키마** 블레이드에서 **삭제**를 선택합니다.

    ![스키마 블레이드](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. 선택한 스키마를 삭제할지 확인하려면 **예**를 선택합니다.

    !["스키마 삭제" 확인 메시지](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    **스키마** 블레이드에서 스키마 목록이 새로 고쳐지고 삭제된 스키마가 더 이상 포함되지 않습니다.

    !["스키마"가 강조 표시된 통합 계정](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>다음 단계
* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")  

