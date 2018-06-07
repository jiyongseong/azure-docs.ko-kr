---
title: Azure Migrate에서 그룹 종속성 매핑을 사용하여 평가 그룹 구체화 | Microsoft Docs
description: Azure Migrate 서비스에서 그룹 종속성 매핑을 사용하여 평가를 구체화하는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: 690231b938f60f16dcd10656b9a1f99c8c8f29cb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>그룹 종속성 매핑을 사용하여 그룹 구체화

이 문서에서는 그룹에 있는 모든 컴퓨터의 종속성을 시각화하여 그룹을 구체화하는 방법을 설명합니다. 일반적으로 이 방법은 평가를 실행하기 전에 그룹 종속성을 교차 확인하여 기존 그룹에 대한 멤버 자격을 구체화하려는 경우에 사용합니다. 종속성 시각화를 사용하여 그룹을 구체화하면 Azure로의 마이그레이션을 효과적으로 계획할 수 있습니다. 즉, 함께 마이그레이션해야 하는 상호 종속 시스템을 모두 검색할 수 있습니다. 따라서 Azure로 마이그레이션할 때 하나도 남겨두지 않고 전부 가져올 수 있으며 예기치 않은 중단이 발생하지 않습니다. 


> [!NOTE]
> 종속성을 시각화하려는 그룹에는 10개 이하의 컴퓨터만 포함되어 있어야 합니다. 그룹에 10개 이상의 컴퓨터가 있는 경우에는 작은 그룹으로 분할하여 종속성 시각화 기능을 활용하는 것이 좋습니다.


# <a name="prepare-the-group-for-dependency-visualization"></a>종속성 시각화를 위해 그룹 준비
그룹의 종속성을 보려면 그룹에 속한 각 온-프레미스 컴퓨터에 에이전트를 다운로드하여 설치해야 합니다. 또한 인터넷에 연결되지 않은 컴퓨터가 있으면 [OMS 게이트웨이](../log-analytics/log-analytics-oms-gateway.md)를 다운로드하여 설치해야 합니다.

### <a name="download-and-install-the-vm-agents"></a>VM 에이전트 다운로드 및 설치
1. **개요**에서 **관리** > **그룹**을 클릭하고 필요한 그룹으로 이동합니다.
2. 컴퓨터 목록의 **종속성 에이전트** 열에서 **설치 필요**를 클릭하여 에이전트를 다운로드하여 설치하는 방법과 관련된 지침을 확인합니다.
3. **종속성** 페이지에서 그룹에 속한 각 VM에 MMA(Microsoft Monitoring Agent) 및 종속성 에이전트를 다운로드하여 설치합니다.
4. 작업 영역 ID와 키를 복사합니다. 온-프레미스 컴퓨터에 MMA를 설치할 때 필요합니다.

### <a name="install-the-mma"></a>MMA 설치

Windows 컴퓨터에 에이전트를 설치하려면

1. 다운로드한 에이전트를 두 번 클릭합니다.
2. **Welcome** 페이지에서 **다음**을 클릭합니다. **사용 조건** 페이지에서 **동의함**을 클릭하여 라이선스에 동의합니다.
3. **대상 폴더**에서 기본 설치 폴더를 유지하거나 수정하고 **다음**을 클릭합니다. 
4. **에이전트 설치 옵션**에서 **Azure Log Analytics** > **다음**을 차례로 선택합니다. 
5. **추가**를 클릭하여 새로운 Log Analytics 작업 영역을 추가합니다. 포털에서 복사한 작업 영역 ID와 키를 붙여넣습니다. **다음**을 클릭합니다.


Linux 컴퓨터에 에이전트를 설치하려면

1. scp/sftp를 사용하여 Linux 컴퓨터로 해당 번들(x86 또는 x64)을 전송합니다.
2. --install 인수를 사용하여 번들을 설치합니다.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>종속성 에이전트 설치
1. Windows 컴퓨터에 종속성 에이전트를 설치하려면 설치 파일을 두 x 누르고 마법사를 따릅니다.
2. Linux 컴퓨터에 종속성 에이전트를 설치하려면 다음 명령을 사용하여 루트로 설치합니다.

    ```sh InstallDependencyAgent-Linux64.bin```

종속성 에이전트에서 지원하는 운영 체제에 대해 [자세히 알아보세요](../monitoring/monitoring-service-map-configure.md#supported-operating-systems). 

## <a name="refine-the-group-based-on-dependency-visualization"></a>종속성 시각화를 기반으로 그룹 구체화
그룹의 모든 시스템에 에이전트를 설치했으면 아래 단계에 따라 그룹의 종속성을 시각화하고 그룹을 구체화할 수 있습니다.

1. Azure Migrate 프로젝트의 **관리** 아래에서  **그룹**을 클릭하고 그룹을 선택합니다.
2. 그룹 페이지에서  **종속성 보기**를 클릭하여 그룹 종속성 맵을 엽니다.
3. 그룹의 종속성 맵은 다음과 같은 세부 정보를 보여 줍니다.
    - 그룹에 속한 모든 컴퓨터와 주고받는 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결
        - MMA 및 종속성 에이전트가 설치되지 않은 종속 컴퓨터는 포트 번호별로 그룹화됩니다.
        - MMA 및 종속성 에이전트가 설치된 종속 컴퓨터는 별도의 상자로 표시됩니다. 
    - 컴퓨터 내부에서 실행 중인 프로세스. 각 컴퓨터 상자를 확장하여 프로세스를 볼 수 있습니다.
    - 각 컴퓨터의 정규화된 도메인 이름, 운영 체제, MAC 주소 등과 같은 속성. 각 시스템 상자를 클릭하여 이러한 세부 정보를 볼 수 있습니다.

     ![그룹 종속성 보기](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. 더 세부적인 종속성을 보려면 시간 범위를 클릭하여 수정합니다. 범위는 기본적으로 1시간입니다. 시간 범위를 수정하거나 시작 및 종료 날짜와 기간을 지정할 수 있습니다.
4. 종속 컴퓨터와 각 컴퓨터 내에서 실행 중인 프로세스를 확인하고 그룹에서 추가하거나 제거할 컴퓨터를 식별합니다.
5. <Ctrl> 키를 누른 채로 클릭하여 맵에서 그룹에 추가하거나 제거할 컴퓨터를 선택합니다.
    - 검색된 컴퓨터만 추가할 수 있습니다.
    - 그룹에 컴퓨터를 추가하거나 제거하면 해당 그룹에 대한 이전의 평가가 무효화됩니다.
    - 그룹을 수정할 때 필요에 따라 새 평가를 만들 수 있습니다.
5. **확인**을 클릭하여 그룹을 저장합니다.

    ![컴퓨터 추가 또는 제거](./media/how-to-create-group-dependencies/add-remove.png)

그룹 종속성 맵에 표시되는 특정 컴퓨터의 종속성을 확인하려면 [컴퓨터 종속성 맵핑을 설정합니다](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>다음 단계

평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
