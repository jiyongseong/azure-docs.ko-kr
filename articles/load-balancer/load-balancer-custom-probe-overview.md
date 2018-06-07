---
title: Load Balancer 사용자 지정 프로브를 사용하여 상태 모니터링 | Microsoft Docs
description: Azure 부하 분산 장치에 사용자 지정 프로브를 사용하여 부하 분산 장치 뒤의 인스턴스를 모니터링하는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/8/2018
ms.author: kumud
ms.openlocfilehash: 0aab72fdf48589a72707ae87f90af11f65f35088
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="understand-load-balancer-probes"></a>Load Balancer 프로브 이해

Azure Load Balancer는 상태 프로브를 사용하여 새 흐름 받아야 하는 백 엔드 풀 인스턴스를 결정합니다. 상태 프로브가 실패하면 Load Balancer는 해당 비정상 인스턴스로 새 흐름을 전송하는 것을 중지하며, 해당 인스턴스의 기존 흐름은 영향을 받지 않습니다.  모든 백 엔드 풀 인스턴스 프로브가 다운되면 백 엔드 풀의 모든 인스턴스에서 모든 기존 흐름이 시간 초과됩니다.

클라우드 서비스 역할(작업자 역할 및 웹 역할)은 프로브 모니터링에 게스트 에이전트를 사용합니다. Load Balancer 뒤의 VM을 사용하는 경우 TCP 또는 HTTP 사용자 지정 상태 프로브를 구성해야 합니다.

## <a name="understand-probe-count-and-timeout"></a>프로브 수 및 시간 제한 이해

프로브 동작은 다음 사항에 따라 달라집니다.

* 인스턴스의 레이블을 켜짐으로 지정할 수 있는 성공 프로브 수.
* 인스턴스의 레이블을 중단으로 지정하게 되는 실패 프로브 수.

SuccessFailCount에 설정된 시간 제한 및 빈도 값에 따라 인스턴스가 실행 중인지 여부가 확인됩니다. Azure 포털에서 시간 제한은 빈도 값의 두 배로 설정됩니다.

끝점에 대한 모든 부하 분산된 인스턴스(즉, 부하 분산된 집합)의 프로브 구성은 동일해야 합니다. 특정 끝점 조합에 대해 호스트된 동일한 서비스의 각 역할 인스턴스 또는 VM에 대한 프로브 구성을 다르게 설정할 수 없습니다. 예를 들어 각 인스턴스의 로컬 포트 및 시간 제한이 동일해야 합니다.

> [!IMPORTANT]
> 부하 분산 장치 프로브는 IP 주소 168.63.129.16을 사용합니다. 이 공용 IP 주소는 사용자 고유의 Azure IP Virtual Network 시나리오에서 내부 플랫폼 리소스에 대한 통신 채널을 용이하게 하는 데 사용됩니다. 가상 공용 IP 주소 168.63.129.16은 모든 지역에서 사용되며, 변경되지 않습니다. 모든 로컬 방화벽 정책에서 이 IP 주소를 허용하는 것이 좋습니다. 내부 Azure 플랫폼에서만 이 주소에서 메시지를 소싱할 수 있으므로 이를 보안 위험으로 간주해서는 안 됩니다. 방화벽 정책에서 이 IP 주소를 허용하지 않는 경우 다양한 시나리오에서 예기치 않은 동작이 발생합니다. 이러한 동작의 예로는 168.63.129.16의 동일한 IP 주소 범위 구성과 IP 주소 복제가 있습니다.

## <a name="learn-about-the-types-of-probes"></a>프로브 유형에 대해 알아보기

### <a name="guest-agent-probe"></a>게스트 에이전트 프로브

게스트 에이전트 프로브는 Azure Cloud Services에만 제공됩니다. Load Balancer는 VM 내 게스트 에이전트를 활용합니다. 그런 다음 인스턴스가 준비 상태인 경우에만 수신 대기하며 HTTP 200 OK로 응답합니다. 다른 상태는 사용 중, 재생 중 또는 중지 중입니다.

