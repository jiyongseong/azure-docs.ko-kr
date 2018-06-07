---
title: AD FS와 함께 Azure AD Connect Health 사용 | Microsoft Docs
description: 온-프레미스 AD FS 인프라를 모니터링하는 방법에 대한 Azure AD Connect Health 페이지입니다.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2018
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b17b4e8581daa5b19aaafd911765d843a9f3fe4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Azure AD Connect Health를 사용하여 AD FS 모니터링
다음 문서는 AZure AD Connect Health와 함께 AD FS 인프라 모니터링에 중점을 둡니다. Azure AD Connect Health와 함께 Azure AD Connect (동기화)를 모니터링하는 방법에 대한 정보는 [동기화를 위해 Azure AD Connect Health 사용](active-directory-aadconnect-health-sync.md)을 참조하세요. 또한 Azure AD Connect Health와 함께 Active Directory Domain Services를 모니터링하는 방법에 대한 정보는 [AD DS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adds.md)을 참조하세요.

## <a name="alerts-for-ad-fs"></a>AD FS의 경고
Azure AD Connect Health 경고 섹션은 활성 경고 목록을 제공합니다. 각 경고에는 관련 정보, 해결 단계 및 관련된 설명서 링크가 포함됩니다.

활성 또는 해결된 경고를 두 번 클릭하면 추가 정보, 경고를 해결하기 위해 취할 수 있는 단계와 적절한 설명서 링크가 포함된 새 블레이드가 표시됩니다. 과거에 해결된 경고에 대한 기록 데이터도 볼 수 있습니다.

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>AD FS의 사용량 분석
Azure AD Connect Health 사용 현황 분석에서는 페더레이션 서버의 인증 트래픽을 분석합니다. 사용 현황 분석 상자를 두 번 클릭하면, 몇 가지 메트릭 및 그룹화를 보여주는 사용 현황 분석 블레이드가 열립니다.

> [!NOTE]
> AD FS와 사용 현황 분석을 사용하려면 AD FS 감사가 사용하도록 설정되어 있어야 합니다. 자세한 내용은 [AD FS 감사 사용](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs)을 참조하십시오.
>
>

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health/report1.png)

추가 메트릭을 선택하거나, 시간 범위를 지정하거나, 그룹화를 변경하려면 사용 현황 분석 차트를 마우스 오른쪽 단추로 클릭하고 차트 편집을 선택합니다. 그런 다음 시간 범위를 지정하고 다른 메트릭을 선택하고 그룹화를 변경할 수 있습니다. 다음 섹션에 설명된 다양한 “메트릭”을 기반으로 인증 트래픽 분산을 살펴보고 적절한 “그룹화 기준” 매개 변수를 사용하여 각 메트릭을 그룹화할 수 있습니다.

**메트릭: 총 요청 수** - AD FS 서버에서 처리한 총 요청 수.

|그룹화 기준 | 그룹화의 의미 및 그룹화가 유용한 이유 |
| --- | --- |
| 모두 | 모든 AD FS 서버에서 처리한 총 요청 수를 보여 줍니다.|
| 응용 프로그램 | 대상 신뢰 당사자를 기반으로 전체 요청을 그룹화합니다. 이 그룹화는 전체 트래픽 중 응용 프로그램이 수신하는 트래픽의 비율을 이해하는 데 유용합니다. |
|  서버 |요청을 처리한 서버를 기반으로 전체 요청을 그룹화합니다. 이 그룹화는 전체 트래픽의 부하 분포를 이해하는 데 유용합니다.
| 작업 공간 연결 |작업 공간이 연결된(알려진) 장치의 요청인지 여부를 기반으로 전체 요청을 그룹화합니다. 이 그룹화는 ID 인프라에 알려지지 않은 장치를 사용하여 리소스에 액세스하는 경우를 이해하는 데 유용합니다. |
|  인증 방법 | 인증에 사용된 인증 방법을 기반으로 전체 요청을 그룹화합니다. 이 그룹화는 인증에 사용되는 일반적인 인증 방법을 이해하는 데 유용합니다. 다음은 가능한 인증 방법입니다. <ol> <li>Windows 통합 인증(Windows)</li> <li>폼 기반 인증(양식)</li> <li>SSO(Single Sign On)</li> <li>X509 인증서 인증(인증서)</li> <br>페더레이션 서버가 SSO 쿠키를 사용하 여 요청을 수신하는 경우 해당 요청은 SSO(Single Sign On)로 계산됩니다. 이 경우 쿠키가 유효하면 사용자는 자격 증명을 입력할 필요 없이 효율적으로 응용 프로그램에 액세스할 수 있습니다. 페더레이션 서버에서 여러 신뢰 당사자를 보호하는 경우 일반적으로 사용되는 동작입니다. |
| 네트워크 위치 | 사용자의 네트워크 위치를 기반으로 전체 요청을 그룹화합니다. 네트워크 위치는 인트라넷 또는 엑스트라넷이 될 수 있습니다. 이 그룹화는 인트라넷 트래픽과 엑스트라넷 트래픽의 비율을 파악하는 데 유용합니다. |


