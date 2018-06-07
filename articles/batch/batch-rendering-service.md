---
title: Azure Batch Rendering 서비스 - 클라우드 규모 렌더링 | Microsoft Docs
description: Maya에서 직접 사용량 기준 과금으로 Azure 가상 머신의 작업을 렌더링합니다.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: danlep
ms.openlocfilehash: df1b2da7628e6c3f9f4bcbb02a936c33aad49698
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="get-started-with-the-batch-rendering-service"></a>Batch Rendering 서비스 시작

Azure Batch Rendering 서비스는 클라우드 규모 렌더링 기능을 사용량 기준 과금으로 제공합니다. Batch Rendering 서비스는 렌더링 작업의 작업 일정 및 큐, 실패 및 재시도 관리, 자동 크기 조정을 처리합니다. Batch Rendering Service는 [Autodesk Maya](https://www.autodesk.com/products/maya/overview), [3ds Max](https://www.autodesk.com/products/3ds-max/overview), [Arnold](https://www.autodesk.com/products/arnold/overview), [V-Ray](https://www.chaosgroup.com/vray/maya) 등의 렌더링 앱을 지원합니다. Maya 2017의 Batch 플러그 인을 사용하면 바탕 화면에서 바로 Azure의 렌더링 작업을 시작할 수 있습니다.

Maya 및 3ds Max를 통해 [BatchLabs](https://github.com/Azure/BatchLabs) 데스크톱 응용 프로그램 또는 [Batch 템플릿 CLI](batch-cli-templates.md)를 사용하여 작업을 실행할 수 있습니다. Azure Batch CLI를 사용하면 코드를 작성하지 않고 Batch 작업을 실행할 수 있습니다. 대신, 템플릿 파일을 사용하여 Batch 풀, 작업, 및 태스크를 만들 수 있습니다. 자세한 내용은 [Azure Batch CLI 템플릿 및 파일 전송 사용](batch-cli-templates.md)을 참조하세요.


## <a name="supported-applications"></a>지원되는 응용 프로그램

Batch Renderin 서비스는 현재 다음과 같은 응용 프로그램을 지원합니다.

CentOS 7의 렌더링 노드:
- Autodesk Maya I/O 2017 업데이트 5(201708032230 잘라내기)
- Autodesk Maya I/O 2018 업데이트 2 201711281015 잘라내기
- Autodesk Arnold for Maya 2017(Arnold 버전 5.0.1.1) MtoA-2.0.1.1-2017
- Autodesk Arnold for Maya 2018(Arnold 버전 5.0.1.4) MtoA-2.1.0.3-2018
- Maya 2017용 Chaos Group V-Ray(버전 3.60.04) 
- Maya 2018용 Chaos Group V-Ray(버전 3.60.04) 
- Blender(2.68)

Windows Server 2016의 렌더링 노드:
- Autodesk Maya I/O 2017 업데이트 5(버전 17.4.5459) 
- Autodesk Maya I/O 2018 업데이트 2(버전 18.2.0.6476) 
- Autodesk 3ds Max I/O 2018 업데이트 4(버전 20.4.0.4254) 
- Maya용 Autodesk Arnold(Arnold 버전 5.0.1.1) MtoA-2.0.1.1-2017
- Maya용 Autodesk Arnold(Arnold 버전 5.0.1.4) MtoA-2.0.2.3-2018
- 3ds Max용 Autodesk Arnold(Arnold 버전 5.0.2.4) (버전 1.2.926) 
- Maya용 Chaos Group V-Ray(버전 3.52.03) 
- 3ds Max용 Chaos Group V-Ray(버전 3.60.02)
- Blender(2.79)


## <a name="prerequisites"></a>필수 조건

Batch Rendering 서비스를 사용하려면 다음이 필요합니다.

- [Azure 계정](https://azure.microsoft.com/free/)입니다.
- **Azure Batch 계정입니다.** Azure Portal에서 Batch 계정을 만드는 방법에 대한 지침은 [Azure Portal에서 Batch 계정 만들기](batch-account-create-portal.md)를 참조하세요.
- **Azure Storage 계정**입니다. 렌더링 작업에 사용되는 자산은 일반적으로 Azure Storage에 저장됩니다. Batch 계정을 설정할 때 자동으로 저장소 계정을 만들 수 있습니다. 기존 저장소 계정을 사용할 수도 있습니다. Batch의 저장소 계정 옵션은 [Batch 기능 개요](batch-api-basics.md#azure-storage-account)를 참조하세요.
- **환경 변수**입니다. 사용자의 솔루션이 환경 변수를 수정하는 경우, 사용이 허가된 위의 응용 프로그램 중 하나 이상이 호출될 때 `AZ_BATCH_ACCOUNT_URL` 및 `AZ_BATCH_SOFTWARE_ENTITLEMENT_TOKEN`의 값이 그대로 유지되고 존재하는지 확인하십시오. 그렇지 않으면 소프트웨어 활성화 문제가 발생할 가능성이 있습니다.
- **BatchLabs**(선택 사항). [BatchLabs](https://azure.github.io/BatchLabs)는 Azure Batch 응용 프로그램을 만들고, 디버그하고, 모니터링할 수 있도록 하는 무료의 풍부한 기능을 가진 독립 실행형 클라이언트 도구입니다. Rendering 서비스를 사용할 필요 없이 Batch 솔루션을 개발하고 디버그하는 유용한 옵션입니다.

Maya에 Batch 플러그 인을 사용하려면 다음이 필요합니다.

- [Autodesk Maya 2017](https://www.autodesk.com/products/maya/overview).
- Maya용 Arnold 또는 Maya용 V-Ray와 같은 지원되는 렌더러.

## <a name="basic-batch-concepts"></a>기본 Batch 개념

Batch Rendering 서비스를 사용하기 전에 계산 노드, 풀, 작업을 포함한 몇 가지 Batch 개념을 익히는 것이 좋습니다. Azure Batch에 대한 일반적인 내용은 [Batch를 사용하여 본질적인 병렬 워크로드 실행](batch-technical-overview.md)을 참조하세요.

### <a name="pools"></a>풀

Batch는 렌더링처럼 계산 집약적인 작업을 **계산 노드** **풀**에서 실행하기 위한 플랫폼 서비스입니다. 풀의 각 계산 노드는 Linux 또는 Windows에서 실행 중인 Azure VM(가상 머신)입니다. 

Batch 풀 및 계산 노드에 대한 자세한 내용은 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)의 [풀](batch-api-basics.md#pool) 및 [Compute 노드](batch-api-basics.md#compute-node) 섹션을 참조하세요.

### <a name="jobs"></a>교육

Batch **작업**은 풀의 계산 노드에서 실행되는 작업 컬렉션입니다. 렌더링 작업을 제출하면 Batch에서 작업을 여러 작업으로 분할하고 실행할 작업을 풀의 계산 노드에 분산합니다.

응용 프로그램 로그를 다운로드하고 RDP 또는 SSH를 사용하여 개별 VM에 원격으로 연결하면 [Azure Portal](https://ms.portal.azure.com/)을 사용하여 작업을 모니터링하고 실패한 작업을 진단할 수 있습니다. 또한 [BatchLabs 도구](https://azure.github.io/BatchLabs)를 사용하여 관리, 모니터링 및 디버그할 수 있습니다.

Batch 작업에 대한 자세한 내용은 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)의 [작업](batch-api-basics.md#job) 섹션을 참조하세요.

## <a name="options-for-provisioning-required-applications"></a>필수 응용 프로그램 프로비전을 위한 옵션

예를 들어 Maya와 Arnold 또는 3DS Max와 V-Ray의 조합과 같은 작업을 렌더링하는 데 필요한 경우 타사 플러그 인과 더불어 여러 응용 프로그램이 필요할 수 있습니다. 또한 일부 고객은 이러한 응용 프로그램의 특정 버전이 필요할 수도 있습니다. 이와 같이 필요한 응용 프로그램 및 소프트웨어를 프로비전하는 데 사용할 수 있는 방법에는 몇 가지가 있습니다.

### <a name="pre-configured-vm-images"></a>사전 구성 VM 이미지

Azure는 사전 설치되어 사용이 가능한 Maya, 3DS Max, Arnold 및 V-Ray의 각 버전과 함께 Windows 및 Linux 이미지를 제공합니다. 풀을 만들 때 [Azure Portal](https://portal.azure.com), Maya 플러그 인 또는 [BatchLabs](https://azure.github.io/BatchLabs)에서 이러한 이미지를 선택할 수 있습니다.

Azure Portal 및 BatchLabs에서 사전 설치된 응용 프로그램으로 VM 이미지 중 하나를 설치할 수 있습니다. 배치 계정의 풀 섹션에서 **새로 만들기**를 선택한 다음, **풀 추가**의 **이미지 형식** 드롭다운 목록에서 **그래픽 및 렌더링(Linux/Windows)** 을 선택하면 됩니다.

![배치 계정에 대한 이미지 형식 선택](./media/batch-rendering-service/add-pool.png)

아래로 스크롤하고 **그래픽 및 렌더링 라이선스** 아래에서 **소프트웨어 및 가격 책정 선택**을 클릭합니다. 소프트웨어 라이선스를 하나 이상 선택:

![풀에 대한 그래픽 및 렌더링 라이선스 선택](./media/batch-rendering-service/graphics-licensing.png)

제공된 특정 라이선스 버전이 위의 "지원되는 응용 프로그램" 섹션에 나오는 버전과 일치합니다.

### <a name="custom-images"></a>사용자 지정 이미지

Azure Batch를 사용하면 사용자 지정 이미지를 제공할 수 있습니다. 이 옵션을 사용하여 정확한 응용 프로그램 및 필요한 특정 버전으로 VM을 구성할 수 있습니다. 자세한 내용은 [사용자 지정 이미지를 사용하여 가상 머신 풀 만들기](https://docs.microsoft.com/azure/batch/batch-custom-images)를 참조하세요. Autodesk 및 Chaos Group은 자체 라이선스 서비스에 대한 유효성을 검사하기 위해 Arnold 및 V-Ray를 각각 수정하였습니다. 이 지원을 통해 이러한 응용 프로그램 버전이 있는지 확인해야 합니다. 그렇지 않으면 사용량 과금 라이선스가 적용되지 않습니다. 이 라이선스 유효성 검사는 Maya 또는 3DS Max에는 필요하지 않습니다. 현재 게시된 버전은 헤드리스 실행 시(배치/명령줄 모드에서) 라이선스 서버가 필요하지 않기 때문입니다. 이 옵션을 진행하는 방법을 잘 모르는 경우 Azure 지원 담당자에게 문의하세요.

## <a name="options-for-submitting-a-render-job"></a>렌더링 작업 제출을 위한 옵션

사용하는 3D 응용 프로그램에 따라 렌더링 작업을 서비스에 제출하기 위한 다양한 옵션이 있습니다.

### <a name="maya"></a>Maya

Maya를 사용하면 다음을 수행할 수 있습니다.

- [Maya용 Batch 플러그 인](https://docs.microsoft.com/azure/batch/batch-rendering-service#use-the-batch-plug-in-for-maya-to-submit-a-render-job)
- [BatchLabs](https://azure.github.io/BatchLabs) 데스크톱 응용 프로그램
- [Batch 템플릿 CLI](batch-cli-templates.md)

### <a name="3ds-max"></a>3DS Max

3DS Max를 사용하면 다음을 수행할 수 있습니다.

- [BatchLabs](https://azure.github.io/BatchLabs) 데스크톱 응용 프로그램(3ds Max BatchLabs 템플릿 사용에 대한 지침은 [BatchLabs 데이터](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) 참조)
- [Batch 템플릿 CLI](batch-cli-templates.md)

3DS Max Batch Labs 템플릿을 사용하면 Azure Batch Rendering 서비스를 통해 VRay와 Arnold 장면을 렌더링할 수 있습니다. VRay 및 Arnold를 위한 템플릿에는 두 가지 변형이 있습니다. 하나는 표준 장면을 위한 것이고, 다른 하나는 자산과 텍스처에 대한 3DS Max 경로 파일(.mxp 파일)이 필요한 더 복잡한 장면을 위한 것입니다. 3ds Max Batch Labs 템플릿에 대한 자세한 내용은 GitHub의 [BatchLabs 데이터](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) 리포지토리를 참조하세요.

또한 [Batch Python SDK](https://docs.microsoft.com/azure/batch/batch-python-tutorial)를 사용하여 렌더링 서비스를 기존 파이프라인과 통합할 수 있습니다.


## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Maya용 Batch 플러그 인을 사용하여 렌더링 작업 제출

Maya용 Batch 플러그 인을 사용하면 Maya에서 바로 Batch Rendering 서비스에 작업을 제출할 수 있습니다. 다음 섹션에서는 플러그 인에서 작업을 구성한 후 제출하는 방법을 설명합니다. 

### <a name="load-the-batch-plug-in-for-maya"></a>Maya용 Batch 플러그 인 로드

Batch 플러그 인은 [GitHub](https://github.com/Azure/azure-batch-maya/releases)에 제공됩니다. 원하는 디렉터리에 아카이브의 압축을 풉니다. *azure_batch_maya* 디렉터리에서 바로 플러그 인을 로드할 수 있습니다.

Maya에서 플러그 인을 로드하려면:

1. Maya를 실행합니다.
2. **Window** > **설정/기본 설정** > **플러그 인 관리자**를 엽니다.
3. **찾아보기**를 클릭합니다.
4. *azure_batch_maya/plug-in/AzureBatch.py*를 찾아서 선택합니다.

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>Batch 및 Storage 계정에 대한 액세스 인증

플러그 인을 사용하려면 Azure Batch 및 Azure Storage 계정 키를 사용하여 인증해야 합니다. 계정 키를 검색하려면:

1. Maya에 플러그 인을 표시하고 **Config** 탭을 선택합니다.
2. [Azure Portal](https://portal.azure.com)로 이동합니다.
3. 왼쪽 메뉴에서 **Batch 계정**을 선택합니다. 필요한 경우 **추가 서비스**를 클릭하고 _Batch_로 필터링합니다.
4. 목록에서 원하는 Batch 계정을 찾습니다.
5. **키** 메뉴 항목을 선택하여 계정 이름, 계정 URL 및 액세스 키를 표시합니다.
    - Batch 계정 URL을 Batch 플러그 인의 **서비스** 필드에 붙여넣습니다.
    - 계정 이름을 **Batch 계정** 필드에 붙여넣습니다.
    - 기본 계정 키를 **Batch 키** 필드에 붙여넣습니다.
7. 왼쪽 메뉴에서 Storage 계정을 선택합니다. 필요한 경우 **추가 서비스**를 클릭하고 _Storage_로 필터링합니다.
8. 목록에서 원하는 Storage 계정을 찾습니다.
9. **액세스 키** 메뉴 항목을 선택하여 저장소 계정 이름 및 키를 표시합니다.
    - Storage 계정 이름을 Batch 플러그 인의 **Storage 계정** 필드에 붙여넣습니다.
    - 기본 계정 키를 **Storage 키** 필드에 붙여넣습니다.
10. 플러그 인이 두 계정에 모두 액세스할 수 있도록 **인증**을 클릭합니다.

성공적으로 인증되면 플러그 인이 상태 필드를 **인증됨**으로 설정합니다. 

![Batch 및 Storage 계정 인증](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>렌더링 작업에 대한 풀 구성

Batch 및 Storage 계정을 인증한 후에는 렌더링 작업에 사용할 풀을 설정합니다. 플러그 인은 사용자가 선택한 항목을 세션 간에 저장합니다. 선호하는 구성을 설정해 두면 변경되지 않는 한 수정할 필요가 없습니다.

다음 섹션에서는 **전송** 탭에서 사용할 수 있는 옵션을 설명합니다.

#### <a name="specify-a-new-or-existing-pool"></a>새 풀 또는 기존 풀 지정

렌더링 작업을 실행할 풀을 지정하려면 **전송** 탭을 선택합니다. 이 탭은 풀을 만들거나 기존 풀을 선택하는 옵션을 제공합니다.

- **이 작업에 사용할 풀을 자동 프로비전**할 수 있습니다(기본 옵션). 이 옵션을 선택하면 Batch에서 현재 작업에 대한 배타적인 풀을 만들고, 렌더링 작업이 완료되면 자동으로 해당 풀을 삭제합니다. 이 옵션은 완료할 렌더링 작업이 하나인 경우에 이상적입니다.
- **기존의 영구 풀을 다시 사용할 수 있습니다**. 유휴 상태인 기존 풀이 있는 경우 드롭다운 목록에서 해당 풀을 선택하여 렌더링 작업을 실행할 풀로 지정할 수 있습니다. 기존의 영구 풀을 다시 사용하면 풀을 프로비전하는 데 필요한 시간이 단축됩니다.  
- **새 영구 풀을 만들 수 있습니다**. 이 옵션을 선택하면 작업 실행에 사용할 새 풀을 만듭니다. 작업이 완료되어도 풀을 삭제하지 않으므로 이후 작업에 다시 사용할 수 있습니다. 렌더링 작업을 지속적으로 실행해야 하는 경우 이 옵션을 선택합니다. 후속 작업에서 **기존 영구 풀 다시 사용**을 선택하여 첫 번째 작업에서 만든 영구 풀을 사용할 수 있습니다.

![풀, OS 이미지, VM 크기 및 라이선스 지정](./media/batch-rendering-service/submit.png)

Azure VM 비용 발생에 대한 자세한 내용은 [Linux 가격 책정 FAQ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq) 및 [Windows 가격 책정 FAQ](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq)를 참조하세요.

#### <a name="specify-the-os-image-to-provision"></a>프로비전할 OS 이미지 지정

**Env**(환경) 탭에서 풀의 계산 노드를 프로비전하는 데 사용할 OS 이미지 유형을 지정할 수 있습니다. 현재 Batch는 렌더링 작업에 다음 이미지 옵션을 지원합니다.

|운영 체제  |이미지  |
|---------|---------|
|Linux     |Batch CentOS |
|Windows     |Batch Windows |

#### <a name="choose-a-vm-size"></a>VM 크기 선택

**Env** 탭에서 VM 크기를 지정할 수 있습니다. 사용 가능한 VM 크기에 대한 자세한 내용은 [Azure에서 Linux VM 크기](../virtual-machines/linux/sizes.md) 및 [Azure에서 Windows VM 크기](../virtual-machines/windows/sizes.md)를 참조하세요. 

![Env 탭에서 VM OS 이미지 및 크기 지정](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>라이선스 옵션 지정

**Env** 탭에서 사용할 라이선스를 지정할 수 있습니다. 옵션은 다음과 같습니다.

- **Maya**, 기본적으로 활성화됩니다.
- **Arnold**, Maya에서 Arnold가 활성 렌더링 엔진으로 검색되는 경우에 활성화됩니다.

 사용자 고유의 라이선스를 사용하여 렌더링하려는 경우 테이블에 적절한 환경 변수를 추가하여 라이선스 끝점을 구성할 수 있습니다. 이렇게 하는 경우 기본 라이선스 옵션의 선택을 취소해야 합니다.

> [!IMPORTANT]
> 현재 VM이 렌더링에 사용되지 않더라도 VM이 풀에서 실행되는 동안 라이선스 사용 요금이 청구됩니다. 추가 요금을 방지하려면 **풀** 탭으로 이동하여 다른 렌더링 작업을 실행할 준비가 완료될 때까지 풀 크기를 0 노드로 조정합니다. 
>
>

#### <a name="manage-persistent-pools"></a>영구 풀 관리

**풀** 탭에서 기존 영구 풀을 관리할 수 있습니다. 목록에서 풀을 선택하면 풀의 현재 상태가 표시됩니다.

**풀** 탭에서 풀을 삭제하고 풀의 VM 수를 조정할 수도 있습니다. 워크로드 사이에 비용이 발생하지 않도록 풀 크기를 0 노드로 조정할 수 있습니다.

![풀 보기, 크기 조정 및 삭제](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>제출할 수 있도록 렌더링 작업 구성

렌더링 작업을 실행할 풀의 매개 변수를 지정한 후에는 작업 자체를 구성합니다. 

#### <a name="specify-scene-parameters"></a>장면 매개 변수 지정

Batch 플러그 인은 현재 Maya에서 사용하고 있는 렌더링 엔진을 감지하고, **전송** 탭에 적절한 렌더링 설정을 표시합니다. 이러한 설정에는 시작 프레임, 끝 프레임, 출력 접두사 및 프레임 단계가 포함됩니다. 플러그 인에서 다른 설정을 지정하여 장면 파일 렌더링 설정을 재정의할 수 있습니다. 플러그 인 설정 변경 사항은 장면 파일 렌더링 설정까지 지속되지 않으므로 장면 파일을 다시 업로드할 필요 없이 작업별로 변경할 수 있습니다.

Maya에서 선택한 렌더링 엔진이 지원되지 않으면 플러그 인에서 경고를 생성합니다.

플러그 인이 열려 있는 동안 새 장면을 로드하는 경우 **새로 고침** 단추를 클릭하여 설정이 업데이트되었는지 확인합니다.

#### <a name="resolve-asset-paths"></a>자산 경로 확인

플러그 인을 로드하면 플러그 인은 장면 파일에서 외부 파일 참조를 검색 합니다. 이러한 참조는 **자산** 탭에 표시됩니다. 참조 경로를 확인할 수 없으면 플러그 인은 다음을 포함한 몇 가지 기본 위치에서 파일을 찾으려고 시도합니다.

- 장면 파일의 위치 
- 현재 프로젝트의 _sourceimages_ 디렉터리
- 현재 작업 디렉터리. 

그래도 자산을 찾을 수 없으면 해당 자산에 경고 아이콘이 표시됩니다.

![누락된 자산이 경고 아이콘과 함께 표시됨](./media/batch-rendering-service/missing_assets.png)

확인되지 않은 파일 참조의 위치를 알고 있는 경우 경고 아이콘을 클릭하면 검색 경로를 추가하라는 메시지가 나타납니다. 그러면 플러그 인이 이 검색 경로를 사용하여 누락된 자산을 확인하려고 시도합니다. 추가 검색 경로를 원하는 만큼 추가할 수 있습니다.

참조가 확인되면 녹색 등 아이콘이 표시됩니다.

![확인된 자산에 녹색 등 아이콘이 표시됨](./media/batch-rendering-service/found_assets.png)

플러그 인에서 검색하지 못한 다른 파일이 장면에 필요한 경우 추가 파일 또는 디렉터리를 추가할 수 있습니다. **파일 추가** 및 **디렉터리 추가** 단추를 사용합니다. 플러그 인이 열려 있는 동안 새 장면을 로드하는 경우 **새로 고침** 단추를 클릭하여 장면의 참조를 업데이트합니다.

#### <a name="upload-assets-to-an-asset-project"></a>자산 프로젝트에 자산 업로드

렌더링 작업을 제출하면 **자산** 탭에 표시된 참조 파일이 자동으로 Azure Storage에 자산 프로젝트로 업로드됩니다. 렌더링 작업에 관계없이 **자산** 탭의 **업로드** 단추를 사용하여 자산 파일을 업로드할 수도 있습니다. 자산 프로젝트 이름은 **프로젝트** 필드에 지정되며 기본값은 현재 Maya 프로젝트의 이름입니다. 자산 파일이 업로드되면 로컬 파일 구조가 보존됩니다. 

자산이 업로드되면 임의의 수의 렌더링 작업에서 자산을 참조할 수 있습니다. 모든 업로드된 자산은 장면에 포함되는지 여부에 관계없이 자산 프로젝트를 참조하는 모든 작업에 사용할 수 있습니다. 다음 작업에서 참조하는 자산 프로젝트를 변경하려면 **자산** 탭의 **프로젝트** 필드에서 이름을 변경합니다. 업로드에서 제외하려는 참조 파일이 있는 경우 목록 옆에 있는 녹색 단추를 사용하여 해당 참조 파일의 선택을 취소합니다.

#### <a name="submit-and-monitor-the-render-job"></a>렌더링 작업 제출 및 모니터링

플러그 인에서 렌더링 작업을 구성한 후에는 **제출** 탭의 **작업 제출** 단추를 클릭하여 작업을 Batch로 제출합니다.

![렌더링 작업 제출](./media/batch-rendering-service/submit_job.png)

플러그 인의 **작업** 탭에서 진행 중인 작업을 모니터링할 수 있습니다. 목록에서 작업을 선택하면 작업의 현재 상태가 표시됩니다. 이 탭을 사용하여 작업을 취소 및 삭제하고 출력 및 렌더링 로그를 다운로드할 수 있습니다. 

출력을 다운로드하려면 **출력** 필드를 수정하여 원하는 대상 디렉터리를 설정합니다. 기어 아이콘을 클릭하면 프로세스가 진행되는 동안 작업을 감시하고 출력을 다운로드하는 백그라운드 프로세스가 시작됩니다. 

![작업 상태 확인 및 출력 다운로드](./media/batch-rendering-service/jobs.png)

다운로드 프로세스를 방해하지 않고 Maya를 닫을 수 있습니다.

## <a name="next-steps"></a>다음 단계

Batch에 대한 자세한 내용은 [Batch를 사용하여 본질적인 병렬 워크로드 실행](batch-technical-overview.md)을 참조하세요.
