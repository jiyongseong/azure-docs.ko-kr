---
title: Azure Cost Management에 대한 질문과 대답 | Microsoft Docs
description: Azure Cost Management에 대한 일반적인 질문에 대한 답변을 제공합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: troubleshooting
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 01d880a668140b5a7ffcff8947ccc6083bca7ea0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302742"
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Azure Cost Management에 대한 질문과 대답

이 문서에서는 Azure Cost Management(Cloudyn이라고도 함)에 대한 몇 가지 일반적인 질문을 다룹니다. Cost Management에 대한 질문이 있는 경우 [FAQs for Azure Cost Management](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn)(Azure Cost Management에 대한 질문과 대답)에서 질문할 수 있습니다.

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>간접 기업의 일반적인 설정 문제를 해결하는 방법은 무엇일까요?

Cloudyn 포털을 처음 사용할 때 기업 계약 또는 CSP(클라우드 솔루션 공급자) 사용자의 경우 다음과 같은 메시지가 나타날 수 있습니다.

- “지정된 API 키가 최상위 수준 등록 키가 아닙니다”가 **Azure Cost Management 설정** 마법사에 표시됨
- “직접 등록 – 아니요”가 기업 계약 포털에 표시됨
- “지난 30일 간의 사용 데이터가 없습니다. 배포자에게 Azure 계정에 대해 표시를 사용하도록 설정했는지 문의하세요”가 Cloudyn 포털에 표시

앞의 메시지는 재판매인 또는 CSP를 통해 Azure 기업계약을 구입했음을 나타냅니다. Cloudyn에서 데이터를 볼 수 있도록 재판매인 또는 CSP가 Azure 계정에 대해 _표시_를 사용하도록 설정해야 합니다.

다음은 문제를 해결하는 방법입니다.

1. 재판매인은 계정에 대해 _표시_를 사용하도록 설정해야 합니다. 지침은 [Indirect Customer Onboarding Guide](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide)(간접 고객 온보딩 가이드)를 참조하세요.

