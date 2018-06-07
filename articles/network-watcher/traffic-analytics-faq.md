---
title: Azure 트래픽 분석 질문과 대답 | Microsoft Docs
description: 트래픽 분석에 대해 자주 묻는 질문에 대한 대답을 알아봅니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 3ab06b624d1e433641d190d9621592ef83df3344
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="traffic-analytics-frequently-asked-questions"></a>트래픽 분석 질문과 대답

1.  트래픽 분석을 사용하기 위한 필수 조건은 무엇인가요?

    트래픽 분석을 사용하려면 다음 필수 구성 요소가 필요합니다.

    - Network Watcher가 설정된 구독
    - 모니터링할 NSG에 대해 설정된 NSG 흐름 로그
    - 원시 흐름 로그를 저장할 Azure Storage 계정
    - Log Analytics(OMS) 작업 영역과 읽기 및 쓰기 권한
    - 계정은 Microsoft.Network 공급자에서 다음 작업을 할당받습니다.

        - Microsoft.Network/applicationGateways/read
        - Microsoft.Network/connections/read
        - Microsoft.Network/loadBalancers/read 
        - Microsoft.Network/localNetworkGateways/read 
        - Microsoft.Network/networkInterfaces/read 
        - Microsoft.Network/networkSecurityGroups/read 
        - Microsoft.Network/publicIPAddresses/read
        - Microsoft.Network/routeTables/read
        - Microsoft.Network/virtualNetworkGateways/read 
        - Microsoft.Network/virtualNetworks/read

2.  트래픽 분석을 사용할 수 있는 Azure 지역은 어디인가요?

    미리 보기 릴리스 기간에는 미국 중서부, 미국 동부, 미국 동부 2, 미국 중북부, 미국 중남부, 미국 중부, 미국 서, 미국 서부 2, 유럽 서부, 북유럽, 영국 서부, 영국 남부, 오스트레일리아 동부 및 오스트레일리아 남동부 등 **지원되는 지역**에서 NSG용 트래픽 분석을 사용할 수 있습니다. Log Analytics 작업 영역이 미국 중서부, 미국 동부, 유럽 서부, 오스트레일리아 남동부 또는 영국 남부 지역에 있어야 합니다.

3.  흐름 로그를 설정하려는 NSG가 OMS 작업 영역과 다른 지역에 있어도 되나요?

    예

4.  단일 작업 영역 내에서 여러 NSG를 구성할 수 있나요?

    예

5.  기존 OMS 작업 영역을 사용해도 되나요?

    예, 기존 작업 영역을 선택하는 경우 새 쿼리 언어로 마이그레이션되었는지 확인해야 합니다. 작업 영역을 업그레이드하지 않으려면 새 작업 영역을 만들어야 합니다. 새 쿼리 언어에 대한 자세한 내용은 [새 로그 검색으로 Azure Log Analytics 업그레이드](../log-analytics/log-analytics-log-search-upgrade.md)를 참조하세요.

6.  Azure Storage 계정과 OMS 작업 영역이 서로 다른 구독에 있어도 되나요?

    예

7.  원시 로그를 다른 구독의 다른 저장소 계정에 저장해도 되나요?

    번호 흐름 로그에 대해 NSG가 설정된 아무 저장소 계정에 원시 로그를 저장할 수 있지만, 저장소 계정과 원시 로그가 같은 구독, 같은 지역에 있어야 합니다.

8.  트래픽 분석에 대해 NSG를 구성하는 동안 "찾을 수 없음" 오류 메시지가 표시된 경우 어떻게 해결해야 하나요?

    질문 2에 나열된 지원되는 지역을 선택합니다. 지원되지 않는 영역을 선택할 경우 "찾을 수 없음" 오류가 표시됩니다.

9.  NSG 흐름 로그 아래에서 NSG 상태가 "로드하지 못함"으로 표시됩니다. 어떻게 해야 하나요?

    흐름 로깅이 성공적으로 작동하려면 Microsoft.Insights 공급자를 등록해야 합니다. Microsoft.Insights 공급자가 등록되었는지 또는 내 구독에 대해 등록된 것인지 확실하지 않은 경우 다음 명령에서 *xxxxx xxxxxx xxxx xxxxx*를 바꾼 후 PowerShell에서 다음 명령을 실행합니다.

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. 솔루션을 구성했습니다. 그런데 왜 대시보드에 아무 것도 표시되지 않나요?

    대시보드에 처음으로 항목이 표시될 때까지 최대 30분이 걸릴 수 있습니다. 솔루션에서 의미 있는 정보를 얻는 데 필요한 데이터를 충분히 수집한 후에야 보고서가 생성됩니다. 

