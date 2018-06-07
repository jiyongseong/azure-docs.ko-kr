---
title: Azure CLI 1.0을 사용하여 네트워크 보안 그룹(클래식) 만들기 | Microsoft Docs
description: Azure CLI 1.0을 사용하여 네트워크 보안 그룹(클래식)을 만들고 배포하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 5468801e56849498d712f51e71cfb31bf068398a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 네트워크 보안 그룹(클래식) 만들기
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 클래식 배포 모델에 대해 설명합니다. [리소스 관리자 배포 모델에서 NSG를 만들](tutorial-filter-network-traffic-cli.md)수도 있습니다.

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

다음 샘플 Azure CLI 명령에는 이 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [VNet을 만들어](virtual-networks-create-vnet-classic-cli.md)테스트 환경을 구축합니다.

## <a name="create-an-nsg-for-the-front-end-subnet"></a>프런트 엔드 서브넷에 대한 NSG 만들기

1. Azure CLI를 사용한 적이 없다면 [Azure CLI 설치 및 구성](../cli-install-nodejs.md)을 참조하세요.
2. 클래식 모드로 전환합니다.

    ```azurecli
    azure config mode asm
    ```   

3. NSG를 만듭니다.
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. 인터넷에서 포트 3389(RDP)에 대한 액세스를 허용하는 보안 규칙을 만듭니다.
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. 인터넷에서 포트 80(HTTP)에 대한 액세스를 허용하는 규칙을 만듭니다.
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. NSG를 프런트 엔드 서브넷에 연결합니다.
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>백 엔드 서브넷에 대한 NSG 만들기

1. NSG를 만듭니다.
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. 프런트 엔드 서브넷에서 포트 1433(SQL)에 대한 액세스를 허용하는 규칙을 만듭니다.
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. 인터넷 액세스를 거부하는 규칙을 만듭니다.
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. NSG를 백 엔드 서브넷에 연결합니다.
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```