---
title: Linux에서 Azure Service Fabric 컨테이너 응용 프로그램 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 Azure Service Fabric에서 첫 번째 Linux 컨테이너 응용 프로그램을 만듭니다.  응용 프로그램을 사용하여 Docker 이미지를 빌드하고, 이미지를 컨테이너 레지스트리로 푸시하고, Service Fabric 컨테이너 응용 프로그램을 빌드하고 배포합니다.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 65f048d67ef5f250691700a382e781814c57e8a8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>빠른 시작: Azure에서 Azure Service Fabric Linux 컨테이너 응용 프로그램 배포
Azure Service Fabric은 확장성 있고 안정성이 뛰어난 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다. 

이 빠른 시작은 Service Fabric 클러스터에 Linux 컨테이너를 배포하는 방법을 보여줍니다. 작업이 완료되면 Python 웹 프런트 엔드 및 Redis 백 엔드로 구성된 Voting 응용 프로그램이 Service Fabric 클러스터에서 실행됩니다. 또한 응용 프로그램을 장애 조치 (failover)하는 방법과 클러스터의 응용 프로그램을 확장하는 방법도 알아봅니다.

![Voting 앱 웹 페이지][quickstartpic]

이 빠른 시작에서는 Azure Cloud Shell의 Bash 환경을 사용하여 Service Fabric CLI 명령을 실행합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Cloud Shell을 처음 실행하는 경우에는 `clouddrive` 파일 공유를 설정하라는 메시지가 표시됩니다. 기본값을 그대로 사용하거나 기존 파일 공유를 연결할 수 있습니다. 자세히 알아보려면 [`clouddrive` 파일 공유 설정](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share)을 참조하세요.

## <a name="get-the-application-package"></a>응용 프로그램 패키지 가져오기
Service Fabric에 컨테이너를 배포하려면 개별 컨테이너 및 응용 프로그램에 대해 설명하는 매니페스트 파일(응용 프로그램 정의)의 집합이 필요합니다.

Cloud Shell에서 git을 사용하여 응용 프로그램 정의를 복사한 다음 디렉터리를 복제본의 `Voting` 디렉터리로 변경합니다. 

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기
응용 프로그램을 Azure에 배포하려면 응용 프로그램을 실행하는 Service Fabric 클러스터가 필요합니다. 파티 클러스터는 Service Fabric 클러스터를 빠르게 만들 수 있는 방법을 제공합니다. 파티 클러스터는 Azure에서 호스팅되는 시간이 제한된 체험용 Service Fabric 클러스터이며 Service Fabric 팀에서 운영합니다. 파티 클러스터를 사용하여 응용 프로그램을 배포하고 플랫폼에 대해 알아볼 수 있습니다. 클러스터는 노드-노드 및 클라이언트-노드 보안에 단일 자체 서명 인증서를 사용합니다.

