---
title: 원격 모니터링 솔루션 배포 - Azure | Microsoft Docs
description: 이 자습서는 azureiotsuite.com에서 원격 모니터링 솔루션 가속기를 프로비전하는 방법을 보여줍니다.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 8738fbfc29f6776a091cdc2b872eab88abd195cd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>원격 모니터링 솔루션 가속기 배포

이 자습서는 원격 모니터링 솔루션 가속기를 프로비전하는 방법을 보여줍니다. azureiotsuite.com에서 솔루션을 배포합니다. CLI를 사용하여 솔루션을 배포할 수도 있습니다. 이 옵션에 대해 자세히 알아보려면 [명령줄에서 솔루션 가속기 배포](iot-accelerators-remote-monitoring-deploy-cli.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 솔루션 가속기 구성
> * 솔루션 가속기 배포
> * 솔루션 가속기에 로그인

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="deploy-the-solution-accelerator"></a>솔루션 가속기 배포

Azure 구독에 솔루션 가속기를 배포하기 전에 일부 구성 옵션을 선택해야 합니다.

1. Azure 계정 자격 증명을 사용하여 [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators)에 로그온합니다.

1. **원격 모니터링** 타일에서 **지금 시도**를 클릭합니다.

    ![원격 모니터링 선택](./media/iot-accelerators-remote-monitoring-deploy/remotemonitoring.png)

1. **원격 모니터링 솔루션 만들기** 페이지에서 원격 모니터링 솔루션 가속기에 **솔루션 이름**을 입력합니다.

1. **기본** 또는 **표준** 배포를 선택합니다. 작동 방법을 알아보거나 데모 실행을 위해 솔루션을 배포하는 경우 **기본** 옵션을 선택하여 비용을 최소화합니다.

1. 언어로 **Java** 또는 **.NET**을 선택합니다. Java 또는 .NET 구현으로 모든 마이크로 서비스가 제공됩니다.

1. 구성 선택에 대한 자세한 내용은 **솔루션 세부 정보** 패널을 검토합니다.

1. 솔루션을 프로비전하는 데 사용할 **구독** 및 **지역**을 선택합니다.

1. **솔루션 만들기** 를 클릭하여 프로비전 프로세스를 시작합니다. 일반적으로 이 프로세스는 실행하는 데 몇 분 정도 걸립니다.

    ![원격 모니터링 솔루션 세부 정보](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

문제 해결 정보는 GitHub 리포지토리에서 [배포에 실패한 경우 수행할 작업](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails)을 참조하세요.

## <a name="sign-in-to-the-solution-accelerator"></a>솔루션 가속기에 로그인

프로비전 프로세스가 완료되면 원격 모니터링 솔루션 가속기에 로그인할 수 있습니다.

1. **프로비전된 솔루션** 페이지에서 새 원격 모니터링 솔루션을 선택합니다.

    ![새 솔루션 선택](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. 표시되는 패널에서 원격 모니터링 솔루션에 대한 정보를 볼 수 있습니다. **솔루션 대시보드**를 선택하여 원격 모니터링 솔루션에 연결합니다.

    > [!NOTE]
    > 완료한 경우 이 패널에서 원격 모니터링 솔루션을 삭제할 수 있습니다.

    ![솔루션 패널](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. 원격 모니터링 솔루션 대시보드는 브라우저에 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 솔루션 가속기 구성
> * 솔루션 가속기 배포
> * 솔루션 가속기에 로그인

이제 원격 모니터링 솔루션을 배포했으며 다음 단계는 [솔루션 대시보드의 기능을 탐색](iot-accelerators-remote-monitoring-explore.md)하는 것입니다.

<!-- Next tutorials in the sequence -->