---
title: Azure 스택 원격 분석 | Microsoft Docs
description: PowerShell을 사용 하 여 Azure 스택 원격 분석 설정을 구성 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: bfd16901c5ce036719a1ed19e9a5b5c6ef52be93
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="azure-stack-telemetry"></a>Azure 스택 원격 분석

Azure 스택 시스템 데이터 또는 원격 분석, 연결 된 사용자 경험을 통해 Microsoft에 자동으로 업로드 됩니다. Azure 스택 원격 분석에서 수집한 데이터는 Microsoft 팀이 고객 환경을 개선 하기 위해 주로 보안, 상태, 품질 및 성능 분석을 위해 사용 됩니다.

Azure 스택 연산자로 원격 분석 엔터프라이즈 배포에 대 한 귀중 한 통찰력을 제공할 수 있습니다 및 셰이프 이후 버전의 Azure 스택을 도움이 되는 음성을 제공 합니다.

> [!NOTE]
> 또한 azure 스택 대금 청구를 위해 전달 사용 정보를 Azure 구성할 수 있습니다. 이것이 사용량 기준 과금-으로--사용 요금 청구 선택 Multi-node Azure 스택 고객을 위해 필요 합니다. 사용 현황 보고 원격 분석에서 독립적으로 제어 됩니다 및 다중 노드 고객에 게 용량 모델을 선택 하거나 사용자가 Azure 스택 개발 키트에 대 한 필요 하지 않습니다. 이러한 시나리오에 대 한 사용 보고 설정 해제할 수 있습니다 [등록 스크립트를 사용 하 여](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting)합니다.

Azure 스택 원격 분석을 사용 하는 Windows Server 2016 연결 된 사용자 환경 및 원격 분석 구성에 따라는 [이벤트 추적에 대 한 ETW (Windows)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) 추적 로깅 기술 수집 하 고 원격 분석 이벤트 및 데이터를 저장 합니다. 이벤트 및 public 운영 체제 이벤트 로깅 및 추적 Api를 사용 하 여 수집한 데이터를 게시 하려면 같은 로깅 기술을 사용 하는 azure 스택 구성 요소입니다. Azure 스택 구성 요소의 예로 네트워크 리소스 공급자, 저장소 리소스 공급자, 리소스 공급자 모니터링 및 업데이트 리소스 공급자를 들 수 있습니다. 사용자 환경 연결 및 원격 분석 구성 요소는 SSL을 사용 하 여 데이터를 암호화 하 고 Microsoft 데이터 관리 서비스에 HTTPS를 통해 원격 분석 데이터를 전송 하는 데 인증서 고정 기능을 사용 합니다.

> [!NOTE]
> 원격 분석 데이터 흐름을 지원 하려면 포트 443 (HTTPS) 네트워크에서 열려 있어야 합니다. 사용자 환경 연결 및 원격 분석 구성 요소에는 Microsoft 데이터 관리 서비스에 연결 https://v10.vortex-win.data.microsoft.com합니다. 사용자 환경 연결 및 원격 분석 구성 요소에도 연결 https://settings-win.data.microsoft.com 구성 정보를 다운로드 합니다.

