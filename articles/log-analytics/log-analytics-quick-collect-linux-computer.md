---
title: Azure Log Analytics를 사용하여 온-프레미스 Linux 컴퓨터에서 데이터 수집 | Microsoft Docs
description: Linux에 대해 Log Analytics 에이전트를 배포하고 Log Analytics로 해당 OS에서 데이터의 수집을 활성화하는 방법을 알아봅니다.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/02/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 30bb21ebd6b19f4cc52e364039b9be8a9a7695e5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="collect-data-from-linux-computer-hosted-in-your-environment"></a>사용자 환경에서 호스팅되는 Linux 컴퓨터에서 데이터 수집
[Azure Log Analytics](log-analytics-overview.md)는 자세한 분석 및 상관 관계를 위해 물리적 또는 가상 Linux 컴퓨터 및 사용자 환경의 다른 리소스의 데이터를 단일 리포지토리로 직접 수집할 수 있습니다.  이 빠른 시작 가이드에서는 몇 가지 간단한 단계로 Linux 컴퓨터에서 데이터를 구성 및 수집하는 방법을 보여 줍니다.  Azure Linux VM의 경우 다음 항목 [Azure Virtual Machines에 대한 데이터 수집](log-analytics-quick-collect-azurevm.md)을 참조하세요.  

