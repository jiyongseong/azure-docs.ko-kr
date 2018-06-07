---
title: Azure DC/OS 클러스터 모니터링 - 운영 관리
description: Log Analytics를 사용하여 Azure Container Service DC/OS 클러스터를 모니터링합니다.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: b326e5b686e14cefac4e6376bd3f26787ea1d10d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>Log Analytics를 사용하여 Azure Container Service DC/OS 클러스터 모니터링

Log Analytics는 온-프레미스 및 클라우드 인프라를 관리하고 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다. Container Solution은 컨테이너 인벤토리, 성능 및 로그를 단일 위치에서 볼 수 있는 Log Analytics의 솔루션입니다. 중앙 위치에서 로그를 확인하여 컨테이너를 감사하고 문제를 해결하며 호스트에서 매우 과도하게 사용되는 컨테이너를 찾을 수 있습니다.

![](media/container-service-monitoring-oms/image1.png)

Container Solution에 대한 자세한 내용은 [Container Solution Log Analytics](../../log-analytics/log-analytics-containers.md)를 참조하세요.

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>DC/OS Universe에서 Log Analytics 설정


여기서는 DC/OS를 설치하고 클러스터에 간단한 웹 컨테이너 응용 프로그램을 배포했다고 가정합니다.

### <a name="pre-requisite"></a>필수 구성 요소
- [Microsoft Azure 구독](https://azure.microsoft.com/free/) - 무료로 다운로드할 수 있습니다.  
- Log Analytics 작업 영역 설정 - 아래 "3단계" 참조
- [DC/OS CLI](https://dcos.io/docs/1.8/usage/cli/install/) 설치

1. DC/OS 대시보드에서 아래와 같이 Universe를 클릭하고 'OMS'를 검색합니다.

![](media/container-service-monitoring-oms/image2.png)

2. **Install**을 클릭합니다. 버전 정보와 **패키지 설치** 또는 **고급 설치** 단추가 있는 팝업이 표시됩니다. **고급 설치**를 클릭하면 **OMS 특정 구성 속성** 페이지로 이동합니다.

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. 여기서 `wsid`(Log Analytics 작업 영역 ID) 및 `wskey`(작업 영역 ID에 대한 기본 키)를 입력하도록 요구하는 메시지가 표시됩니다. `wsid` 및 `wskey`를 모두 가져오려면 <https://mms.microsoft.com>에서 계정을 만들어야 합니다.
다음 단계에 따라 계정을 만듭니다. 계정을 만들었으면 아래와 같이 **설정**, **연결된 원본**, **Linux 서버**를 차례로 클릭하여 `wsid` 및 `wskey`를 획득해야 합니다.

 ![](media/container-service-monitoring-oms/image5.png)

4. 원하는 인스턴스 수를 선택하고 '검토 및 설치' 단추를 클릭합니다. 일반적으로 인스턴스 수는 에이전트 클러스터에 있는 VM 수와 같아야 합니다. Linux용 OMS 에이전트는 각 VM에 개별 컨테이너로 설치되어 모니터링 및 로깅 정보에 대한 정보를 수집하려고 합니다.

## <a name="setting-up-a-simple-oms-dashboard"></a>간단한 OMS 대시보드 설정

VM에 Linux용 OMS 에이전트를 설치했으면 다음 단계는 OMS 대시보드를 설정하는 것입니다. 이렇게 수행하는 두 가지 방법으로 OMS Portal 또는 Azure Portal이 있습니다.

### <a name="oms-portal"></a>OMS 포털 

OMS 포털(<https://mms.microsoft.com>)에 로그인하고 **솔루션 갤러리**로 이동합니다.

![](media/container-service-monitoring-oms/image6.png)

**솔루션 갤러리**에 연결되면 **컨테이너**를 선택합니다.

![](media/container-service-monitoring-oms/image7.png)

컨테이너 솔루션을 선택하면 [OMS 개요 대시보드] 페이지에 해당 타일이 표시됩니다. 수집된 컨테이너 데이터가 인덱싱되면 솔루션 보기 타일에 대한 정보로 채워진 타일이 표시됩니다.

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Azure Portal 

Azure Portal ( <https://portal.microsoft.com/> ) 에 로그인합니다. **Marketplace**로 이동하고 **모니터링 + 관리**를 선택한 다음 **모두 표시**를 클릭합니다. 그런 다음 검색 상자에서 `containers`를 입력합니다. 그러면 검색 결과에서 "컨테이너"가 표시됩니다. **컨테이너**를 선택하고 **만들기**를 클릭합니다.

![](media/container-service-monitoring-oms/image9.png)

**만들기**를 클릭하면 작업 영역을 묻습니다. 작업 영역을 선택하거나 작업 영역이 없는 경우 새 작업 영역을 만듭니다.

![](media/container-service-monitoring-oms/image10.PNG)

작업 영역을 선택했으면 **만들기**를 클릭합니다.

![](media/container-service-monitoring-oms/image11.png)

Log Analytics Container Solution에 대한 자세한 내용은 [Container Solution Log Analytics](../../log-analytics/log-analytics-containers.md)를 참조하세요.

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>ACS DC/OS로 OMS 에이전트를 확장하는 방법 

실제 노드 수에 부족한 OMS 에이전트를 설치해야 하거나 VM을 더 추가하여 VMSS를 확장하는 경우 `msoms` 서비스를 확장하여 그렇게 할 수 있습니다.

Marathon 또는 DC/OS UI Services 탭으로 이동하여 노드 수를 확장할 수 있습니다.

![](media/container-service-monitoring-oms/image12.PNG)

이렇게 하면 OMS 에이전트를 아직 배포하지 않은 다른 노드에 배포합니다.

## <a name="uninstall-ms-oms"></a>MS OMS 제거

MS OMS를 제거하려면 다음 명령을 입력합니다.

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>알려주세요!!!
무슨 일입니까? 무엇이 필요합니까? 여러분에게 도움이 되기 위해 무엇이 더 필요합니까? <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>에 알려주세요.

## <a name="next-steps"></a>다음 단계

 이제 컨테이너를 모니터링하도록 Log Analytics를 설정했으므로 [컨테이너 대시보드를 참조하세요](../../log-analytics/log-analytics-containers.md).
