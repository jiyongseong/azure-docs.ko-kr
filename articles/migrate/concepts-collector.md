---
title: Azure Migrate의 Collector 어플라이언스 | Microsoft Docs
description: Collector 어플라이언스에 대한 개요 및 구성 방법을 제공합니다.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: d0dd310a1f6dff389a4d3dd41dc389b7117272fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203685"
---
# <a name="collector-appliance"></a>Collector 어플라이언스

[Azure Migrate](migrate-overview.md)는 Azure로 마이그레이션하는 온-프레미스 워크로드를 평가합니다. 이 문서는 Collector 어플라이언스 사용 방법에 대한 정보를 제공합니다.



## <a name="overview"></a>개요

Azure Migrate Collector는 온-프레미스 vCenter 환경을 검색하는 데 사용할 수 있는 경량 어플라이언스입니다. 이 어플라이언스는 온-프레미스 VMware 컴퓨터를 검색하여 관련 메타데이터를 Azure Migrate 서비스에 보냅니다.

Collector 어플라이언스는 Azure Migrate 프로젝트에서 다운로드할 수 있는 OVF입니다. 4코어, 8GB RAM 및 80GB 디스크가 한 개 있는 VMware 가상 머신을 인스턴스화합니다. 어플라이언스의 운영 체제는 Windows Server 2012 R2(64 비트)입니다.

