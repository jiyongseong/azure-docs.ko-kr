---
title: "Azure Portal에서 Windows VM에 대한 FQDN 만들기 | Microsoft Docs"
description: "Azure Portal에서 가상 머신을 기반으로 한 Resource Manager에 대한 정규화된 도메인 이름 또는 FQDN을 만드는 방법에 대해 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ebc1ef89b24a9aa21f39e5b05051c16351f08cd
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2017
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Windows VM용 Azure Portal에서 정규화된 도메인 이름 만들기

[Azure Portal](https://portal.azure.com)에서 VM(가상 머신)을 만들 때, 가상 머신의 공용 IP 리소스가 자동으로 만들어집니다. 이 IP 주소를 사용하여 VM에 원격으로 액세스합니다. 포털에서 [정규화된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) 또는 FQDN을 만들지는 않지만 VM을 만들면 이름을 만들 수 있습니다. 이 문서에서는 DNS 이름 또는 FQDN을 만드는 단계를 보여 줍니다.

## <a name="create-a-fqdn"></a>FQDN 만들기
이 문서에서는 사용자가 VM을 이미 만들었다고 가정합니다. 필요한 경우 [포털에서](quick-create-portal.md) 또는 [Azure PowerShell을 사용하여](quick-create-powershell.md) VM을 만들 수 있습니다. VM이 실행되면 다음 단계를 따릅니다.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

이제 RDP(원격 데스크톱 프로토콜)의 경우처럼 이 DNS 이름을 사용하여 원격으로 VM에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계
VM에 공용 IP 및 DNS 이름을 지정했으므로 IIS, SQL, SharePoint 등의 공용 응용 프로그램 프레임워크 또는 서비스를 배포할 수 있습니다.

또한 Azure 배포 구축에 대한 팁을 보려면 [Resource Manager 사용](../../azure-resource-manager/resource-group-overview.md)에 대해 자세히 읽어볼 수도 있습니다.

