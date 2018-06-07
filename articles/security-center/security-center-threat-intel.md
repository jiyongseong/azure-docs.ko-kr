---
title: Azure Security Center의 위협 인텔리전스 | Microsoft Docs
description: Azure Security Center에서 위협 인텔리전스 기능을 사용하여 VM 및 컴퓨터에서 잠재적 위협을 식별하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 9654c25bf6b51d48dc195d6a536b61312fdcbeff
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="threat-intelligence-in-azure-security-center"></a>Azure Security Center의 위협 인텔리전스
이 문서를 통해 Azure Security Center 위협 인텔리전스를 사용하여 보안 관련 문제를 해결할 수 있습니다.

## <a name="what-is-threat-intelligence"></a>위협 인텔리전스란 무엇입니까?
Security Center에서 사용할 수 있는 위협 인텔리전스 옵션을 사용하여 IT 관리자는 환경에 대한 보안 위협을 식별할 수 있습니다. 예를 들어 특정 컴퓨터가 봇네트의 일부인지 여부를 식별할 수 있습니다. 공격자가 컴퓨터를 명령 및 컨트롤에 몰래 연결하는 맬웨어를 불법으로 설치할 경우 컴퓨터가 봇네트의 노드가 될 수 있습니다. 또한 위협 인텔리전스는 다크 웹과 같은 지하 통신 채널에서 오는 잠재적 위협도 식별할 수 있습니다.

Security Center는 이러한 위협 인텔리전스를 구축하기 위해 Microsoft 내의 여러 소스에서 가져온 데이터를 사용합니다. Security Center는 이 데이터를 사용하여 사용자 환경에 대한 잠재적 위협을 식별합니다. **위협 인텔리전스** 창은 세 가지 기본 옵션으로 구성되어 있습니다.

- 검색된 위협 유형
- 위협 원본
- 위협 인텔리전스 맵


## <a name="when-should-you-use-threat-intelligence"></a>언제 위협 인텔리전스를 사용해야 해야 합니까?
[보안 인시던트 대응 프로세스](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) 단계 중 하나는 손상된 시스템의 심각도를 식별하는 것입니다. 이 단계에서는 다음 작업을 수행해야 합니다.

- 공격 특성 확인
- 공격 원점 확인
- 공격 의도 확인 특정 정보를 얻기 위해 사용자 조직을 노린 공격입니까, 아니면 무작위 공격입니까?
- 손상된 시스템 식별
- 액세스된 파일 식별 및 해당 파일의 중요도 확인

Security Center에서 위협 인텔리전스 정보를 사용하여 이러한 작업을 수행할 수 있습니다. 

## <a name="access-the-threat-intelligence"></a>위협 인텔리전스에 액세스
사용자 환경에 대한 현재 위협 인텔리전스를 시각화하려면 먼저 정보가 상주하는 작업 영역을 선택해야 합니다. 여러 작업 영역이 없는 경우 작업 영역 선택기를 무시하여 **위협 인텔리전스** 대시보드로 바로 이동합니다. 대시보드에 액세스하려면:

1. **Security Center** 대시보드를 엽니다.

2. 왼쪽 창에 있는 **검색** 아래에 있는 **위협 인텔리전스**를 선택합니다. **위협 인텔리전스** 대시보드가 나타납니다.

    ![위협 인텔리전스 대시보드](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > 맨 오른쪽의 열이 **업그레이드 계획**을 표시하면 이 작업 영역은 체험용 구독을 사용하는 중입니다. 표준으로 업그레이드하여 이 기능을 사용합니다. 맨 오른쪽의 열이 **업데이트 필요**를 표시하면 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)를 업데이트하여 이 기능을 사용합니다. 가격 계획에 대한 자세한 내용은 Azure Security Center 가격 책정을 참조하세요. 
    > 
3. 둘 이상의 작업 영역을 조사할 경우 **악성 IP** 열에 따라 조사의 우선 순위를 지정합니다. 이 작업 영역에서 현재 악성 IP 수를 보여줍니다. 사용하려는 작업 영역을 선택하면 **위협 인텔리전스** 대시보드가 나타납니다.

    ![위협 인텔리전스 정보](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. 대시보드는 네 개의 타일로 구성됩니다.

    a.  **위협 형식** 선택한 작업 영역에서 검색된 위협의 유형을 요약합니다.

    나.  **국적** 해당 원본 위치에 따라 트래픽 양을 집계합니다.

    다.  **위협 위치** 사용자 환경과 통신하는 전 세계의 현재 위치를 식별할 수 있습니다. 표시된 맵에서 주황색(들어옴)과 빨간색(보냄) 화살표는 트래픽 방향을 식별합니다. 이러한 화살표 중 하나를 선택하는 경우 위협의 유형 및 트래픽 방향이 나타납니다.

    d.  **위협 세부 정보** 맵에서 선택된 위협에 대한 세부 정보를 표시합니다.

선택한 옵션 타일에 관계 없이 표시되는 대시보드는 로그 검색 쿼리를 기반으로 합니다. 유일한 차이점은 쿼리 및 결과의 유형입니다.

### <a name="threat-types"></a>위협 유형
**위협 형식** 타일을 선택하여 **로그 검색** 대시보드를 엽니다. 필터 옵션이 왼쪽에 표시되고 쿼리 결과는 오른쪽에 표시됩니다.

![로그 검색](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

쿼리 결과는 이름을 기준으로 위협을 표시합니다. 왼쪽 창을 사용하여 필터링하려는 특성을 선택할 수 있습니다. 예를 들어 현재 컴퓨터에 연결되어 있는 위협만을 확인하려면 **SESSIONSTATE**에서 **연결됨** > **적용**을 선택합니다.

![세션 상태](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Azure VM의 경우 에이전트를 통해 흐르는 네트워크 데이터만이 **위협 인텔리전스** 대시보드에 표시됩니다. 다음 데이터 형식은 위협 인텔리전스에서도 사용됩니다.

- CEF 데이터(Type=CommonSecurityLog)
- WireData(Type= WireData)
- IIS 로그(Type=W3CIISLog)
- Windows 방화벽(Type=WindowsFirewall)
- DNS 이벤트(Type=DnsEvents)


## <a name="see-also"></a>참고 항목
이 문서에서는 의심스러운 활동을 식별하기 위해 Security Center에서 위협 인텔리전스를 사용하는 방법을 살펴보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에서 보안 경고 관리 및 응답](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다. 
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답에 대한 답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

