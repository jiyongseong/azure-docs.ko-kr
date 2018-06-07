---
title: 사용자 지정 이미지에서 Azure Batch 풀 프로비전| Microsoft Docs
description: 사용자 지정 이미지에서 Batch 풀을 만들어 애플리케이션에 대해 사용자가 필요한 소프트웨어 및 데이터가 들어 있는 계산 노드를 프로비전할 수 있습니다. 사용자 지정 이미지는 Batch 워크로드를 실행하도록 계산 노드를 구성하는 효율적인 방법입니다.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 78bc50a1189d8f42281f81643a5e907d94480082
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32158615"
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>관리되는 사용자 지정 이미지를 사용하여 가상 머신 풀 만들기 

Virtual Machine 구성을 사용하여 Azure Batch 풀을 만들 경우 풀에서 각 계산 노드에 대해 운영 체제를 제공하는 VM 이미지를 지정합니다. Azure Marketplace 이미지 또는 사용자 지정 이미지(사용자가 만들고 직접 구성한 VM 이미지)를 사용하여 가상 머신 풀을 만들 수 있습니다. 사용자 지정 이미지는 배치 계정과 같이 동일한 Azure 구독 및 지역의 *관리되는 이미지*여야 합니다.

## <a name="why-use-a-custom-image"></a>사용자 지정 이미지를 사용하는 이유
사용자 이미지를 제공할 경우에는 운영 체제 구성, 사용할 운영 체제의 유형 및 데이터 디스크를 관리합니다. 사용자 지정 이미지는 프로비전되는 즉시 모든 Batch 풀 노드에서 사용할 수 있는 응용 프로그램 및 참조 데이터를 포함할 수 있습니다.

사용자 지정 이미지를 사용하면 풀의 계산 노드가 Batch 워크로드를 실행하는 데 걸리는 시간을 절약할 수 있습니다. Azure Marketplace 이미지를 사용할 경우 프로비전 후 각 계산 노드에서 소프트웨어를 설치할 수 있지만 사용자 지정 이미지를 사용하면 효율성을 높일 수 있습니다.

시나리오에 맞게 구성된 사용자 지정 이미지를 사용하면 몇 가지 이점을 제공할 수 있습니다.

- **OS(운영 체제) 구성**. 사용자 지정 이미지의 운영 체제 디스크에서 운영 체제의 특별한 구성을 모두 수행할 수 있습니다. 
- **응용 프로그램 미리 설치**. OS 디스크에 사전 설치된 응용 프로그램으로 사용자 지정 이미지를 만들 수 있으며, 이 경우 StartTask를 사용하여 계산 노드를 프로비전한 후 응용 프로그램을 설치하는 경우에 비해 효율성이 향상되고 오류 발생 가능성이 감소합니다.
- **VM에서 재부팅 시간 절감.** 응용 프로그램을 설치할 경우 일반적으로 VM을 재부팅해야 하며, 이 작업은 많은 시간이 소요됩니다. 응용 프로그램을 사전 설치하면 재부팅 시간을 절감할 수 있습니다. 
- **많은 데이터를 한 번에 복사.** 관리되는 사용자 지정 이미지를 관리되는 이미지의 데이터 디스크로 복사하여 관리되는 사용자 지정 이미지의 정적 데이터 부분을 만들 수 있습니다. 이 작업은 한 번만 수행하며 풀의 각 노드에서 데이터를 사용할 수 있게 됩니다.
- **디스크 유형 선택.** VHD, Azure VM의 관리되는 디스크, 이러한 디스크의 스냅숏 또는 사용자가 구성한 Linux 또는 Windows 설치에서 관리되는 사용자 지정 이미지를 만들 수 있습니다. OS 디스크 및 데이터 디스크에 Premium Storage를 사용할 수 있습니다.
- **모든 크기에 대한 풀 증가.** 풀을 만들기 위해 관리되는 사용자 지정 이미지를 사용하는 경우 사용자가 요청하는 크기로 풀을 증가할 수 있습니다. 많은 VM을 지원하기 위해 이미지 Blob VHD의 사본을 만들 필요는 없습니다. 


## <a name="prerequisites"></a>필수 조건

- **관리되는 이미지 리소스**. 사용자 지정 이미지를 사용하여 가상 머신 풀을 만들려면 배치 계정과 동일한 Azure 구독 및 지역에 관리되는 이미지 계정을 만들어야 합니다. 관리되는 이미지를 만들기 위한 옵션을 보려면 다음 섹션을 참조하세요.
- **AAD(Azure Active Directory) 인증**. Batch 클라이언트 API가 AAD 인증을 사용해야 합니다. AAD에 대한 Azure Batch 지원은 [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)에 설명되어 있습니다.

    
## <a name="prepare-a-custom-image"></a>사용자 지정 이미지 준비
VHD, 관리되는 디스크를 사용하는 Azure VM, VM 스냅숏에서 관리되는 이미지를 만들 수 있습니다. Batch의 경우 관리 디스크 또는 VM 스냅숏으로 VM에서 관리되는 이미지를 만드는 것이 좋습니다. 관리되는 이미지 및 기본 리소스는 강화하도록 풀에 대해 있어야 하고 풀이 삭제되면 제거할 수 있습니다. 

