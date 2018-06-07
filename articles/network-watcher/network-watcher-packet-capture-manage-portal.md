---
title: Azure Network Watcher를 사용하여 패킷 캡처 관리 - Azure Portal | Microsoft Docs
description: 이 페이지에서는 Azure Portal을 사용하여 Network Watcher의 패킷 캡처 기능을 관리하는 방법에 대해 설명합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 508b9e7eef757277d4bc0e93a26f3a63045f31e4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187534"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>포털에서 Azure Network Watcher를 사용하여 패킷 캡처 관리

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher 패킷 캡처를 사용하면 가상 컴퓨터 간에 트래픽을 추적하는 캡처 세션을 만들 수 있습니다. 원하는 트래픽만 캡처할 수 있도록 캡처 세션에 대 한 필터가 제공됩니다. 패킷 캡처를 통해 사후 및 사전 대응적으로 네트워크 예외를 진단할 수 있습니다. 또한 네트워크 침입에 대한 정보를 가져오는 네트워크 통계를 수집하는 것을 포함하여 클라이언트 서버 간 통신을 디버깅할 수 있습니다. 이 기능은 원격으로 패킷 캡처를 트리거할 수 있게 하여 원하는 컴퓨터에서 수동으로 패킷 캡처를 실행하는 부담을 줄이고 시간을 단축합니다.

이 문서에서는 패킷 캡처를 위해 현재 사용할 수 있는 여러 관리 태스크를 설명합니다.

