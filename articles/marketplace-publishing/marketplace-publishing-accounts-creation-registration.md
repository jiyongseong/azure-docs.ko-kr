---
title: 게시자 계정 만들기 및 등록 | Microsoft Docs
description: Microsoft 개발자 계정을 만들고 승인 시 Azure Marketplace에서 다양한 제품 유형을 판매할 수 있는 지침입니다.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: mbaldwin
ms.openlocfilehash: 69da37cbf5691fe94b9550200e259711e9e201d4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-microsoft-developer-account"></a>Microsoft 개발자 계정 만들기
이 문서에서는 Azure Marketplace에서 승인된 Microsoft 개발자가 되기 위해 필요한 계정 만들기 및 등록 과정을 안내합니다.

## <a name="1-create-a-microsoft-account"></a>1. Microsoft 계정 만들기
게시 프로세스를 시작하려면 Microsoft 계정을 만들어야 합니다. 이 계정은 **Microsoft 개발자 센터** 및 **Azure 게시 포털**에 등록하는 데 사용됩니다. Azure Marketplace 제품에 대해 Microsoft 계정이 하나만 있으면 됩니다. 서비스 또는 제품에 한정될 필요가 없습니다.

사용자 이름을 구성하는 주소는 귀사의 도메인에 속하고 귀사의 IT 팀에서 제어해야 합니다. 모든 게시 관련 작업은 이 계정을 통해 수행되어야 합니다.

> [!WARNING]
> **"Azure"** 및 **"Microsoft"** 와 같은 단어는 Microsoft 계정 등록에 대해 지원되지 않습니다. 계정 만들기 및 등록 프로세스를 완료하려면 이러한 단어를 사용하지 마십시오.
>
>

### <a name="guidelines-for-company-accounts"></a>회사 계정에 대한 지침
회사 계정을 만들 때 둘 이상의 사용자가 계정을 연 Microsoft 계정으로 로그인하여 계정에 액세스해야 하는 경우 다음 지침을 따릅니다.

> [!Important]
> 중요 여러 사용자가 개발자 센터 계정에 액세스할 수 있게 하려면 Azure Active Directory를 사용하여 개인 Azure AD 자격 증명으로 로그인하여 계정에 액세스할 수 있는 개별 사용자에게 역할을 할당하는 것이 좋습니다. 자세한 내용은 [계정 사용자 관리](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users)를 참조하세요.

* 회사 도메인에 속하지만 단일 개인이 아닌 전자 메일 주소(예: windowsapps@fabrikam.com)를 사용하여 Microsoft 계정을 만듭니다.
* 이 Microsoft 계정에 대한 액세스를 최대한 적은 수의 개발자로 제한합니다.
* 개발자 계정에 액세스해야 하는 모든 사람을 포함하는 회사 전자 메일 배포 목록을 설정하고 보안 정보에 이 전자 메일 주소를 추가합니다. 이렇게 하면 목록에 있는 모든 직원이 필요할 때 보안 코드를 받고 Microsoft 계정의 보안 정보를 관리할 수 있습니다. 배포 목록을 설정할 수 없는 경우 개인 전자 메일 계정 소유자는 메시지가 표시되면(예: 새 보안 정보가 계정에 추가되거나 새 장치에서 액세스해야 하는 경우) 보안 코드에 액세스하여 공유할 수 있어야 합니다.
* 확장할 필요가 없고 핵심 팀 구성원에 액세스할 수 있는 회사 전화 번호를 추가합니다.
* 일반적으로 개발자가 신뢰할 수 있는 장치를 사용하여 회사의 개발자 계정에 로그인하도록 합니다. 모든 핵심 팀 구성원은 이러한 신뢰할 수 있는 장치에 액세스할 수 있어야 합니다. 이렇게 하면 계정에 액세스할 때 보안 코드를 전송할 필요성이 줄어듭니다.
* 신뢰할 수 없는 PC에서 계정에 액세스할 수 있도록 해야 하는 경우 해당 액세스를 최대 5명의 개발자로 제한합니다. 이상적으로는 이러한 개발자가 동일한 지리적 네트워크 위치를 공유하는 컴퓨터에서 계정에 액세스해야 합니다.
* [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage)에서 회사의 보안 정보를 자주 검토하여 모두 최신 정보인지 확인합니다.

