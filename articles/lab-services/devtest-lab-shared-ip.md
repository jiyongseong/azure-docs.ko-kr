---
title: Azure DevTest Labs에서 공유 IP 주소 이해 | Microsoft Docs
description: Azure DevTest Labs에서 공유 IP 주소를 사용하여 랩 VM에 액세스하는 데 필요한 공용 IP 주소를 최소화하는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c62f8808565022371484b936f5a2bdaba1f8900e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Azure DevTest Labs에서 공유 IP 주소 이해

Azure DevTest Labs에서는 랩 VM이 동일한 공용 IP 주소를 공유하여 개별 랩 VM에 액세스하는 데 필요한 공용 IP 주소 수를 최소화할 수 있습니다.  이 문서에서는 공유 IP의 작동 방식과 관련 구성 옵션에 대해 설명합니다.

## <a name="shared-ip-setting"></a>공유 IP 설정

랩을 만들면 가상 네트워크의 서브넷에 상주합니다.  기본적으로 이 서브넷은 **Enable shared public IP**(공유 공용 IP 사용)을 *예*로 설정하면 생성됩니다.  이 구성은 전체 서브넷에 하나의 공용 IP 주소를 만듭니다.  가상 네트워크 및 서브넷 구성에 대한 자세한 내용은 [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md)을 참조하세요.

![새 랩 서브넷](media/devtest-lab-shared-ip/lab-subnet.png)

기존 랩의 경우 **구성 및 정책 > Virtual Networks**를 선택하여 이 옵션을 사용하도록 설정할 수 있습니다. 그런 다음, 목록에서 가상 네트워크를 선택하고 선택한 서브넷에 대해 **공유 공용 IP 사용**을 선택합니다. 랩 VM 간에 공용 IP 주소를 공유하지 않으려는 경우 아무 랩에서나 이 옵션을 해제할 수 있습니다.

이 랩에 생성된 모든 VM은 기본적으로 공유 IP를 사용합니다.  VM을 만들 때 **IP 주소 구성** 아래 **고급 설정** 블레이드에서 이 설정을 확인할 수 있습니다.

![새 VM](media/devtest-lab-shared-ip/new-vm.png)

- **공유:** **공유**로 생성된 모든 VM은 하나의 리소스 그룹(RG)에 배치됩니다. 해당 RG에는 단일 IP 주소가 할당되며, RG에 있는 모든 VM은 이 IP 주소를 사용합니다.
- **공개:** 만든 각 VM에 자체 IP 주소가 있으며 자체 리소스 그룹에 생성됩니다.
- **비공개:** 만든 각 VM이 개인 IP 주소를 사용합니다. 원격 데스크톱을 사용하여 인터넷에서 직접 이 VM에 연결할 수 없습니다.

공유 IP가 사용된 VM이 서브넷에 추가될 때마다 DevTest Labs는 자동으로 부하 분산 장치에 VM을 추가하고 공용 IP 주소에 TCP 포트 번호를 할당하여 VM의 RDP 포트에 전달합니다.  

## <a name="using-the-shared-ip"></a>공유 IP 사용

- **Linux 사용자:** IP 주소 또는 정규화된 도메인 이름, 콜론, 포트를 순서대로 사용하여 VM에 SSH합니다. 예를 들어 아래 이미지에서 VM에 연결되는 RDP 주소는 `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`입니다.

  ![VM 예](media/devtest-lab-shared-ip/vm-info.png)

- **Windows 사용자:** Azure Portal에서 **연결** 단추를 선택하여 미리 구성된 RDP 파일을 다운로드하고 VM에 액세스합니다.

## <a name="next-steps"></a>다음 단계

* [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)
* [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md)