11.  "이 작업 영역에서 선택한 시간 간격의 데이터를 찾을 수 없습니다. 시간 간격을 변경하거나 다른 작업 영역을 선택하십시오." 메시지가 표시되면 어떻게 해야 하나요?

        다음 옵션을 시도해 보세요.
        - 위쪽 메뉴에서 시간 간격을 변경해 봅니다.
        - 위쪽 메뉴에서 다른 Log Analytics 작업 영역을 선택합니다.
        - 트래픽 분석이 최근에 설정된 경우 30분 후에 액세스해 봅니다.
    
        그래도 문제가 계속되면 [사용자 의견 포럼](https://feedback.azure.com/forums/217313-networking?category_id=195844)에 문제를 올려주세요.

12.  “1) NSG 흐름 로그를 처음으로 분석하는 중입니다. 이 프로세스를 완료하는 데 20-30분 정도 걸릴 수 있습니다. 잠시 후 다시 확인하십시오. 2) 위의 단계가 작동하지 않거나 작업 영역이 무료 SKU에 소속된 경우 여기서 작업 영역 사용량을 확인하여 할당량과 비교해 보십시오. 추가 정보가 필요하면 FAQ를 참조하십시오." 메시지가 표시됩니다. 어떻게 해야 하나요?

        다음과 같은 이유로 이 오류가 나타날 수 있습니다.
        - 트래픽 분석이 최근에 설정되었으며 의미 있는 정보를 얻는 데 필요한 데이터를 아직 충분히 수집하지 못했을 수 있습니다.
        - OMS 작업 영역이 무료 SKU에 속해 있으며 할당량 제한에 도달했습니다. 이 경우 더 큰 용량의 SKU에 속한 작업 영역을 사용해야 합니다.
    
        그래도 문제가 계속되면 [사용자 의견 포럼](https://feedback.azure.com/forums/217313-networking?category_id=195844)에 문제를 올려주세요.
    
13.  “리소스 데이터(토폴로지)는 있는데 흐름 정보가 표시되지 않습니다. 리소스 데이터를 보려면 여기를 클릭하고 추가 정보가 필요하면 FAQ를 참조하십시오.” 메시지가 표시됩니다. 어떻게 해야 하나요?

        대시보드에서 리소스 정보를 보고 있지만 흐름 관련 통계가 없습니다. 리소스 간 통신이 없어 데이터가 없는 것일 수 있습니다. 60분 후에 상태를 다시 확인하십시오. 리소스 간에 통신 흐름이 있다고 확신하는 경우 [사용자 의견 포럼](https://feedback.azure.com/forums/217313-networking?category_id=195844)에 문제를 올려주세요.

14. PowerShell 또는 Azure Resource Manager 템플릿을 사용하여 트래픽 분석을 구성할 수 있나요?

    아니요, 트래픽 분석은 Azure Portal을 통해서만 구성할 수 있습니다.

15.  트래픽 분석의 비용은 얼마인가요?

        트래픽 분석은 축소된 로그를 향상시키고 Log Analytics 작업 영역에서 향상된 로그를 저장하기 위해 요금이 측정됩니다. 미리 보기 상태에서 축소된 로그를 향상시키기 위해 트래픽 분석의 요금이 부과되지 않습니다. 하지만 작업 영역에서 데이터의 보존은 게시된 요금제로 청구됩니다. 트래픽 분석에 가격 책정을 사용할 수 있게 되면 이 응답이 업데이트됩니다.

16.  지역 지도 보기에서 키보드를 사용하여 탐색할 수 있나요?

        지역 지도 페이지에는 두 개의 기본 섹션이 있습니다.
    
        - **배너**: 지역 지도의 맨 위에 있는 배너는 배포/배포 없음/활성/비활성/트래픽 분석 사용/트래픽 분석 사용 안 함/국가의 트래픽/무해/악성/허용되는 악의적인 트래픽, 범례 정보 같은 단추를 통해 트래픽 분포 필터를 선택할 수 있는 기능을 제공합니다. 정의된 단추를 선택하면 각 필터가 지도에 적용됩니다. 예를 들어 사용자가 배너 아래에서 "활성" 필터 단추를 선택하면 지도에서 배포의 "활성" 데이터 센터가 강조 표시됩니다.
        - **지도**: 배너 아래의 지도 섹션에는 Azure 데이터 센터와 국가 간의 트래픽 분포가 표시됩니다.
    
        **배너에서 키보드 탐색**
    
        - 기본적으로 배너에 대한 지역 지도 페이지에서 선택할 수 있는 항목은 "Azure DC" 필터 단추입니다.
        - 다른 필터 단추로 이동하려면 `Tab` 또는 `Right arrow` 키를 사용하여 다음 항목으로 이동하면 됩니다. 뒤로 이동하려면 `Shift+Tab` 또는 `Left arrow` 키를 사용합니다. 전방 탐색 방향의 우선 순위는 왼쪽에서 오른쪽, 그리고 위쪽에서 아래쪽입니다.
        - `Enter` 또는 `Down` 화살표 키를 누르면 선택한 필터가 적용됩니다. 선택하는 필터와 배포에 따라 지도 섹션 아래에 있는 하나 또는 여러 노드가 강조 표시됩니다.
            - **배너** 및 **지도** 사이에서 전환하려면 `Ctrl+F6` 키를 누릅니다.
        
        **지도에서 키보드 탐색**
    
        - 배너에서 필터를 선택하고 `Ctrl+F6` 키를 누르면 지도 보기에서 강조 표시된 노드 중 하나(**Azure 데이터 센터** 또는 **국가/지역**)로 포커스가 이동됩니다.
        - 지도에서 강조 표시된 다른 노드로 이동하려면 `Tab` 또는 `Right arrow` 키를 사용하여 앞으로 이동하거나 `Shift+Tab` 또는 `Left arrow` 키를 사용하여 뒤로 이동할 수 있습니다.
        - 지도에서 강조 표시된 노드를 선택하려면 `Enter` 또는 `Down arrow` 키를 사용합니다.
        - 노드를 선택하면 해당 노드의 **정보 도구 상자**로 포커스가 이동됩니다. 기본적으로 포커스는 **정보 도구 상자**의 닫힌 단추로 이동합니다. **상자** 보기 내부를 추가로 탐색하려면 `Right` 및 `Left arrow` 키를 사용하여 앞뒤로 이동할 수 있습니다. `Enter` 키를 누르면 **정보 도구 상자**에서 포커스가 있는 단추를 선택한 것과 동일한 효과가 적용됩니다.
        - **정보 도구 상자**에 포커스가 있는 동안 `Tab` 키를 누르면 선택된 노드와 동일한 대륙의 엔드포인트로 포커스가 이동합니다. `Right` 및 `Left arrow` 키를 사용하여 이러한 엔드포인트를 탐색할 수 있습니다.
        - 다른 흐름 엔드포인트/대륙 클러스터를 탐색하려면 `Tab` 키를 사용하여 앞으로 이동하고 `Shift+Tab` 키를 사용하여 뒤로 이동할 수 있습니다.
        - `Continent clusters`에 포커스가 있을 때 `Enter` 또는 `Down` 화살표 키를 사용하여 대륙 클러스터 내부의 엔드포인트를 강조 표시할 수 있습니다. 대륙 클러스터의 정보 상자에서 엔드포인트와 닫기 단추를 탐색하려면 `Right` 또는 `Left arrow` 키를 사용하여 앞뒤로 이동할 수 있습니다. 아무 엔드포인트에서 `Shift+L` 키를 사용하여 선택한 노드와 엔드포인트를 잇는 연결선으로 전환할 수 있습니다. `Shift+L` 키를 다시 누르면 선택한 엔드포인트로 이동됩니다.
        
        모든 단계에서:
    
        - `ESC` 키는 확장된 선택 영역을 축소합니다.
        - `UP Arrow` 키는 `ESC` 키와 동일한 작업을 수행합니다. `Down arrow` 키는 `Enter` 키와 동일한 작업을 수행합니다.
        - `Shift+Plus` 키로 확대, `Shift+Minus` 키로 축소할 수 있습니다.
