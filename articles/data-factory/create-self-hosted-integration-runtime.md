---
title: Azure Data Factory에서 자체 호스팅 통합 런타임 만들기 | Microsoft Docs
description: 데이터 팩터리가 개인 네트워크의 데이터 저장소에 액세스할 수 있게, Azure Data Factory에서 자체 호스팅 통합 런타임을 만드는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: b18fa2879056600cfc9c6a85afc599e2695af711
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임을 만들고 구성하는 방법
IR(Integration Runtime)은 서로 다른 네트워크 환경에서 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 계산 인프라입니다. IR에 대한 세부 정보는 [Integration Runtime 개요](concepts-integration-runtime.md)를 참조하세요.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

자체 호스팅 통합 런타임은 클라우드 데이터 저장소와 개인 네트워크의 데이터 저장소 간에 복사 작업을 실행하고, 온-프레미스 또는 Azure Virtual Network에서 계산 리소스에 대해 변환 작업을 디스패치할 수 있습니다. 자체 호스팅 통합 런타임을 설치하려면 온-프레미스 컴퓨터나, 개인 네트워크 내부의 가상 컴퓨터가 필요합니다.  

이 문서에서는 자체 호스팅 IR을 만들고 구성하는 방법을 소개합니다.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>자체 호스팅 IR을 설치하는 대략적인 단계
1.  자체 호스팅 통합 런타임을 만듭니다. 다음은 PowerShell 예제입니다.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  자체 호스팅 통합 런타임을 다운로드하여 설치합니다(로컬 컴퓨터).
3.  인증 키를 검색하고 자체 호스팅 통합 런타임을 키로 등록합니다. 다음은 PowerShell 예제입니다.

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>명령 흐름 및 데이터 흐름
온-프레미스와 클라우드 간에 데이터를 복사할 때 이 작업은 자체 호스팅 통합 런타임을 사용하여 온-프레미스 데이터 소스와 클라우드 간에 데이터를 전송합니다.

다음은 자체 호스팅 IR을 통한 복사 절차를 요약하는 대략적인 데이터 흐름입니다.

