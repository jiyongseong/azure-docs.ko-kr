---
title: Java 원격 모니터링 솔루션 배포 - Azure | Microsoft Docs
description: 이 자습서는 CLI를 사용하여 원격 모니터링 솔루션 가속기를 프로비전하는 방법을 보여줍니다.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 3178d51cd2c04f3be8d4a6284a4f1635845def8c
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>CLI를 사용하여 원격 모니터링 솔루션 가속기 배포

이 자습서는 원격 모니터링 솔루션 가속기를 프로비전하는 방법을 보여줍니다. CLI를 사용하여 솔루션을 배포합니다. azureiotsuite.com에서 웹 기반 UI를 사용하여 솔루션을 배포할 수도 있습니다. 이 옵션에 대해 알아보려면 [원격 모니터링 솔루션 가속기 배포](iot-accelerators-remote-monitoring-deploy.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

원격 모니터링 솔루션 가속기를 배포하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

CLI를 실행하려면 [Node.js](https://nodejs.org/)를 로컬 컴퓨터에 설치해야 합니다.

## <a name="install-the-cli"></a>CLI 설치

CLI를 설치하려면 명령줄 환경에서 다음 명령을 실행합니다.

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>CLI에 로그인

솔루션 가속기를 배포하기 전에 다음과 같이 CLI를 사용하여 Azure 구독에 로그인해야 합니다.

```cmd/sh
pcs login
```

화면의 지시에 따라 로그인 프로세스를 완료합니다.

## <a name="deployment-options"></a>배포 옵션

솔루션 가속기를 배포하는 경우 배포 프로세스를 구성하는 여러 옵션이 있습니다.

| 옵션 | 값 | 설명 |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | _기본_ 배포는 테스트 및 데모를 위한 것이며 단일 가상 머신에 모든 마이크로 서비스를 배포합니다. _표준_ 배포는 프로덕션을 위한 것이며 여러 가상 머신에 마이크로 서비스를 배포합니다. _로컬_ 배포를 통해 로컬 컴퓨터에서 마이크로 서비스를 실행하고, 클라우드에서 저장소 및 Cosmos DB와 같은 Azure 서비스를 사용하도록 Docker 컨테이너를 구성합니다. |
| 런타임 | `dotnet`, `java` | 마이크로 서비스의 언어 구현을 선택합니다. |

로컬 배포를 사용하는 방법에 대한 자세한 내용은 [원격 모니터링 솔루션 로컬로 실행](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables)을 참조하세요.

## <a name="basic-vs-standard-deployments"></a>기본 및 표준 배포

### <a name="basic"></a>Basic
기본 배포는 솔루션을 보여주기 위한 것입니다. 이 데모의 비용을 줄이기 위해 모든 마이크로 서비스가 단일 가상 머신에 배포됩니다. 이것은 프로덕션에 사용 가능한 아키텍처로 간주되지 않습니다.

크기 조정 및 확장성을 위해 작성된 프로덕션 사용 가능 아키텍처를 사용자 지정할 준비가 되면 표준 배포 옵션을 사용해야 합니다.

기본 솔루션을 만들면 다음 Azure 서비스가 원가로 Azure 구독에서 프로비전됩니다. 

| 개수 | 리소스                       | type         | 용도 |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux 가상 머신](https://azure.microsoft.com/services/virtual-machines/) | 표준 D1 V2  | 마이크로 서비스 호스트 |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – 기본 계층 | 장치 관리 및 통신 |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | 구성 데이터와 규칙, 경보 및 메시지와 같은 장치 원격 분석 저장 |  
| 1     | [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | VM 및 스트리밍 검사점에 대한 저장소 |
| 1     | [웹 응용 프로그램](https://azure.microsoft.com/services/app-service/web/)        |                 | 프런트 엔드 웹 응용 프로그램 호스트 |

### <a name="standard"></a>Standard
표준 배포는 개발자가 요구에 맞게 사용자 지정하고 확장할 수 있는 프로덕션 사용 가능 배포입니다. 안정성과 확장성을 위해 응용 프로그램 마이크로 서비스는 Docker 컨테이너로 빌드되고 오케스트레이터(기본적으로 [Kubernetes](https://kubernetes.io/))를 사용하여 배포됩니다. 오케스트레이터는 응용 프로그램의 배포, 확장 및 관리를 담당합니다.

표준 솔루션을 만들면 다음 Azure 서비스가 원가로 Azure 구독에서 프로비전됩니다.

| 개수 | 리소스                                     | SKU/크기      | 용도 |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Linux Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)   | 표준 D2 V2  | 중복성을 위해 마이크로 서비스를 호스트하기 위한 1개 마스터 및 3개 에이전트 |
| 1     | [Azure Container Service](https://azure.microsoft.com/services/container-service/) |                 | [Kubernetes](https://kubernetes.io) Orchestrator |
| 1     | [Azure IoT Hub][https://azure.microsoft.com/services/iot-hub/]                     | S1 – 기본 계층 | 장치 관리, 명령 및 제어 |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | 구성 데이터와 규칙, 경보 및 메시지와 같은 장치 원격 분석 저장 |
| 5     | [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | VM 저장소용 4개, 스트리밍 검사점용 1개 |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 표준     | SSL을 통한 Application Gateway |

> 이러한 서비스에 대한 가격 책정 정보는 [여기](https://azure.microsoft.com/pricing)에서 확인할 수 있습니다. 구독에 대한 사용 금액 및 청구 정보는 [Azure Portal](https://portal.azure.com/)에서 찾을 수 있습니다.

## <a name="deploy-the-solution-accelerator"></a>솔루션 가속기 배포

### <a name="example-deploy-net-version"></a>예제: .NET 버전 배포

다음 예제는 원격 모니터링 솔루션 가속기의 기본, .NET 버전을 배포하는 방법을 보여줍니다.

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>예제: Java 버전 배포

다음 예제는 원격 모니터링 솔루션 가속기의 표준, Java 버전을 배포하는 방법을 보여줍니다.

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs 명령 옵션

`pcs` 명령을 실행하여 솔루션을 배포하는 경우 다음을 지정해야 합니다.

- 솔루션에 대한 이름 이 이름은 고유해야 합니다.
- 사용할 Azure 구독입니다.
- 위치
- 마이크로 서비스를 호스팅하는 가상 머신에 대한 자격 증명 이러한 자격 증명을 사용하여 문제 해결을 위해 가상 머신에 액세스할 수 있습니다.

`pcs` 명령이 완료되면 새 솔루션 가속기 배포의 URL을 표시합니다. `pcs` 명령 또한 프로비전된 IoT Hub의 이름과 같은 추가 정보로 `{deployment-name}-output.json` 파일을 만듭니다.

명령줄 매개 변수에 대한 자세한 내용은 다음을 실행합니다.

```cmd/sh
pcs -h
```

CLI에 대한 자세한 내용은 [CLI를 사용하는 방법](https://github.com/Azure/pcs-cli/blob/master/README.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 솔루션 가속기 구성
> * 솔루션 가속기 배포
> * 솔루션 가속기에 로그인

이제 원격 모니터링 솔루션을 배포했으며 다음 단계는 [솔루션 대시보드의 기능을 탐색](./iot-accelerators-remote-monitoring-deploy.md)하는 것입니다.

<!-- Next tutorials in the sequence -->