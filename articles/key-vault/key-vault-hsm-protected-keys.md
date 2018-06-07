---
title: "Azure Key Vault에 HSM 보호 키를 생성하고 전송하는 방법 | Microsoft Docs"
description: "이 문서를 통해 Azure Key Vault에서 사용할 고유의 HSM 보호 키를 생성하고 전송하는 데 필요한 계획을 세울 수 있습니다. BYOK, 즉 Bring Your Own Key라고도 합니다."
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: barclayn
ms.openlocfilehash: 0d34a19658ae67a9c98d6f31aaca35e67add5beb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2017
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Azure Key Vault에 대해 HSM 보호된 키를 생성하고 전송하는 방법
## <a name="introduction"></a>소개
보안을 강화하기 위해 Azure Key Vault 사용 시 HSM 경계를 절대로 벗어나지 않고 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이 시나리오를 흔히 BYOK( *Bring Your Own Key*)라고 합니다. HSM은 FIPS 140-2 Level 2 유효성 검사가 적용됩니다. Azure Key Vault는 HSM의 Thales nShield 제품군을 사용하여 키를 보호합니다.

이 토픽의 내용을 통해 Azure Key Vault에서 사용할 고유의 HSM 보호된 키를 생성하고 전송하는 데 필요한 계획을 세울 수 있습니다.

이 기능은 Azure 중국에 사용할 수 없습니다.

> [!NOTE]
> Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](key-vault-whatis.md)  
>
> HSM 보호된 키를 위해 주요 자격 증명 모음 만들기가 포함된 자습서를 시작하려면 [Azure Key Vault 시작](key-vault-get-started.md)을 참조하세요.
>
>

다음은 인터넷을 통해 HSM 보호된 키를 생성하고 전송하는 작업에 대한 추가 정보입니다.

* 공격에 대한 취약성을 줄이기 위해 오프라인 워크스테이션에서 키를 생성합니다.
* 키는 KEK(키 교환 키)로 암호화되어 Azure Key Vault HSM으로 전송될 때까지 암호화 상태를 유지합니다. 암호화된 버전의 키만 원래 워크스테이션을 벗어납니다.
* 도구 집합은 Azure Key Vault 보안 영역에 키를 바인딩하는 테넌트 키에 대한 속성을 설정합니다. 따라서 Azure Key Vault HSM이 키를 받고 암호를 해독한 후에는 이 HSM만 사용할 수 있습니다. 키는 내보낼 수 없습니다. 이 바인딩은 Thales HSM에 의해 적용됩니다.
* 키 암호화에 사용되는 KEK(키 교환 키)는 Azure Key Vault HSM 내에서 생성되며 내보낼 수 없습니다. HSM은 HSM 외부에 클리어 버전의 KEK가 있을 수 없도록 강제합니다. 또한 도구 집합에는 KEK는 내보낼 수 없으며 Thales에서 제조한 정품 HSM 내에서 생성된 것이라는 Thales의 증명이 포함되어 있습니다.
* 도구 집합에는 Azure Key Vault 보안 영역도 Thales에서 제조한 정품 HSM에서 생성된 것이라는 Thales의 증명이 포함되어 있습니다. 이를 통해 Microsoft가 정품 하드웨어를 사용하고 있다는 것을 입증합니다.
* Microsoft는 각 지역별로 별도의 보안 권역과 별도의 KEK를 사용합니다. 이러한 구분을 통해 해당 키가 사용자가 암호화한 지역의 데이터 센터에서만 사용되게 할 수 있습니다. 예를 들어 유럽 고객의 키는 북아메리카 또는 아시아의 데이터 센터에서 사용할 수 없습니다.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Thales HSM 및 Microsoft 서비스에 대한 추가 정보
Thales e-Security는 금융 서비스, 첨단 기술, 제조, 정부 및 기술 분야에서 데이터 암호화 및 사이버 보안 솔루션을 제공하는 선도적인 글로벌 기업입니다. 기업과 정부의 정보를 보호하는 지난 40년간 누적된 성과를 바탕으로, Thales 솔루션은 최대 에너지 및 항공 우주 기업 다섯 곳 중 네 곳에서 사용되고 있습니다. 또한 22개 NATO 국가에서 사용되고 있으며 전세계 지불 거래의 80퍼센트 이상의 보안을 담당하고 있습니다.

