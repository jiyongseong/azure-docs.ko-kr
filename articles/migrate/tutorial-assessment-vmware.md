---
title: Azure Migrate를 사용하여 Azure로의 마이그레이션에 대한 온-프레미스 VMware VM 검색 및 평가 | Microsoft Docs
description: Azure Migrate 서비스를 사용하여 Azure로의 마이그레이션에 대한 온-프레미스 VMware VM을 검색하고 평가하는 방법에 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 695298be6cb9f56de26b8682c556285aba22d4a6
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34272066"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Azure로의 마이그레이션에 대한 온-프레미스 VMware VM 검색 및 평가

[Azure Migrate](migrate-overview.md) 서비스는 Azure로의 마이그레이션에 대한 온-프레미스 워크로드를 평가합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Migrate에서 온-프레미스 VM을 검색하는 데 사용하는 계정 만들기
> * Azure Migrate 프로젝트를 만듭니다.
> * 평가를 위해 온-프레미스 VMware VM을 검색하도록 온-프레미스 수집기 가상 머신(VM)을 설정합니다.
> * VM을 그룹화하고 평가를 만듭니다.


Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prerequisites"></a>필수 조건

- **VMware**: 마이그레이션하려는 VM은 버전 5.5, 6.0 또는 6.5를 실행하는 vCenter Server에서 관리해야 합니다. 또한 수집기 VM을 배포하려면 5.0 이상을 실행하는 ESXi 호스트가 하나 필요합니다.

> [!NOTE]
> Hyper-V에 대한 지원은 준비 중이며 곧 사용할 수 있습니다.

- **vCenter Server 계정**: vCenter Server에 액세스하려면 읽기 전용 계정이 필요합니다. Azure Migrate는 이 계정을 사용하여 온-프레미스 VM을 검색합니다.
- **사용 권한**: vCenter Server에서 .OVA 형식으로 파일을 가져와 VM을 만들려면 사용 권한이 필요합니다.
- **통계 구성**: vCenter Server에 대한 통계 설정은 배포를 시작하기 전에 수준 3으로 설정되어야 합니다. 수준 3 평가보다 낮게 작동한다면, 저장소 및 네트워크에 대한 성능 데이터는 수집되지 않습니다. 이 경우 권장되는 크기는 CPU 및 메모리의 성능 데이터와 디스크 및 네트워크 어댑터의 구성 데이터를 기반으로 수행됩니다.

## <a name="create-an-account-for-vm-discovery"></a>VM 검색을 위한 계정 만들기

Azure Migrate에서 평가를 위해 VM을 자동으로 검색하려면 VMware 서버에 대한 액세스가 필요합니다. 다음 속성으로 VMware 계정을 만듭니다. 이 계정은 Azure Migrate를 설치하는 동안 지정합니다.

- 사용자 유형: 읽기 전용 사용자(최소)
- 권한: 데이터 센터 개체 –> 자식 개체에 전파, role=Read-only
- 세부 정보: 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.
- 액세스를 제한하려는 경우 자식에 전파 개체를 사용하여 액세스 권한 없음 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com) 에 로그인합니다.

## <a name="create-a-project"></a>프로젝트 만들기

1. Azure Portal에서 **리소스 만들기**를 클릭합니다.
2. **Azure Migrate**를 검색하고 검색 결과에서 서비스 **Azure Migrate**을 선택합니다. 그런 다음 **Create**를 클릭합니다.
3. 프로젝트에 대해 프로젝트 이름과 Azure 구독을 지정합니다.
4. 새 리소스 그룹을 만듭니다.
5. 프로젝트를 만들 위치를 지정한 다음 **만들기**를 클릭합니다. Azure Migrate 프로젝트는 미국 중서부 또는 미국 동부 지역에서만 만들 수 있습니다. 그러나 대상 Azure 위치에 대한 마이그레이션을 계속 계획할 수 있습니다. 프로젝트에 대해 지정된 위치는 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데 사용됩니다.

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)



