---
title: Azure Migrate를 사용하여 검색 및 평가 크기 조정 | Microsoft Docs
description: Azure Migrate 서비스를 사용하여 많은 수의 온-프레미스 컴퓨터를 평가하는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/18/2018
ms.author: raynew
ms.openlocfilehash: c8943aec1c81abb34b646180df48bcc55764ca24
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365334"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>대규모 VMware 환경 검색 및 평가

이 문서에서는 [Azure Migrate](migrate-overview.md)를 사용하여 많은 수의 온-프레미스 가상 머신을 평가하는 방법을 설명합니다. Azure Migrate는 Azure로의 마이그레이션에 적합한지를 확인하기 위해 컴퓨터를 평가합니다. 서비스는 Azure에서 실행되는 컴퓨터의 크기 조정 및 비용 예상을 제공합니다.

## <a name="prerequisites"></a>필수 조건

- **VMware**: 마이그레이션하려는 VM은 vCenter Server 버전 5.5, 6.0 또는 6.5에서 관리되어야 합니다. 또한 수집기 VM을 배포하려면 5.0 이상을 실행하는 ESXi 호스트가 하나 필요합니다.
- **vCenter 계정**: vCenter Server에 액세스하려면 읽기 전용 계정이 필요합니다. Azure Migrate는 이 계정을 사용하여 온-프레미스 VM을 검색합니다.
- **사용 권한**: vCenter Server에서 .OVA 형식으로 파일을 가져와 VM을 만들려면 사용 권한이 필요합니다.
- **통계 구성**: vCenter Server에 대한 통계 설정은 배포를 시작하기 전에 수준 3으로 설정되어야 합니다. 수준이 3보다 낮은 경우 평가가 작동하지만 저장소 및 네트워크에 대한 성능 데이터는 수집되지 않습니다. 이 경우에 권장되는 크기는 CPU 및 메모리의 성능 데이터와 디스크 및 네트워크 어댑터의 구성 데이터를 기반으로 합니다.

## <a name="plan-azure-migrate-projects"></a>Azure Migrate 프로젝트 계획

다음과 같은 제한에 따라 검색 및 평가를 계획합니다.

| **엔터티** | **컴퓨터 제한** |
| ---------- | ----------------- |
| Project    | 1,500             |
| 검색  | 1,500             |
| 평가 | 1,500             |

<!--
- If you have fewer than 400 machines to discover and assess, you need a single project and a single discovery. Depending on your requirements, you can either assess all the machines in a single assessment or split the machines into multiple assessments.
- If you have 400 to 1,000 machines to discover, you need a single project with a single discovery. But you will need multiple assessments to assess these machines, because a single assessment can hold up to 400 machines.
- If you have 1,001 to 1,500 machines, you need a single project with two discoveries in it.
- If you have more than 1,500 machines, you need to create multiple projects, and perform multiple discoveries, according to your requirements. For example:
    - If you have 3,000 machines, you can set up two projects with two discoveries, or three projects with a single discovery.
    - If you have 5,000 machines, you can set up four projects: three with a discovery of 1,500 machines, and one with a discovery of 500 machines. Alternatively, you can set up five projects with a single discovery in each one.
      -->

## <a name="plan-multiple-discoveries"></a>여러 검색 계획

하나 이상의 프로젝트에 여러 검색 작업을 수행하기 위해 동일한 Azure Migrate Collector를 사용할 수 있습니다. 이러한 계획 고려 사항을 고려하세요.

- Azure Migrate Collector를 사용하여 검색할 때 vCenter Server 폴더, 데이터 센터, 클러스터 또는 호스트로 검색 범위를 설정할 수 있습니다.
- 둘 이상의 검색을 수행하려면 vCenter Server에서 검색할 VM이 1,500대 컴퓨터 제한을 지원하는 폴더, 데이터 센터, 클러스터 또는 호스트에 위치하는지 확인합니다.
- 평가 목적을 위해 동일한 프로젝트 및 평가 내에서 컴퓨터 간 상호 종속성을 유지하는 것이 좋습니다. vCenter Server에서 종속 컴퓨터가 평가를 위해 동일한 폴더, 데이터 센터 또는 클러스터에 있는지 확인합니다.


## <a name="create-a-project"></a>프로젝트 만들기

요구 사항에 따라 Azure Migrate 프로젝트를 만듭니다.