Microsoft는 Thales과의 협력을 통해 HSM을 최첨단 상태로 향상시켰습니다. 이렇게 향상된 기능을 통해 사용자는 자신의 키에 대한 제어를 포기하지 않으면서 호스트된 서비스의 일반적인 이점을 누릴 수 있습니다. Microsoft에서 이러한 향상된 기능을 사용하여 HSM을 관리해 주므로 이 부분에 신경 쓰지 않아도 된다는 것이 특별한 장점입니다. 클라우드 서비스로써 Azure Key Vault는 급박한 요청에도 크기 확장이 가능하기 때문에 조직의 급증하는 사용량을 맞출 수 있습니다. 그와 동시에 키는 Microsoft의 HSM 내에서 보호됩니다. 키를 생성하고 Microsoft의 HSM에 전송하기 때문에 키의 수명 주기 동안 사용자가 키에 대한 제어를 유지할 수 있습니다.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Azure Key Vault에 대한 BYOK(Bring Your Own Key) 구현
고유의 HSM 보호된 키를 생성한 다음 Azure Key Vault로 전송하는 경우(BYOK(Bring Your Own Key) 시나리오) 다음 내용 및 절차를 사용합니다.

## <a name="prerequisites-for-byok"></a>BYOK에 대한 필수 조건
Azure Key Vault에 대해 BYOK(Bring Your Own Key)를 위한 필수 조건 목록은 다음 표를 참조하세요.

