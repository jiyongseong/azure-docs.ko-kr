---
title: Azure에서 클러스터에 Java Service Fabric 응용 프로그램 배포 | Microsoft Docs
description: 이 자습서에서는 Azure Service Fabric 클러스터에 Java Service Fabric 응용 프로그램을 배포하는 방법을 알아봅니다.
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
ms.openlocfilehash: 92445ffa7954d42ec1a864264fbfc7555986ad58
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>자습서: Azure의 Service Fabric 클러스터에 Java 응용 프로그램 배포
이 자습서는 시리즈의 3부로, Azure에서 클러스터에 Service Fabric 응용 프로그램을 배포하는 방법을 보여줍니다.

시리즈 3부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 보안 Linux 클러스터 만들기 
> * 클러스터에 응용 프로그램 배포

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> *  [Java Service Fabric Reliable Services 응용 프로그램 빌드](service-fabric-tutorial-create-java-app.md)
> * [로컬 클러스터에서 응용 프로그램 배포 및 디버그](service-fabric-tutorial-debug-log-local-cluster.md)
> * Azure 클러스터에 응용 프로그램 배포
> * [응용 프로그램에 대한 모니터링 및 진단 설정](service-fabric-tutorial-java-elk.md)
> * [CI/CD를 설정합니다](service-fabric-tutorial-java-jenkins.md).

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Mac](service-fabric-get-started-mac.md) 또는 [Linux](service-fabric-get-started-linux.md)용 Service Fabric SDK 설치
- [Python 3 설치](https://wiki.python.org/moin/BeginnersGuide/Download)

## <a name="create-a-service-fabric-cluster-in-azure"></a>Azure에서 Service Fabric 클러스터 만들기

다음 단계에서는 Service Fabric 클러스터에 응용 프로그램을 배포하는 데 요구되는 필요한 리소스를 만듭니다. 또한 ELK(Elasticsearch, Logstash, Kibana) 스택을 사용하여 솔루션의 상태를 모니터링하는 데 필요한 리소스를 설정합니다. 특히, [Event Hubs](https://azure.microsoft.com/services/event-hubs/)는 Service Fabric에서 로그에 대한 싱크로 사용됩니다. Service Fabric 클러스터에서 Logstash 인스턴스에 로그를 보내기 위해 구성됩니다. 

1. 터미널을 열고 필요한 도우미 스크립트와 Azure에서 리소스를 만들 템플릿을 포함하는 다음 패키지 다운로드

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Azure 계정에 로그인합니다. 

    ```bash
    az login
    ```

3. 리소스를 만들려면 사용하려는 Azure 구독 설정 

    ```bash
    az account set --subscription [SUBSCRIPTION-ID]
    ``` 

4. *service-fabric-java-quickstart/AzureCluster* 폴더에서 다음 명령을 실행해 Key Vault에서 클러스터 인증서를 만듭니다. 이 인증서는 Service Fabric 클러스터를 보호하는 데 사용됩니다. 영역(Service Fabric 클러스터와 동일해야 함), Key Vault 리소스 그룹 이름, Key Vault 이름, 인증서 암호 및 클러스터 DNS 이름을 제공합니다. 

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]
    
    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    위의 명령은 나중에 사용하기 위해 확인해야 하는 다음 정보를 반환합니다.

    ```
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. 로그를 저장하는 저장소 계정에 대한 리소스 그룹 만들기 

    ```bash
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]
    
    Example: az group create --location westus --name teststorageaccountrg
    ```

6. 생산될 로그를 저장하는 데 사용될 저장소 계정 만들기

    ```bash
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage
    
    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. [Azure Portal](https://portal.azure.com)에 액세스하고 Storage 계정에 대한 **공유 액세스 서명** 탭으로 이동합니다. 다음과 같이 SAS 토큰을 생성합니다. 

    ![Storage에 대한 SAS 생성](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. 계정 SAS URL을 복사하고 Service Fabric 클러스터를 만들 때 사용하기 위해 따로 보관해 둡니다. 다음 URL과 유사합니다.

    ```
    ?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Event Hub 리소스를 포함하는 리소스 그룹을 만듭니다. Event Hubs는 Service Fabric에서 ELK 리소스를 실행하는 서버로 메시지를 보내는 데 사용됩니다.

    ```bash
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]
    
    Example: az group create --location westus --name testeventhubsrg
    ```

10. 다음 명령을 사용하여 Event Hubs 리소스를 만듭니다. 지시에 따라 namespaceName, eventHubName, consumerGroupName, sendAuthorizationRule 및 receiveAuthorizationRule에 대한 세부 정보를 입력합니다. 

    ```bash
    az group deployment create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json
    
    Example: 
    az group deployment create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    이전 명령의 JSON 출력에서 **출력** 필드의 내용을 복사합니다. 보낸 사람 정보는 Service Fabric 클러스터를 만들 때 사용됩니다. 수신기 이름 및 키는 Logstash 서비스가 Event Hub에서 메시지를 수신하도록 구성된 경우 다음 자습서에서 사용하기 위해 저장돼야 합니다. 다음 BLOB은 JSON 출력 예제입니다.     
    
    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. *eventhubssastoken.py* 스크립트를 실행해 만든 EventHubs 리소스에 대한 SAS url을 생성합니다. 이 SAS URL은 Event Hubs에 로그를 보낼 Service Fabric 클러스터에서 사용합니다. 결과적으로 **보낸 사람** 정책은 URL을 생성하는 데 사용됩니다. 스크립트는 다음 단계에서 사용되는 Event Hubs 리소스에 대한 SAS URL을 반환합니다.

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    반환된 JSON에서 **sr** 필드 값을 복사합니다. **sr** 필드 값은 EventHubs에 대한 SAS 토큰입니다. 다음 URL은 **sr** 필드의 예제입니다.

    ```bash
    https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender
    ```

    EventHubs에 대한 SAS URL은 다음 https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken> 구조를 따릅니다. 예를 들어 https://testeventhubnamespace.servicebus.windows.net/testeventhub?sr=https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender

12. *sfdeploy.parameters.json* 파일을 열고 이전 단계에서 다음 내용을 바꾸기 

    ```
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. Service Fabric 클러스터를 만들려면 다음 명령 실행

    ```bash
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>클러스터에 응용 프로그램 배포

1. 응용 프로그램을 배포 하기 전에 다음 코드 조각을 *Voting/VotingApplication/ApplicationManifest.xml* 파일에 추가해야 합니다. **X509FindValue** 필드는 **Azure에서 Service Fabric 클러스터 만들기** 섹션의 4단계에서 반환된 지문입니다. 이 코드 조각은 **ApplicationManifest** 필드(루트 필드) 아래에 중첩됩니다. 

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. 이 클러스터에 응용 프로그램을 배포하려면 클러스터에 연결을 설정할 SFCTL을 사용해야 합니다. SFCTL은 클러스터에 연결하기 위해 공용 및 개인 키가 있는 PEM 파일을 필요로 하며 그 결과 다음 명령을 실행해 공용 및 개인 키가 있는 PEM 파일을 생산합니다. 

    ```bash
    openssl pkcs12 -in testservicefabric.westus.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. 다음 명령을 실행하여 클러스터에 연결합니다.

    ```bash
    sfctl cluster select --endpoint https://testlinuxcluster.westus.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. 응용 프로그램을 배포하려면 *Voting/Scripts* 폴더로 이동해 **install.sh** 스크립트를 실행합니다. 

    ```bash
    ./install.sh
    ```

5. Service Fabric Explorer에 액세스하려면 좋아하는 브라우저를 열고 https://testlinuxcluster.westus.cloudapp.azure.com:19080에 입력합니다. 이 끝점에 연결하는 데 사용하려는 인증서 저장소에서 인증서를 선택합니다. Linux 컴퓨터를 사용하는 경우 Service Fabric Explorer를 보려면 *new-service-fabric-cluster-certificate.sh* 스크립트에서 생성한 인증서를 크롬으로 가져와야 합니다. Mac을 사용하는 경우 Keychain에 PFX 파일을 설치해야 합니다. 응용 프로그램이 클러스터에 설치됐음을 알립니다. 

    ![SFX Java Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. 응용 프로그램에 액세스하려면 https://testlinuxcluster.westus.cloudapp.azure.com:8080에 입력 

    ![Voting App Java Azure](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. 클러스터에서 응용 프로그램을 제거하려면 **스크립트** 폴더에서 *uninstall.sh* 스크립트 실행 

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 보안 Linux 클러스터 만들기 
> * ELK를 사용하여 모니터링하는 데 필요한 리소스 만들기 
> * 선택 사항: Service Fabric 실행하기 위해 파티 클러스터를 사용하는 방법

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [모니터링 및 진단 설정](service-fabric-tutorial-java-elk.md)