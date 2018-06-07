---
title: ELK를 사용하여 Azure Service Fabric에서 응용 프로그램 모니터링 | Microsoft Docs
description: 이 자습서에서는 ELK를 설정하고 Service Fabric 응용 프로그램을 모니터링 하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 2c948a137abdbbf6ef8c64d26065030db1633a0a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>자습서: ELK를 사용한 Service Fabric 응용 프로그램 모니터링 
이 자습서는 시리즈의 4부입니다. 여기서는 ELK(Elasticsearch, Logstash 및 Kibana)를 사용하여 Azure에서 실행하는 Service Fabric 응용 프로그램을 모니터링하는 방법을 설명합니다. 

시리즈 4부에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure에 ELK 서버 설정
> * Event Hubs에서 로그를 수신할 Logstash 구성
> * Kibana에서 플랫폼 및 응용 프로그램 로그 시각화 

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [Java Service Fabric Reliable Services 응용 프로그램 빌드](service-fabric-tutorial-create-java-app.md)
> * [로컬 클러스터에서 응용 프로그램 배포 및 디버그](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Azure 클러스터에 응용 프로그램 배포](service-fabric-tutorial-java-deploy-azure.md)
> * 응용 프로그램에 대한 모니터링 및 진단 설정
> * [CI/CD를 설정합니다](service-fabric-tutorial-java-jenkins.md).

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [2부](service-fabric-tutorial-debug-log-local-cluster.md)에서 지정된 위치로 로그를 내보내는 응용 프로그램을 설정합니다.
- [3부](service-fabric-tutorial-java-deploy-azure.md)를 완료하고 Event Hubs에 로그를 보내기 위해 실행 중인 Service Fabric 클러스터를 구성합니다. 
- '수신 대기' 권한과 시리즈 3부에서 연결된 기본 키가 있는 Event Hubs 정책입니다.

## <a name="download-the-voting-sample-application"></a>투표 응용 프로그램 예제 다운로드
[이 자습서 시리즈의 1부](service-fabric-tutorial-create-java-app.md)에서 투표 예제 응용 프로그램을 빌드하지 않은 경우 다운로드할 수 있습니다. 명령 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>Azure에서 ELK 서버 만들기
이 자습서에 대해 미리 구성된 ELK 환경을 사용하되 이미 ELK 환경이 있는 경우 **Logstash 설정** 섹션을 건너뛸 수 있습니다. 그러나 없는 경우 다음 단계는 Azure에서 ELK 환경을 만듭니다. 

1. Azure에서 [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6)에 의해 인증된 ELK를 만듭니다. 이 자습서의 목적인 이 서버를 만드는 데 특별히 따라야 할 사양 같은 것은 없습니다. 

2. Azure Portal에서 리소스로 이동하고 **지원 + 문제 해결** 섹션 아래의 **부팅 진단** 탭을 입력합니다. 다음으로 **직렬 로그** 탭을 클릭합니다.

    ![부팅 진단](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. Kibana 인스턴스에 액세스하기 위해 필요한 암호에 대한 로그 검색을 수행합니다. 다음 코드 조각과 유사합니다.

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. 로그인 정보를 가져오려면 Azure Portal에서 서버의 개요 페이지의 연결 단추를 누릅니다. 

    ![VM 연결](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. 다음 명령을 사용하여 ELK 이미지를 호스팅하는 서버에 대한 SSH

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER] 
    
    Example: ssh testaccount@104.40.63.157 
    ```

## <a name="set-up-elk"></a>ELK 설정 

1. 첫 번째 단계는 ELK 환경을 로드하는 것임

    ```bash
    sudo /opt/bitnami/use_elk 
    ```

2. 기존 환경을 사용 하는 경우 Logstash 서비스를 중지하려면 다음 명령을 실행

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. Event Hubs에 대한 Logstash 플러그인을 설치하려면 다음 명령을 실행합니다. 

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. 다음 내용을 사용하여 기존 Logstash 구성 파일을 만들거나 수정합니다. 파일을 만들 때 Azure에서 ELK Bitnami 이미지를 사용하는 경우 ```/opt/bitnami/logstash/conf/access-log.conf```에서 만들어야 합니다. 

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }
    
    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. 구성을 확인하려면 다음 명령을 실행합니다.

    ```bash 
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. Logstash 서비스 시작

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. 데이터를 수신하고 있는지 확인하려면 Elasticsearch를 확인

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. **http://SERVER-IP**에서 Kibana 대시보드에 액세스하고 Kibana에 대한 사용자 이름 및 암호를 입력합니다. Azure에서 ELK 이미지를 사용한 경우 기본 사용자 이름은 'user'가 되고 암호는 **부팅 진단**에서 가져옵니다. 

    ![Kibana](./media/service-fabric-tutorial-java-elk/kibana.png)    

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 실행되는 ELK 서버 가져오기 
> * Service Fabric 클러스터에서 진단 정보를 수신하도록 서버 구성

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Jenkins를 사용하여 CI/CD 설정](service-fabric-tutorial-java-jenkins.md)