개발자 계정은 주로 신뢰할 수 있는 PC에서 액세스해야 합니다. 이는 주당 계정별로 생성되는 코드 수에 제한이 있기 때문에 중요합니다. 또한 가장 원활한 로그인 환경을 가능하게 합니다.

추가 개발자 계정 지침 및 보안에 대한 자세한 내용을 보려면 [여기](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts)를 클릭하세요.

### <a name="instructions"></a>지침
1. 새 Chrome Incognito 또는 Internet Explorer InPrivate 브라우징 세션을 열어 기존 계정에 로그인되지 않았는지 확인합니다.
2. [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx) 링크를 사용하여 이메일(예: 위의 지침에 따라 windowsapp@fabrikam.com)을 Microsoft 계정으로 등록합니다. 아래의 지침을 따르세요.

   1. 계정을 Microsoft 계정으로 등록하는 동안 시스템에서 문자 메시지 또는 자동 응답 통화 형태로 계정 확인 코드를 보낼 올바른 전화번호를 제공해야 합니다.
   2. 계정을 Microsoft 계정으로 등록하는 동안 계정 확인을 위해 자동 전자 메일을 수신하기 위한 올바른 전자 메일 ID를 제공해야 합니다.
3. DL에 전송된 메일 주소를 확인합니다.
4. 이제 Microsoft 개발자 센터에서 새 Microsoft 계정을 사용할 준비가 되었습니다.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Microsoft 개발자 센터에서 계정 등록
Microsoft 개발자 센터는 회사 정보를 등록하는 데 한 번 사용됩니다. 등록자는 회사의 정식 담당자여야 하며 신원을 확인할 수 있는 개인 정보를 제공해야 합니다. 등록자는 회사에서 공유되는 Microsoft 계정을 사용해야 하며 **동일한 계정을 Azure 게시 포털에서도 사용해야 합니다.** 계정 만들기를 시작하기 전에 회사에 Microsoft 개발자 센터 계정이 아직 없는지 확인해야 합니다. 프로세스 중에 회사 주소 정보, 은행 계좌 정보 및 세금 정보를 수집합니다. 이러한 정보는 일반적으로 재무 또는 업무 연락처에서 확인할 수 있습니다.

> [!IMPORTANT]
> 제품 만들기 및 배포의 여러 단계를 진행하기 위해서는 다음 개발자 프로필 구성 요소를 완료해야 합니다.
>
>

| 개발자 프로필 | 초안 시작 | 스테이징 | 무료 게시 및 솔루션 템플릿 | 상업적 게시 |
| --- | --- | --- | --- | --- |
| 회사 등록 |필수 |필수 |필수 |필수 |
| 세금 프로필 ID |옵션 |옵션 |옵션 |필수 |
| 은행 계좌 |옵션 |옵션 |옵션 |필수 |

> [!NOTE]
> BYOL(사용자 라이선스 필요)은 가상 머신에만 지원되며 **무료** 제공됩니다.
>
>

### <a name="register-your-company-account"></a>회사 계정 등록
1. 새 Chrome Incognito 또는 Internet Explorer InPrivate 브라우징 세션을 열어 개인 계정에 로그인되지 않았는지 확인합니다.
2. [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)로 이동하여 개발자 센터에서 판매자로 등록합니다. 계속 진행하기 전에 다음 중요 정보를 읽어 보세요.

   > [!IMPORTANT]
   > 개발자 센터에서 등록하는 데 사용할 전자 메일 ID 또는 메일 그룹(개인 종속성을 제거하려면 메일 그룹이 권장됨)을 Microsoft 계정으로 처음 등록해야 합니다. 그러지 않은 경우 이 [링크](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1)를 사용하여 등록하세요. 또한 개발자 센터 등록을 위해서는 **Microsoft 회사 도메인의 전자 메일 ID(예: @microsoft.com)는 사용할 수 없습니다**.
   >
   >

    ![drawing][img-signin]
