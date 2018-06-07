---
title: 커넥터 버전 릴리스 내역 | Microsoft Docs
description: 이 항목에서는 FIM(Forefront Identity Manager) 및 MIM(Microsoft Identity Manager)에 대한 커넥터의 모든 버전을 보여 줍니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2018
ms.author: davidste
ms.openlocfilehash: 039e21cb3c35f8eb024ad2fca56e8a59e3182d3a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="connector-version-release-history"></a>커넥터 버전 릴리스 내역
FIM(Forefront Identity Manager) 및 MIM(Microsoft Identity Manager)의 커넥터는 자주 업데이트됩니다.

> [!NOTE]
> 이 항목은 FIM 및 MIM에만 있습니다. 이러한 커넥터는 Azure AD Connect에서 설치하도록 지원되지 않습니다. 출시된 커넥터는 지정된 빌드로 업그레이드할 때 AADConnect에 미리 설치됩니다.


이 항목은 출시된 커넥터의 모든 버전을 나열합니다.

관련 링크:

* [최신 커넥터 다운로드](http://go.microsoft.com/fwlink/?LinkId=717495)
* [일반 LDAP 커넥터](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 참조 설명서
* [일반 SQL 커넥터](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql) 참조 설명서
* [웹 서비스 커넥터](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-ma-ws) 참조 설명서
* [PowerShell 커넥터](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-powershell) 참조 설명서
* [Lotus Domino 커넥터](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-domino) 참조 설명서


## <a name="118300"></a>1.1.830.0

### <a name="fixed-issues"></a>수정된 문제:
* ConnectorsLog System.Diagnostics.EventLogInternal.InternalWriteEvent를 해결했습니다. (메시지: 시스템에 연결된 장치가 작동하지 않습니다.)
* 커넥터의 이 릴리스의 miiserver.exe.config에서 3.3.0.0-4.1.3.0에서 4.1.4.0으로 바인딩 리디렉션을 업데이트해야 합니다.
* 일반 웹 서비스:
    * 해결된 유효한 JSON 응답을 구성 도구에 저장할 수 없습니다.
* 일반 SQL:
    * 내보내기는 항상 삭제 작업의 업데이트 쿼리만을 생성합니다. 삭제 쿼리를 생성하도록 추가했습니다
    * '델타 전략'이 '변경 내용 추적'인 경우 델타 가져오기 작업에 대한 개체를 가져오는 SQL 쿼리가 수정되었습니다. 이 구현이 알려진 제한에서 '변경 내용 추적' 모드를 사용하는 델타 가져오기는 다중 값 특성에서 변경 내용을 추적하지 않습니다
    * 다중 값 특성의 마지막 값을 삭제하는 데 필요하고 이 행이 삭제하는 데 필요한 값을 제외한 다른 데이터를 포함하지 않는 경우에 삭제 쿼리를 생성할 가능성이 증가되었습니다.
    * SP에 의해 출력 매개 변수를 구현할 때 처리하는 System.ArgumentException 
    * varbinary(max) 형식이 있는 필드에 내보내기 작업을 수행하는 잘못된 쿼리
    * (함수 ExportAttributes 및 GetQueryForMultiValue에서) parameterList 변수를 두 번 초기화하는 문제


## <a name="116490-aadconnect-116490"></a>1.1.649.0(AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>수정된 문제:

* Lotus Notes:
  * 사용자 지정 인증자 필터링 옵션
  * ImportOperations 클래스 가져오기에서는 ‘보기’ 모드에서 실행할 수 있는 작업과 ‘검색’ 모드에서 실행할 수 있는 작업의 정의가 수정되었습니다.
* 일반 LDAP:
  * OpenLDAP Directory는 entryUUI 대신 DN을 앵커로 사용합니다. 앵커 수정이 가능한 GLDAP 커넥터의 새로운 옵션
* 일반 SQL:
  * varbinary(max) 유형의 필드로 내보내기가 수정되었습니다.
  * 데이터 원본의 이진 데이터를 CSEntry 개체에 추가할 때 DataTypeConversion 함수가 0바이트에서 실패했습니다. CSEntryOperationBase 클래스의 DataTypeConversion 함수가 수정되었습니다.




### <a name="enhancements"></a>향상된 기능:

* 일반 SQL:
  * 명명되거나 명명되지 않은 매개 변수로 저장 프로시저를 실행하기 위한 모드를 구성하는 기능이 일반 SQL 관리 에이전트 구성 창의 '글로벌 매개 변수' 페이지에 추가되었습니다. '글로벌 매개 변수' 페이지에는 '명명된 매개 변수를 사용하여 저장 프로시저 실행'이라는 레이블의 확인란이 있습니다. 이는 명명된 매개 변수로 저장 프로시저를 실행할지에 대한 모드를 담당합니다.
    * 현재 명명된 매개 변수로 저장 프로시저를 실행하는 기능은 IBM DB2 및 MSSQL 데이터베이스에서만 작동합니다. Oracle 및 MySQL 데이터베이스의 경우 이 방법이 작동하지 않습니다. 
      * MySQL의 SQL 구문은 저장 프로시저에서 명명된 매개 변수를 지원하지 않습니다.
      * Oracle용 ODBC 드라이버는 저장 프로시저에서 명명된 매개 변수에 대해 명명된 매개 변수를 지원하지 않습니다.

## <a name="116040-aadconnect-116140"></a>1.1.604.0(AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>수정된 문제:

* 일반 웹 서비스:
  * 두 개 이상의 끝점이 있을 때 SOAP 프로젝트가 만들어지지 않도록 방지하는 문제를 해결했습니다.
* 일반 SQL:
  * 가져오기 작업에서 커넥터 공간에 저장할 때 GSQL이 시간을 제대로 변환하지 않았습니다. GSQL의 커넥터 공간에 대한 기본 날짜 및 시간 형식이 'yyyy-MM-dd hh:mm:ssZ'에서 'yyyy-MM-dd HH:mm:ssZ'로 변경되었습니다.

## <a name="115510-aadconnect-115530"></a>1.1.551.0(AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>수정된 문제:

* 일반 웹 서비스:
  * Wsconfig 도구는 REST 서비스 메서드에 대한 "샘플 요청"의 Json 배열을 제대로 변환하지 못했습니다. 이로 인해 REST 요청에 대한 이 Json 배열의 직렬화와 관련된 문제가 발생했습니다.
  * 웹 서비스 커넥터 구성 도구는 JSON 속성 이름에 공백 기호를 사용하는 것을 지원하지 않습니다. 
    * WSConfigTool.exe.config 파일에 대체 패턴을 수동으로 추가할 수 있습니다(예: ```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```).
> [!NOTE]
> 다음 오류를 수신하는 내보내기에 JSONSpaceNamePattern 키가 필요합니다. 메시지: 이름이 비어 있으면 안됩니다. 

* Lotus Notes:
  * **조직/조직 구성 단위에 대해 사용자 지정 인증자 허용** 옵션이 해제된 경우 내보내기(업데이트) 중 커넥터가 실패합니다. 내보내기 흐름 후에 모든 특성이 Domino로 내보내지지만, 내보내기 시 KeyNotFoundException이 동기화에 반환됩니다. 
    * 이 문제는 아래 특성 중 하나를 변경하여 DN(UserName 특성) 변경을 시도할 때 이름 바꾸기 작업이 실패하기 때문에 발생합니다.  
      - LastName
      - FirstName
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - ou
      - altcommonname

  * **조직/조직 구성 단위에 대한 사용자 지정 인증자 허용** 옵션이 설정되었지만 필수 인증자가 여전히 비어 있으면 KeyNotFoundException이 발생합니다.

### <a name="enhancements"></a>향상된 기능:

* 일반 SQL:
  * **시나리오: 다시 설계되고 구현됨:** "*" 기능
  * **솔루션 설명:** [다중 값 참조 특성 처리](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql) 접근 방식이 변경되었습니다.


### <a name="fixed-issues"></a>수정된 문제:

* 일반 웹 서비스:
  * 웹 서비스 커넥터가 있는 데도 서버 구성을 가져올 수 없습니다.
  * 웹 서비스 커넥터는 여러 웹 서비스에 작동하지 않습니다.

* 일반 SQL:
  * 단일 값 참조 특성에 대해 나열되는 개체 형식이 없습니다.
  * 변경 추적 전략에 대한 델타 가져오기는 다중 값 테이블에서 값이 제거될 때 개체를 삭제합니다.
  * AS/400의 DB2에서 GSQL 커넥터의 OverflowException

Lotus:
  * GlobalParameters 페이지를 열기 전에 OU 검색을 설정/해제하는 옵션이 추가되었습니다.

## <a name="114430"></a>1.1.443.0

출시 날짜: 2017년 3월

### <a name="enhancements"></a>향상된 기능

* 일반 SQL:</br>
  **시나리오 증상:** 하나의 개체 형식에 대한 참조만 허용하고 멤버와 상호 참조하는 SQL 커넥터의 잘 알려진 제한입니다. </br>
  **솔루션 설명:** “*” 옵션을 선택한 참조를 위한 처리 단계에서 모든 개체 형식 조합은 동기화 엔진으로 다시 반환됩니다.

>[!Important]
- 그러면 자리 표시자가 많이 생성됩니다.
- 이름이 고유한 상호 개체 형식인지 확인해야 합니다.


* 일반 LDAP:</br>
 **시나리오:** 몇 가지 컨테이너만 특정 파티션에서 선택된 경우 검색은 여전히 전체 파티션에서 수행됩니다. 특정 파티션은 성능 저하로 이어질 수 있는 MA가 아닌 Synchronization Service에서 필터링됩니다. </br>

 **해결 방법 설명:** 모든 컨테이너에 걸쳐 수행하고 전체 파티션에서 검색하는 대신 각 파티션에서 개체를 검색하기 위해 변경된 GLDAP 커넥터의 코드입니다.


* Lotus Domino:

  **시나리오:** 내보내기를 수행하는 동안 사용자 제거를 위한 Domino 메일 삭제 지원입니다. </br>
  **해결 방법:** 내보내기를 수행하는 동안 사용자 제거를 위한 구성 가능한 메일 삭제 지원입니다.

### <a name="fixed-issues"></a>수정된 문제:
* 일반 웹 서비스:
 * WebService 구성 도구를 통해 기본 SAP wsconfig 프로젝트에서 서비스 URL을 변경한 후 다음과 같은 오류가 발생한 경우: 경로의 일부를 찾을 수 없습니다.

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* 일반 LDAP:
 * GLDAP 커넥터에서 AD LDS의 모든 특성을 확인할 수 없음
 * LDAP 디렉터리 스키마에서 UPN 특성이 감지되지 않는 경우 마법사 중단
 * "objectclass" 특성이 선택되지 않은 경우 전체 가져오기를 수행하는 동안 검색 오류가 발생하지 않으면 델타 가져오기 실패
 * "파티션 및 계층 구조 구성" 구성 페이지에 일반  
LDAP MA의 Novel 서버에 대한 파티션과 형식이 비슷한 개체가 표시되지 않습니다. RootDSE 파티션의 개체만 표시됩니다.


* 일반 SQL:
 * 일반 SQL 워터마크 델타 가져오기 다중값 특성이 버그를 가져오지 않는 문제 해결
 * 다중값 특성의 삭제/추가된 값을 내보낼 때 데이터 원본에서 삭제/추가되지 않음.  


* Lotus Notes:
 * 특성에 대한 값이 Null이거나 비어있다는 알림을 내보낼 때 특정 필드 "전체 이름"이 메타버스에 올바르게 표시됩니다.
 * 중복 인증자 오류 해결
 * 데이터가 없는 개체가 다른 개체를 통해 Lotus Domino 커넥터에서 선택된 경우 전체 가져오기를 수행하는 동안 검색 오류를 수신합니다.
 * 델타 가져오기가 Lotus Domino 커넥터에서 실행 중인 경우 실행 마지막에 Microsoft.IdentityManagement.MA.LotusDomino.Service.exe 서비스가 때때로 응용 프로그램 오류를 반환합니다.
 * 멤버 자격에서 사용자를 제거하려고 시도하기 위해 내보내기를 실행할 때 성공적으로 업데이트되었음이 표시되지만 실제로 사용자가 Lotus Notes의 멤버 자격에서 삭제되지는 않는 경우를 제외하면 전체 그룹 멤버는 정상적으로 작동하며 유지됩니다.
 * 다중값 특성을 내보내는 동안 새 항목을 아래에 추가할 수 있도록 "아래에 항목 추가"로 내보내기 모드를 선택할 수 있는 기능이 Lotus MA의 GUI 구성에 추가되었습니다.
 * 커넥터는 메일 폴더 및 ID 자격 증명 모음에서 파일을 삭제하는 데 필요한 논리를 추가합니다.
 * 멤버 자격 삭제는 NAB 멤버 간에 작동하지 않습니다.
 * 값은 다중값 특성에서 성공적으로 삭제되어야 합니다.

## <a name="111170"></a>1.1.117.0
출시 날짜: 2016년 3월

**일반 SQL 커넥터**  
의 [일반 SQL 커넥터](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql)초기 릴리스입니다.

**새로운 기능:**

* 일반 LDAP 커넥터:
  * Isode에서 델타 가져오기에 대한 지원을 추가했습니다.
* 웹 서비스 커넥터:
  * 개체 수준 오류가 동기화 엔진으로 다시 반환될 수 있도록 csEntryChangeResult 작업 및 setImportErrorCode 작업을 업데이트했습니다.
  * 새 개체 수준 오류 기능을 사용하도록 SAP6 및 SAP6User 템플릿을 업데이트했습니다.
* Lotus Domino 커넥터:
  * 내보내는 경우 주소록당 하나의 인증자가 있어야 합니다. 이제 관리를 쉽게 수행할 수 있도록 모든 인증자에 대해 동일한 암호를 사용할 수 있습니다.

**수정된 문제:**

* 일반 LDAP 커넥터:
  * IBM Tivoli DS의 경우 일부 참조 특성이 제대로 검색되지 않았습니다.
  * 델타 가져오기 동안 열린 LDAP의 경우 문자열의 시작과 끝에 있는 공백이 잘렸습니다.
  * Novell과 NetIQ의 경우 OU/컨테이너 사이의 개체를 이동하고 동시에 개체의 이름도 바꾼 내보내기가 실패했습니다.
* 웹 서비스 커넥터:
  * 웹 서비스에 동일한 바인딩에 대한 여러 개의 끝점이 있으면 커넥터에서 이러한 끝점을 올바르게 검색하지 않았습니다.
* Lotus Domino 커넥터:
  * fullName 특성을 메일 내 데이터베이스에 내보내지 못했습니다.
  * 그룹에서 멤버를 추가하고 제거한 내보내기에서 추가된 멤버만 내보냈습니다.
  * Notes Document가 잘못된 경우(isValid 특성이 false로 설정) 커넥터가 실패합니다.

## <a name="older-releases"></a>이전 릴리스
2016년 3월 이전에 커넥터가 지원 항목으로 출시되었습니다.

**일반 LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, 2015년 9월
* [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, 2015년 3월
* [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, 2015년 1월
* [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, 2014년 9월
* [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, 2014년 3월

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, 2014년 9월

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, 2014년 9월

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, 2015년 9월
* [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, 2015년 3월
* [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, 2014년 8월
* [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, 2014년 2월  
* [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, 2013년 10월
* [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, 2013년 8월

## <a name="troubleshooting"></a>문제 해결 

> [!NOTE]
> ECMA2 커넥터 중 하나를 사용하여 Microsoft Identity Manager 또는 AADConnect를 업데이트하는 경우입니다. 

일치하도록 업그레이드할 때 커넥터 정의를 새로 고침해야 하거나, 응용 프로그램 이벤트 로그 시작 부분에서 경고 ID 6947을 보고하는 다음 오류를 수신합니다. "Assembly version in AAD Connector configuration ("X.X.XXX.X") is earlier than the actual version ("X.X.XXX.X") of "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll"("AAD 커넥터 구성의 어셈블리 버전("XXXXX.X")이 C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll의 실제 버전("X.X.XXX.X")보다 이전 버전입니다").

정의를 새로 고치려면:
* 커넥터 인스턴스의 속성 열기
* 연결/연결 대상 탭 클릭
  * 커넥터 계정 암호 입력
* 각 속성 탭을 차례로 클릭
  * 이 커넥터 유형에 새로 고침 단추가 포함된 파티션 탭이 있는 경우 해당 탭에서 새로 고침 단추를 클릭합니다.
* 모든 속성 탭에 액세스한 후 확인 단추를 클릭하여 변경 사항을 저장합니다.


## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.
