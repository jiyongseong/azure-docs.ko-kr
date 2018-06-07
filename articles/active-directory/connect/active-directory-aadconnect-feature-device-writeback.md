---
title: 'Azure AD Connect: 장치 쓰기 저장 사용 | Microsoft Docs'
description: 이 문서는 Azure AD Connect를 사용하여 장치 쓰기 저장을 사용하는 방법을 자세히 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: billmath
ms.openlocfilehash: c813be558df9dc3bdfd9850402b9458f1fdf971a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353820"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: 장치 쓰기 저장 사용
> [!NOTE]
> Azure AD Premium에 대한 구독에는 장치 쓰기 저장이 필요합니다.
> 
> 

다음 설명서에서는 Azure AD Connect에서 장치 쓰기 저장 기능을 사용하는 방법에 대한 정보를 제공합니다. 쓰기 저장 장치를 다음과 같은 시나리오에서 사용합니다.

* 장치에 따라 ADFS(2012 R2 이상) 보호된 응용 프로그램에 조건부 액세스를 사용하도록 설정합니다.(신뢰 당사자 트러스트)

응용 프로그램에 대한 액세스 권한이 신뢰할 수 있는 장치에 부여된 추가 보안 및 보증을 제공합니다. 조건부 액세스에 대한 자세한 내용은 [조건부 액세스로 위험 관리](../active-directory-conditional-access-azure-portal.md) 및 [Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정](../active-directory-conditional-access-automatic-device-registration-setup.md)을 참조하세요.

> [!IMPORTANT]
> <li>장치는 사용자와 동일한 포리스트에 있어야 합니다. 장치가 단일 포리스트에 쓰기 저장해야 하기 때문에 이 기능은 현재 여러 사용자 포리스트에서 배포를 지원하지 않습니다.</li>
> <li>온-프레미스 Active Directory 포리스트에 하나의 장치 등록 구성 개체를 추가할 수 있습니다. 이 기능은 온-프레미스 Active Directory가 여러 Azure AD 디렉터리에 동기화되는 토폴로지와 호환되지 않습니다.</li>> 

## <a name="part-1-install-azure-ad-connect"></a>1부: Azure AD Connect 설치
사용자 지정 또는 Express 설정을 사용하여 Azure AD Connect를 설치합니다. Microsoft는 장치 쓰기 저장을 사용하도록 설정하기 전에 모든 사용자 및 그룹을 성공적으로 동기화하고 시작할 것을 권장합니다.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>2부: Azure AD Connect에서 장치 쓰기 저장 사용
1. 설치 마법사를 다시 실행합니다. [추가 작업] 페이지에서 **장치 옵션 구성**을 선택하고 **다음**을 클릭합니다. 

    ![장치 옵션 구성](./media/active-directory-aadconnect-feature-device-writeback/deviceoptions.png)

    >[!NOTE]
    > 새로운 장치 구성 옵션은 버전 1.1.819.0 이상에서만 사용할 수 있습니다.

2. 장치 옵션 페이지에서 **장치 쓰기 저장 구성**을 선택합니다. **장치 쓰기 저장을 사용하지 않도록 설정** 옵션은 장치 쓰기 저장을 사용하기 전에는 사용할 수 없습니다. **다음**을 클릭하여 마법사의 다음 페이지로 이동합니다.
    ![장치 작업 선택](./media/active-directory-aadconnect-feature-device-writeback/configuredevicewriteback1.png)

3. 쓰기 저장 페이지에서 기본 장치 쓰기 저장 포리스트로 제공된 도메인이 표시됩니다.
   ![사용자 지정 설치 장치 쓰기 저장 대상 포리스트](./media/active-directory-aadconnect-feature-device-writeback/writebackforest.png)

4. **장치 컨테이너** 페이지에서는 active directory를 준비하는 다음과 같은 두 가지 옵션을 제공합니다.

    a. **엔터프라이즈 관리자 자격 증명 제공**: 장치를 다시 써야 하는 포리스트에 대한 엔터프라이즈 관리자 자격 증명을 제공하면 Azure AD Connect는 장치 쓰기 저장을 구성하는 동안 자동으로 포리스트를 준비합니다.

    나. **PowerShell 스크립트 다운로드**: Azure AD Connect는 장치 쓰기 저장에 대한 active directory를 준비할 수 있는 PowerShell 스크립트를 자동으로 생성합니다. Azure AD Connect에서 엔터프라이즈 관리자 자격 증명을 제공할 수 없는 경우 PowerShell 스크립트를 다운로드하면 됩니다. 장치를 다시 써야 하는 포리스트의 엔터프라이즈 관리자에게 다운로드한 **CreateDeviceContainer.psq** PowerShell 스크립트를 제공합니다.
    ![active diretory 포리스트 준비](./media/active-directory-aadconnect-feature-device-writeback/devicecontainercreds.png)
    
    active directory 포리스트를 준비하기 위해 다음 작업이 수행됩니다.
    * 존재하지 않는 경우 CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn]에서 새 컨테이너 및 개체를 생성하고 구성합니다.
    * 존재하지 않는 경우 CN=RegisteredDevices,[domain-dn]에서 새 컨테이너 및 개체를 생성하고 구성합니다. 이 컨테이너에서 장치 개체를 만듭니다.
    * Active Directory에서 장치를 관리하려면 Azure AD 커넥터 계정에 필요한 사용 권한을 설정합니다.
    * Azure AD Connect가 여러 포리스트에 설치되었더라도 하나의 포리스트에서 실행해야 합니다.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>장치의 Active Directory에 동기화 여부 확인
