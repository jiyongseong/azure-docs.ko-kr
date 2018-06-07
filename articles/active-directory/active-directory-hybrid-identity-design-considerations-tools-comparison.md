---
title: '하이브리드 ID: 디렉터리 통합 도구 비교 | Microsoft Docs'
description: 이 페이지는 디렉터리 통합을 위해 사용할 수 있는 다양한 디렉터리 통합 도구를 비교하는 포괄적인 테이블을 제공합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/27/2018
ms.author: billmath
ms.openlocfilehash: 5d189af9b08f2b6e9ea194c15bfba683afc75a54
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>하이브리드 ID 디렉터리 통합 도구 비교
디렉터리 통합 도구는 수년에 걸쳐 성장하고 발전했습니다.  이 문서는 이러한 도구에 대한 통합 뷰와 각각에서 사용할 수 있는 기능에 대한 비교를 제공하는데 도움이 됩니다.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect는 이전에 DirSync 및 AAD 동기화로 릴리스된 구성 요소와 기능을 통합합니다. 이러한 도구는 이제 개별적으로 릴리스되지 않습니다. 향후의 모든 향상 기능은 Azure AD Connect 업데이트에 포함되므로, 최신 기능을 구할 위치를 항상 알 수 있습니다.
> 
> 디렉터리 동기화 및 Azure AD Sync는 사용되지 않습니다. 자세한 내용은 [여기](active-directory-aadconnect-dirsync-deprecated.md)를 참조하세요.
> 
> 

각 테이블에 다음 키를 사용합니다.

● = 지금 사용 가능  
FR = 향후 릴리스  
PP = 공개 미리 보기 상태  

## <a name="on-premises-to-cloud-synchronization"></a>온-프레미스와 클라우드 간 동기화
| 기능 | Azure Active Directory 연결 | Azure Active Directory 동기화 서비스(AAD Sync) | Azure Active Directory 동기화 도구(디렉터리 동기화) | Forefront Identity Manager(FIM) 2010 R2 | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 단일 온-프레미스 AD 포리스트에 연결 |● |● |● |● |● |
| 여러 온-프레미스 AD 포리스트에 연결 |● |● | |● |● |
| 여러 온-프레미스 Exchange 조직에 연결 |● | | | | |
| 단일 온-프레미스 LDAP 디렉터리에 연결 | | | |● |● |
| 여러 온-프레미스 LDAP 디렉터리에 연결 |  | | |● |● |
| 온-프레미스 AD 및 온-프레미스 LDAP 디렉터리에 연결 | | | |● |● |
| 사용자 지정 시스템(예: SQL, Oracle, MySQL 등)에 연결 |FR | | |● |● |
| 고객 정의 특성(디렉터리 확장) 동기화 |● | | | | |
| 온-프레미스 HR(즉, SAP, Oracle eBusiness, PeopleSoft)에 연결 |FR | | |● |● |
| 온-프레미스 시스템에 프로비전하기 위해 FIM 동기화 규칙과 커넥터를 지원합니다. | | | |● |● |


## <a name="cloud-to-on-premises-synchronization"></a>클라우드와 온-프레미스 간 동기화
| 기능 | Azure Active Directory 연결 | Azure Active Directory 동기화 서비스 | Azure Active Directory 동기화 도구(디렉터리 동기화) | Forefront Identity Manager(FIM) 2010 R2 | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 장치의 쓰기 저장 |● | |● | | |
| 특성 쓰기 저장(Exchange 하이브리드 배포) |● |● |● |● |● |
| 그룹 개체의 쓰기 저장 |● | | | | |
| 암호의 쓰기 저장(SSPR(셀프 서비스 암호 재설정) 및 암호 변경에서) |● |● | | | |

## <a name="authentication-feature-support"></a>인증 기능 지원
| 기능 | Azure Active Directory 연결 | Azure Active Directory 동기화 서비스 | Azure Active Directory 동기화 도구(디렉터리 동기화) | Forefront Identity Manager(FIM) 2010 R2 | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 단일 온-프레미스 AD 포리스트에 대한 암호 동기화 |● |● |● | | |
| 여러 온-프레미스 AD 포리스트에 대한 암호 동기화 |● |● | | | |
| 페더레이션이 있는 Single Sign-On |● |● |● |● |● |
| 암호의 쓰기 저장(SSPR 및 암호 변경에서) |● |● | | | |

## <a name="set-up-and-installation"></a>설정 및 설치
| 기능 | Azure Active Directory 연결 | Azure Active Directory 동기화 서비스 | Azure Active Directory 동기화 도구(디렉터리 동기화) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| 도메인 컨트롤러에서 설치 지원 |● |● |● | |
| SQL Express를 사용하여 설치 지원 |● |● |● | |
| 디렉터리 동기화에서 쉽게 업그레이드 |● | | | |
| Admin UX를 Windows Server 언어로 지역화 |● |● |● | |
| 최종 사용자 UX를 Windows Server 언어로 지역화 | | | |● |
| Windows Server 2008 및 Windows Server 2008 R2 지원 |● 동기화용, 페더레이션용이 아님 |● |● |● |
| Windows Server 2012 및 Windows Server 2012 R2 지원 |● |● |● |● |

## <a name="filtering-and-configuration"></a>필터링 및 구성
| 기능 | Azure Active Directory 연결 | Azure Active Directory 동기화 서비스 | Azure Active Directory 동기화 도구(디렉터리 동기화) | Forefront Identity Manager(FIM) 2010 R2 | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 도메인 및 조직 구성 단위에 대한 필터링 |● |● |● |● |● |
| 개체의 특성 값에 대한 필터링 |● |● |● |● |● |
| 최소 특성 집합이 동기화되도록 허용(MinSync) |● |● | | | |
| 다양한 서비스 템플릿이 특성 흐름에 적용되도록 허용 |● |● | | | |
| AD에서 Azure AD로의 흐름에서 특성 제거 허용 |● |● | | | |
| 특성 흐름에 대한 고급 사용자 지정 허용 |● |● | |● |● |

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

