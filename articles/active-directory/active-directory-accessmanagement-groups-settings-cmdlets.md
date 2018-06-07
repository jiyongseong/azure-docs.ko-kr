---
title: Azure Active Directory에서 PowerShell을 사용하여 그룹 설정 구성 | Microsoft Docs
description: Azure Active Directory cmdlet을 사용하여 그룹 설정을 관리하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 02/20/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.openlocfilehash: d61907ddbd6eaecc8aeae8d2f1cea204f287daea
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>그룹 설정을 구성하는 Azure Active Directory cmdlets
이 문서에서는 Azure Active Directory(Azure AD) PowerShell cmdlet을 사용하여 그룹을 만들고 업데이트하는 방법을 설명합니다. 이 콘텐츠는 Office 365 그룹에만 적용됩니다(통합 그룹이라고도 함). 

> [!IMPORTANT]
> 일부 설정에는 Azure Active Directory Premium P1 라이선스가 필요합니다. 자세한 내용은 [템플릿 설정](#template-settings) 표를 참조하세요.

관리자가 아닌 사용자가 *보안* 그룹을 만들 수 없도록 하는 방법에 대한 자세한 내용은 [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)에 설명된 대로 `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False`를 설정하세요. 

Office 365 그룹 설정은 설정 개체와 SettingsTemplate 개체를 사용하여 구성됩니다. 처음에는 디렉터리가 기본 설정으로 구성되어 있으므로 디렉터리에 설정 개체가 표시되지 않습니다. 기본 설정을 변경하려면 설정 템플릿을 사용하여 새 설정 개체를 만들어야 합니다. 설정 템플릿은 Microsoft가 정의합니다. 여러 종류의 설정 템플릿이 있습니다. 디렉터리에 대한 Office 365 그룹 설정을 구성하려면 "Group.Unified" 템플릿을 사용하세요. 단일 그룹의 Office 365 그룹 설정을 구성하려면 "Group.Unified.Guest" 템플릿을 사용하세요. 이 템플릿은 Office 365 그룹에 대한 게스트 액세스 관리에 사용됩니다. 

cmdlet은 Azure Active Directory PowerShell V2 모듈의 일부입니다. 컴퓨터에 모듈을 다운로드하여 설치하는 방법에 대한 지침은 [Azure Active Directory PowerShell 버전 2](https://docs.microsoft.com/powershell/azuread/)를 참조하세요. 모듈의 버전 2 릴리스를 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAD/)에서 설치할 수 있습니다.

## <a name="retrieve-a-specific-settings-value"></a>특정 설정 값 검색
검색할 설정의 이름을 알고 있는 경우 아래 cmdlet을 사용하여 현재 설정 값을 검색할 수 있습니다. 이 예제에서는 "UsageGuidelinesUrl"이라는 설정의 값을 검색합니다. 향후 이 문서에서 디렉터리 설정 및 해당 이름에 대해 자세히 살펴볼 수 있습니다.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 만들기
다음 단계는 디렉터리 수준에서 설정을 만드는 것입니다. 이 설정은 디렉터리에 있는 모든 Office 365 그룹에 적용됩니다.

1. DirectorySettings cmdlet에서 사용하려는 SettingsTemplate의 ID를 지정해야 합니다. 이 ID를 모르면 cmdlet이 모든 설정 템플릿 목록을 반환합니다.
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  이 cmdlet을 호출하면 사용할 수 있는 모든 템플릿이 반환됩니다.
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. 사용 지침 URL을 추가하려면 사용 지침 URL 값을 정의하는 SettingsTemplate 개체를 가져와야 합니다. 즉, Group.Unified 템플릿입니다.
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. 다음에는 위 템플릿에 기초하여 새 설정 개체를 만듭니다.
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. 그런 다음 사용 지침 값을 업데이트합니다.
  
  ```
  $setting["UsageGuidelinesUrl"] = "https://guideline.com"
  ```  
5. 마지막으로 설정을 적용합니다.
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

성공적으로 완료되면 cmdlet이 새 설정 개체의 ID를 반환합니다.
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
## <a name="template-settings"></a>템플릿 설정
다음은 Group.Unified 설정 템플릿에서 정의된 설정입니다. 달리 명시되지 않은 한 이 기능에는 Azure Active Directory Premium P1 라이선스가 필요합니다. 

| **설정** | **설명** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>형식: Boolean<li>기본값: True |관리자가 아닌 사용자에 의해 디렉터리에서 Office 365 그룹 만들기가 허용되는지 여부를 나타내는 플래그입니다. 이 설정에는 Azure Active Directory Premium P1 라이선스가 필요하지 않습니다.|
|  <ul><li>GroupCreationAllowedGroupId<li>형식: String<li>기본값: “” |EnableGroupCreation == false일 때도 구성원이 Office 365 그룹을 만들도록 허용된 보안 그룹의 GUID입니다. |
|  <ul><li>UsageGuidelinesUrl<li>형식: String<li>기본값: “” |그룹 사용 지침 링크입니다. |
|  <ul><li>ClassificationDescriptions<li>형식: String<li>기본값: “” | 쉼표로 구분된 분류 설명 목록입니다. |
|  <ul><li>DefaultClassification<li>형식: String<li>기본값: “” | 설정이 지정되지 않은 경우에 그룹의 기본 분류로 사용되는 분류입니다.|
|  <ul><li>PrefixSuffixNamingRequirement<li>형식: String<li>기본값: “” | Office 365 그룹에 대해 구성된 명명 규칙을 정의하는 최대 64자의 문자열입니다. 자세한 내용은 [Office 365 그룹에 대한 명명 정책 적용(미리 보기)](groups-naming-policy.md)을 참조하세요. |
| <ul><li>CustomBlockedWordsList<li>형식: String<li>기본값: “” | 사용자가 그룹 이름 또는 별칭에서 사용하도록 허용되지 않은 쉼표로 구분된 구의 문자열입니다. 자세한 내용은 [Office 365 그룹에 대한 명명 정책 적용(미리 보기)](groups-naming-policy.md)을 참조하세요. |
| <ul><li>EnableMSStandardBlockedWords<li>형식: Boolean<li>기본값: "False" | 사용 안 함
|  <ul><li>AllowGuestsToBeGroupOwner<li>형식: Boolean<li>기본값: False | 게스트 사용자가 그룹의 소유자일 수 있는지 여부를 나타내는 부울 값입니다. |
|  <ul><li>AllowGuestsToAccessGroups<li>형식: Boolean<li>기본값: True | 게스트 사용자가 Office 365 그룹의 콘텐츠에 액세스할 수 있는지 여부를 나타내는 부울 값입니다.  이 설정에는 Azure Active Directory Premium P1 라이선스가 필요하지 않습니다.|
|  <ul><li>GuestUsageGuidelinesUrl<li>형식: String<li>기본값: “” | 게스트 사용 지침의 링크 url입니다. |
|  <ul><li>AllowToAddGuests<li>형식: Boolean<li>기본값: True | 이 디렉터리에 게스트를 추가하는 것이 허용되는지 여부를 나타내는 부울 값입니다.|
|  <ul><li>ClassificationList<li>형식: String<li>기본값: “” |Office 365 그룹에 적용할 수 있는 유효한 분류 값을 쉼표로 구분한 목록입니다. |

## <a name="read-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 읽기
다음 단계는 디렉터리 수준에서 설정을 읽는 것입니다. 이 설정은 디렉터리에 있는 모든 Office 그룹에 적용됩니다.

1. 모든 기존 디렉터리 설정 읽기:
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  이 cmdlet은 모든 디렉터리 설정 목록을 반환합니다.
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. 특정 그룹의 모든 설정 읽기:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. 설정 Id GUID를 사용하여 특정 디렉터리 설정 개체의 모든 디렉터리 설정 값을 읽습니다.
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  이 cmdlet은 이 특정 그룹에 대한 이 설정 개체의 이름과 값을 반환합니다.
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  CustomBlockedWordsList        
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            https://guideline.com
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>특정 그룹의 설정 업데이트

1. "Groups.Unified.Guest"라는 설정 템플릿 검색
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Groups.Unified.Guest 템플릿에 대한 템플릿 개체를 검색합니다.
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. 템플릿으로 새로운 설정 개체를 만듭니다.
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. 필요한 값의 설정을 지정합니다.
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. 디렉터리에 필요한 그룹의 새로운 설정을 만듭니다.
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 업데이트

다음 단계는 디렉터리 수준에서 설정을 업데이트하는 것입니다. 이 설정은 디렉터리에 있는 모든 Office 365 그룹에 적용됩니다. 다음 예에서는 이미 디렉터리에 설정 개체가 있는 것으로 가정합니다.

1. 기존 설정 개체를 찾습니다.
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. 값을 업데이트합니다.
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. 설정을 업데이트합니다.
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 제거
다음 단계는 디렉터리 수준에서 설정을 제거하는 것입니다. 이 설정은 디렉터리에 있는 모든 Office 그룹에 적용됩니다.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet 구문 참조
[Azure Active Directory Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0)에서 더 많은 Azure Active Directory PowerShell 설명서를 찾을 수 있습니다.

## <a name="additional-reading"></a>추가 참조 자료

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
