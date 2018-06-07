---
title: Azure IoT Central 응용 프로그램에 이미지 업로드 | Microsoft Docs
description: 작성자로서 이미지를 준비하여 Azure IoT Central 응용 프로그램에 업로드하는 방법을 알아봅니다.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: a43f2dd780604235ada1d8e3ae8a20563042fbaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200234"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>이미지를 준비하여 Azure IoT Central 응용 프로그램에 업로드

이 문서에서는 작성자로서 사용자 지정 이미지를 업로드하여 Microsoft Azure IoT Central 응용 프로그램을 사용자 지정하는 방법을 설명합니다. 예를 들어 장치 그림을 사용하여 장치 대시보드를 사용자 지정할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

1. Azure IoT Central 응용 프로그램. 자세한 내용은 [Azure IoT Central 응용 프로그램 만들기](howto-create-application.md)를 참조하세요.
1. 이미지 파일의 배율과 크기를 조정하는 데 사용되는 도구.

## <a name="choose-where-to-use-custom-images"></a>사용자 지정 이미지를 사용할 위치 선택

Azure IoT Central 응용 프로그램에서 다음 위치에 사용자 지정 이미지를 추가할 수 있습니다.

* **응용 프로그램 관리자** 페이지

    ![응용 프로그램 관리자 페이지의 이미지](media/howto-prepare-images/applicationmanager.png)

* 홈페이지

    ![홈페이지의 이미지](media/howto-prepare-images/homepage.png)

* 장치 템플릿

    ![장치 템플릿의 이미지](media/howto-prepare-images/devicetemplate.png)

* 장치 대시보드의 타일

    ![장치 타일의 이미지](media/howto-prepare-images/devicetile.png)

* 장치 집합 대시보드의 타일

    ![장치 집합 타일의 이미지](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>이미지 준비

네 위치 모두 PNG, GIF 또는 JPEG 이미지를 사용할 수 있습니다.

다음 표에는 사용할 수 있는 이미지 크기가 요약되어 있습니다.

| 위치 | 크기 |
| -------- | ------ |
| **응용 프로그램 관리자** | 268x160픽셀 |
| 장치 템플릿 | 64x64픽셀 |
| 홈페이지 및 대시보드 타일 | 최소 타일 크기는 200x200픽셀이고, 작은 타일 여러 개를 정사각형 또는 직사각형 모양으로 이어 붙여서 큰 파일을 만들 수 있습니다. 예를 들어 200x400픽셀, 400x200픽셀 또는 400x400픽셀 타일을 만들 수 있습니다. |

응용 프로그램에서 최상의 디스플레이를 원한다면 앞의 표에 정리된 치수와 일치하는 이미지를 만들어야 합니다.

## <a name="upload-the-images"></a>이미지 업로드

다음 섹션에서는 여러 위치에 사용할 이미지를 업로드하는 방법을 설명합니다.

### <a name="application-manager"></a>응용 프로그램 관리자

**응용 프로그램 관리자**에 사용할 이미지를 업로드하려면 **관리** 섹션에서 **응용 프로그램 설정** 페이지로 이동합니다. 이 작업을 완료하려면 관리자여야 합니다.

![응용 프로그램 이미지 업로드](media/howto-prepare-images/uploadapplicationmanager.png)

[이미지 업로드]를 클릭하고 로컬 컴퓨터에서 업로드할 파일을 선택합니다.

### <a name="home-page"></a>홈 페이지

홈페이지에 사용할 이미지를 업로드하려면 응용 프로그램의 **홈페이지**로 이동하여 디자인 모드를 켭니다. 이 작업을 완료하려면 작성자여야 합니다.

![홈페이지 이미지 업로드](media/howto-prepare-images/uploadhomepage.png)

[이미지 업로드]를 클릭하고 로컬 컴퓨터에서 업로드할 파일을 선택합니다.

이미지를 업로드한 후 디자인 모드가 켜져 있는 동안 이미지 크기를 조정할 수 있습니다.

### <a name="device-template"></a>장치 템플릿

장치 템플릿에 사용할 이미지를 업로드하려면 **Device Explorer**로 이동하여 장치 템플릿을 선택하고, 장치를 선택하고, 디자인 모드를 켭니다. 이 작업을 완료하려면 작성자여야 합니다.

![장치 템플릿 이미지 업로드](media/howto-prepare-images/uploaddevicetemplate.png)

[이미지 업로드]를 클릭하고 로컬 컴퓨터에서 업로드할 파일을 선택합니다.

### <a name="device-dashboard"></a>장치 대시보드

장치 대시보드에 사용할 이미지를 업로드하려면 **Device Explorer**로 이동하여 장치 템플릿을 선택하고, 장치를 선택합니다. **대시보드** 페이지를 선택하고 디자인 모드를 켭니다. 이 작업을 완료하려면 작성자여야 합니다.

![장치 대시보드 이미지 업로드](media/howto-prepare-images/uploaddevicedashboard.png)

[이미지 업로드]를 클릭하고 로컬 컴퓨터에서 업로드할 파일을 선택합니다.

이미지를 업로드한 후 **디자인 모드**가 켜져 있는 동안 이미지 크기를 조정하고 위치를 변경할 수 있습니다.

### <a name="device-set-dashboard"></a>장치 집합 대시보드

장치 집합 대시보드에 사용할 이미지를 업로드하려면 **장치 집합**으로 이동하여 장치 집합을 선택하고, 장치를 선택합니다. **대시보드** 페이지를 선택하고 **디자인 모드**를 켭니다.

![장치 집합 대시보드 이미지 업로드](media/howto-prepare-images/uploaddevicesetdashboard.png)

[이미지 업로드]를 클릭하고 로컬 컴퓨터에서 업로드할 파일을 선택합니다.

이미지를 업로드한 후 디자인 모드가 켜져 있는 동안 이미지 크기를 조정하고 위치를 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이미지를 준비하여 Azure IoT Central 응용 프로그램에 업로드하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [Azure IoT Central 응용 프로그램에서 장치 관리](howto-manage-devices.md)