- [**패킷 캡처 시작**](#start-a-packet-capture)
- [**패킷 캡처 중지**](#stop-a-packet-capture)
- [**패킷 캡처 삭제**](#delete-a-packet-capture)
- [**패킷 캡처 다운로드**](#download-a-packet-capture)

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 사용자에게 다음 리소스가 있는 것으로 가정합니다.

- 패킷 캡처를 만들려는 영역의 Network Watcher 인스턴스
- 패킷 캡처 확장을 사용하는 Virtual Machine

> [!IMPORTANT]
> 패킷 캡처에는 가상 머신 확장 `AzureNetworkWatcherExtension`이 필요합니다. Windows VM에서 확장을 설치하려면 [Windows용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/windows/extensions-nwa.md)을 방문하고 Linux VM인 경우 [Linux용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/linux/extensions-nwa.md)을 방문하세요.

### <a name="packet-capture-agent-extension-through-the-portal"></a>포털을 통한 패킷 캡처 에이전트 확장

포털을 통해 패킷 캡처 VM 에이전트를 설치하려면 가상 머신으로 이동하고 **확장** > **추가**를 클릭하고 **Network Watcher Agent for Windows**를 검색합니다.

![에이전트 보기][agent]

## <a name="packet-capture-overview"></a>패킷 캡처 개요

[Azure Portal](https://portal.azure.com)로 이동하고 **네트워킹** > **Network Watcher** > **패킷 캡처**를 클릭합니다.

개요 페이지에 상태에 관계없이, 존재하는 모든 패킷 캡처 목록이 표시됩니다.

> [!NOTE]
> 패킷을 캡처하려면 다음 연결이 필요합니다.
> * 포트 443을 통해 저장소 계정에 아웃바운드 연결
> * 169.254.169.254에 인바운드 및 아웃 바운드 연결
> * 168.63.129.16에 인바운드 및 아웃 바운드 연결

![패킷 캡처 개요 화면][1]

## <a name="start-a-packet-capture"></a>패킷 캡처 시작

**추가**를 클릭하여 패킷 캡처를 만듭니다.

패킷 캡처에서 정의할 수 있는 속성은 다음과 같습니다.

**기본 설정**

- **구독** - 이 값은 사용되는 구독이며 각 구독에는 network watcher의 인스턴스가 필요합니다.
- **리소스 그룹** - 대상 지정된 가상 머신의 리소스 그룹입니다.
- **대상 가상 머신** - 패킷 캡처를 실행 중인 가상 머신
- **패킷 캡처 이름** - 이 값은 패킷 캡처의 이름입니다.

**캡처 구성**

- **로컬 파일 경로** - 패킷 캡처가 저장되는 가상 머신의 로컬 경로(**[파일]** 을 선택했을 때만 유효)입니다. 유효한 경로를 지정해야 합니다. Linux 가상 머신의 경우 경로는 / var / captures로 시작해야 합니다.
- **Storage 계정** - 패킷 캡처를 Storage 계정에 저장할지 여부를 결정합니다.
- **파일** - 패킷 캡처를 가상 머신에 로컬로 저장할지 여부를 결정합니다.
- **Storage 계정** - 패킷 캡처를 저장할 선택한 Storage 계정입니다. 기본 위치는 https://{저장소 계정 이름}.blob.core.windows.net/network-watcher-logs/subscriptions/{구독 ID}/resourcegroups/{리소스 그룹 이름}/providers/microsoft.compute/virtualmachines/{가상 머신 이름}/{YY}/{MM}/{DD}/packetcapture_{HH}_{MM}_{SS}_{XXX}.cap입니다. (**저장소**를 선택한 경우에만 사용됨)
- **로컬 파일 경로** - 패킷 캡처를 저장할 가상 머신의 로컬 경로입니다. (**파일**을 선택한 경우에만 사용됨). 유효한 경로를 제공해야 합니다. Linux 가상 머신의 경우 경로는 */var/captures*로 시작해야 합니다.
- **패킷당 최대 바이트** - 캡처된 각 패킷의 바이트 수이며 비어 있으면 모든 바이트가 캡처됩니다.
- **세션당 최대 바이트** - 값이 패킷 캡처 종료일에 도달한 후 캡처된 총 바이트 수입니다.
- **시간 제한(초)** - 중지할 패킷 캡처에 대한 시간 제한을 설정합니다. 기본값은 18000초입니다.

> [!NOTE]
> Premium Storage 계정에서는 패킷 캡처 저장이 현재 지원되지 않습니다.

**필터링(선택 사항)**

- **프로토콜** - 패킷 캡처에 대해 필터링할 프로토콜입니다. 사용 가능한 값은 TCP, UDP 및 모두입니다.
- **로컬 IP 주소** - 이 값은 패킷 캡처를 로컬 IP 주소가 이 필터 값과 일치하는 패킷으로 필터링합니다.
- **로컬 포트** - 이 값은 패킷 캡처를 로컬 포트가 이 필터 값과 일치하는 패킷으로 필터링합니다.
- **원격 IP 주소** - 이 값은 패킷 캡처를 원격 IP 주소가 이 필터 값과 일치하는 패킷으로 필터링합니다.
- **원격 포트** - 이 값은 패킷 캡처를 원격 포트가 이 필터 값과 일치하는 패킷으로 필터링합니다.

> [!NOTE]
> 포트 및 IP 주소 값은 단일 값이거나 값 범위이거나 집합일 수 있습니다. (즉, 포트의 경우 80-1024) 원하는 만큼 많은 필터를 정의할 수 있습니다.

값을 입력한 후 **확인**을 클릭하여 패킷 캡처를 만듭니다.

![패킷 캡처 만들기][2]

패킷 캡처에서 시간 제한 설정이 만료되면 패킷 캡처가 중지되고 검토할 수 있습니다. 패킷 캡처 세션을 수동으로 중지할 수도 있습니다.

## <a name="delete-a-packet-capture"></a>패킷 캡처 삭제

패킷 캡처 보기에서 **상황에 맞는 메뉴**(...)를 클릭하거나 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭하여 패킷 캡처를 중지합니다.

![패킷 캡처 삭제][3]

> [!NOTE]
> 패킷 캡처를 삭제하면 저장소 계정에서 파일을 삭제하지 않습니다.

패킷 캡처를 삭제할지 확인하는 메시지가 표시되면 **예**를 클릭합니다.

![확인][4]

## <a name="stop-a-packet-capture"></a>패킷 캡처 중지

패킷 캡처 보기에서 **상황에 맞는 메뉴**(...)를 클릭하거나 마우스 오른쪽 단추로 클릭하고 **중지**를 클릭하여 패킷 캡처를 중지합니다.

## <a name="download-a-packet-capture"></a>패킷 캡처 다운로드

패킷 캡처 세션이 완료되면 캡처 파일을 Blob Storage 또는 VM의 로컬 파일에 업로드합니다. 패킷 캡처의 저장 위치는 세션 생성 시 정의됩니다. 저장소 계정에 저장되는 이러한 캡처 파일에 액세스하는 편리한 도구는 Microsoft Azure Storage 탐색기이며 http://storageexplorer.com/에서 다운로드할 수 있습니다.

저장소 계정이 지정되어 있으면 패킷 캡처 파일은 다음 위치에서 저장소 계정에 저장됩니다.
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>다음 단계

[경고로 트리거된 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 확인하여 가상 머신 경고로 패킷 캡처를 자동화하는 방법을 알아봅니다.

[IP 흐름 확인 확인](diagnose-vm-network-traffic-filtering-problem.md)을 방문하여 특정 트래픽이 VM에서 허용되는지 알아봅니다.

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













