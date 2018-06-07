---
title: 클라우드 서비스에서 Windows VM 연결 | Microsoft Docs
description: 클래식 배포 모델을 사용하여 만든 Windows 가상 머신을 Azure 클라우드 서비스 또는 가상 네트워크에 연결합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: cynthn
ms.openlocfilehash: b5778336b2dfb3d65cb678c96525ec22da1634a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>클래식 배포 모델을 사용하여 만든 Windows 가상 머신을 가상 네트워크 또는 클라우드 서비스에 연결
> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

클래식 배포 모델을 사용하여 만든 Windows 가상 머신은 항상 클라우드 서비스에 배치됩니다. 클라우드 컴퓨터는 컨테이너 역할을 하며 인터넷을 통해 가상 머신에 액세스할 수 있도록 고유한 공용 DNS 이름, 공용 IP 주소 및 끝점 집합을 제공합니다. 클라우드 서비스는 가상 네트워크에 있을 수 있지만 요구 사항은 아닙니다.

가상 네트워크에 없는 클라우드 서비스를 *독립 실행형* 클라우드 서비스라고 합니다. 독립 실행형 클라우드 서비스의 가상 머신은 다른 가상 머신의 공용 DNS 이름을 사용하여 다른 가상 머신과 통신하고 인터넷을 통해 트래픽을 이동시킵니다. 클라우드 서비스가 가상 네트워크에 있는 경우 해당 클라우드 서비스의 가상 머신은 인터넷을 통해 트래픽을 보내지 않고도 가상 네트워크에 있는 다른 모든 가상 머신과 통신할 수 있습니다.

같은 독립 실행형 클라우드 서비스에 가상 머신을 배치하는 경우에는 계속 부하 분산 및 가용성 집합을 사용할 수 있습니다. 자세한 내용은 [가상 머신 부하 분산](../../virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 및 [가상 머신의 가용성 관리](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. 그러나 서브넷에서 가상 머신을 구성하거나 독립 실행형 클라우드 서비스를 온-프레미스 네트워크에 연결할 수는 없습니다. 예를 들면 다음과 같습니다.

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>다음 단계
가상 머신을 만들고 나서 서비스 및 워크로드에 데이터를 저장할 위치가 포함되도록 [데이터 디스크를 추가](attach-disk.md) 하는 것이 좋습니다.