**메트릭: 실패한 총 요청 수** - 페더레이션 서비스에서 처리에 실패한 총 요청 수. (이 메트릭은 Windows Server 2012 R2용 AD FS에서만 사용할 수 있습니다.)

|그룹화 기준 | 그룹화의 의미 및 그룹화가 유용한 이유 |
| --- | --- |
| 오류 유형 | 미리 정의된 오류 유형을 기반으로 오류의 수를 표시합니다. 이 그룹화는 오류의 일반적인 유형을 이해하는 데 유용합니다. <ul><li>잘못된 사용자 이름 또는 암호: 잘못된 사용자 이름 또는 암호로 인해 발생하는 오류입니다.</li> <li>"엑스트라넷 잠금": 엑스트라넷에서 잠근 사용자로부터 받은 요청으로 인해 발생하는 오류입니다. </li><li> "암호 만료": 만료된 암호를 사용하여 로그인하는 사용자로 인해 발생하는 오류입니다.</li><li>"계정 사용 안 함": 비활성화된 계정으로 로그인하는 사용자로 인해 발생하는 오류입니다.</li><li>"장치 인증": 장치 인증을 사용하여 인증에 실패하는 사용자로 인해 발생하는 오류입니다.</li><li>"사용자 인증서 인증": 잘못된 인증서로 인해 인증에 실패할 사용자로 인해 발생하는 오류입니다.</li><li>"MFA": Multi-Factor Authentication을 사용하여 인증에 실패하는 사용자로 인해 발생하는 오류입니다.</li><li>"다른 자격 증명": "발급 권한 부여": 인증 실패로 인해 발생하는 오류입니다.</li><li>"발급 위임": 발급 위임 오류로 인해 발생하는 오류입니다.</li><li>"승인 토큰": 타사 ID 공급자로부터 토큰을 거부하는 ADFS로 인해 발생하는 오류입니다.</li><li>"Protocol": 프로토콜 오류로 인해 발생하는 오류입니다.</li><li>"알 수 없음": 모두 Catch합니다. 정의된 카테고리에 맞지 않는 다른 모든 오류입니다.</li> |
| 서버 | 서버를 기반으로 오류를 그룹화합니다. 이 그룹화는 서버 전반의 오류 분포를 파악하는 데 유용합니다. 분포가 균일하지 않으면 특정 서버에 문제가 있음을 나타낼 수 있습니다. |
| 네트워크 위치 | 요청의 네트워크 위치(인트라넷 및 엑스트라넷)를 기반으로 오류를 그룹화합니다. 이 그룹화는 실패하는 요청의 유형을 파악하는 데 유용합니다. |
|  적용 | 대상 응용 프로그램(신뢰 당사자)을 기반으로 오류를 그룹화합니다. 이 그룹화는 가장 많은 수의 오류가 발생하는 대상 응용 프로그램을 파악하는 데 유용합니다. |

**메트릭: 사용자 수** - 적극적으로 AD FS를 사용하여 인증하는 평균 고유 사용자 수

|그룹화 기준 | 그룹화의 의미 및 그룹화가 유용한 이유 |
| --- | --- |
|모두 |이 메트릭은 선택한 시간 조각에서 페더레이션 서비스를 사용하는 평균 사용자 수를 제공합니다. 사용자가 그룹화되지 않습니다. <br>평균은 선택한 시간 조각에 따라 달라집니다. |
| 응용 프로그램 |대상 응용 프로그램(신뢰 당사자)을 기반으로 평균 사용자 수를 그룹화합니다. 이 그룹화는 특정 응용 프로그램을 사용하는 사용자의 수를 파악하는 데 유용합니다. |

