---
title: 로컬로 원격 모니터링 솔루션 배포 - Azure | Microsoft Docs
description: 이 자습서에서는 테스트 및 개발을 위해 원격 모니터링 솔루션 가속기를 로컬 컴퓨터에 배포하는 방법을 보여줍니다.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: a11df1dc17b4dcbacece85526eeac39502cbbe34
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>로컬로 원격 모니터링 솔루션 가속기 배포

이 문서에서는 테스트 및 개발을 위해 원격 모니터링 솔루션 가속기를 로컬 컴퓨터에 배포하는 방법을 보여줍니다. 이 방법은 마이크로 서비스를 로컬 Docker 컨테이너에 배포하고, 클라우드에서 IoT Hub, Cosmos DB 및 Azure 저장소 서비스를 사용합니다. PCS(솔루션 가속기) CLI를 사용하여 Azure 클라우드 서비스를 배포합니다.

## <a name="prerequisites"></a>필수 조건

원격 모니터링 솔루션 가속기에서 사용되는 Azure 서비스를 배포하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

로컬 배포를 완료하기 위해 로컬 개발 컴퓨터에 설치되어 있어야 하는 도구는 다음과 같습니다.

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker 작성](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) - 이 소프트웨어는 PCS CLI에 대한 필수 구성 요소입니다.
* PCS CLI
* 소스 코드의 로컬 리포지토리

> [!NOTE]
> 이러한 도구는 Windows, Linux 및 iOS를 포함한 다양한 플랫폼에서 사용할 수 있습니다.

### <a name="install-the-pcs-cli"></a>PCS CLI 설치

npm을 통해 PCS CLI를 설치하려면 명령줄 환경에서 다음 명령을 실행합니다.

```cmd/sh
npm install iot-solutions -g
```

