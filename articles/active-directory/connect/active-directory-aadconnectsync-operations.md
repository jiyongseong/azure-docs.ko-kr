---
title: "Azure AD Connect Sync: 운영 작업 및 고려 사항 | Microsoft Docs"
description: "이 항목에서는 Azure AD Connect Sync 및 이 구성 요소를 운영하기 위한 준비 방법에 대한 운영 작업을 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 0dfdae45ef7508337a1233c651d355d83b9f0430
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Azure AD Connect Sync: 운영 작업 및 고려 사항
이 항목은 Azure AD Connect Sync에 대한 관리 작업을 설명하는 것을 목표로 합니다.

## <a name="staging-mode"></a>스테이징 모드
다음을 비롯한 여러 시나리오에 스테이징 모드를 사용할 수 있습니다.

* 고가용성.
* 새 구성 변경 내용을 테스트하고 배포합니다.
* 새 서버를 도입하고 이전 서비스를 제거합니다.

스테이징 모드에 있는 서버로 서버를 활성화하기 전에 구성을 변경하고 변경을 미리 볼 수 있습니다. 또한 프로덕션 환경에 해당 변경 사항을 적용하기 전에 필요한지를 확인하기 위해 전체 가져오기 및 동기화를 실행할 수 있습니다.

설치 중에 서버를 선택하여 **스테이징 모드**로 둘 수 있습니다. 이 작업은 서버가 가져오기 및 동기화에 대해 활성화하도록 하지만 내보내기는 수행하지 않습니다. 설치 중에 이러한 기능을 선택하더라도 스테이징 모드에 있는 서버는 암호 동기화 또는 비밀번호 쓰기 저장을 실행하지 않습니다. 스테이징 모드를 해제하면 서버가 내보내기를 시작하고 암호 동기화 및 비밀번호 쓰기 저장을 사용하도록 설정합니다.

> [!NOTE]
> 암호 해시 동기화 기능이 사용되도록 설정된 Azure AD Connect를 사용한다고 가정합니다. 준비 모드에서 서버는 온-프레미스 AD의 암호 변경에 대한 동기화를 중지합니다. 준비 모드를 사용되지 않도록 설정하면 서버는 마지막에 중단한 암호 변경부터 동기화를 다시 시작합니다. 서버가 오랫 동안 준비 모드를 유지할 경우 서버가 해당 기간 동안 발생한 모든 암변호 경 내용을 동기화하는 데 다소 시간이 걸릴 수 있습니다.
>
>

여전히 동기화 서비스 관리자를 사용하여 강제로 내보낼 수 있습니다.

스테이징 모드에 있는 서버는 Active Directory 및 Azure AD에서 계속 변경 내용을 받습니다. 항상 최신 변경 내용의 복사본이 있고 빠르게 다른 서버의 책임을 넘겨 받을 수 있습니다. 기본 서버의 구성을 변경하는 경우 스테이징 모드에서 서버에 동일한 변경 내용을 적용해야 합니다.

이전 동기화 기술에 대해 알고 있는 사용자의 경우 다른 서버에 자체 SQL 데이터베이스가 있으므로 스테이징 모드는 다릅니다. 이 아키텍처를 통해 스테이징 모드 서버를 다른 데이터 센터에서 찾을 수 있습니다.

### <a name="verify-the-configuration-of-a-server"></a>서버의 구성 확인
이 메서드를 적용 하려면 다음 단계를 수행합니다.

