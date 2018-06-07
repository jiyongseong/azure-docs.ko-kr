---
title: "Azure Security Center 빠른 시작 - Security Center에 Linux 컴퓨터 온보딩 | Microsoft Docs"
description: "이 빠른 시작에서는 Security Center에 Linux 컴퓨터를 온보딩하는 방법을 보여 줍니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: 05e4bed0f9b4dfb6d1879408085447ef53db8655
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>빠른 시작: Azure Security Center에 Linux 컴퓨터 온보딩
Azure 구독을 온보딩한 후 Linux 에이전트를 프로비전하여 Azure 외부(예: 온-프레미스 또는 다른 클라우드)에서 실행되는 Linux 리소스에 Security Center를 사용하도록 설정할 수 있습니다.

이 빠른 시작에서는 Linux 컴퓨터에 Linux 에이전트를 설치하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건
Security Center를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)으로 등록할 수 있습니다.

이 빠른 시작을 시작하기 전에 Security Center의 표준 가격 책정 계층에 있어야 합니다. 업그레이드 지침은 [Security Center 표준에 Azure 구독 온보딩](security-center-get-started.md)을 참조하세요. 처음 60일 동안 추가 비용 없이 Security Center 표준을 사용해 볼 수 있습니다.

## <a name="add-new-linux-computer"></a>새 Linux 컴퓨터 추가

1. [Azure 포털](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.
2. **Microsoft Azure** 메뉴에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다.

 ![Security Center 개요][2]

3. Security Center 주 메뉴에서 **고급 보안으로 온보딩**을 선택합니다.
4. **비 Azure 컴퓨터를 추가할까요?**를 선택합니다.
   ![고급 보안으로 온보딩][3]

5. **새 비 Azure 컴퓨터 추가**에 Log Analytics 작업 영역 목록이 표시됩니다. 이 목록에는 자동 프로비저닝을 사용하는 경우 Security Center에서 자동으로 생성되는 기본 작업 영역이 포함됩니다(해당하는 경우). 이 작업 영역이나 사용할 다른 작업 영역을 선택합니다.

    ![비 Azure 컴퓨터 추가][4]

6.  **직접 에이전트** 페이지의 **Linux용 에이전트 다운로드 및 온보딩** 아래에서 **복사** 단추를 선택하여 *wget* 명령을 복사합니다.

7.  메모장을 열고 이 명령을 붙여넣습니다. Linux 컴퓨터에서 액세스할 수 있는 위치에 이 파일을 저장합니다.

## <a name="install-the-agent"></a>에이전트 설치

1.  Linux 컴퓨터에서 이전에 저장한 파일을 엽니다. 전체 내용을 선택하고 복사한 다음 터미널 콘솔을 열고 명령을 붙여넣습니다.
2.  설치가 완료되면 *pgrep* 명령을 실행하여 *omsagent*가 설치되어 있는지 확인할 수 있습니다. 이 명령은 다음과 같이 *omsagent* PID(프로세스 ID)를 반환합니다.

  ![에이전트 설치][5]

Linux용 Security Center 에이전트에 대한 로그는 */var/opt/microsoft/omsagent/<workspace id>/log/*에서 확인할 수 있습니다.

  ![에이전트에 대한 로그][6]

잠시 후에(최대 30분까지 걸릴 수 있음) 새 Linux 컴퓨터가 Security Center에 나타납니다.

이제 Azure VM과 비 Azure 컴퓨터를 한곳에서 모니터링할 수 있습니다. **계산** 아래에는 모든 VM 및 컴퓨터의 개요가 권장 사항과 함께 나와 있습니다. 각 열에 하나의 권장 사항 집합이 표시됩니다. 색은 해당 권장 사항에 대한 VM 또는 컴퓨터의 현재 보안 상태를 나타냅니다. Security Center는 보안 경고를 통해서도 이러한 컴퓨터에서 검색된 내용을 표시합니다.

  ![계산 블레이드][7] **계산** 블레이드에는 두 가지 유형의 아이콘이 표시됩니다.

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) 비 Azure 컴퓨터

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 Linux 컴퓨터에서 에이전트를 제거할 수 있습니다.

에이전트를 제거하려면

1. Linux 에이전트 [범용 스크립트](https://github.com/Microsoft/OMS-Agent-for-Linux/releases)를 컴퓨터에 다운로드합니다.
2. 컴퓨터에서 *--purge* 인수와 함께 번들 .sh 파일을 실행합니다. 그러면 에이전트와 해당 구성이 완전히 제거됩니다.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Linux 컴퓨터에 에이전트를 프로비전했습니다. Security Center를 사용하는 방법을 자세히 알아보려면 보안 정책 구성 및 리소스 보안 평가 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [자습서: 보안 정책 정의 및 평가](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
