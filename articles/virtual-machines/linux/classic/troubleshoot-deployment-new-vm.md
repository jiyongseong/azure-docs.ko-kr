---
title: "Linux VM 배포 클래식 문제 해결 | Microsoft Docs"
description: "Azure에서 새 Linux 가상 머신을 만드는 경우 클래식 배포 문제 해결"
services: virtual-machines-linux
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: c8a963fa-6b2a-4c7a-a1f4-7793adb02b19
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2016
ms.author: cjiang
ms.openlocfilehash: 581fbaa477bd603fea5fdc0ef77c6ef7498b7897
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Azure에서 새 Linux 가상 머신 생성 관련 클래식 배포 문제 해결
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. 이 문서의 Resource Manager 버전은 [여기](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>감사 로그 수집
문제 해결을 시작하려면 문제와 관련된 오류를 파악하기 위해 감사 로그를 수집합니다.

Azure 포털에서 **찾아보기** > **가상 머신** > *Windows 가상 머신* > **설정** > **감사 로그**를 클릭합니다.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** OS가 일반화된 Linux인 경우, 일반화된 설정을 사용하여 업로드되거나 캡처되고, 오류는 발생하지 않습니다. 마찬가지로 OS가 특수화된 Linux인 경우, 특수화된 설정을 사용하여 업로드되거나 캡처되고, 오류는 발생하지 않습니다.

**업로드 오류:**

**N<sup>1</sup>:** OS가 일반화된 Linux이고 특수화된 것으로 업로드된다면, VM이 프로비전 단계에서 중단되기 때문에 프로비전 시간 초과 오류가 발생합니다.

**N<sup>2</sup>:** OS가 특수화된 Linux이고 일반화된 것으로 업로드된다면, 새 VM이 원래 컴퓨터 이름, 사용자 이름 및 암호로 실행되기 때문에 프로비전 실패 오류가 발생합니다.

**해결 방법:**

이 두 가지 오류를 모두 해결하려면, 온-프레미스에 있는 원본 VHD를, OS와 같은 설정(일반화/특수화)으로 업로드합니다. 일반화된 것으로 업로드하려면, 먼저 프로비전 해제를 실행해야 합니다. 자세한 내용은 [Linux 운영 체제를 포함한 가상 하드 디스크 만들기 및 업로드](create-upload-vhd-classic.md) 를 참조하세요.

**캡처 오류:**

**N<sup>3</sup>:** OS가 일반화된 Linux이고 특수화된 것으로 캡처된다면, 원본 VM이 일반화된 것으로 표시되어 사용할 수 없기 때문에 프로비전 시간 초과 오류가 발생합니다.

**N<sup>4</sup>:** OS가 특수화된 Linux이고 일반화된 것으로 캡처된다면, 새 VM이 원래 컴퓨터 이름, 사용자 이름 및 암호로 실행되기 때문에 프로비전 실패 오류가 발생합니다. 또한, 원본 VM이 일반화된 것으로 표시되기 때문에 사용할 수 없습니다.

**해결 방법:**

이 두 가지 오류를 모두 해결하려면, 현재 이미지를 포털에서 제거하고, OS와 같은 설정(일반화/특수화)으로 [현재 VHD에서 다시 캡처](capture-image-classic.md) 합니다.

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>문제: 사용자 지정/ 갤러리/ 마켓플레이스 이미지; 할당 오류
이 오류는 요청을 수용할 여유 공간이 없거나 요청되는 VM 크기를 지원할 수 없는 클러스터로 새 VM 요청이 전송된 상황에서 발생합니다. 동일한 클라우드 서비스에 일련의 다른 VM을 혼합하는 것은 불가능합니다. 따라서 클라우드 서비스에서 지원할 수 있는 것과 다른 크기의 VM을 새로 만들려고 하면, 계산 요청이 실패합니다.

새 VM을 만드는 데 사용한 클라우드 서비스의 제약 조건에 따라서, 둘 중 한가지 상황에 의해 유발되는 오류를 접하게 됩니다.

**원인 1:** 클라우드 서비스가 특정 클러스터에 고정되어 있거나, 선호도 그룹에 연결되어 있기 때문에, 의도적으로 특정 클러스터에 고정되어 있습니다. 선호도 그룹의 새로운 계산 리소스 요청이 기존 리소스가 호스트된 동일한 클러스터에서 시도됩니다. 하지만, 동일한 클러스터가 요청된 VM 크기를 지원하지 않거나 사용할 수 있는 공간이 부족하여 할당 오류가 발생합니다. 새로운 리소스가 새 클라우드 서비스에서 만들어지건 아니면 기존 클라우드 서비스를 통해 만들어지건 이 내용은 사실입니다.

**해결 방법 1:**

* 새 클라우드 서비스를 만들어서 지역 또는 지역 기반 가상 네트워크와 연결합니다.
* 새 클라우드 서비스에 새 VM을 만듭니다.
  새 클라우드 서비스를 만들려다 오류가 발생하면, 나중에 다시 시도하거나 클라우드 서비스의 지역을 변경합니다.

> [!IMPORTANT]
> 기존 클라우드 서비스에 새 VM을 만들려다 실패했고, 새 VM을 위해 새 클라우드 서비스를 만들어야 한다면, 모든 VM을 동일한 클라우드 서비스에 통합하도록 선택할 수 있습니다. 이렇게 하려면, 기존 클라우드 서비스의 VM을 삭제하고, 새 클라우드 서비스의 디스크에서 VM을 다시 캡처합니다. 하지만, 새 클라우드 서비스가 새 이름과 VIP를 갖게 되므로, 기존 클라우드 서비스에 이 정보를 사용하는 모든 종속성에 대해 해당 정보를 업데이트해야 합니다.
> 
> 

**원인 2:** 클라우드 서비스가 선호도 그룹에 연결된 가상 네트워크와 연결되어 있어서, 의도적으로 특정 클러스터에 고정되어 있습니다. 따라서 선호도 그룹의 모든 새로운 계산 리소스 요청이 기존 리소스가 호스트된 동일한 클러스터에서 시도됩니다. 하지만, 동일한 클러스터가 요청된 VM 크기를 지원하지 않거나 사용할 수 있는 공간이 부족하여 할당 오류가 발생합니다. 새로운 리소스가 새 클라우드 서비스에서 만들어지건 아니면 기존 클라우드 서비스를 통해 만들어지건 이 내용은 사실입니다.

**해결 방법 2:**

* 새 지역 가상 네트워크를 만듭니다.
* 새 가상 네트워크에 새 VM을 만듭니다.
* [기존 가상 네트워크를 연결](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) 합니다. [지역 가상 네트워크](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)에 대한 자세한 내용을 참조하세요. 또는 [선호도 그룹 기반 가상 네트워크를 지역 가상 네트워크로 마이그레이션](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)한 다음 새 VM을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
중지된 Linux VM을 시작하거나 Azure에서 기존 Linux VM의 크기를 조정할 때 문제가 발생하면 [Azure의 기존 Linux Virtual Machine 재시작 또는 크기 조정 관련 클래식 배포 문제 해결](restart-resize-error-troubleshooting.md)을 참조하세요.

