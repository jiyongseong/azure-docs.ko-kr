---
title: 'Azure Active Directory Connect: FAQ - | Microsoft Docs'
description: 이 페이지에는 Azure AD Connect에 대해 자주 묻는 질문과 대답이 있습니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: billmath
ms.openlocfilehash: 46a9bf47b4998c4d5be47f67556fbdb3ba7b71db
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054097"
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Azure Active Directory Connect에 대한 질문과 대답

## <a name="general-installation"></a>일반 설치
**Q: Azure AD 전역 관리자가 2FA를 사용하도록 설정한 경우 설치가 작동하나요?**  
2016년 2월 빌드에서 지원됩니다.

**Q: Azure AD Connect를 무인 설치하는 방법이 있나요?**  
설치 마법사를 사용하여 Azure AD Connect 설치만 지원됩니다. 무인 및 자동 설치는 지원되지 않습니다.

**Q: 하나의 도메인에 연결할 수 없는 포리스트가 있습니다. Azure AD Connect를 설치하려면 어떻게 하나요?**  
2016년 2월 빌드에서 지원됩니다.

**Q: AD DS 상태 에이전트는 서버 코어에서 작동하나요?**  
예. 에이전트를 설치한 후 다음 PowerShell cmdlet을 사용하여 등록 프로세스를 완료할 수 있습니다. 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Q: AADConnect는 두 도메인에서 Azure AD로의 동기화를 지원하나요?**</br>
예, 지원됩니다. [여러 도메인](active-directory-aadconnect-multiple-domains.md)을 참조하세요.
 
**Q: Azure AD Connect에 동일한 Active Directory 도메인에 대한 여러 커넥터를 지원하나요?**</br> 아니요, 지원되지 않습니다. 

## <a name="network"></a>네트워크
**Q: 방화벽, 네트워크 장치 또는 네트워크에서 열려 있는 상태로 유지할 수 있는 최대 시간 연결을 제한하는 다른 요소가 있습니다. Azure AD Connect를 사용하는 경우 클라이언트 쪽 기간 내 클라이언트 쪽 시간 제한 임계값은 얼마나 길어야 합니까?**  
모든 네트워킹 소프트웨어, 물리적 장치 또는 열린 상태로 둘 수 있는 최대 시간 연결을 제한하는 다른 것은 Azure AD Connect 클라이언트를 설치한 서버와 Azure Active Directory 간의 연결에 대해 적어도 5분(300초)의 임계값을 사용해야 합니다. 이는 이전에 릴리스된 모든 Microsoft ID 동기화 도구에 적용됩니다.

**Q: SLD(단일 레이블 도메인)가 지원되나요?**  
아니요 Azure AD Connect는 SLD를 사용하는 온-프레미스 포리스트/도메인을 지원하지 않습니다.

**Q: 비연속 AD 도메인을 갖는 포리스트가 지원되나요?**  
아니요. Azure AD Connect는 비연속 네임스페이스를 갖는 온-프레미스 포리스트를 지원하지 않습니다.

**Q: "마침표"를 포함하는 NetBios 이름이 지원되나요?**  
아니요, Azure AD Connect는 NetBios 이름에 마침표 "."를 포함하는 온-프레미스 포리스트/도메인을 지원하지 않습니다.

**Q: 순수한 IPv6 환경이 지원되나요?**  
아니요. Azure AD Connect는 순수한 IPv6 환경을 지원하지 않습니다.

## <a name="federation"></a>페더레이션
**Q: 내 Office 365 인증서를 갱신하라는 메일을 받는 경우 어떻게 해야 하나요?**  
인증서를 갱신하는 방법은 [인증서 갱신](active-directory-aadconnect-o365-certs.md) 항목에 설명된 참고 자료를 사용하세요.

**Q: O365 신뢰 당사자에 대해 "신뢰 당사자 자동 업데이트"를 설정했습니다. 토큰 서명 인증서가 자동으로 롤오버될 때 어떤 조치를 취해야 하나요?**  
[인증서 갱신](active-directory-aadconnect-o365-certs.md)문서에 설명된 참고 자료를 사용하세요.

## <a name="environment"></a>Environment
**Q: Azure AD Connect를 설치한 후에 서버 이름을 변경하는 것이 지원되나요?**  
번호 서버 이름을 변경하면 동기화 엔진이 SQL 데이터베이스에 연결할 수 없게 되고 서비스를 시작할 수 없게 됩니다.

