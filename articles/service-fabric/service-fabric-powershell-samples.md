---
title: Azure PowerShell 샘플 - Service Fabric | Microsoft Docs
description: Azure PowerShell 샘플 - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 04/09/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 65f6252ecfa4e5850824fc7aa2e6eca2bdb88692
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205759"
---
# <a name="azure-powershell-samples"></a>Azure PowerShell 샘플

다음 표에는 Service Fabric 클러스터, 응용 프로그램 및 서비스를 만들고 관리하는 PowerShell 스크립트 샘플에 대한 링크가 제공됩니다.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **클러스터 만들기** ||
| [클러스터 만들기(Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Azure Service Fabric 클러스터를 만듭니다. |
|[테스트 클러스터 만들기(Azure)](./scripts/service-fabric-powershell-create-test-cluster.md)| Azure에 3개 노드의 테스트 Service Fabric 클러스터를 만듭니다.|
| **클러스터, 노드 및 인프라 관리** ||
| [응용 프로그램 인증서 추가](./scripts/service-fabric-powershell-add-application-certificate.md) .| 클러스터의 모든 노드에 응용 프로그램 X.509 인증서를 추가합니다. |
| [클러스터 VM에서 RDP 포트 범위 업데이트](./scripts/service-fabric-powershell-change-rdp-port-range.md)|배포된 클러스터에서 클러스터 노드 VM의 RDP 포트 범위를 변경합니다.|
| [클러스터 노드 VM에 대한 관리자 사용자 및 암호 업데이트](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | 클러스터 노드 VM에 대한 관리자 사용자 및 암호를 업데이트합니다. |
| [Load Balancer에서 포트 열기](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | 특정 포트에서 인바운드 트래픽을 허용하도록 Azure Load Balancer에서 응용 프로그램 포트를 엽니다. |
| [인바운드 네트워크 보안 그룹 규칙 만들기](./scripts/service-fabric-powershell-add-nsg-rule.md) | 특정 포트에서 클러스터에 대한 인바운드 트래픽을 허용하는 인바운드 네트워크 보안 그룹 규칙을 만듭니다. |
| **응용 프로그램 관리** ||
| [응용 프로그램 배포](./scripts/service-fabric-powershell-deploy-application.md)| 클러스터에 응용 프로그램을 배포합니다.|
| [응용 프로그램 업그레이드](./scripts/service-fabric-powershell-upgrade-application.md)| 응용 프로그램을 업그레이드합니다.|
| [응용 프로그램 제거](./scripts/service-fabric-powershell-remove-application.md)| 클러스터에서 응용 프로그램을 제거합니다.|
