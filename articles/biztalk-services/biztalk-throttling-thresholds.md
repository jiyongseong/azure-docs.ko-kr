---
title: "BizTalk Services의 제한에 대한 자세한 정보 | Microsoft Docs"
description: "BizTalk Services에 대한 제한 임계값 및 결과 런타임 동작에 대해 알아봅니다. 제한은 메모리 사용량 및 메시지 수를 기반으로 합니다. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 39fc5ef36bb581c3a81c9948fda048f6cb75eb7e
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: 제한

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk Services는 메모리 사용량과 동시 메시지 처리 수의 두 가지 조건에 따라 서비스 제한을 구현합니다. 이 항목에서는 제한 임계값을 나열하고 제한 조건이 발생할 때의 런타임 동작을 설명합니다.

## <a name="throttling-thresholds"></a>제한 임계값
다음 표에서는 제한 원본 및 임계값을 나열합니다.

|  | 설명 | 낮은 임계값 | 높은 임계값 |
| --- | --- | --- | --- |
| 메모리 |사용 가능한 총 시스템/PageFileBytes 백분율 <p><p>사용 가능한 총 PageFileBytes는 시스템 RAM의 대략 2배입니다. |60% |70% |
| 메시지 처리 |동시 메시지 처리 수 |40 * 코어 수 |100 * 코어 수 |

높은 임계값에 도달하면 Azure BizTalk Services에서 제한을 시작합니다. 낮은 임계값에 도달하면 제한이 중지됩니다. 예를 들어 서비스에서 65% 시스템 메모리를 사용하고 있다고 가정합니다. 이런 상황에서는 서비스가 제한하지 않습니다. 그러나 서비스가 70%의 시스템 메모리를 사용하기 시작하면 상황이 달라집니다. 서비스가 제한을 시작하여 60%(낮은 임계값)의 시스템 메모리를 사용할 때까지 계속 제한합니다.

Azure BizTalk Services는 제한 상태(일반 상태와 제한된 상태) 및 제한 기간을 추적합니다.

## <a name="runtime-behavior"></a>런타임 동작
Azure BizTalk Services가 제한 상태에 들어가면 다음과 같은 상황이 발생합니다.

* 조정은 역할 인스턴스별로 적용됩니다. 예:<br/>
  RoleInstanceA가 제한 중입니다. RoleInstanceB는 제한하고 있지 않습니다. 이런 경우 RoleInstanceB의 메시지가 예상대로 처리됩니다. RoleInstanceA의 메시지는 취소되고 다음과 같은 오류를 표시하며 실패합니다.<br/><br/>
  **서버가 사용 중입니다. 나중에 다시 시도하세요.**<br/><br/>
* 어떤 끌어오기 원본도 메시지를 폴링하거나 다운로드하지 않습니다. 예를 들면 다음과 같습니다.<br/>
  파이프라인이 외부 FTP 원본에서 메시지를 끌어옵니다. 끌어오기를 수행하는 역할 인스턴스가 제한 상태로 전환됩니다. 이런 경우 역할 인스턴스가 제한을 중지할 때까지 파이프라인에서 추가 메시지 다운로드를 중지합니다.
* 클라이언트에서 메시지를 다시 제출할 수 있도록 응답이 클라이언트로 전송됩니다.
* 제한이 해결될 때까지 기다려야 합니다. 특히 낮은 임계값에 도달할 때까지 기다려야 합니다.

## <a name="important-notes"></a>중요
* 제한을 사용하지 않도록 설정할 수 없습니다.
* 제한 임계값은 수정할 수 없습니다.
* 제한은 시스템 전체에서 구현됩니다.
* Azure SQL Database 서버에는 기본 제공 제한도 있습니다.

## <a name="additional-azure-biztalk-services-topics"></a>추가 Azure BizTalk Services 항목
* [Azure BizTalk Services SDK 설치](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [자습서: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Azure BizTalk Services SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>참고 항목
* [BizTalk Services: Developer, Basic, Standard 및 Premium Editions 차트](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: 프로비저닝 상태 차트](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: 대시보드, 모니터 및 크기 조정 탭](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Backup 및 복원](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: 발급자 이름 및 발급자 키](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

