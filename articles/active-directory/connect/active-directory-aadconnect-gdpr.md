---
title: Azure AD Connect 및 사용자 개인 정보 | Microsoft Docs
description: 이 문서에서는 Azure AD Connect를 사용하여 GDPR 준수를 가져오는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: a1fa7f58040b420bf52d89a57b1234416c2fb939
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149392"
---
# <a name="user-privacy-and-azure-ad-connect"></a>사용자 개인 정보 및 Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>이 문서에서는 Azure AD Connect 및 사용자 개인 정보를 다룹니다.  Azure AD Connect Health 및 사용자 개인 정보에 대한 자세한 내용은 [여기](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md) 문서를 참조하세요.

Azure AD Connect 설치를 위한 사용자 개인 정보 규정 준수는 두 가지 방법으로 도달할 수 있습니다.

1.  요청 시 사람에 대한 데이터를 추출하고 그 사람의 데이터를 설치에서 제거합니다.
2.  데이터는 48시간 이상 데이터가 보존하지 않도록 합니다.

Azure AD Connect 팀은 두 번째 옵션이 구현 및 유지 관리가 더 용이하므로 권장합니다.

Azure AD Connect 동기화 서버는 다음 사용자 개인 정보 데이터를 저장합니다.
1.  **Azure AD Connect 데이터베이스**에 있는 사람에 관한 데이터
2.  사람에 관한 정보를 포함할 수 있는 **Windows 이벤트 로그** 파일의 데이터
3.  사람에 관해 포함할 수있는 **Azure AD Connect 설치 로그 파일**의 데이터

사용자 데이터를 제거할 때 Azure AD Connect 고객은 다음 지침을 사용해야 합니다.
1.  적어도 48시간마다 Azure AD Connect 설치 로그 파일이 들어있는 폴더의 내용을 정기적으로 삭제하세요.
2.  이 제품은 이벤트 로그를 생성할 수도 있습니다.  이벤트 로그에 대한 자세한 내용은 [문서 여기](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx)를 참조하세요.

사람에 관한 데이터는 그 사람의 데이터가 처음 생성된 원본 시스템에서 제거되면 Azure AD Connect 데이터베이스에서 자동으로 제거됩니다. GDPR을 준수하기 위해 관리자의 특정 조치가 필요하지 않습니다.  그러나 Azure AD Connect 데이터는 적어도 2일마다 데이터 원본과 동기화되어야 합니다.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Azure AD Connect 설치 로그 파일 폴더 내용 삭제
정기적으로 **c:\programdata\aadconnect** 폴더의 콘텐츠를 확인하고 삭제합니다. 단, **PersistedState.Xml** 파일은 예외입니다. 이 파일은 Azure A Connect의 이전 설치 상태를 유지하며 업그레이드 설치를 수행할 때 사용됩니다. 이 파일에는 사람에 관한 데이터가 없으므로 삭제해서는 안됩니다.

>[!IMPORTANT]
>PersistedState.xml 파일은 삭제하지 마십시오.  이 파일에는 어떠한 사용자 정보도 없고 이전 설치의 상태를 유지합니다.

Windows 탐색기를 사용하여 이러한 파일을 검토하고 삭제하거나 다음과 같은 스크립트를 사용하여 필요한 작업을 수행할 수 있습니다.


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>이 스크립트가 48시간마다 실행되도록 예약합니다.
스크립트가 48시간마다 실행되도록 예약하려면 다음 단계를 사용합니다.

1.  확장명이 **&#46;PS1**인 파일에 스크립트를 저장한 다음, 제어판을 열고 **시스템 및 보안**을 클릭합니다.
    ![시스템](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  관리 도구 제목 아래에서 **Schedule Tasks**를 클릭합니다.
    ![Task](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  작업 스케줄러에서 **Task Schedule Library**를 마우스 오른쪽 단추로 클릭하고 **Create Basic 작업...** 을 클릭합니다.
4.  새 작업의 이름을 입력하고 **다음**을 클릭합니다.
5.  작업 트리거로 **매일**을 선택하고 **다음**을 클릭합니다.
6.  되풀이를 **2일**로 설정하고 **다음**을 클릭합니다.
7.  **프로그램**을 작업으로 선택하고 **다음**을 클릭합니다.
8.  프로그램/스크립트 상자에 **PowerShell**을 입력하고 **인수 추가(선택 사항)** 상자에 앞서 만든 스크립트의 전체 경로를 입력한 다음, **다음**을 클릭합니다.
9.  다음 화면에는 작성하려는 작업의 요약이 표시됩니다. 값을 확인하고 **마침**을 클릭하여 작업을 만듭니다.



## <a name="next-steps"></a>다음 단계
* [보안 센터에서 Microsoft 개인 정보 취급 방침을 검토합니다.](https://www.microsoft.com/trustcenter)
- [Azure AD Connect Health 및 사용자 개인 정보](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