## <a name="performance-monitoring-for-ad-fs"></a>AD FS의 모니터링 성능
Azure AD Connect Health 성능 모니터링은 메트릭에 대한 모니터링 정보를 제공합니다. 모니터링 상자를 선택하면 메트릭에 대한 자세한 정보가 포함된 새 블레이드가 열립니다.

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health/perf1.png)

블레이드 맨 위의 옵션을 선택하여 서버별로 필터링하면 개별 서버의 메트릭을 확인할 수 있습니다. 메트릭을 변경하려면 모니터링 블레이드에서 모니터링 차트를 마우스 오른쪽 단추로 클릭하고 차트 편집을 선택합니다(또는 차트 편집 단추를 선택). 그런 다음 열리는 새 블레이드의 드롭다운에서 추가 메트릭을 선택하여 성능 데이터를 볼 시간 범위를 지정할 수 있습니다.

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>사용자 이름/암호 로그인이 실패한 상위 사용자 50명
AD FS 서버에서 인증 요청이 실패하는 일반적인 이유 중 하나는 잘못된 자격 증명, 다시 말해서 사용자 이름 또는 암호가 올바르지 않은 것입니다. 일반적으로 복잡한 암호, 잊어버린 암호 또는 오타로 인해 사용자에게 발생합니다.

하지만, 응용 프로그램이 사용자 자격 증명을 캐시했는데 자격 증명이 만료되었거나 일련의 잘 알려진 암호를 사용하여 계정에 로그인을 시도하는 악의적인 사용자와 같은 다른 이유로 인해 AD FS 서버에서 처리되는 예상치 못한 요청 수가 발생할 수 있습니다. 이러한 두 가지 예는 요청의 급증을 유발할 수 있는 유효한 원인입니다.

Azure AD Connect Health for ADFS는 사용자 이름 또는 암호가 잘못되어 로그인 시도가 실패한 상위 사용자 50명에 대한 보고서를 제공합니다. 이 보고서는 팜의 모든 AD FS 서버에서 생성된 감사 이벤트를 처리하여 얻을 수 있습니다.

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health-adfs/report1a.png)

이 보고서 내에서 다음 정보에 간편하게 액세스할 수 있습니다.

* 지난 30일 동안 잘못된 사용자 이름/암호를 사용하여 실패한 요청 수
* 잘못된 사용자 이름/암호로 로그인하여 실패한 일별 평균 사용자 수

이 부분을 클릭하면 추가 세부 정보를 제공하는 주 보고서 블레이드로 이동합니다. 이 블레이드는 잘못된 사용자 이름이나 암호를 사용하는 요청에 대한 기준을 설정하는 데 도움이 되는 추세 정보가 있는 그래프를 포함합니다. 또한 지난 한 주 동안 실패한 시도 횟수가 포함된 상위 50명의 사용자 목록을 제공합니다. 지난 주 상위 50명의 사용자가 잘못된 암호 스파이크를 식별하는 데 도움이 될 수 있습니다.  

이 그래프는 다음 정보를 제공합니다.

* 잘못된 사용자 이름/암호로 인해 실패한 일별 총 로그인 수
* 로그인에 실패한 일별 총 고유 사용자 수
* 마지막 요청에 대한 클라이언트 IP 주소

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health-adfs/report3a.png)

이 보고서는 다음 정보를 제공합니다.

| 보고서 항목 | 설명 |
| --- | --- |
| 사용자 ID |사용된 사용자 ID를 표시합니다. 이것은 사용자가 입력한 값이며, 잘못된 사용자 ID가 사용되는 경우도 있습니다. |
| 실패한 시도 |특정 사용자 ID에 대한 총 실패 횟수를 보여 줍니다. 테이블은 가장 높은 실패 횟수부터 내림차순으로 정렬됩니다. |
| 마지막 실패 |마지막 실패가 발생한 타임 스탬프를 보여 줍니다. |
| 마지막 실패 IP |최신 잘못된 요청에서 클라이언트 IP 주소를 표시합니다. 이 값에 둘 이상의 IP 주소가 표시되면 사용자의 마지막 시도 요청 IP와 함께 포워드 클라이언트 IP가 포함될 수 있습니다.  |

> [!NOTE]
> 이 보고서는 12시간마다 자동으로 업데이트되어 해당 시간 내에 수집된 새 정보가 포함됩니다. 따라서 마지막 12시간 내에 발생하는 로그인 시도가 보고서에 포함되지 않을 수 있습니다.
>
>