## <a name="identity-data"></a>ID 데이터
**Q: Azure AD의 UPN(userPrincipalName) 특성이 온-프레미스 UPN가 일치하지 않습니다. 왜 그런가요?**  
아래 문서를 참조하세요.

* [Office 365, Azure 또는 Intune의 사용자 이름이 온-프레미스 UPN 또는 대체 로그인 ID와 일치하지 않습니다(영문).](https://support.microsoft.com/en-us/kb/2523192)
* [다른 페더레이션된 도메인을 사용하기 위해 사용자 계정의 UPN을 변경한 후에 Azure Active Directory 동기화 도구에서 변경 사항이 동기화되지 않습니다(영문).](https://support.microsoft.com/en-us/kb/2669550)

[Azure AD Connect 동기화 서비스 기능](active-directory-aadconnectsyncservice-features.md)에 설명된 대로 동기화 엔진이 userPrincipalName을 업그레이드할 수 있도록 Azure AD를 구성할 수도 있습니다.

**Q:기존 Azure AD Group/Contact 개체와 온-프레미스 AD Group/Contact 개체의 소프트 매칭이 지원되나요?**  
예. proxyAddress에 따라 다릅니다.  소프트 매칭은 메일 사용이 가능하지 않은 그룹에서 지원되지 않습니다.

**Q: 기존 Azure AD Group/Contact 개체의 ImmutableId 특성을 온-프레미스 AD Group/Contact 개체에 하드 매칭하도록 수동 설정하는 것이 지원되나요?**  
아니요. 현재는 지원되지 않습니다.

## <a name="custom-configuration"></a>사용자 지정 구성
**Q: Azure AD Connect에 대한 PowerShell cmdlet 설명서는 어디에 있나요?**  
이 사이트에 설명되어 있는 cmdlet을 제외하고, Azure AD Connect에 나오는 다른 PowerShell cmdlet은 고객 사용이 지원되지 않습니다.

**Q: *Synchronization Service Manager*의 "서버 내보내기/서버 가져오기"를 사용하여 서버 간에 구성을 이동할 수 있나요?**  
번호 이 옵션은 모든 구성 설정을 가져오는 것이 아니므로 사용하지 말아야 합니다. 그 대신 마법사를 사용하여 두 번째 서버에 기본 구성을 만들고 동기화 규칙 편집기를 사용하여 PowerShell 스크립트를 생성하여 서버 간에 사용자 지정 규칙을 이동할 수 있습니다. [스윙 마이그레이션](active-directory-aadconnect-upgrade-previous-version.md#swing-migration)을 참조하세요.

**Q: autocomplete 특성이 "false"인 암호 입력 요소를 포함하고 있으니 Azure 로그인 페이지에 대한 암호를 캐시하면 이 문제를 방지할 수 있나요?**</br>
현재는 autocomplete 태그를 포함하여 암호 입력 필드의 HTML 특성을 수정하는 기능이 지원되지 않습니다. 현재 암호 필드에 특성을 추가할 수 있도록 사용자 지정 Javascript를 허용하는 기능을 개발하는 중입니다. 이 기능은 2017년 하반기에 제공될 예정입니다.

**Q: Azure 로그인 페이지에서 이전에 성공적으로 로그인한 사용자의 사용자 이름이 표시됩니다.  이 동작을 끌 수 있나요?**</br>
현재는 로그인 페이지의 HTML 특성을 수정하는 기능이 지원되지 않습니다. 현재 암호 필드에 특성을 추가할 수 있도록 사용자 지정 Javascript를 허용하는 기능을 개발하는 중입니다. 이 기능은 2017년 하반기에 제공될 예정입니다.

**Q: 동시 세션을 방지하는 방법이 있나요?**</br>
번호

## <a name="auto-upgrade"></a>자동 업그레이드

**Q: 자동 업그레이드를 사용하는 이점 및 결과는 무엇인가요?**</br>
모든 고객이 해당 Azure AD Connect 설치에 대한 자동 업그레이드를 사용하는 것이 좋습니다. 그러면 Azure AD Connect에서 확인된 취약성에 대한 보안 업데이트를 비롯한 최신 패치를 받는다는 이점이 있습니다. 업그레이드 프로세스는 간편하고 새 버전이 지원되는 즉시 자동으로 수행됩니다. 수천명의 Azure AD Connect 고객에게 모든 새 릴리스의 자동 업그레이드를 제공합니다.

자동 업그레이드 프로세스는 먼저 설치 프로그램이 자동 업그레이드를 지원하는지 여부(규칙, 특정 환경 요소 등에 대한 사용자 지정 변경 내용 검색 포함)를 설정하고 그렇다면 업그레이드를 수행하고 테스트합니다. 테스트가 업그레이드에 성공하지 못했다고 표시하는 경우 이전 버전이 자동으로 복원됩니다.

환경의 크기에 따라 프로세스는 몇 시간이 걸릴 수 있습니다. 또한 업그레이드를 수행하는 동안 Windows Server AD 및 Azure AD 간의 동기화는 수행되지 않습니다.

**Q: 내 자동 업그레이드가 더 이상 작동하지 않고 새 버전을 설치해야 한다는 이메일을 받았습니다. 이렇게 하는 것이 왜 필요한가요?**</br>
지난 해 Azure AD Connect 버전을 릴리스했습니다만 특정 상황에서 서버의 자동 업그레이드 기능이 비활성화될 수 있습니다. 지난 달 말에 릴리스된 Azure AD Connect 버전 1.1.750.0에서 이 문제가 해결되었습니다. 이 문제의 영향을 받는 고객은 문제를 완화하기 위해 최신 버전의 Azure AD Connect로 수동 업그레이드해야 합니다. 수동으로 업그레이드하려면 AADConnect.msi 파일의 최신 버전을 다운로드하고 실행해야 합니다.
 
-  현재 버전이 1.1.750.0 이전인 경우 [여기에서 다운로드 가능한](https://www.microsoft.com/en-us/download/details.aspx?id=47594) 최신 버전으로 업그레이드해야 합니다.
- Azure AD Connect 버전이 1.1.750.0 이상인 경우 이를 수정한 버전이 이미 설치되어 자동 업그레이드 문제를 완화하는 조치를 취할 필요가 없습니다. 

**Q: 자동 업그레이드를 다시 활성화하기 위해 최신 버전으로 업그레이드하라는 이메일을 받았습니다. 1.1.654.0을 사용 중인데 업그레이드해야 하나요?** </br>    
예, 1.1.750 이상으로 업그레이드하거나 최신 자동 업그레이드를 다시 활성화해야 합니다. 최신 버전으로 업그레이드하는 방법을 설명하는 링크는 다음과 같습니다.

**Q: 자동 업그레이드를 다시 활성화하기 위해 최신 버전으로 업그레이드하라는 이메일을 받았습니다. PowerShell을 사용하여 자동 업그레이드를 활성화했는데 최신 버전을 설치해야 하나요?**</br>    
예, 버전 1.1.750.0 이상으로 업그레이드해야 합니다. PowerShell을 사용하여 자동 업그레이드 서비스를 설정하면 1.1.750 이전 버전에서 발견된 자동 업그레이드 문제를 완화하지 않습니다.

**Q: 최신 버전으로 업그레이드하려고 하지만 Azure AD Connect를 설치한 사용자가 확실하지 않고 사용자 이름 및 암호가 없습니다.  필요한가요?**</br>
Azure AD Connect를 업그레이드하는 데 처음 사용된 사용자 이름 및 암호를 알 필요가 없습니다. 전역 관리자 역할이 있는 Azure AD 계정이면 사용할 수 있습니다.

**Q: 내가 설치한 Azure AD Connect 버전을 어떻게 찾을 수 있나요?**</br>   
서버에 설치된 Azure AD Connect의 버전을 확인하려면 제어판으로 이동하고 "프로그램 > 프로그램 및 기능"에서 Microsoft Azure AD Connect의 설치된 버전을 조회하세요.

![버전](media/active-directory-aadconnect-faq/faq1.png)

**Q: AADConnect의 최신 버전으로 업그레이드하려면 어떻게 할까요?**</br>    
이 [아티클](active-directory-aadconnect-upgrade-previous-version.md)에서는 최신 버전으로 업그레이드하는 방법을 설명합니다. 

**Q: 작년에 AADConnect의 최신 버전으로 이미 업그레이드했습니다. 다시 업그레이드해야 하나요?**</br> Azure AD Connect 팀은 서비스를 자주 업데이트합니다. 서버는 버그 수정 및 보안 업데이트뿐만 아니라 새로운 기능을 활용할 수 있는 최신 버전으로 업데이트되어야 합니다. 자동 업그레이드를 사용하면 소프트웨어 버전이 자동으로 업데이트됩니다. Azure AD Connect의 버전 릴리스 기록을 찾으려면 이 [링크](active-directory-aadconnect-version-history.md)를 따라가세요.

**Q: 업그레이드하는 데 걸리는 시간 및 내 사용자에게 미치는 영향은 무엇인가요?**</br>    
업그레이드하는 데 필요한 시간은 테넌트 크기에 따라 다릅니다. 대규모 조직의 경우 저녁이나 주말에 수행하는 편이 좋을 수 있습니다. 업그레이드하는 동안 동기화 작업이 수행되지 않습니다.

**Q: AADConnect로 업그레이드했지만 Office 포털에서 여전히 DirSync를 언급합니다.  이유가 무엇인가요?**</br>    
Office 팀은 Office 포털 업데이트가 현재 제품 이름을 반영하도록 작업 중입니다. 아직 사용 중인 동기화 도구를 반영하지 않습니다.

**Q: 내 자동 업그레이드 상태를 확인했는데 "일시 중단됨"으로 표시됩니다. 일시 중지된 이유가 무엇인가요? 활성화해야 하나요?**</br>     
이전 버전에서 특정 상황에서 자동 업그레이드 상태를 "일시 중지"로 설정하는 버그가 소개되었습니다. 수동으로 사용하도록 설정하는 것이 기술적으로 가능하지만 복잡한 여러 단계를 거쳐야 합니다. 따라서 최신 버전의 Azure AD Connect를 설치하는 것이 가장 좋습니다.

**Q: 회사의 관리 요구 사항이 엄격해서 푸시되는 시기를 제어하려고 합니다. 자동 업그레이드를 시작할 시기를 제어할 수 있나요?**</br> 아니요, 현재 그러한 기능은 없지만 이후 릴리스에서 도입하도록 평가 중입니다.

**Q: 자동 업그레이드에 실패한 경우 이메일을 수신하나요? 성공했는지 어떻게 알 수 있나요?**</br>     
업그레이드 결과에 대해 알림을 받지 않습니다. 이것은 이후 릴리스에서 도입하도록 평가 중입니다.

**Q: 자동 업그레이드를 푸시하도록 계획한 시기로 타임라인을 게시하나요?**</br>    
자동 업그레이드는 새 버전의 릴리스 프로세스에서 첫 번째 단계이므로 새로운 릴리스가 있을 때마다 자동 업그레이드를 푸시할 예정입니다. Azure AD Connect의 최신 버전은 [Azure AD 로드맵](../../active-directory/whats-new.md)에서 사전에 발표됩니다.

**Q: 자동 업그레이드는 AAD Connect Health를 업그레이드하나요?**</br>   네, 자동 업그레이드는 AAD Connect Health도 업그레이드합니다.

**Q: 준비 모드의 AAD Connect 서버도 자동 업그레이드하나요?**</br>   
아니요, 준비 모드인 Azure AD Connect 서버는 자동 업그레이드할 수 없습니다.

**Q: 자동 업그레이드가 실패하고 AAD Connect 서버가 시작되지 않으면 어떻게 해야 하나요?**</br>   
드물게 업그레이드를 수행한 후에 Azure AD Connect 서비스가 시작되지 않을 수 있습니다. 이러한 경우에 서버를 재부팅하세요. 그러면 일반적으로 문제를 해결합니다. Azure AD Connect 서비스가 여전히 시작되지 않는 경우 지원 티켓을 개시하세요. 이 작업을 수행하는 방법을 설명하는 [링크](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)는 다음과 같습니다. 

**Q: 최신 버전의 Azure AD Connect로 업그레이드할 경우 위험성을 모르겠습니다. 업그레이드에서 도움을 받기 위해 통화할 수 있나요?**</br>
Azure AD Connect의 최신 버전으로 업그레이드하는 데 도움이 필요한 경우 지원 티켓을 개시하세요. 작업을 수행하는 방법을 보여주는 [링크](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)는 다음과 같습니다.

## <a name="troubleshooting"></a>문제 해결
**Q: Azure AD Connect에 대한 도움을 받으려면 어떻게 합니까?**

[Microsoft KB(기술 자료) 검색](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Microsoft KB(기술 자료)에서 Azure AD Connect 지원에 대한 일반적인 고장 수리 문제에 대한 기술 솔루션을 검색하세요.

[Microsoft Azure Active Directory 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* 커뮤니티에서 기술 질문 및 대답을 검색하고 찾아보거나 [여기](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)를 클릭하여 직접 질문을 할 수 있습니다.

[Azure AD에 대한 지원을 받는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Azure Portal을 통해 지원을 받으려면 이 링크를 사용합니다.

