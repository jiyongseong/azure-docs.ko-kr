---
title: 배포된 StorSimple 장치 문제 해결 | Microsoft Docs
description: 현재 배포되어 작동하는 StorSimple 장치에서 발생하는 오류를 진단하고 수정하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: cf037f7f1c1384b654a7144485d38f569eb7c167
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>작동 StorSimple 장치 문제 해결
> [!NOTE]
> StorSimple의 클래식 포털은 사용되지 않습니다. StorSimple 장치 관리자는 사용 중단 일정에 따라 자동으로 새 Azure Portal로 이동합니다. 이 이동에 대한 메일 및 포털 알림을 받게 됩니다. 이 문서도 곧 사용 중지됩니다. 이동과 관련된 자세한 내용은 [FAQ: Azure Portal로 이동](storsimple-8000-move-azure-portal-faq.md)을 참조하세요.

## <a name="overview"></a>개요
이 문서에서는 StorSimple 장치가 배포되고 작동된 후 발생할 수 있는 구성 문제를 해결하기 위한 유용한 문제 해결 지침을 제공합니다. 일반 문제, 가능한 원인 및 Microsoft Azure StorSimple을 실행할 때 발생할 수 있는 문제를 해결할 수 있는 권장 단계를 설명합니다. 이 정보는 StorSimple 온-프레미스 물리적 장치 및 StorSimple 가상 장치 모두에 적용됩니다.

이 문서의 끝에서 Microsoft Azure StorSimple 작업 중 발생할 수 있는 오류 코드 목록 및 오류를 해결하기 위해 취할 수 있는 단계를 찾을 수 있습니다. 

## <a name="setup-wizard-process-for-operational-devices"></a>운영 장치에 대한 설치 마법사 프로세스
장치 구성을 확인하고 필요한 경우 수정 작업을 수행하려면 설치 마법사([Invoke-HcsSetupWizard][1])를 사용합니다.

이전에 구성된 작동 장치에서 설치 마법사를 실행하면 프로세스 흐름은 다릅니다. 다음 항목만 변경할 수 있습니다.

* IP 주소, 서브넷 마스크 및 게이트웨이
* 기본 DNS 서버
* 기본 NTP 서버
* 선택적 웹 프록시 구성

설치 마법사는 암호 컬렉션 및 장치 등록과 관련된 작업을 수행하지 않습니다.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>설치 마법사를 후속 실행하는 동안 발생하는 오류
다음 표에서 운영 장치에서 설치 마법사를 실행할 때 발생할 수 있는 오류, 해당 오류에 대한 가능한 원인 및 해결하기 위한 권장 작업을 설명합니다. 

| 번호 | 오류 메시지 또는 조건 | 가능한 원인 | 권장 작업 |
|:--- |:--- |:--- |:--- |
| 1 |오류 350032: 이 장치는 이미 비활성화되었습니다. |비활성화된 장치에서 설치 마법사를 실행하는 경우 이 오류가 나타납니다. |[Microsoft 지원에 문의](storsimple-contact-microsoft-support.md) 하세요. 비활성화된 장치를 제공할 수 없습니다. 장치를 다시 활성화하기 전에 공장 재설정이 필요할 수 있습니다. |
| 2 |Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(HRESULT: 0x80070001에서 예외 발생) |DNS 서버 업데이트에 실패했습니다. DNS 설정은 전역 설정이며 사용할 수 있는 모든 네트워크 인터페이스를 통해 적용됩니다. |인터페이스를 사용하고 DNS 설정을 다시 적용합니다. 전역 설정 때문에 사용되는 다른 인터페이스에 대한 네트워크에 방해가 될 수 있습니다. |
| 3 |장치가 StorSimple Manager 서비스 포털에서 온라인 상태인 것으로 나타나지만 최소 설치를 완료하고 구성을 저장하려고 할 때 작업이 실패합니다. |초기 설치 중, 실제 프록시 서버가 제 자리에 있더라도 웹 프록시는 구성되지 않았습니다. |[Test-HcsmConnection][2] cmdlet을 사용하여 오류를 찾습니다. [Microsoft 지원에 문의](storsimple-contact-microsoft-support.md) 하세요. |
| 4 |Invoke-HcsSetupWizard: 예상 범위 내에 값이 없습니다. |잘못된 서브넷 마스크 때문에 이 오류가 발생합니다. 가능한 원인:  <ul><li> 서브넷 마스크가 없거나 비어 있습니다.</li><li>Ipv6 접두사 형식이 잘못되었습니다.</li><li>인터페이스가 클라우드를 사용하도록 설정되었지만 게이트웨이가 없거나 잘못되었습니다.</li></ul>설치 마법사를 통해 구성된 경우 데이터 0은 자동으로 클라우드 설정됩니다. |문제를 확인하려면 0.0.0.0 또는 256.256.256.256, 서브넷을 사용한 다음 출력을 확인합니다. 필요에 따라 서브넷 마스크, 게이트웨이 및 Ipv6 접두사에 대한 올바른 값을 입력합니다. |

## <a name="error-codes"></a>오류 코드
오류는 숫자 순서로 나열됩니다.

| 오류 번호 | 오류 텍스트 또는 설명 | 권장되는 사용자 작업 |
|:--- |:--- |:--- |
| 10502 |저장소 계정에 액세스하는 동안 오류가 발생했습니다. |몇 분간 기다린 다음 작업을 다시 시도하세요. 문제가 지속되면 다음 단계는 Microsoft 지원에 문의하세요. |
| 40017 |백업 정책에 지정된 볼륨을 장치에서 찾을 수 없어서 백업 작업에 실패했습니다. |백업 작업을 다시 시도하고, 오류가 지속되면 Microsoft 지원에 다음 단계를 문의하세요. |
| 40018 |백업 정책에 지정된 볼륨이 장치에 없어서 백업 작업에 실패했습니다. |백업 작업을 다시 시도하고, 오류가 지속되면 Microsoft 지원에 다음 단계를 문의하세요. |
| 390061 |시스템이 사용 중이거나 사용할 수 없습니다. |몇 분간 기다린 다음 작업을 다시 시도하세요. 문제가 지속되면 다음 단계는 Microsoft 지원에 문의하세요. |
| 390143 |오류 코드가 390143인 오류가 발생했습니다. (알 수 없는 오류.) |문제가 지속되면 다음 단계는 Microsoft 지원에 문의하세요. |

## <a name="next-steps"></a>다음 단계
문제를 해결할 수 없는 경우 [Microsoft 지원에 문의](storsimple-contact-microsoft-support.md) 하세요. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
