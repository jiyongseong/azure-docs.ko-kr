---
title: Azure 지역 간 SAP HANA 가용성 | Microsoft Docs
description: 여러 Azure 지역의 Azure VM에서 SAP HANA를 실행하는 경우 가용성 고려 사항에 대해 간략히 설명합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: edbd1885dd529e4ccd38f2012d56865a2147f64d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>Azure 지역 간 SAP HANA 가용성

이 문서에서는 여러 Azure 지역에서 SAP HANA 가용성과 관련된 시나리오를 설명합니다. Azure 지역 간의 거리로 인해 여러 Azure 지역에서 SAP HANA 가용성을 설정하는 경우 특별한 고려 사항이 있습니다.

## <a name="why-deploy-across-multiple-azure-regions"></a>여러 Azure 지역에 배포해야 하는 이유

Azure 지역은 먼 거리로 인해 떨어져 있는 경우가 있습니다. 지정학적 지역에 따라 Azure 지역 간의 거리는 미국에서처럼 수백 마일 또는 수천 마일이 될 수 있습니다. 거리로 인해 서로 다른 두 Azure 지역에 배포된 자산 간의 네트워크 트래픽은 상당한 네트워크 왕복 대기 시간이 발생합니다. 대기 시간은 일반적인 SAP 워크로드 하에서 두 개의 SAP HANA 인스턴스 간의 동기 데이터 교환을 제외할 정도로 중요합니다. 

