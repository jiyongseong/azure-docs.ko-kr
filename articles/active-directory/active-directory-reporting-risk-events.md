---
title: Azure Active Directory 위험 이벤트 | Microsoft Docs
description: 이 문서에서는 위험 이벤트의 자세한 개요를 제공합니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 보안, 위험, 위험 이벤트, 취약점, 보안 정책
author: MarkusVi
manager: mtillman
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e883caa63bde26e13234dde949ce4517b328e3a5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195321"
---
# <a name="azure-active-directory-risk-events"></a>Azure Active Directory 위험 이벤트

대부분의 보안 침해는 공격자가 사용자의 ID를 도용하여 환경에 대한 액세스 권한을 얻을 때 발생합니다. 손상된 ID를 검색하는 것은 쉬운 작업이 아닙니다. Azure Active Directory는 적응 기계 학습 알고리즘 및 추론을 사용하여 사용자의 계정과 관련된 의심스러운 작업을 검색합니다. 검색된 각 의심스러운 동작은 *위험 이벤트*라는 레코드에 저장됩니다.

현재, Azure Active Directory는 6가지 유형의 위험 이벤트를 검색합니다.

- [자격 증명이 손실된 사용자](#leaked-credentials) 
- [익명 IP 주소에서 로그인](#sign-ins-from-anonymous-ip-addresses) 
- [비정상적 위치로 불가능한 이동](#impossible-travel-to-atypical-locations) 
- [감염된 장치에서 로그인](#sign-ins-from-infected-devices) 
- [의심스러운 작업이 있는 IP 주소에서 로그인](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [알 수 없는 위치에서 로그인](#sign-in-from-unfamiliar-locations) 


![위험 이벤트](./media/active-directory-reporting-risk-events/91.png)

감지된 위험 이벤트에 대해 얻은 정보는 Azure AD 구독에 연결됩니다. Azure AD Premium P2 버전에서 모든 기본 감지에 대한 가장 자세한 정보를 가져옵니다. Azure AD Premium P1 버전에서 라이선스에서 다루지 않는 감지는 **추가 위험이 있는 로그인이 감지됨** 위험 이벤트로 표시됩니다.


이 문서에서는 위험 이벤트의 자세한 개요와 이를 사용하여 Azure AD ID를 보호하는 방법을 제공합니다.


## <a name="risk-event-types"></a>위험 이벤트 유형

위험 이벤트 유형 속성은 위험 이벤트 레코드가 만들어진 의심스러운 동작의 식별자입니다.

검색 프로세스에 대한 Microsoft의 지속적인 투자는 다음과 같은 결과를 가져왔습니다.

- 기존 위험 이벤트의 검색 정확도 향상 
- 향후 추가될 새 위험 이벤트 유형

### <a name="leaked-credentials"></a>유출된 자격 증명

사이버 범죄자가 합법적인 사용자의 유효한 암호를 손상시키는 경우 범죄자는 종종 이러한 자격 증명을 공유합니다. 보통 이러한 작업은 Dark 웹 또는 붙여넣기 사이트에 공개적으로 게시하거나 암시장에서 자격 증명을 거래 또는 판매하는 방식으로 이루어집니다. Microsoft 유출된 자격 증명 서비스는 공개 및 Dark 웹 사이트를 모니터링하고 다음 대상과 협력하여 사용자 이름/암호 쌍을 획득합니다.

- 연구원
- 사법 기관
- Microsoft 보안 팀
- 신뢰할 수 있는 기타 소스 

서비스에서 사용자 이름/암호 쌍을 획득하면 AAD에 대해 사용자의 현재 유효한 자격 증명인지 확인합니다. 일치하면 사용자의 암호가 손상된 것이며 *누출된 자격 증명 위험 이벤트*가 만들어집니다.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>익명 IP 주소에서 로그인

이 위험 이벤트 유형은 익명 프록시 IP 주소로 식별된 IP 주소에서 시도하여 성공적으로 로그인한 사용자를 식별합니다. 이 프록시는 해당 장치의 IP 주소를 숨기려는 사용자가 사용하며 악의적인 의도로 사용될 수 있습니다.


### <a name="impossible-travel-to-atypical-locations"></a>비정상적 위치로 불가능한 이동

이 위험 이벤트 유형은 지역적으로 떨어진 위치에서 시작한 두 번의 로그인을 식별합니다. 과거 동작을 고려하면 이 위치 중 하나 이상이 사용자에 대해 불규칙적입니다. 다른 요인 중에서 이 Machine Learning 알고리즘은 두 번의 로그인 간 시간과 사용자가 첫 번째 위치에서 두 번째 위치로 이동하는 데 걸리는 시간을 고려하여 서로 다른 사용자가 동일한 자격 증명을 사용하고 있음을 나타냅니다.

이 알고리즘은 조직의 다른 사용자가 정기적으로 사용하는 VPN 및 위치와 같은 불가능한 이동 조건에 영향을 주는 확실한 "가양성"을 무시합니다. 시스템에는 새 사용자의 로그인 동작을 알아보는 동안 14일의 초기 학습 기간이 있습니다. 

### <a name="sign-in-from-unfamiliar-locations"></a>잘 모르는 위치에서 로그인

이 위험 이벤트 유형은 새로운/알 수 없는 위치를 확인하기 위해 과거 로그인 위치(IP, 위도/경도 및 ASN)를 고려합니다. 시스템은 사용자가 사용한 이전 위치에 대한 정보를 저장하고 이러한 "익숙한" 위치를 고려합니다. 로그인이 익숙한 위치 목록에 없는 위치에서 발생하는 경우 위험 이벤트가 트리거됩니다. 시스템에는 새로운 위치를 알 수 없는 위치의 플래그로 지정하지 않는 30일의 초기 학습 기간이 있습니다. 또한 시스템은 익숙한 장치 및 익숙한 위치에 지리적으로 가까운 위치에서 시도한 로그인을 무시합니다. 

ID 보호는 기본 인증/레거시 프로토콜에 대한 일반적이지 않은 위치에서의 로그인을 검색합니다. 이러한 프로토콜에는 클라이언트 ID와 같은 친숙한 현대적인 기능이 없기 때문에 거짓 긍정을 줄일 만큼 원격 분석이 충분하지 않습니다. 검색된 위험 이벤트 수를 줄이려면 최신 인증으로 이동해야 합니다.   

### <a name="sign-ins-from-infected-devices"></a>감염된 장치에서 로그인

이 위험 이벤트 유형은 적극적으로 봇 서버와 통신한다고 알려진 맬웨어로 감염된 장치에서 시도한 로그인을 식별합니다. 봇 서버와 접촉했던 IP 주소에 대해 사용자의 장치의 IP 주소를 상호 연결하여 결정됩니다. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>의심스러운 작업이 있는 IP 주소에서 로그인
이 위험 이벤트 유형은 짧은 기간 동안에 여러 사용자 계정에서 실패한 로그인 시도가 많이 확인되는 IP 주소를 식별합니다. 이는 공격자가 사용하는 IP 주소의 트래픽 패턴과 일치하며 계정이 이미 또는 손상되었거나 손상될 것이라는 확실한 지표입니다. 조직의 다른 사용자가 정기적으로 사용하는 IP 주소와 같은 명백한 "*가양성*"을 무시하는 기계 학습 알고리즘입니다.  시스템에는 새 사용자 및 새 테넌트의 로그인 동작을 알아보는 14일의 초기 학습 기간이 있습니다.


## <a name="detection-type"></a>검색 유형

검색 유형 속성은 위험 이벤트의 검색 기간에 대한 지표입니다(실시간 또는 오프라인). 현재 대부분의 위험 이벤트는 위험 이벤트가 발생되고 사후 처리 작업에서 오프라인으로 검색됩니다.

다음 표에는 검색 유형이 관련 보고서에 표시되는 데 걸리는 시간이 나와 있습니다.

| 검색 유형 | 보고 대기 시간 |
| --- | --- |
| 실시간 | 5~10분 |
| 오프라인 | 2~4시간 |


Azure Active Directory가 검색하는 위험 이벤트 유형의 경우 검색 유형은 다음과 같습니다.

| 위험 이벤트 유형 | 검색 유형 |
| :-- | --- | 
| [자격 증명이 손실된 사용자](#leaked-credentials) | 오프라인 |
| [익명 IP 주소에서 로그인](#sign-ins-from-anonymous-ip-addresses) | 실시간 |
| [비정상적 위치로 불가능한 이동](#impossible-travel-to-atypical-locations) | 오프라인 |
| [알 수 없는 위치에서 로그인](#sign-in-from-unfamiliar-locations) | 실시간 |
| [감염된 장치에서 로그인](#sign-ins-from-infected-devices) | 오프라인 |
| [의심스러운 작업이 있는 IP 주소에서 로그인](#sign-ins-from-ip-addresses-with-suspicious-activity) | 오프라인|


## <a name="risk-level"></a>위험 수준

위험 이벤트의 위험 수준 속성은 위험 이벤트의 심각도 및 신뢰도에 대한 지표입니다(높음, 보통 또는 낮음). 이 속성은 수행해야 하는 작업의 우선 순위를 지정하는 데 도움이 됩니다. 

위험 이벤트의 심각도는 신호의 강도를 ID 손상의 예측 변수로 나타냅니다. 신뢰도는 가양성의 가능성에 대한 표시기입니다. 

예를 들면 다음과 같습니다. 

* **높음**: 신뢰도 및 심각도가 높은 위험 이벤트입니다. 이러한 이벤트는 사용자의 ID가 손상되었음을 나타내는 확실한 지표이며 영향을 받는 모든 사용자는 즉시 수정되어야 합니다.

* **보통**: 심각도가 높지만 신뢰도가 낮은 위험 이벤트 또는 그 반대의 경우입니다. 이러한 이벤트는 잠재적으로 위험하며 영향을 받는 사용자 계정은 수정되어야 합니다.

* **낮음**: 신뢰도 및 심각도가 낮은 위험 이벤트입니다. 이 이벤트는 즉각적인 조치가 필요하지 않을 수 있지만 다른 위험 이벤트와 결합되면 ID가 손상되었음을 확실하게 표시할 수 있습니다.

![위험 수준](./media/active-directory-reporting-risk-events/01.png)

### <a name="leaked-credentials"></a>유출된 자격 증명

사용자 이름 및 암호를 공격자가 사용할 수 있다고 명백히 표시하기 때문에 유출된 자격 증명 위험 이벤트는 **높음**으로 분류됩니다.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>익명 IP 주소에서 로그인

익명 IP 주소는 계정 손상을 확실히 표시하지 않기 때문에 이 위험 이벤트 유형의 위험 수준은 **보통**입니다. 사용자에게 즉시 문의하여 익명 IP 주소를 사용했는지를 확인하는 것이 좋습니다.


### <a name="impossible-travel-to-atypical-locations"></a>비정상적 위치로 불가능한 이동

불가능한 이동은 일반적으로 해커가 성공적으로 로그인할 수 있는 훌륭한 지표입니다. 그러나 사용자가 새 장치를 사용하거나 조직의 다른 사용자가 일반적으로 사용하지 않는 VPN을 사용하여 이동하는 경우 가양성이 발생할 수 있습니다. 가양성의 다른 원본은 서버 IP 및 클라이언트 IP를 정확하지 않게 전달하는 응용 프로그램이며 응용 프로그램의 백 엔드가 호스팅되는 데이터 센터에서 발생하는 로그인의 모양을 가져올 수 있습니다(대개 이러한 Microsoft 데이터 센터는 Microsoft에서 소유한 고유의 IP 주소를 발생시키는 로그인의 모양을 제공할 수 있음). 이러한 가양성의 결과로 이 위험 이벤트에 대한 위험 수준은 **보통**입니다.

> [!TIP]
> [명명된 위치](active-directory-named-locations.md)를 구성하여 이 위험 이벤트 형식에 대해 보고된 가양성의 양을 줄일 수 있습니다. 

### <a name="sign-in-from-unfamiliar-locations"></a>잘 모르는 위치에서 로그인

알 수 없는 위치는 공격자가 도난당한 ID를 사용할 수 있다는 확실한 표시를 제공할 수 있습니다. 가양성은 사용자가 이동하거나, 새 장치를 사용하거나, 새 VPN을 사용할 때 발생할 수 있습니다. 이러한 가양성의 결과로 이 이벤트 유형의 위험 수준은 **보통**입니다.

### <a name="sign-ins-from-infected-devices"></a>감염된 장치에서 로그인

이 위험 이벤트는 사용자 장치가 아닌 IP 주소를 식별합니다. 여러 장치에서 단일 IP 주소를 지지하고 일부만 봇 네트워크에서 제어하는 경우 다른 장치에서 시도한 로그인은 이 이벤트를 불필요하게 트리거할 수 있습니다. 이러한 이유로 해당 위험 이벤트를 **낮음**으로 분류합니다.  

사용자에게 연락하고 모든 사용자 장치를 검사하는 것이 좋습니다. 사용자의 개인 장치가 감염되었거나 이전에 언급했듯이 사용자 이외의 다른 사람이 사용자와 동일한 IP 주소에서 감염된 장치를 사용했을 가능성이 있습니다. 감염된 장치는 종종 바이러스 백신 소프트웨어로 아직 식별되지 않는 맬웨어로 감염됩니다. 또한 장치에 감염을 일으킬 수 있는 잘못된 사용자 습관을 나타낼 수 있습니다.

맬웨어 감염을 해결하는 방법에 대한 자세한 내용은 [맬웨어 보호 센터](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409)(영문)를 참조하세요.


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>의심스러운 작업이 있는 IP 주소에서 로그인

사용자에게 문의하여 의심스럽다고 표시된 IP 주소에서 실제로 로그인했는지를 확인하는 것이 좋습니다. 일부가 의심스러운 작업에 책임이 있을 수 있는 반면 동일한 IP 주소에 여러 장치가 있을 수 있으므로 이 이벤트 유형의 위험 수준은 "**보통**"입니다. 


 
## <a name="next-steps"></a>다음 단계

위험 이벤트는 Azure AD의 ID 보호에 대한 기반입니다. Azure AD는 현재 여섯 가지 위험 이벤트를 감지할 수 있습니다. 


| 위험 이벤트 유형 | 위험 수준 | 검색 유형 |
| :-- | --- | --- |
| [자격 증명이 손실된 사용자](#leaked-credentials) | 높음 | 오프라인 |
| [익명 IP 주소에서 로그인](#sign-ins-from-anonymous-ip-addresses) | 중간 | 실시간 |
| [비정상적 위치로 불가능한 이동](#impossible-travel-to-atypical-locations) | 중간 | 오프라인 |
| [알 수 없는 위치에서 로그인](#sign-in-from-unfamiliar-locations) | 중간 | 실시간 |
| [감염된 장치에서 로그인](#sign-ins-from-infected-devices) | 낮음 | 오프라인 |
| [의심스러운 작업이 있는 IP 주소에서 로그인](#sign-ins-from-ip-addresses-with-suspicious-activity) | 중간 | 오프라인|

사용자 환경에서 검색된 위험 이벤트는 어디에서 확인할 수 있습니까?
두 곳에서 보고된 위험 이벤트를 검토할 수 있습니다.

 - **Azure AD 보고** - 위험 이벤트는 Azure AD의 보안 보고서의 일부입니다. 자세한 내용은 [위험에 노출된 사용자 보안 보고서](active-directory-reporting-security-user-at-risk.md) 및 [위험한 로그인 보안 보고서](active-directory-reporting-security-risky-sign-ins.md)를 참조하세요.

 - **Azure AD ID 보호** - 위험 이벤트는 [Azure Active Directory ID 보호](active-directory-identityprotection.md) 보고 기능의 일부입니다.
    

위험 이벤트의 감지는 ID 보호의 중요한 측면을 나타내는 반면 수동으로 해결하거나 조건부 액세스 정책을 구성하여 자동화된 응답을 구현하는 옵션도 있습니다. 자세한 내용은 [Azure Active Directory Identity Protection](active-directory-identityprotection.md)을 참조하세요.
 