Collector는 다음 단계에 따라 만들 수 있습니다. - [Collector VM을 만드는 방법](tutorial-assessment-vmware.md#create-the-collector-vm)

## <a name="collector-communication-diagram"></a>수집기 통신 다이어그램

![수집기 통신 다이어그램](./media/tutorial-assessment-vmware/portdiagram.PNG)


| 구성 요소      | 통신 대상   | 필요한 포트                            | 이유                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| 데이터 수집기      | Azure Migrate 서비스 | TCP 443                                  | 수집기는 SSL 포트 443을 통해 서비스와 통신할 수 있어야 합니다. |
| 데이터 수집기      | vCenter Server        | 기본값 443                             | 수집기는 vCenter Server와 통신할 수 있어야 합니다. 기본적으로 443에서 vCenter에 연결합니다. vCenter가 다른 포트에서 수신 대기하는 경우 해당 포트는 수집기에서 송신 포트로 제공되어야 합니다. |
| 데이터 수집기      | RDP|   | TCP 3389 | 수집기 컴퓨터로 RDP할 수 있는 사용자의 경우 |





## <a name="collector-pre-requisites"></a>Collector 필수 조건

Collector는 Azure Migrate 서비스에 연결하여 검색된 데이터를 업로드할 수 있도록 몇 가지 필수 조건 검사를 통과해야 합니다. 이 문서에서는 각 필수 구성 요소를 살펴보고 필요한 이유를 파악합니다.

### <a name="internet-connectivity"></a>인터넷 연결

검색된 컴퓨터 정보를 전송하려면 수집기 어플라이언스가 인터넷에 연결되어야 합니다. 컴퓨터는 다음 두 가지 방법 중 하나로 인터넷에 연결할 수 있습니다.

1. 인터넷에 직접 연결되도록 Collector를 구성할 수 있습니다.
2. 프록시 서버를 통해 연결하도록 Collector를 구성할 수 있습니다.
    * 프록시 서버에 인증이 필요한 경우 연결 설정에서 사용자 이름과 암호를 지정할 수 있습니다.
    * 프록시 서버의 IP 주소/FQDN은 http://IPaddress 또는 http://FQDN 형식이어야 합니다. HTTP 프록시만 지원됩니다.

> [!NOTE]
> HTTPS 기반 프록시 서버는 수집기에서 지원되지 않습니다.

#### <a name="whitelisting-urls-for-internet-connection"></a>인터넷 연결을 위한 URL 허용 목록

Collector가 제공된 설정을 통해 인터넷에 연결할 수 있으면 필수 조건 검사가 완료된 것입니다. 다음 표에 있는 URL 목록에 연결하여 연결 확인의 유효성을 검사합니다. URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 다음과 같은 필수 URL을 허용 목록에 포함해야 합니다.

**URL** | **목적**  
--- | ---
*.portal.azure.com | Azure 서비스와의 연결을 확인하고 시간 동기화 문제의 유효성을 검사하는 데 필요합니다.

또한 이 검사는 다음 URL에 대한 연결의 유효성을 검사하려고 하지만, 액세스할 수 없더라도 검사에 실패하지는 않습니다. 다음 URL에 대한 허용 목록을 구성하는 것은 선택 사항이지만 필수 조건 검사를 줄이려면 수동 단계를 진행해야 합니다.

**URL** | **목적**  | **허용 목록을 만들지 않으면 어떻게 되나요?**
--- | --- | ---
*.oneget.org:443 | powershell 기반 vCenter PowerCLI 모듈을 다운로드하는 데 필요합니다. | PowerCLI 설치에 실패합니다. 모듈을 수동으로 설치합니다.
*.windows.net:443 | powershell 기반 vCenter PowerCLI 모듈을 다운로드하는 데 필요합니다. | PowerCLI 설치에 실패합니다. 모듈을 수동으로 설치합니다.
*.windowsazure.com:443 | powershell 기반 vCenter PowerCLI 모듈을 다운로드하는 데 필요합니다. | PowerCLI 설치에 실패합니다. 모듈을 수동으로 설치합니다.
*.powershellgallery.com:443 | powershell 기반 vCenter PowerCLI 모듈을 다운로드하는 데 필요합니다. | PowerCLI 설치에 실패합니다. 모듈을 수동으로 설치합니다.
*.msecnd.net:443 | powershell 기반 vCenter PowerCLI 모듈을 다운로드하는 데 필요합니다. | PowerCLI 설치에 실패합니다. 모듈을 수동으로 설치합니다.
*.visualstudio.com:443 | powershell 기반 vCenter PowerCLI 모듈을 다운로드하는 데 필요합니다. | PowerCLI 설치에 실패합니다. 모듈을 수동으로 설치합니다.

### <a name="time-is-in-sync-with-the-internet-server"></a>인터넷 서버와 시간 동기화

서비스에 대한 요청이 인증되었는지 확인하려면 Collector가 인터넷 시간 서버와 동기화 되어야 합니다. 시간 유효성을 검사할 수 있도록 Collector에서 portal.azure.com URL에 연결할 수 있어야 합니다. 컴퓨터가 동기화되지 않은 경우, Collector VM의 시계 시간이 현재 시간과 일치하도록 다음과 같이 변경해야 합니다.

1. VM에서 관리자 명령 프롬프트를 엽니다.
1. 표준 시간대를 확인하려면 w32tm /tz 명령을 실행합니다.
1. 시간을 동기화하려면 w32tm /resync 명령을 실행합니다.

### <a name="collector-service-should-be-running"></a>Collector 서비스를 실행 중이어야 함

Azure Migrate Collector 서비스를 시스템에서 실행 중이어야 합니다. 서비스는 시스템이 부팅될 때 자동으로 시작됩니다. 서비스를 실행하고 있지 않으면 제어판을 통해 *Azure Migrate Collector* 서비스를 시작할 수 있습니다. Collector 서비스는 vCenter 서버에 연결하고 컴퓨터 메타데이터 및 성능 데이터를 수집하여 서비스로 전송하는 작업을 담당합니다.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5

Collector가 vCenter Server와 통신하고 시스템 세부 정보 및 성능 데이터를 쿼리할 수 있도록 VMware PowerCLI PowerShell 모듈을 설치해야 합니다. PowerShell 모듈은 필수 조건 검사의 일환으로 자동으로 다운로드되고 설치됩니다. 자동 다운로드를 수행하려면 몇 가지 URL을 허용 목록에 추가해야 하며, 그렇게 하지 않으면 해당 URL을 허용 목록에 추가하여 액세스를 제공하거나 모듈을 수동으로 설치해야 합니다.

다음 단계를 사용하여 수동으로 모듈을 설치합니다.

1. 인터넷 연결 없이 Collector에 PowerCli를 설치하려면 [이 링크](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html)에 있는 단계를 수행합니다.
2. 인터넷에 액세스할 수 있는 다른 컴퓨터에 PowerShell 모듈을 설치한 다음, 해당 컴퓨터에서 Collector 컴퓨터로 VMware. * 파일을 복사합니다.
3. 필수 조건 검사를 다시 시작하여 PowerCLI가 설치되었는지 확인합니다.

## <a name="connecting-to-vcenter-server"></a>vCenter Server에 연결

Collector는 vCenter Server에 연결되어야 하고 가상 머신, 가상 머신의 메타데이터 및 성능 카운터를 쿼리할 수 있어야 합니다. 이 데이터는 프로젝트에서 평가를 계산하는 데 사용됩니다.

1. vCenter Server에 연결하려면, 다음 표에 제시된 권한이 있는 읽기 전용 계정을 사용하여 검색을 실행합니다.

    |Task  |필요한 역할/계정  |권한  |
    |---------|---------|---------|
    |수집기 어플라이언스 기반 검색    | 최소한 읽기 전용 사용자 필요        |데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용         |

2. 지정된 vCenter 계정으로 액세스할 수 있는 데이터 센터만 검색을 위해 액세스할 수 있습니다.
3. vCenter Server에 액세스하려면 vCenter FQDN/IP 주소를 지정해야 합니다. 기본적으로 포트 443을 통해 연결됩니다. 다른 포트 번호에서 수신 대기하도록 vCenter를 구성한 경우, IPAddress: Port_Number 또는 FQDN: Port_Number 형식으로 서버 주소의 일부를 지정할 수 있습니다.
4. vCenter Server에 대한 통계 설정은 배포를 시작하기 전에 수준 3으로 설정해야 합니다. 수준이 3보다 낮은 경우 검색이 완료되지만 저장소 및 네트워크에 대한 성능 데이터는 수집되지 않습니다. 이 경우에 권장되는 평가 크기는 CPU 및 메모리의 성능 데이터와 디스크 및 네트워크 어댑터의 구성 데이터를 기반으로 합니다. 수집되는 데이터와 수집된 데이터가 평가에 미치는 영향에 대해 [자세히 알아보세요](./concepts-collector.md).
5. Collector에는 vCenter Server에 대한 네트워크 시야가 있어야 합니다.

> [!NOTE]
> vCenter Server 버전 5.5, 6.0 및 6.5만 공식적으로 지원됩니다.

> [!IMPORTANT]
> 모든 카운터는 올바르게 수집되도록 통계 수준에 대한 가장 높은 일반적인 수준(3)을 설정하는 것이 좋습니다. vCenter가 낮은 수준으로 설정된 경우 몇 가지 카운터만 완벽히 수집되고 나머지는 0으로 설정됩니다. 그러면 평가는 불완전한 데이터를 표시합니다.

### <a name="selecting-the-scope-for-discovery"></a>검색 범위 선택

vCenter에 연결되면 검색할 범위를 선택할 수 있습니다. 범위를 선택하면 지정된 vCenter 인벤토리 경로의 모든 가상 머신이 검색됩니다.

1. 범위는 데이터센터, 폴더 또는 ESXi 호스트일 수 있습니다.
2. 범위는 한 번에 하나만 선택할 수 있습니다. 가상 머신을 더 많이 선택하려면 검색 하나를 완료하고 새 범위로 검색 프로세스를 다시 시작합니다.
3. *가상 머신이 1500대 미만*인 범위만 선택할 수 있습니다.

## <a name="specify-migration-project"></a>마이그레이션 프로젝트 지정

온-프레미스 vCenter가 연결되고 범위가 지정되면, 검색 및 평가에 사용할 마이그레이션 프로젝트 세부 정보를 지정할 수 있습니다. 프로젝트 ID와 키를 지정하고 연결합니다.

## <a name="start-discovery-and-view-collection-progress"></a>검색 시작 및 컬렉션 진행률 보기

검색이 시작되면 vCenter 가상 머신이 검색되고 해당 메타데이터 및 성능 데이터가 서버로 전송됩니다. 진행 상태에도 다음 ID가 표시됩니다.

1. Collector ID: Collector 컴퓨터에 부여되는 고유 ID입니다. ID는 다양한 검색 전체에서 지정된 시스템에 대해 변경되지 않습니다. 이 ID는 Microsoft 지원에 문제를 보고할 때 사용할 수 있습니다.
2. 세션 ID: 진행 중인 컬렉션 작업에 대한 고유 ID입니다. 검색 작업이 완료되면 포털에서 동일한 세션 ID를 참조할 수 있습니다. 이 ID는 컬렉션 작업마다 변경됩니다. 오류가 발생하면 이 ID를 Microsoft 지원에 보고할 수 있습니다.

### <a name="what-data-is-collected"></a>수집되는 데이터

컬렉션 작업은 선택된 가상 머신에 대해 다음과 같은 정적 메타데이터를 검색합니다.

1. VM 표시 이름(vCenter)
2. VM의 인벤토리 경로(vCenter의 호스트/폴더)
3. IP 주소
4. MAC 주소
5. 운영 체제
5. 코어, 디스크, NIC 수
6. 메모리 크기, 디스크 크기
7. VM, 디스크 및 네트워크의 성능 카운터는 아래 표를 참조하세요.

다음 표에는 수집된 성능 카운터가 나열되어 있고 특정 카운터가 수집되지 않으면 영향을 받는 평가 결과도 나열되어 있습니다.

|카운터                                  |Level    |장치 단위 수준  |영향 평가                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |해당 없음                |권장되는 VM 크기 및 비용                    |
|mem.usage.average                        | 1       |해당 없음                |권장되는 VM 크기 및 비용                    |
|virtualDisk.read.average                 | 2       |2                 |디스크 크기, 저장소 비용 및 VM 크기         |
|virtualDisk.write.average                | 2       |2                 |디스크 크기, 저장소 비용 및 VM 크기         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |디스크 크기, 저장소 비용 및 VM 크기         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |디스크 크기, 저장소 비용 및 VM 크기         |
|net.received.average                     | 2       |3                 |VM 크기 및 네트워크 비용                        |
|net.transmitted.average                  | 2       |3                 |VM 크기 및 네트워크 비용                        |

> [!WARNING]
> 방금 더 높은 통계 수준을 설정한 경우 성능 카운터를 생성하는 데 하루가 걸립니다. 따라서 하루 뒤에 검색을 실행하는 것이 좋습니다.

### <a name="time-required-to-complete-the-collection"></a>컬렉션을 완료하는 데 필요한 시간입니다.

Collector는 컴퓨터 데이터를 검색하여 프로젝트로 보내기만 합니다. 검색된 데이터가 포털에 표시되고 평가 생성이 시작되기까지 프로젝트에 시간이 더 걸릴 수 있습니다.

정적 메타데이터를 프로젝트에 보내는 데 최대 15분이(선택한 범위에 있는 가상 머신의 수를 기반으로) 소요됩니다. 정적 메타데이터를 포털에서 사용할 수 있게 되면, 포털에 컴퓨터 목록이 표시되고 그룹 만들기를 시작할 수 있습니다. 컬렉션 작업이 완료되고 프로젝트가 데이터를 처리할 때까지 평가를 생성할 수 없습니다. Collector에서 컬렉션 작업이 완료된 후, 포털에서 성능 데이터를 사용할 수 있게 되기까지는 선택한 범위에 있는 가상 머신의 수에 따라 최대 1시간이 걸릴 수 있습니다.

## <a name="locking-down-the-collector-appliance"></a>수집기 어플라이언스 잠금
수집기 어플라이언스에서 지속적인 Windows 업데이트를 실행하는 것이 좋습니다. 수집기가 45일 동안 업데이트되지 않으면, 수집기는 컴퓨터를 자동 종료하기 시작합니다. 검색을 실행 중인 경우 45일의 기간이 경과되어도 컴퓨터는 꺼지지 않습니다. 검색 작업이 완료된 후 컴퓨터가 꺼집니다. 45일 이상 수집기를 사용 중인 경우 Windows 업데이트를 실행하여 컴퓨터를 항상 업데이트된 상태로 유지하는 것이 좋습니다.

또한 다음 단계를 통해 어플라이언스를 보호하는 것이 좋습니다.
1. 관리자 암호를 권한이 없는 주체와 공유하거나 잃어버리지 마십시오.
2. 사용 중이 아니면 어플라이언스를 종료합니다.
3. 보안된 네트워크에 어플라이언스를 배치합니다.
4. 마이그레이션 작업이 완료되면 어플라이언스 인스턴스를 삭제합니다. 디스크에 캐시된 vCenter 자격 증명이 있을 수 있으므로 디스크 백업 파일(VMDK)을 삭제해야 합니다.

## <a name="how-to-upgrade-collector"></a>수집기를 업그레이드하는 방법

다시 한 번의 OVA를 다운로드하지 않고 수집기를 최신 버전으로 업그레이드할 수 있습니다.

1. 최신 [업그레이드 패키지](https://aka.ms/migrate/col/latestupgrade)를 다운로드합니다.
2. 다운로드한 핫픽스가 안전한지 확인하려면 관리자 권한 명령 창을 열고 다음 명령을 실행하여 Zip 파일에 대한 해시를 생성합니다. 생성된 해시는 특정 버전에 대해 언급된 해시와 일치해야 합니다.

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (예제 사용량 C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.5.zip SHA256)
3. Zip 파일을 Azure Migrate 수집기 가상 머신(수집기 어플라이언스)에 복사합니다.
4. Zip 파일을 마우스 오른쪽 단추로 클릭하고 모두 압축 풀기를 선택합니다.
5. Setup.ps1 단추를 마우스 오른쪽 단추로 클릭하고 PowerShell과 함께 실행을 선택하고 화면의 지시를 따라 업데이트를 설치합니다.

### <a name="list-of-updates"></a>업데이트 목록

#### <a name="upgrade-to-version-1097"></a>버전 1.0.9.7로 업그레이드

버전 1.0.9.7 다운로드 [패키지](https://aka.ms/migrate/col/upgrade_9_7)로 업그레이드하는 경우

**알고리즘** | **해시 값**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

#### <a name="upgrade-to-version-1095"></a>버전 1.0.9.5로 업그레이드

버전 1.0.9.5 다운로드 [패키지](https://aka.ms/migrate/col/upgrade_9_5)로 업그레이드하는 경우

**알고리즘** | **해시 값**
--- | ---
MD5 | d969ebf3bdacc3952df0310d8891ffdf
SHA1 | f96cc428eaa49d597eb77e51721dec600af19d53
SHA256 | 07c03abaac686faca1e82aef8b80e8ad8eca39067f1f80b4038967be1dc86fa1

## <a name="next-steps"></a>다음 단계

[온-프레미스 VMware VM에 대한 평가 설정](tutorial-assessment-vmware.md)
