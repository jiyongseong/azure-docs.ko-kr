---
title: Azure Log Analytics Linux 에이전트 문제 해결 | Microsoft Docs
description: Log Analytics Linux 에이전트에서 발생하는 가장 일반적인 문제에 대한 증상, 원인 및 해결 방법에 대해 설명합니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: magoedte
ms.openlocfilehash: 80d7e39b284554ebfa8cac4488e1663b3e3648e8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Log Analytics용 Linux 에이전트의 문제를 해결하는 방법

이 문서에서는 Log Analytics용 Linux 에이전트에서 발생할 수 있는 오류를 해결하는 데 도움을 주며, 가능한 해결 방법을 제안합니다.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>문제: 프록시를 통해 Log Analytics에 연결할 수 없습니다.

### <a name="probable-causes"></a>가능한 원인
* 등록하는 동안 지정된 프록시가 올바르지 않습니다.
* Log Analytics 및 Azure Automation 서비스 끝점이 데이터 센터의 허용 목록에 없습니다. 

### <a name="resolutions"></a>해결 방법
1. 다음 명령과 `-v` 옵션을 사용하여 OMS Agent for Linux가 있는 Log Analytics 서비스에 다시 등록합니다. OMS 서비스에 대한 프록시를 통해 연결되는 에이전트의 자세한 정보를 출력할 수 있습니다. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. [프록시 설정 업데이트](log-analytics-agent-manage.md#update-proxy-settings) 섹션을 검토하여 프록시 서버를 통해 통신하도록 에이전트를 제대로 구성했는지 확인합니다.    
* 다음 Log Analytics 서비스 끝점이 허용 목록에 있는지 한 번 더 확인합니다.

    |에이전트 리소스| 포트 | 방향 |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | 포트 443| 인바운드 및 아웃바운드 |  
    |*.oms.opinsights.azure.com | 포트 443| 인바운드 및 아웃바운드 |  
    |\*.blob.core.windows.net | 포트 443| 인바운드 및 아웃바운드 |  
    |* .azure-automation.net | 포트 443| 인바운드 및 아웃바운드 | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>문제: 등록하는 동안 403 오류 발생

### <a name="probable-causes"></a>가능한 원인
* Linux 서버의 날짜 및 시간이 올바르지 않습니다 
* 사용된 작업 영역 ID 및 작업 영역 키가 올바르지 않습니다.

### <a name="resolution"></a>해결 방법

1. 명령 시간을 사용하여 Linux 서버에서 시간을 확인합니다. 시간이 현재 시간에서 +/-15분인 경우 등록이 실패합니다. 이 문제를 해결하려면 Linux 서버의 날짜 및/또는 시간대를 업데이트합니다. 
2. Linux용 OMS 에이전트 최신 버전이 설치되어 있는지 확인합니다.  최신 버전은 시간 차이로 인해 등록 실패가 발생하는지 여부를 알려줍니다.
3. 이 항목 앞부분의 설치 지침에 따라 올바른 작업 영역 ID 및 작업 영역 키를 사용하여 다시 등록합니다.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>문제: 등록 직후에 로그 파일에 500 및 404 오류가 표시됨
이 문제는 알려진 문제이며 Linux 데이터를 Log Analytics 작업 영역으로 처음 업로드할 때 발생합니다. 이 문제는 전송되는 데이터 또는 서비스 환경에 영향을 미치지 않습니다.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>문제: Azure Portal에서 데이터가 보이지 않습니다.

### <a name="probable-causes"></a>가능한 원인

- Log Analytics 서비스 등록이 실패했습니다.
- Log Analytics 서비스에 대한 연결이 차단되었습니다.
- Linux용 OMS 에이전트가 백업되었습니다.

### <a name="resolutions"></a>해결 방법
1. 파일이 있는지 확인하여 Log Analytics 서비스 등록에 성공했는지 알아봅니다. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. `omsadmin.sh` 명령줄 명령을 사용하여 다시 등록합니다.
3. 프록시를 사용하는 경우 앞서 제공된 프록시 문제 해결 단계를 참조하세요.
4. OMS Agent for Linux가 서비스와 통신할 수 없는 경우 에이전트의 데이터가 최대 버퍼 크기인 50MB로 대기될 수 있습니다. 명령 `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`를 실행하여 Linux용 OMS 에이전트를 다시 시작해야 합니다. 

    >[!NOTE]
    >이 문제는 에이전트 버전 1.1.0-28 및 이상에서 해결되었습니다.