| 요구 사항 | 자세한 정보 |
| --- | --- |
| Azure 구독 |Azure Key Vault를 만들려면 Azure 구독이 필요합니다([무료 평가판 가입](https://azure.microsoft.com/pricing/free-trial/)). |
| HSM 보호 키를 지원하는 Azure Key Vault 프리미엄 서비스 계층 |Azure Key Vault에 대한 서비스 계층 및 기능에 대한 자세한 내용은 [Azure Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/) 웹 사이트를 참조하세요. |
| Thales HSM, 스마트 카드 및 지원 소프트웨어 |Thales 하드웨어 보안 모듈에 대한 액세스 권한 및 Thales HSM의 기본 작동 지식이 있어야 합니다. 호환되는 모델 목록을 보거나, HSM이 없는 경우 구매하려면 [Thales 하드웨어 보안 모듈](https://www.thales-esecurity.com/msrms/buy) 을 참조하세요. |
| 다음 하드웨어 및 소프트웨어:<ol><li>Windows 운영 체제 Windows 7 이상 및 Thales nShield 소프트웨어 버전 11.50 이상이 설치된 오프라인 x64 워크스테이션.<br/><br/>이 워크스테이션에서 Windows 7을 실행하는 경우 [Microsoft.NET Framework 4.5를 설치](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)해야 합니다.</li><li>인터넷에 연결되어 있으며 Windows 7 이상의 Windows 운영 체제 및 [Azure PowerShell](/powershell/azure/overview) **최소 버전 1.1.0**이 설치된 워크스테이션</li><li>여유 공간이 16MB 이상인 USB 드라이브 또는 기타 휴대용 저장 장치 </li></ol> |보안상의 이유로 첫 번째 워크스테이션은 네트워크에 연결하지 않는 것이 좋습니다. 그러나 이 권고는 프로그램 방식으로 강제 적용되지는 않습니다.<br/><br/>이후의 지침에서는 이 워크스테이션을 분리된 워크스테이션이라 합니다.</p></blockquote><br/>또한 테넌트 키가 프로덕션 네트워크용인 경우 별도의 두 번째 워크스테이션을 사용하여 도구 집합을 다운로드하고 테넌트 키를 업로드하는 것이 좋습니다. 그러나 테스트 목적인 경우에는 첫 번째와 동일한 워크스테이션을 사용할 수 있습니다.<br/><br/>이후의 지침에서는 이 두 번째 워크스테이션을 인터넷에 연결된 워크스테이션이라 합니다.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>키 생성 및 Azure Key Vault에 전송
다음 5단계에 따라 키를 생성하여 Azure Key Vault HSM으로 보냅니다.

* [1단계: 인터넷에 연결된 워크스테이션 준비](#step-1-prepare-your-internet-connected-workstation)
* [2단계: 연결이 끊어진 워크스테이션 준비](#step-2-prepare-your-disconnected-workstation)
* [3단계: 키 생성](#step-3-generate-your-key)
* [4단계: 전송할 키 준비](#step-4-prepare-your-key-for-transfer)
* [5단계: Azure Key Vault에 키 전송](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>1단계: 인터넷에 연결된 워크스테이션 준비
이 첫 번째 단계는 인터넷에 연결된 워크스테이션에서 다음 절차를 수행합니다.

### <a name="step-11-install-azure-powershell"></a>1.1단계: Azure PowerShell 설치
인터넷에 연결된 워크스테이션에서 Azure Key Vault를 관리하기 위해 cmdlet이 포함된 Azure PowerShell 모듈을 다운로드하고 설치합니다. 이를 위해 0.8.13 이상 버전이 필요합니다.

설치 지침은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

### <a name="step-12-get-your-azure-subscription-id"></a>1.2단계: Azure 구독 ID 얻기
Azure PowerShell 세션을 시작하고 다음 명령을 사용하여 Azure 계정에 로그인합니다.

```Powershell
   Add-AzureAccount
```
팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. 그런 다음 [Get-azuresubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) 명령을 사용합니다.

```powershell
   Get-AzureSubscription
```
출력된 내용에서 Azure Key Vault에 사용할 구독 ID를 찾습니다. 이 구독 ID는 나중에 필요합니다.

Azure PowerShell 창을 닫지 마세요.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>1.3단계: Azure Key Vault에 대한 BYOK 도구 집합 다운로드
Microsoft 다운로드 센터로 이동하여 해당 지리적 지역 또는 Azure 인스턴스에 대한 [Azure Key Vault BYOK 도구 집합을 다운로드](http://www.microsoft.com/download/details.aspx?id=45345) 합니다. 다음 정보를 사용하여 패키지 이름을 식별하고 해당 SHA-256 패키지 해시를 다운로드합니다.

- - -
**미국:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

- - -
**유럽:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

- - -
**아시아:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

- - -
**라틴 아메리카:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

- - -
**일본:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

- - -
**한국:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

- - -
**오스트레일리아:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

- - -
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

- - -
**미국 정부 DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

- - -
**캐나다:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

- - -
**독일:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

- - -
**인도:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

- - -
**영국:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

- - -

다운로드한 BYOK 도구 집합의 무결성을 확인하려면 Azure PowerShell 세션에서 [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) cmdlet을 사용합니다.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

도구 집합에는 다음이 포함되어 있습니다.

* 이름이 **BYOK-KEK-pkg-**
* 이름이 **BYOK-SecurityWorld-pkg-**
* 이름이 **verifykeypackage.py**인 python 스크립트
* 이름이 **KeyTransferRemote.exe** 인 명령줄 실행 파일 및 관련 DLL
* 이름이 **vcredist_x64.exe**인 Visual C++ 재배포 가능 패키지

USB 드라이브 또는 기타 휴대용 저장소에 패키지를 복사합니다.

## <a name="step-2-prepare-your-disconnected-workstation"></a>2단계: 연결이 끊어진 워크스테이션 준비
이 두 번째 단계에서는 네트워크(인터넷 또는 내부 네트워크)에 연결되지 않은 워크스테이션에서 다음 절차를 수행합니다.

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>2.1단계: Thales HSM이 있는 연결이 끊어진 워크스테이션 준비
Windows 컴퓨터에 nCipher(Thales) 지원 소프트웨어를 설치한 다음 Thales HSM을 해당 컴퓨터에 연결합니다.

Thales 도구가 해당 경로(**%nfast_home%\bin**)에 있는지 확인합니다. 예를 들어 다음을 입력합니다.

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

자세한 내용은 Thales HSM에 포함된 사용자 가이드를 참조하세요.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>2.2단계: 연결이 끊어진 워크스테이션에 BYOK 도구 집합 설치
USB 드라이브 또는 기타 휴대용 저장소에서 BYOK 도구 집합 패키지를 복사한 후 다음을 수행합니다.

1. 다운로드한 패키지에서 임의 폴더로 파일을 추출합니다.
2. 해당 폴더에서 vcredist_x64.exe를 실행합니다.
3. 지침에 따라 Visual Studio 2013용 Visual C++ 런타임 구성 요소를 설치합니다.

## <a name="step-3-generate-your-key"></a>3단계: 키 생성
이 3단계에서는 연결이 끊어진 워크스테이션에서 다음 절차를 수행합니다. 이 단계를 완료하려면 HSM이 초기화 모드에 있어야 합니다. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>3.1단계: HSM 모드를 'I'로 변경
Thales nShield Edge를 사용하는 경우 모드를 1로 변경합니다. 모드 단추를 사용하여 필요한 모드를 강조 표시합니다. 2. 몇 초 안에 지우기 단추를 몇 초간 길게 누릅니다. 모드가 변경되면 새로운 모드의 LED가 깜박임을 중지하고 켜져 있습니다. 상태 LED가 몇 초간 불규칙하게 깜박일 수도 있으며, 장치가 준비되면 정기적으로 깜박입니다. 그렇지 않으면 장치가 현재 모드로 유지되고 해당 모드 LED가 켜져 있습니다.

### <a name="step-32-create-a-security-world"></a>3.2단계: 보안 영역 만들기
명령 프롬프트를 시작하고 Thales의 새 영역 프로그램을 실행합니다.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3
   ```

이 프로그램은 C:\ProgramData\nCipher\Key Management Data\local 폴더에 해당하는 %NFAST_KMDATA%\local\world에 **Security World** 파일을 만듭니다. 쿼럼에 다른 값을 사용할 수 있지만, 이 예에서는 각각에 대해 3개의 빈 카드와 핀을 입력하라는 메시지가 표시됩니다. 그러면 임의의 두 카드에서 보안 영역에 대한 모든 권한을 제공합니다. 이러한 카드가 새 보안 영역에 대한 **관리자 카드 집합** 이 됩니다.

그런 다음 아래 작업을 수행합니다.

* 영역 파일을 백업합니다. 영역 파일, 관리자 카드, 해당 핀을 보호하고 한 사람이 둘 이상의 카드에 액세스 권한을 가지지 않도록 합니다.

### <a name="step-33-change-the-hsm-mode-to-o"></a>3.3단계: HSM 모드를 'O'로 변경
Thales nShield Edge를 사용하는 경우 모드를 1로 변경합니다. 모드 단추를 사용하여 필요한 모드를 강조 표시합니다. 2. 몇 초 안에 지우기 단추를 몇 초간 길게 누릅니다. 모드가 변경되면 새로운 모드의 LED가 깜박임을 중지하고 켜져 있습니다. 상태 LED가 몇 초간 불규칙하게 깜박일 수도 있으며, 장치가 준비되면 정기적으로 깜박입니다. 그렇지 않으면 장치가 현재 모드로 유지되고 해당 모드 LED가 켜져 있습니다.


### <a name="step-34-validate-the-downloaded-package"></a>3.4단계: 다운로드한 패키지의 유효성 검사
이 단계는 선택 사항이지만 다음 사항을 확인할 수 있으므로 권장됩니다.

* 도구 집합에 포함된 키 교환 키가 정품 Thales HSM에서 생성되었습니다.
* 도구 집합에 포함된 Security World의 해시가 정품 Thales HSM에서 생성되었습니다.
* 키 교환 키를 내보낼 수 없습니다.

> [!NOTE]
> 다운로드한 패키지의 유효성을 검사하려면 HSM이 연결되고 전원이 켜져 있어야 하며 그 안에 지금 만든 것 같은 보안 영역이 있어야 합니다.
>
>

다운로드한 패키지의 유효성을 검사하려면:

1. 해당하는 지리적 지역 또는 Azure 인스턴스에 따라 다음 중 하나를 입력하여 verifykeypackage.py 스크립트를 실행합니다.

   * 북아메리카:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * 유럽:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * 아시아:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * 라틴 아메리카:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * 일본:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * 한국:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * 오스트레일리아:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * [Azure Government](https://azure.microsoft.com/features/gov/)(Azure의 미국 정부 인스턴스 사용):

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * 미국 정부 DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * 캐나다:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * 독일:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * 인도:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * 영국:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1

     > [!TIP]
     > Thales 소프트웨어에는 %NFAST_HOME%\python\bin에 python이 포함되어 있습니다. 
     >
     >
2. 다음 내용이 표시되는지 확인합니다. 이는 유효성 검사가 성공했다는 것입니다. **Result: SUCCESS**

이 스크립트는 서명자 체인의 유효성을 Thales 루트 키까지 검사합니다. 이 루트 키의 해시는 스크립트에 포함되어 있으며 해당 값은 **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**여야 합니다. [Thales 웹 사이트](http://www.thalesesec.com/)에서 이 값을 개별적으로 확인할 수도 있습니다.

이제 새 키를 만들 준비가 되었습니다.

### <a name="step-35-create-a-new-key"></a>3.5단계: 새 키 만들기
Thales **generatekey** 프로그램을 사용하여 키를 생성합니다.

다음 명령을 실행하여 키를 생성합니다.

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

이 명령을 실행할 때 다음 지침을 사용합니다.

* 매개 변수 *보호* 는 다음과 같이 값 **모듈**에 설정되어야 합니다. 이렇게 하면 모듈 보호 키가 만들어집니다. BYOK 도구 집합은 OCS 보호 키를 지원하지 않습니다.
* **ident** 및 **plainname**의 *contosokey* 값을 임의의 문자열 값으로 바꿉니다. 관리 오버헤드를 최소화하고 오류 위험성을 줄이려면 두 가지에 동일한 값을 사용하는 것이 좋습니다. **ident** 값은 숫자, 대시 및 소문자만 포함해야 합니다.
* pubexp는 이 예에서 비어 있지만(기본값) 특정 값을 지정할 수 있습니다. 자세한 내용은 Thales 설명서를 참조하세요.

이 명령은 %NFAST_KMDATA%\local 폴더에 토큰화된 키 파일을 만듭니다. 이 파일은 이름이 **key_simple_**로 시작하며 명령에서 지정한 **ident**가 뒤에 붙습니다. 예들 들어 **key_simple_contosokey**입니다. 이 파일은 암호화된 키를 포함합니다.

안전한 위치에 이 토큰화된 키 파일을 백업합니다.

> [!IMPORTANT]
> 나중에 Azure Key Vault에 키를 전송하는 경우 Microsoft에서는 이 키를 사용자에게 다시 내보낼 수 없으므로 키 및 보안 영역을 안전하게 백업하는 것이 매우 중요합니다. 키 백업에 대한 지침 및 모범 사례는 Thales에 문의하세요.
>
>

이제 Azure Key Vault에 키를 전송할 준비가 되었습니다.

## <a name="step-4-prepare-your-key-for-transfer"></a>4단계: 전송할 키 준비
이 4단계에서는 연결이 끊어진 워크스테이션에서 다음 절차를 수행합니다.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>4.1단계: 축소된 권한을 가진 키의 복사본을 만듭니다.

새 명령 프롬프트를 열고 현재 디렉터리를 BYOK zip 파일의 압축이 풀린 위치로 변경합니다. 키에 대한 권한을 축소하려면 명령 프롬프트에서 해당 지리적 지역 또는 Azure 인스턴스에 따라 다음 중 하나를 실행합니다.

* 북아메리카:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* 유럽:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* 아시아:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* 라틴 아메리카:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* 일본:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* 한국:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* 오스트레일리아:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* [Azure Government](https://azure.microsoft.com/features/gov/)(Azure의 미국 정부 인스턴스 사용):

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* 미국 정부 DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* 캐나다:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* 독일:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* 인도:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* 영국:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1

이 명령을 실행할 때 [키 생성](#step-3-generate-your-key) 단계의 **3.5단계: 새 키 만들기**에서 지정한 값과 동일한 값으로 *contosokey*를 바꿉니다.

보안 영역 관리자 카드를 플러그인하라는 메시지가 표시됩니다.

명령이 완료되면 **Result: SUCCESS**가 표시되고 축소된 권한을 가진 키 복사본이 key_xferacId_<contosokey>라는 파일에 저장됩니다.

Thales 유틸리티에서 다음 명령을 사용하여 ACL을 검사할 수 있습니다.

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  이러한 명령을 실행할 때 [키 생성](#step-3-generate-your-key) 단계의 **3.5단계: 새 키 만들기**에서 지정한 값과 동일한 값으로 contosokey를 바꿉니다.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>4.2단계: Microsoft의 키 교환 키를 사용하여 키 암호화
지리적 지역 또는 Azure의 인스턴스에 따라 다음 명령 중 하나를 실행합니다.

* 북아메리카:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 유럽:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 아시아:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 라틴 아메리카:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 일본:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 한국:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 오스트레일리아:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* [Azure Government](https://azure.microsoft.com/features/gov/)(Azure의 미국 정부 인스턴스 사용):

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 미국 정부 DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 캐나다:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 독일:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 인도:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 영국:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

이 명령을 실행할 때 다음 지침을 사용합니다.

* [키 생성](#step-3-generate-your-key) 단계의 **3.5단계: 새 키 만들기**에서 키를 생성하기 위해 사용한 식별자로 *contosokey*를 바꿉니다.
* *SubscriptionID* 를 주요 자격 증명 모음이 포함된 Azure 구독 ID로 바꿉니다. **인터넷에 연결된 워크스테이션 준비** 단계의 [1.2단계: Azure 구독 ID 가져오기](#step-1-prepare-your-internet-connected-workstation) 에서 이 값을 검색했습니다.
* *ContosoFirstHSMKey*를 출력 파일 이름에 사용할 레이블로 바꿉니다.

이 작업이 성공적으로 완료되면 **Result: SUCCESS**가 표시되고 현재 폴더에 KeyTransferPackage-*ContosoFirstHSMkey*.byok라는 이름의 새 파일이 생성됩니다.

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>4.3단계: 인터넷에 연결된 워크스테이션에 키 전송 패키지 복사
USB 드라이브 또는 기타 휴대용 저장소를 사용하여 인터넷에 연결된 워크스테이션에 이전 단계의 출력 파일(KeyTransferPackage-ContosoFirstHSMkey.byok)을 복사합니다.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>5단계: Azure Key Vault에 키 전송
이 마지막 단계에서는 인터넷에 연결된 워크스테이션에서 [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) cmdlet을 사용하여 연결이 끊어진 워크스테이션에서 Azure Key Vault HSM으로 복사한 키 전송 패키지를 업로드합니다.

   ```powershell
        Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

업로드가 성공하면 방금 추가한 키의 속성이 표시됩니다.

## <a name="next-steps"></a>다음 단계
이제 주요 자격 증명 모음에서 이 HSM 보호된 키를 사용할 수 있습니다. 자세한 내용은 **Azure Key Vault 시작** 자습서에서 [HSM(하드웨어 보안 모듈)을 사용하려는 경우](key-vault-get-started.md) 를 참조하세요.
