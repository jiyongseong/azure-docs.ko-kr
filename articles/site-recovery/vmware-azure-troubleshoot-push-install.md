---
title: VMware에서 Azure로의 Azure Site Recovery 문제 해결 | Microsoft Docs
description: Azure 가상 머신을 복제할 때 오류 문제 해결
services: site-recovery
author: anoopkv
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: bdd5d87670ddb26d2c4474a319fe19bef1db62f5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Mobility Service 푸시 설치 문제 해결

이 문서에서는 원본 서버에서 Azure Site Recovery Mobility Service를 설치하여 보호를 구현할 때 발생할 수 있는 일반적인 오류를 해결하는 방법에 대해 설명합니다.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>오류 78007 - 요청된 작업을 완료할 수 없습니다.
몇 가지 이유로 서비스에서 이 오류를 throw할 수 있습니다. 추가로 문제를 해결하기 위해 해당 공급자 오류를 선택합니다.

* [오류 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [오류 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [오류 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [오류 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [오류 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [오류 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [오류 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [오류 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>오류 코드 95105 - 보호를 사용하도록 설정할 수 없습니다(EP0856).

**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95105 </br>**메시지 -** 원본 컴퓨터에 Mobility Service를 강제 설치하지 못했습니다(오류 코드: **EP0856**). <br> 원본 컴퓨터에서 **파일 및 프린터 공유**가 허용되지 않거나 프로세스 서버와 원본 컴퓨터 간에 네트워크 연결 문제가 있음| **파일 및 프린터 공유**가 사용하도록 설정되지 않음 | Windows 방화벽에서 원본 컴퓨터의 **파일 및 프린터 공유**를 허용합니다. 원본 컴퓨터의 **Windows 방화벽** > **방화벽을 통해 앱 또는 기능 허용**에서 **모든 프로필에 대해 파일 및 프린터 공유**를 선택합니다. </br> 또한 푸시 설치를 완료하기 위한 다음 필수 조건을 확인합니다.<br> [WMI 문제 해결](#troubleshoot-wmi-issues)에 대해 자세히 알아봅니다.


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>오류 코드 95107 - 보호를 사용하도록 설정할 수 없습니다(EP0858).

**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95107 </br>**메시지 -** 원본 컴퓨터에 Mobility Service를 강제 설치하지 못했습니다(오류 코드: **EP0858**). <br> Mobility Service를 설치하기 위해 제공된 자격 증명이 잘못되었거나 사용자 계정에 권한이 부족합니다. | 원본 컴퓨터에 Mobility Service를 설치하기 위해 제공된 자격 증명이 올바르지 않습니다. | 구성 서버에서 원본 컴퓨터에 대해 제공된 사용자 자격 증명이 올바른지 확인하세요. <br> 사용자 자격 증명을 추가 또는 편집하려면 구성 서버에서 **Cspsconfigtool** > **계정 관리**를 선택합니다. </br> 또한 강제 설치를 완료하기 위해 다음 [필수 조건](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)을 확인합니다.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>오류 코드 95117 - 보호를 사용하도록 설정할 수 없습니다(EP0865).

**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95117 </br>**메시지:**  원본 컴퓨터에 Mobility Service를 강제 설치하지 못했습니다(오류 코드: **EP0865**). <br> 원본 컴퓨터가 실행 중이 아니거나 프로세스 서버와 원본 컴퓨터 간에 네트워크 연결 문제가 있음 | 프로세스 서버와 원본 서버 간의 네트워크 연결 문제 | 프로세스 서버와 원본 서버 간의 연결을 확인합니다. </br> 또한 강제 설치를 완료하기 위해 다음 [필수 조건](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)을 확인합니다.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>오류 코드 95103 - 보호를 사용하도록 설정할 수 없습니다(EP0854).

**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95103 </br>**메시지:**  원본 컴퓨터에 Mobility Service를 강제 설치하지 못했습니다(오류 코드: **EP0854**). <br> 원본 컴퓨터에서 WMI(Windows Management Instrumentation)가 허용되지 않거나 프로세스 서버와 원본 컴퓨터 간에 네트워크 연결 문제가 있음| WMI가 Windows 방화벽에서 차단됨 | Windows 방화벽에서 WMI를 허용합니다. **Windows 방화벽 설정** > **방화벽을 통해 앱 또는 기능 허용**에서 **모든 프로필에 대해 WMI**를 선택합니다. </br> 또한 강제 설치를 완료하기 위해 다음 [필수 조건](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)을 확인합니다.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>오류 코드 95213 - 보호를 사용하도록 설정할 수 없습니다(EP0874).

**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95213 </br>**메시지:**  원본 컴퓨터 %SourceIP;에 Mobility Service를 설치하지 못했습니다(오류 코드: **EP0874**). <br> | 원본 컴퓨터의 운영 체제 버전이 지원되지 않습니다. <br>| 원본 컴퓨터 OS 버전이 지원되는지 확인하세요. [지원 매트릭스](https://aka.ms/asr-os-support)를 참고하세요. </br> 또한 강제 설치를 완료하기 위해 다음 [필수 조건](https://aka.ms/pushinstallerror)을 확인합니다.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>오류 코드 95108 - 보호를 사용하도록 설정할 수 없습니다(EP0859).

**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95108 </br>**메시지:**  원본 컴퓨터에 Mobility Service를 강제 설치하지 못했습니다(오류 코드: **EP0859**). <br>| Mobility Service를 설치하기 위해 제공된 자격 증명이 잘못되었거나 사용자 계정에 권한이 부족합니다. <br>| 제공된 자격 증명이 **루트** 계정의 자격 증명인지 확인하세요. 사용자 자격 증명을 추가 또는 편집하려면 구성 서버로 이동하고 바탕 화면에서 **Cspsconfigtool** 바로 가기 아이콘을 선택합니다. **계정 관리**를 선택하여 자격 증명을 추가 또는 편집합니다.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>오류 코드 95265 - 보호를 사용하도록 설정할 수 없습니다(EP0902).

**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95265 </br>**메시지:** 원본 컴퓨터에 Mobility Service를 강제 설치했지만 일부 시스템 변경 내용을 적용하기 위해 원본 컴퓨터를 다시 시작해야 합니다. <br>| 이전 버전의 Mobility Service가 서버에 이미 설치되었습니다.| 가상 머신의 복제가 원활하게 계속됩니다.<br> 다음 유지 관리 기간 동안 서버를 다시 부팅하여 Mobility Service의 새로운 기능 향상의 이점을 활용합니다.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>오류 코드 95224 - 보호를 사용하도록 설정할 수 없습니다(EP0883).

**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95224 </br>**메시지:** 원본 컴퓨터 %SourceIP;에 Mobility Service를 강제 설치하지 못했습니다(오류 코드: **EP0883**). 시스템이 이전 설치로 다시 시작합니다. 또는 업데이트가 보류 중입니다.| 이전 또는 호환 불가능한 버전의 Mobility Service를 제거하는 경우 시스템이 다시 시작되지 않습니다.| Mobility Service 버전이 서버에 설치되어 있지 않은지 확인하세요. <br> 서버를 다시 부팅하고 사용 가능한 보호 작업을 다시 실행합니다.|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>강제 설치 문제를 해결하기 위한 리소스

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>파일 및 프린터 공유 문제 해결
* [그룹 정책을 사용하여 파일 공유 사용 또는 사용 안 함](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [Windows 방화벽을 통해 파일 및 인쇄 공유 사용](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>WMI 문제 해결
* [기본 WMI 테스트](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 문제 해결](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI 스크립트 및 WMI 서비스 관련 문제 해결](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>다음 단계

VMware VM에 대한 재해 복구를 설정하는 [방법을 알아봅니다](vmware-azure-tutorial.md).