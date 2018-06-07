---
title: 네트워크 보안 그룹 만들기 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 네트워크 보안 그룹을 만들고 배포하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Azure Portal을 사용하여 네트워크 보안 그룹 만들기

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다. [클래식 배포 모델에서 NSG를 만들](virtual-networks-create-nsg-classic-ps.md)수도 있습니다.

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>NSG-FrontEnd NSG 만들기
시나리오에 나온 것처럼 **NSG-FrontEnd** NSG를 만들려면 다음 단계를 완료합니다.

1. 브라우저에서 https://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. **+리소스 만들기 >** > **네트워크 보안 그룹**을 선택합니다.
   
    ![Azure Portal - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. **네트워크 보안 그룹**에서 **추가**를 선택합니다.
   
    ![Azure Portal - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. **네트워크 보안 그룹 만들기**의 *RG-NSG* 리소스 그룹에 *NSG-FrontEnd*라는 이름의 NSG를 만든 다음, **만들기**를 선택합니다.
   
    ![Azure Portal - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>기존 NSG에서 규칙 만들기
Azure Portal의 기존 NSG에 규칙을 만들려면 다음 단계를 완료합니다.

1. **모든 서비스**를 선택한 다음, **네트워크 보안 그룹**을 검색합니다. **네트워크 보안 그룹**이 표시되면 선택합니다.
2. NSG 목록에서 **NSG-FrontEnd** > **인바운드 보안 규칙**을 선택합니다.
   
    ![Azure Portal - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. **인바운드 보안 규칙** 목록에서 **추가**를 선택합니다.
   
    ![Azure Portal - 규칙 추가](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. **인바운드 보안 규칙 추가**에서 우선순위 *200*의 *web-rule*이라는 규칙을 만들어 *TCP* 및 포트 *80*을 통한 모든 원본의 모든 VM으로 액세스를 허용한 다음, **확인**을 선택합니다. 이러한 설정 중 대부분은 이미 기본값입니다.
   
    ![Azure Portal - 규칙 설정](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. 몇 초 후, NSG에 새 규칙이 표시됩니다.
   
    ![Azure Portal - 새 규칙](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. 6단계까지 반복하여 *TCP* 및 포트 *3389*를 통해 모든 원본의 모든 VM에 대한 액세스를 허용하는 우선 순위 *250*의 *rdp-rule*이라는 인바운드 규칙을 만듭니다.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>NSG를 FrontEnd 서브넷에 연결

1. **모든 서비스 >** 를 선택하고, **리소스 그룹**을 입력하여 표시된 **리소스 그룹**을 선택한 다음, **RG-NSG**를 선택합니다.
2. **RG-NSG**에서 **...** > **TestVNet**을 선택합니다.
   
    ![Azure Portal - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. **설정**에서 **서브넷** > **FrontEnd** > **네트워크 보안 그룹** > **NSG-FrontEnd**를 선택합니다.
   
    ![Azure Portal - 서브넷 설정](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. **FrontEnd** 블레이드에서 **저장**을 선택합니다.
   
    ![Azure Portal - 서브넷 설정](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>NSG-BackEnd NSG 만들기
**NSG-BackEnd** NSG를 만들고 **BackEnd** 서브넷에 연결하려면 다음 단계를 완료합니다.

1. *NSG-BackEnd*라는 NSG를 만들려면 [NSG-FrontEnd NSG 만들기](#Create-the-NSG-FrontEnd-NSG)의 단계를 반복합니다.
2. 아래 표에 있는 **인바운드** 규칙을 만들려면 [기존 NSG에서 규칙 만들기](#Create-rules-in-an-existing-NSG)의 단계를 반복합니다.
   
   | 인바운드 규칙 | 아웃바운드 규칙 |
   | --- | --- |
   | ![Azure Portal - 인바운드 규칙](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure Portal - 아웃바운드 규칙](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. **NSG-Backend** NSG를 [BackEnd](#Associate-the-NSG-to-the-FrontEnd-subnet) 서브넷에 연결하려면 **FrontEnd 서브넷에 NSG 연결**의 단계를 반복합니다.

## <a name="next-steps"></a>다음 단계
* [기존 NSG 관리](manage-network-security-group.md)
* [로깅을 사용합니다](virtual-network-nsg-manage-log.md).