[Linux 클러스터](http://aka.ms/tryservicefabric)에 로그인하고 조인합니다. **PFX** 링크를 클릭하여 PFX 인증서를 컴퓨터에 다운로드합니다. **추가 정보** 링크를 클릭하여 인증서 암호 및 다양한 환경에서 인증서를 사용하도록 구성하는 방법에 대한 지침을 찾습니다. **시작** 페이지와 **추가 정보** 페이지를 모두 열어두고 다음 단계에서 일부 지침을 사용합니다. 

> [!Note]
> 시간당 사용 가능한 파티 클러스터의 수는 제한되어 있습니다. 파티 클러스터에 등록하려고 할 때 오류가 발생하면, 일정 기간 동안 기다린 후 다시 시도하거나, [Azure에서 Service Fabric 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)의 단계에 따라 구독에 클러스터를 만들 수 있습니다. 
> 
>자체 클러스터를 만드는 경우 웹 프런트 엔드 서비스는 들어오는 트래픽을 포트 80에서 수신 대기하도록 구성됩니다. 클러스터에 대해 포트가 열려 있는지 확인합니다. (파티 클러스터를 사용하는 경우 이 포트는 열려 있습니다.)
>

## <a name="configure-your-environment"></a>환경 구성
Service Fabric은 클러스터 및 해당 응용 프로그램을 관리하는 데 사용할 수 있는 몇 가지 도구를 제공합니다.

- Service Fabric Explorer, 브라우저 기반 도구입니다.
- Service Fabric CLI(명령줄 인터페이스), Azure CLI 2.0을 기반으로 실행됩니다.
- PowerShell 명령 

이 빠른 시작에서는 Cloud Shell 및 Service Fabric Explorer에서 Service Fabric CLI를 사용합니다. 다음 섹션에서는 이러한 도구로 보안 클러스터에 연결하는 데 필요한 인증서를 설치하는 방법을 보여줍니다.

### <a name="configure-certificate-for-the-service-fabric-cli"></a>Service Fabric CLI에 대한 인증서 구성
Cloud Shell에서 CLI를 사용하려면 인증서 PFX 파일을 Cloud Shell에 업로드한 다음, 이를 사용하여 PEM 파일을 만들어야 합니다.

1. Cloud Shell에서 인증서를 현재 작업 디렉터리에 업로드하려면 컴퓨터의 다운로드한 폴더에서 인증서 PFX 파일을 끌어서 Cloud Shell 창에 놓습니다.  

2. PFX 파일을 PEM 파일로 변환하려면 다음 명령을 사용합니다. (파티 클러스터의 경우 **추가 정보** 페이지의 지침에서 PFX 파일 및 암호에 해당하는 명령을 복사할 수 있습니다.)

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

### <a name="configure-certificate-for-service-fabric-explorer"></a>Service Fabric Explorer에 대한 인증서 구성
Service Fabric Explorer를 사용하려면 파티 클러스터 웹 사이트에서 다운로드한 인증서 PFX 파일을 인증서 저장소(Windows 또는 Mac) 또는 브라우저 자체(Ubuntu)로 가져와야 합니다. PFX 개인 키 암호가 필요하며 **추가 정보** 페이지에서 얻을 수 있습니다.

가장 편리한 방법을 사용하여 시스템에서 인증서를 가져옵니다. 예: 

- Windows: PFX 파일을 두 번 클릭하고 프롬프트에 따라 개인 저장소 `Certificates - Current User\Personal\Certificates`에 인증서를 설치합니다. 또는 **추가 정보** 지침의 PowerShell 명령을 사용할 수 있습니다.
- Mac: PFX 파일을 두 번 클릭하고 프롬프트에 따라 키 집합에 인증서를 설치합니다.
- Ubuntu: Mozilla Firefox는 Ubuntu 16.04의 기본 브라우저입니다. 인증서를 Firefox로 가져 오려면 브라우저의 오른쪽 위 모서리에 있는 메뉴 단추를 클릭한 다음 **옵션**을 클릭합니다. **기본 설정** 페이지에서 검색 상자를 사용하여 "인증서"를 검색합니다. **인증서 보기**를 클릭하고 **Your Certificates**(인증서) 탭을 선택하고 **가져오기**를 클릭하여 프롬프트에 따라 인증서를 가져옵니다.
 
   ![Firefox에서 인증서 설치](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png) 

## <a name="deploy-the-service-fabric-application"></a>Service Fabric 응용 프로그램 배포 
1. Cloud Shell에서 CLI를 사용하여 Azure에서 Service Fabric 클러스터에 연결합니다. 엔드포인트는 클러스터의 관리 엔드포인트입니다. 이전 섹션에서 PEM 파일을 만들었습니다. (파티 클러스터의 경우 **추가 정보** 페이지의 지침에서 PEM 파일 및 관리 엔드포인트에 해당하는 명령을 복사할 수 있습니다.)

    ```bash
    sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
    ```

2. 설치 스크립트를 사용하여 클러스터에 Voting 응용 프로그램 정의를 복사하고, 응용 프로그램 유형을 등록하며, 응용 프로그램의 인스턴스를 만듭니다.

    ```bash
    ./install.sh
    ```

2. 웹 브라우저를 열고 클러스터의 Service Fabric Explorer 엔드포인트로 이동합니다. 엔드포인트는 다음과 같은 형식입니다. https://\<my-azure-service-fabric-cluster-url>:19080/Explorer; 예: `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. </br>(파티 클러스터의 경우 **시작** 페이지에서 클러스터의 Service Fabric Explorer 엔드포인트를 찾을 수 있습니다.) 

3. **응용 프로그램** 노드를 확장하여 만든 Voting 응용 프로그램 유형 및 생성한 인스턴스에 대한 항목이 표시되는지 확인합니다.

    ![Service Fabric Explorer][sfx]

3. 실행 중인 컨테이너에 연결하려면 웹 브라우저를 열고 클러스터의 URL로 이동합니다. 예: `http://linh1x87d1d.westus.cloudapp.azure.com:80`. 브라우저에서 선택 응용 프로그램이 표시됩니다.

    ![Voting 앱 웹 페이지][quickstartpic]


> [!NOTE]
> Docker Compose를 사용하여 Service Fabric 응용 프로그램을 배포할 수도 있습니다. 예를 들면, 다음 명령으로 Docker Compose를 사용하여 클러스터에 응용 프로그램을 배포하고 설치할 수 있습니다.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>클러스터에서 컨테이너 장애 조치(failover)
Service Fabric은 장애가 발생할 경우 컨테이너 인스턴스가 클러스터의 다른 노드로 자동 이동하도록 합니다. 또한 수동으로 컨테이너의 노드를 드레이닝하고 클러스터의 다른 노드로 정상적으로 옮길 수 있습니다. Service Fabric은 서비스를 확장하는 여러 가지 방법을 제공합니다. 다음 단계에서는 Service Fabric Explorer를 사용합니다.

프런트 엔드 컨테이너를 장애 조치(failover)하려면 다음 단계를 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다. 예: `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`
2. 트리 뷰에서 **fabric:/Voting/azurevotefront** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다. 트리 뷰에서 노드 이름을 확인합니다. 컨테이너에서 현재 실행 중인 노드를 보여 줍니다. 예: `_nodetype_4`
3. 트리 뷰에서 **노드** 노드를 확장합니다. 컨테이너를 실행 중인 노드 옆에 있는 줄임표(...)를 클릭합니다.
4. **다시 시작**을 선택하여 해당 노드를 다시 시작하고 다시 시작 작업을 확인합니다. 다시 시작하면 컨테이너를 클러스터의 다른 노드로 장애 조치(failover)합니다.

    ![Service Fabric Explorer의 노드 보기][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>클러스터에서 응용 프로그램 및 서비스 크기 조정
Service Fabric 서비스는 해당 서비스에 대한 부하를 수용하도록 클러스터 간에 쉽게 크기를 조정할 수 있습니다. 클러스터에서 실행되는 인스턴스 수를 변경하여 서비스 크기를 조정합니다.

웹 프런트 엔드 서비스의 크기를 조정하려면 다음 단계를 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다. 예: `https://linh1x87d1d.westus.cloudapp.azure.com:19080`
2. 트리 뷰에서 **fabric:/Voting/azurevotefront** 노드 옆에 있는 줄임표(...)를 클릭하고 **Scale Service**(서비스 크기 조정)를 선택합니다.

    ![Service Fabric Explorer 크기 조정 서비스 시작][containersquickstartscale]

  이제 웹 프런트 엔드 서비스의 인스턴스 수를 조정하도록 선택할 수 있습니다.

3. 이 수를 **2**로 변경하고 **Scale Service**를 클릭합니다.
4. 트리 뷰에서 **fabric:/Voting/azurevotefront** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다.

    ![Service Fabric Explorer 크기 조정 서비스 완료][containersquickstartscaledone]

    이제 서비스에 두 인스턴스가 있는 것을 볼 수 있습니다. 트리 뷰에서 인스턴스가 실행하는 노드를 볼 수 있습니다.

이 간단한 관리 작업을 통해 프런트 엔드 서비스에서 사용 가능한 리소스를 두 배로 늘려서 사용자 부하를 처리했습니다. 서비스를 안정적으로 실행하기 위해 여러 인스턴스가 필요하지 않다는 것을 이해하는 것이 중요합니다. 서비스가 실패하면 Service Fabric은 클러스터에서 새 서비스 인스턴스가 실행되는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리
1. 템플릿에 제공된 제거 스크립트(uninstall.sh)를 사용하여 클러스터에서 응용 프로그램 인스턴스를 삭제하고 응용 프로그램 유형을 등록 해제합니다. 이 스크립트는 인스턴스를 정리하는 데 약간의 시간이 걸리므로 이 스크립트 직후에 설치 스크립트를 실행하면 안됩니다. Service Fabric Explorer를 사용하여 인스턴스가 제거되고 응용 프로그램 유형이 등록 취소된 시기를 확인할 수 있습니다. 

    ```bash
    ./uninstall.sh
    ```

2. 클러스터 작업이 완료되면 인증서를 인증서 저장소에서 제거할 수 있습니다. 예: 
   - Windows: [인증서 MMC 스냅인](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)을 사용합니다. 스냅인을 추가할 때 반드시 **내 사용자 계정**을 선택합니다. `Certificates - Current User\Personal\Certificates`로 이동하여 인증서를 제거합니다.
   - Mac: 키 집합 앱을 사용합니다.
   - Ubuntu: 인증서를 보는 데 사용한 단계를 수행하여 인증서를 제거합니다.

3. Cloud Shell을 계속 사용하지 않으려면 요금을 방지하기 위해 Cloud Shell과 연결된 저장소 계정을 삭제합니다. Cloud Shell 세션을 닫습니다. Azure Portal에서 Cloud Shell과 연결된 저장소 계정을 클릭한 다음 페이지 상단의 **삭제**를 클릭하고 프롬프트에 응답합니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Azure에서 Service Fabric 클러스터에 Linux 컨테이너 응용 프로그램을 배포하고, 응용 프로그램에서 장애 조치(failover)를 수행하고, 클러스터에서 응용 프로그램을 확장했습니다. Service Fabric에서 Linux 컨테이너 작업에 대해 자세히 알아보려면 Linux 컨테이너 앱에 대한 자습서를 계속 진행하십시오.

> [!div class="nextstepaction"]
> [Linux 컨테이너 앱 만들기](./service-fabric-tutorial-create-container-images.md)


[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
