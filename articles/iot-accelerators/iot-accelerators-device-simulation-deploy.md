---
title: 장치 시뮬레이션 솔루션 배포 - Azure | Microsoft Docs
description: 이 자습서는 azureiotsuite.com에서 장치 시뮬레이션 솔루션을 프로비전하는 방법을 보여 줍니다.
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 4d468c527c658707da2b5f35f43676626baf5ca2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>Azure IoT 장치 시뮬레이션 솔루션 배포

이 자습서는 장치 시뮬레이션 솔루션을 프로비전하는 방법을 보여 줍니다. azureiotsuite.com에서 솔루션을 배포합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 장치 시뮬레이션 솔루션 구성
> * 장치 시뮬레이션 솔루션 배포
> * 장치 시뮬레이션 솔루션에 로그인

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="deploy-the-solution"></a>솔루션 배포

Azure 구독에 솔루션을 배포하기 전에 일부 구성 옵션을 선택해야 합니다.

1. Azure 계정 자격 증명을 사용하여 [azureiotsuite.com](https://www.azureiotsuite.com)에 로그인한 다음 **+** 를 클릭하여 새 솔루션을 만듭니다.

    ![새 솔루션 만들기](./media/iot-accelerators-device-simulation-deploy/createnewsolution.png)

1. **장치 시뮬레이션** 타일에서 **선택**을 클릭합니다.

    ![장치 시뮬레이션 선택](./media/iot-accelerators-device-simulation-deploy/select.png)

1. **장치 시뮬레이션 솔루션 만들기** 페이지에서 장치 시뮬레이션 솔루션의 **솔루션 이름**을 입력합니다.

1. 솔루션을 프로비전하는 데 사용할 **구독** 및 **지역**을 선택합니다.

1. 장치 시뮬레이션 솔루션과 함께 새 IoT Hub를 배포할 것인지를 지정합니다. 이 확인란을 선택하는 경우 새 IoT Hub가 구독에 배포됩니다. 선택에 관계없이, 항상 시뮬레이션으로 어떤 IoT Hub도 가리킬 수 있습니다.

1. **솔루션 만들기** 를 클릭하여 프로비전 프로세스를 시작합니다. 일반적으로 이 프로세스는 실행하는 데 몇 분 정도 걸립니다.

    ![장치 시뮬레이션 솔루션 세부 정보](./media/iot-accelerators-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>솔루션에 로그인

프로비전 프로세스가 완료되면 장치 시뮬레이션 솔루션에 로그인할 수 있습니다.

1. **프로비전된 솔루션** 페이지의 새 장치 시뮬레이션 솔루션에서 **시작**을 클릭합니다.

    ![새 솔루션 선택](./media/iot-accelerators-device-simulation-deploy/newsolution.png)

1. 표시되는 패널에서 장치 시뮬레이션 솔루션에 대한 정보를 볼 수 있습니다. **솔루션 대시보드**를 선택하여 장치 시뮬레이션 솔루션에 연결합니다.

    > [!NOTE]
    > 작업이 끝나면 이 패널에서 장치 시뮬레이션 솔루션을 삭제할 수 있습니다.

    ![솔루션 패널](./media/iot-accelerators-device-simulation-deploy/properties.png)

1. 장치 시뮬레이션 솔루션 대시보드는 브라우저에 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 솔루션 구성
> * 솔루션 배포
> * 솔루션에 로그인

이제 장치 시뮬레이션 솔루션을 배포했으며 다음 단계는 [장치 시뮬레이션 솔루션의 기능을 탐색](iot-accelerators-device-simulation-explore.md)하는 것입니다.

<!-- Next tutorials in the sequence -->
