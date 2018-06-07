---
title: AWS에서 Service Fabric 클러스터에 대한 인프라 만들기 자습서 - Azure Service Fabric | Microsoft Docs
description: 이 자습서에서는 Service Fabric 클러스터를 실행하는 AWS 인프라를 설정하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: 6b7d2223d33abb429ab5f59b14c80d43c70598dc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209653"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>자습서: AWS 인프라를 만들어 Service Fabric 클러스터 호스팅하기

Service Fabric 독립 실행형 클러스터는 사용자 자신의 환경을 선택하고 Service Fabric이 수행하는 "모든 OS 및 모든 클라우드" 접근 방법의 일부로서 클러스터를 만드는 옵션을 제공합니다. 이 자습서 시리즈에서는 AWS에서 호스팅되는 독립 실행형 클러스터를 만들고 여기에 응용 프로그램을 설치합니다.

이 자습서는 시리즈의 1부입니다. 이 문서에서는 Service Fabric의 독립 실행형 클러스터를 호스팅하는 데 필요한 AWS 리소스를 생성합니다. 후속 문서에서는 Service Fabric 독립 실행형 도무 모음을 설치하고 클러스터에 샘플 응용 프로그램을 설치하고 마지막으로 클러스터를 정리해야 합니다.

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * EC2 인스턴스 집합 만들기
> * 보안 그룹 수정
> * 인스턴스 중 하나에 로그인
> * Service Fabric에 대한 인스턴스 준비

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 AWS 계정이 필요합니다.  계정이 없는 경우 [AWS 콘솔](https://aws.amazon.com/)로 이동해 계정을 만듭니다.

## <a name="create-ec2-instances"></a>EC2 인스턴스 만들기

AWS 콘솔에 로그인 > 검색 상자에 **EC2** 입력 > **클라우드의 EC2 가상 서버**

![AWS 콘솔 검색][aws-console]

**인스턴스 시작**을 선택하고 다음 화면에서 Microsoft Windows Server 2016 Base 옆에 있는 **선택**을 선택합니다.

![EC2 인스턴스 선택][aws-ec2instance]

**t2.medium**을 선택한 후, **다음: 인스턴스 세부 정보 구성**을 선택하고 다음 화면에서 인스턴스 수를 `3`으로 변경한 다음, **고급 세부 정보**를 선택하여 해당 섹션을 확장합니다.

Service Fabric에서 가상 머신을 함께 연결하려면 인프라를 호스팅하는 VM에는 동일한 자격 증명이 있어야 합니다.  일관된 자격 증명을 가져오려면 동일한 도메인에 모두 조인하거나 각 VM에서 동일한 관리자 암호를 설정하는 등의 두 가지 일반적인 방법이 있습니다.  이 자습서의 경우 모두 동일한 암호를 갖도록 EC2 인스턴스를 설정하려면 사용자 데이터 스크립트를 사용합니다.  프로덕션 환경에서 호스트를 Windows 도메인에 조인하는 것이 보다 안전합니다.

콘솔의 사용자 데이터 필드에 다음 스크립트를 입력합니다.

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

PowerShell 스크립트 입력했으면 **검토 및 시작** 선택

![EC2 검토 및 시작][aws-ec2configure2]

검토 화면에서 **시작**을 선택합니다.  그런 다음, 드롭다운을 **키 쌍 없이 진행**으로 변경하고 확인란을 선택하여 암호를 안다고 표시합니다.

![AWS 키 쌍 선택][aws-keypair]

마지막으로 **인스턴스 시작** 및 **인스턴스 보기**를 차례로 선택합니다.  만든 Service Fabric 클러스터에 대한 기초가 있으므로 이제 Service Fabric 구성을 준비하려면 인스턴스 자체에 몇 가지 최종 구성을 추가해야 합니다.

## <a name="modify-the-security-group"></a>보안 그룹 수정

Service Fabric은 클러스터의 호스트 사이에 많은 포트가 열려 있어야 합니다. AWS 인프라에서 이러한 포트를 열려면 만든 인스턴스 중 하나를 선택합니다. 그런 다음, 보안 그룹의 이름을 선택합니다(예: **launch-wizard-1**). 이제 **인바운드** 탭을 선택합니다.

이러한 포트를 전 세계에 여는 것을 방지하려면 대신 동일 보안 그룹의 호스트에 대해서만 포트를 엽니다. 보안 그룹 ID를 기록해 둡니다. 예를 들어 **sg-c4fb1eba**입니다.  그런 다음, **편집**을 선택합니다.

다음으로, 서비스 종속성에 대한 보안 그룹에 규칙 4개를 추가한 다음, Service Fabric에 대한 규칙 3개를 더 추가합니다. 첫 번째 규칙은 기본 연결 확인에 대한 ICMP 트래픽을 허용하는 것입니다. 다른 규칙은 SMB 및 원격 레지스트리를 사용하도록 설정하는 데 필요한 포트를 엽니다.

첫 번째 규칙의 경우 **규칙 추가**를 선택한 다음, 드롭다운 메뉴에서 **모든 ICMP-IPv4**를 선택합니다. 사용자 지정 옆의 입력란을 선택하고 위에서 사용자 보안 그룹 ID를 입력합니다.

마지막 세 가지 종속성의 경우 비슷한 프로세스를 따라야 합니다.  **규칙 추가**를 선택하고, 드롭다운 메뉴에서 **사용자 지정 TCP 규칙**을 선택하고, 각 규칙에 대한 `135`, `137-139`, `445` 중 하나를 포트 범위에 입력합니다. 마지막으로 원본 상자에 보안 그룹 ID를 입력합니다.

![보안 그룹 포트][aws-ec2securityports]

종속성에 대한 포트가 열려 있으므로 Service Fabric이 통신에 사용하는 포트에 대해 동일한 작업을 수행해야 합니다. **규칙 추가**를 선택하고, 드롭 다운 메뉴에서 **사용자 지정 TCP 규칙**을 선택하고, 포트 범위에 `20001-20031` 원본 상자의 보안 그룹을 입력합니다.

다음으로 임시 포트 범위에 대한 규칙을 추가합니다.  **규칙 추가**를 선택하고, 드롭다운 메뉴에서 **사용자 지정 TCP 규칙**을 선택하고, 포트 범위에 `20606-20861`를 입력합니다. 마지막으로 원본 상자에 보안 그룹 ID를 입력합니다.

Service Fabric에 대한 마지막 두 개의 규칙의 경우 전 세계에 열려있으므로 개인용 컴퓨터에서 서비스 패브릭 클러스터를 관리할 수 있습니다. **규칙 추가**를 선택하고, 드롭다운 메뉴에서 **사용자 지정 TCP 규칙**을 선택하고, 포트 범위에 `19000-19003`, `19080-19081` 중 하나를 입력한 다음, 원본 드롭 다운을 어디서나로 변경합니다.

마지막으로 배포할 때 응용 프로그램을 볼 수 있도록 포트 8080을 열어 두어야 합니다. **규칙 추가**를 선택하고, 드롭다운 메뉴에서 **사용자 지정 TCP 규칙**을 선택하고, 포트 범위에 `8080`를 입력한 다음, 원본 드롭 다운을 어디서나로 변경합니다.

모든 규칙이 이제 입력됩니다. **저장**을 선택합니다.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>인스턴스에 연결 및 연결의 유효성 검사

보안 그룹 탭의 왼쪽 메뉴에서 **인스턴스**를 선택합니다.  만든 인스턴스 각각을 선택하고 아래 예제에 대한 개인 IP 주소는 `172.31.21.141` 및 `172.31.20.163`를 사용함을 유의합니다.

모든 IP 주소가 연결할 인스턴스 중 하나를 선택하게 한 후 인스턴스를 마우스 오른쪽 단추로 클릭하고 **연결**을 선택합니다.  이 특정 인스턴스에 대한 RDP 파일을 여기에서 다운로드할 수 있습니다.  **원격 데스크톱 파일 다운로드**를 선택한 다음, 이 인스턴스에 RDP(원격 데스크톱 연결)를 설정하려면 다운로드된 파일을 엽니다.  메시지가 표시되면 암호 `serv1ceF@bricP@ssword`를 입력합니다.

![원격 데스크톱 파일 다운로드][aws-rdp]

인스턴스에 성공적으로 연결한 후 서로 연결이 가능하고 파일을 공유할 수 있는지 유효성을 검사합니다.  모든 인스턴스에 대한 IP 주소를 수집했으면 현재 연결되지 않은 IP 주소 하나를 선택합니다. **시작**으로 이동하여 `cmd`을 입력하고 **명령 프롬프트**를 선택합니다.

이 예제에서는 다음 IP 주소 172.31.21.141에 RDP 연결을 설정했습니다. 모든 연결 테스트는 다른 IP 주소 172.31.20.163에서 일어납니다.

기본 연결 작업의 유효성을 검사 하려면 ping 명령을 사용하세요.

```
ping 172.31.20.163
```

출력이 `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128`과 같은 경우 인스턴스 간의 연결이 4차례 반복됩니다.  다음 명령으로 SMB 공유가 작동하는지 유효성을 검사합니다.

```
net use * \\172.31.20.163\c$
```

출력으로 `Drive Z: is now connected to \\172.31.20.163\c$.`을 반환해야 합니다.

## <a name="prep-instances-for-service-fabric"></a>Service Fabric에 대한 인스턴스 준비

처음부터 이를 만들 경우 몇 가지 추가 단계를 수행해야 합니다.  즉, 원격 레지스트리가 실행 중인지 유효성을 검사하고, SMB를 사용하고, SMB 및 원격 레지스트리에 대한 필수 포트를 열어 두어야 합니다.

더 쉽게 하려면 사용자 데이터 스크립트를 사용하여 인스턴스를 부트스트랩할 때 이 모든 작업을 포합합니다.

SMB를 사용하려면 사용한 PowerShell 명령입니다.

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

방화벽에서 포트를 열려면 여기에 PowerShell 명령이 있습니다.

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>다음 단계

시리즈의 1부에서 EC2 인스턴스 3개를 시작하고 Service Fabric 설치에 대해 이를 구성하는 방법을 알아봤습니다.

> [!div class="checklist"]
> * EC2 인스턴스 집합 만들기
> * 보안 그룹 수정
> * 인스턴스 중 하나에 로그인
> * Service Fabric에 대한 인스턴스 준비

클러스터에서 Service Fabric을 구성하려면 시리즈의 2부로 이동합니다.

> [!div class="nextstepaction"]
> [Service Fabric 설치](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png