## <a name="download-the-collector-appliance"></a>수집기 어플라이언스를 다운로드 합니다.

Azure Migrate는 수집기 어플라이언스로 알려진 온-프레미스 VM을 만듭니다. 이 VM은 온-프레미스 VMware VM을 검색하고 그것들에 대한 메타데이터를 Azure Migrate 서비스에 보냅니다. 수집기 어플라이언스를 설정하려면 OVA 파일을 다운로드하여 VM을 만들기 위해 온-프레미스 vCenter 서버로 가져옵니다.

1. Azure Migrate 프로젝트에서 **시작** > **검색 및 평가** > **컴퓨터 검색**을 클릭합니다.
2. **컴퓨터 검색**에서 **다운로드**를 클릭하여 OVA 파일을 다운로드합니다.
3. **프로젝트 자격 증명 복사**에서 프로젝트 ID 및 키를 복사합니다. 수집기를 구성할 때 이러한 항목들이 필요합니다.

    ![.ova 파일 다운로드](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>수집기 어플라이언스를 확인합니다.

배포하기 전에 OVA 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 OVA에 대한 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 생성된 해시는 이러한 설정에 일치해야 합니다.

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

    OVA 버전 1.0.8.59의 경우

    **알고리즘** | **해시 값**
    --- | ---
    MD5 | 71139e24a532ca67669260b3062c3dad
    SHA1 | 1bdf0666b3c9c9a97a07255743d7c4a2f06d665e
    SHA256 | 6b886d23b24c543f8fc92ff8426cd782a77efb37750afac397591bda1eab8656  

    OVA 버전 1.0.8.49의 경우
    **알고리즘** | **해시 값**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    OVA 버전 1.0.8.40의 경우:

    **알고리즘** | **해시 값**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>수집기 VM을 만듭니다.

다운로드한 파일을 vCenter Server에 가져옵니다.

1. vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포**를 클릭합니다.

    ![OVF 배포](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. OVF 템플릿 배포 마법사 > **원본**에서 .ova 파일의 위치를 지정합니다.
3. **이름** 및 **위치**에서 수집기 VM에 대한 익숙한 이름과 VM이 호스트될 인벤토리 개체를 지정합니다.
5. **호스트/클러스터**에서 수집기 VM이 실행될 호스트 또는 클러스터를 지정합니다.
7. 저장소에서 수집기 VM에 대한 저장소 대상을 지정합니다.
8. **디스크 형식**에서 디스크 유형 및 크기를 지정합니다.
9. **네트워크 매핑**에서 수집기 VM이 연결할 네트워크를 지정합니다. 메타데이터를 Azure로 전송하려면 네트워크에 인터넷 연결이 필요합니다.
10. 설정을 검토 및 확인한 다음 **마침**을 클릭합니다.

## <a name="run-the-collector-to-discover-vms"></a>VM을 검색하려면 수집기를 실행합니다.

1. vSphere Client 콘솔에서 VM > **Open Console**을 마우스 오른쪽 단추로 클릭합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 기본 암호를 제공합니다.
3. 바탕 화면에서 **수집기 실행** 바로 가기를 클릭합니다.
4. Azure Migrate Collector에서 **필수 조건 설정**을 엽니다.
    - 사용 조건에 동의하고 타사 정보를 읽습니다.
    - 수집기는 VM이 인터넷에 액세스를 수 있는지 확인합니다.
    - VM이 프록시를 통해 인터넷에 액세스하는 경우 **프록시 설정**을 클릭하고, 프록시 주소 및 수신 대기 포트를 지정합니다. 프록시에 인증이 필요한 경우 자격 증명을 지정합니다. 인터넷 연결 요구 사항 및 수집기가 액세스하는 URL 목록에 대해 [자세히 알아보세요](https://docs.microsoft.com/en-us/azure/migrate/concepts-collector#internet-connectivity).

    > [!NOTE]
    > 프록시 주소는 http://ProxyIPAddress 또는 http://ProxyFQDN 형식으로 입력해야 합니다. HTTP 프록시만 지원됩니다.

    - 수집기는 collectorservice가 실행 중인지 확인합니다. 서비스는 수집기 VM에 기본적으로 설치됩니다.
    - VMware PowerCLI를 다운로드하여 설치합니다.

5. **vCenter Server 세부 정보 지정**에서 다음을 수행합니다.
    - vCenter 서버의 이름(FQDN) 또는 IP 주소를 지정합니다.
    - **사용자 이름** 및 **암호**에서, 수집기가 vCenter 서버에서 VM을 검색하기 위해 사용할 읽기 전용 계정 자격 증명을 지정합니다.
    - **컬렉션 범위**에서 VM 검색에 대한 범위를 선택합니다. 수집기는 지정된 범위 내의 VM만 검색할 수 있습니다. 범위를 특정 폴더, 데이터 센터 또는 클러스터로 설정할 수 있습니다. VM은 1500대 미만이어야 합니다. 더 큰 환경을 검색하는 방법을 [자세히 알아보세요](how-to-scale-assessment.md).

6. **마이그레이션 프로젝트 지정**에서 Azure Migrate 프로젝트 ID를 지정하고 포털에서 복사한 키를 지정합니다. 해당 항목을 복사하지 않은 경우 수집기 VM에서 Azure Portal을 엽니다. 프로젝트 **개요** 페이지에서 **컴퓨터 검색**을 클릭하고 값을 복사합니다.  
7. **컬렉션 진행률 보기**에서 검색을 모니터링하고 VM에서 수집한 메타데이터가 범위 내에 있는지 확인합니다. 수집기는 대략적인 검색 시간을 제공합니다. Azure Migrate Collector가 어떤 데이터를 수집하는지 [자세히 알아보세요](https://docs.microsoft.com/en-us/azure/migrate/concepts-collector#what-data-is-collected).

> [!NOTE]
> 수집기는 "영어(미국)"만을 운영 체제 언어와 수집기 인터페이스 언어로 지원합니다. 더 많은 언어에 대한 지원이 곧 제공될 예정입니다.


### <a name="verify-vms-in-the-portal"></a>포털에서 VM을 확인합니다.

검색 시간은 검색 중인 VM이 얼마나 많은지에 달려 있습니다. 일반적으로 VM 100대의 경우, 수집기가 실행을 완료한 후 검색을 완료하기까지 약 1시간이 걸립니다.

1. Migration Planner 프로젝트에서 **관리** > **컴퓨터**를 클릭합니다.
2. 검색하려는 VM이 포털에 나타나는지 확인합니다.


## <a name="create-and-view-an-assessment"></a>평가 만들기 및 보기

VM을 검색한 후 그룹화하여 평가를 만듭니다.

1. 프로젝트 **개요** 페이지에서 **+평가 만들기**를 클릭합니다.
2. 평가 속성을 검토하려면 **모두 보기**를 클릭합니다.
3. 그룹을 만들고 그룹 이름을 지정합니다.
4. 그룹에 추가하려는 컴퓨터를 선택합니다.
5. **평가 만들기**를 클릭하고, 그룹 및 평가를 만듭니다.
6. 평가를 만든 후 **개요** > **대시보드**에서 봅니다.
7. **평가 내보내기**를 클릭하고, Excel 파일로 다운로드합니다.

### <a name="assessment-details"></a>평가 세부 정보

평가에는 온-프레미스 VM의 Azure 호환성, Azure에서 VM을 실행하기에 적합한 크기, 예상 월별 Azure 비용에 대한 정보가 포함됩니다.

![평가 보고서](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure 준비 상태

평가의 Azure 준비 상태 보기는 각 VM의 준비 상태를 보여줍니다. VM의 속성에 따라 각 VM이 다음과 같이 표시됩니다.
- Azure 준비 완료
- 조건부 Azure 준비 완료
- Azure를 사용할 준비 안 됨
- 준비 상태 알 수 없음

준비된 VM의 경우 Azure Migrate는 Azure에서의 VM 크기를 권장합니다. Azure Migrate에서 권장하는 크기는 평가 속성에 지정된 크기 조정 기준에 따라 달라집니다. 크기 조정 기준이 성능 기반이면 VM의 성능 기록을 고려하여 권장 크기가 결정됩니다. 크기 조정 기준이 '온-프레미스로'이면 Azure의 VM 권장 크기는 VM 온-프레미스 크기(있는 그대로 크기 조정)를 살펴보고 결정됩니다. VM의 CPU 및 메모리 사용률 데이터는 VM 크기 조정 시 고려 사항이 아닙니다. 그러나 디스크는 온-프레미스 크기 조정 시 성능 데이터를 살펴보고 크기가 결정됩니다.  Azure Migrate의 크기 조정 방식에 대해 [자세히 알아보세요](concepts-assessment-calculation.md).

VM 상태가 Azure 준비 완료 또는 조건부 Azure 준비 완료인 경우 Azure Migrate는 준비 상태 문제를 설명하고 수정 단계를 제공합니다.

Azure Migrate가 Azure 준비 상태를 알 수 없는(데이터를 사용할 수 없어서) VM은 준비 상태 알 수 없음으로 표시됩니다.

Azure 준비 상태 및 크기 조정 외에도, Azure Migrate는 VM 마이그레이션에 사용할 수 있는 도구를 추천합니다. 여기에는 온-프레미스 환경에서 심층 검색이 필요합니다. 온-프레미스 컴퓨터에 에이전트를 설치하여 심층 검색을 수행하는 방법을 [알아보세요](how-to-get-migration-tool.md). 온-프레미스 컴퓨터에 에이전트가 설치되어 있지 않으면 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용한 리프트 앤 시프트 마이그레이션이 좋습니다. 온-프레미스 컴퓨터에 에이전트가 설치되어 있지 않은 경우 Azure Migrate은 컴퓨터 내부에서 실행 중인 프로세스를 확인하고 컴퓨터가 데이터베이스 컴퓨터인지 여부를 식별합니다. 컴퓨터가 데이터베이스 컴퓨터이면 마이그레이션 도구로 [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)가 좋으며, 그렇지 않으면 Azure Site Recovery가 좋습니다.

  ![평가 준비 상태](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>월별 예상 비용

이 보기는 각 컴퓨터의 세부 사항과 함께 Azure에서 VM을 실행하는 데 필요한 총 계산 및 저장소 비용을 보여 줍니다. 예상 비용은 Azure Migrate에서 컴퓨터, 컴퓨터의 디스크 및 평가 속성에 대해 수행한 크기 권장 사항을 고려하여 계산됩니다.

> [!NOTE]
> Azure Migrate가 제공하는 비용 예측은 온-프레미스 VM을 Azure IaaS(Infrastructure as a service) VM으로 실행하기 위한 것입니다. Azure Migrate는 PaaS(Platform as a Service) 또는 SaaS(Software as a Service) 비용을 고려하지 않습니다.

계산 및 저장소에 대한 월별 예상 비용은 그룹의 모든 VM에 대해 집계됩니다.

![VM 비용 평가](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>신뢰 등급

Azure Migrate의 각 평가는 별 1개~5개 사이의 신뢰 등급에 연결됩니다(별 1개가 가장 낮고 5개가 가장 높음). 신뢰 등급은 평가 계산에 필요한 데이터 요소의 가용성에 따라 평가에 할당됩니다. 평가의 신뢰 등급은 Azure Migrate에서 제공하는 권장 크기의 신뢰성을 추정하는 데 도움이 됩니다.

VM의 성능 기반 크기 조정의 경우 Azure Migrate에 CPU 및 메모리 사용률 데이터가 필요합니다. 또한, VM에 연결된 각 디스크의 크기 조정 시 읽기/쓰기 IOPS 및 처리량이 필요합니다. 마찬가지로, VM에 연결된 각 네트워크 어댑터에 대해 성능 기반 크기 조정을 수행하려면 Azure Migrate에 네트워크 입/출력이 필요합니다. 위의 사용률 데이터를 vCenter Server에서 사용할 수 없는 경우 Azure Migrate가 권장하는 크기의 신뢰성이 떨어질 수 있습니다. 사용 가능한 데이터 요소의 백분율에 따라 아래와 같이 평가의 신뢰 등급이 제공됩니다.

   **데이터 요소 가용성** | **신뢰 등급**
   --- | ---
   0%-20% | 별 1개
   21%-40% | 별 2개
   41%-60% | 별 3개
   61%-80% | 별 4개
   81%-100% | 별 5개

다음과 같은 이유 중 하나로 인해 평가에 일부 데이터 요소가 없을 수도 있습니다.
- vCenter Server의 통계 설정이 수준 3으로 설정되지 않았습니다. vCenter Server의 통계 설정이 수준 3보다 낮으면 vCenter Server에서 디스크 및 네트워크의 성능 데이터가 수집되지 않습니다. 이 경우 디스크 및 네트워크에 대해 Azure Migrate에서 제공하는 권장 사항은 사용률 기반이 아닙니다. 디스크의 IOPS/처리량을 고려하지 않으면 Azure Migrate는 디스크에 Azure의 프리미엄 디스크가 필요한지 확인할 수 없고, 따라서 이 경우 Azure Migrate는 모든 디스크에 표준 디스크를 권장합니다.
- vCenter Server의 통계 설정이 검색을 시작하기 전까지 짧은 시간 동안 수준 3으로 설정되었습니다. 예를 들어 오늘은 통계 설정 수준을 3으로 변경하고 내일(24시간 후) 수집기 어플라이언스를 사용하여 검색을 시작하는 시나리오를 생각해보겠습니다. 하루에 대한 평가를 생성하는 경우 모든 데이터 요소가 있으며 평가의 신뢰도는 5등급이 됩니다. 하지만 평가 속성의 성능 기간을 1달로 변경하면 마지막 1달의 디스크 및 네트워크 성능 데이터를 사용할 수 없으므로 신뢰 등급이 하락합니다. 마지막 1달의 성능 데이터를 고려하고 싶으면, 검색을 시작하기 전 한 달 동안은 vCenter Server 설정을 수준 3으로 유지하는 것이 좋습니다.
- 평가 계산 기간에 일부 VM이 종료되었습니다. 평가 기간 중 일부 VM이 꺼지면 vCenter Server는 해당 기간의 성능 데이터를 얻지 못합니다.
- 평가 계산 기간에 일부 VM이 생성되었습니다. 예를 들어 마지막 1달의 성능 기록에 대한 평가를 만들려고 하는데, 일부 VM이 불과 일주일 전에 환경에서 생성되었습니다. 이 경우 전체 기간에 대한 새 VM의 성능 기록이 제공되지 않습니다.

> [!NOTE]
> 평가의 신뢰 등급이 별 4개 미만이면 vCenter Server 통계 설정 수준을 3으로 변경하고, 평가를 고려하는 기간(1일/1주/1개월) 동안 기다린 후 검색 및 평가를 수행합니다. 위의 단계를 수행할 수 없는 경우 성능 기반 크기 조정의 신뢰성이 떨어질 수 있으므로 평가 속성을 변경하여 *온-프레미스로 크기 조정*으로 전환하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- 요구 사항에 따라 평가를 사용자 지정하는 방법을 [알아보세요](how-to-modify-assessment.md).
- [컴퓨터 종속성 매핑](how-to-create-group-machine-dependencies.md)을 사용하여 정확도 높은 평가 그룹을 만드는 방법을 알아봅니다.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
- 대규모 VMware 환경을 검색하고 평가하는 방법을 [알아봅니다](how-to-scale-assessment.md).