1. Azure Portal에서 **리소스 만들기**를 선택합니다.
2. **Azure Migrate**를 검색하고 검색 결과에서 서비스 **Azure Migrate(미리 보기)** 를 선택합니다. 그런 다음 **만들기**를 선택합니다.
3. 프로젝트에 대해 프로젝트 이름과 Azure 구독을 지정합니다.
4. 새 리소스 그룹을 만듭니다.
5. 프로젝트를 만들 위치를 지정하고 **만들기**를 선택합니다. 다른 대상 위치에서 VM을 평가할 수도 있습니다. 프로젝트에 대해 지정된 위치는 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데 사용됩니다.

## <a name="set-up-the-collector-appliance"></a>수집기 어플라이언스 설정

Azure Migrate는 수집기 어플라이언스라고 하는 온-프레미스 VM을 만듭니다. 이 VM은 온-프레미스 VMware VM을 검색하고 이에 대한 메타데이터를 Azure Migrate 서비스에 보냅니다. 수집기 어플라이언스를 설정하려면 OVA 파일을 다운로드하고 온-프레미스 vCenter Server로 가져옵니다.

### <a name="download-the-collector-appliance"></a>수집기 어플라이언스를 다운로드 합니다.

프로젝트가 여러 개인 경우 수집기 어플라이언스는 vCenter Server에 한 번만 다운로드해야 합니다. 어플라이언스를 다운로드하고 설정한 후에 각 프로젝트에 대해 실행하고 고유한 프로젝트 ID와 키를 지정합니다.

1. Azure Migrate 프로젝트에서 **시작** > **검색 및 평가** > **컴퓨터 검색**을 선택합니다.
2. **컴퓨터 검색**에서 **다운로드**를 선택하여 OVA 파일을 다운로드합니다.
3. **프로젝트 자격 증명 복사**에서 프로젝트에 대한 ID 및 키를 복사합니다. 수집기를 구성하는 경우 필요합니다.


### <a name="verify-the-collector-appliance"></a>수집기 어플라이언스를 확인합니다.

배포하기 전에 OVA 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.

2. 다음 명령을 실행하여 OVA에 대한 해시를 생성합니다.

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. 생성된 해시가 다음 설정과 일치하는지 확인합니다.

    OVA 버전 1.0.9.8의 경우

    **알고리즘** | **해시 값**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

    OVA 버전 1.0.9.7의 경우

    **알고리즘** | **해시 값**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

    OVA 버전 1.0.9.5의 경우

    **알고리즘** | **해시 값**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    OVA 버전 1.0.9.2의 경우

    **알고리즘** | **해시 값**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

## <a name="create-the-collector-vm"></a>수집기 VM을 만듭니다.

다운로드한 파일을 vCenter Server에 가져옵니다.

1. vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포**를 클릭합니다.

    ![OVF 배포](./media/how-to-scale-assessment/vcenter-wizard.png)

2. OVF 템플릿 배포 마법사 > **원본**에서 OVA 파일의 위치를 지정합니다.
3. **이름** 및 **위치**에서 수집기 VM에 대한 익숙한 이름과 VM이 호스트될 인벤토리 개체를 지정합니다.
4. **호스트/클러스터**에서 수집기 VM이 실행될 호스트 또는 클러스터를 지정합니다.
5. 저장소에서 수집기 VM에 대한 저장소 대상을 지정합니다.
6. **디스크 형식**에서 디스크 유형 및 크기를 지정합니다.
7. **네트워크 매핑**에서 수집기 VM이 연결할 네트워크를 지정합니다. 메타데이터를 Azure로 전송하려면 네트워크에 인터넷 연결이 필요합니다.
8. 설정을 검토하고 확인한 다음 **마침**을 선택합니다.

## <a name="identify-the-id-and-key-for-each-project"></a>각 프로젝트에 대한 ID 및 키 식별

프로젝트가 여러 개인 경우 각 프로젝트에 대한 ID 및 키를 식별했는지 확인합니다. VM을 검색하기 위해 수집기를 실행하는 경우 키가 필요합니다.

