---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: f403e060859df6d1de96a3c0d478d57df2677eee
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "31531058"
---
다음은 할당 요청이 고정되도록 하는 일반적인 할당 시나리오입니다. 이 문서의 뒷 부분에서 각 시나리오에 대해 자세히 알아봅니다.

- VM 크기 조정 또는 기존 클라우드 서비스에 VM 또는 역할 인스턴스 추가
- 부분적으로 중지(할당 취소)된 VM 다시 시작
- 완전히 중지(할당 취소)된 VM 다시 시작
- 준비 및 프로덕션 배포(Platform as a Service 전용)
- 선호도 그룹(VM 또는 서비스 근접)
- 선호도 그룹 기반 가상 네트워크

할당 오류가 발생하면 나열된 시나리오 중 하나라도 오류에 적용되는지 확인합니다. Azure 플랫폼에서 반환된 할당 오류를 사용하여 해당하는 시나리오를 식별합니다. 요청이 고정된 경우에는 고정시키는 일부 제약 조건을 제거하여 더 많은 클러스터에 요청을 열고 할당이 성공할 기회를 높입니다.
일반적으로 "요청한 VM 크기가 지원되지 않습니다."라는 오류가 표시되지 않으면 나중에 언제든지 다시 시도할 수 있습니다. 요청을 수용하기에 충분한 리소스가 클러스터에서 해제될 수 있기 때문입니다. 요청한 VM 크기가 지원되지 않는 것이 문제라면 다른 VM 크기를 시도하세요. 그렇지 않은 경우 유일한 옵션은 고정 제약 조건을 제거하는 것입니다.

두 가지 일반적인 실패 시나리오는 선호도 그룹과 관련되어 있습니다. 과거에 선호도 그룹은 VM 및 서비스 인스턴스에 가까운 근접성을 제공하는 데 사용되거나 가상 네트워크를 생성할 수 있도록 하는 데 사용되었습니다. 지역 가상 네트워크가 도입되면서 선호도 그룹이 가상 네트워크 생성에 더 이상 필요하지 않게 되었습니다. Azure 인프라의 네트워크 대기 시간이 감소하면서 VM 또는 서비스 근접성을 위해 선호도 그룹을 사용하는 권장 사항이 변경되었습니다.

아래 다이어그램은 (고정된) 할당 시나리오의 분류 방식을 나타냅니다. 

