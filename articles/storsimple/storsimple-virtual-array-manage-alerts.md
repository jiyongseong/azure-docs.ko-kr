---
title: "Microsoft Azure StorSimple 가상 배열 경고 보기 및 관리 | Microsoft Docs"
description: "StorSimple 가상 배열 경고 조건 및 심각도 및 StorSimple 관리자 서비스를 사용하여 경고를 관리하는 방법을 설명합니다."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d4d680e3460fbeff73c2f334c6461da7967374d
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2018
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>StorSimple 장치 관리자를 사용하여 StorSimple 가상 배열에 대한 경고 관리

## <a name="overview"></a>개요

StorSimple Device Manager 서비스의 경고 기능은 StorSimple 가상 배열과 관련된 경고를 실시간으로 검토하고 삭제할 방법을 제공합니다. **서비스 요약** 블레이드에 대한 경고를 사용하여 StorSimple 가상 배열 및 전체 Microsoft Azure StorSimple 솔루션의 상태 문제를 중앙에서 모니터링할 수 있습니다.

이 자습서에서는 경고 알림을 구성하는 방법, 일반적인 경고 조건, 경고 심각도 수준 및 경고를 보고 추적하는 방법을 설명합니다. 또한 경고 빠른 참조 테이블을 포함하여 신속하게 특정 경고를 찾아서 적절 하게 응답할 수 있습니다.

