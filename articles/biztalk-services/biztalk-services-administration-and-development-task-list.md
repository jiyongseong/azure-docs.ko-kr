---
title: "BizTalk Services의 관리 및 개발 작업 목록 | Microsoft Docs"
description: "계획 및 작업은 Azure BizTalk Services를 배포하는 데 도움을 줍니다."
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 9edd7261ca62f505ffb4854e3132fae916768f67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>BizTalk Services의 관리 및 개발 작업 목록

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>시작하기
Microsoft Azure BizTalk Services로 작업할 때 고려해야 할 몇 가지 온-프레미스와 클라우드 기반 구성 요소가 있습니다. 시작하려면 다음 프로세스 흐름을 고려하세요.  

| 단계 | 담당자 | 작업 | 관련 링크 |
| --- | --- | --- | --- |
| 1. |관리자 |Microsoft 계정 또는 조직 계정을 사용하여 Microsoft Azure 구독 만들기 |[Azure 포털](https://portal.azure.com) |
| 2. |관리자 |BizTalk 서비스 만들기 또는 프로비전 |[BizTalk 서비스 만들기](https://msdn.microsoft.com/library/azure/dn232347.aspx) |
| 3. |관리자 |사용자 또는 회사의 BizTalk Services 배포 등록 |[BizTalk Services 포털에서 BizTalk Services 배포 등록 및 업데이트](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |관리자 |응용 프로그램이 온-프레미스 LOB(기간 업무) 시스템에 연결하는 데 BizTalk 어댑터 서비스를 사용하거나 큐 또는 토픽 대상을 사용하는 경우 적용합니다.  Azure Service Bus 네임스페이스 만들기 이 네임스페이스, Service Bus 발급자 이름 및 Service Bus 발급자 키 값을 개발자에게 제공합니다. |[방법: Service Bus 서비스 네임스페이스 만들기 또는 수정](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) 및 [발급자 이름 및 발급자 키 값 가져오기](biztalk-issuer-name-issuer-key.md) |
| 5. |Developer |SDK를 설치하고 Visual Studio에서 BizTalk 서비스 프로젝트를 만듭니다. |[Azure BizTalk Services SDK 설치](https://msdn.microsoft.com/library/azure/hh689760.aspx) 및 [Azure에서 다양한 메시징 끝점 만들기](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Developer |BizTalk 서비스 프로젝트를 Azure에서 호스트된 BizTalk 서비스에 배포합니다. |[BizTalk Services 프로젝트 배포 및 새로 고침](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |관리자 |EDI를 사용하는 경우 적용합니다.  파트너를 추가하고 Microsoft Azure BizTalk Services 포털에서 계약을 만들 수 있습니다. 계약을 만들 때 개발자가 만든 브리지 및/또는 변환을 계약 설정에 추가할 수 있습니다. |[BizTalk Services 포털에서 EDI, AS2 및 EDIFACT 구성](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |관리자 |[REST](https://msdn.microsoft.com/library/azure/dn232347.aspx)를 사용하여 성능 메트릭을 포함한 BizTalk 서비스의 상태를 모니터링합니다. |[BizTalk Services: 대시보드, 모니터 및 크기 조정 탭](http://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |관리자 |Microsoft Azure BizTalk Services 포털을 사용하여 BizTalk Services에서 사용하는 아티팩트를 관리하고 메시지가 브리지 파일에서 처리될 때 메시지를 추적합니다. |[BizTalk Services 포털 사용](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |관리자 |BizTalk 서비스를 백업하려면 백업 계획을 만듭니다. |[BizTalk Services에서 무중단 업무 방식 및 재해 복구](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>다음 단계
[자습서 및 샘플](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Visual Studio에서 프로젝트 만들기](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Azure BizTalk Services SDK 설치](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>개념
[Visual Studio에서 프로젝트 만들기](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 및 EDIFACT 메시징(B2B)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>기타 리소스
[원본, 대상 및 브리지 메시징 끝점 추가](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[메시지 맵 및 변환 학습 및 만들기](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[BizTalk 어댑터 서비스(BAS) 사용](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)

