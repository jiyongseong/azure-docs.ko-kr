---
title: Azure IoT(사물 인터넷) 소개
description: Azure IoT 및 관련 서비스와 기술에 대해 간략히 설명합니다.
services: iot-hub
documentationcenter: ''
author: BryanLa
manager: timlt
editor: bryanla
ms.service: iot
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: bryanla; v-clay
layout: LandingPage
ms.openlocfilehash: 183925324c404b412b5d12c3a9bff93ccd842535
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="introduction-to-azure-and-the-internet-of-things"></a>Azure IoT(사물 인터넷) 소개

Azure IoT는 IoT 응용 프로그램의 종단 간 개발을 쉽게 수행할 수 있도록 설계된 세 가지 영역의 기술과 솔루션(솔루션, 플랫폼 서비스 및 에지)으로 구성됩니다. 이 문서에서는 클라우드에서 IoT 솔루션의 일반적인 특성을 설명하고, Azure IoT에서 IoT 프로젝트 과제를 처리하는 방법과 Azure IoT를 채택해야 하는 이유에 대해 간략하게 설명합니다.

## <a name="iot-solution-architecture"></a>IoT 솔루션 아키텍처

IoT 솔루션은 수백만 대에 이르는 장치 및 솔루션 백 엔드 사이의 안전한, 양방향 통신을 필요로 합니다. 예를 들어 장치-클라우드 이벤트 스트림에서 새 정보를 발견하기 위하여 솔루션에서 자동화된 예측 분석을 사용할 수 있습니다. 

다음 다이어그램에서는 일반적인 IoT 솔루션 아키텍처의 주요 요소를 보여 줍니다. 이 다이어그램은 특정 구현 세부 정보(예: 사용되는 Azure 서비스 및 장치 운영 체제)와 관계가 없습니다. 이 아키텍처에서 IoT 장치는 클라우드 게이트웨이로 보내는 데이터를 수집합니다. 클라우드 게이트웨이를 통해 다른 백 엔드 서비스에 의한 처리에 데이터를 사용할 수 있습니다. 이러한 백 엔드 서비스에서 데이터를 제공할 수 있는 대상은 다음과 같습니다.

* 다른 기간 업무 응용 프로그램
* 대시보드 또는 다른 프레젠테이션 장치를 통한 인간 조작자

![IoT 솔루션 아키텍처][img-solution-architecture]

> [!NOTE]
> IoT 아키텍처에 대한 자세한 설명은 [Microsoft Azure IoT 참조 아키텍처][lnk-refarch]를 참조하세요.

### <a name="device-connectivity"></a>장치 연결

IoT 솔루션 아키텍처에서 장치는 일반적으로 저장 및 처리를 위해 클라우드에 원격 분석을 보냅니다. 예를 들어 예측 유지 관리 시나리오에서 솔루션 백 엔드는 센서 데이터 스트림을 사용하여 특정 펌프에 유지 관리가 필요한 시기를 결정할 수 있습니다. 또한 장치는 클라우드 끝점에서 메시지를 읽어 수신하고 클라우드-장치 메시지에 응답할 수 있습니다. 동일한 예에서 솔루션 백 엔드는 메시지를 펌핑 스테이션의 다른 펌프로 보내서 유지 관리가 시작되기 직전에 라우팅 흐름을 다시 시작할 수 있습니다. 이 절차를 통해 유지 관리 엔지니어가 도착하는 즉시 작업을 시작할 수 있는지 확인합니다.

장치를 안전하고 안정적으로 연결하는 것은 종종 IoT 솔루션의 가장 중요한 과제입니다. 이는 브라우저 및 모바일 앱과 같은 다른 클라이언트에 비해 다른 특징이 IoT 장치에 있기 때문입니다. IoT 장치의 이러한 특징은 구체적으로 다음과 같습니다.

* 인간 조작자가 없는 내장 시스템인 경우가 많습니다(전화기와 다름).
* 물리적 액세스에 많은 비용이 드는 원격 위치에 배포될 수 있습니다.
* 솔루션 백 엔드를 통해서만 연결 가능합니다. 장치와 상호 작용하는 다른 방법이 없습니다.
* 전원 및 리소스 처리 제한이 있을 수 있습니다.
* 일시적이거나 느리거나 비용이 많이 드는 네트워크 연결이 있을 수 있습니다.
* 독점, 사용자 지정 또는 업계 특정 응용 프로그램 프로토콜을 사용해야 할 수 있습니다.
* 널리 사용되는 다양한 하드웨어 및 소프트웨어 플랫폼을 사용하여 만들 수 있습니다.

