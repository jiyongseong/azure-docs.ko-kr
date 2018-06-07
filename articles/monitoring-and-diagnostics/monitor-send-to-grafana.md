---
title: Grafana를 사용하여 Azure 서비스 및 응용 프로그램 모니터링 | Microsoft Docs
description: Azure Monitor 및 Application Insights 데이터를 라우팅하여 Grafana에서 볼 수 있도록 합니다.
services: monitoring-and-diagnostics
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: 537760554baa542d4cd967d2e1e885f936303175
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="monitor-your-azure-services-in-grafana"></a>Grafana에서 Azure 서비스 모니터링
이제 [Azure Monitor 데이터 원본 플러그 인](https://grafana.com/plugins/grafana-azure-monitor-datasource)을 사용하여 [Grafana](https://grafana.com/)에서 Azure 서비스 및 응용 프로그램을 모니터링할 수 있습니다. 플러그 인은 Azure Monitor에서 제공하는 인프라 데이터뿐만 아니라 Application Insights SDK에서 수집한 응용 프로그램 성능 데이터도 수집합니다. 그런 다음 Grafana 대시보드에 이 데이터를 표시할 수 있습니다.

플러그 인은 현재 미리 보기에 있습니다.

Azure Marketplace에서 Grafana 서버를 설정하고 Azure Monitor 및 Application Insights에서 메트릭에 대한 대시보드를 빌드하려면 다음 단계를 사용합니다.

## <a name="set-up-a-grafana-instance"></a>Grafana 인스턴스 설정
1. Azure Marketplace로 이동하여 Grafana Labs에서 Grafana를 선택합니다.

2. 이름 및 세부 정보를 입력합니다. 새 리소스 그룹을 만듭니다. VM 사용자 이름, VM 암호 및 Grafana 서버 관리자 암호에 대해 선택한 값을 추적합니다.  

3. VM 크기 및 저장소 계정을 선택합니다.

4. 네트워크 구성 설정을 구성합니다.

5. 요약을 보고 사용 약관에 동의한 후 **만들기**를 선택합니다.

## <a name="log-in-to-grafana"></a>Grafana에 로그인
1. 배포가 완료되면 **리소스 그룹으로 이동**을 선택합니다. 새로 만든 리소스의 목록이 표시됩니다.

    ![Grafana 리소스 그룹 개체](.\media\monitor-how-to-grafana\grafana1.png)

    네트워크 보안 그룹(이 경우 *grafana-nsg*)을 선택하면 3000 포트가 Grafana 서버에 액세스하는 데 사용됨을 알 수 있습니다.

2. 리소스 목록으로 돌아가서 **공용 IP 주소**를 선택합니다. 이 화면에 있는 값을 사용하여 브라우저에서 *http://<IP address>:3000* 또는 *<DNSName>:3000*을 입력합니다. 방금 빌드한 Grafana 서버에 대한 로그인 페이지가 표시됩니다.

    ![Grafana 로그인 화면](.\media\monitor-how-to-grafana\grafana2.png)

3. 사용자 이름으로 *admin* 및 이전에 만든 Grafana 서버 관리자 암호를 사용하여 로그인합니다.

## <a name="configure-data-source-plugin"></a>데이터 원본 플러그 인 구성

성공적으로 로그인하면 Azure Monitor 데이터 원본 플러그 인이 이미 포함되어 있는지 확인해야 합니다.

![Azure Monitor 플러그 인을 보여 주는 Grafana](.\media\monitor-how-to-grafana\grafana3.png)

1. **데이터 원본 추가**를 선택하여 Azure Monitor 및 Application Insights를 구성합니다.

2. 데이터 원본 이름을 선택하고, 드롭다운에서 **Azure Monitor**를 데이터 원본으로 선택합니다.


## <a name="create-a-service-principal"></a>서비스 주체 만들기

Grafana는 Azure Monitor API에 연결하고 메트릭 데이터를 수집하는 데 Azure Active Directory 서비스 주체를 사용합니다. Azure 리소스에 대한 액세스를 관리하려면 서비스 주체를 만들어야 합니다.

1. 서비스 주체를 만들려면 [이러한 지침](../azure-resource-manager/resource-group-create-service-principal-portal.md)을 참조하세요. 테넌트 ID, 클라이언트 ID 및 클라이언트 암호를 복사하고 저장합니다.

2. Azure Active Directory 응용 프로그램에 읽기 권한자 역할을 할당하려면 [역할에 응용 프로그램 할당](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role)을 참조하세요.     

3. Application Insights를 사용하는 경우 Application Insights API 및 응용 프로그램 ID를 포함하여 Application Insights 기반 메트릭을 수집할 수도 있습니다. 자세한 내용은 [API 키 및 응용 프로그램 ID 가져오기](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)를 참조하세요.

4. 이 정보를 모두 입력한 후에는 **저장**을 선택하고 Grafana에서 API를 테스트합니다. 다음과 비슷한 메시지가 표시됩니다.  

    ![Azure Monitor 플러그 인을 보여 주는 Grafana](.\media\monitor-how-to-grafana\grafana4-1.png)

> [!NOTE]
> 플러그 인을 구성하는 동안 플러그 인이 구성되기를 원하는 Azure 클라우드(공개, Azure 미국 정부, Azure 독일 또는 Azure 중국)를 지정할 수 있습니다.
>
>

## <a name="build-a-grafana-dashboard"></a>Grafana 대시보드 빌드

1. 홈으로 이동하여 **새 대시보드**를 선택합니다.

2. 새 대시보드에서 **그래프**를 선택합니다. 다른 차트 옵션을 사용해 볼 수도 있지만 이 문서에서는 *그래프*를 예로 사용합니다.

    ![새 Grafana 대시보드](.\media\monitor-how-to-grafana\grafana5.png)

3. 빈 그래프가 대시보드에 표시됩니다.

4. 패널 제목을 클릭하고 **편집**을 선택하여 이 그래프 차트에 그리려는 데이터의 세부 정보를 입력합니다.

5. 올바른 VM을 모두 선택하면 대시보드에서 메트릭 보기를 시작할 수 있습니다.

다음은 두 개의 차트가 있는 간단한 대시보드입니다. 왼쪽 차트에는 두 VM의 CPU 백분율이 표시됩니다. 오른쪽 차트에는 트랜잭션 API 유형별로 분류된 Azure Storage 계정의 트랜잭션이 표시됩니다.

![두 Grafana 차트 예제](.\media\monitor-how-to-grafana\grafana6.png)


## <a name="optional-create-dashboard-playlists"></a>선택 사항: 대시보드 재생 목록 만들기

Grafana의 많은 유용한 기능 중 하나는 대시보드 재생 목록입니다. 여러 개의 대시보드를 만들고 각 대시보드에서 표시할 간격을 구성하여 재생 목록에 추가할 수 있습니다. **재생**을 선택하여 대시보드가 순환하는지 확인합니다. 그룹에 대한 "상태 보드"를 대형 벽면 모니터에 표시하여 제공할 수도 있습니다.

![Grafana 재생 목록 예제](.\media\monitor-how-to-grafana\grafana7.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>선택 사항: 동일한 Grafana 서버에서 사용자 지정 메트릭 모니터링

Telegraf 및 InfluxDB를 설치하여 동일한 Grafana 인스턴스에서 사용자 지정 및 에이전트 기반 메트릭을 수집하고 그릴 수도 있습니다. 대시보드에서 이러한 메트릭을 함께 가져오는 데 사용할 수 있는 많은 데이터 원본 플러그 인이 있습니다.

또한 이 설정을 다시 사용하여 Prometheus 서버의 메트릭을 포함할 수도 있습니다. Grafana의 플러그 인 갤러리에서 Prometheus 데이터 원본 플러그 인을 사용합니다.

다음은 Telegraf, InfluxDB, Prometheus 및 Docker를 사용하는 방법에 대한 좋은 참조 문서입니다.
 - [Ubuntu 16.04에서 TICK 스택을 사용하여 시스템 메트릭을 모니터링하는 방법](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Grafana, InfluxDB 및 Telegraf를 사용하여 Docker 리소스 메트릭 모니터링](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Docker 호스트, 컨테이너 및 컨테이너화된 서비스에 대한 모니터링 솔루션](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

다음은 Azure Monitor 및 Application Insights의 메트릭이 포함된 전체 Grafana 대시보드에 대한 이미지입니다.
![Grafana 예제 메트릭](.\media\monitor-how-to-grafana\grafana8.png)


## <a name="clean-up-resources"></a>리소스 정리

VM 사용 여부에 관계없이 VM이 실행되는 경우 요금이 청구됩니다. 추가 비용이 발생하지 않도록 방지하려면 이 문서에서 만든 리소스 그룹을 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 **Grafana**를 클릭합니다.
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고, 텍스트 상자에서 **Grafana**를 입력한 다음, **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계
* [Azure Monitor 메트릭 개요](monitoring-overview-metrics.md)