## <a name="risky-ip-report-public-preview"></a>위험 IP 보고서(공개 미리 보기)
AD FS 고객은 암호 인증 엔드포인트를 인터넷에 공개하여 최종 사용자가 Office 365와 같은 SaaS 응용 프로그램에 액세스할 수 있게 하는 인증 서비스를 제공할 수 있습니다. 이 경우 악의적 행위자가 AD FS 시스템에 대한 로그인을 시도하여 사용자의 암호를 추측하고 종료하고 응용 프로그램 리소스에 액세스할 수 있습니다. Windows Server 2012 R2의 AD FS 이후부터 AD FS에서는 이러한 유형의 공격을 방지하기 위해 엑스트라넷 계정 잠금 기능을 제공합니다. 더 낮은 버전을 사용하는 경우 AD FS 시스템을 Windows Server 2016으로 업그레이드하는 것이 좋습니다. <br />
또한 단일 IP 주소에서 여러 사용자에 대해 여러 로그인을 시도할 수도 있습니다. 이러한 경우 사용자당 시도 횟수가 AD FS의 계정 잠금 보호 임계값보다 낮을 수 있습니다. Azure AD Connect Health는 이제 이 조건을 감지하고 해당 문제가 발생하면 관리자에게 알리는 "위험한 IP 보고서"를 제공합니다. 이 보고서의 주요 이점은 다음과 같습니다. 
- 실패한 암호 기반 로그인의 임계값을 초과하는 IP 주소 검색
- 암호가 잘못되었거나 엑스트라넷 잠금 상태로 인해 실패한 로그인 지원
- 사용자 지정 가능한 이메일 설정을 사용하여 문제가 발생하는 즉시 관리자에게 경고하는 이메일 알림
- 조직의 보안 정책과 일치하는 사용자 지정 가능한 임계값 설정
- 오프라인 분석을 위한 다운로드 가능한 보고서 및 자동화를 통한 다른 시스템과의 통합

> [!NOTE]
> 이 보고서를 사용하려면 AD FS 감사를 사용하도록 설정해야 합니다. 자세한 내용은 [AD FS 감사 사용](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs)을 참조하십시오. <br />
> 미리 보기에 액세스하려면 전역 관리자 또는 [보안 읽기 권한자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader) 권한이 필요합니다.  
> 

### <a name="what-is-in-the-report"></a>보고서에 포함된 내용
위험한 IP 보고서의 각 항목에는 지정된 임계값을 초과하는 실패한 AD FS 로그인 활동에 대한 집계 정보가 표시됩니다. 제공되는 정보는 다음 과 같습니다. ![Azure AD Connect Health Portal 포털](./media/active-directory-aadconnect-health-adfs/report4a.png)

| 보고서 항목 | 설명 |
| ------- | ----------- |
| 타임스탬프 | 탐지 시간 범위가 시작되면 Azure Portal 현지 시간 기준의 타임스탬프가 표시됩니다.<br /> 모든 일별 이벤트는 자정 UTC 시간에 생성됩니다. <br />시간별 이벤트의 타임스탬프는 시간의 시작 시점으로 반올림됩니다. 첫 번째 활동 시작 시간은 내보낸 파일의 "firstAuditTimestamp"에서 찾을 수 있습니다. |
| 트리거 형식 | 탐지 시간 범위 형식이 표시됩니다. 집계 트리거 형식은 시간 또는 일 단위입니다. 이렇게 하면 하루 동안에 걸쳐 분산된 시도 횟수에서 자주 발생한 무차별 암호 대입 공격 및 느린 공격을 탐지하는 데 도움이 될 수 있습니다. |
| IP 주소 | 잘못된 암호 또는 엑스트라넷 잠금 로그인 활동이 있는 위험한 단일 IP 주소입니다. IPv4 또는 IPv6 주소일 수 있습니다. |
| 잘못된 암호 오류 수 | 탐지 시간 범위 동안 IP 주소에서 잘못된 암호 오류가 발생했습니다. 잘못된 암호 오류는 특정 사용자에게 여러 번 발생할 수 있습니다. 만료된 암호로 인해 실패한 시도는 포함되지 않습니다. |
| 엑스트라넷 잠금 오류 수 | 탐지 시간 범위 동안 IP 주소에서 엑스트라넷 잠금 오류가 발생했습니다. 엑스트라넷 잠금 오류는 특정 사용자에게 여러 번 발생할 수 있습니다. 이 정보는 엑스트라넷 잠금이 AD FS(2012R2 버전 이상)로 구성된 경우에만 표시됩니다. <b>참고</b> 암호를 사용하는 엑스트라넷 로그인을 허용하는 경우 이 기능을 설정하는 것이 좋습니다. |
| 시도한 고유 사용자 | 탐지 시간 범위 동안 IP 주소에서 시도한 고유 사용자 계정의 수입니다. 이 정보는 단일 사용자 공격 패턴 및 다중 사용자 공격 패턴을 구분하는 메커니즘을 제공합니다.  |

