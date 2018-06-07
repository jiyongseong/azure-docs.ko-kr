---
title: Azure Migrate에서 컴퓨터 종속성을 사용하여 컴퓨터 그룹화 | Microsoft Docs
description: Azure Migrate 서비스에서 컴퓨터 종속성을 사용하여 평가를 만드는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: a9850044266ec05cee5e32c6825609bcf969351d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>컴퓨터 종속성 매핑을 사용하여 컴퓨터 그룹화

이 문서에서는 컴퓨터 종속성을 시각화하여 [Azure Migrate](migrate-overview.md) 평가를 위한 컴퓨터 그룹을 만드는 방법에 대해 설명합니다. 일반적으로 평가를 실행하기 전에 컴퓨터 종속성을 교차 확인하여 더 높은 수준의 신뢰도로 VM 그룹을 평가하려는 경우에 이 방법을 사용합니다. 종속성을 시각화화면 Azure로의 마이그레이션을 효과적으로 계획할 수 있습니다. Azure로 마이그레이션할 때 하나도 남겨두지 않고 전부 가져올 수 있으며 예기치 않은 중단이 발생하지 않습니다. 함께 마이그레이션해야 하는 모든 상호 종속적인 시스템을 검색하고, 실행 중인 시스템이 여전히 사용자에게 서비스 중인지 아니면 마이그레이션 대신 서비스 해제가 적합한지 여부를 식별할 수 있습니다. 


## <a name="prepare-machines-for-dependency-mapping"></a>종속성 매핑을 위한 컴퓨터 준비
컴퓨터의 종속성을 보려면 평가하려는 각 온-프레미스 컴퓨터에 에이전트를 다운로드하여 설치해야 합니다. 또한 인터넷에 연결되지 않은 컴퓨터가 있으면 [OMS 게이트웨이](../log-analytics/log-analytics-oms-gateway.md)를 다운로드하여 설치해야 합니다.

### <a name="download-and-install-the-vm-agents"></a>VM 에이전트 다운로드 및 설치
1. **개요**에서 **관리** > **컴퓨터**를 차례로 클릭하고 필요한 컴퓨터를 선택합니다.
2. **종속성** 열에서 **에이전트 설치**를 클릭합니다. 
3. **종속성** 페이지에서 평가하려는 각 VM에 MMA(Microsoft Monitoring Agent) 및 종속성 에이전트를 다운로드하여 설치합니다.
4. 작업 영역 ID와 키를 복사합니다. 이 옵션은 온-프레미스 컴퓨터에 MMA를 설치할 때 필요합니다.

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

## <a name="create-a-group"></a>그룹 만들기

1. 에이전트를 설치한 후에 포털로 이동하여 **관리** > **컴퓨터**를 차례로 클릭합니다.
2. 에이전트를 설치한 컴퓨터를 검색합니다.
3. 이제 컴퓨터의 **종속성** 열이 **종속성 보기**로 표시됩니다. 해당 열을 클릭하여 컴퓨터의 종속성을 봅니다.
4. 컴퓨터의 종속성 맵은 다음과 같은 세부 정보를 보여 줍니다.
    - 컴퓨터와 주고 받는 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결
        - MMA 및 종속성 에이전트가 설치되지 않은 종속 컴퓨터는 포트 번호별로 그룹화됩니다.
        - MMA 및 종속성 에이전트가 설치된 종속 컴퓨터는 별도의 상자로 표시됩니다. 
    - 컴퓨터 내부에서 실행 중인 프로세스. 각 컴퓨터 상자를 확장하여 프로세스를 볼 수 있습니다.
    - 각 컴퓨터의 정규화된 도메인 이름, 운영 체제, MAC 주소 등과 같은 속성. 각 시스템 상자를 클릭하여 이러한 세부 정보를 볼 수 있습니다.

 ![컴퓨터 종속성 보기](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. 시간 범위 레이블의 시간 범위를 클릭하여 여러 시간 범위의 종속성을 살펴볼 수 있습니다. 범위는 기본적으로 1시간입니다. 시간 범위를 수정하거나 시작 및 종료 날짜와 기간을 지정할 수 있습니다.
5. 그룹화하려는 종속 컴퓨터를 식별한 후 Ctrl 키를 누른 상태로 맵에서 여러 컴퓨터를 클릭하여 선택하고 **컴퓨터 그룹화**를 클릭합니다.
6. 그룹 이름을 지정합니다. Azure Migrate에서 종속 컴퓨터가 검색되는지 확인합니다. 

    > [!NOTE]
    > Azure Migrate에서 종속 컴퓨터가 검색되지 않으면 그룹에 추가할 수 없습니다. 종속 컴퓨터를 그룹을 추가하려면 vCenter Server에서 올바른 범위에 대해 검색 프로세스를 다시 실행하고 Azure Migrate에서 컴퓨터가 검색되는지 확인해야 합니다.  

7. 이 그룹에 대한 평가를 만들려면 그룹에 대한 새 평가를 만드는 확인란을 선택합니다.
8. **확인**을 클릭하여 그룹을 저장합니다.

그룹이 만들어지면 해당 그룹의 모든 컴퓨터에 에이전트를 설치하고 전체 그룹의 종속성을 시각화하여 그룹을 구체화하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- 그룹 종속성을 시각화하여 그룹을 구체화하는 [방법을 알아봅니다](how-to-create-group-dependencies.md).
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