이미지를 준비할 때는 다음 사항에 유의해야 합니다.

* Batch 풀을 프로비전하는 데 사용할 기본 OS 이미지에 사전 설치된 Azure 확장(사용자 지정 스크립트 확장)이 없는지 확인합니다. 이미지에 사전 설치된 확장이 있는 경우 Azure에서 VM 배포 시 문제가 발생할 수 있습니다.
* 제공하는 기본 OS 이미지가 기본 임시 드라이브를 사용하도록 해야 합니다. Batch 노드 에이전트는 현재 기본 임시 드라이브를 예상합니다.
* Batch 풀에서 참조하는 관리되는 이미지 리소스는 풀의 수명 동안 삭제할 수 없습니다. 관리되는 이미지 리소스가 삭제된 경우 풀을 더 이상 증가할 수 없습니다. 

### <a name="to-create-a-managed-image"></a>관리되는 이미지 만들기
기존에 준비된 Windows 또는 Linux 운영 체제 디스크를 사용하여 관리되는 이미지를 만들 수 있습니다. 예를 들어 로컬 이미지를 사용하려면 AzCopy 또는 다른 업로드 도구를 사용하여 로컬 디스크를 배치 계정과 동일한 구독 및 지역에 있는 Azure Storage 계정에 업로드합니다. VHD를 업로드하고 관리되는 이미지를 만드는 방법에 대한 자세한 절차는 [Windows](../virtual-machines/windows/upload-generalized-managed.md) 또는 [Linux](../virtual-machines/linux/upload-vhd.md) VM에 대한 지침을 참조하세요.

새로운 또는 기존 Azure VM 또는 VM 스냅숏에서 관리되는 이미지를 준비할 수도 있습니다. 

* 새 VM을 만드는 경우 Azure Marketplace 이미지를 관리되는 이미지에 대한 기본 이미지로 사용한 후 사용자 지정할 수 있습니다. 

* 포털을 사용하여 이미지를 캡처하려는 경우에는 관리되는 디스크를 사용하여 VM을 만들도록 하세요. VM을 만들 때 기본 저장소 설정입니다.

* VM이 실행되면 RDP(Windows용) 또는 SSH(Linux용)를 통해 연결합니다. 필요한 소프트웨어를 설치하거나 원하는 데이터를 복사한 후 VM을 일반화합니다.  

Azure VM을 일반화하고 관리되는 이미지를 만드는 방법에 대한 자세한 절차는 [Windows](../virtual-machines/windows/capture-image-resource.md) 또는 [Linux](../virtual-machines/linux/capture-image.md) VM에 대한 지침을 참조하세요.

이미지를 사용하여 Batch 풀을 어떻게 만들 것인가에 따라 이미지에 대해 다음 식별자가 필요합니다.

* Batch API를 사용하여 이미지로 풀을 만들려는 경우 이미지의 **리소스 ID** (`/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` 양식). 
* 포털을 사용하려는 경우 이미지의 **이름**. 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>포털의 사용자 지정 이미지에서 풀 만들기

사용자 지정 이미지를 저장했고 리소스 ID 또는 이름을 알고 있으면 해당 이미지에서 Batch 풀을 만들 수 있습니다. 다음 단계는 Azure Portal에서 풀을 만드는 방법을 보여줍니다.

> [!NOTE]
> Batch API 중 하나를 사용하여 풀을 만드는 경우 AAD 인증에 사용하는 ID가 이미지 리소스에 대한 권한을 가지고 있는지 확인합니다. [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)을 참조하세요.
>

1. Azure Portal에서 Batch 계정으로 이동합니다. 이 계정은 사용자 지정 이미지가 포함된 리소스 그룹과 동일한 구독 및 지역에 있어야 합니다. 
2. 왼쪽의 **설정** 창에서 **풀** 메뉴 항목을 선택합니다.
3. **풀** 창에서 **추가** 명령을 선택합니다.
4. **풀 추가** 창의 **이미지 형식** 드롭다운에서 **사용자 지정 이미지(Linux/Windows)** 를 선택합니다. **사용자 지정 VM 이미지** 드롭다운에서 이미지 이름(리소스 ID의 약식)을 선택합니다.
5. 사용자 지정 이미지에 대해 올바른 **게시자/제품/Sku**를 선택합니다.
6. **노드 크기**, **대상 전용 노드** 및 **낮은 우선 순위 노드** 등 나머지 필수 설정과 선택 사항인 설정을 원하는 대로 지정합니다.

    예를 들어 Microsoft Windows Server Datacenter 2016 사용자 지정 이미지에 대해서는 아래 표시된 것처럼 **풀 추가** 창이 나타납니다.

    ![사용자 지정 Windows 이미지에서 풀 추가](media/batch-custom-images/add-pool-custom-image.png)
  
기존 풀이 사용자 지정 이미지를 기반으로 하는지 확인하려면 **풀** 창의 리소스 요약 섹션에 있는 **운영 체제** 속성을 참조하세요. 사용자 지정 이미지에서 풀을 만든 경우 **사용자 지정 VM 이미지**로 설정합니다.

풀과 연결된 모든 사용자 지정 이미지가 해당 풀의 **속성** 창에 표시됩니다.
 
## <a name="next-steps"></a>다음 단계

- Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)을 참조하세요.