![경고 페이지](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>경고 설정 구성

각 StorSimple 가상 배열에 대한 경고 조건을 메일로 알림을 받을 것인지 여부를 선택할 수 있습니다. 또한 **추가 메일 받는 사람** 상자에서 해당 메일 주소를 입력하여 다른 경고 알림 받는 사람을 식별할 수 있으며 세미콜론으로 구분합니다.

> [!NOTE]
> 가상 배열 당 최대 20개의 메일 주소를 입력할 수 있습니다.


가상 배열에 메일 알림을 사용하도록 설정한 후에 알림 목록의 멤버는 중요한 경고가 발생할 때마다 메일 메시지를 받게 됩니다. 메시지는 *storsimple-alerts-noreply@mail.windowsazure.com*에서 전송되며 경고 조건을 설명합니다. 받는 사람은 **구독 취소**를 클릭하여 메일 알림 목록에서 제거할 수 있습니다.

#### <a name="to-enable-email-notification-for-alerts"></a>경고에 메일 알림을 사용하려면

1. StorSimple Device Manager 서비스로 이동하고 **관리** 섹션에서 **장치**를 선택하고 클릭합니다. 표시되는 장치 목록에서 장치를 선택하고 클릭합니다.
   
    ![경고 설정](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. 그러면 **설정** 블레이드를 엽니다. **장치 설정** 섹션에서 **일반**을 선택합니다. 그러면 **일반 설정** 블레이드를 엽니다.
   
    ![경고 알림 구성](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. **일반 설정** 블레이드에서 **경고 설정** 섹션으로 이동하고 다음 항목을 설정합니다.
   
   1. **전자 메일 알림 사용** 필드에서 **예**를 선택합니다.
   2. 서비스 관리자 및 모든 공동 관리자가 경고 알림을 받기를 원하는 경우 **전자 메일 서비스 관리자** 필드에서 **예**를 선택합니다.
   3. **추가 메일 받는 사람** 필드에 경고 알림을 받을 다른 모든 받는 사람의 메일 주소를 입력합니다. *someone@somewhere.com* 형식으로 이름을 입력합니다. 세미콜론을 사용하여 메일 주소를 구분합니다. 가상 장치당 최대 20개의 메일 주소를 구성할 수 있습니다.
      
       ![경고 알림 구성](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. 테스트 전자 메일 알림을 보내려면 **테스트 전자 메일 보내기**를 클릭합니다. 테스트 알림을 전달하여 StorSimple Device Manager 서비스가 상태 메시지를 표시합니다.
      
       ![전송된 경고 테스트 알림 메일](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > 테스트 알림 메시지를 보낼 수 없는 경우 StorSimple Device Manager 서비스가 적절한 메시지를 표시합니다. **확인**을 클릭하고 몇 분 정도 기다렸다가 다시 테스트 알림 메시지를 보냅니다.
      > 
      > 
   5. 페이지 맨 아래에서 **저장** 을 클릭하여 구성을 저장합니다. 확인하라는 메시지가 표시되면 **예**를 클릭합니다.
      
      ![전송된 경고 테스트 알림 메일](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>일반 경고 조건

StorSimple 가상 배열은 다양한 조건에 대한 응답으로 경고를 생성합니다. 다음은 가장 일반적인 유형의 경고 조건입니다.

* **연결 문제** – 이러한 경고는 데이터 전송이 어려울 때 발생합니다. Azure Storage 계정에서 데이터를 전송하는 동안 또는 가상 장치와 StorSimple Device Manager 서비스 사이의 연결 부족으로 인해 통신 문제가 발생할 수 있습니다. 통신 문제는 너무 많은 실패 지점이 있기 때문에 해결하기 어렵습니다. 고급 문제를 해결하기 전에 항상 먼저 네트워크 연결 및 인터넷 액세스를 사용할 수 있는지 확인해야 합니다. 포트 및 방화벽 설정에 대한 내용은 [StorSimple 가상 배열 시스템 요구 사항](storsimple-ova-system-requirements.md)으로 이동합니다. 문제 해결에 대한 도움은 [Test-Connection cmdlet 문제 해결](storsimple-troubleshoot-deployment.md)로 이동합니다.
* **성능 문제** – 이러한 경고는 부하가 높을 때처럼 시스템이 최적으로 작동하지 않는 경우에 발생합니다.

또한 보안, 업데이트 또는 작업 실패와 관련된 경고를 표시할 수 있습니다.

## <a name="alert-severity-levels"></a>경고 심각도 수준

경고는 경고 상황에 있는 영향 및 경고에 대한 응답을 위한 필요성에 따라 심각도 수준이 다릅니다. 경고 심각도 수준은 다음과 같습니다.

* **중요** – 이 경고는 시스템의 성공적인 성능이 영향을 미치는 조건에 대한 응답입니다. 작업은 StorSimple 서비스가 중단되지 않았는지 확인해야 합니다.
* **경고** – 이 상태는 확인되지 않은 경우 위험할 수 있습니다. 상황을 조사하고 문제를 삭제하는 데 필요한 모든 작업을 수행해야 합니다.
* **정보** – 이 경고는 시스템의 추적 및 관리에 유용할 수 있는 정보를 포함합니다.

## <a name="view-and-track-alerts"></a>경고 보기 및 추적

StorSimple Device Manager 서비스 요약 블레이드는 가상 장치에서 경고의 개수를 빠른 보기로 제공하여 심각도 수준에 따라 정렬합니다.

![경고 대시보드](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

심각도 수준을 클릭하여 **경고** 블레이드를 엽니다. 결과는 해당 심각도와 일치하는 경고만을 포함합니다.

![경고 유형으로 지정된 경고 보고서](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

목록에서 경고를 클릭하여 경고가 보고된 마지막 시간, 장치에서 경고의 발생 횟수 및 경고를 해결하기 위한 권장된 조치를 포함하여 경고에 대한 추가 세부 정보를 가져옵니다.

![경고 목록 및 세부 정보](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Microsoft 지원에 정보를 보내야 할 경우 텍스트 파일에 알림 세부 정보를 복사할 수 있습니다. 권장 사항을 따르고 경고 조건 온-프레미스를 해결한 후에 목록에서 경고를 지워야 합니다. 목록에서 경고를 선택한 다음 **지우기**를 클릭합니다. 여러 경고를 지우려면 각 경고를 선택하고 **경고** 열을 제외한 모든 열을 클릭한 다음 삭제할 모든 경고를 선택한 후 **지우기**를 클릭합니다.

**지우기**를 클릭하는 경우 경고에 대한 의견 및 문제를 해결하기 위해 수행할 단계에 대한 의견을 제공할 수 있습니다. 

![경고 설명](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

다른 이벤트가 새 정보로 트리거될 경우 일부 이벤트가 시스템에서 지워집니다. 

## <a name="sort-and-review-alerts"></a>경고 정렬 및 검토

**경고** 블레이드에서는 최대 250개의 경고를 표시할 수 있습니다. 해당 경고 수를 초과한 경우 경고 전체가 기본 보기에 표시되지 않습니다. 어떤 경고를 표시할지 사용자 지정을 하려면 다음 필드를 결합할 수 있습니다.

* **상태** – **활성** 또는 **지워짐** 경고를 표시할 수 있습니다. 시스템이 새 정보로 경고 조건을 업데이트하기 때문에 삭제된 경고는 관리자가 수동으로 지웠거나 프로그래밍 방식으로 지운 반면 활성 경고는 시스템에서 여전히 트리거됩니다.
* **심각도** – 모든 심각도 수준(중요, 경고, 정보) 또는 중요한 경고와 같은 특정 심각도의 경고를 표시할 수 있습니다.
* **원본** – 모든 원본에서 경고를 표시하거나 서비스 또는 하나 또는 모든 가상 장치에서 발생하는 경고를 제한할 수 있습니다.
* **시간 범위** – **시작** 및 **종료** 날짜 및 시간 스탬프를 지정하여 관심이 있는 기간 동안 경고를 살펴볼 수 있습니다.

## <a name="alerts-quick-reference"></a>빠른 참조 경고

다음 표에서는 사용 가능한 경우 추가 정보 및 권장 사항 뿐만 아니라 발생할 수 있는 StorSimple 경고의 일부를 나열합니다. StorSimple 가상 배열 경고는 다음 범주 중 하나에 속합니다.

* [클라우드 연결 경고](#cloud-connectivity-alerts)
* [구성 경고](#configuration-alerts)
* [작업 실패 경고](#job-failure-alerts)
* [성능 경고](#performance-alerts)
* [보안 경고](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>클라우드 연결 경고

| 경고 텍스트 | 행사 | 자세한 내용 / 권장 작업 |
|:--- |:--- |:--- |
| 장치 *<device name>* 이(가) 클라우드에 연결되지 않았습니다. |명명된 장치는 클라우드로 연결할 수 없습니다. |클라우드에 연결할 수 없습니다. 다음 중 하나 때문일 수 있습니다.<ul><li>장치에서 네트워크 설정에 문제가 있을 수 있습니다.</li><li>저장소 계정 자격 증명에 문제가 있을 수 있습니다.</li></ul>연결 문제 해결에 대한 자세한 내용은 장치의 [로컬 웹 UI](storsimple-ova-web-ui-admin.md) 로 이동합니다. |

### <a name="configuration-alerts"></a>구성 경고

| 경고 텍스트 | 행사 | 자세한 내용 / 권장 작업 |
|:--- |:--- |:--- |
| 온-프레미스 가상 장치 구성은 지원되지 않습니다. |성능 저하. |현재 구성은 성능을 저하할 수 있습니다. 서버가 최소 구성 요구 사항을 충족하는지 확인합니다. 자세한 내용은 [StorSimple 가상 배열 요구 사항](storsimple-ova-system-requirements.md)을 참조합니다. |
| <*장치 이름*>에서 프로비전된 디스크 공간이 부족합니다. |디스크 공간 경고입니다. |프로비전된 디스크 공간이 부족합니다. 공간을 확보하려면 다른 볼륨으로 작업을 이동하거나 데이터 공유 또는 삭제하는 것이 좋습니다. |

### <a name="job-failure-alerts"></a>작업 실패 경고

| 경고 텍스트 | 행사 | 자세한 내용 / 권장 작업 |
|:--- |:--- |:--- |
| <*장치 이름*>의 Backup을 완료할 수 없습니다. |Backup 작업이 실패했습니다. |백업을 만들 수 없습니다. 다음 중 하나를 고려하십시오.<ul><li>연결 문제가 성공적인 백업 작업을 방해할 수 있습니다. 연결 문제가 없는지 확인합니다. 연결 문제 해결에 대한 자세한 내용은 가상 장치의 [로컬 웹 UI](storsimple-ova-web-ui-admin.md)로 이동합니다.</li><li>사용 가능한 저장소 공간의 한도에 도달했습니다. 공간을 확보하려면 더 이상 필요 없는 모든 백업을 삭제하는 것이 좋습니다.</li></ul> 문제를 해결하고 경고를 지우고 작업을 다시 시도합니다. |
| <*장치 이름*>의 복제를 완료할 수 없습니다. |복제 작업이 실패했습니다. |복제본을 만들 수 없습니다. 다음 중 하나를 고려하십시오.<ul><li>백업 목록이 잘못되었을 수 있습니다. 목록을 새로고쳐서 여전히 유효한지 확인하십시오.</li><li>연결 문제는 성공적인 복제 작업을 방해할 수 있습니다. 연결 문제가 없는지 확인합니다.</li><li>사용 가능한 저장소 공간의 한도에 도달했습니다. 공간을 확보하려면 더 이상 필요 없는 모든 백업을 삭제하는 것이 좋습니다.</li></ul>문제를 해결하고 경고를 지우고 작업을 다시 시도합니다. |

### <a name="networking-alerts"></a>네트워킹 경고
| 경고 텍스트 | 행사 | 자세한 내용 / 권장 작업 |
|:--- |:--- |:--- |
| 인증 서비스에 연결할 수 없습니다. |데이터 경로 오류 |인증에 사용되는 URL에 연결할 수 없습니다. 방화벽 규칙에 StorSimple 장치에 대해 지정된 URL 패턴이 포함되어 있는지 확인합니다. Azure Portal에서 URL 패턴에 대한 자세한 내용은 [StorSimple 가상 배열 네트워킹 요구 사항](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules)을 참조하세요.|

### <a name="performance-alerts"></a>성능 경고

| 경고 텍스트 | 행사 | 자세한 내용 / 권장 작업 |
|:--- |:--- |:--- |
| 데이터 전송에 예기치 않은 대기 시간이 발생했습니다. |느린 데이터 전송. |저장소 서비스의 확장성 목표를 초과하면 제한 오류가 발생합니다. 저장소 서비스는 단일 클라이언트나 테넌트만이 서비스를 사용하는 현상을 방지하기 위해 이 오류를 발생시킵니다. Azure Storage 계정 문제 해결에 대한 자세한 내용은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](../storage/common/storage-monitoring-diagnosing-troubleshooting.md)로 이동합니다. |
| <*장치 이름*>의 로컬 예약 디스크 공간이 부족합니다. |느린 응답 시간. |<*장치 이름*>에 대해 프로비전된 총 크기의 10%가 로컬 장치에 예약되어 있으며, 이제 예약된 공간이 부족합니다. <*장치 이름*>의 워크로드가 작업 부하가 높은 비율의 변동을 발생시키고 있거나 최근에 대용량의 데이터를 마이그레이션한 것 같습니다. 이로 인해 성능이 저하될 수 있습니다. 다음 작업 중 하나를 고려하여 이것을 해결하십시오.<ul><li>이 장치에 대한 클라우드 대역폭을 늘립니다.</li><li>워크로드를 줄이거나 다른 볼륨 또는 공유에 이동시킵니다.</li></ul> |

### <a name="security-alerts"></a>보안 경고

| 경고 텍스트 | 행사 | 자세한 내용 / 권장 작업 |
|:--- |:--- |:--- |
| <*장치 이름*>에 대한 암호가 <*숫자*> 일 후에 만료됩니다. |암호 경고. |암호가 <숫자>일에 만료됩니다. 암호를 변경하는 것이 좋습니다. 자세한 내용은 [StorSimple 가상 배열 장치 관리자 암호를 변경](storsimple-virtual-array-change-device-admin-password.md)으로 이동합니다. |

## <a name="next-steps"></a>다음 단계

* [StorSimple 가상 배열에 대해 더 알아봅니다](storsimple-ova-overview.md).

