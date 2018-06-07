---
title: 샘플 데이터를 사용하여 Azure Stream Analytics 작업 테스트
description: Stream Analytics 작업에서 쿼리를 테스트하는 방법
keywords: 이 문서에서는 Azure Portal을 사용하여 Azure Stream Analytics 작업을 테스트하고, 입력을 샘플링하고, 샘플 데이터를 업로드하는 방법을 설명합니다.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 3dc9091934f3db8ededc13f74d2f302eccace4d6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>샘플 데이터로 Stream Analytics 쿼리 테스트

Azure Stream Analytics를 사용하면 작업을 시작하거나 중지하지 않고도 Azure Portal에서 샘플 데이터를 업로드하고 쿼리를 테스트할 수 있습니다.

## <a name="upload-sample-data-and-test-the-query"></a>샘플 데이터 업로드 및 쿼리 테스트

1. Azure 포털에 로그인합니다. 

2. 기존 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지의 **작업 토폴로지** 제목 아래에서 **쿼리**를 선택하여 쿼리 편집기 창을 엽니다. 

4. 샘플 입력 데이터로 쿼리를 테스트하려면 아무 입력을 마우스 오른쪽 단추로 클릭합니다.  **파일에서 샘플 데이터 업로드**를 선택합니다.

   데이터가 JSON 형식이어야 합니다. 데이터가 CSV 등의 다른 형식인 경우 업로드하기 전에 JSON으로 변환해야 합니다. [CSV-JSON 변환기](http://www.convertcsv.com/csv-to-json.htm)와 같은 오픈 소스 변환 도구를 사용하여 데이터를 JSON으로 변환할 수 있습니다.

    ![Stream Analytics 쿼리 편집기 쿼리 테스트](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. 업로드가 완료되면 **테스트**를 선택하여 입력한 샘플 데이터에 대해 이 쿼리를 테스트합니다.

    ![Stream Analytics 쿼리 편집기 샘플 데이터 테스트](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. 나중에 필요할 때 테스트 출력을 사용할 수 있도록 사용자 쿼리의 출력이 다운로드 결과에 대한 링크와 함께 브라우저에 표시됩니다. 

7. 반복해서 쿼리를 수정하고 테스트하여 출력이 어떻게 변하는지 확인합니다.

   ![Stream Analytics 쿼리 편집기 샘플 출력](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   쿼리에 여러 출력을 사용하면 각 결과가 별도의 탭에 표시되며, 간편하게 탭을 전환할 수 있습니다.

8. 브라우저에 표시된 결과를 확인했으면 쿼리를 **저장**합니다. 그런 다음, 작업을 **시작**하여 들어오는 이벤트를 처리합니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
