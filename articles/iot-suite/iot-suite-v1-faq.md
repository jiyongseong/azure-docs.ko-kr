---
title: Azure IoT Suite FAQ | Microsoft Docs
description: "IoT Suite에 대한 질문과 대답"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 6c644395aa7ad51c289cdc4a23b02e9e0239a0fe
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>IoT Suite에 대한 질문과 대답

연결된 팩터리 관련 [FAQ](iot-suite-faq-cf.md)도 참조하세요.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>어디에서 미리 구성된 솔루션의 소스 코드를 찾을 수 있습니까?

소스 코드는 다음 GitHub 리포지토리에 저장됩니다.
* [미리 구성된 원격 모니터링 솔루션][lnk-remote-monitoring-github]
* [예측 유지 관리 미리 구성된 솔루션][lnk-predictive-maintenance-github]
* [연결된 팩터리 미리 구성된 솔루션](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>IoT Hub 장치 관리 기능을 사용하는 미리 구성된 원격 모니터링 솔루션을 최신 버전으로 업데이트하려면 어떻게 해야 합니까?

* https://www.azureiotsuite.com/ 사이트에서 미리 구성된 솔루션을 배포하는 경우 항상 최신 버전의 솔루션의 새 인스턴스를 배포합니다.
* 명령줄을 사용하여 미리 구성된 솔루션을 배포하는 경우 새 코드와 함께 기존 배포를 업데이트할 수 있습니다. GitHub [리포지토리][lnk-remote-monitoring-github]에서 [클라우드 배포][lnk-cloud-deployment]를 참조하세요.

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>미리 구성된 원격 모니터링 솔루션에 새 장치 메서드에 대한 지원을 어떻게 추가할 수 있습니까?

[미리 구성된 솔루션 사용자 지정][lnk-customize] 문서에서 [시뮬레이터에 새 메서드에 대한 지원 추가][lnk-add-method] 섹션을 참조하세요.

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>시뮬레이션된 장치가 desired 속성 변경을 무시하고 있는 이유는 무엇입니까?
미리 구성된 원격 모니터링 솔루션에서 시뮬레이션된 장치 코드는 **Desired.Config.TemperatureMeanValue** 및 **Desired.Config.TelemetryInterval** desired 속성만을 사용하여 reported 속성을 업데이트합니다. 다른 모든 desired 속성 변경 요청은 무시됩니다.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>내 장치가 솔루션 대시보드의 장치 목록에 나타나지 않는 이유는 무엇입니까?

솔루션 대시보드의 장치 목록은 쿼리를 사용하여 장치 목록을 반환합니다. 현재 쿼리는 10K 이상의 장치를 반환할 수 없습니다. 쿼리에 대한 검색 조건을 더 제한적으로 설정해 보세요.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Azure 포털에서 리소스 그룹을 삭제하는 것과 azureiotsuite.com의 미리 구성된 솔루션에서 삭제를 클릭하는 것의 차이는 무엇인가요?

* [azureiotsuite.com][lnk-azureiotsuite]에서 미리 구성된 솔루션을 삭제하면, 미리 구성된 솔루션을 만들 때 프로비전된 모든 리소스가 삭제됩니다. 리소스 그룹에 리소스를 추가하면 이들 역시 삭제됩니다. 
* [Azure Portal][lnk-azure-portal]에서 리소스 그룹을 삭제하는 경우 해당 리소스 그룹에서 리소스만 삭제합니다. 또한 [Azure Portal][lnk-azure-portal]에 미리 구성된 솔루션과 연결된 Azure Active Directory 응용 프로그램을 삭제해야 합니다.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 IoT Hub 인스턴스는 몇 개인가요?

기본적으로 [구독당 10개의 IoT Hub][link-azuresublimits]를 프로비전할 수 있습니다. [Azure 지원 티켓][link-azuresupportticket]을 만들어 이 제한을 높일 수 있습니다. 결과적으로 미리 구성된 모든 솔루션이 새 IoT Hub를 프로비전하기 때문에 지정된 구독에서 최대 10개의 미리 구성된 솔루션을 프로비전할 수 있습니다. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 Azure Cosmos DB 인스턴스는 몇 개인가요?

50개입니다. [Azure 지원 티켓][link-azuresupportticket]을 만들어서 이 한도를 높일 수 있지만, 기본적으로 구독당 Cosmos DB 인스턴스를 50개만 프로비전할 수 있습니다. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 무료 Bing 지도 API는 몇 개인가요?

2개입니다. 두 개의 Enterprise용 내부 트랜잭션 Level 1 Bing Maps 계획을 Azure 구독에서 만들 수 있습니다. 원격 모니터링 솔루션이 내부 트랜잭션 Level 1 계획을 사용하여 기본으로 프로비전됩니다. 결과적으로 구독에 원격 모니터링 솔루션을 가감 없이 2개까지만 프로비전할 수 있습니다.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>정적 맵이 있는 원격 모니터링 솔루션 배포에서는 대화형 Bing 맵을 어떻게 추가하나요?

1. [Azure Portal][lnk-azure-portal]에서 엔터프라이즈용 Bing 맵 API QueryKey를 가져옵니다. 
   
   1. [Azure Portal][lnk-azure-portal]에서 엔터프라이즈용 Bing 맵 API가 있는 리소스 그룹으로 이동합니다.
   2. **모든 설정**을 클릭한 다음 **키 관리**를 클릭합니다. 
   3. **MasterKey**와 **QueryKey** 등, 두 개의 키를 볼 수 있습니다. **QueryKey**에 대한 값을 복사합니다.
      
      > [!NOTE]
      > 엔터프라이즈용 Bing 맵 API 계정이 없는 경우 [Azure Portal][lnk-azure-portal]에서 + 새로 만들기를 클릭하고 엔터프라이즈용 Bing 맵 API를 검색한 다음 지침에 따라 새로 만듭니다.
      > 
      > 
2. [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github]에서 최신 코드를 풀다운합니다.
3. 저장소의 /docs/ 폴더에서 명령줄 배포 지침에 따라 로컬 또는 클라우드 배포를 실행합니다. 
4. 로컬 또는 클라우드 배포를 실행한 후에는 루트 폴더에서 배포 중에 *.user.config 파일이 만들어졌는지 확인합니다. 텍스트 편집기에서 이 파일을 엽니다. 
5. **QueryKey**에서 복사한 값을 포함하도록 다음 줄을 변경합니다. 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>DreamSpark용 Microsoft Azure가 있는 경우 사전 구성된 솔루션을 만들 수 있나요?

> [!NOTE]
> Microsoft Azure for DreamSpark는 현재 학생용 Microsoft Imagine이라고 합니다.

현재는 [DreamSpark용 Microsoft Azure](https://azure.microsoft.com/pricing/member-offers/imagine/) 계정으로 미리 구성된 솔루션을 만들 수 없습니다. 하지만 몇 분 이내에 [Azure용 평가판 계정](https://azure.microsoft.com/free/)을 만들면 미리 구성된 솔루션을 만들 수 있습니다.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>CSP(클라우드 솔루션 공급자) 구독이 있는 경우 미리 구성된 솔루션을 만들 수 있나요?

현재는 CSP(클라우드 솔루션 공급자) 구독이 있는 미리 구성된 솔루션을 만들 수 없습니다. 하지만 몇 분 이내에 [Azure용 평가판 계정][lnk-30daytrial]을 만들면 사전 구성된 솔루션을 만들 수 있습니다.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Azure AD 테넌트를 삭제하려면 어떻게 하나요?

Eric Golpe의 블로그 게시물 [Azure AD 테넌트 삭제 연습(영문)][lnk-delete-aad-tennant]을 참조하세요.

### <a name="next-steps"></a>다음 단계

미리 구성된 IoT Suite 솔루션의 몇 가지 다른 기능 및 기능을 탐색할 수 있습니다.

* [예측 정비 사전 구성 솔루션 개요][lnk-predictive-overview]
* [연결된 팩터리 미리 구성된 솔루션 개요](iot-suite-connected-factory-overview.md)
* [처음부터 IoT 보안을 고려][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
