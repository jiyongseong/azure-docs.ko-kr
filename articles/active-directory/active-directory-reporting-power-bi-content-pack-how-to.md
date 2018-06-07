---
title: "Azure Active Directory Power BI 콘텐츠 팩 사용 방법 | Microsoft Docs"
description: "Azure Active Directory Power BI 콘텐츠 팩 사용 방법 알아보기"
services: active-directory
author: MarkusVi
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: 
ms.topic: get-started-article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/06/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 503b3f4c576382d8ce965d1f90aadda32c819a0b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Azure Active Directory Power BI 콘텐츠 팩 사용 방법

사용자가 Azure Active Directory 기능을 채택하고 사용하는 방법을 이해하는 것은 IT 관리자로서 중요한 일입니다. IT 인프라와 통신을 계획하여 사용량을 늘리고 AAD 기능을 최대한 활용할 수 있습니다. Azure Active Directory용 Power BI 콘텐츠 팩을 통해 사용자는 데이터를 보다 세부적으로 분석하여 이 데이터를 사용하는 방법을 이해하고 Azure Active Directory를 통해 사용자가 크게 의존하는 다양한 기능의 상황에 대해 더 많은 정보를 수집할 수 있습니다.  Azure Active Directory API를 Power BI에 통합하면 미리 빌드된 콘텐츠 팩을 쉽게 다운로드하고 Power BI가 제공하는 풍부한 시각화 환경을 사용하여 Azure Active Directory 내에서 모든 작업에 대한 정보를 얻을 수 있습니다. 사용자 고유의 대시보드를 만들고 조직 내 사람들과 쉽게 공유할 수 있습니다. 

이 항목에서는 사용자 환경에서 콘텐츠 팩을 설치하고 사용하는 방법에 대한 단계별 지침을 제공합니다.

## <a name="installation"></a>설치  

**Power BI 콘텐츠 팩을 설치하려면:**