이전의 제약 조건 외에도 모든 IoT 솔루션이 확장 가능하고, 안전하며, 신뢰할 수 있어야 합니다.

통신 프로토콜 및 네트워크 가용성에 따라 장치는 클라우드와 직접 통신하거나 중간 게이트웨이를 통해 통신할 수 있습니다. IoT 아키텍처에는 이러한 두 가지 통신 패턴이 혼합되어 있는 경우가 많습니다.

### <a name="data-processing-and-analytics"></a>데이터 처리 및 분석

최신 IoT 솔루션에서 데이터 처리는 클라우드 또는 장치 쪽에서 발생할 수 있습니다. 장치 쪽 처리를 *에지 컴퓨팅*이라고 합니다. 데이터를 처리할 위치는 다음과 같은 요인에 따라 다릅니다.

* 네트워크 제약 조건 - 장치와 클라우드 간의 대역폭이 제한된 경우에는 더 많은 에지 처리를 수행할 동기가 있습니다.
* 응답 시간 - 거의 실시간으로 장치를 작동해야 한다는 요구 사항이 있는 경우 장치 자체에서 응답을 처리하는 것이 더 나을 수 있습니다. 예를 들어 비상시에 중지해야 하는 로봇 팔이 있습니다.
* 규정 환경 - 일부 데이터는 클라우드로 보낼 수 없습니다.

일반적으로 에지 및 클라우드 모두의 데이터 처리는 다음 기능을 결합한 것입니다.

* 장치로부터 원격 분석을 대규모로 받고 해당 데이터를 처리하고 저장하는 방법을 결정합니다.
* 실시간이든 사후이든 상관없이 원격 분석을 분석하여 통찰력을 제공합니다.
* 클라우드 또는 게이트웨이 장치에서 특정 장치로 명령을 보냅니다.

또한 IoT 클라우드 백 엔드는 다음을 제공해야 합니다.

* 다음을 수행할 수 있는 장치 등록 기능
    * 장치를 프로비전합니다.
    * 인프라에 연결할 수 있는 장치를 제어합니다.
* 장치 상태를 제어하고 해당 작업을 모니터링하는 장치 관리

예를 들어 예측 유지 관리 시나리오에서 솔루션 백 엔드는 원격 분석 기록 데이터를 저장합니다. 솔루션에서 이 데이터를 사용하여 실제 문제가 발생하기 전에 특정 펌프의 잠재적인 비정상 동작을 식별합니다. 데이터 분석을 사용하면 예방 솔루션에서 정정 작업을 수행하도록 명령을 장치로 다시 보내는 것을 식별할 수 있습니다. 이 프로세스는 장치와 클라우드 간에 자동화된 피드백 루프를 생성하여 솔루션의 효율성을 크게 높입니다.

### <a name="presentation-and-business-connectivity"></a>프레젠테이션 및 Business Connectivity

프레젠테이션 및 Business Connectivity 계층을 통해 최종 사용자가 IoT 솔루션 및 장치를 조작할 수 있습니다. 이를 통해 사용자가 해당 장치에서 수집된 데이터를 보고 분석할 수 있습니다. 이러한 보기는 기록 데이터와 거의 실시간 데이터를 모두 표시할 수 있는 대시보드 또는 BI 보고서 형식일 수 있습니다. 예를 들어 운영자는 특정 펌핑 스테이션의 상태를 검사하고 시스템에서 발생한 경고를 확인할 수 있습니다. 또한 이 계층을 통해 IoT 솔루션 백 엔드와 기존 기간 업무 응용 프로그램을 통합하여 엔터프라이즈 비즈니스 프로세스 또는 워크플로와 연결할 수 있습니다. 예를 들어 예측 유지 관리 솔루션은 유지 관리가 필요한 펌프를 식별한 경우 엔지니어가 펌핑 스테이션을 방문하는 일정을 예약하는 예약 시스템과 통합될 수 있습니다.

[img-solution-architecture]: ./media/iot-introduction/iot-reference-architecture.png
[img-dashboard]: ./media/iot-introduction/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf


## <a name="why-azure-iot"></a>Azure IoT가 적합한 이유

Azure IoT는 IoT 프로젝트의 복잡성을 간소화하고, 보안, 인프라 비호환성 및 IoT 솔루션 크기 조정과 같은 과제를 처리합니다. 방법은 다음과 같습니다.

