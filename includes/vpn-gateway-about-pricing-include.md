---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 205c67377e4bff66c02e3ee508c0f6f4e2823608
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
가상 네트워크 게이트웨이에 대한 시간당 계산 비용 및 가상 네트워크 게이트웨이에서 송신 데이터 전송 등 두 가지에 대한 비용을 지불합니다. 가격 책정 정보는 [가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway) 페이지에 있습니다.

**가상 네트워크 게이트웨이 계산 비용**<br>각 가상 네트워크 게이트웨이에는 시간당 계산 비용이 포함됩니다. 가격은 가상 네트워크 게이트웨이를 만들 때 지정한 게이트웨이 SKU에 따라 다릅니다. 비용은 게이트웨이 자체에 대한 것이며 게이트웨이를 통해 전달되는 데이터 전송에 추가됩니다.

**데이터 전송 비용**<br>데이터 전송 비용은 원본 가상 네트워크 게이트웨이의 송신 트래픽에 따라 계산됩니다.

* 온-프레미스 VPN 장치에 트래픽을 보내는 경우 인터넷 송신 데이터 전송율을 사용하여 청구됩니다.
* 다른 지역에 있는 가상 네트워크 사이에 트래픽을 보내는 경우 가격 책정은 지역을 기반으로 합니다.
* 동일한 지역에 있는 가상 네트워크 간에 트래픽을 보내는 경우 데이터 비용이 없습니다. 동일한 지역에 있는 VNet 간의 트래픽은 무료입니다.