![고정된 할당 분류법](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>VM 크기 조정 또는 기존 클라우드 서비스에 VM 또는 역할 인스턴스 추가
**오류**

Upgrade_VMSizeNotSupported 또는 GeneralError

**클러스터 고정의 원인**

VM 크기를 조정하거나 기존 클라우드 서비스에 VM 또는 역할 인스턴스를 추가하는 요청은 기존 클라우드 서비스를 호스트하는 원래 클러스터에서 시도되어야 합니다. 클라우드 서비스를 새로 만들면 Azure 플랫폼에서 유휴 리소스가 있거나 사용자가 요청한 VM 크기를 지원하는 또 다른 클러스터를 찾을 수 있습니다.

**해결 방법**

오류가 Upgrade_VMSizeNotSupported*이면, 다른 VM 크기를 시도합니다. 다른 VM 크기를 사용할 수는 없지만 다른 VIP(가상 IP 주소)를 사용하는 것이 허용되면, 새로운 VM을 호스트할 새 클라우드 서비스를 만들고 새 클라우드 서비스를 기존 VM이 실행되는 지역 가상 네트워크에 추가합니다. 기존 클라우드 서비스가 지역 가상 네트워크를 사용하지 않더라도 새 클라우드 서비스에 새 가상 네트워크를 만든 후 [기존 가상 네트워크를 새 가상 네트워크](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)에 연결할 수 있습니다. [지역 가상 네트워크](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)에 대한 자세한 내용을 참조하세요.

오류가 GeneralError*이면 리소스 유형(예: 특정한 VM 크기)이 클러스터에서 지원되지만 해당 시점에 클러스터에 유휴 리소스가 없을 가능성이 있습니다. 위 시나리오와 유사하게 새 클라우드 서비스를 만들고(새 클라우드 서비스는 다른 VIP를 사용해야 함) 지역 가상 네트워크를 사용하여 클라우드 서비스로 연결하여 원하는 계산 리소스를 추가합니다.

## <a name="restart-partially-stopped-deallocated-vms"></a>부분적으로 중지(할당 취소)된 VM 다시 시작
**오류**

GeneralError*

**클러스터 고정의 원인**

일부 할당 취소란 클라우드 서비스에서 하나 이상을 중지(할당 취소)했지만 모든 VM을 중지한 것은 아니라는 의미입니다. VM을 중지(할당 취소)하면 연결된 리소스가 해제됩니다. 따라서 중지(할당 취소)한 VM을 재시작하는 것은 새로운 할당 요청입니다. 부분적으로 할당 취소된 클라우드 서비스에서 VM을 다시 시작하는 것은 기존 클라우드 서비스에 VM을 추가하는 것과 같습니다. 할당 요청은 기존 클라우드 서비스를 호스트하는 원래 클러스터에서 시도되어야 합니다. 다른 클라우드 서비스를 만들면 Azure 플랫폼에서 유휴 리소스가 있거나 사용자가 요청한 VM 크기를 지원하는 또 다른 클러스터를 찾을 수 있습니다.

**해결 방법**

다른 VIP를 사용하는 것이 허용되면 중지된(할당 취소된) VM을 삭제하고(하지만 연결된 디스크는 유지한 채로) 다른 클라우드 서비스를 통해 VM을 다시 추가합니다. 지역 가상 네트워크를 사용하여 클라우드 서비스에 연결합니다.

* 기존 클라우드 서비스가 지역 가상 네트워크를 사용하면 새 클라우드 서비스를 동일한 가상 네트워크에 간단히 추가합니다.
* 기존 클라우드 서비스가 지역 가상 네트워크를 사용하지 않더라도 새 클라우드 서비스에 새 가상 네트워크를 만든 후 [기존 가상 네트워크를 새 가상 네트워크](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)에 연결합니다. [지역 가상 네트워크](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)에 대한 자세한 내용을 참조하세요.

## <a name="restart-fully-stopped-deallocated-vms"></a>완전히 중지(할당 취소)된 VM 다시 시작
**오류**

GeneralError*

**클러스터 고정의 원인**

전체 할당 취소란 클라우드 서비스의 모든 VM을 중지(할당 취소)했다는 의미입니다. VM 재시작을 위한 할당 요청은 클라우드 서비스를 호스트하는 원래 클러스터에서 시도되어야 합니다. 클라우드 서비스를 새로 만들면 Azure 플랫폼에서 유휴 리소스가 있거나 사용자가 요청한 VM 크기를 지원하는 또 다른 클러스터를 찾을 수 있습니다.

**해결 방법**

다른 VIP를 사용하는 것이 허용되면, 원래 중지(할당 취소)된 VM을 연결된 디스크를 유지한 채로 삭제하고 해당 클라우드 서비스를 삭제합니다. 연결된 계산 리소스는 VM을 중지(할당 취소)할 때 이미 해제되었습니다. 새 클라우드 서비스를 만들어서 VM에 다시 추가합니다.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>스테이징/프로덕션 배포(Platform as a Service 전용)
**오류**

New_General* 또는 New_VMSizeNotSupported\*

**클러스터 고정의 원인**

클라우드 서비스의 스테이징 배포 및 프로덕션 배포는 동일한 클러스터에서 호스트됩니다. 두 번째 배포를 추가하는 경우 할당 요청은 첫 번째 배포를 호스팅하는 동일한 클러스터에서 시도됩니다.

**해결 방법**

첫 번째 배포와 원래 클라우드 서비스를 삭제하고 클라우드 서비스를 다시 배포합니다. 이렇게 하면 2개의 배포를 수용하기에 충분한 유휴 리소스가 있는 클러스터나 요청한 VM 크기를 지원하는 클러스터에 첫 번째 배포가 배치될 수 있습니다.

## <a name="affinity-group-vmservice-proximity"></a>선호도 그룹(VM/서비스 근접)
**오류**

New_General* 또는 New_VMSizeNotSupported\*

**클러스터 고정의 원인**

선호도 그룹에 할당된 모든 계산 리소스는 하나의 클러스터에 구속됩니다. 해당 선호도 그룹에 속하는 새로운 계산 리소스 요청은 기존 리소스를 호스트하는 동일한 클러스터에서 시도됩니다. 새로운 리소스가 새 클라우드 서비스에서 만들어지건 아니면 기존 클라우드 서비스를 통해 만들어지건 이 내용은 사실입니다.

**해결 방법**

선호도 그룹이 필요하지 않으면 선호도 그룹을 사용하지 않거나 여러 선호도 그룹의 계산 리소스를 그룹화합니다.

## <a name="affinity-group-based-virtual-network"></a>선호도-그룹-기반 가상 네트워크
**오류**

New_General* 또는 New_VMSizeNotSupported\*

**클러스터 고정의 원인**

지역 가상 네트워크가 도입되기 전에는 가상 네트워크를 선호도 그룹과 연결해야 했습니다. 그 결과 선호도 그룹에 배치된 계산 리소스가 위의 “할당 시나리오: 선호도 그룹(VM/서비스 근접성)” 섹션의 설명처럼 동일한 제약 조건에 바인딩되었습니다. 계산 리소스는 한 개의 클러스터에 연결됩니다.

**해결 방법**

선호도 그룹이 필요하지 않으면 추가하는 새 리소스에 대해 새로운 지역 가상 네트워크를 만들고 [기존 가상 네트워크를 새 가상 네트워크에 연결](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)합니다. [지역 가상 네트워크](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)에 대한 자세한 내용을 참조하세요.

또는 [선호도 그룹 기반 가상 네트워크를 지역 가상 네트워크로 마이그레이션](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)한 다음 원하는 리소스를 다시 추가할 수 있습니다.
