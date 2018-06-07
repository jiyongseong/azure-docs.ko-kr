---
title: Azure에서 Linux용 Docker VM 확장 사용
description: Docker 및 Azure Virtual Machines 확장에 대해 설명하고, Azure CLI를 사용하여 명령줄에서 Docker 호스트인 Virtual Machines를 Azure에 프로그래밍 방식으로 만드는 방법을 안내합니다.
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
ms.openlocfilehash: 91f7ea54afce0e94953d4bb01bbb1b33f167fe22
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Azure 명령줄 인터페이스(Azure CLI)에서 Docker VM 확장 사용
> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Resource Manager 모델에서 Docker VM 확장을 사용하는 방법에 대한 정보는 [여기](../dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

이 항목에서는 모든 플랫폼의 Azure CLI에서 서비스 관리(asm) 모드로 Docker VM 확장을 사용하여 VM을 만드는 방법을 설명합니다. [Docker](https://www.docker.com/)는 공유 리소스의 데이터와 계산을 격리시키는 한 가지 방법으로 가상 머신 대신 [Linux 컨테이너](http://en.wikipedia.org/wiki/LXC)를 사용하는 가장 많이 사용되는 가상화 방법 중 하나입니다. [Azure Linux 에이전트](../../extensions/agent-linux.md)에 대한 Docker VM 확장을 사용하여 Azure에 응용 프로그램의 컨테이너를 개수에 제한없이 호스트하는 Docker VM을 만들 수 있습니다. 컨테이너와 해당 이점에 대한 간략한 설명을 확인하려면 [Docker 요약 화이트보드](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)를 참조하세요.

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Azure와 함께 Docker VM 확장을 사용하는 방법
Azure와 함께 Docker VM 확장을 사용하려면 [Azure 명령줄 인터페이스](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) 0.8.6 이상 버전을 설치해야 합니다(이 문서를 작성할 당시 현재 버전은 0.10.0임). Mac, Linux 및 Windows에 Azure CLI를 설치할 수 있습니다.

Azure에서 Docker를 사용하는 전체 프로세스는 간단합니다.

* Azure를 제어하려는 컴퓨터(Windows의 경우 가상 머신으로 실행 중인 Linux 배포임)에 Azure CLI와 해당 종속성을 설치합니다.
* Azure CLI Docker 명령을 사용하여 Azure에 VM Docker 호스트를 만듭니다.
* 로컬 Docker 명령을 사용하여 Azure의 Docker VM에서 Docker 컨테이너를 관리합니다.

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Azure 명령줄 인터페이스(Azure CLI) 설치
Azure CLI를 설치하고 구성하려면, [Azure 명령줄 인터페이스를 설치하는 방법](../../../cli-install-nodejs.md)을 참조하세요. 설치를 확인하려면 명령줄에 `azure`를 입력합니다. 잠시 후에 사용 가능한 기본 명령을 나열하는 Azure CLI ASCII 아트가 표시됩니다. 설치가 제대로 작동한 경우 `azure help vm`을 입력하면 나열된 명령 중 하나가 "docker"임을 확인할 수 있습니다.

> [!NOTE]
> Docker에 Windows용 도구, [Docker Machine](https://docs.docker.com/installation/windows/)이 있으며, docker 호스트로 Azure VM과 작업에 사용할 수 있는 docker 클라이언트 작성을 자동화할 수도 있습니다.
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Azure CLI를 Azure 계정에 연결
Azure CLI를 사용하려면 먼저 Azure 계정 자격 증명을 사용자 플랫폼의 Azure CLI에 연결해야 합니다. [Azure 구독에 연결하는 방법](/cli/azure/authenticate-azure-cli) 섹션에서 **.publishsettings** 파일을 다운로드하고 가져오거나 Azure CLI를 조직 ID에 연결하는 방법에 대해 설명합니다.

> [!NOTE]
> 사용하는 인증 방법에 따라 동작에 몇 가지 차이점이 있으므로 위 문서를 읽고 서로 다른 기능을 이해해야 합니다.
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Docker 설치 및 Azure용 Docker VM 확장 사용
사용자 컴퓨터에 Docker를 로컬로 설치하려면 [Docker 설치 지침](https://docs.docker.com/installation/#installation) 을 따르세요.

Azure Virtual Machine에서 Docker를 사용하려면 VM에 사용하는 Linux 이미지에 [Azure Linux VM 에이전트](../../extensions/agent-linux.md)가 설치되어 있어야 합니다. 현재 이 에이전트를 설치할 수 있는 이미지의 유형은 다음의 두 가지뿐입니다.

* Azure 이미지 갤러리의 Ubuntu 이미지 또는
* Azure Linux VM 에이전트를 설치 및 구성하여 만든 사용자 지정 Linux 이미지. Azure VM 에이전트로 사용자 지정 Linux VM을 빌드하는 방법에 대한 자세한 내용은 [Azure Linux VM 에이전트](../../extensions/agent-linux.md)를 참조하세요.

### <a name="using-the-azure-image-gallery"></a>Azure 이미지 갤러리 사용
Bash 또는 터미널 세션에서 다음 Azure CLI 명령을 사용하여 VM 갤러리에서 사용하려는 최신 Ubuntu 이미지를 찾아 다음을 입력하여 사용합니다.

`azure vm image list | grep Ubuntu-14_04`

`b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`와 같은 이미지 이름 중 하나를 선택한 후에 다음 명령을 사용하여 해당 이미지를 사용하는 새 VM을 만듭니다.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

설명:

* *&lt;vm-cloudservice name&gt;* 은 Azure에서 Docker 컨테이너 호스트 컴퓨터로 사용할 VM의 이름입니다.
* *&lt;username&gt;* 은 VM의 기본 루트 사용자의 사용자 이름입니다.
* *&lt;password&gt;* 는 Azure의 복잡성 표준을 충족하는 *username* 계정의 암호입니다.

> [!NOTE]
> 현재 암호는 8자 이상이어야 하며 소문자, 대문자, 숫자 및 다음 문자 중 하나와 같은 특수 문자를 각각 한 개씩 포함해야 합니다. `!@#$%^&+=` 앞의 문장에서 끝에 있는 마침표는 특수 문자가 아닙니다.
> 
> 

명령이 성공한 경우 사용한 정확한 인수와 옵션에 따라 다음과 같이 표시됩니다.

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> 가상 머신을 만들려면 몇 분 정도 걸릴 수 있습니다. 가상 머신이 프로비전된 후에는(상태 값 `ReadyRole`) Docker 데몬(Docker 서비스)이 시작되며 Docker 컨테이너 호스트에 연결할 수 있습니다.
> 
> 

Azure에 만든 Docker VM을 테스트하려면 다음을 입력합니다.

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

여기서 *&lt;vm-name-you-used&gt;* 는 `azure vm docker create`를 호출하는 데 사용한 가상 머신의 이름입니다. 다음과 같은 결과가 표시됩니다. 이 결과는 Azure에서 Docker 호스트 VM이 작동하여 실행 중이며 명령을 기다리고 있음을 나타냅니다. 

이제 정보를 얻기 위해 Docker 클라이언트를 사용하여 연결을 시도할 수 있습니다(Mac과 같은 일부 Docker 클라이언트 설정에서는 `sudo`를 사용해야 할 수 있음).

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

모두 작동하고 있는지 확인하기 위해 VM의 Docker 확장을 살펴볼 수 있습니다.

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Docker Host VM 인증
Docker VM을 만드는 것뿐만 아니라 `azure vm docker create` 명령은 Docker 클라이언트 컴퓨터가 HTTPS를 사용하여 Azure 컨테이너 호스트에 연결할 수 있도록 필요한 인증서도 자동으로 만듭니다. 인증서는 해당하는 경우 클라이언트와 호스트 컴퓨터 둘 다에 저장됩니다. 이후에 시도할 때는 기존 인증서가 다시 사용되며 새 호스트와 공유됩니다.

기본적으로 인증서는 `~/.docker`에 배치되고 Docker는 포트 **2376**에서 실행되도록 구성됩니다. 다른 포트나 디렉터리를 사용하려는 경우 다음 `azure vm docker create` 명령줄 옵션 중 하나를 사용하여 클라이언트 연결에 다른 포트나 다른 인증서를 사용하도록 Docker 컨테이너 호스트 VM을 구성할 수 있습니다.

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

호스트의 Docker 데몬은 `azure vm docker create` 명령에서 생성된 인증서를 사용하여 지정된 포트에서 클라이언트 연결을 수신 대기하고 인증하도록 구성됩니다. 클라이언트 컴퓨터가 Docker 호스트에 액세스하려면 해당 인증서가 있어야 합니다.

> [!NOTE]
> 해당 인증서 없이 실행되는 네트워크 호스트는 컴퓨터에 연결할 수 있는 모든 사용자에게 취약합니다. 기본 구성을 수정하기 전에 컴퓨터와 응용 프로그램에 대한 위험을 이해해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* 이제 [Docker 사용자 가이드] 로 이동하여 Docker VM을 사용할 수 있습니다. 새 포털에서 Docker 사용 가능 VM을 만들려면 [포털에서 Docker VM 확장을 사용하는 방법]을 참조하세요.
* Azure Docker VM 확장은 또한 개발자 모델링된 응용 프로그램을 모든 환경에 가져가고 일관된 배포를 생성하기 위해 선언적 YAML 파일을 사용하는 Docker Compose를 지원합니다. [Azure 가상 머신에서 다중 컨테이너 응용 프로그램 정의 및 실행을 위해 Docker 및 Compose 시작]을 참조하세요.  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[포털에서 Docker VM 확장을 사용하는 방법]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Docker 사용자 가이드]:https://docs.docker.com/userguide/

[Azure 가상 머신에서 다중 컨테이너 응용 프로그램 정의 및 실행을 위해 Docker 및 Compose 시작]:../docker-compose-quickstart.md
