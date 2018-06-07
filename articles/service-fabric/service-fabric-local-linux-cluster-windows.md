---
title: Windows에서 Azure Service Fabric Linux 클러스터 설정 | Microsoft Docs
description: 이 문서에서는 Windows 개발 컴퓨터에서 실행되는 Service Fabric Linux 클러스터를 설정하는 방법을 알아봅니다. 이 기능은 크로스 플랫폼 개발에 특히 유용합니다.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: ad0d383888c173ece5a7fbd3b0de690ed13074f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Windows 개발자 컴퓨터에서 Linux Service Fabric 클러스터 설정

이 문서에서는 Windows 개발 컴퓨터에 로컬 Linux Service Fabric을 설정하는 방법을 설명합니다. 로컬 Linux 클러스터를 설정하는 경우 Linux 클러스터를 대상으로 하지만 Windows 컴퓨터에서 개발되는 응용 프로그램을 빠르게 테스트하는 데 유용합니다.

## <a name="prerequisites"></a>필수 조건
Linux 기반 Service Fabric 클러스터는 기본적으로 Windows에서 실행되지 않습니다. 로컬 Service Fabric 클러스터를 실행하기 위해 미리 구성된 Docker 컨테이너 이미지를 제공합니다. 시작하기 전에 다음 항목이 필요합니다.

* RAM 4GB 이상
* 최신 버전의 [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)

>[!TIP]
> * 공식 Docker [설명서](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)에서 언급한 단계를 따라 Windows에 Docker를 설치할 수 있습니다. 
> * 설치가 완료되면 [여기](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)에서 언급된 단계를 따라 적절하게 설치되었는지 유효성을 검사합니다.


## <a name="create-a-local-container-and-setup-service-fabric"></a>로컬 컨테이너 만들기 및 Service Fabric 설치
로컬 Docker 컨테이너를 설정하고 서비스 패브릭 클러스터가 실행되도록 하려면 PowerShell에서 다음 단계를 수행합니다.

1. Docker 허브 리포지토리에서 이미지를 끌어옵니다.

    ```powershell
    docker pull microsoft/service-fabric-onebox
    ```

2. 다음을 사용하여 호스트에서 Docker 디먼 구성을 업데이트하고 Docker 디먼을 다시 시작합니다. 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    권장되는 업데이트 방법은 Docker 아이콘 > 설정 > 디먼 > 고급으로 이동하고 업데이트하는 것입니다. 다음으로 변경 내용을 적용하려면 Docker 디먼을 다시 시작합니다. 

3. 이미지와 함께 Service Fabric One-box 컨테이너 인스턴스를 시작합니다.

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    > * 컨테이너 인스턴스의 이름을 지정하여 보다 읽기 쉬운 방식으로 처리할 수 있습니다. 
    > * 응용 프로그램을 특정 포트에서 수신하는 경우 추가 -p 태그를 사용하여 지정해야 합니다. 예를 들어 응용 프로그램이 포트 8080에서 수신하는 경우 docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox를 실행합니다.

4. 대화형 ssh 모드에서 Docker 컨테이너에 로그인합니다.

    ```powershell
    docker exec -it sfonebox bash
    ```

5. 필요한 종속성을 인출하는 설치 스크립트를 실행하고 그 후 컨테이너에서 클러스터를 시작합니다.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. 5단계를 성공적으로 완료한 후 Windows에서 ``http://localhost:19080``으로 이동하고 Service Fabric 탐색기를 볼 수 있습니다. 이때 Windows 개발자 컴퓨터의 도구를 사용하여 이 클러스터에 연결하고 Linux Service Fabric 클러스터용 응용 프로그램을 배포할 수 있습니다. 

    > [!NOTE]
    > Eclipse 플러그 인은 현재 Windows에서 지원되지 않습니다. 

## <a name="next-steps"></a>다음 단계
* [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse) 시작
* 다른 [Java 샘플](https://github.com/Azure-Samples/service-fabric-java-getting-started) 확인


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png