예를 들어 아래의 보고서 항목은 <i>104.2XX.2XX.9</i> IP 주소에서 2018년 2월 28일 오후 6 시부터 오후 7시까지 잘못된 암호 오류는 없지만 284개의 엑스트라넷 잠금 오류가 있었다고 나타냅니다. 조건 내에서 14명의 고유 사용자가 영향을 받았습니다. 활동 이벤트가 지정된 보고서 시간당 임계값을 초과했습니다. 

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health-adfs/report4b.png)

> [!NOTE]
> - 지정된 임계값을 초과하는 활동만 보고서 목록에 표시됩니다. 
> - 이 보고서는 최대 30일까지 다시 추적할 수 있습니다.
> - 이 경고 보고서에는 Exchange IP 주소 또는 개인 IP 주소가 표시되지 않습니다. 이러한 주소는 여전히 내보내기 목록에 포함되어 있습니다. 
>

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health-adfs/report4c.png)

### <a name="load-balancer-ip-addresses-in-the-list"></a>목록의 부하 분산 장치 IP 주소
부하 분산 장치는 실패한 로그인 작업을 집계하여 경고 임계값에 도달합니다. 부하 분산 장치 IP 주소가 표시되는 경우 웹 응용 프로그램 프록시 서버에 요청을 전달할 때 외부 부하 분산 장치에서 클라이언트 IP 주소를 보내지 않을 가능성이 큽니다. 포워드 클라이언트 IP 주소를 전달하도록 올바르게 부하 분산 장치를 구성하세요. 

### <a name="download-risky-ip-report"></a>위험한 IP 보고서 다운로드 
**다운로드** 기능을 사용하면 지난 30일 동안의 위험한 IP 주소 목록 전체를 Connect Health 포털에서 내보낼 수 있습니다. 내보내기 결과에는 각 탐지 시간 범위에서 실패한 AD FS 로그인 활동이 모두 포함되므로 내보낸 후에 필터링을 사용자 지정할 수 있습니다. 포털에서 강조 표시된 집계 외에도 내보내기 결과에는 실패한 로그인 활동에 대해 IP 주소별로 자세한 정보가 표시됩니다.

|  보고서 항목  |  설명  | 
| ------- | ----------- | 
| firstAuditTimestamp | 탐지 시간 범위 동안 실패한 활동이 시작된 첫 번째 타임스탬프를 표시합니다.  | 
| lastAuditTimestamp | 탐지 시간 범위 동안 실패한 활동이 종료된 마지막 타임스탬프를 표시합니다.  | 
| attemptCountThresholdIsExceeded | 현재 활동이 경고 임계값을 초과하는 경우의 플래그입니다.  | 
| isWhitelistedIpAddress | 경고 및 보고에서 IP 주소가 필터링된 경우의 플래그입니다. 개인 IP 주소(<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) 및 Exchange IP 주소는 필터링되고 True로 표시됩니다. 개인 IP 주소 범위가 표시되는 경우 웹 응용 프로그램 프록시 서버에 요청을 전달할 때 외부 부하 분산 장치에서 클라이언트 IP 주소를 보내지 않을 가능성이 큽니다.  | 

### <a name="configure-notification-settings"></a>알림 설정 구성
보고서의 관리자 연락처는 **알림 설정**을 통해 업데이트할 수 있습니다. 기본적으로 위험한 IP 경고 이메일 알림은 해제 상태입니다. "실패한 활동 임계값 보고서를 초과하는 IP 주소에 대한 이메일 알림 받기" 아래의 단추를 설정/해제하여 알림을 사용하도록 설정할 수 있습니다. Connect Health의 일반 경고 알림 설정과 마찬가지로 여기서는 위험한 IP 보고서에 대한 지정된 알림 받는 사람 목록을 사용자 지정할 수 있습니다. 또한 변경하는 동안 모든 전역 관리자에게 알릴 수도 있습니다. 

