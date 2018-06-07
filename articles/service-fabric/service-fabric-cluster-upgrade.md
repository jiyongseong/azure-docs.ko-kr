---
title: Azure Service Fabric 클러스터 업그레이드 | Microsoft Docs
description: 클러스터 업데이트 모드 설정, 인증서 업그레이드, 응용 프로그램 포트 추가, OS 패치 수행 등을 포함하는 Service Fabric 클러스터를 실행하는 Service Fabric 코드 및/또는 구성을 업그레이드합니다. 업그레이드를 수행할 때 예상할 수 있는 것은 무엇입니까?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/10/2017
ms.author: aljo
ms.openlocfilehash: 3e5ef2cbc9d7bec82cadc0c7663de52636938505
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Azure Service Fabric 클러스터 업그레이드
> [!div class="op_single_selector"]
> * [Azure 클러스터](service-fabric-cluster-upgrade.md)
> * [독립 실행형 클러스터](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

최신 시스템의 경우 업그레이드 기능 디자인이 제품의 장기적 성공 달성의 비결입니다. Azure 서비스 패브릭 클러스터는 개인이 소유하지만 Microsoft에서 부분적으로 관리하는 리소스입니다. 이 문서는 자동으로 관리되는 것과 스스로 구성할 수 있는 것을 설명합니다.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>클러스터에서 실행되는 패브릭 버전 제어
Microsoft에서 자동 패브릭 업그레이드를 릴리스하면 클러스터가 수신하도록 설정할 수 있습니다. 또는 클러스터를 배치하려는 지원되는 패브릭 버전을 선택할 수 있습니다.

포털에서 "upgradeMode" 클러스터를 설정하거나 라이브 클러스터 생성 시 또는 나중에 Resource Manager를 사용하여 이 작업을 수행합니다. 

> [!NOTE]
> 클러스터에서 지원되는 패브릭 버전이 항상 실행되도록 해야 합니다. 새로운 버전의 서비스 패브릭 릴리스를 발표하면 이전 버전은 해당 날짜부터 최소 60일 후 지원 종료되는 것으로 표시됩니다. 새로운 릴리스는 [서비스 패브릭 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)에서 발표됩니다. 그러면 새로운 릴리스를 선택할 수 있습니다. 
> 
> 

클러스터가 실행되는 릴리스가 만료되기 14일 전에, 클러스터를 경고 성능 상태로 전환하는 상태 이벤트가 생성됩니다. 지원되는 패브릭 버전으로 업그레이드할 때까지 클러스터는 경고 상태로 유지됩니다.

### <a name="setting-the-upgrade-mode-via-portal"></a>포털을 통해 업그레이드 모드 설정
클러스터를 만들 때 클러스터를 자동 또는 수동으로 설정할 수 있습니다.

![Create_Manualmode][Create_Manualmode]

라이브 클러스터인 경우 관리 환경을 사용하여 클러스터를 자동 또는 수동으로 설정할 수 있습니다. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>포털을 통해 수동 모드로 설정된 클러스터에서 새 버전으로 업그레이드
새 버전으로 업그레이드하려면 드롭다운 목록에서 사용 가능한 버전을 선택하고 저장하기만 하면 됩니다. 패브릭 업그레이드는 자동으로 시작됩니다. 클러스터 상태 정책(노드 상태 및 클러스터에서 실행 중인 모든 응용 프로그램의 상태 조합)은 업그레이드의 기간을 준수합니다.

클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 이 문서를 아래로 스크롤하여 사용자 지정 상태 정책을 설정하는 방법에 대해 자세히 알아보세요. 

롤백을 일으킨 문제를 수정했으면 이전과 동일한 단계에 따라 업그레이드를 다시 시작해야 합니다.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Resource Manager 템플릿을 통해 업그레이드 모드 설정
아래 표시된 것처럼 "upgradeMode" 구성을 Microsoft.ServiceFabric/clusters 리소스 정의에 추가하고 "clusterCodeVersion"을 지원되는 패브릭 버전 중 하나로 설정한 후 템플릿을 배포합니다. "upgradeMode"에 대해 유효한 값은 "Manual" 또는 "Automatic"입니다.

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Resource Manager 템플릿을 통해 수동 모드로 설정된 클러스터에서 새 버전으로 업그레이드
클러스터가 수동 모드인 경우 새 버전으로 업그레이드하려면 "clusterCodeVersion"을 지원되는 버전으로 변경하고 배포합니다. 템플릿의 배포 시 패브릭 업그레이드는 자동으로 시작됩니다. 클러스터 상태 정책(노드 상태 및 클러스터에서 실행 중인 모든 응용 프로그램의 상태 조합)은 업그레이드의 기간을 준수합니다.

클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 이 문서를 아래로 스크롤하여 사용자 지정 상태 정책을 설정하는 방법에 대해 자세히 알아보세요. 

롤백을 일으킨 문제를 수정했으면 이전과 동일한 단계에 따라 업그레이드를 다시 시작해야 합니다.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>지정된 구독의 모든 환경에 대해 사용 가능한 모든 버전 목록 가져오기
다음 명령을 실행하면 다음과 유사한 결과가 표시됩니다.

"supportExpiryUtc"는 지정된 릴리스가 만료되거나 이미 만료되었음을 알려 줍니다. 최신 릴리스는 유효한 날짜를 포함하지 않으며 "9999-12-31T23:59:59.9999999" 값을 포함합니다. 이는 만료 날짜가 아직 설정되지 않음을 의미합니다.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>클러스터 업그레이드 모드가 자동인 경우 패브릭 업그레이드 동작
Microsoft는 Azure 클러스터에서 실행하는 패브릭 코드 및 구성을 유지 관리합니다. 필요한 기준으로 소프트웨어에 자동 모니터링된 업그레이드를 수행합니다. 이러한 업그레이드는 코드, 구성 또는 둘 모두가 될 수 있습니다. 응용 프로그램이 이러한 업그레이드로 인해 영향이 없거나 최소한의 영향이 있는지 확인하기 위해 다음 단계로 업그레이드를 수행합니다.

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1단계: 모든 클러스터 상태 정책을 사용하여 업그레이드 수행
이 단계 동안 업그레이드는 한 번에 하나의 업그레이드 도메인을 진행하고 클러스터에서 실행 중이던 응용 프로그램은 가동 중지 시간 없이 계속 실행합니다. 클러스터 상태 정책(노드 상태 및 클러스터에서 실행 중인 모든 응용 프로그램의 상태 조합)은 업그레이드의 기간을 준수합니다.

클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 그런 다음 구독 소유자에게 전자 메일이 전송됩니다. 전자 메일에는 다음 정보가 포함되어 있습니다.

* 클러스터 업그레이드를 롤백해야 했다는 알림.
* 권장 수정 작업(있는 경우).
* 2단계를 실행할 때까지 일 수(n)

인프라 이유로 업그레이드가 실패한 경우 동일한 업그레이드를 몇 번 더 실행하기 위해 노력합니다. 전자 메일이 전송된 날짜에서 n일 후 2단계로 진행합니다.

클러스터 상태 정책이 충족하는 경우 업그레이드가 성공한 것으로 간주하고 완료로 표시됩니다. 이는 초기 업그레이드 또는 이 단계의 업그레이드 다시 실행 중 발생할 수 있습니다. 성공적 실행에 대한 전자 메일 확인은 없습니다. 너무 많은 전자 메일을 전송하지 않도록 하며 전자 메일 수신은 정상에 대한 예외로 보여야 합니다. 클러스터 업그레이드가 응용 프로그램 가용성에 영향을 주지 않고 성공되기를 예상합니다.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2단계: 기본 상태 정책만을 사용하여 업그레이드 수행
이 단계의 상태 정책은 업그레이드 시작 시 정상 상태가 업그레이드 프로세스의 기간 동안 동일하게 유지되는 응용 프로그램의 수 방식으로 설정됩니다. 1단계와 마찬가지로 2단계 업그레이드는 한 번에 하나의 업그레이드 도메인을 진행하고 클러스터에서 실행 중이던 응용 프로그램은 가동 중지 시간 없이 계속 실행합니다. 클러스터 상태 정책(노드 상태 및 클러스터에서 실행 중인 모든 응용 프로그램의 상태 조합)은 업그레이드의 기간을 준수합니다.

적용되는 클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 그런 다음 구독 소유자에게 전자 메일이 전송됩니다. 전자 메일에는 다음 정보가 포함되어 있습니다.

* 클러스터 업그레이드를 롤백해야 했다는 알림.
* 권장 수정 작업(있는 경우).
* 3단계를 실행할 때까지 일 수(n)

인프라 이유로 업그레이드가 실패한 경우 동일한 업그레이드를 몇 번 더 실행하기 위해 노력합니다. 미리 알림 전자 메일은 n일이 끝나기 몇 일 전에 전송됩니다. 전자 메일이 전송된 날짜에서 n일 후 3단계로 진행합니다. 2단계에서 전송한 전자 메일은 심각하게 받아들여져야 하며 수정 작업이 수행되어야 합니다.

클러스터 상태 정책이 충족하는 경우 업그레이드가 성공한 것으로 간주하고 완료로 표시됩니다. 이는 초기 업그레이드 또는 이 단계의 업그레이드 다시 실행 중 발생할 수 있습니다. 성공적 실행에 대한 전자 메일 확인은 없습니다.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3단계: 까다로운 상태 정책을 사용하여 업그레이드 수행
이 단계의 이러한 상태 정책은 응용 프로그램의 상태보다 업그레이드 완료에 맞춰집니다. 이 단계에서 매우 적은 클러스터 업그레이드가 끝납니다. 클러스터가 이 단계에 도달하는 경우 응용 프로그램이 비정상이 되고/되거나 가용성이 손실될 수 있습니다.

다른 두 단계와 마찬가지로 3단계 업그레이드는 한 번에 하나의 업그레이드 도메인을 진행합니다.

클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 인프라 이유로 업그레이드가 실패한 경우 동일한 업그레이드를 몇 번 더 실행하기 위해 노력합니다. 그런 다음 지원 및/또는 업그레이드를 더 이상 받지 않도록 클러스터가 고정됩니다.

이 정보가 있는 메일이 수정 작업과 함께 구독 소유자에게 전송됩니다. 3단계가 실패된 상태로 클러스터가 도달하지 않을 것입니다.

클러스터 상태 정책이 충족하는 경우 업그레이드가 성공한 것으로 간주하고 완료로 표시됩니다. 이는 초기 업그레이드 또는 이 단계의 업그레이드 다시 실행 중 발생할 수 있습니다. 성공적 실행에 대한 전자 메일 확인은 없습니다.

## <a name="cluster-configurations-that-you-control"></a>사용자가 제어하는 클러스터 구성
클러스터 업그레이드 모드를 설정하는 기능 외에도 라이브 클러스터에서 변경할 수 있는 구성은 다음과 같습니다.

### <a name="certificates"></a>인증서
포털을 통해 클러스터 및 클라이언트에 대한 인증서를 쉽게 새로 추가하거나 삭제할 수 있습니다. [자세한 지침은 이 문서](service-fabric-cluster-security-update-certs-azure.md)

![Azure 포털의 인증서 지문을 보여 주는 스크린샷][CertificateUpgrade]

### <a name="application-ports"></a>응용 프로그램 포트
노드 유형에 연결된 부하 분산 장치 리소스 속성을 변경하여 응용 프로그램 포트를 변경할 수 있습니다. 포털을 사용하거나 리소스 관리자 PowerShell을 직접 사용할 수 있습니다.

노드 유형에서 모든 VM의 새 포트를 열려면 다음을 수행합니다.

1. 적절한 부하 분산 장치에 새 프로브를 추가합니다.
   
    포털을 사용하여 클러스터를 배포한 경우 부하 분산 장치는 각 노드 형식에 대해 "LB-name of the Resource group-NodeTypename"으로 이름이 지정됩니다. 부하 분산 장치 이름은 리소스 그룹에만 고유하므로 특정 리소스 그룹 아래에 대해 검색하는 것이 가장 좋습니다.
   
    ![포털에서 부하 분산 장치에 프로브 추가를 보여 주는 스크린샷][AddingProbes]
2. 부하 분산 장치에 새 규칙을 추가합니다.
   
    이전 단계에서 만든 프로브를 사용하여 동일한 부하 분산 장치에 새 규칙을 추가합니다.
   
    ![포털에서 부하 분산 장치에 새 규칙 추가.][AddingLBRules]

### <a name="placement-properties"></a>배치 속성
각 노드 유형의 경우 사용하려는 사용자 지정 배치 속성을 응용 프로그램에 추가할 수 있습니다. NodeType은 명시적으로 추가하지 않고 사용할 수 있는 기본 속성입니다.

> [!NOTE]
> 배치 제약 조건과 노드 속성 사용 및 정의 방법에 대한 자세한 내용은 서비스 패브릭 클러스터 리소스 관리자 문서의 [클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md)에 있는 "배치 제약 조건 및 노드 속성" 섹션을 참조하세요.
> 
> 

### <a name="capacity-metrics"></a>용량 메트릭
각 노드 유형의 경우 부하를 보고하도록 사용하려는 사용자 용량 메트릭을 응용 프로그램에 추가할 수 있습니다. 부하를 보고하는 용량 메트릭 사용에 대한 자세한 내용은 서비스 패브릭 클러스터 리소스 관리자 설명서에서 [클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md) 및 [메트릭 및 부하](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.

### <a name="fabric-upgrade-settings---health-polices"></a>패브릭 업그레이드 설정 - 상태 정책
패브릭 업그레이드에 대한 사용자 지정 상태 정책을 지정할 수 있습니다. 클러스터를 자동 패브릭 업그레이드로 설정한 경우 이러한 정책은 자동 패브릭 업그레이드의 1단계에 적용됩니다.
클러스터를 수동 패브릭 업그레이드로 설정한 경우 이러한 정책은 새 버전을 선택할 때마다 적용되며 그러면 시스템이 클러스터에서 패브릭 업그레이드를 시작하도록 트리거링합니다. 정책을 재정의하지 않으면 기본값이 사용됩니다.

사용자 지정 상태 정책을 지정하거나 "패브릭 업그레이드" 블레이드 아래에서 고급 업그레이드 설정을 선택하여 현재 설정을 검토할 수 있습니다. 다음 그림은 이 방법을 보여줍니다. 

![사용자 지정 상태 정책 관리][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>클러스터에 대한 패브릭 설정 사용자 지정
설정에 대한 내용과 설정을 사용자 지정하는 방법은 [서비스 패브릭 클러스터 패브릭 설정](service-fabric-cluster-fabric-settings.md) 을 참조하세요.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>클러스터를 구성하는 VM의 OS 패치
[패치 오케스트레이션 응용 프로그램](service-fabric-patch-orchestration-application.md)을 참조하세요. 그러면 오케스트레이션된 방식으로 Windows 업데이트에서 패치를 설치하도록 클러스터에 배포할 수 있으므로 서비스를 항상 사용할 수 있도록 유지합니다. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>클러스터를 구성하는 VM의 OS 업그레이드
클러스터의 가상 머신의 OS 이미지를 업그레이드해야 하는 경우 한 번에 하나의 VM에 이 작업을 수행하고 이 업그레이드에 대한 책임을 져야 합니다. 현재 자동화 기능은 없습니다.

## <a name="next-steps"></a>다음 단계
* [서비스 패브릭 클러스터 패브릭 설정](service-fabric-cluster-fabric-settings.md)
* [클러스터를 확장 및 축소하는](service-fabric-cluster-scale-up-down.md)
* [응용 프로그램 업그레이드](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
