---
title: Azure IoT Central에서 새 장치 유형 정의 | Microsoft Docs
description: 이 자습서에서는 작성기로서 Azure IoT Central 응용 프로그램에서 새 장치 유형을 정의하는 방법을 알려줍니다. 유형에 대한 원격 분석, 상태, 속성 및 설정을 정의합니다.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: e1488b708bbbee67362d834a9a703520d37bef37
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201675"
---
# <a name="1---define-a-new-device-type-in-your-azure-iot-central-application"></a>1 - Azure IoT Central 응용 프로그램에서 새 장치 유형 정의

이 자습서에서는 작성기로서 Microsoft Azure IoT Central 응용 프로그램에서 새 장치 유형을 정의하기 위해 장치 템플릿을 사용하는 방법을 알려줍니다. 장치 템플릿은 장치 유형에 대해 원격 분석, 상태, 속성 및 설정을 정의합니다.

실제 장치를 연결하기 전에 응용 프로그램을 테스트할 수 있으려면 장치를 만들 경우 Azure IoT Central이 장치 템플릿에서 시뮬레이션된 장치를 생성합니다.

이 자습서에서는 **연결된 공조 장치** 템플릿을 만듭니다. 연결된 공조 장치:

* 온도 및 습도 같은 원격 분석을 보냅니다.
* 설정 또는 해제했는지 같은 상태를 보고합니다.
* 펌웨어 버전 및 일련 번호 같은 속성이 있습니다.
* 대상 온도 및 팬 속도 같은 설정이 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 새 장치 템플릿 만들기
> * 장치에 원격 분석 추가
> * 시뮬레이션된 원격 분석 보기
> * 이벤트 측정 정의
> * 시뮬레이션된 이벤트 보기
> * 상태 측정 정의
> * 시뮬레이션된 상태 보기
> * 장치 속성 사용
> * 장치 설정 사용

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Azure IoT Central 응용 프로그램이 필요합니다. [Azure IoT Central 응용 프로그램](quick-deploy-iot-central.md) 빠른 시작을 완료한 경우 빠른 시작에서 만든 응용 프로그램을 다시 사용할 수 있습니다. 그렇지 않은 경우 빈 Azure IoT Central 응용 프로그램을 만들려면 다음 단계를 완료합니다.