1. 프로젝트에서 **시작** > **검색 및 평가** > **컴퓨터 검색**을 선택합니다.
2. **프로젝트 자격 증명 복사**에서 프로젝트에 대한 ID 및 키를 복사합니다.
    ![프로젝트 자격 증명 복사](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>vCenter 통계 수준 설정
검색하는 동안 수집되는 성능 카운터 목록은 다음과 같습니다. 이러한 카운터는 기본적으로 vCenter Server의 다양한 수준에서 사용할 수 있습니다.

모든 카운터는 올바르게 수집되도록 통계 수준에 대한 가장 높은 일반적인 수준(3)을 설정하는 것이 좋습니다. vCenter가 낮은 수준으로 설정된 경우 몇 가지 카운터만 완벽히 수집되고 나머지는 0으로 설정됩니다. 그러면 평가는 불완전한 데이터를 표시합니다.

다음 표에 특정 카운터가 수집되지 않은 경우 영향을 받는 평가 결과가 나열되어 있습니다.

| 카운터                                 | Level | 장치 단위 수준 | 영향 평가                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| cpu.usage.average                       | 1     | 해당 없음               | 권장되는 VM 크기 및 비용         |
| mem.usage.average                       | 1     | 해당 없음               | 권장되는 VM 크기 및 비용         |
| virtualDisk.read.average                | 2     | 2                | 디스크 크기, 저장소 비용 및 VM 크기 |
| virtualDisk.write.average               | 2     | 2                | 디스크 크기, 저장소 비용 및 VM 크기 |
| virtualDisk.numberReadAveraged.average  | 1     | 3                | 디스크 크기, 저장소 비용 및 VM 크기 |
| virtualDisk.numberWriteAveraged.average | 1     | 3                | 디스크 크기, 저장소 비용 및 VM 크기 |
| net.received.average                    | 2     | 3                | VM 크기 및 네트워크 비용             |
| net.transmitted.average                 | 2     | 3                | VM 크기 및 네트워크 비용             |

> [!WARNING]
> 방금 더 높은 통계 수준을 설정한 경우 성능 카운터를 생성하는 데 하루가 걸립니다. 따라서 하루 뒤에 검색을 실행하는 것이 좋습니다.

## <a name="run-the-collector-to-discover-vms"></a>VM을 검색하려면 수집기를 실행합니다.

수행해야 하는 각 검색마다 수집기를 실행하여 필요한 범위에서 VM을 검색합니다. 검색을 차례로 실행합니다. 동시 검색은 지원되지 않으며, 각 검색은 다른 범위에 있어야 합니다.

1.  vSphere 클라이언트 콘솔에서 VM을 마우스 오른쪽 단추로 클릭하고 **콘솔 열기**를 클릭합니다.
2.  어플라이언스에 대한 언어, 표준 시간대 및 기본 암호를 제공합니다.
3.  바탕 화면에서 **수집기 실행** 바로 가기를 선택합니다.
4.  Azure Migrate Collector에서 **필수 조건 설정**을 열고 다음을 수행합니다.

    a. 사용 조건에 동의하고 타사 정보를 읽습니다.

    수집기는 VM이 인터넷에 액세스를 수 있는지 확인합니다.

    나. VM이 프록시를 통해 인터넷에 액세스하는 경우 **프록시 설정**을 선택하고, 프록시 주소 및 수신 대기 포트를 지정합니다. 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.

    수집기는 collectorservice가 실행 중인지 확인합니다. 서비스는 수집기 VM에 기본적으로 설치됩니다.

    다. VMware PowerCLI를 다운로드하여 설치합니다.

5.  **vCenter Server 세부 정보 지정**에서 다음을 수행합니다.
    - vCenter Server의 이름(FQDN) 또는 IP 주소를 지정합니다.
    - **사용자 이름** 및 **암호**에서, 수집기가 vCenter Server에서 VM을 검색하기 위해 사용할 읽기 전용 계정 자격 증명을 지정합니다.
    - **범위 선택**에서 VM 검색에 대한 범위를 선택합니다. 수집기는 지정된 범위 내의 VM만 검색할 수 있습니다. 범위를 특정 폴더, 데이터 센터 또는 클러스터로 설정할 수 있습니다. VM은 1,000대 미만이어야 합니다.

6.  **마이그레이션 프로젝트 지정**에서 프로젝트에 대한 ID 및 키를 지정합니다. 해당 항목을 복사하지 않은 경우 수집기 VM에서 Azure Portal을 엽니다. 프로젝트의 **개요** 페이지에서 **컴퓨터 검색**을 선택하고 값을 복사합니다.  
7.  **컬렉션 진행률 보기**에서 검색 프로세스를 모니터링하고 VM에서 수집한 메타데이터가 범위 내에 있는지 확인합니다. 수집기는 대략적인 검색 시간을 제공합니다.


### <a name="verify-vms-in-the-portal"></a>포털에서 VM 확인

검색 시간은 검색 중인 VM이 얼마나 많은지에 달려 있습니다. 일반적으로 100대의 VM인 경우 수집기가 실행을 완료한 후에 검색을 완료하기까지 약 1시간이 걸립니다.

1. Migration Planner 프로젝트에서 **관리** > **컴퓨터**를 선택합니다.
2. 검색하려는 VM이 포털에 나타나는지 확인합니다.


## <a name="next-steps"></a>다음 단계

- 평가를 위해 [그룹 만들기](how-to-create-a-group.md) 방법을 알아봅니다.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