2. Cloudyn에 사용할 기업 계약 키를 생성합니다. 지침은 [Azure EA 추가](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) 또는 [EA 등록 ID 및 API 키를 찾는 방법](https://youtu.be/u_phLs_udig)을 참조하세요.

Azure 서비스 관리자만 Cost Management를 사용하도록 설정할 수 있습니다. 공동 관리자 권한이 충분하지 않습니다.

Cloudyn을 설정하기 위해 Azure Enterprise Agreement API 키를 생성하려면 먼저 다음 지침에 따라 Azure Billing API를 사용하도록 설정해야 합니다.

- [기업 고객을 위한 보고 API 개요](../billing/billing-enterprise-api.md)
- **API에 대한 데이터 액세스 사용**의 [Microsoft Azure Enterprise Portal 보고 API](https://ea.azure.com/helpdocs/reportingAPI)


부서 관리자, 계정 소유자 및 엔터프라이즈 관리자에게 청구 API로 _요금 보기_ 권한을 부여해야 할 수도 있습니다.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Optimizer 권장 사항 표시되지 않는 이유

권장 사항 정보는 활성화된 계정에만 사용할 수 있습니다. 다음을 포함하여, *활성화되지 않은* 계정에 대해서는 **Optimizer** 보고서 카테고리에 권장 사항 정보가 표시되지 않습니다.

- Optimization Manager
- 크기 조정 최적화
- 비효율성

Optimizer 권장 사항 데이터가 표시되지 않는다면 계정이 비활성 상태일 가능성이 가장 높습니다. 계정을 활성화하려면 Azure 자격 증명을 사용하여 등록해야 합니다.

계정을 활성화하려면

1.  Cloudyn 포털의 오른쪽 위에서 **설정**을 클릭하고 **클라우드 계정**을 선택합니다.
2.  Microsoft Azure 계정 탭에서 **활성화되지 않은** 구독이 있는 계정을 찾습니다.
3.  활성화되지 않은 계정의 오른쪽에서 연필 모양인 **편집** 기호를 클릭합니다.
4.  테넌트 ID와 등급 ID가 자동으로 검색됩니다. **다음**을 클릭합니다.
5.  Azure Portal로 리디렉션됩니다. 포털에 로그인하고 Cloudyn Collector에게 Azure 데이터 액세스 권한을 부여합니다.
6.  다음으로 Cloudyn 계정 관리 페이지로 리디렉션되며 해당 구독이 **활성** 계정 상태로 업데이트됩니다. 녹색 확인 표시가 기호가 나타납니다.
7.  하나 이상의 구독에 대해 녹색 확인 표시가 나타나지 않는다면 해당 구독에 대해 판독기 앱(CloudynCollector)을 만들 권한이 없는 것입니다. 구독에 대해 더 높은 권한이 있는 사용자가 3, 4단계를 반복해야 합니다.  

이전 단계를 완료한 후 하루 이틀 안에 Optimizer 권장 사항을 볼 수 있습니다. 그러나 전체 최적화 데이터를 사용할 수 있으려면 최대 5일까지 소요됩니다.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>일시 중단되거나 잠긴 사용자를 활성화하려면 어떻게 할까요?

사용자에 대한 액세스를 허용하라는 경고 요청을 받으면 사용자 계정을 활성화해야 합니다.

사용자 계정을 활성화하려면:

1. Cloudyn을 설정하는 데 사용한 Azure 관리자 사용자 계정을 사용하여 Cloudyn에 로그인합니다. 또는 관리자 액세스 권한이 부여된 사용자 계정으로 로그인합니다.

2. 오른쪽 위의 기어 기호를 선택하고 **사용자 관리**를 선택합니다.

3. 사용자를 찾아 연필 기호를 선택한 다음 사용자를 편집합니다.

4. **사용자 상태**에서 상태를 **일시 중단됨**에서 **활성**으로 변경합니다.

Cloudyn 사용자 계정은 Azure에서 Single Sign-On을 사용하여 연결합니다. 사용자가 암호를 잘못 입력하면 Azure에 계속 액세스할 수는 있지만 Cloudyn에 잠겨 있을 수 있습니다.

Cloudyn의 메일 주소를 Azure의 기본 주소에서 변경하면 계정이 잠길 수 있습니다. “상태가 초기에 일시 중단됨”이 표시될 수 있습니다. 사용자 계정이 잠긴 경우 대체 관리자에게 문의하여 계정을 재설정합니다.

계정 중 하나가 잠기는 경우를 대비하여 적어도 두 개의 Cloudyn 관리자 계정을 만드는 것이 좋습니다.

Cloudyn 포털에 로그인할 수 없는 경우 올바른 Azure Cost Management URL을 사용하여 Cloudyn 포털에 로그인했는지 확인합니다. [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade)를 사용합니다.

Cloudyn 직접 URL https://app.cloudyn.com은 사용하지 마세요.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Azure 자격 증명으로 활성화되지 않은 계정을 활성화하는 방법

Cloudyn에서 Azure 계정을 발견하면 즉시 비용 기반 보고서에 비용 데이터가 제공됩니다. 그러나 Cloudyn이 사용 및 성능 데이터를 제공하려면 Azure 자격 증명을 계정에 등록해야 합니다. 지침은 [Azure Resource Manager 추가](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager)를 참조하세요.

계정에 Azure 자격 증명을 추가하려면 Cloudyn 포털에서 구독이 아닌 계정 이름 오른쪽에 있는 편집 기호를 선택합니다.

Azure 자격 증명이 Cloudyn에 추가될 때까지 계정은 _비활성화_로 표시됩니다.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>기존 구독에 여러 계정 및 엔터티를 추가하려면 어떻게 할까요?

추가 엔터티는 Cloudyn 구독에 기업계약을 추가하는 데 사용됩니다. 다음 링크는 엔터티를 추가하는 방법을 설명합니다.

- [Adding an Entity](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity)(엔터티 추가) 문서
- [Defining your hierarchy with Cost Entities](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities)(비용 엔터티를 사용하여 계층 구조 정의) 비디오

CSP의 경우:

엔터티에 CSP 계정을 추가하려면 새 엔터티를 만들 때 **엔터프라이즈** 대신 **MSP 액세스**를 선택합니다. 계정이 기업계약으로 등록되어 있고 CSP 자격 증명을 추가하려는 경우 Cloudyn 지원 담당자가 계정 설정을 수정해야 할 수 있습니다. 유료 Azure 구독자인 경우 Azure Portal에서 새 지원 요청을 만들 수 있습니다. **도움말 + 지원**을 선택한 다음 **새 지원 요청**을 선택합니다.

## <a name="currency-symbols-in-cloudyn-reports"></a>Cloudyn 보고서의 통화 기호

서로 다른 통화를 사용하는 여러 Azure 계좌가 있을 수 있습니다. 그러나 Cloudyn의 비용 보고서는 보고서당 하나 이상의 통화 형식을 표시하지 않습니다.

다른 통화를 사용하는 구독이 여러 개인 경우 부모 엔터티 및 자식 엔터티 통화가 USD **$** 로 표시됩니다. 동일한 엔터티 계층 구조에서 다른 통화를 사용하지 않는 것이 좋습니다. 즉 엔터티 구조로 구성된 모든 구독은 동일한 통화를 사용해야 합니다.

Cloudyn에서는 기업 계약 구독 통화를 자동으로 검색하여 보고서에 올바르게 표시합니다.  그러나 CSP 및 Web-Direct Azure 계정의 경우 Cloudyn에 USD **$** 만 표시됩니다.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Cloudyn 데이터 새로 고침 타임라인은 무엇인가요?

Cloudyn에는 다음과 같은 데이터 새로 고침 타임라인이 있습니다.

- **초기**: 설정 후 Cloudyn에서 비용 데이터를 보려면 최대 24시간이 걸릴 수 있습니다. 또한 Cloudyn이 크기 조정 권장 사항을 표시하기 위해 충분한 데이터를 수집하는 데 최대 10일이 소요될 수 있습니다.
- **매일**: 매월 10일부터 말일까지 Cloudyn은 다음 날 약 UTC+3 이후에 이전 날짜의 데이터를 최신으로 표시합니다.
- **매월**: 매월 1일부터 10일 사이에 Cloudyn은 지난 달 말까지의 데이터만 표시할 수 있습니다.

Cloudyn은 이전 날짜의 전체 데이터를 사용할 수 있을 때 이전 날짜 데이터를 처리합니다. 이전 날짜의 데이터는 일반적으로 매일 약 UTC+3까지 Cloudyn에서 사용할 수 있습니다. 태그와 같은 일부 데이터는 처리하는 데 24시간이 추가로 걸릴 수 있습니다.

이번 달의 데이터는 매월 초에 수집할 수 없습니다. 이 기간 동안 서비스 공급자는 지난 달 요금을 확정합니다. 이전 달의 데이터는 매월 초 이후 5일에서 10일 사이에 Cloudyn에 표시됩니다. 이 기간 동안에는 이전 달의 분할 상환된 비용만 표시될 수 있습니다. 일일 청구 또는 사용 데이터는 표시되지 않을 수 있습니다. 데이터가 사용 가능해지면 Cloudyn에서 이를 소급하여 처리합니다. 처리가 끝나면 모든 월별 데이터가 매월 5일에서 10일 사이에 표시됩니다.

Azure에서 Cloudyn으로 데이터를 보내는 데 지연이 발생하면 데이터가 계속 Azure에 기록됩니다. 연결이 복원되면 데이터가 Cloudyn으로 전송됩니다.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>직접 CSP가 간접 CSP 고객 또는 파트너를 위해 Cloudyn 액세스를 구성하는 방법은 무엇인가요?

지침은 [Cloudyn에서 간접 CSP 액세스 구성](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn)을 참조하세요.

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>최적화 프로그램 메뉴 항목이 나타나는 이유는 무엇입니까?

Azure Resource Manager 액세스를 추가하고 데이터를 수집한 후에는 **최적화 프로그램** 옵션이 표시됩니다. Azure Resource Manager 액세스를 활성화하려면 [Azure 자격 증명으로 활성화되지 않은 계정을 활성화하는 방법](#how-do-i-activate-unactivated-accounts-with-azure-credentials)을 참조하세요.

## <a name="is-cost-managementcloudyn-agent-based"></a>Cost Management/Cloudyn 에이전트 기반인가요?

번호 에이전트는 사용되지 않습니다. VM에 대한 Azure 가상 머신 메트릭 데이터는 Microsoft Insights API에서 수집합니다. Azure VM에서 메트릭 데이터를 수집하려면 VM의 진단 설정을 켜야 합니다.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Cloudyn 보고서는 보고서당 두 개 이상의 AD 테넌트를 표시합니까?

예. 보유한 각 AD 테넌트에 대해 [해당 클라우드 계정 엔터티를 만들 수 있습니다](tutorial-user-access.md#create-and-manage-entities). 그런 다음 Amazon Web Services 및 Google Cloud Platform을 포함하여 모든 Azure AD 테넌트 데이터와 기타 클라우드 플랫폼 공급자를 볼 수 있습니다.