CLI에 대한 자세한 내용은 [CLI를 사용하는 방법](https://github.com/Azure/pcs-cli/blob/master/README.md)을 참조하세요.

### <a name="download-the-source-code"></a>소스 코드 다운로드

 원격 모니터링 소스 코드 리포지토리에는 마이크로 서비스가 포함된 Docker 이미지를 다운로드, 구성 및 실행하는 데 필요한 Docker 구성 파일이 포함되어 있습니다. 리포지토리의 로컬 버전을 복제하고 만들려면 원하는 명령줄 또는 터미널을 통해 로컬 컴퓨터의 적합한 폴더로 이동하고 다음 명령 중 하나를 실행합니다.

마이크로 서비스의 Java 구현을 설치하려면 다음을 실행합니다.

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

마이크로 서비스의 .Net 구현을 설치하려면 다음을 실행합니다.

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

미리 구성된 원격 모니터링 솔루션 리포지토리 및 하위 모듈 [ [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) ]

> [!NOTE]
> 이러한 명령은 모든 마이크로 서비스에 대한 소스 코드를 다운로드합니다. Docker에서 마이크로 서비스를 실행하는 데는 소스 코드가 필요하지 않지만, 나중에 미리 구성된 솔루션을 수정하고 변경 내용을 로컬로 테스트하려는 경우에는 소스 코드가 유용합니다.

## <a name="deploy-the-azure-services"></a>Azure 서비스 배포

이 문서에서는 마이크로 서비스를 로컬로 실행하는 방법을 보여 주지만 클라우드에서 실행되는 3개의 Azure 서비스를 사용합니다. 이러한 Azure 서비스를 [Azure Portal](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup)을 통해 수동으로 배포하거나 PCS CLI를 사용할 수 있습니다. 이 문서에서는 `pcs` 도구를 사용하는 방법을 보여 줍니다.

### <a name="sign-in-to-the-cli"></a>CLI에 로그인

솔루션 가속기를 배포하기 전에 다음과 같이 CLI를 사용하여 Azure 구독에 로그인해야 합니다.

```cmd/sh
pcs login
```

화면의 지시에 따라 로그인 프로세스를 완료합니다. CLI 내의 어떤 곳도 클릭하지 않도록 합니다. 그렇지 않으면 로그인에 실패할 수 있습니다. 로그인을 완료한 경우에 CLI에 성공적인 로그인 메시지가 나타납니다. 

### <a name="run-a-local-deployment"></a>로컬 배포 실행

다음 명령을 사용하여 로컬 배포를 시작합니다. 필요한 azure 리소스를 만들고 환경 변수를 teh 콘솔에 출력합니다. 

```cmd/pcs
pcs -s local
```

스크립트에서 다음 정보를 요구하는 메시지가 표시됩니다.

* 솔루션 이름
* 사용할 Azure 구독입니다.
* 사용할 Azure 데이터 센터의 위치

> [!NOTE]
> 이 스크립트는 Azure 구독의 리소스 그룹에 IoT Hub 인스턴스, Cosmos DB 인스턴스 및 Azure 저장소 계정을 만듭니다. 리소스 그룹의 이름은 위의 `pcs` 도구를 실행할 때 선택한 솔루션의 이름입니다. 

> [!IMPORTANT]
> 스크립트를 실행하는 데 몇 분이 걸립니다. 완료되면 `Copy the following environment variables to /scripts/local/.env file:` 메시지가 표시됩니다. 메시지 뒤에 나오는 환경 변수 정의를 복사하여 나중의 단계에서 사용합니다.

## <a name="run-the-microservices-in-docker"></a>Docker에서 마이크로 서비스 실행

Docker에서 마이크로 서비스를 실행하려면, 먼저 위의 이전 단계에서 복제한 리포지토리의 로컬 복사본에서 **scripts\\local\\.env** 파일을 편집합니다. 파일의 전체 내용을 마지막 단계에 `pcs` 명령을 실행했을 적어둔 환경 변수 정의로 바꿉니다. 이러한 환경 변수를 사용하면 Docker 컨테이너의 마이크로 서비스에서 `pcs` 도구로 만든 Azure 서비스에 연결할 수 있습니다.

솔루션 가속기를 실행하려면 명령줄 환경의 **scripts\local** 폴더로 이동하고 다음 명령을 실행합니다.

```cmd\sh
docker-compose up
```

이 명령을 처음 실행하는 경우 Docker는 Docker 허브에서 마이크로 서비스 이미지를 다운로드하여 컨테이너를 로컬로 빌드합니다. 후속 실행에서 Docker는 컨테이너를 즉시 실행합니다.

별도의 셸을 사용하여 컨테이너에서 로그를 볼 수 있습니다. 먼저 `docker ps -a` 명령을 사용하여 컨테이너 ID를 찾습니다. 그런 다음, `docker logs {container-id} --tail 1000`을 사용하여 지정된 컨테이너에 대한 마지막 1,000개의 로그 항목을 봅니다.

원격 모니터링 솔루션 대시보드에 액세스하려면 브라우저에서 [http://localhost:8080](http://localhost:8080)으로 이동합니다.

## <a name="clean-up"></a>정리

불필요한 요금을 방지하려면 테스트를 마친 후 Azure 구독에서 클라우드 서비스를 제거합니다. 서비스를 제거하는 가장 쉬운 방법은 [Azure Portal](https://ms.portal.azure.com)로 이동하고 `pcs` 도구를 통해 만든 리소스 그룹을 삭제하는 것입니다.

`docker-compose down --rmi all` 명령을 사용하여 Docker 이미지를 제거하고 로컬 컴퓨터의 공간을 확보합니다. 또한 GitHub에서 소스 코드를 복제할 때 만들어진 원격 모니터링 리포지토리의 로컬 복사본을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 로컬 개발 환경 설정
> * 솔루션 가속기 구성
> * 솔루션 가속기 배포
> * 솔루션 가속기에 로그인

이제 원격 모니터링 솔루션을 배포했으며 다음 단계는 [솔루션 대시보드의 기능을 탐색](iot-accelerators-remote-monitoring-deploy.md)하는 것입니다.

<!-- Next tutorials in the sequence -->