![대략적인 개요](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. 데이터 개발자가 PowerShell cmdlet을 사용하여 Azure Data Factory 내에서 자체 호스팅 통합 런타임을 만듭니다. 현재 Azure Portal은 이 기능을 지원하지 않습니다.
2. 데이터 개발자가 데이터 저장소에 연결하는 데 사용할 자체 호스팅 통합 런타임 인스턴스를 지정하여 온-프레미스 데이터 저장소에 대한 연결된 서비스를 만듭니다. 연결된 서비스 설정 중에 데이터 개발자는 ‘Credential Manager’ 응용 프로그램(현재 지원 안 함)을 사용하여 인증 유형 및 자격 증명을 설정합니다. Credential Manager 응용 프로그램 대화 상자는 데이터 저장소와 통신하여 연결을 테스트하고 자체 호스팅 통합 런타임과 통신하여 자격 증명을 저장합니다.
4.  자체 호스팅 통합 런타임 노드는 Windows 데이터 보호 API(DPAPI)를 사용하여 자격 증명을 암호화하고 로컬로 저장합니다. 고가용성을 위해 여러 노드가 설정된 경우 자격 증명이 다른 노드 간에 동기화됩니다. 각 노드는 DPAPI를 사용하여 이를 암호화하고 로컬에 저장합니다. 자격 증명 동기화는 데이터 개발자에게 투명하고 자체 호스팅 IR에서 처리됩니다.    
5.  데이터 팩터리 서비스는 작업의 예약 및 관리를 위해, 공유 Azure Service Bus 큐를 사용하는 **컨트롤 채널**을 통해 자체 호스팅 통합 런타임과 통신합니다. 작업을 실행해야 할 때 데이터 팩터리가 자격 증명 정보와 함께 요청을 큐에 지정합니다(자격 증명이 아직 자체 호스팅 통합 런타임에 저장되지 않은 경우). 큐 폴링 후 자체 호스팅 통합 런타임에서 작업을 시작합니다.
6.  자체 호스팅 통합 런타임은 데이터 파이프라인에서 복사 작업을 구성하는 방법에 따라 온-프레미스 저장소와 클라우드 저장소 간에 데이터를 복사합니다. 이 단계에서 자체 호스팅 통합 런타임은 보안(HTTPS) 채널을 통해 Azure Blob Storage 등의 클라우드 기반 저장소 서비스와 직접 통신합니다.

## <a name="considerations-for-using-self-hosted-ir"></a>자체 호스팅 IR 사용을 위한 고려 사항

- 단일 자체 호스팅 통합 런타임을 여러 온-프레미스 데이터 원본에 사용할 수 있습니다. 그러나 **단일 자체 호스팅 통합 런타임은 Azure Data Factory 하나에만 연결**되며, 다른 데이터 팩터리와 공유할 수는 없습니다.
- 단일 컴퓨터에 **자체 호스팅 통합 런타임 인스턴스를 하나만** 설치할 수 있습니다. 온-프레미스 데이터 소스에 액세스해야 하는 데이터 팩터리가 두 개라면 두 온-프레미스 컴퓨터에 자체 호스팅 통합 런타임을 설치해야 합니다. 즉, 자체 호스팅 통합 런타임을 특정 데이터 팩터리에 연결합니다.
- **자체 호스팅 통합 런타임이 데이터 원본과 같은 컴퓨터에 있을 필요는 없습니다**. 그러나 자체 호스팅 통합 런타임이 데이터 원본에 가까울수록 데이터 원본에 연결하는 시간이 줄어듭니다. 온-프레미스 데이터 원본을 호스트하는 컴퓨터와는 다른 컴퓨터에 자체 호스팅 통합 런타임을 설치하는 것이 좋습니다. 자체 호스팅 통합 런타임과 데이터 원본이 서로 다른 컴퓨터에 있을 때 자체 호스팅 통합 런타임은 데이터 원본과 리소스를 경합하지 않습니다.
- **서로 다른 컴퓨터의 여러 자체 호스팅 통합 런타임이 동일한 온-프레미스 데이터 원본에 연결**할 수 있습니다. 예를 들어 두 자체 호스팅 통합 런타임이 두 데이터 팩터리를 처리하지만 동일한 온-프레미스 데이터 원본이 두 데이터 팩터리에 등록되어 있습니다.
- 컴퓨터에 **Power BI** 시나리오를 처리할 게이트웨이가 이미 설치된 경우 **별도의 Azure Data Factory용 자체 호스팅 통합 런타임**을 다른 컴퓨터에 설치합니다.
- 자체 호스팅 통합 런타임은 Azure Virtual Network 내에서 데이터 통합을 지원하는 데 사용되어야 합니다.
- **ExpressRoute**를 사용하더라도 데이터 소스는 방화벽으로 보호되는 온-프레미스 데이터 소스로 취급해야 합니다. 자체 호스팅 통합 런타임을 사용하여 서비스와 데이터 원본 간의 연결을 설정합니다.
- 클라우드의 데이터 저장소가 **Azure IaaS 가상 머신**에 있더라도 자체 호스팅 통합 런타임을 사용해야 합니다.
- FIPS 호환 암호화가 설정된 Windows 서버에 설치된 자체 호스팅 통합 런타임에서는 태스크가 실패할 수 있습니다. 이 문제를 해결하려면 서버에서 FIPS 호환 암호화를 사용하지 않도록 설정합니다. FIPS 호환 암호화를 사용하지 않도록 설정하려면 다음 레지스트리 값을 1(사용)에서 0(사용 안 함)으로 변경합니다. `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`

## <a name="prerequisites"></a>필수 조건

- 지원되는 **운영 체제** 버전은 Windows 7 서비스 팩 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016입니다. 자체 호스팅 통합 런타임을 **도메인 컨트롤러에 설치하는 것은 지원되지 않습니다**.
- **.NET Framework 4.6.1 이상**이 필요합니다. Windows 7 컴퓨터에 자체 호스팅 통합 런타임을 설치하는 경우 .NET Framework 4.6.1 이상을 설치합니다. 자세한 내용은 [.NET Framework 시스템 요구 사항](/dotnet/framework/get-started/system-requirements)을 참조하세요.
- 자체 호스팅 통합 런타임 컴퓨터에 대한 권장 **구성**은 최소 2GHz, 4개 코어, 8GB RAM 및 80GB 디스크입니다.
- 호스트 컴퓨터가 최대 절전 모드인 경우 자체 호스팅 통합 런타임이 데이터 요청에 응답하지 않습니다. 따라서 자체 호스팅 통합 런타임을 설치하기 전에 컴퓨터에서 전원 계획을 적절하게 구성하세요. 컴퓨터가 최대 절전 모드로 구성된 경우 자체 호스팅 통합 런타임 설치 시에 메시지가 표시됩니다.
- 자체 호스팅 통합 런타임을 성공적으로 설치 및 구성하려면 컴퓨터의 관리자여야 합니다.
- 또한 특정 주기에 복사 활동이 실행되므로 컴퓨터에서 리소스 사용(CPU, 메모리)은 유휴 시간 사용량이 가장 많을 때와 동일한 패턴을 따릅니다. 리소스 사용률은 이동하는 데이터 양에 따라 달라집니다. 여러 복사 작업이 진행 중인 경우 사용량이 많은 시간 동안 리소스 사용량이 증가하는 것을 볼 수 있습니다.

## <a name="installation-best-practices"></a>설치 모범 사례
자체 호스팅 통합 런타임은 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 MSI 설치 패키지를 다운로드하여 설치할 수 있습니다. 단계별 지침은 [온-프레미스 및 클라우드 간 데이터 이동](tutorial-hybrid-copy-powershell.md) 문서를 참조하세요.

- 호스트 컴퓨터에서 자체 호스팅 통합 런타임에 대한 전원 계획을 구성하여 컴퓨터가 최대 절전 모드를 시작하지 않도록 합니다. 호스트 컴퓨터가 최대 절전 모드인 경우 자체 호스팅 통합 런타임이 오프라인이 됩니다.
- 자체 호스팅된 통합 런타임과 연결된 자격 증명은 정기적으로 백업합니다.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>다운로드 센터에서 자체 호스팅 IR 설치 및 등록

1. [Microsoft Integration Runtime 다운로드 페이지](https://www.microsoft.com/download/details.aspx?id=39717)로 이동합니다.
2. **다운로드**를 클릭하여 적절한 버전(**32비트** 및 **64비트**)을 선택하고 **다음**을 클릭합니다.
3. **MSI**를 직접 실행하거나 하드 디스크에 저장하고 실행합니다.
4. **시작** 페이지에서 **언어**를 선택하고 **다음**을 클릭합니다.
5. 최종 사용자 라이선스 규약에 **동의**하고 **다음**을 클릭합니다.
6. 자체 호스팅 통합 런타임을 설치할 **폴더**를 선택하고 **다음**을 클릭합니다.
7. **설치 준비 완료** 페이지에서 **설치**를 클릭합니다.
8. **마침**을 클릭하고 설치를 완료합니다.
9. Azure PowerShell을 사용하여 인증 키를 가져옵니다. 인증 키 검색을 위한 PowerShell 예제:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. 컴퓨터에서 실행되는 Microsoft Integration Runtime Configuration Manager의 **Integration Runtime(자체 호스팅) 등록** 페이지에서 다음 단계를 수행합니다.
    1. 텍스트 영역에 **인증 키**를 붙여넣습니다.
    2. 필요에 따라 **인증 키 표시**를 클릭하여 키 텍스트를 확인합니다.
    3. **등록**을 클릭합니다.


## <a name="high-availability-and-scalability"></a>고가용성 및 확장성 
자체 호스팅 Integration Runtime은 여러 온-프레미스 컴퓨터에 연결할 수 있습니다. 이러한 컴퓨터를 노드라고 합니다. 최대 4개의 노드를 자체 호스팅 Integration Runtime에 연결할 수 있습니다. 논리 게이트웨이에 여러 다중 노드(게이트웨이가 설치된 온-프레미스 컴퓨터)를 사용하는 이점은 다음과 같습니다.
1. Azure Data Factory와의 빅 데이터 솔루션이나 클라우드 데이터 통합에서 자체 호스팅 Integration Runtime의 고가용성으로 단일 실패 지점이 없어 최대 4개 노드의 연속성을 보장합니다.
2. 온-프레미스 및 클라우드 데이터 저장소 간의 데이터 이동 성능 및 처리량을 향상시킵니다. 자세한 내용은 [성능 비교](copy-activity-performance.md)를 참조하세요.

[자습서](tutorial-hybrid-copy-powershell.md)에서 설명한 대로 [다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 자체 호스팅 Integration Runtime을 설치하고 New-AzureRmDataFactoryV2IntegrationRuntimeKey cmdlet에서 얻은 인증 키를 등록하는 것만으로 간단히 여러 노드를 연결할 수 있습니다.

> [!NOTE]
> 각각의 노드를 연결하기 위해 새로운 자체 호스팅 Integration Runtime을 만들 필요는 없습니다. 자체 호스팅 통합 런타임을 다른 컴퓨터에서 설치하고 동일한 인증 키를 사용하여 등록할 수 있습니다. 

> [!NOTE]
> **고가용성 및 확장**에 다른 노드를 추가하기 전에 **‘인트라넷에 원격 액세스’** 옵션이 첫 번째 노드(Microsoft Integration Runtime 구성 관리자 -> 인트라넷에 원격 액세스)에서 **활성화**로 설정되어 있는지 확인합니다. 

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 인증서 요구 사항
Integration Runtime 노드 간의 통신 보안에 사용되는 TLS/SSL 인증서에 대한 요구 사항은 다음과 같습니다.

- 인증서는 공개적으로 신뢰할 수 있는 X509 v3 인증서여야 합니다. 공용(타사) CA(인증 기관)에서 발급한 인증서를 사용하는 것이 좋습니다.
- 각 통합 런타임 노드는 이 인증서를 신뢰해야 합니다.
- 와일드 카드 인증서가 지원됩니다. FQDN 이름이 **node1.domain.contoso.com**인 경우 ***. domain.contoso.com**을 인증서의 주체 이름으로 사용할 수 있습니다.
- SAN 인증서는 현재 제한 때문에 주체 대체 이름의 마지막 항목만 사용되고 다른 항목은 무시되므로 권장되지 않습니다. 예: 해당 SAN이 **node1.domain.contoso.com** 및 **node2.domain.contoso.com**인 SAN 인증서가 있으며 해당 FQDN이 **node2.domain.contoso.com**인 컴퓨터에만 이 인증서를 사용할 수 있습니다.
- Windows Server 2012 R2에서 지원하는 SSL 인증서의 키 크기는 모두 지원됩니다.
- CNG 키를 사용하는 인증서는 지원되지 않습니다. CNG 키를 사용하는 인증서는 지원되지 않습니다.

## <a name="system-tray-icons-notifications"></a>시스템 트레이 아이콘/알림
커서를 시스템 트레이 아이콘/알림 메시지 위로 이동하면 자체 호스팅 통합 런타임의 상태에 대한 세부 정보가 팝업 창에 표시됩니다.

![시스템 트레이 알림](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>포트 및 방화벽
다음과 같은 고려해야 할 두 방화벽이 있습니다. 조직의 중앙 라우터에서 실행되는 **회사 방화벽** 및 자체 호스팅 통합 런타임을 설치한 로컬 컴퓨터에서 디먼으로 구성 **Windows 방화벽**입니다.

![방화벽](media\create-self-hosted-integration-runtime\firewall.png)

**회사 방화벽** 수준에서 다음 도메인 및 아웃바운드 포트를 구성 해야 합니다.

도메인 이름 | 포트 | 설명
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | 데이터 이동 서비스 백 엔드와 통신에 사용됨
*.core.windows.net | 443 | Azure Blob를 사용하여 준비된 복사에 사용됨(구성된 경우)
*.frontend.clouddatahub.net | 443 | 데이터 이동 서비스 백 엔드와 통신에 사용됨

**Windows 방화벽** 수준(컴퓨터 수준)에서 이러한 아웃바운드 포트를 일반적으로 사용할 수 있습니다. 그렇지 않은 경우 자체 호스팅 통합 런타임 컴퓨터에서 도메인 및 포트를 그에 따라 구성할 수 있습니다.

> [!NOTE]
> 소스/싱크에 따라 회사/Windows 방화벽에서 추가 도메인 및 아웃바운드 포트를 허용 목록으로 만들어야 할 수 있습니다.
>
> 일부 클라우드 데이터베이스(예: Azure SQL Database, Azure Data Lake 등)의 경우 해당 방화벽 구성에서 자체 호스팅 통합 런타임 컴퓨터의 IP 주소를 허용 목록으로 만들어야 합니다.

### <a name="copy-data-from-a-source-to-a-sink"></a>원본에서 싱크로 데이터 복사
방화벽 규칙이 회사 방화벽, 자체 호스팅 통합 런타임 컴퓨터의 Windows 방화벽 및 데이터 저장소 자체에 올바르게 설정되어 있는지 확인합니다. 이러한 규칙을 사용하면 자체 호스팅 통합 런타임이 원본과 싱크에 모두 정상적으로 연결할 수 있습니다. 복사 작업과 관련된 각 데이터 저장소에 대해 규칙을 사용하도록 설정합니다.

예를 들어 **온-프레미스 데이터 저장소에서 Azure SQL Database 싱크 또는 Azure SQL Data Warehouse 싱크로** 복사하려면 다음 단계를 수행합니다.

- Windows 방화벽 및 회사 방화벽 둘 다에 대해 포트 **1433**에서 아웃바운드 **TCP** 통신을 허용합니다.
- Azure SQL Server의 방화벽 설정을 구성하여 허용된 IP 주소 목록에 자체 호스팅 통합 런타임 컴퓨터의 IP 주소를 추가합니다.

> [!NOTE]
> 방화벽이 아웃바운드 포트 1433을 허용하지 않으면 자체 호스팅 통합 런타임은 Azure SQL에 직접 액세스할 수 없습니다. 이 경우에 SQL Azure Database/SQL Azure DW에 [준비된 복사](copy-activity-performance.md)를 사용할 수 있습니다. 이 시나리오에서는 데이터 이동에 HTTPS(포트 443)만 필요합니다.


## <a name="proxy-server-considerations"></a>프록시 서버 고려 사항
회사 네트워크 환경에서 프록시 서버를 사용하여 인터넷에 액세스하는 경우 자체 호스팅 통합 런타임이 적절한 프록시 설정을 사용하도록 구성합니다. 초기 등록 단계에서 프록시를 설정할 수 있습니다.

![프록시 지정](media\create-self-hosted-integration-runtime\specify-proxy.png)

자체 호스팅 통합 런타임은 프록시 서버를 사용하여 클라우드 서비스에 연결합니다. 초기 설치 중에 변경 링크를 클릭합니다. 프록시 설정 대화가 표시됩니다.

![프록시 설정](media\create-self-hosted-integration-runtime\set-http-proxy.png)

이 대화 상자에는 세 가지 구성 옵션이 있습니다.

- **프록시 사용 안 함**: 자체 호스팅 통합 런타임이 클라우드 서비스에 연결하는 데 프록시를 명시적으로 사용하지 않습니다.
- **시스템 프록시 사용**: 자체 호스팅 통합 런타임이 diahost.exe.config 및 diawp.exe.config에 구성되어 있는 프록시 설정을 사용합니다. diahost.exe.config 및 diawp.exe.config에 프록시가 구성되어 있지 않으면 자체 호스팅 통합 런타임은 프록시를 거치지 않고 클라우드 서비스에 직접 연결합니다.
- **사용자 지정 프록시 사용**: diahost.exe.config 및 diawp.exe.config의 구성을 사용하는 대신자체 호스팅 통합 런타임에 사용할 HTTP 프록시 설정을 구성합니다. 이 경우 주소 및 포트를 지정해야 합니다. 사용자 이름 및 암호는 프록시 인증 설정에 따라 입력할 수 있습니다. 모든 설정은 자체 호스팅 통합 런타임d에서 Windows DPAPI를 사용하여 암호화되며 컴퓨터에 로컬로 저장됩니다.

업데이트된 프록시 설정을 저장하고 나면 통합 런타임 호스트 서비스가 자동으로 다시 시작됩니다.

자체 호스팅 통합 런타임을 올바르게 등록한 후 프록시 설정을 확인하거나 업데이트하려면 Integration Runtime Configuration Manager를 사용합니다.

1.  **Microsoft Integration Runtime Configuration Manager**를 시작합니다.
2.  **설정** 탭으로 전환합니다.
3.  **HTTP 프록시** 섹션에서 **변경** 링크를 클릭하여 **HTTP 프록시 설정** 대화 상자를 시작합니다.
4.  **다음** 단추를 클릭하면 프록시 설정을 저장하고 Integration Runtime 호스트 서비스를 다시 시작하기 위한 권한이 필요하다는 경고 대화 상자가 표시됩니다.

구성 관리자 도구를 사용하여 HTTP 프록시를 확인하고 업데이트할 수 있습니다.

![프록시 보기](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> NTLM 인증을 사용하여 프록시 서버를 설치하면 Integration Runtime 호스트 서비스가 도메인 계정에서 실행됩니다. 나중에 도메인 계정의 암호를 변경하는 경우에는 서비스의 구성 설정을 업데이트하여 서비스를 다시 시작해야 합니다. 이 요구 사항을 감안하여, 암호를 자주 업데이트하지 않아도 되는 전용 도메인 계정을 사용해 프록시 서버에 액세스하는 것이 좋습니다.

### <a name="configure-proxy-server-settings"></a>프록시 서버 설정 구성

HTTP 프록시에 대해 **시스템 프록시 사용** 설정을 선택하는 경우 자체 호스팅 통합 런타임은 diahost.exe.config 및 diawp.exe.config의 프록시 설정을 사용합니다. diahost.exe.config 및 diawp.exe.config에 프록시가 지정되어 있지 않으면 자체 호스팅 통합 런타임은 프록시를 거치지 않고 클라우드 서비스에 직접 연결합니다. 다음 절차에서는 diahost.exe.config 파일을 업데이트하는 지침을 제공합니다.

1. 파일 탐색기에서 원본 파일을 백업할 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config의 안전한 복사본을 만듭니다.
2. 관리자 권한으로 Notepad.exe 실행을 시작하고 텍스트 파일 "C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config"를 엽니다. 다음 코드와 같이 system.net에 대한 기본 태그를 찾습니다.

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    그러면 다음 예제와 같이 프록시 서버 세부 정보를 추가할 수 있습니다.

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    프록시 태그 내에 scriptLocation과 같은 필수 설정을 지정하는 추가 속성을 사용할 수 있습니다. 구문은 [proxy 요소(네트워크 설정)](https://msdn.microsoft.com/library/sa91de1e.aspx)를 참조하세요.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. 구성 파일을 원래 위치에 저장한 다음 자체 호스팅 Integration Runtime 호스트 서비스를 다시 시작하면 변경 내용이 적용됩니다. 서비스를 다시 시작하려면 제어판의 서비스 애플릿을 사용하거나, **Integration Runtime Configuration Manager**에서 **서비스 중지** 단추를 클릭한 후 **서비스 시작**을 클릭합니다. 서비스가 시작되지 않으면 잘못된 XML 태그 구문이 편집된 응용 프로그램 구성 파일에 추가되었을 가능성이 높습니다.

> [!IMPORTANT]
> diahost.exe.config 및 diawp.exe.config를 둘 다 업데이트해야 합니다.

이러한 사항 외에 Microsoft Azure가 회사의 허용 목록에 있는지도 확인해야 합니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=41653)에서 유효한 Microsoft Azure IP 주소의 목록을 다운로드할 수 있습니다.

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>방화벽 및 프록시 서버 관련 문제 발생 시 나타날 수 있는 증상
다음과 비슷한 오류가 발생할 경우 방화벽 또는 프록시 서버가 잘못 구성된 것일 수 있습니다. 그러면 자체 호스팅 통합 런타임이 데이터 팩터리에 연결되어 인증을 할 수 없게 됩니다. 이전 섹션을 참조하여 방화벽 및 프록시 서버가 올바르게 구성되었는지 확인합니다.

1.  자체 호스팅 통합 런타임을 등록할 때 다음과 같은 오류 메시지가 표시됩니다. “이 Integration Runtime 노드를 등록하지 못했습니다. 인증 키가 유효하고 Integration Runtime 호스트 서비스가 이 컴퓨터에서 실행 중인지 확인하십시오. "
2.  Integration Runtime Configuration Manager를 열 때 상태가 "**연결 끊김**" 또는 "**연결 중**"으로 표시됩니다. Windows 이벤트 로그를 확인할 때 "이벤트 뷰어" > "응용 프로그램 및 서비스 로그" > "Microsoft Integration Runtime"에 다음 오류와 같은 오류 메시지가 표시됩니다.

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>인트라넷에서 원격 액세스를 사용하도록 설정  
**PowerShell** 또는 **자격 증명 관리자 응용 프로그램**을 사용하여 자체 호스팅 통합 런타임이 설치된 위치 외 다른 컴퓨터에서(네트워크 상) 자격 증명을 암호화하는 경우 **‘인트라넷에서 원격 액세스’** 옵션을 활성화해야 합니다. **PowerShell** 또는 **자격 증명 관리자 응용 프로그램**을 실행하여 자체 호스팅 통합 런타임이 설치된 위치와 동일한 컴퓨터에서 자격 증명을 암호화하는 경우 **‘인트라넷에서 원격 액세스’**를 사용할 수 없습니다.

인트라넷에서 원격 액세스는 **고가용성 및 확장성**에 다른 노드를 추가하기 전에 **활성화**해야 합니다.  

자체 호스팅 통합 런타임 설정(v 3.3.xxxx.x 이상) 중에는 기본적으로 자체 호스팅 통합 런타임 설치는 자체 호스팅 통합 런타임 컴퓨터에서 **‘인트라넷에서 원격 액세스’**를 사용하지 않습니다.

타사 방화벽을 사용 중인 경우 포트 8060을 수동으로 열 수 있습니다(또는 사용자 구성된 포트). 자체 호스팅 통합 런타임을 설치하는 동안 방화벽 문제가 발생하는 경우 다음 명령을 사용하여 방화벽을 구성하지 않고 자체 호스팅 통합 런타임을 설치할 수 있습니다.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> **자격 증명 관리자 응용 프로그램**은 ADFv2에서 자격 증명을 암호화하는 데 아직 사용할 수 없습니다. 이 지원은 나중에 추가될 예정입니다.  

자체 호스팅 통합 런타임 컴퓨터에서 포트 8060을 열지 않는 경우 **자격 증명 설정** 응용 프로그램을 사용하는 방식 이외의 메커니즘을 사용하여 데이터 저장소 자격 증명을 구성합니다. 예를 들어 New-AzureRmDataFactoryV2LinkedServiceEncryptCredential PowerShell cmdlet을 사용할 수 있습니다. 데이터 저장소 자격 증명을 설정할 수 있는 방법은 자격 증명 및 보안 설정 섹션을 참조하세요.


## <a name="next-steps"></a>다음 단계
단계별 지침은 [자습서: 온-프레미스 데이터를 클라우드에 복사](tutorial-hybrid-copy-powershell.md)를 참조하세요.