3. "계정 보호 지원" 마법사를 완료하여 전화 번호 또는 메일 주소를 통해 신원을 확인합니다.

    ![drawing][img-verify]
4. "등록 계정 정보" 섹션에서 드롭다운 메뉴의 **계정 국가/지역** 을 선택합니다.

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **"판매" 국가:** Azure Marketplace에서 서비스를 판매하기 위해서는 등록된 법인이 승인된 위의 “판매” 국가 중 하나에 속해 있어야 합니다. 이 제한은 지급액 및 세금 때문에 있습니다. Microsoft는 가까운 장래에 이 국가 목록을 확장하기 위해 적극 노력하고 있으니 기대해 주세요. 자세한 내용은 [Marketplace 참가 정책](http://go.microsoft.com/fwlink/?LinkID=526833)을 참조하세요.
   >
   >
5. "계정 형식"으로 **회사**를 선택하고 **다음** 단추를 클릭합니다.

   > [!IMPORTANT]
   > 계정 형식에 대한 이해를 돕고 여러분을 위한 적합한 선택이 무엇인지 알려면 [계정 형식, 위치 및 수수료](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. **게시자 표시 이름**을 입력합니다. 이는 일반적으로 여러분의 회사 이름입니다.

   > [!TIP]
   > 개발자 센터에 입력한 게시자 표시 이름은 제공 제품이 나열될 때 Azure Marketplace에 표시되지 않습니다. 하지만 등록 프로세스를 완료하려면 입력해야 합니다.
   >
   >
7. 계정 확인을 위한 **연락처 정보** 를 입력합니다.

   > [!IMPORTANT]
   > 개발자 센터에서 승인된 회사에 대한 확인 프로세스에 사용되므로 정확한 연락처 정보를 제공해야 합니다.
   >
   >
8. **회사 승인자**의 연락처 정보를 입력합니다. 회사 승인자는 조직을 대신하여 개발자 센터에서 계정을 만드는 권한이 있는 사람을 확인할 수 있는 사람입니다. 작업을 마쳤으면 **다음**을 클릭하여 **"지급 섹션"** 으로 이동합니다.

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. 결제 정보를 입력하여 해당 계정에 대한 비용을 지불합니다. 등록 비용을 부담하는 프로모션 코드를 가지고 있는 경우 여기에 프로모션 코드를 입력할 수 있습니다. 그렇지 않은 경우 신용 카드 정보(또는 지원되는 시장에서 PayPal)를 제공합니다. 작업을 마쳤으면 **다음**을 클릭하여 **"검토 화면"** 으로 이동합니다.

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. 계정 정보를 검토하고 모든 정보가 올바른지 확인합니다. 그런 다음 [Microsoft Azure Marketplace 판매자 계약](http://go.microsoft.com/fwlink/?LinkID=699560)의 약관을 읽고 동의합니다. 확인란을 선택하여 이러한 약관을 읽고 동의했음을 나타냅니다.
11. **마침** 을 클릭하여 등록을 확인합니다. 메일 주소로 확인 메시지를 보냅니다.
12. 무료 제품만 게시할 계획인 경우 **Azure Marketplace 게시 포털로 이동** 을 클릭하여 이 문서의 섹션 3, [게시 포털에서 계정 등록](#3-register-your-account-in-the-publishing-portal)을 건너뛰어도 됩니다.

상업용 제품(예: 시간별로 요금이 청구되는 Virtual Machine 제품)을 게시할 계획인 경우 개발자 센터 계정에서 세금 및 은행 정보를 완료해야 하는 **계정 정보 업데이트**를 클릭합니다.

나중에 세금 및 은행 정보를 업데이트하려는 경우 다음 섹션(예: 이 문서의 섹션 3, [게시 포털에서 계정 등록](#3-register-your-account-in-the-publishing-portal))으로 이동했다가 Azure 게시 포털의 링크를 사용하여 나중에 다시 돌아올 수 있습니다.

> [!IMPORTANT]
> 상업용 제품의 경우 세금 및 은행 계좌 정보를 업데이트하지 않으면 제품을 프로덕션으로 푸시할 수 없습니다.
>
>

나중에 세금 및 은행 정보를 업데이트하려는 경우 섹션 3, [게시 포털에서 계정 등록](#3-register-your-account-in-the-publishing-portal)으로 이동했다가 Azure 게시 포털의 링크를 사용하여 나중에 다시 돌아올 수 있습니다.

### <a name="add-tax-and-banking-information"></a>세금 및 은행 정보 추가
 구매를 위한 상업용 제품을 게시하려는 경우 지급액 및 세금 정보를 추가하고 개발자 센터에서 확인을 위해 제출해야 합니다. 무료 제품만(또는 BYOL 제품) 게시하는 경우 이 정보를 추가할 필요가 없습니다. 나중에 추가해도 되지만 세금 정보를 확인하는 데 시간이 소요됩니다. 구매를 위한 상업용 제품을 제공할 것을 알고 있다면 가능한 빨리 추가하는 것이 좋습니다.

**은행 정보**

1. Microsoft 계정을 사용하여 [Microsoft 개발자 센터](http://dev.windows.com/registration?accountprogram=azure) 에 로그인합니다.
2. 왼쪽 메뉴에서 **지급 계좌**를 클릭하고 **지불 방법 선택** 아래에서 **은행 계좌** 또는 **PayPal**을 클릭합니다.

   > [!IMPORTANT]
   > Marketplace에서 고객이 구매하는 상업용 제품인 경우 이 계좌로 해당 구매에 대한 지급액을 받게 됩니다.
   >
   >
3. 결제 정보를 입력하고 만족할 경우 **저장** 을 클릭합니다.

   > [!IMPORTANT]
   > 지급 계좌를 업데이트하거나 변경해야 하는 경우 위의 동일한 단계를 따라 현재 정보를 새 정보로 바꿉니다. 지급 계좌를 변경하면 한 번의 지불 주기까지 지불을 연기할 수 있습니다. 이러한 지불 연기는 처음 지급 계좌를 설정할 때와 마찬가지로 계정 변경 내용을 확인해야 하기 때문에 발생합니다. 계정 확인 작업이 끝난 후 여전히 전체 금액에 대해 비용을 지불해야 하는 경우 현재 지불 주기에 대한 모든 지불 대금이 다음 주기에 추가됩니다.
   >
   >
4. **다음**을 클릭합니다.

**세금 정보**

1. Microsoft 계정을 사용하여(필요한 경우) [Microsoft 개발자 센터](http://dev.windows.com/registration?accountprogram=azure) 에 로그인합니다.
2. 왼쪽 메뉴에서 **세금 프로필** 을 클릭합니다.
3. **세금 양식 작성** 페이지에서 영주권이 있는 국가 또는 지역을 선택한 후 주요 시민권을 보유한 국가 또는 지역을 선택합니다. **다음**을 클릭합니다.
4. 세금 세부 정보를 입력하고 **다음**을 클릭합니다.

> [!WARNING]
> Microsoft 개발자 센터 계정에서 세금 및 은행 계좌 정보를 완료하지 않으면 상업용 제품을 프로덕션으로 푸시할 수 없습니다.
>
>

개발자 센터 등록에 문제가 있으면 아래와 같이 지원 티켓을 기록하세요.

1. 지원 링크 [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)로 이동
2. **문의처** 섹션 아래에서 **문제 제출** 버튼을 클릭합니다(아래 스크린샷 참조).

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. **문제 형식**으로 "개발자 센터 도움말"을 선택하고 **범주**로 "앱 게시 및 관리"를 선택합니다. 그 다음에 "전자 메일 시작" 버튼을 클릭합니다.

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. 로그인 페이지가 표시됩니다. Microsoft 계정 로그인을 사용합니다. Microsoft 계정이 없는 경우 이 [링크](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)를 사용하여 하나 만듭니다.
5. 문제 세부 정보를 입력한 후 **제출** 버튼을 클릭하여 티켓을 제출합니다.

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. 게시 포털에서 계정 등록
[게시 포털](http://publish.windowsazure.com) 은 제품을 게시하고 관리하는 데 사용됩니다.

1. 새 Chrome Incognito 또는 Internet Explorer InPrivate 검색 세션을 열어 개인 계정에 로그인되지 않았는지 확인합니다.
2. [http://publish.windowsazure.com](http://publish.windowsazure.com)로 이동합니다.
3. 새 사용자이며, 게시 포털에 처음으로 로그인하는 경우 개발자 센터 계정이 등록된 동일한 전자 메일 ID를 사용하여 로그인해야 합니다. 이러한 방식으로 개발자 센터 계정 및 게시 포털 계정이 서로 연결됩니다. 나중에 다음 단계를 수행하여 응용 프로그램에 대해 작업 중인 회사의 다른 멤버를 게시 포털에 공동 관리자로 추가할 수 있습니다.

게시 포털에서 공동 관리자로 추가되면 공동 관리자 계정을 사용하여 로그인할 수 있습니다.

> [!TIP]
> 참가 정책은 [Azure 웹 사이트](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)에 설명되어 있습니다.
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. 게시 포털에 공동 관리자를 추가하는 단계
**관리자인 경우** 아래에서 공동 관리자 추가 단계를 참조하세요.

> [!NOTE]
> **새 사용자인 경우** 게시에 포털에 공동 관리자를 추가하기 전에 게시 포털에서 하나 이상의 응용 프로그램을 만들었는지 확인합니다. **게시자** 탭은 게시 포털에서 하나 이상의 응용 프로그램을 만든 이후에만 나타나기 때문입니다.
>
>

1. 공동 관리자 전자 메일 ID가 MSA(Microsoft 계정)인지 확인합니다. 그렇지 않은 경우 이 [링크](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)를 사용하여 MSA로 등록합니다.
2. 공동 관리자를 추가하려고 하기 전에 관리자 계정 아래에 있는지 하나 이상의 응용 프로그램이 있는지 확인합니다.
3. 위 단계를 수행했으면 공동 관리자 전자 메일 ID를 사용하여 게시 포털에 로그인한 후 다시 로그아웃합니다.
4. 이제 관리자 전자 메일 ID를 사용하여 게시 포털에 로그인합니다.
5. 게시자->계정 선택->관리자->공동 관리자 추가로 이동합니다(아래 스크린샷 참조).

   ![drawing](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. 게시 포털에서 공동 관리자를 삭제하는 단계
**관리자인 경우** 아래에서 공동 관리자 삭제 단계를 참조하세요.

1. 관리자 전자 메일 ID를 사용하여 게시 포털에 로그인합니다.
2. **게시자**로 이동하여 계정을 선택하고 **관리자** -> **공동 관리자**로 이동합니다.
3. 삭제하려는 공동 관리자 옆의 **X** 단추를 클릭합니다(아래에서 스크린샷 제공).

    ![drawing](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>다음 단계
계정을 만들고 등록했으므로 [비기술 필수 구성 요소](marketplace-publishing-pre-requisites.md)를 검토하여 제품을 게시하기 위한 모든 일반 필수 조건을 충족해야 합니다.

## <a name="see-also"></a>참고 항목
* [시작: Azure Marketplace에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