## <a name="privacy-considerations"></a>개인정보처리방침 고려 사항
ETW 서비스 원격 분석 데이터를 보호 된 클라우드 저장소 다시 라우팅합니다. 원격 분석 데이터에 대 한 최소 권한이 가진된 가이드 액세스의 원칙입니다. 유효한 비즈니스 요구를 사용 하 여 Microsoft 직원만 원격 분석 데이터에 대 한 액세스를 허용 됩니다. Microsoft 공유 하지 않습니다 제 3 자와 고객의 개인 데이터를 제외 하 고 고객의 판단에 따라 또는에서 설명 하는 제한 된 용도로 [Azure 스택 개인정보취급방침](https://privacy.microsoft.com/PrivacyStatement)합니다. Oem 및 집계, 익명화 된 원격 분석 정보를 포함 하는 파트너와 비즈니스 보고서를 공유지 않습니다 했습니다. 의사 결정을 공유 하는 데이터 개인 정보, 법률 및 데이터 관리 관련자를 포함 하는 내부 Microsoft 팀에서 수행 됩니다.

Microsoft에서이 있다고 인식 하 고 정보 최소화 사례입니다. 필요한 및 범위 내 에서만 서비스를 제공 하는 데 필요한 것 처럼 또는 분석을 위해 म 저장 정보만 수집을 줄이려고 합니다. 대부분의 Azure 스택 시스템 및 Azure 서비스 작동 하는 방법에 대 한 정보는 6 개월 내에서 삭제 됩니다. 요약 또는 집계 된 데이터를 장기간 유지 됩니다.

개인 정보 및 고객의 정보의 보안은 중요 한 점을 이해 합니다. 한 신중한 포괄적인 접근 방식을 고객 개인 정보 및 Azure 스택을 사용 하 여 고객 데이터의 보호로 이동 합니다. IT 관리자는 언제 든 지 기능 및 개인 정보 설정 사용자 지정 하는 컨트롤이 있습니다. 투명도 신뢰를 위한 노력 만으로도:
- 에서는 수집할 데이터의 형식에 대 한 고객으로 열려 있습니다.
- 기업 고객 컨트롤에 넣을까요-자신의 개인 정보 설정을 사용자 지정할 수 있습니다.
- 고객의 개인 정보 및 보안 먼저 입력.
- 원격 분석 사용 되는 방법에 대 한 투명 하 게 하는입니다.
- 원격 분석 고객 만족도 향상 하기 위해 사용 합니다.

Microsoft는 신용 카드 번호, 사용자 이름 및 암호, 전자 메일 주소 또는 기타 마찬가지로 중요 한 정보 등의 중요 한 정보를 수집 하려고 하지 않습니다. 중요 한 정보 수신 실수로, 판단 되는 경우 삭제 합니다.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Microsoft 원격 분석 데이터를 사용 하는 방법의 예
원격 분석 신속 하 게 식별 하 고 고객의 배포 및 구성에 대 한 중요 한 안정성 문제를 해결 하는 데 도와에서 중요 한 역할을 재생 합니다. 서비스 또는 하드웨어 구성 문제를 신속 하 게 식별 도움이 म 수집 하는 원격 분석 데이터에 대 한 정보. Microsoft의 고객 및 생태계에 향상 된 드라이브에서이 데이터를 가져올 수는 통합된 Azure 스택 솔루션의 품질에 대 한 막대 하는 데 도움이 됩니다.

원격 분석에는 또한 Microsoft에 더 잘 고객 구성 요소를 배포 하는 방법을 이해 기능을 사용 하 고 서비스를 사용 하 여 자신의 비즈니스 목표를 달성할 수 있습니다. 얻고 해당 데이터에서 직접 고객의 환경 및 작업에 참여할 수 있는 영역에 대 한 엔지니어링 투자 우선 순위는 데 도움이 됩니다.

몇 가지 예의 컨테이너, 저장소, 네트워킹 구성을 Azure 스택 역할과 연결 된 고객 사용을 포함 합니다. 또한 insights 드라이브 개선 사항 및 관리 및 모니터링 솔루션 중 일부를 인텔리전스로 사용 합니다. 품질 문제를 진단 하는 고객 고 Microsoft에 대 한 호출이 더 적은 지원 하 여 비용을 절감할 수 있습니다.

## <a name="manage-telemetry-collection"></a>원격 분석 컬렉션 관리
해제 하는 원격 분석 조직에서 원격 분석 향상 된 제품 기능 및 안정성을 구동 하는 데이터를 제공 하는 것은 좋지 않습니다. 하지만 일부 시나리오에서이 필요할 수 있습니다 알고 수행 합니다.

이러한 경우에 레지스트리 설정을 배포 전 또는 배포에 대 한 후에 원격 분석 끝점을 사용 하 여 Microsoft로 전송 하는 원격 분석 수준을 구성할 수 있습니다.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Windows 레지스트리에서 수준 원격 분석 설정
Windows 레지스트리 편집기는 수동으로 Azure 스택을 배포 하기 전에 물리적 호스트 컴퓨터에서 원격 분석 수준을 설정 하는 데 사용 됩니다. 그룹 정책과 같은 관리 정책에서 이미 있는 경우이 레지스트리 설정을 재정의 합니다.

Azure 스택 개발 키트 호스트를 배포 하기 전에 CloudBuilder.vhdx로 부팅 하 고 관리자 권한 PowerShell 창에서 다음 스크립트를 실행 합니다.

```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

원격 분석 수준은 누적 및 4 개의 수준 (0-3)으로 분류 된:

**0 (보안)** 합니다. 보안 데이터에만 해당 합니다. 연결 된 사용자 환경 및 원격 분석 구성 설정과 Windows Defender에 대 한 데이터를 포함 하 여 운영 체제를 유지 하는 데 필요한 정보를 보호 합니다. Azure 스택 특정 원격 분석은이 수준에서 내보내집니다.

**1 (기본)** 합니다. 보안 데이터 및 기본 상태 및 품질 데이터입니다. 기본 장치 정보를 포함 하 여: 품질 관련 데이터, 응용 프로그램 호환성, 응용 프로그램 사용 현황 데이터 및 데이터의 보안 수준입니다. 원격 분석 수준을 기본 수 있도록 Azure 스택 원격 분석 설정 합니다. 이 수준에서 수집 되는 데이터에는 다음이 포함 됩니다.

- **기본 장치 정보** 는 사용 하면 형식 및 네이티브 및 가상화 된 Windows Server 2016 인스턴스는 에코 시스템에서 구성 하는 방법에 대 한 이해를 제공 하는 포함 하 여:
 - OEM 같은 컴퓨터 특성을 모델
 - 네트워킹 수와 속도의 네트워크 어댑터 등의 특성
 - 프로세서 및 메모리 크기를 코어 수와 같이 메모리 특성
 - 드라이브, 유형 및 크기의 수와 같은 저장소 특성입니다.
- **원격 분석 기능**를 포함 하 여 업로드 된 이벤트, 삭제 된 이벤트 및 마지막의 % 업로드 시간입니다.
- **품질 관련 정보** Microsoft Azure 스택 수행 되는 방법을 대 한 기본적인 이해를 개발할 수 있게 해 주는 합니다. 예로 특정 하드웨어 구성에서 중요 한 알림 수 있습니다.
- * * 호환성 데이터에 대 한 리소스 공급자는 시스템 및 가상 컴퓨터에 설치 되 고 잠재적인 호환성 문제 이해 제공을 지원 합니다.

**2 (고급)** 합니다. 추가 정보를 포함 하 여: 운영 체제 및 기타 Azure 스택 서비스 사용 방법, 수행 방법, 고급 안정성 데이터 및 데이터를 기본 및 보안 수준입니다.

**(전체) 3**합니다. 모든 식별 하 고 문제를 해결 하는 데 필요한 데이터와 데이터를는 **보안**, **기본**, 및 **고급** 수준입니다.

> [!NOTE]
> 기본 원격 분석 수준 값은 2 (확장)입니다.

Windows 및 Azure 스택 원격 분석 해제 SQL 원격 분석을 사용할 수 없습니다. Windows Server 원격 분석 설정의 의미에 대 한 자세한 내용은 참조는 [Windows 원격 분석 백서](https://aka.ms/winservtelemetry)합니다.

> [!IMPORTANT]
> 이러한 원격 분석 수준 Microsoft Azure 스택 구성 요소에만 적용 됩니다. Microsoft 이외 소프트웨어 구성 요소 및 Azure 스택 하드웨어 파트너에서 하드웨어 수명 주기 호스트에서 실행 되는 서비스는 이러한 원격 분석 수준 외부에서 해당 클라우드 서비스와 통신할 수 있습니다. 원격 분석 정책이 옵트인 하거나 옵트아웃 하는 방법 이해 하는 Azure 스택 하드웨어 솔루션 공급자와 함께 작동 해야 합니다.

### <a name="enable-or-disable-telemetry-after-deployment"></a>설정 또는 배포 후 원격 분석 해제

를 사용 하거나 배포 후 원격 분석을 사용 하지 않도록 설정 하려면 액세스 하는 권한 있는 끝점 (PEP) ERCS Vm에 노출 되가 필요 합니다.
1.  사용 하려면: `Set-Telemetry -Enable`
2.  사용 하지 않으려면: `Set-Telemetry -Disable`

매개 변수 세부 정보:
> . 매개 변수 사용-원격 분석 데이터 업로드 켜기

> . 매개 변수 사용 안 함-원격 분석 데이터 업로드를 해제  

**원격 분석을 사용 하도록 설정 하려면 스크립트:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**원격 분석을 사용 하지 않도록 설정 하는 스크립트:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>다음 단계
[마켓플레이스 항목을 추가 합니다.](asdk-marketplace-item.md)