Linux 에이전트를 배포하기 위한 네트워크 및 시스템 요구 사항을 이해하려면 [Linux 운영 체제에 대한 필수 구성 요소](log-analytics-concept-hybrid.md#prerequisites)를 검토하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure-portal"></a>Azure Portal에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다. 

## <a name="create-a-workspace"></a>작업 영역 만들기
1. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. **만들기**를 클릭하고 다음 항목에 대한 옵션을 선택합니다.

  * 새 **OMS 작업 영역**의 이름(예: *DefaultLAWorkspace*)을 지정합니다. 
  * 기본으로 선택된 값이 적절하지 않으면 드롭다운 목록에서 선택하여 연결할 **구독**을 선택합니다.
  * **리소스 그룹**에 대해 하나 이상의 Azure Virtual Machines를 포함하는 기존 리소스 그룹을 선택합니다.  
  * VM이 배포된 **위치**를 선택합니다.  자세한 내용은 [Log Analytics를 사용할 수 있는 지역](https://azure.microsoft.com/regions/services/)을 참조하세요.  
  * 2018년 4월 2일 이후에 만들어진 새 구독에서 작업 영역을 만드는 경우 *GB당* 가격 책정 계획이 자동으로 사용되며 가격 책정 계층을 선택할 수 있는 옵션이 제공되지 않습니다.  4월 2일 전에 만들어진 기존 구독 또는 기존 EA 등록과 연결된 구독에서 작업 영역을 만드는 경우 선호하는 가격 책정 계층을 선택합니다.  특정 계층에 대한 자세한 내용은 [Log Analytics 가격 책정 정보](https://azure.microsoft.com/pricing/details/log-analytics/)를 참조하세요.

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png)<br>  

3. **OMS 작업 영역** 창에서 필요한 정보를 제공한 후에 **확인**을 클릭합니다.  

정보가 확인되고 작업 영역이 만들어지는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다. 

## <a name="obtain-workspace-id-and-key"></a>작업 영역 ID 및 키 가져오기
Linux용 OMS 에이전트를 설치하기 전에 Log Analytics 작업 영역에 대한 작업 영역 ID 및 키가 필요합니다.  이 정보는 에이전트를 적절히 구성하고 Log Analytics와 성공적으로 통신할 수 있는지 확인하기 위해 에이전트 래퍼 스크립트에서 필요합니다.  

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
2. Log Analytics 작업 영역 목록에서 이전에 만든 *DefaultLAWorkspace*를 선택합니다.
3. **고급 설정**을 선택합니다.<br><br> ![Log Analytics 고급 설정](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. **연결된 원본**을 선택한 다음 **Linux 서버**를 선택합니다.   
5. **작업 영역 ID** 및 **기본 키**의 오른쪽에 값이 있습니다. 두 항목을 복사하여 선호하는 편집기에 붙여넣습니다.   

## <a name="install-the-agent-for-linux"></a>Linux용 에이전트 설치
다음 단계는 Azure 및 Azure Government 클라우드에서 Log Analytics에 대한 에이전트의 설정을 구성합니다.  

>[!NOTE]
>Linux용 OMS 에이전트는 여러 Log Analytics 작업 공간에 보고하도록 구성할 수 없습니다.  

Linux 컴퓨터에서 프록시 서버를 통해 Log Analytics에 통신해야 하는 경우 프록시 구성은 명령줄에 `-p [protocol://][user:password@]proxyhost[:port]`를 포함하여 지정할 수 있습니다.  *proxyhost* 속성은 프록시 서버의 정규화된 도메인 이름 또는 IP 주소를 허용합니다. 

예: `https://user01:password@proxy01.contoso.com:30443`

1. Linux 컴퓨터를 Log Analytics에 연결하도록 구성하려면 앞에서 복사한 작업 영역 ID 및 기본 키를 제공하는 다음 명령을 실행합니다.  다음 명령은 에이전트를 다운로드하고, 해당 체크섬의 유효성을 검사한 다음, 설치합니다. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    다음 명령에는 `-p` 프록시 매개 변수와 예제 구문이 포함됩니다.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Azure Government 클라우드에서 Linux 컴퓨터를 Log Analytics에 연결하도록 구성하려면 앞에서 복사한 작업 영역 ID 및 기본 키를 제공하는 다음 명령을 실행합니다.  다음 명령은 에이전트를 다운로드하고, 해당 체크섬의 유효성을 검사한 다음, 설치합니다. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    다음 명령에는 `-p` 프록시 매개 변수와 예제 구문이 포함됩니다.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. 다음 명령을 실행하여 에이전트를 다시 시작합니다. 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>이벤트 및 성능 데이터 수집
Log Analytics는 Linux Syslog에서 이벤트를 수집하고, 좀 더 긴 기간의 분석 및 보고를 위해 지정한 성능 카운터를 수집할 수 있으며 특정 조건이 검색되면 작업을 수행할 수 있습니다.  다음 단계에 따라 Linux Syslog의 수집과 시작할 몇 가지 일반 성능 카운터를 구성하세요.  

1. **Syslog**를 선택합니다.  
2. 로그 이름을 입력하여 이벤트 로그를 추가합니다.  **Syslog**를 입력하고 더하기 기호 **+** 를 클릭합니다.  
3. 표에서 심각도 **정보**, **알림** 및 **디버그**를 선택 취소합니다. 
4. 페이지 맨 위에서 **저장**을 클릭하여 구성을 저장합니다.
5. **Linux 성능 데이터**를 선택하여 Linux 컴퓨터의 성능 카운터 수집을 사용하도록 설정합니다. 
6. 새 Log Analytics 작업 영역에 대한 Linux 성능 카운터를 처음으로 구성하는 경우, 몇 가지 공용 카운터를 신속하게 만드는 옵션이 제공됩니다. 각 항목은 옆에 확인란과 함께 나열됩니다.<br><br> ![선택된 기본 Windows 성능 카운터](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png)<br> **선택한 성능 카운터 추가**를 클릭합니다.  해당 성능 카운터가 추가되고, 10초의 수집 샘플 간격으로 미리 설정됩니다.  
7. 페이지 맨 위에서 **저장**을 클릭하여 구성을 저장합니다.

## <a name="view-data-collected"></a>수집되는 데이터 보기
데이터 수집을 사용하도록 설정했으므로 대상 컴퓨터의 일부 데이터를 확인하는 간단한 로그 검색 예제를 실행해보겠습니다.  

1. Azure Portal에서 Log Analytics로 이동한 후 앞서 만든 작업 영역을 선택합니다.
2. **로그 검색** 타일을 클릭하고 로그 검색 창의 쿼리 필드에 `Perf`를 입력한 후 Enter 키를 누르거나 쿼리 필드의 오른쪽의 검색 단추를 클릭합니다.<br><br> ![Log Analytics 로그 검색 쿼리 예제](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> 예를 들어, 다음 그림의 쿼리는 735개의 성능 레코드를 반환했습니다.<br><br> ![Log Analytics 로그 검색 결과](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 Linux 컴퓨터에서 에이전트를 제거하고 Log Analytics 작업 영역을 삭제할 수 있습니다.  

에이전트를 제거하려면 Linux 컴퓨터에서 다음 명령을 실행합니다.  *--purge* 인수는 에이전트와 해당 구성을 완전히 제거합니다.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

작업 영역을 삭제하려면 앞서 만든 Log Analytics 작업 영역을 선택하고 리소스 페이지에서 **삭제**를 클릭합니다.<br><br> ![Log Analytics 리소스 삭제](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>다음 단계
온-프레미스 Linux 컴퓨터에서 운영 및 성능 데이터를 수집하도록 구성했으므로 이제 *무료*로 수집하는 데이터를 쉽게 탐색하고 분석하고 관련 작업을 수행할 수 있습니다.  

데이터를 보고 분석하는 방법을 알아보려면 자습서를 계속 진행합니다.   

> [!div class="nextstepaction"]
> [Log Analytics에서 데이터 보기 또는 분석](log-analytics-tutorial-viewdata.md)
