---
title: Emulator Express를 사용하여 Azure 클라우드 서비스를 로컬 컴퓨터에서 실행 및 디버그 | Microsoft Docs
description: Emulator Express를 사용하여 클라우드 서비스를 로컬 컴퓨터에서 실행 및 디버그
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: mikejo
ms.openlocfilehash: 638aa005321963ce8bdcbb7df4317fc4bdec74ae
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Emulator Express를 사용하여 로컬 컴퓨터에서 Azure 클라우드 서비스 실행 및 디버그
Emulator Express를 사용하여 관리자로 Visual Studio를 실행하지 않고 클라우드 서비스를 테스트 및 디버그할 수 있습니다. 클라우드 서비스의 요구 사항에 따라 Emulator Express 또는 전체 에뮬레이터를 사용하도록 프로젝트를 설정할 수 있습니다. 전체 에뮬레이터에 대한 자세한 내용은 [Compute 에뮬레이터에서 Azure 응용 프로그램 실행](storage/common/storage-use-emulator.md)을 참조하세요.

## <a name="using-emulator-express-in-visual-studio"></a>Visual Studio에서 Emulator Express 사용
Azure SDK 2.3 이상에서 Azure 프로젝트를 만들 때 Emulator Express가 자동으로 선택됩니다. 이전 버전의 Azure SDK를 사용하여 만든 기존 프로젝트의 경우 다음 단계에 따라 Emulator Express를 선택합니다.

1. Visual Studio에서 Azure 클라우드 서비스 프로젝트를 만들거나 엽니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, 상황에 맞는 메뉴에서 **속성**을 선택합니다.

1. 프로젝트 속성 페이지에서 **웹** 탭을 선택합니다.

    ![Azure 클라우드 서비스 프로젝트의 속성](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. **로컬 개발 서버** 아래에서 **IIS Express 사용 옵션**을 선택합니다.

1. **에뮬레이터** 아래에서 **Emulator Express 사용**을 선택합니다.
   
1. Emulator Express를 시작하려면 명령 프롬프트에서 다음 명령을 실행합니다. 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Emulator Express 제한 사항
다음 문제는 Emulator Express의 알려진 제한 사항입니다. 

- Emulator Express는 IIS 웹 서버와 호환되지 않습니다.
- 클라우드 서비스는 여러 역할을 포함할 수 있지만 각 역할은 하나의 인스턴스로 제한됩니다.
- 1000 아래의 포트 번호에 액세스할 수 없습니다. 일반적으로 1000 아래의 포트를 사용하는 인증 공급자를 사용하는 경우 1000 위의 포트 번호로 이 값을 변경해야 할 수도 있습니다.
- Azure Compute 에뮬레이터에 적용되는 모든 제한 사항은 Emulator Express에도 적용됩니다. 예를 들어 배포당 50개 이상의 역할 인스턴스를 가질 수 없습니다. Azure Compute 에뮬레이터에 대한 자세한 내용은 [계산 에뮬레이터에서 Azure 응용 프로그램 실행](http://go.microsoft.com/fwlink/p/?LinkId=623050)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Cloud Services 디버그](https://msdn.microsoft.com/library/azure/ee405479.aspx)
