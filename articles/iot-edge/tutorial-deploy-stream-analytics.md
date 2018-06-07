---
title: Azure IoT Edge를 사용하여 Azure Stream Analytics 배포 | Microsoft Docs
description: Azure Stream Analytics를 Edge 장치에 모듈로 배포
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5d4c2b3bc55b94b08287a06125e15ac61013834a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362042"
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Azure Stream Analytics를 IoT Edge 모듈로 배포 - 미리 보기

IoT 장치는 많은 양의 데이터를 생성할 수 있습니다. 업로드되는 데이터 양을 줄이거나 실행 가능한 정보를 얻는 데 소요되는 왕복 대기 시간을 없애기 위해 클라우드에 연결하기 전에 이 데이터를 분석하거나 처리해야 하는 경우도 있습니다.

Azure IoT Edge는 빠른 배포를 위해 미리 빌드한 Azure 서비스 IoT Edge 모듈을 사용합니다. [Azure Stream Analytics][azure-stream]가 이러한 모듈 중 하나입니다. 해당 포털에서 Azure Stream Analytics 작업을 만든 후 Azure IoT Hub 포털로 와서 IoT Edge 모듈로 배포할 수 있습니다. 

ASA(Azure Stream Analytics)는 클라우드 및 IoT Edge 장치 둘 다에서 데이터 분석을 위한 풍부하게 구조화된 쿼리 구문을 제공합니다. IoT Edge의 Azure Stream Analytics에 대한 자세한 내용은 [Azure Stream Analytics 설명서](../stream-analytics/stream-analytics-edge.md)를 참조하세요.

이 자습서에서는 Azure Stream Analytics 작업을 만들고 IoT Edge 장치에 배포하는 과정을 설명합니다. 이렇게 하면 장치에서 직접 로컬 원격 분석 스트림을 처리하고 장치에 즉각적인 조치를 발생하는 경고를 생성할 수 있습니다. 

이 자습서에서는 다음 두 개의 모듈을 제공합니다. 
* 시뮬레이션의 온도 센서 모듈(tempSensor)은 20~120도 범위의 날짜 데이터를 생성하며 5초마다 1씩 증가합니다. 
* Stream Analytics 모듈은 30초 평균이 70에 도달할 때 tempSensor를 다시 설정합니다. 프로덕션 환경에서 이 기능을 사용하여 온도가 위험 수준에 도달하면 컴퓨터를 종료하거나 예방 조치를 취할 수 있습니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 에지에서 데이터를 처리하는 Azure Stream Analytics 작업을 만듭니다.
> * 새 Azure Stream Analytics 작업을 다른 IoT Edge 모듈에 연결합니다.
> * Azure Stream Analytics 작업을 IoT Edge 장치에 배포합니다.

## <a name="prerequisites"></a>필수 조건

* IoT Hub 
* 빠른 시작 또는 [Windows][lnk-tutorial1-win] 및 [Linux][lnk-tutorial1-lin]의 시뮬레이트된 장치에 Azure IoT Edge 배포하는 작업에 대한 문서에서 만들어 구성한 장치 장치 연결 키 및 장치 ID를 알고 있어야 합니다. 
* IoT Edge 장치에서 실행되는 Docker.
    * [Windows에 Docker를 설치][lnk-docker-windows]합니다.
    * [Linux에 Docker를 설치][lnk-docker-linux]합니다.
* IoT Edge 장치의 Python 2.7.x.
    * [Windows에 Python 2.7을 설치][lnk-python]합니다.
    * Ubuntu를 포함한 대부분의 Linux 배포에는 Python 2.7이 이미 설치되어 있습니다. pip가 설치되었는지 확인하려면 다음 명령을 사용합니다. `sudo apt-get install python-pip`

## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics 작업 만들기

이 섹션에서는 IoT Hub에서 데이터를 가져오고, 장치에서 전송된 원격 분석 데이터를 쿼리하고, 결과를 Azure Blob Storage 컨테이너로 전달하기 위한 Azure Stream Analytics 작업을 만듭니다. 자세한 내용은 [Stream Analytics 설명서][azure-stream]의 “개요” 섹션을 참조하세요. 

### <a name="create-a-storage-account"></a>저장소 계정 만들기

Azure Stream Analytics 작업에서 출력으로 사용할 끝점을 제공하려면 Azure Storage 계정이 필요합니다. 이 섹션의 예제에서는 BLOB Storage 유형을 사용합니다. 자세한 내용은 [Azure Storage 설명서][azure-storage]의 “Blob” 섹션을 참조하세요.

1. Azure Portal에서 **리소스 만들기**로 이동한 후 검색 상자에 **저장소 계정**을 입력하고 **저장소 계정 - Blob, 파일, 테이블, 큐**를 선택합니다.

2. **저장소 계정 만들기** 창에 저장소 계정의 이름을 입력하고, IoT Hub가 저장된 위치와 동일한 위치를 선택하고, IoT Hub와 동일한 리소스 그룹을 선택한 다음, **만들기**를 선택합니다. 나중에 사용할 수 있게 이름을 적어둡니다.

    ![저장소 계정 만들기][1]

3. 방금 만든 저장소 계정으로 이동한 다음 **Blob 찾아보기**를 선택합니다. 