1. Power BI 계정으로 [Power BI](https://app.powerbi.com/groups/me/getdata/services)에 로그인합니다(이 계정은 O365 계정 또는 Azure AD 계정과 동일).

2. 왼쪽 탐색 창의 아래쪽에서 **데이터 가져오기**를 선택합니다.

    ![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/01.png)
 
3. **서비스** 상자에서 **가져오기**를 클릭합니다.
   
    ![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/02.png)

4.  **Azure Active Directory** 검색

    ![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/03.png)
 
5.  프롬프트가 나타나면 Azure AD 테넌트 ID를 입력한 후 **다음**을 클릭합니다.

    > [!TIP] 
    > Office 365/Azure AD 테넌트의 테넌트 ID를 가져올 수 있는 빠른 방법은 Azure AD 포털에 로그인하고, 디렉터리로 드릴다운하고, [**속성** 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)에서 **디렉터리 ID**를 복사하는 것입니다.

    ![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/04.png) 

6.  **로그인**을 클릭합니다. 
 
    ![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/05.png) 



7.  사용자 이름 및 암호를 입력한 다음 **로그인**을 클릭합니다.
 
    ![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/06.png) 

8.  앱 동의 대화 상자에서 **동의**를 클릭합니다.
 
9.  Azure Active Directory 작업 로그 대시보드가 만들어지면 이를 클릭합니다.
 
    ![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/08.png) 

## <a name="what-can-i-do-with-this-content-pack"></a>이 콘텐츠 팩으로 무엇을 할 수 있습니까?

이 콘텐츠 팩으로 할 수 있는 작업으로 이동하기 전에 콘텐츠 팩의 다양 한 보고서의 빠른 미리 보기가 여기에 나와 있습니다. **지난 30일** 간의 보고서 데이터가 나와 있습니다.

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>이 버전의 Azure Active Directory 로그 콘텐츠 팩에 포함된 보고서

**앱 사용 및 추세 보고서**: 조직에서 사용된 앱과 어떤 앱이 언제 가장 많이 사용되는지에 대한 정보를 얻습니다. 조직에서 최근에 출시된 앱이 어떻게 사용되고 있는지에 대한 정보를 수집하고 어떤 앱이 인기가 있는지 파악하기 위해 이 보고서를 사용할 수 있습니다. 이 작업을 수행하면 앱이 사용되고 있지 않는지 확인할 때 활용도를 높일 수 있습니다.

**위치 및 사용자별 로그인**: Azure ID를 사용하여 수행된 모든 로그인에 대한 정보를 수집하고 사용자 ID에 대한 정보를 제공합니다. 이를 통해 개별 로그인에 대해 심층적으로 분석할 수 있으며 다음과 같은 질문에 답변할 수 있습니다.

- 이 사용자는 어디에서 로그인했는가?
- 어느 사용자가 가장 많이 로그인하고 어디에서 로그인하는가? 
- 성공적으로 로그인되었는가?  
 
특정 날짜 또는 위치를 클릭하여 세부 정보를 자세히 살펴볼 수 있습니다.

**앱당 고유 사용자**: 지정된 앱을 사용하여 모든 고유 사용자를 봅니다. 여기에는 응용 프로그램에 "*성공적으로*" 로그인한 사용자만 포함됩니다.

**장치 로그인**: 다음을 포함하여 사용자에 대한 자세한 정보와 함께 조직에서 사용자가 사용하는 OS 및 브라우저 형식을 봅니다.

- 사용자 이름
- IP 주소
- 위치 
- 로그인 상태 

이 보고서를 통해 조직 내에서 사용된 다양한 장치 프로필을 이해하고 용도에 따라 장치 정책을 결정합니다.

**SSPR 깔때기**: 조직에서 암호 재설정이 어떻게 수행되는지 알 수 있습니다. SSPR 도구를 통해 암호를 재설정하는 데 몇 개의 암호가 필요하고 그 중 몇 개가 성공하는지 확인합니다. SSPR 깔때기를 사용하여 암호 재설정 실패를 심층적으로 분석하고 특정 오류가 발생하는 이유를 이해합니다. 이 보고서를 통해 SSPR 도구가 조직 내에서 어떻게 사용되는지 자세히 알 수 있어 올바른 결정을 내릴 수 있습니다.

## <a name="customizing-azure-ad-activity-content-pack"></a>Azure AD 활동 콘텐츠 팩 사용자 지정

**시각화 변경**: **보고서 편집**을 클릭하여 보고서 시각화를 변경하고 원하는 시각화를 선택할 수 있습니다.
 
![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/09.png) 
 
![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/10.png) 

**추가 필드 포함**: 필드를 추가/제거하려는 시각적 개체를 선택하여 보고서에 필드를 추가하거나 제거할 수 있습니다. 아래 예에서는 "로그인 상태" 필드를 테이블 뷰에 추가하겠습니다. 
 
![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/11.png) 

**시각화를 대시보드에 고정**: 대시보드를 사용자 지정하여 시각화를 보고서에 포함시켜 대시보드에 고정할 수 있습니다. 아래 예에서는 "로그인 상태"라는 새 필터를 추가하여 보고서에 포함했습니다. 또 시각화를 가로 막대형 차트에서 꺾은선형 차트로 변경했으며 새로운 시각적 개체를 대시보드에 고정할 수 있습니다.

![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/12.png) 

![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/13.png) 
 

 


**대시보드 공유**: 원하는 콘텐츠를 만든 후 대시보드를 조직의 사용자와 공유할 수 있습니다. 보고서를 공유하면 보고서에서 선택한 필드를 볼 수 있습니다.
 
![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Power BI 보고서의 매일 새로 고침 예약

Power BI 보고서의 매일 새로 고침을 예약하려면 **데이터 집합 > 설정 > 새로 고침 예약**으로 이동하고 아래와 같이 설정합니다.
 
![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>콘텐츠 팩의 최신 버전으로 업데이트

최신 버전을 얻기 위해 콘텐츠 팩을 업데이트 하려면:

- 새 콘텐츠 팩을 다운로드하고 이 문서에 나열된 지침에 따라 설정합니다.

- 설정되면 **데이터 소스 > 설정 > 데이터 원본 자격 증명**으로 이동하여 아래와 같이 자격 증명을 다시 입력합니다.

    ![Azure Active Directory Power BI 콘텐츠 팩](./media/active-directory-reporting-power-bi-content-pack-how-to/16.png) 

콘텐츠 팩의 새 버전이 작동하면 필요한 경우 이전 버전의 콘텐츠 팩과 연결된 기본 보고서 및 데이터 집합을 삭제하여 이전 버전을 제거할 수 있습니다.

## <a name="still-having-issues"></a>아직도 문제가 있으십니까? 

[문제 해결 가이드](active-directory-reporting-troubleshoot-content-pack.md)를 확인하세요. Power BI와 관련된 일반적인 도움말은 이 [도움말 문서](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-started/)을 확인하세요.
 

## <a name="next-steps"></a>다음 단계

보고 개요는 [Azure Active Directory 보고](active-directory-reporting-azure-portal.md)를 참조하세요.