### <a name="configure-threshold-settings"></a>임계값 설정 구성
알림 임계값은 임계값 설정을 통해 업데이트할 수 있습니다. 먼저 임계값은 기본적으로 시스템에 설정되어 있습니다. 위험한 IP 보고서 임계값 설정에는 다음 네 가지 범주가 있습니다.

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health-adfs/report4d.png)

| 임계값 항목 | 설명 |
| --- | --- |
| (잘못된 U/P 수 + 엑스트라넷 잠금 수) / 일  | **일** 단위의 잘못된 암호 수와 엑스트라넷 잠금 수의 합계가 이 값을 초과하는 경우 활동을 보고하고 경고 알림을 트리거하는 임계값 설정 |
| (잘못된 U/P 수 + 엑스트라넷 잠금 수) / 시간 | **시간** 단위의 잘못된 암호 수와 엑스트라넷 잠금 수의 합계가 이 값을 초과하는 경우 활동을 보고하고 경고 알림을 트리거하는 임계값 설정 |
| 엑스트라넷 잠금 수 / 일 | **일** 단위의 엑스트라넷 잠금 수가 이 값을 초과하는 경우 활동을 보고하고 경고 알림을 트리거하는 임계값 설정 |
| 엑스트라넷 잠금 수 / 시간| **시간** 단위의 엑스트라넷 잠금 수가 이 값을 초과하는 경우 활동을 보고하고 경고 알림을 트리거하는 임계값 설정 |

> [!NOTE]
> - 보고서 임계값 변경은 설정 변경 1시간 후에 적용됩니다. 
> - 보고된 기존 항목은 임계값 변경의 영향을 받지 않습니다. 
> - 환경 내에서 표시되는 이벤트 수를 분석하여 임계값을 적절히 조정하는 것이 좋습니다. 
>
>

### <a name="faq"></a>FAQ
1. 보고서에 개인 IP 주소 범위가 표시되는 이유는 무엇인가요?  <br />
개인 IP 주소(<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) 및 Exchange IP 주소는 IP 허용 목록에서 필터링되고 True로 표시됩니다. 개인 IP 주소 범위가 표시되는 경우 웹 응용 프로그램 프록시 서버에 요청을 전달할 때 외부 부하 분산 장치에서 클라이언트 IP 주소를 보내지 않을 가능성이 큽니다.

2. 보고서에 부하 분산 장치 IP 주소가 표시되는 이유는 무엇인가요?  <br />
부하 분산 장치 IP 주소가 표시되는 경우 웹 응용 프로그램 프록시 서버에 요청을 전달할 때 외부 부하 분산 장치에서 클라이언트 IP 주소를 보내지 않을 가능성이 큽니다. 포워드 클라이언트 IP 주소를 전달하도록 올바르게 부하 분산 장치를 구성하세요. 

3. IP 주소를 차단하려면 어떻게 해야 할까요?  <br />
식별된 악성 IP 주소는 방화벽에 추가되거나 Exchange에서 차단해야 합니다.   <br />

4. 이 보고서에 항목이 표시되지 않는 이유는 무엇인가요? <br />
   - 실패한 로그인 활동이 임계값 설정을 초과하지 않습니다. 
   - AD FS 서버 목록에서 "상태 서비스가 최신이 아닙니다" 경고가 활성화되어 있지 않은지 확인합니다.  [이 경고 문제를 해결하는 방법](active-directory-aadconnect-health-data-freshness.md)에 대해 자세히 알아보세요.
   - AD FS 팜에서는 감사를 사용할 수 없습니다.
 
5. 보고서에 액세스할 수 없는 이유는 무엇인가요?  <br />
전역 관리자 또는 [보안 읽기 권한자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader) 권한이 필요합니다. 전역 관리자에게 문의하여 액세스 권한을 얻으세요.


## <a name="related-links"></a>관련 링크
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health Agent 설치](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 작업](active-directory-aadconnect-health-operations.md)
* [동기화에 대한 Azure AD Connect Health 사용](active-directory-aadconnect-health-sync.md)
* [AD DS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 버전 내역](active-directory-aadconnect-health-version-history.md)
