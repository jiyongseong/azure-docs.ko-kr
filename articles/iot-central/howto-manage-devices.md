---
title: Azure IoT Central 응용 프로그램에서 장치 관리 | Microsoft Docs
description: 운영자로서 Azure IoT Central 응용 프로그램에서 장치를 관리하는 방법을 알아봅니다.
services: iot-central
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 75472d701160e7cfd331d01efcdc1a19ae20fb2d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303582"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램에서 장치 관리

이 문서에서는 운영자로서 Microsoft Azure IoT Central 응용 프로그램에서 장치를 관리하는 방법을 설명합니다. 운영자로서 다음과 같은 일을 할 수 있습니다.

- **Explorer** 페이지를 사용하여 장치를 살펴보고, Azure IoT Central 응용 프로그램에 연결하고, 삭제합니다.
- 장치 인벤토리를 최신 상태로 유지합니다.
- 장치 속성에 저장된 값을 변경하여 장치 메타데이터를 최신 상태로 유지합니다.
- **설정** 페이지에서 특정 장치의 설정을 업데이트하여 장치 동작을 제어합니다.

## <a name="view-your-devices"></a>장치 보기

개별 장치를 보려면:

1. 왼쪽 탐색 메뉴에서 **Explorer**를 선택합니다. 여기서 [장치 템플릿](howto-set-up-template.md) 목록을 볼 수 있습니다.

1. 왼쪽 창에서 **장치 템플릿**을 선택합니다.

1. 이 장치 템플릿으로 만든 장치 목록이 오른쪽 창에 표시됩니다. 개별 장치를 선택하면 해당 장치의 **장치 세부 정보** 페이지를 볼 수 있습니다.

    [![장치 세부 정보 페이지](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>장치 추가

Azure IoT Central 응용 프로그램에 장치를 추가하려면:

1. 왼쪽 탐색 메뉴에서 **Explorer**를 선택합니다.

1. 장치를 만들 장치 템플릿을 선택합니다.

1. + **새로 만들기**를 선택합니다.

1. **실제 장치** 또는 **시뮬레이션된 장치**를 선택합니다. 실제 장치는 Azure IoT Central 응용 프로그램에 연결하는 물리적 장치입니다. 시뮬레이션된 장치에는 Azure IoT Central에서 생성한 샘플 데이터가 있습니다. 이 예제에서는 실제 장치를 사용합니다. **실제 장치**를 선택하여 새 장치의 **장치 세부 정보** 페이지로 이동합니다.


## <a name="bulk-import-devices"></a>장치 대량 가져오기

대량의 장치를 응용 프로그램에 연결할 수 있도록 Azure IoT Central은 CSV 파일을 통해 장치 대량 가져오기 기능을 제공합니다. 

CSV 파일 요구 사항:
1. CSV 파일에는 장치 ID를 포함하고 있는 열 하나만 있어야 합니다.

1. 파일에 헤더가 없어야 합니다.


장치를 응용 프로그램에 대량으로 등록하려면:

1. 왼쪽 탐색 메뉴에서 **Explorer**를 선택합니다.

1. 왼쪽 패널에서 장치를 대량으로 만들 장치 템플릿을 선택합니다.

1. **새로 만들기**를 선택하고 **대량 가져오기**를 선택합니다.

    [![대량 가져오기 작업](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. 가져올 장치 ID 목록이 들어 있는 CSV 파일을 선택합니다.

1. 파일이 업로드되면 장치 가져오기가 시작됩니다. 장치 그리드 위쪽에서 가져오기 상태를 추적할 수 있습니다.

1. 가져오기가 완료되면 장치 그리드에 성공 메시지가 표시됩니다.

    [![대량 가져오기 성공](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

장치 가져오기 작업이 실패하면 장치 그리드에 오류가 표시됩니다. 모든 오류를 캡처하는 로그 파일이 생성되며 오류 메시지를 클릭하여 다운로드할 수 있습니다.



## <a name="delete-a-device"></a>장치 삭제

Azure IoT Central 응용 프로그램에서 실제 장치 또는 시뮬레이션된 장치를 삭제하려면:

1. 탐색 메뉴에서 **Explorer**를 선택합니다.

1. 삭제할 장치의 장치 템플릿을 선택합니다.

1. 삭제할 장치 옆에 있는 확인란을 선택합니다.

1. **삭제**를 선택합니다.

## <a name="change-a-device-setting"></a>장치 설정 변경

설정은 장치의 동작을 제어합니다. 다시 말해서, 설정을 통해 장치에 입력을 제공할 수 있습니다. **장치 세부 정보** 페이지에서 장치 설정을 살펴보고 업데이트할 수 있습니다.

1. 탐색 메뉴에서 **Explorer**를 선택합니다.

1. 설정을 변경하려는 장치의 장치 템플릿을 선택합니다.

1. **설정** 탭을 선택합니다. 장치에 있는 모든 설정과 현재 값이 표시됩니다. 각 설정에 대해 장치가 여전히 동기화 중인지 확인할 수 있습니다.

1. 원하는 값으로 설정을 수정합니다. 한 번에 여러 설정을 수정하고 동시에 업데이트할 수 있습니다.

1. **업데이트**를 선택합니다. 값이 장치로 전송됩니다. 장치에서 설정 변경을 확인하면 설정 상태가 **동기화됨**으로 돌아갑니다.

## <a name="change-a-property"></a>속성 변경

속성은 도시나 일련 번호처럼 장치와 연결된 장치 메타데이터입니다. **장치 세부 정보** 페이지에서 속성을 살펴보고 업데이트할 수 있습니다.

1. 탐색 메뉴에서 **Explorer**를 선택합니다.

1. 속성을 변경하려는 장치의 장치 템플릿을 선택합니다.

1. **속성** 탭을 선택하면 모든 속성이 표시됩니다.

1. 원하는 값으로 속성을 수정합니다. 한 번에 여러 속성을 수정하고 동시에 업데이트할 수 있습니다.

1. **업데이트**를 선택합니다.

> [!NOTE]
> _장치 속성_의 값은 변경할 수 없습니다 장치 속성은 장치에서 설정하며 Azure IoT Central 응용 프로그램 내에서 읽기 전용입니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 장치를 관리하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [장치 집합을 사용하는 방법](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
