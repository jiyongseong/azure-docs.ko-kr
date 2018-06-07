---
title: Microsoft Azure Cloud Services FAQ에 대한 응용 프로그램 및 서비스 사용 가능성 문제 | Microsoft Docs
description: 이 문서는 Microsoft Azure Cloud Services의 응용 프로그램 및 서비스 사용 가능성에 대한 질문과 대답을 나열합니다.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 49576aa99f6cd505648e33348b89e502bea9d5c4
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services의 응용 프로그램 및 서비스 사용 가능성 문제: FAQ(질문과 대답)

이 문서는 [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)의 응용 프로그램 및 서비스 사용 가능성 문제에 대한 질문과 대답을 포함합니다. 크기 정보는 [Cloud Services VM 크기 페이지](cloud-services-sizes-specs.md) 를 참조할 수도 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>내 역할은 재활용되었습니다. 클라우드 서비스에 롤아웃된 업데이트가 있습니까?
Microsoft에서는 대략 한 달에 한 번 Windows Azure PaaS VM에 대한 새 게스트 OS 버전을 릴리스합니다. 이러한 업데이트로 게스트 OS가 파이프라인에서 유일합니다. 릴리스는 다른 요인에 따라 달라질 수 있습니다. 또한 Azure는 수 천만 대의 컴퓨터에서 실행됩니다. 따라서 역할이 다시 부팅되는 정확한 날짜와 시간을 예측할 수 있습니다. 최신 정보로 게스트 OS 업데이트 RSS 피드를 업데이트하지만 보고된 시간은 대략적인 값을 고려해야 합니다. 고객에 문제가 있어 정확하게 재부팅 시간을 제한하도록 작업 중인 것으로 알고 있습니다.

최신 게스트 OS 업데이트에 대한 자세한 내용은 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](cloud-services-guestos-update-matrix.md)를 참조하세요.

게스트 및 호스트 OS 업데이트의 기술 세부 정보에 대한 다시 시작 및 포인터에 도움이 되는 정보는 [OS 업그레이드로 인한 역할 인스턴스 다시 시작](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)이라는 MSDN 블로그 게시물을 참조하세요.

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>서비스가 일정 시간 동안 유휴 상태가 된 후에 클라우드 서비스에 대한 첫 번째 요청이 평소보다 오래 걸리는 이유는 무엇인가요?
웹 서버가 첫 번째 요청을 받으면 먼저 코드를 다시 컴파일하고 다음 요청을 처리합니다. 바로 이러한 이유로 첫 번째 요청은 다른 항목보다 더 많은 시간이 걸립니다. 기본적으로 앱 풀은 사용자가 휴지하는 경우에 종료됩니다. 또한 앱 풀은 기본적으로 1,740분(29시간)마다 재활용됩니다.

응용 프로그램, 중단 또는 메모리 누수를 일으킬 수 있는 불안정한 경우 상태를 방지하기 위해 IIS(인터넷 정보 서비스) 응용 프로그램 풀을 정기적으로 재활용할 수 있습니다.

다음 문서는 이 문제를 이해하고 완화하는 데 도움이 됩니다.
* [IIS에서 느린 초기 로드 수정](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [앱 풀 재활용이 느려진 후 IIS 7.5 웹 응용 프로그램 첫 번째 요청](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

IIS의 기본 동작을 변경하려는 경우 웹 역할 인스턴스에 변경 내용을 수동으로 적용하면 변경 내용이 손실되기 때문에 시작 작업을 사용해야 합니다.

자세한 내용은 [클라우드 서비스에 대한 시작 작업 구성 및 실행 방법](cloud-services-startup-tasks.md)을 참조하세요.