자세한 내용은 [상태 프로브에 대한 서비스 정의 파일(csdef) 구성](https://msdn.microsoft.com/library/azure/ee758710.aspx) 또는 [클라우드 서비스를 위한 공용 부하 분산 장치 만들기 시작](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)을 참조하세요.

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>게스트 에이전트 프로브에서 인스턴스를 비정상으로 표시하는 경우

게스트 에이전트가 HTTP 200 OK로 응답하는 데 실패하면 부하 분산 장치가 인스턴스를 응답하지 않음으로 표시합니다. 그런 다음 해당 인스턴스에 대한 트래픽 전송을 중지합니다. 이때 부하 분산 장치는 계속해서 인스턴스에 ping을 실행합니다. 게스트 에이전트가 HTTP 200으로 응답하면 부하 분산 장치는 다시 트래픽을 해당 인스턴스에 전송합니다.

웹 역할을 사용하는 경우 웹 사이트 코드는 일반적으로 w3wp.exe에서 실행되며 이는 Azure 패브릭 또는 게스트 에이전트에서 모니터링하지 않습니다. w3wp.exe(예: HTTP 500 응답)의 실패는 게스트 에이전트에 보고되지 않습니다. 결과적으로 부하 분산 장치는 해당 인스턴스를 순환에서 제거하지 않습니다.

### <a name="http-custom-probe"></a>HTTP 사용자 지정 프로브

HTTP 사용자 지정 프로브는 기본 게스트 에이전트 프로브를 재정의합니다. 사용자는 고유한 사용자 지정 논리를 만들어 역할 인스턴스의 상태를 확인할 수 있습니다. 부하 분산 장치는 기본적으로 15초마다 끝점을 검색합니다. 인스턴스는 시간 제한 기간 내에 HTTP 200으로 응답하는 경우 부하 분산 장치 순환에 포함되는 것으로 간주됩니다. 시간 제한 기간은 기본적으로 31초입니다.

HTTP 사용자 지정 프로브는 부하 분산 장치 순환에서 인스턴스를 제거하는 사용자 고유의 논리를 구현하는 경우 유용할 수 있습니다. 예를 들어 CPU 사용량이 90%를 초과하고 200이 아닌 상태를 반환하는 경우 인스턴스를 제거하도록 결정할 수 있습니다. w3wp.exe를 사용하는 웹 역할이 있는 경우에도 웹 사이트 자동 모니터링이 이루어집니다. 웹 사이트 코드에서 실패하면 부하 분산 장치 프로브로 200이 아닌 상태를 반환합니다.

> [!NOTE]
> HTTP 사용자 지정 프로브는 상대 경로 및 HTTP 프로토콜만 지원합니다. HTTPS는 지원되지 않습니다.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>HTTP 사용자 지정 프로브에서 인스턴스를 비정상으로 표시하는 경우

* HTTP 응용 프로그램에서 200 이외의 HTTP 응답 코드(예: 403, 404 또는 500)를 반환합니다. 이 긍정 응답은 서비스에서 즉시 응용 프로그램 인스턴스를 제거하도록 경고합니다.
* 시간 제한 기간 후에는 HTTP 서버가 전혀 응답하지 않습니다. 설정된 시간 제한 값에 따라 프로브가 실행되지 않음으로 표시되기 전에 즉, SuccessFailCount 프로브가 전송되기 전에 여러 프로브 요청이 무응답 상태가 될 수 있습니다.
* 서버가 TCP 재설정을 통해 연결을 닫습니다.

### <a name="tcp-custom-probe"></a>TCP 사용자 지정 프로브

TCP 사용자 지정 프로브는 정의된 포트를 통해 3방향 핸드셰이크를 수행하여 연결을 시작합니다.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>TCP 사용자 지정 프로브에서 인스턴스를 비정상으로 표시하는 경우

* 시간 제한 기간 후에는 TCP 서버가 전혀 응답하지 않습니다. 프로브가 실행되지 않음으로 표시되는 시기는 프로브 요청이 일정 횟수 이상 무응답 상태로 이동되면 프로브를 실행되지 않음으로 표시하도록 구성된 실패한 프로브 요청 수에 따라 다릅니다.
* 프로브가 역할 인스턴스에서 TCP 재설정을 받습니다.

HTTP 상태 프로브 또는 TCP 프로브 구성 방법에 대한 자세한 내용은 [PowerShell을 사용하여 리소스 관리자에서 공용 부하 분산 장치 만들기 시작](load-balancer-get-started-internet-arm-ps.md)을 참조하세요.

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>부하 분산 장치 순환에 다시 정상 인스턴스 추가

TCP 및 HTTP 프로브는 다음과 같은 경우 정상으로 간주되며 역할 인스턴스를 정상 상태로 표시합니다.

* VM이 처음 부팅될 때 부하 분산 장치에서 긍정 프로브를 가져온 경우.
* 앞에서 설명한 SuccessFailCount 숫자가 역할 인스턴스를 정상 상태로 표시하는 데 필요한 성공한 프로브 값을 정의하는 경우. 역할 인스턴스가 제거되면 성공한 연속 프로브 수가 역할 인스턴스를 실행 중으로 표시하는 SuccessFailCount 값보다 크거나 같아야 합니다.

> [!NOTE]
> 역할 인스턴스의 상태가 변동되는 경우 부하 분산 장치는 역할 인스턴스를 다시 정상 상태로 전환하기 전에 좀 더 오래 기다립니다. 이러한 추가 대기 시간은 의도적인 정책으로, 사용자 및 인프라를 보호합니다.

## <a name="use-log-analytics-for-a-load-balancer"></a>부하 분산 장치에 대한 로그 분석 사용

[로그 분석](load-balancer-monitor-log.md)을 사용하여 부하 분산 장치 프로브 상태 및 프로브 수를 확인할 수 있습니다. Power BI 또는 Azure Operational Insights에서 로깅을 사용하여 부하 분산 장치 상태에 대한 통계를 제공할 수 있습니다.
