---
title: Windows Azure Virtual Machines의 SQL Server FAQ | Microsoft Docs
description: 이 문서는 Azure VM에서 SQL Server를 실행하는 방법에 대한 질문과 대답을 제공합니다.
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/20/2018
ms.author: v-shysun
ms.openlocfilehash: e0254cd16c27597c3d52aed19b4c4ece49bac765
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366395"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-azure-virtual-machines"></a>Windows Azure Virtual Machines에서 실행되는 SQL Server에 대한 FAQ(질문과 대답)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

이 문서에서는 [Windows Azure Virtual Machines에서 SQL Server 실행](https://azure.microsoft.com/services/virtual-machines/sql-server/)에 대해 가장 일반적인 질문 중 몇 가지에 대한 답변을 제공합니다.

> [!NOTE]
> 이 문서에서는 Windows VM의 SQL Server에 관련된 문제에 초점을 맞춥니다. Linux VM에서 SQL Server를 실행 하는 경우 [Linux FAQ](../../linux/sql/sql-server-linux-faq.md)를 참조하세요.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>이미지

1. **사용 가능한 SQL Server 가상 머신 갤러리 이미지는 무엇인가요?**

   Azure는 모든 Windows 및 Linux용 버전의 SQL Server의 지원되는 모든 주요 릴리스에서 가상 머신 이미지를 유지 관리합니다. 자세한 내용은 [Windows VM 이미지](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) 및 [Linux VM 이미지](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)의 전체 목록은 참조하세요.

1. **기존 SQL Server 가상 머신 갤러리 이미지가 업데이트되나요?**

   2개월마다 가상 머신 갤러리의 SQL Server 이미지는 최신 Windows 및 Linux 업데이트로 업데이트됩니다. Windows 이미지의 경우 중요한 SQL Server 보안 업데이트 및 서비스 팩을 비롯한 Windows 업데이트에서 중요하다고 표시된 모든 업데이트가 포함됩니다. Linux 이미지의 경우 최신 시스템 업데이트가 포함됩니다. SQL Server 누적 업데이트는 Linux 및 Windows에서 다르게 처리됩니다. Linux의 경우 SQL Server 누적 업데이트는 새로 고침에도 포함됩니다. 하지만 이번에 Windows VM은 SQL Server 또는 Windows Server 누적 업데이트로 업데이트되지 않습니다.

1. **SQL Server 가상 머신 이미지가 갤러리에서 제거될 수 있나요?**

   예. Azure에서는 주 버전당 하나의 이미지를 유지 관리합니다. 예를 들어 새 SQL Server 서비스 팩이 출시되면 Azure에서는 해당 서비스 팩의 갤러리에 새 이미지를 추가합니다. 이전 서비스 팩의 SQL Server 이미지는 Azure Portal에서 즉시 제거됩니다. 그러나 다음 3개월 동안은 PowerShell에서 프로비전할 수 있습니다. 3개월 후에는 이전 서비스 팩 이미지를 사용할 수 없습니다. 해당 수명 끝에 도달하면 이 제거 정책이 SQL Server 버전이 지원되지 않는 경우에 적용됩니다.

1. **SQL Server VM에서 VHD 이미지를 만들 수 있나요?**

   예, 그러나 몇 가지 고려 사항이 있습니다. 이 VHD를 Azure의 새 VM에 배포하는 경우 포털에서 SQL Server 구성 섹션을 가질 수 없습니다. PowerShell을 통해 SQL Server 구성 옵션을 관리해야 합니다. 또한 이미지가 원래 기반으로 했던 SQL VM의 속도에서 청구됩니다. 배포하기 전에 VHD에서 SQL Server를 제거하는 경우에도 마찬가지입니다. 

1. **가상 머신 갤러리에 표시되지 않은 구성을 설정할 수 있습니까(예: Windows 2008 R2 + SQL Server 2012)?**

   번호 SQL Server가 포함된 가상 머신 갤러리 이미지의 경우 제공된 이미지 중 하나를 선택해야 합니다.

## <a name="creation"></a>만들기

1. **SQL Server를 사용하여 Azure 가상 머신을 만들려면 어떻게 해야 합니까?**

   가장 쉬운 해결 방법은 SQL Server가 포함된 Virtual Machine을 만드는 것입니다. Azure에 등록하고 포털에서 SQL VM을 만드는 방법에 대한 자습서는 [Azure Portal에 SQL Server 가상 머신 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요. 초 단위로 요금이 부과되는 SQL Server 라이선스를 사용하는 가상 머신 이미지를 선택하거나 사용자 SQL Server 라이선스를 가져오도록 허용하는 이미지를 사용할 수 있습니다. 무료 라이선스 버전(Developer 또는 Express)을 사용하거나 온-프레미스 라이선스를 재사용하여 VM에 SQL Server를 수동으로 설치할 수도 있습니다. 사용자 라이선스가 필요하면 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)이 가능해야 합니다. 자세한 내용은 [SQL Server Azure VM에 대한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)을 참조하세요.

1. **온-프레미스 SQL Server 데이터베이스를 클라우드로 마이그레이션하려면 어떻게 해야 합니까?**

   가장 먼저 SQL Server 인스턴스를 사용하여 Azure 가상 머신을 만듭니다. 그런 다음 온-프레미스 데이터베이스를 해당 인스턴스로 마이그레이션합니다. 데이터 마이그레이션 전략에 대해서는 [Azure VM의 SQL Server로 SQL Server 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)을 참조하세요.

## <a name="licensing"></a>라이선스

1. **Azure VM에 라이선스가 있는 내 SQL Server 사본을 설치하려면 어떻게 해야 합니까?**

   두 가지 방법으로 이 작업을 수행할 수 있습니다. [라이선스를 지원하는 가상 머신 이미지](virtual-machines-windows-sql-server-iaas-overview.md#BYOL) 중 하나를 프로비전할 수 있습니다. 다른 옵션은 SQL Server 설치 미디어를 Windows Server VM으로 복사한 다음 VM에 SQL Server를 설치하는 것입니다. 라이선싱의 이유로 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)이 가능해야 합니다. 자세한 내용은 [SQL Server Azure VM에 대한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)을 참조하세요.

1. **종량제 갤러리 이미지 중 하나에서 만들어진 경우 사용자 고유의 SQL Server 라이선스를 사용하도록 VM을 변경할 수 있나요?**

   번호 초 단위로 요금이 부과되는 라이선스에서 사용자 라이선스 사용으로 전환할 수 없습니다. [BYOL 이미지](virtual-machines-windows-sql-server-iaas-overview.md#BYOL) 중 하나를 사용하여 새 Azure 가상 머신을 만든 다음 표준 [데이터 마이그레이션 기법](virtual-machines-windows-migrate-sql.md)을 사용하여 데이터베이스를 새 서버로 마이그레이션합니다.

1. **대기/장애 조치(failover)에만 사용되는 경우 Azure VM에서 SQL Server 라이선스 비용을 지불해야 하나요?**

   [Virtual Machine 라이선스 FAQ](http://azure.microsoft.com/pricing/licensing-faq/)에 설명된 대로 Software Assurance가 있고 License Mobility를 사용할 경우 HA 배포에서 수동 보조 복제본으로 참여하는 하나의 SQL Server 라이선스에 대해 비용을 지불할 필요가 없습니다. 그렇지 않으면 그에 대한 라이선스 비용을 지불해야 합니다.


## <a name="administration"></a>관리

1. **같은 VM에 SQL Server의 두 번째 인스턴스를 설치할 수 있나요? 기본 인스턴스의 설치된 기능을 변경할 수 있나요?**

   예. SQL Server 설치 미디어는 **C** 드라이브의 폴더에 있습니다. 이 위치에서 **Setup.exe** 를 실행하여 새 SQL Server 인스턴스를 실행하거나 컴퓨터에 설치된 다른 SQL Server 기능을 변경합니다. 자동화된 Backup, 자동화된 패치 및 Azure Key Vault 통합과 같은 일부 기능은 기본 인스턴스에 대해서만 작동합니다.

1. **SQL Server의 기본 인스턴스를 제거할 수 있나요?**

   예, 그러나 몇 가지 고려 사항이 있습니다. 앞의 답변에서 설명한 것처럼, [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 사용하는 기능은 기본 인스턴스에 대해서만 작동합니다. 기본 인스턴스를 제거하면 확장에서 계속 기본 인스턴스를 찾아 이벤트 로그 오류가 발생할 수 있습니다. 이러한 오류는 두 원본 **Microsoft SQL Server 자격 증명 관리** 및 **Microsoft SQL Server IaaS 에이전트**에서 발생합니다. 오류 중 하나는 다음과 유사할 수 있습니다.

      SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다.

   기본 인스턴스를 제거하려면 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)도 제거합니다.

1. **SQL VM에서 SQL Server를 완전히 제거할 수 있나요?**

   예, 그러나 [SQL Server Azure VM에 대한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)에 설명된 대로 SQL VM에 대한 비용 청구가 계속됩니다. SQL Server가 더 이상 필요하지 않는 경우 새 가상 컴퓨터를 배포하고 데이터와 응용 프로그램을 새 가상 컴퓨터에 마이그레이션할 수 있습니다. 그런 다음 SQL Server 가상 머신을 제거할 수 있습니다.
   
## <a name="updating-and-patching"></a>업데이트 및 패치

1. **Azure VM에서 SQL Server의 새 버전을 업그레이드하려면 어떻게 합니까?**

   현재는 Azure VM에서 실행 중인 SQL Server를 전체 업그레이드할 수 없습니다. 원하는 SQL Server 버전으로 새 Azure 가상 머신을 만든 다음 표준 [데이터 마이그레이션 기법](virtual-machines-windows-migrate-sql.md)을 사용하여 데이터베이스를 새 서버로 마이그레이션합니다.

1. **업데이트와 서비스 팩은 SQL Server VM에 어떻게 적용됩니까?**

   가상 머신에서는 호스트 컴퓨터에 업데이트를 적용할 시기와 방법 등을 제어할 수 있습니다. 운영 체제의 경우 Windows 업데이트를 수동으로 적용하거나 [자동 패칭](virtual-machines-windows-sql-automated-patching.md)이라는 예약 서비스를 사용할 수 있습니다. 자동 패칭은 해당 범주의 SQL Server 업데이트를 포함하여 중요함으로 표시된 업데이트를 설치합니다. SQL Server에 대한 기타 선택적 업데이트는 수동으로 설치해야 합니다.

## <a name="general"></a>일반

1. **SQL Server FCI(장애 조치 클러스터 인스턴스)는 Azure VM에서 지원되나요?**

   예. [Windows Server 2016에서 Windows 장애 조치 클러스터를 만들고](virtual-machines-windows-portal-sql-create-failover-cluster.md) 클러스터 저장소에 대한 S2D(저장소 공간 다이렉트)를 사용할 수 있습니다. 또는 [Azure Virtual Machines에서 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)에 설명된 대로 타사 클러스터링 또는 저장소 솔루션을 사용할 수 있습니다.

1. **SQL VM과 SQL Database는 어떤 점이 다릅니까?**

   개념적으로 Azure 가상 머신에서 SQL Server를 실행하는 것은 원격 데이터 센터에 SQL Server를 실행하는 것과 크게 다르지 않습니다. 반면, [SQL Database](../../../sql-database/sql-database-technical-overview.md) 는 DaaS(Database-as-a-Service)를 제공합니다. SQL Database에서는 데이터베이스를 호스팅하고 있는 컴퓨터에 액세스할 수 없습니다. 전체 비교를 보려면 [클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) 데이터베이스 또는 Azure VM의 SQL Server(IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)를 참조하세요.

1. **Azure VM에 SQL Data Tools를 설치하려면 어떻게 해야 합니까?**

    [Microsoft SQL Server Data Tools - Visual Studio 2013용 Business Intelligence](https://www.microsoft.com/en-us/download/details.aspx?id=42313)에서 SQL Data Tools를 다운로드하고 설치하세요.

## <a name="resources"></a>리소스

**Windows VM**:

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM 프로비전](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)
* [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines의 SQL Server에 대한 응용 프로그램 패턴 및 개발 전략](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**:

* [Linux VM의 SQL Server 개요](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux VM 프로비전](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ(Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
