---
title: "Azure로 장애 조치(failover) 문제 해결 | Microsoft Docs"
description: "이 문서에서는 Azure로 장애 조치(failover) 시 일반적인 오류 문제를 해결하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 5c94e26c4639284f7e4c53d924f16040118d996c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>가상 머신을 Azure로 장애 조치 시 오류 문제 해결
가상 컴퓨터를 Azure로 장애 조치하는 동안 다음 오류 중 하나가 나타날 수 있습니다. 문제를 해결하려면 각 오류 조건에 대해 설명된 단계를 따르세요.


## <a name="failover-failed-with-error-id-28031"></a>장애 조치 실패(오류 ID 28031)

Site Recovery가 Azure에서 장애 조치된 가상 머신을 만들 수 없습니다. 다음 이유 중 하나로 인해 발생할 수 있습니다.

* 가상 머신을 만드는 데 사용할 수 있는 할당량이 충분하지 않습니다. [구독] -&gt; [사용량 + 할당량]으로 이동하여 사용할 수 있는 할당량을 확인할 수 있습니다. [새로운 지원 요청](http://aka.ms/getazuresupport)을 열어 할당량을 늘릴 수 있습니다.

* 동일한 가용성 집합에 다른 크기 제품군의 가상 머신을 장애 조치하려고 합니다. 동일한 가용성 집합의 모든 가상 머신에 대해 동일한 크기 제품군을 선택했는지 확인합니다. 가상 머신의 Compute 및 네트워크 설정으로 이동하여 크기를 변경한 후 장애 조치를 다시 시도합니다.

* 구독에는 가상 머신의 생성을 방지하는 정책이 있습니다. 가상 머신의 생성을 허용하도록 정책을 변경한 후 장애 조치를 다시 시도합니다.

## <a name="failover-failed-with-error-id-28092"></a>장애 조치 실패(오류 ID 28092)

Site Recovery가 장애 조치된 가상 머신에 대해 네트워크 인터페이스를 만들 수 없습니다. 구독에서 네트워크 인터페이스를 만드는 데 사용할 수 있는 충분한 할당량이 있는지 확인합니다. [구독] -> [사용량 + 할당량]으로 이동하여 사용할 수 있는 할당량을 확인할 수 있습니다. [새로운 지원 요청](http://aka.ms/getazuresupport)을 열어 할당량을 늘릴 수 있습니다. 할당량이 충분하고 일시적인 문제일 수 있다면 작업을 다시 시도합니다. 다시 시도 후에도 문제가 계속되면 이 문서의 끝에 의견을 남겨 주세요.  

## <a name="failover-failed-with-error-id-70038"></a>장애 조치 실패(오류 ID 70038)

Site Recovery가 Azure에서 장애 조치된 클래식 가상 머신을 만들 수 없습니다. 다음 때문에 발생할 수 있습니다.

* 만들 가상 머신에 필요한 가상 네트워크와 같은 리소스 중 하나가 존재하지 않습니다. 가상 머신의 Compute 및 네트워크 설정에 제공된 대로 가상 네트워크를 만들거나 이미 존재하는 가상 네트워크로 설정을 수정한 다음 장애 조치를 다시 시도합니다.


## <a name="next-steps"></a>다음 단계

도움이 더 필요한 경우 [Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 쿼리를 게시하거나 이 문서의 끝에 의견을 남기세요. 도움을 드릴 수 있도록 커뮤니티를 운영 중입니다.