4. Azure Stream Analytics 모듈에서 데이터를 저장할 새 컨테이너를 만들고, 액세스 수준을 **컨테이너**로 설정한 후 **확인**을 선택합니다.

    ![저장소 설정][10]

### <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. Azure Portal에서 **리소스 만들기** > **사물 인터넷**으로 이동한 후 **Stream Analytics 작업**을 선택합니다.

2. **새 Stream Analytics 작업** 창에서 다음을 수행합니다.

    a. **작업 이름** 상자에 작업 이름을 입력합니다.
    
    나. **호스팅 환경** 아래에서 **에지**를 선택합니다.
    
    다. 나머지 필드에서 기본값을 사용합니다.

    > [!NOTE]
    > 현재 IoT Edge의 Azure Stream Analytics 작업은 미국 서부 2 지역에서 지원되지 않습니다. 

3. **만들기**를 선택합니다.

4. 만든 작업의 **작업 토폴로지** 아래에서 **입력**을 엽니다.

   ![Azure Stream Analytics 입력](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. **스트림 입력 추가**를 선택하고 **Edge Hub**를 선택합니다.

5. **새 입력** 창에 입력 별칭으로 **온도**를 입력합니다. 

6. **저장**을 선택합니다.

7. **작업 토폴로지**에서 **출력**을 엽니다.

   ![Azure Stream Analytics 출력](./media/tutorial-deploy-stream-analytics/asa_output.png)

8. **추가**를 선택하고 **Edge Hub**를 선택합니다.

8. **새 출력** 창에서 출력 별칭으로 **경고**를 입력합니다. 

9. **만들기**를 선택합니다.

9. **작업 토폴로지** 아래에서 **쿼리**를 선택하고 기본 텍스트를 다음 쿼리로 바꿉니다.

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

10. **저장**을 선택합니다.

## <a name="deploy-the-job"></a>작업 배포

이제 IoT Edge 장치에 Azure Stream Analytics 작업을 배포할 준비가 되었습니다.

1. Azure Portal의 IoT Hub에서 **IoT Edge(미리 보기)** 로 이동한 후 IoT Edge 장치의 세부 정보 페이지를 엽니다.

2. **모듈 설정**을 선택합니다.  
    이전에 이 장치에 tempSensor 모듈을 배포한 경우 자동으로 입력될 수 있습니다. 그렇지 않은 경우 다음을 수행하여 모듈을 추가합니다.

   a. **IoT Edge 모듈 추가**를 선택합니다.

   나. 이름으로 **tempSensor**를 입력합니다.
    
   다. 이미지의 URI로 **microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview**를 입력합니다. 

   d. 다른 설정은 변경하지 말고 그대로 둡니다.
   
   e. **저장**을 선택합니다.

3. Azure Stream Analytics Edge 작업을 추가하려면 **Azure Stream Analytics IoT Edge 모듈 가져오기**를 선택합니다.

4. 구독 및 사용자가 만든 Azure Stream Analytics Edge 작업을 선택합니다. 

5. 구독 및 사용자가 만든 저장소 계정을 선택한 후 **저장**을 선택합니다.

    ![모듈 설정][6]

6. Azure Stream Analytics 모듈의 이름을 복사합니다. 

    ![온도 모듈][11]

7. 경로를 구성하려면 **다음**을 선택합니다.

8. 다음 코드를 **경로**로 복사합니다. _{moduleName}_ 을 복사한 모듈 이름으로 바꿉니다.

    ```json
    {
        "routes": {                                                               
          "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream", 
          "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream", 
          "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")", 
          "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")" 
        }
    }
    ```

9. **다음**을 선택합니다.

10. **템플릿 검토** 단계에서 **제출**을 선택합니다.

11. 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다.  
    IoT Edge 에이전트 모듈 및 IoT Edge Hub와 함께 실행되는 새로운 Stream Analytics 모듈이 표시됩니다.

    ![모듈 출력][7]

## <a name="view-data"></a>데이터 보기

이제 IoT Edge 장치로 가서 Azure Stream Analytics 모듈과 tempSensor 모듈 간의 상호 작용을 확인할 수 있습니다.

1. Docker에서 모든 모듈이 실행되는지 확인합니다.

   ```cmd/sh
   docker ps  
   ```

   ![Docker 출력][8]

2. 모든 시스템 로그 및 메트릭 데이터를 봅니다. Stream Analytics 모듈 이름을 사용합니다.

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

30초 동안 70도에 도달할 때까지 컴퓨터의 온도가 점차 상승하는 것을 볼 수 있어야 합니다. 그러면 Stream Analytics 모듈이 재설정을 트리거하고 컴퓨터 온도가 21도로 떨어집니다. 

   ![Docker 로그][9]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치의 데이터를 분석하도록 Azure Storage 컨테이너 및 Stream Analytics 작업을 구성했습니다. 그런 다음 다운로드 목적으로 장치의 데이터를 스트림을 통해 Blob으로 이동하기 위해 사용자 지정 Azure Stream Analytics 모듈을 로드했습니다. Azure IoT Edge에서 추가 업무용 솔루션을 만드는 방법을 확인하려는 경우 다른 자습서를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"] 
> [Azure Machine Learning 모델을 모듈로 배포][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
