---
title: Azure Logic Apps에서 플랫 파일 인코딩 또는 디코딩 | Microsoft Docs
description: Logic Apps에서 엔터프라이즈 통합 팩에 포함된 플랫 파일 인코더 또는 디코더를 사용하는 방법
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: ''
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; divswa
ms.openlocfilehash: 410f2e184a92b879d4f75b31216da783e82dc953
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>플랫 파일을 사용한 엔터프라이즈 통합 개요

B2B(Business-to-Business) 시나리오에서 비즈니스 파트너에게 보내기 전에 XML 콘텐츠를 인코딩하려 할 수 있습니다. 논리 앱에서 플랫 파일 인코딩 커넥터를 사용하여 이 작업을 수행할 수 있습니다. 만든 논리 앱은 HTTP 요청 트리거를 포함하여 다른 응용 프로그램 또는 다양한 [커넥터](../connectors/apis-list.md)에서도 다양한 원본의 XML 콘텐츠를 가져올 수 있습니다. 논리 앱에 대한 자세한 내용은 [논리 앱 설명서](logic-apps-overview.md "논리 앱에 대해 자세히 알아보기")에서도 다양한 원본의 XML 콘텐츠를 가져올 수 있습니다.  

## <a name="create-the-flat-file-encoding-connector"></a>플랫 파일 인코딩 커넥터 만들기
다음 단계를 수행하여 플랫 파일 인코딩 커넥터를 논리 앱을 추가합니다.

1. 논리 앱을 만들고 [통합 계정에 연결](logic-apps-enterprise-integration-accounts.md "논리 앱에 통합 계정을 연결하는 방법 알아보기")에서도 다양한 원본의 XML 콘텐츠를 가져올 수 있습니다. 이 계정은 XML 데이터를 인코딩하는 데 사용할 스키마를 포함합니다.  
2. **요청 - HTTP 요청을 받은 경우** 트리거를 논리 앱에 추가합니다.  
   ![선택할 트리거의 스크린샷](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
3. 다음을 수행하여 플랫 파일 인코딩 작업을 추가합니다.
   
    a. **더하기** 기호를 선택합니다.
   
    나. 더하기 기호를 선택한 후에 표시되는 **작업 추가** 링크를 선택합니다.
   
    다. 검색 상자에 *플랫* 을 입력하여 사용하려는 작업에 대해 모든 작업을 필터링합니다.
   
    d. 목록에서 **플랫 파일 Encoding** 옵션을 선택합니다.   
   ![플랫 파일 Encoding 옵션의 스크린샷](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
4. **플랫 파일 Encoding** 대화 상자에서 **콘텐츠** 텍스트 상자를 선택합니다.  
   ![텍스트 상자 콘텐츠의 스크린샷](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
5. 본문 태그를 인코딩하려는 콘텐츠로 선택합니다. 본문 태그는 콘텐츠 필드를 채웁니다.     
   ![본문 태그의 스크린샷](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
6. **스키마 이름** 목록 상자를 선택하고 입력 콘텐츠를 인코딩하는 데 사용할 스키마를 선택합니다.    
   ![스키마 이름 목록 상자의 스크린샷](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
7. 작업을 저장합니다.   
   ![저장 아이콘의 스크린샷](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

이 시점에서 플랫 파일 인코딩 커넥터의 설정이 완료됩니다. 실제 응용 프로그램에서는 SalesForce와 같은 LOB(line-of-business) 응용 프로그램에 인코딩한 데이터를 저장할 수 있습니다. 또는 인코딩된 데이터를 거래 업체에 보낼 수 있습니다. 제공된 다른 커넥터 중 하나를 사용하여 Salesforce 또는 거래 파트너에게 인코딩 작업의 출력을 보내는 동작을 쉽게 추가할 수 있습니다.

이제 HTTP 끝점에 요청하고 요청 본문에 XML 콘텐츠를 포함하여 커넥터를 테스트할 수 있습니다.  

## <a name="create-the-flat-file-decoding-connector"></a>플랫 파일 디코딩 커넥터 만들기

> [!NOTE]
> 이러한 단계를 완료하려면 통합 계정에 이미 업로드된 스키마 파일이 있어야 합니다.

1. **요청 - HTTP 요청을 받은 경우** 트리거를 논리 앱에 추가합니다.  
   ![선택할 트리거의 스크린샷](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
2. 다음을 수행하여 플랫 파일 디코딩 작업을 추가합니다.
   
    a. **더하기** 기호를 선택합니다.
   
    나. 더하기 기호를 선택한 후에 표시되는 **작업 추가** 링크를 선택합니다.
   
    다. 검색 상자에 *플랫* 을 입력하여 사용하려는 작업에 대해 모든 작업을 필터링합니다.
   
    d. 목록에서 **플랫 파일 디코딩** 옵션을 선택합니다.   
   ![플랫 파일 디코딩 옵션의 스크린샷](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
3. **콘텐츠** 제어를 선택합니다. 그러면 이전 단계에서 콘텐츠로 디코딩하는 데 사용할 수 있는 콘텐츠의 목록이 생성됩니다. 들어오는 HTTP 요청의 *본문* 을 콘텐츠로 디코딩하는 데 사용할 수 있습니다. **콘텐츠** 제어에 직접 디코딩할 콘텐츠를 입력할 수도 있습니다.     
4. *본문* 태그를 선택합니다. 이제 본문 태그는 **콘텐츠** 제어에 위치합니다.
5. 콘텐츠를 디코딩하는 데 사용할 스키마의 이름을 선택합니다. 다음 스크린샷에서는 *OrderFile* 이 선택한 스키마 이름임을 보여 줍니다. 이전에 이 스키마 이름을 통합 계정에 업로드했습니다.
   
   ![플랫 파일 디코딩 대화 상자의 스크린샷](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. 작업을 저장합니다.  
   ![저장 아이콘의 스크린샷](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

이 시점에서 플랫 파일 디코딩 커넥터의 설정이 완료됩니다. 실제 응용 프로그램에서는 SalesForce와 같은 LOB(line-of-business) 응용 프로그램에 디코딩한 데이터를 저장할 수 있습니다. Salesforce에 디코딩 작업의 출력을 보내는 작업을 쉽게 추가할 수 있습니다.

이제 HTTP 끝점에 요청하고 요청 본문에 디코딩하려는 XML 콘텐츠를 포함하여 커넥터를 테스트할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")  

