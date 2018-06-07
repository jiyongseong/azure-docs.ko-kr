---
title: Azure 스택 통합 시스템 배포에 대 한 Azure 스택 공개 키 인프라 인증서 준비 | Microsoft Docs
description: Azure 스택 통합 시스템에 대 한 Azure 스택 PKI 인증서를 준비 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 934585082e2832c41885874c82ab43d64a1fa361
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Azure 스택 PKI 인증서 배포 준비
인증서 파일 [선택한 해당 CA에서 가져온](azure-stack-get-pki-certs.md) 가져오고 Azure 스택 인증서 요구 사항 일치 하는 속성을 사용 하 여 내보낸 해야 합니다.


## <a name="prepare-certificates-for-deployment"></a>배포에 대 한 인증서를 준비 합니다.
다음이 단계를 사용 하 여 준비 하 고 Azure 스택 PKI 인증서의 유효성을 검사 합니다. 

### <a name="import-the-certificate"></a>인증서 가져오기

1.  원래 인증서 버전을 복사할 [선택한 해당 CA에서 가져온](azure-stack-get-pki-certs.md) 배포 호스트에 있는 디렉터리에 있습니다. 
  > [!WARNING]
  > CA에서 직접 제공 하는 파일에서 어떤 방식으로든에서 이미, 내보낼 가져오거나 변경 하는 파일을 복사 하지 마십시오.

2.  선택한 인증서를 마우스 오른쪽 단추로 클릭 **인증서 설치** 또는 **PFX 설치** CA에서 인증서가 배달 하는 방법을 따라 합니다.

3. 에 **인증서 가져오기 마법사**선택, **로컬 컴퓨터** 가져오기 위치도 합니다. **다음**을 선택합니다. 다음 화면에서 다음 다시 클릭 합니다.

    ![로컬 컴퓨터 가져오기 위치](.\media\prepare-pki-certs\1.png)

4.  선택 **모든 인증서를 다음 저장소에 위치** 선택한 후 **엔터프라이즈 신뢰** 위치로 합니다. 클릭 **확인** 를 인증서 저장소 선택 대화 상자를 닫으려면 다음 **다음**합니다.

    ![인증서 저장소 구성](.\media\prepare-pki-certs\3.png)

    a. PFX를 가져오는 경우 추가 된 대화 상자가 나타납니다. 에 **개인 키 보호** 페이지에서 인증서 파일에 대 한 암호를 입력 한 후 사용 하도록 설정 된 **이 키를 내보낼 수 있도록 표시 합니다. 백업 또는 나중에 키를 전송할 수 있습니다** 옵션입니다. **다음**을 선택합니다.

    ![키를 내보낼 수 있도록 표시](.\media\prepare-pki-certs\2.png)

5. 가져오기를 완료 하려면 마침을 클릭 합니다.

### <a name="export-the-certificate"></a>인증서 내보내기

인증서 관리자 MMC 콘솔을 열고 로컬 컴퓨터 인증서 저장소에 연결 합니다.

1. Microsoft 관리 콘솔을 열고, Windows 10에서 마우스 오른쪽 단추로 클릭 하 여 시작 메뉴에서 실행을 차례로 클릭 합니다. 형식 **mmc** 확인을 클릭 합니다.

2. 파일 추가/제거 스냅인에서 다음 select 인증서 추가 클릭 합니다.

    ![인증서 스냅인 추가](.\media\prepare-pki-certs\mmc-2.png)
 
3. 컴퓨터 계정을 선택, 다음을 클릭 한 다음 로컬 컴퓨터를 선택한 다음 완료 합니다. 스냅인 추가/제거 페이지를 닫으려면 확인을 클릭 합니다.

    ![인증서 스냅인 추가](.\media\prepare-pki-certs\mmc-3.png)

4. 인증서를 찾아 > 엔터프라이즈 신뢰 > 인증서 위치 합니다. 오른쪽의 인증서에 표시 되는지 확인 합니다.

5. 작업 표시줄의 인증서 관리자 콘솔에서 선택 **동작** > **모든 작업** > **내보내기**합니다. **다음**을 선택합니다.

  > [!NOTE]
  > Azure 스택 개수에 따라 인증서가 있습니다이 프로세스를 두 번 이상 완료 해야 할 수 있습니다.

4. 선택 **예, 개인 키 내보내기**, 클릭 하 고 **다음**합니다.

5. 파일 내보내기 형식 섹션에서 선택 **확장 된 속성 모두 내보내기** 클릭 하 고 **다음**합니다.

6. 선택 **암호** 인증서에 대 한 암호를 지정 합니다. 배포 매개 변수로 사용 중 이므로이 암호를 기억해 둡니다. **다음**을 선택합니다.

7. 파일 이름 및 pfx 파일을 내보낼 위치를 선택 합니다. **다음**을 선택합니다.

8. **마침**을 선택합니다.

## <a name="next-steps"></a>다음 단계
[PKI 인증서의 유효성을 검사합니다](azure-stack-validate-pki-certs.md)