1. [준비](#prepare)
2. [구성](#configuration)
3. [가져오기 및 동기화](#import-and-synchronize)
4. [Verify](#verify)
5. [활성 서버 전환](#switch-active-server)

#### <a name="prepare"></a>준비
1. Azure AD Connect를 설치하고 **스테이징 모드**를 선택하고 설치 마법사의 마지막 페이지에서 **동기화 시작**의 선택을 취소합니다. 이 모드를 통해 동기화 엔진을 수동으로 실행할 수 있습니다.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. 시작 메뉴에서 로그오프/로그온하고 **동기화 서비스**를 선택합니다.

#### <a name="configuration"></a>구성
주 서버에 사용자 지정 변경 내용을 적용했으며 구성을 스테이징 서버와 비교하려는 경우 [Azure AD Connect 구성 구조 분석](https://github.com/Microsoft/AADConnectConfigDocumenter)을 사용합니다.

#### <a name="import-and-synchronize"></a>가져오기 및 동기화
1. **커넥터**를 선택하고 **Active Directory Domain Services** 형식을 가진 첫 번째 커넥터를 선택합니다. **실행**을 클릭하고 **전체 가져오기** 및 **확인**을 선택합니다. 이 형식인 모든 커넥터에 해당 단계를 수행합니다.
2. **Azure Active Directory(Microsoft)**형식이 있는 커넥터를 선택합니다. **실행**을 클릭하고 **전체 가져오기** 및 **확인**을 선택합니다.
3. 탭 커넥터가 여전히 선택되어있는지 확인합니다. **Active Directory Domain Services** 형식인 각 커넥터의 경우 **실행**을 클릭하고 **델타 동기화** 및 **확인**을 선택합니다.
4. **Azure Active Directory(Microsoft)**형식이 있는 커넥터를 선택합니다. **실행**을 클릭하고 **델타 동기화** 및 **확인**을 차례로 선택합니다.

이제 Azure AD 및 온-프레미스 AD에 스테이징된 내보내기 변경 사항이 있습니다.(Exchange 하이브리드 배포를 사용하는 경우) 다음 단계를 사용하면 디렉터리에 실제로 내보내기를 시작하기 전에 무엇이 변경될지를 검사할 수 있습니다.

#### <a name="verify"></a>Verify
1. cmd 프롬프트를 시작하고 `%ProgramFiles%\Microsoft Azure AD Sync\bin`로 이동합니다.
2. 실행: `csexport "Name of Connector" %temp%\export.xml /f:x` 동기화 서비스에서 커넥터의 이름을 찾을 수 있습니다. Azure AD에 "contoso.com – AAD"와 유사한 이름이 있습니다.
3. 실행: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` %temp%에 Microsoft Excel에서 검사할 수 있는 export.csv라는 파일이 있습니다. 이 파일은 내보낼 수 있는 모든 변경 내용을 포함합니다.
4. 내보내려는 변경 사항이 예정될 때까지 데이터 또는 구성에 필요한 변경을 수행하고 이러한 단계(가져오기 및 동기화 및 확인)를 다시 실행합니다.

**export.csv 파일 이해** 파일은 대부분 따로 설명이 필요하지 않습니다. 콘텐츠를 이해하기 위한 일부 약어입니다.
* OMODT - 개체 수정 형식입니다. 개체 수준에서 작업이 추가, 업데이트 또는 삭제 중 어떤 것인지를 나타냅니다.
* AMODT - 특성 수정 형식입니다. 특성 수준에서 작업이 추가, 업데이트 또는 삭제 중 어떤 것인지를 나타냅니다.

**일반 식별자 검색** export.csv 파일에는 내보내려는 모든 변경 내용이 들어 있습니다. 각 행은 커넥터 공간에 있는 개체의 변경 사항에 해당하며 개체는 DN 특성으로 식별됩니다. DN 특성은 커넥터 공간에서 개체에 할당된 고유한 식별자입니다. export.csv에서 많은 행/변경 사항을 분석할 때 DN 특성만으로는 변경 사항이 있는 개체를 파악하기 어려울 수 있습니다. 변경 사항을 분석하는 과정을 단순화하려면 csanalyzer.ps1 PowerShell 스크립트를 사용합니다. 스크립트는 개체의 공통 식별자(예: displayName, userPrincipalName)를 검색합니다. 스크립트를 사용하려면 다음을 수행합니다.
1. 섹션 [CSAnalyzer](#appendix-csanalyzer)의 PowerShell 스크립트를 `csanalyzer.ps1`이라는 파일에 복사합니다.
2. PowerShell 창을 열고 PowerShell 스크립트를 만든 폴더로 이동합니다.
3. `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`을 실행합니다.
4. 이제 Microsoft Excel에서 검사할 수 있는 **processedusers1.csv**라는 파일이 있습니다. 이 파일은 DN 특성에서 공통 식별자(예: displayName 및 userPrincipalName)로의 매핑을 제공합니다. 현재는 내보내려는 실제 특성 변경 사항이 포함되어 있지 않습니다.

#### <a name="switch-active-server"></a>활성 서버 전환
1. Azure AD로 내보내지 않으므로 현재 활성 서버에서 서버를 해제하거나(DirSync/FIM Azure AD Sync) 스테이징 모드로 설정합니다(Azure AD Connect).
2. **스테이징 모드** 서버에서 설치 마법사를 실행하고 **스테이징 모드**를 사용하지 않도록 설정합니다.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>재해 복구
구현 설계의 일부는 동기화 서버를 분실한 경우 재해 발생 시 수행할 작업을 계획하는 것입니다. 사용할 다른 모델이 있고 어떤 것을 사용하는지는 다음을 포함한 여러 요인에 따라 달라집니다.

* 가동 중지 시간 동안 Azure AD에서 개체를 변경하지 않는 오차는 무엇입니까?
* 암호 동기화를 사용하면 사용자가 온-프레미스를 변경할 경우 Azure AD에서 이전 암호를 사용해야 합니까?
* 비밀번호 쓰기 저장 등의 실시간 작업에 대한 종속성이 있습니까?

이러한 질문 및 조직의 정책에 대한 답변에 따라 다음 전략 중 하나를 구현할 수 있습니다.

* 필요할 때 다시 작성합니다.
* **스테이징 모드**라고 하는 예비 대기 서버가 있습니다.
* 가상 머신을 사용합니다.

기본 제공 SQL Express 데이터베이스를 사용하지 않는 경우 [SQL 고가용성](#sql-high-availability) 섹션도 또한 검토해야 합니다.

### <a name="rebuild-when-needed"></a>필요할 때 다시 작성
실행 가능한 전략은 필요할 경우 서버를 다시 작성하려는 계획입니다. 일반적으로 동기화 엔진 설치와 초기 가져오기 및 동기화 수행은 몇 시간 안에 완료할 수 있습니다. 사용 가능한 예비 서버가 없는 경우 일시적으로 동기화 엔진을 호스팅하는 데 도메인 컨트롤러를 사용할 수 있습니다.

동기화 엔진 서버는 개체에 대한 상태를 저장하지 않으므로 Active Directory 및 Azure AD의 데이터에서 데이터베이스를 다시 빌드할 수 있습니다. **sourceAnchor** 특성을 온-프레미스 및 클라우드에서 개체를 조인하는데 사용합니다. 기존 온-프레미스 및 클라우드 개체로 서버를 다시 작성하는 경우 다시 설치 시 동기화 엔진은 해당 개체를 일치시킵니다. 문서화 및 저장해야 할 사항은 필터링 및 동기화 규칙 등과 같은 서버의 구성 변경입니다. 동기화를 시작하기 전에 해당 사용자 지정 구성을 다시 적용해야 합니다.

### <a name="have-a-spare-standby-server---staging-mode"></a>스테이징 모드라는 예비 대기 서버가 있습니다.
더 복잡한 환경을 사용하는 경우 하나 이상의 대기 서버가 있는 것이 좋습니다. 설치 중에 서버를 사용하도록 설정하여 **스테이징 모드**로 둘 수 있습니다.

자세한 내용은 [스테이징 모드](#staging-mode)를 참조하세요.

### <a name="use-virtual-machines"></a>가상 머신 사용
일반적이며 지원되는 메서드는 가상 머신에서 동기화 엔진을 실행하는 것입니다. 호스트에는 문제가 있는 경우 동기화 엔진 서버로 이미지를 다른 서버에 마이그레이션할 수 있습니다.

### <a name="sql-high-availability"></a>SQL 고가용성
Azure AD Connect와 함께 제공되는 SQL Server Express를 사용하지 않는 경우 SQL Server에 대한 고가용성을 고려해야 합니다. 지원되는 고가용성 솔루션은 SQL 클러스터링 및 AOA(Always On 가용성 그룹)를 포함합니다. 지원되지 않는 솔루션은 미러링을 포함합니다.

버전 1.1.524.0에서는 SQL AOA에 대한 지원이 Azure AD Connect에 추가되었습니다. Azure AD Connect를 설치하기 전에 SQL AOA를 사용하도록 설정해야 합니다. Azure AD Connect는 설치 중에 제공된 SQL 인스턴스에서 SQL AOA를 사용하도록 설정되었는지 여부를 검색합니다. SQL AOA를 사용하도록 설정된 경우 Azure AD Connect는 SQL AOA에서 동기 복제 또는 비동기 복제를 사용하도록 구성되어 있는지 확인합니다. 가용성 그룹 수신기를 설정할 때 RegisterAllProvidersIP 속성을 0으로 설정하는 것이 좋습니다. 이는 Azure AD Connect에서 현재 SQL Native Client를 사용하여 SQL에 연결하고, SQL Native Client에서는 MultiSubNetFailover 속성 사용을 지원하지 않기 때문입니다.

## <a name="appendix-csanalyzer"></a>Appendix CSAnalyzer
이 스크립트 사용 방법은 [verity](#verify) 섹션을 참조하세요.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as as CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>다음 단계
**개요 항목**  

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)  
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)  
