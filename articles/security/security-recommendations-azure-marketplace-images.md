---
title: "Azure Marketplace의 보안 권장 사항 이미지 | Microsoft Docs"
description: "이 문서는 마켓플레이스에 포함된 이미지에 대한 권장 사항을 제공합니다."
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 4ae36f87c29975c82bb99f713893a9dc78a249e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2017
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Azure Marketplace의 보안 권장 사항 이미지

각 솔루션이 다음 보안 구성 권장 사항을 준수하도록 하는 것이 좋습니다. 이것은 Azure Marketplace에서 파트너 솔루션 이미지에 대해 높은 수준의 보안을 유지하는 데 도움이 됩니다.

이 권장 사항은 Azure Marketplace에 이미지가 없는 조직에도 유용할 수 있습니다. 아래 표에 있는 지침에 따라 회사의 Windows 및 Linux 이미지 구성을 확인할 수도 있습니다.

## <a name="open-source-based-images"></a>소스 기반 이미지 열기

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **범주**                                                 | **확인**                                                                                                                                                                                                                                                                              |
| 보안                                                     | Linux 배포에 대한 모든 최신 보안 패치가 설치됩니다.                                                                                                                                                                                                              |
| 보안                                                     | 특정 Linux 배포에 대한 VM 이미지를 보호하기 위한 산업 지침을 준수했습니다.                                                                                                                                                                                     |
| 보안                                                     | 필요한 Windows Server 역할, 기능, 서비스 및 네트워킹 포트만으로 공간을 최소화하여 공격 화면을 제한합니다.                                                                                                                                               |
| 보안                                                     | 맬웨어의 소스 코드 및 결과 VM 이미지를 검색합니다.                                                                                                                                                                                                                                   |
| 보안                                                     | VHD 이미지에는 백도어가 아닌 대화형 로그인을 허용하는 기본 암호가 없는 필수 잠긴 계정만 포함되어 있습니다.                                                                                                                                           |
| 보안                                                     | 응용 프로그램 기능이 사용하지 않는 방화벽 어플라이언스와 같은 방화벽 규칙은 사용 불가능합니다.                                                                                                                                                                             |
| 보안                                                     | 테스트 SSH 키, 알려진 호스트 파일, 로그 파일 및 불필요한 인증서와 같은 중요한 정보가 모두 VHD 이미지에서 제거되었습니다.                                                                                                                                       |
| 보안                                                     | LVM을 사용하지 않는 것이 좋습니다.                                                                                                                                                                                                                                            |
| 보안                                                     | 필수 라이브러리의 최신 버전은 다음과 같습니다. </br> - OpenSSL v1.0 이상 </br> - Python 2.5 이상(Python 2.6 이상 권장) </br> - Python pyasn1 패키지(아직 설치되지 않은 경우) </br> - d.OpenSSL v 1.0 이상                                                                |
| 보안                                                     | Bash/Shell 히스토리 항목을 지워야 합니다.                                                                                                                                                                                                                                             |
| 네트워킹                                                   | SSH 서버는 기본적으로 포함되어야 합니다. sshd config에 SSH keep alive를 ClientAliveInterval 180 옵션으로 설정                                                                                                                                                        |
| 네트워킹                                                   | 이미지에 사용자 정의 네트워크 구성이 포함되어 있지 않아야 합니다. resolv.conf: `rm /etc/resolv.conf` 삭제                                                                                                                                                                                |
| 배포                                                   | 최신 Azure Linux 에이전트를 설치해야 합니다. </br> - RPM 또는 Deb 패키지를 사용해 에이전트를 설치해야 합니다.  </br> - 수동 설치 프로세스를 사용할 수도 있지만 설치 프로그램 패키지가 권장되며 선호됩니다. </br> - github 리포지토리에서 에이전트를 수동으로 설치하는 경우에는 먼저 `waagent` 파일을 `/usr/sbin`에 복사하고 루트로 실행합니다. </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>에이전트 구성 파일은 `/etc/waagent.conf`에 있습니다.    |
| 배포                                                   | Azure 지원 팀은 필요한 경우 파트너에게 직렬 콘솔 출력을 제공하고 클라우드 저장소에서 OS 디스크 마운트에 적절한 제한 시간을 제공할 수 있어야 합니다. 이미지는 다음 매개 변수를 커널 부팅 줄에 추가했을 것입니다. `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| 배포                                                   | OS 디스크에 스왑 파티션이 없습니다. Linux 에이전트에서 로컬 리소스 디스크에 생성할 스왑을 요청할 수 있습니다.         |
| 배포                                                   | OS 디스크에 대한 단일 루트 파티션을 작성하는 것이 좋습니다.      |
| 배포                                                   | 64비트 운영 체제만 해당됩니다.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server 기반 이미지

|||
|-------------| -------------------------|
| **범주**                                                     | **확인**                                                                                                                                                                |
| 보안                                                         | 보안 OS 기본 이미지를 사용합니다. Windows Server를 기반으로 하는 모든 이미지의 원본에 사용된 VHD는 Microsoft Azure를 통해 제공되는 Windows Server OS 이미지에서 가져와야 합니다. |
| 보안                                                         | 모든 최신 보안 업데이트를 설치합니다.                                                                                                                                     |
| 보안                                                         | 응용 프로그램에는 관리자, 루트 및 관리자와 같이 제한된 사용자 이름에 대한 종속성이 없어야 합니다.                                                                |
| 보안                                                         | 운영 체제 하드 디스크에서는 BitLocker 드라이브 암호화가 지원되지 않습니다. 데이터 디스크에서 BitLocker를 사용할 수 있습니다.                                                            |
| 보안                                                         | 필요한 Windows Server 역할, 기능, 서비스 및 네트워킹 포트만 활성화한 상태로 공간을 최소화하여 공격 화면을 제한합니다.                         |
| 보안                                                         | 맬웨어의 소스 코드 및 결과 VM 이미지를 검색합니다.                                                                                                                     |
| 보안                                                         | Windows Server 이미지 보안 업데이트를 자동 업데이트로 설정합니다.                                                                                                                |
| 보안                                                         | VHD 이미지에는 백도어가 아닌 대화형 로그인을 허용하는 기본 암호가 없는 필수 잠긴 계정만 포함되어 있습니다.                             |
| 보안                                                         | 응용 프로그램 기능이 사용하지 않는 방화벽 어플라이언스와 같은 방화벽 규칙은 사용 불가능합니다.                                                               |
| 보안                                                         | 모든 중요한 정보가 VHD 이미지에서 제거되었습니다. 예를 들어 HOSTS 파일, 로그 파일 및 불필요한 인증서는 제거해야 합니다.                                              |
| 배포                                                       | 64비트 운영 체제만 해당됩니다.                            |