반면, 조직에는 종종 주 데이터 센터와 보조 데이터 센터의 위치 간 거리 요구 사항이 있습니다. 더 넓은 지리적 위치에서 자연 재해가 발생하는 경우 거리 요구 사항은 가용성을 제공하는 데 도움이 됩니다. 예를 들어 허리케인이 2017년 9월과 10월에 카리브 해와 플로리다에서 발생했습니다. 이 경우 조직에 최소 거리 요구 사항이 있을 수 있습니다. 대부분의 Azure 고객에 대해 이 최소 거리를 정의하려면 [Azure 지역](https://azure.microsoft.com/regions/) 간의 가용성을 설계해야 합니다. 두 Azure 지역 간의 거리가 너무 멀어 HANA 동기 복제 모드를 사용할 수 없으므로, RTO 및 RPO 요구 사항에 따라 한 지역의 가용성 구성을 배포한 다음, 두 번째 지역에 추가 배포를 보완하도록 적용할 수 있습니다.

이 시나리오에서 고려해야 할 또 다른 측면은 장애 조치 및 클라이언트 리디렉션입니다. 다른 두 Azure 지역에 있는 SAP HANA 인스턴스 간의 장애 조치가 항상 수동 장애 조치라고 가정합니다. SAP HANA 시스템 복제의 복제 모드가 비동기로 설정되었으므로, 주 HANA 인스턴스에서 커밋된 데이터가 아직 보조 HANA 인스턴스에 적용되지 않았을 수도 있습니다. 따라서 복제가 비동기인 구성에서 자동 장애 조치는 옵션이 아닙니다. 장애 조치 연습과 같이 수동으로 제어되는 장애 조치를 사용하는 경우에도 다른 Azure 지역으로 수동으로 이동하기 전에 주 인스턴스 쪽에서 커밋된 모든 데이터가 보조 인스턴스에 적용되도록 하는 작업을 수행해야 합니다.
 
Azure Virtual Network에서는 다른 IP 주소 범위를 사용합니다. IP 주소는 두 번째 Azure 지역에 배포됩니다. 따라서 SAP HANA 클라이언트 구성을 변경하거나 이름 확인을 변경하기 위한 단계를 만들어야 합니다. 이렇게 하면 클라이언트가 새 보조 사이트의 서버 IP 주소로 리디렉션됩니다. 자세한 내용은 [인수 후 클라이언트 연결 복구](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html) SAP 문서를 참조하세요.   

## <a name="simple-availability-between-two-azure-regions"></a>두 Azure 지역 간의 간단한 가용성

단일 지역 내에 가용성 구성을 배치하지 않도록 선택 수도 있지만, 재해가 발생하는 경우에도 워크로드를 처리해야 합니다. 이와 같은 시스템에 대한 일반적 사례는 비프로덕션 시스템입니다. 시스템을 반나절 또는 하루 동안 중단한 생태로 유지할 수 있지만, 48시간 이상 사용할 수 없도록 허용할 수는 없습니다. 설치 비용을 줄이려면 VM에서 덜 중요한 다른 시스템을 실행합니다. 다른 시스템은 대상으로 작동합니다. 또한 보조 지역의 VM 크기를 더 작게 조정할 수 있고 데이터를 미리 로드하지 않도록 선택할 수도 있습니다. 장애 조치가 수동으로 수행되고 전체 응용 프로그램 스택을 장애 조치하는 데 더 많은 단계가 필요하므로, VM을 종료하고 크기를 조정한 다음, VM을 재시작하는 추가 시간이 허용됩니다.

> [!NOTE]
> HANA 시스템 복제 대상에서 데이터 미리 로드를 사용하지 않는 경우에도 64GB 이상의 메모리가 필요합니다. 또한 64GB 외에도, rowstore 데이터를 대상 인스턴스의 메모리에 유지하기 위해 충분한 메모리가 필요합니다.

![두 지역의 두 VM에 대한 다이어그램](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> 이 구성에서는 HANA 시스템 복제 모드가 비동기이므로 RPO=0을 제공할 수 없습니다. RPO=0을 제공해야 하는 경우 이 구성은 선택할 수 있는 구성이 아닙니다.

구성에서 수행할 수 있는 사소한 변경 작업은 데이터를 미리 로드로 구성하는 것일 수 있습니다. 그러나 장애 조치의 수동 특성 및 응용 프로그램 계층이 두 번째 지역으로 이동해야 하는 한다는 사실을 고려할 때 데이터를 미리 로드하는 것이 적합하지 않을 수 있습니다. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>단일 지역 내 및 여러 지역 간 가용성 결합 

지역 내 및 지역 간 가용성의 결합은 다음과 같은 요인으로 결정될 수 있습니다.

- 한 Azure 지역 내의 RPO=0에 대한 요구 사항
- 더 큰 지역에 영향을 주는 주요 자연 재해로 인해 영향을 받는 회사의 전역 작업에 대한 의지 또는 능력 부재. 여기에는 지난 몇 년 동안 카리브 해에 발생한 일부 허리케인의 경우가 있습니다.
- Azure 가용성 영역에서 제공할 수 있는 것보다 더 명확하게 주 사이트와 보조 사이트 간의 거리를 요구하는 규정

이러한 경우 HANA 시스템 복제를 사용하여 [SAP HANA 다계층 시스템 복제 구성](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html)을 호출하는 SAP를 설정할 수 있습니다. 아키텍처는 다음과 같습니다.

![두 지역의 세 VM에 대한 다이어그램](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

이 구성은 주 지역 내에서 RTO가 낮은 RPO=0을 제공합니다. 또한 이 구성은 두 번째 지역으로의 이동과 관련되는 경우에도 적절한 RPO를 제공합니다. 두 번째 지역의 RTO 시간은 데이터가 미리 로드되었는지 여부에 따라 달라집니다. 많은 고객이 보조 지역의 VM을 사용하여 테스트 시스템을 실행합니다. 이 경우 데이터는 미리 로드할 수 없습니다.

> [!NOTE]
> 계층 1에서 계층 2로 이동하는 HANA 시스템 복제(주 지역의 동기 복제)에 **logreplay** 작업 모드를 사용하므로, 계층 2와 계층 3 간의 복제(보조 사이트에 복제)는 **delta_datashipping** 작업 모드에 있을 수 없습니다. 작업 모드에 대한 자세한 내용 및 몇 가지 제한 사항은 [SAP HANA 시스템 복제에 대한 작업 모드](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) SAP 문서를 참조하세요. 

## <a name="next-steps"></a>다음 단계

Azure에서 이러한 설정을 구성하는 단계별 지침은 다음을 참조하세요.

- [Azure VM에서 SAP HANA 시스템 복제 설정](sap-hana-high-availability.md)
- [시스템 복제를 사용한 SAP HANA 고가용성](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
