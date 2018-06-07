---
title: 가상 네트워크에 대한 Azure PowerShell 샘플 | Microsoft Docs
description: 가상 네트워크에 대한 Azure PowerShell 샘플입니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: ab3a0935f23cda70cfef49af3563f6b1e85d1d8b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-powershell-samples-for-virtual-network"></a>가상 네트워크에 대한 Azure PowerShell 샘플

다음 표에는 Azure PowerShell 스크립트에 대한 링크가 포함됩니다.

| | |
|----|----|
| [다중 계층 응용 프로그램을 위한 가상 네트워크 만들기](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 트래픽은 HTTP로 제한되며, 백 엔드 서브넷에 대한 트래픽은 SQL, 포트 1433으로 제한됩니다. |
| [2개 가상 네트워크 피어링](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | 동일한 지역에 2개 가상 네트워크를 만들고 연결합니다. |
| [네트워크 가상 어플라이언스를 통한 트래픽 라우팅](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | 프런트 엔드 및 백 엔드 서브넷과 두 서브넷 간에 트래픽을 라우팅할 수 있는 VM이 있는 가상 네트워크를 만듭니다. |
| [인바운드 및 아웃바운드 VM 네트워크 트래픽 필터링](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 인바운드 네트워크 트래픽은 HTTP 및 HTTPS로 제한됩니다. 백 엔드 서브넷에서 인터넷으로의 아웃바운드 트래픽은 허용되지 않습니다. |