1. Azure IoT Central [응용 프로그램 관리자](https://aka.ms/iotcentral) 페이지로 이동합니다.

1. Azure 구독에 액세스 하는 데 사용하는 이메일 주소와 암호를 입력합니다.

   ![조직 계정 입력](media/tutorial-define-device-type/sign-in.png)

1. 새로운 Azure IoT Central 응용 프로그램 만들기를 시작하려면 **새 응용 프로그램**을 선택합니다.

    ![Azure IoT Central 응용 프로그램 관리자 페이지](media/tutorial-define-device-type/iotcentralhome.png)

1. 새로운 Azure IoT Central 응용 프로그램을 만들려면:

    1. **Contoso 공조 장치** 같은 친숙한 응용 프로그램 이름을 선택합니다. Azure IoT Central은 사용자를 위해 고유한 URL 접두사를 생성합니다. 이 URL 접두사를 더욱 기억하기 쉬운 것으로 변경할 수 있습니다.
    1. 사용할 Azure Active Directory 및 Azure 구독을 선택합니다. 디렉터리 및 구독에 대한 자세한 내용은 [Azure IoT Central 응용 프로그램 만들기](howto-create-application.md)를 참조합니다.
    1. 선택한 이름으로 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 예: **contoso-rg**.
    1. 지리적으로 가장 가까운 지역을 선택합니다.
    1. **사용자 지정 응용 프로그램** 템플릿을 선택합니다.
    1. **무료 30일 평가판 응용 프로그램** 결제 계획을 선택합니다.
    1. 그런 다음 **만들기**를 선택합니다.

    ![Azure IoT Central 응용 프로그램 페이지](media/tutorial-define-device-type/iotcentralcreate.png)

자세한 내용은 [Azure IoT Central 응용 프로그램 만드는 방법](howto-create-application.md)을 참조하세요.

## <a name="create-a-new-custom-device-template"></a>새 사용자 지정 장치 템플릿 만들기

작성기로서 응용 프로그램에서 장치 템플릿을 만들고 편집할 수 있습니다. 장치 템플릿을 만들 때 Azure IoT Central은 템플릿에서 시뮬레이션된 장치를 생성합니다. 시뮬레이션된 장치는 원격 분석을 생성하여 물리적 장치에 연결하기 전에 응용 프로그램의 동작을 테스트할 수 있습니다.

응용 프로그램에 새 장치 템플릿을 추가하려면 **응용 프로그램 작성기** 페이지로 이동해야 합니다. 그러려면 왼쪽 탐색 메뉴에서 **응용 프로그램 작성기**를 선택합니다.

    ![Application Builder page](media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>장치 추가 및 원격 분석 정의

다음 단계에서는 응용 프로그램에 온도 원격 분석을 전송하는 장치에 대한 새 **연결된 공조 장치** 템플릿을 만드는 방법을 보여줍니다.

1. **응용 프로그램 작성기** 페이지에서 **장치 템플릿 만들기**를 선택합니다.

    ![응용 프로그램 작성기 페이지에서 장치 템플릿 만들기](media/tutorial-define-device-type/builderhomedevices.png)

1. **장치 템플릿** 페이지에서 **사용자 지정**을 선택합니다. **사용자 지정** 장치 템플릿을 사용하면 연결된 공조 장치의 모든 특성 및 동작을 정의할 수 있습니다.

    ![장치](media/tutorial-define-device-type/builderhomedevicescustom.png)

1. **새 장치 템플릿을** 페이지에서 장치 이름으로 **연결된 공조 장치**를 입력한 다음, **만들기**를 선택합니다. 장치 탐색기의 연산자에 표시되는 장치의 이미지를 업로드할 수 있습니다.

    ![사용자 지정 장치](media/tutorial-define-device-type/createcustomdevice.png)

1. **연결된 공조 장치** 템플릿에서 원격 분석을 정의하는 경우 **측정** 페이지에 있는지 확인합니다. 정의하는 각 장치 템플릿은 다음을 위한 별도 페이지가 있습니다.

    * 장치에서 보내는 원격 분석, 이벤트 및 상태 같은 측정값을 지정합니다.
    * 장치를 컨트롤하는 데 사용하는 설정을 정의합니다.
    * 장치에 대한 정보를 기록하는 데 사용하는 속성을 정의합니다.
    * 장치와 연결된 규칙을 정의합니다.
    * 연산자에 대한 장치 대시보드를 사용자 지정합니다.

    ![공조 장치 측정값](media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > 장치 또는 장치 템플릿의 이름을 변경하려면 페이지 위쪽에 있는 텍스트를 클릭합니다.

1. 온도 원격 분석 측정값을 추가하려면 **새 측정**을 선택합니다. 그런 다음, 측정 유형으로 **원격 분석**을 선택합니다.

    ![연결된 공조 장치 측정값](media/tutorial-define-device-type/airconmeasurementsnew.png)

1. 장치 템플릿에 대해 정의하는 각 유형의 원격 분석은 다음과 같은 [구성 옵션](howto-set-up-template.md)을 포함합니다.

    * 옵션을 표시합니다.
    * 원격 분석의 세부 정보입니다.
    * 시뮬레이션 매개 변수입니다.

    **온도** 원격 분석을 구성하려면 다음 표의 정보를 사용합니다.

    | 설정              | 값         |
    | -------------------- | -----------   |
    | 표시 이름         | 온도   |
    | 필드 이름           | 온도   |
    | Units                | F             |
    | Min                  | 60            |
    | max                  | 110           |
    | 소수 자릿수       | 0             |

    또한 원격 분석 표시에 대한 색을 선택할 수 있습니다. 원격 분석 정의를 저장하려면 **저장**을 선택합니다.

    ![온도 시뮬레이션 구성](media/tutorial-define-device-type/temperaturesimulation.png)

1. 잠시 후 **측정** 페이지는 시뮬레이션한 연결된 공조 장치에서 온도 원격 분석 차트를 표시합니다. 컨트롤을 사용하여 표시 유형 및 집계를 관리하거나 원격 분석 정의를 편집합니다.

    ![온도 시뮬레이션 보기](media/tutorial-define-device-type/viewsimulation.png)

1. **줄**, **누적** 및 **편집 시간 범위** 컨트롤을 사용하여 차트를 사용자 지정할 수도 있습니다.

    ![차트 사용자 지정](media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>이벤트 측정 정의
이벤트를 사용하여 오류 또는 구성 요소 오류와 같이 중요한 것을 나타내려면 장치에서 전송된 지정 시간 데이터를 정의합니다. 원격 분석 측정처럼 Azure IoT Central은 물리적 장치에 연결하기 전에 응용 프로그램의 동작을 테스트할 수 있도록 장치 이벤트를 시뮬레이션할 수 있습니다. **측정** 보기에서 장치 유형에 대한 이벤트 측정을 정의합니다.

1. **팬 모터 오류** 이벤트 측정을 추가하려면 **새 측정**을 선택합니다. 그런 다음, 측정 유형으로 **이벤트**를 선택합니다.

    ![연결된 공조 장치 측정값](media/tutorial-define-device-type/eventnew.png)

1. 장치 템플릿에 대해 정의하는 각 유형의 이벤트은 다음과 같은 [구성 옵션](howto-set-up-template.md)을 포함합니다.

    * 표시 이름입니다.
    * 필드 이름입니다.
    * 심각도입니다.

    **팬 모터 오류** 이벤트를 구성하려면 다음 표의 정보를 사용합니다.

    | 설정              | 값             |
    | -------------------- | -----------       |
    | 표시 이름         | 팬 모터 오류   |
    | 필드 이름           | fanmotorerr       |
    | 심각도             | 오류             |

    이벤트 정의를 저장하려면 **저장**을 선택합니다.

    ![이벤트 측정 구성](media/tutorial-define-device-type/eventconfiguration.png)

1. 잠시 후 **측정** 페이지는 시뮬레이션한 연결된 공조 장치에서 임의로 생성된 이벤트의 차트를 표시합니다. 컨트롤을 사용하여 표시 유형을 관리하거나 이벤트 정의를 편집합니다.

    ![이벤트 시뮬레이션 보기](media/tutorial-define-device-type/eventview.png)

1. 이벤트에 대한 추가 세부 정보를 보려면 차트에서 이벤트를 클릭합니다.

    ![이벤트 세부 정보 보기](media/tutorial-define-device-type/eventviewdetail.png)


## <a name="define-state-measurement"></a>상태 측정 정의
상태를 사용하여 일정 시간 동안 장치나 해당 구성 요소의 상태를 정의하고 시각화할 수 있습니다. 원격 분석 측정처럼 Azure IoT Central은 물리적 장치에 연결하기 전에 응용 프로그램의 동작을 테스트할 수 있도록 장치 상태를 시뮬레이션할 수 있습니다. **측정** 보기에서 장치 유형에 대한 상태 측정을 정의합니다.

1. **팬 모드** 측정을 추가하려면 **새 측정**을 선택합니다. 그런 다음, 측정 유형으로 **상태**를 선택합니다.

    ![연결된 공조 장치 상태 측정값](media/tutorial-define-device-type/statenew.png)

1. 장치 템플릿에 대해 정의하는 각 유형의 상태는 다음과 같은 [구성 옵션](howto-set-up-template.md)을 포함합니다.

    * 표시 이름입니다.
    * 필드 이름입니다.
    * 선택 사항으로 값이 레이블을 표시합니다.
    * 각 값에 대한 색

    **팬 모터** 상태를 구성하려면 다음 표의 정보를 사용합니다.

    | 설정              | 값             |
    | -------------------- | -----------       |
    | 표시 이름         | 팬 모드          |
    | 필드 이름           | fanmode           |
    | 값                | 1                 |
    | 레이블 표시        | 운영         |
    | 값                | 0                 |
    | 레이블 표시        | 중지됨           |

    상태 측정값을 저장하려면 **저장**을 선택합니다.

    ![상태 측정 구성](media/tutorial-define-device-type/stateconfiguration.png)

1. 잠시 후 **측정** 페이지는 시뮬레이션한 연결된 공조 장치에서 임의로 생성된 상태의 차트를 표시합니다. 컨트롤을 사용하여 표시 유형을 관리하거나 상태 정의를 편집합니다.

    ![상태 시뮬레이션 보기](media/tutorial-define-device-type/stateview.png)

1. 짧은 기간 내에 장치에서 보낸 데이터 요소가 너무 많은 경우 상태 측정값은 아래와 같이 다른 시각적 개체로 표시됩니다. 차트를 클릭하는 경우 해당 기간 내의 모든 데이터 요소가 시간순으로 표시됩니다. 차트에 표시된 측정값을 볼 수 있도록 시간 범위를 좁힐 수 있습니다.

    ![상태 세부 정보 보기](media/tutorial-define-device-type/stateviewdetail.png)

## <a name="properties-device-properties-and-settings"></a>속성, 장치 속성 및 설정

속성, 장치 속성 및 설정은 장치 템플릿에서 정의되고 각 개별 장치와 연결된 다른 값입니다.

* _설정_을 사용하여 응용 프로그램에서 구성 데이터를 장치에 보낼 수 있습니다. 예를 들어 연산자는 설정을 사용하여 장치 원격 분석 간격을 2초에서 5초로 변경할 수 있습니다. 연산자가 설정을 변경하는 경우 장치가 설정을 변경했다는 것을 인식할 때까지 설정은 UI에서 보류 중으로 표시됩니다.
* _속성_을 사용하여 응용 프로그램에서 장치에 대한 정보를 기록합니다. 예를 들어 장치의 일련 번호 또는 장치 제조업체의 전화 번호를 기록하려면 속성을 사용할 수 있습니다. 속성은 응용 프로그램에 저장되고 장치와 동기화되지 않습니다. 연산자는 속성에 값을 할당할 수 있습니다.
* 속성 값을 응용 프로그램에 보내도록 장치를 설정하려면 _장치 속성_을 사용할 수 있습니다. 이러한 속성은 장치에서만 변경할 수 있습니다. 연산자의 경우 장치 속성은 읽기 전용입니다.

## <a name="use-settings"></a>설정 사용

구성 데이터를 장치에 보내도록 장치를 설정하려면 _설정_을 사용합니다. 이 섹션에서 **연결된 공조 장치** 템플릿에 설정을 추가하여 연산자가 연결된 공조 장치의 대상 온도를 설정하게 할 수 있습니다.

1. **연결된 공조 장치** 템플릿에 대한 **설정** 페이지로 이동합니다.

    ![설정 추가 준비](media/tutorial-define-device-type/deviceaddsetting.png)

    숫자 또는 텍스트 등 다른 형식의 설정을 만들 수 있습니다.

1. **번호**를 선택하여 장치에 숫자 설정을 추가합니다.

1. **온도 설정**을 구성하려면 다음 표의 정보를 사용합니다.

    | 필드                | 값           |
    | -------------------- | -----------     |
    | 표시 이름         | 온도 설정 |
    | 필드 이름           | setTemperature  |
    | 측정 단위  | F               |
    | 소수 자릿수       | 1               |
    | 최솟값        | 20              |
    | 최댓값        | 200             |
    | 초기 값        | 80              |
    | 설명          | 공조 장치에 대한 대상 온도 설정 |

    그런 다음, **저장**을 선택합니다.

    ![온도 설정 구성](media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > 장치가 설정 변경을 인식하는 경우 설정 상태가 **동기화됨**으로 변경됩니다.

1. 설정 타일을 이동하고 크기 조정하여 **설정** 페이지의 레이아웃을 사용자 지정할 수 있습니다.

    ![설정 레이아웃 사용자 지정](media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>속성 사용

_속성_을 사용하여 응용 프로그램에서 장치에 대한 정보를 저장합니다. 이 섹션에서는 **연결된 공조 장치** 템플릿에 속성을 추가하여 장치 일련 번호 및 각 장치의 펌웨어 버전을 저장합니다.

1. **연결된 공조 장치** 템플릿에 대한 **속성** 페이지로 이동합니다.

    ![속성 추가 준비](media/tutorial-define-device-type/deviceaddproperty.png)

    숫자 또는 텍스트 등 다른 형식의 속성을 만들 수 있습니다. 장치 템플릿에 일련 번호 속성을 추가하려면 **텍스트**를 선택합니다.

1. 일련 번호 속성을 구성하려면 다음 표의 정보를 사용합니다.

    | 필드                | 값                |
    | -------------------- | -------------------- |
    | 표시 이름         | 일련 번호        |
    | 필드 이름           | serialNumber         |
    | 초기 값        | cac00001             |
    | 설명          | 장치 일련 번호 |

    다른 필드는 기본값 그대로 둡니다.

    ![장치 속성 구성](media/tutorial-define-device-type/configureproperties.png)

    그런 다음, **저장**을 선택합니다.

1. 장치 템플릿에 펌웨어 버전 속성을 추가하려면 **텍스트** 선택

1. 펌웨어 버전 속성을 구성하려면 다음 표의 정보를 사용합니다.

    | 필드                | 값                   |
    | -------------------- | ----------------------- |
    | 표시 이름         | 펌웨어 버전        |
    | 필드 이름           | firmwareVersion         |
    | 초기 값        | 0.1                     |
    | 설명          | 장치 펌웨어 버전 |

    ![장치 속성 구성](media/tutorial-define-device-type/configureproperties2.png)

    그런 다음, **저장**을 선택합니다.

1. 속성 타일을 이동하고 크기 조정하여 **속성** 페이지의 레이아웃을 사용자 지정할 수 있습니다.

    ![속성 레이아웃 사용자 지정](media/tutorial-define-device-type/propertieslayout.png)

## <a name="view-your-simulated-device"></a>시뮬레이션된 장치 보기

**연결된 공조 장치** 템플릿을 정의했으므로 이제 **대시보드**를 사용자 지정하여 정의한 측정, 설정 및 속성을 포함할 수 있습니다. 그런 다음, 연산자로서 대시보드를 미리 보기할 수 있습니다.

1. **연결된 공조 장치** 템플릿에 대한 **대시보드** 페이지를 선택합니다.

    ![연결된 공조 장치 대시보드](media/tutorial-define-device-type/aircondashboards.png)

1. **꺾은선형 차트**를 선택하여 **대시보드**에 구성 요소를 추가합니다.

    ![대시보드 구성 요소](media/tutorial-define-device-type/dashboardcomponents1.png)

1. 다음 표의 정보를 사용하여 **꺾은선형 차트** 구성 요소를 구성합니다.

    | 설정      | 값       |
    | ------------ | ----------- |
    | 제목        | 온도 |
    | 시간 범위   | 지난 30분 |
    | 측정값 | 온도(**온도** 옆의 **표시 유형** 선택) |

    ![꺾은선형 차트 설정](media/tutorial-define-device-type/linechartsettings.png)

    그런 다음, **저장**을 선택합니다.

1. 다음 표의 정보를 사용하여 **이벤트 차트** 구성 요소를 구성합니다.

    | 설정      | 값       |
    | ------------ | ----------- |
    | 제목        | 이벤트 |
    | 시간 범위   | 지난 30분 |
    | 측정값 | 팬 모터 오류(**팬 모터 오류** 옆의 **표시 유형** 선택) |

    ![꺾은선형 차트 설정](media/tutorial-define-device-type/dashboardeventchartsetting.png)

    그런 다음, **저장**을 선택합니다.

1. 다음 표의 정보를 사용하여 **상태 차트** 구성 요소를 구성합니다.

    | 설정      | 값       |
    | ------------ | ----------- |
    | 제목        | 팬 모드 |
    | 시간 범위   | 지난 30분 |
    | 측정값 | 팬 모드(**팬 모드** 옆의 **표시 유형** 선택) |

    ![꺾은선형 차트 설정](media/tutorial-define-device-type/dashboardstatechartsetting.png)

    그런 다음, **저장**을 선택합니다.

1. 온도 설정을 대시보드에 추가하려면 **설정 및 속성**을 선택합니다.

    ![대시보드 구성 요소](media/tutorial-define-device-type/dashboardcomponents4.png)

1. 다음 표의 정보를 사용하여 **설정 및 속성** 구성 요소를 구성합니다.

    | 설정                 | 값         |
    | ----------------------- | ------------- |
    | 제목                   | 대상 온도 설정 |
    | 설정 및 속성 | 온도 설정 |

    ![일련 번호 속성 설정](media/tutorial-define-device-type/propertysettings3.png)

    그런 다음, **저장**을 선택합니다.

1. 장치 일련 번호를 대시보드에 추가하려면 **설정 및 속성**을 선택합니다.

    ![대시보드 구성 요소](media/tutorial-define-device-type/dashboardcomponents3.png)

1. 다음 표의 정보를 사용하여 **설정 및 속성** 구성 요소를 구성합니다.

    | 설정                 | 값         |
    | ----------------------- | ------------- |
    | 제목                   | 일련 번호 |
    | 설정 및 속성 | 일련 번호 |

    ![일련 번호 속성 설정](media/tutorial-define-device-type/propertysettings1.png)

    그런 다음, **저장**을 선택합니다.

1. 장치 펌웨어 버전을 대시보드에 추가하려면 **설정 및 속성**을 선택합니다.

    ![대시보드 구성 요소](media/tutorial-define-device-type/dashboardcomponents4.png)

1. 다음 표의 정보를 사용하여 **설정 및 속성** 구성 요소를 구성합니다.

    | 설정                 | 값            |
    | ----------------------- | ---------------- |
    | 제목                   | 펌웨어 버전 |
    | 설정 및 속성 | 펌웨어 버전 |

    ![일련 번호 속성 설정](media/tutorial-define-device-type/propertysettings2.png)

    그런 다음, **저장**을 선택합니다.

1. 연산자로서 대시보드 보려면 페이지의 오른쪽 상단에서 **디자인 모드**를 해제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 새 장치 템플릿 만들기
> * 장치에 원격 분석 추가
> * 시뮬레이션된 원격 분석 보기
> * 장치 이벤트 정의
> * 시뮬레이션된 이벤트 보기
> * 상태 정의
> * 시뮬레이션된 상태 보기
> * 장치 속성 사용
> * 장치 설정 사용

Azure IoT Central 응용 프로그램에서 장치 템플릿을 정의했으므로 제안된 다음 단계는 다음과 같습니다.

* [장치에 대한 규칙 및 작업 구성](tutorial-configure-rules.md)
* [연산자의 뷰 사용자 지정](tutorial-customize-operator.md)