장치 쓰기 저장은 이제 제대로 작동해야 합니다. AD에 장치 개체를 다시 쓰기 저장하는 데 최대 3시간이 걸릴 수 있습니다.  장치가 제대로 동기화되었는지 확인하려면 동기화 규칙을 완료한 후에 다음을 수행합니다.

1. Active Directory 관리 센터를 시작합니다.
2. 페더레이션된 도메인 내에서 RegisteredDevices를 확장합니다.

   ![Active Directory 관리 센터 등록 장치](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)

3. 현재 등록된 장치가 나열됩니다.

   ![Active Directory 관리 센터 등록 장치 목록](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>조건부 액세스 사용
이 시나리오를 사용하기 위한 자세한 지침은 [Azure Active Directory 장치 등록을 사용하여 온-프레미스 조건부 액세스 설정](../active-directory-conditional-access-automatic-device-registration-setup.md)내에서 사용할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결
### <a name="the-writeback-checkbox-is-still-disabled"></a>쓰기 저장 확인란이 계속 비활성화되어 있음
위 단계를 수행했는데도 장치 쓰기 저장 확인란이 활성화되지 않는 경우 다음 단계에 따르면 상자가 활성화되기 전에 설치 마법사가 확인하는 사항을 알 수 있습니다.

먼저 첫 번째로

* 하나 이상의 포리스트에 Windows Server 2012R2가 있는지 확인합니다. 장치 개체 유형이 있어야 합니다.
* 설치 마법사가 이미 실행 중인 경우 변경 내용이 검색되지 않습니다. 이 경우 설치 마법사를 완료하고 다시 실행하세요.
* 초기화 스크립트에서 제공하는 계정이 Active Directory Connector에서 사용하는 올바른 사용자인지 확인합니다. 이를 확인하려면 다음 단계를 수행하세요.
  * 시작 메뉴에서 **동기화 서비스**를 엽니다.
  * **커넥터** 탭을 엽니다.
  * 형식이 Active Directory Domain Services인 커넥터를 찾아 선택합니다.
  * **작업** 아래에서 **속성**을 선택합니다.
  * **Active Directory 포리스트에 연결**로 이동합니다. 이 화면에 지정된 도메인 및 사용자 이름이 스크립트에 제공된 계정과 일치하는지 확인합니다.
    ![동기화 서비스 관리자의 커넥터 계정](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Active Directory의 구성 확인:

* 장치 등록 서비스가 구성 명명 컨텍스트의 (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) 아래 위치에 있는지 확인합니다.

![문제 해결, 구성 네임스페이스의 DeviceRegistrationService](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

* 구성 네임스페이스를 검색하여 구성 개체가 하나만 있는지 확인합니다. 구성 개체가 둘 이상인 경우 중복되는 항목을 삭제합니다.

![문제 해결, 중복 개체 검색](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

* Device Registration Service 개체에서 msDS-DeviceLocation 특성이 존재하며 값이 있는지 확인합니다. 이 위치를 조회하고 objectType msDS-DeviceContainer와 함께 있는지 확인합니다.

![문제 해결, msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![문제 해결, RegisteredDevices 개체 클래스](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

* Active Directory Connector에서 사용하는 계정에 이전 단계에서 찾은 등록된 장치 컨테이너에 대해 필요한 권한이 있는지 확인합니다. 다음은 이 컨테이너에 대해 예상되는 권한입니다.

![문제 해결, 컨테이너에 대한 권한 확인](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

* Active Directory 계정에 CN=Device Registration Configuration,CN=Services,CN=Configuration 개체에 대한 권한이 있는지 확인합니다.

![문제 해결, 장치 등록 구성에 대한 권한 확인](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>추가 정보
* [조건부 액세스를 사용한 위험 관리](../active-directory-conditional-access-azure-portal.md)
* [Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정](../active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