**민첩성** <br>
IoT 과정 가속화
* 규모: 작은 규모로 시작하여 원하는 크기로 언제 어디서나 확장할 수 있습니다. 전 세계 대부분의 지역에서 수백만 개의 장치, 테라바이트 단위의 데이터를 사용할 수 있습니다.

* 개방: 장치, 소프트웨어 또는 서비스에 연결하여 현재 보유하고 있는 기능을 사용하거나 미래를 위해 현대화합니다.

* 하이브리드: 에지, 클라우드 또는 그 중간의 어디에서든 IoT 솔루션을 배포하여 필요에 따라 빌드합니다.

* 속도: 더 빠르게 배포하고, 제품 출시 시간을 앞당기고, IoT의 솔루션 가속기 및 혁신 속도 분야에서 선도업체와의 경쟁 우위를 유지하고 있습니다.

**포괄성** <br>
강력한 비즈니스 효과

* 완전: Microsoft는 빅 데이터, 고급 분석 및 관리 서비스를 통해 장치를 클라우드로 확장하는 완벽한 플랫폼을 갖춘 유일한 IoT 솔루션 공급자입니다.

* 성공을 보장하는 파트너: 세계 최대의 파트너 에코시스템의 역량을 활용하고, 업계 전반 및 전 세계에 걸친 LOB(기간 업무)와 기술에 활기를 불어넣습니다.

* 데이터 기반: IoT는 데이터에 관한 것이고, 최상의 IoT 솔루션은 데이터를 저장, 해석, 변환, 분석 및 제공하는 데 필요한 모든 도구를 결합하여 적절한 시간에, 적절한 위치에서, 적절한 사용자에게 제공합니다.

* 장치 중심: Microsoft IoT를 사용하면 레거시 장비에서 인증된 하드웨어의 방대한 에코시스템에 이르기까지 모든 장치를 연결할 수 있으며, 에지, 모바일 및 포함된 시스템 전반에 걸쳐 자신의 장치를 직접 구축할 수 있습니다.

**보안** <br>
가장 어려운 IoT 부분 - 보안 해결

* 권한 부여: Microsoft IoT를 사용하면 기술, 모범 사례 및 기능과 함께 비전을 제공하여 가장 어려운 IoT 부분인 보안을 해결할 수 있습니다.

* 조치: ID 및 액세스 관리, 위협 및 정보 보호, 보안 관리 등을 통해 IoT 데이터를 보호하고 위험을 관리합니다.

* 안심: 온-프레미스 환경뿐만 아니라 장치, 소프트웨어, 응용 프로그램 및 클라우드 서비스 전반에 걸쳐 중요한 정보의 안전성을 보장합니다.

* 규정 준수: Microsoft는 IoT 장치, 데이터 및 서비스에 대한 광범위한 국제 및 업계 관련 표준을 충족하는 보안 요구 사항을 수립하는 데 있어 업계를 선도하고 있습니다.

## <a name="next-steps"></a>다음 단계

다음 기술 및 솔루션 영역을 살펴보거나 왼쪽의 목차에서 Azure IoT 서비스 목록을 참조하세요.

<ul class="panelContent cardsF">  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>솔루션</h3>
                        <a href="/azure/iot-suite">IoT Suite</a><br/>
                        <a href="/azure/iot-central">IoT Central</a>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>플랫폼 서비스</h3>
                        <a href="/azure/iot-hub">IoT 허브</a><br/>
                        <a href="/azure/iot-dps">IoT Hub Device Provisioning 서비스</a><br/>
                        <a href="/azure/azure-maps">Maps</a><br/>
                        <a href="/azure/time-series-insights">Time Series Insights</a>
                    </div>
                </div>
            </div>
        </div>
    </li>  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Edge</h3>
                        <a href="/azure/iot-edge">IoT Edge</a><br/>
                        <a href="/azure/iot-edge/how-iot-edge-works">IoT Edge란?</a>
                    </div>
                </div>
            </div>
        </div>
    </li>      
</ul>

[img-paas-saas-technologies-solutions]: media/index/paas-saas-technologies-solutions.png

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-central-land]: https://docs.microsoft.com/microsoft-iot-central/
[lnk-iot-dps-land]: /azure/iot-dps/index.yml
[lnk-iot-edge-land]: /azure/iot-edge/index.yml
[lnk-iot-hub-land]: /azure/iot-hub/index.md
[lnk-iot-maps-land]: /azure/azure-maps/index.yml
[lnk-iot-suite-land]: /azure/iot-suite/index.md
[lnk-iot-tsi-land]: /azure/time-series-insights/index.yml


