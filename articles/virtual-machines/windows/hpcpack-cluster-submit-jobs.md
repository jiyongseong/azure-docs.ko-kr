---
title: Azure에서 HPC 팩 클러스터에 작업 제출 | Microsoft Docs
description: Azure에서 HPC 팩 클러스터로 작업을 제출하기 위해 온-프레미스 컴퓨터를 설정하는 방법
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 78f6833c-4aa6-4b3e-be71-97201abb4721
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 025ff3dea365ab75af55f107da1fb7331861eb06
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166372"
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>온-프레미스 컴퓨터에서 Azure에 배포된 HPC 팩 클러스터로 HPC 작업 제출
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure의 [Microsoft HPC 팩](https://technet.microsoft.com/library/cc514029) 클러스터로 작업을 제출하도록 온-프레미스 클라이언트 컴퓨터를 구성합니다. 이 문서에서는 HTTPS를 통해 Azure의 클러스터로 작업을 제출하도록 클라이언트 도구를 사용하여 로컬 컴퓨터를 설정하는 방법을 보여 줍니다. 이 방식으로 여러 클러스터 사용자가 헤드 노드 VM에 직접 연결하거나 Azure 구독에 액세스하지 않고도 클라우드 기반 HPC 팩 클러스터로 작업을 제출할 수 있습니다.

![Azure의 클러스터로 작업 제출][jobsubmit]

## <a name="prerequisites"></a>필수 조건
* **Azure VM에 배포된 HPC 팩 헤드 노드** - [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)과 같은 자동화된 도구를 사용하여 헤드 노드 및 클러스터를 배포하는 것이 좋습니다. 이 문서의 단계를 완료하려면 헤드 노드의 DNS 이름 및 클러스터 관리자 자격 증명이 필요합니다.
* **클라이언트 컴퓨터** - HPC 팩 클라이언트 유틸리티를 실행할 수 있는 Windows 또는 Windows Server 클라이언트 컴퓨터가 필요합니다([시스템 요구 사항](https://technet.microsoft.com/library/dn535781.aspx) 참조). HPC 팩 웹 포털 또는 REST API를 사용하여 작업을 제출하려는 경우 사용자가 선택한 모든 클라이언트 컴퓨터를 사용할 수 있습니다.
* **HPC 팩 설치 미디어** - HPC 팩 클라이언트 유틸리티를 설치하려면 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=56360)에서 최신 HPC 팩 버전의 체험 설치 패키지를 사용할 수 있습니다. 헤드 노드 VM에 설치된 HPC 팩과 동일한 버전의 HPC 팩을 다운로드해야 합니다.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>1단계: 헤드 노드에 웹 구성 요소 설치 및 구성
HTTPS를 통해 클러스터로 작업을 제출하도록 REST 인터페이스를 설정하려면 HPC 팩 헤드 노드에 HPC 팩 웹 구성 요소를 구성합니다. 아직 설치되지 않았으면 먼저 HpcWebComponents.msi 설치 파일을 실행하여 웹 구성 요소를 설치합니다. 그런 다음 HPC PowerShell 스크립트 **Set-HPCWebComponents.ps1**을 실행하여 구성 요소를 구성합니다.

자세한 절차는 [Microsoft HPC 팩 웹 구성 요소 설치](http://technet.microsoft.com/library/hh314627.aspx)를 참조하세요.

> [!TIP]
> HPC 팩 클러스터에 대한 특정 Azure 빠른 시작 템플릿은 웹 구성 요소를 자동으로 설치하고 구성합니다.
> 
> 

**웹 구성 요소를 설치하려면**

1. 클러스터 관리자의 자격 증명을 사용하여 헤드 노드 VM에 연결합니다.
2. HPC 팩 설치 폴더에서 헤드 노드에 HpcWebComponents.msi를 실행합니다.
3. 마법사의 단계를 따라 웹 구성 요소 설치

**웹 구성 요소를 구성하려면**

1. 헤드 노드에서 관리자 권한으로 HPC PowerShell을 시작합니다.
2. 디렉터리를 구성 스크립트의 위치로 변경하려면 다음 명령을 입력합니다.
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. REST 인터페이스를 구성하고 HPC 웹 서비스를 시작하려면 다음 명령을 입력합니다.
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. 인증서를 선택하라는 메시지가 표시되면 헤드 노드의 공용 DNS 이름에 해당하는 인증서를 선택합니다. 예를 들어 클래식 배포 모델을 사용하여 헤드 노드 VM을 배포하는 경우 인증서 이름은 CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net 형식입니다. Resource Manager 배포 모델을 사용하는 경우 인증서 이름은 CN=&lt;*HeadNodeDnsName*&gt;.&lt;*region*&gt;.cloudapp.azure.com 형식입니다.
   
   > [!NOTE]
   > 나중에 온-프레미스 컴퓨터에서 헤드 노드로 작업을 제출할 때 이 인증서를 선택합니다. Active Directory 도메인에 있는 헤드 노드의 컴퓨터 이름(예: CN=*MyHPCHeadNode.HpcAzure.local*)에 해당하는 인증서를 선택 또는 구성하지 마십시오.
   > 
   > 
5. 작업 제출을 위해 웹 포털을 구성하려면 다음 명령을 입력합니다.
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. 스크립트가 완료되면 다음 명령을 입력하여 HPC 작업 Scheduler 서비스를 중지했다 다시 시작합니다.
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>2단계: 온-프레미스 컴퓨터에 HPC 팩 클라이언트 유틸리티 설치
컴퓨터에 HPC Pack 클라이언트 유틸리티를 설치하려면 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=56360)에서 HPC Pack 설치 파일(전체 설치)을 다운로드합니다. 설치를 시작할 때 **HPC Pack 클라이언트 유틸리티**에 대한 설정 옵션을 선택합니다.

HPC 팩 클라이언트 도구를 사용하여 헤드 노드 VM으로 작업을 제출하려면 헤드 노드에서 인증서를 내보내고 클라이언트 컴퓨터에 설치해야 합니다. 인증서는 .CER 형식이어야 합니다.

**헤드 노드에서 인증서를 내보내려면**

1. 헤드 노드에서 로컬 컴퓨터 계정의 Microsoft Management Console에 인증서 스냅인을 추가합니다. 스냅인을 추가하는 단계는 [MMC에 인증서 스냅인 추가](https://technet.microsoft.com/library/cc754431.aspx)를 참조하세요.
2. 콘솔 트리에서 **인증서 - 로컬 컴퓨터** > **개인**을 확장한 다음 **인증서**를 클릭합니다.
3. [1단계: 헤드 노드에 웹 구성 요소 설치 및 구성](#step-1:-install-and-configure-the-web-components-on-the-head-node)에서 HPC Pack 웹 구성 요소에 구성한 인증서를 찾습니다(예: CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net).
4. 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업** > **내보내기**를 클릭합니다.
5. 인증서 내보내기 마법사에서 **다음**을 클릭하고 **아니요, 개인 키를 내보내지 않습니다.** 를 선택합니다.
6. 마법사의 나머지 단계를 따라 인증서를 DER 인코딩 이진 X.509(.CER) 형식으로 내보냅니다.

**클라이언트 컴퓨터로 인증서를 가져오려면**

1. 헤드 노드에서 클라이언트 컴퓨터의 폴더로 내보낸 인증서를 복사합니다.
2. 클라이언트 컴퓨터에서 certmgr.msc를 실행합니다.
3. 인증서 관리자에서 **인증서 - 현재 사용자** > **신뢰할 수 있는 루트 인증 기관**을 확장한 후 **인증서**를 마우스 오른쪽 단추로 클릭하고 **모든 작업** > **가져오기**를 클릭합니다.
4. 인증서 가져오기 마법사에서 **다음** 을 클릭하고 헤드 노드에서 신뢰할 수 있는 루트 인증 기관 저장소로 내보낸 인증서를 가져오는 단계를 따릅니다.

> [!TIP]
> 클라이언트 컴퓨터가 헤드 노드의 인증 기관을 인식할 수 없기 때문에 보안 경고가 표시될 수 있습니다. 테스트 목적으로 이 경고를 무시하고 인증서 가져오기를 완료할 수 있습니다.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>3단계: 클러스터에서 테스트 작업 실행
구성을 확인하려면 온-프레미스 컴퓨터에서 Azure의 클러스터에 작업을 실행해 봅니다. 예를 들어 HPC 팩 GUI 도구 또는 명령줄 명령을 사용하여 클러스터로 작업을 제출할 수 있습니다. 웹 기반 포털을 사용하여 작업을 제출할 수도 있습니다.

**클라이언트 컴퓨터에서 작업 제출 명령을 실행하려면**

1. HPC Pack 클라이언트 유틸리티가 설치되어 있는 클라이언트 컴퓨터에서 명령 프롬프트를 시작합니다.
2. 샘플 명령을 입력합니다. 예를 들어, 클러스터의 모든 작업을 나열하려면, 헤드 노드의 전체 DNS 이름에 따라서 다음 중 하나와 유사한 명령을 입력합니다.
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    또는
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > 스케줄러 URL에 IP 주소가 아닌 헤드 노드의 전체 DNS 이름을 사용합니다. IP 주소를 지정할 경우 "서버 인증서에 유효한 신뢰 체인이 있거나 서버 인증서를 신뢰할 수 있는 루트 저장소에 저장해야 합니다."와 유사한 오류가 표시됩니다.
   > 
   > 
3. 메시지가 표시되면 구성해 놓은 HPC 클러스터 관리자 또는 다른 클러스터 사용자의 사용자 이름(&lt;DomainName&gt;\\&lt;UserName&gt; 형식)과 암호를 입력합니다. 추가 작업에 대해 자격 증명을 저장할 수도 있습니다.
   
    작업 목록이 나타납니다.

**클라이언트 컴퓨터에서 HPC 작업 관리자를 사용하려면**

1. 작업을 제출할 때 이전에 클러스터 사용자의 도메인 자격 증명을 저장해 놓지 경우 자격 증명 관리자에서 자격 증명을 추가할 수 있습니다.
   
    a. 클라이언트 컴퓨터의 제어판에서 자격 증명 관리자를 시작합니다.
   
    나. **Windows 자격 증명** > **일반 자격 증명 추가**를 클릭합니다.
   
    다. 구성한 클러스터 관리자 또는 다른 클러스터 사용자의 인터넷 주소(예: https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler 또는 https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com/HpcScheduler)와 사용자 이름(&lt;도메인 이름&gt;\\&lt;사용자 이름&gt;) 및 암호를 지정합니다.
2. 클라이언트 컴퓨터에서 HPC 작업 관리자를 시작합니다.
3. **헤드 노드 선택** 대화 상자에서 Azure 헤드 노드의 URL을 입력합니다(예: https://&lt;HeadNodeDnsName&gt;.cloudapp.net 또는 https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com).
   
    HPC 작업 관리자가 열리고 헤드 노드의 작업 목록이 표시됩니다.

**헤드 노드에서 실행되는 웹 포털을 사용하려면**

1. 클라이언트 컴퓨터에서 웹 브라우저를 시작하고, 헤드 노드의 전체 DNS 이름에 따라서 다음 주소 중 하나를 입력합니다.
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    또는
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. 보안 대화 상자가 나타나면 HPC 클러스터 관리자의 도메인 자격 증명을 입력합니다. (다른 역할의 다른 클러스터 사용자를 추가할 수도 있습니다. [클러스터 사용자 관리](https://technet.microsoft.com/library/ff919335.aspx)를 참조하세요.)
   
    웹 포털이 작업 목록 보기로 열립니다.
3. 클러스터에서 "Hello World" 문자열을 반환하는 샘플 작업을 제출하려면 왼쪽 탐색 창에서 **새 작업** 을 클릭합니다.
4. **새 작업** 페이지의 **제출 페이지에서**에서 **HelloWorld**를 클릭합니다. 작업 제출 페이지가 나타납니다.
5. **Submit**를 클릭합니다. 메시지가 표시되면 HPC 클러스터 관리자의 도메인 자격 증명을 입력합니다. 작업이 제출되고 **내 작업** 페이지에 작업 ID가 나타납니다.
6. 제출한 작업의 결과를 보려면 작업 ID를 클릭한 다음 **작업 보기**를 클릭하여(**출력**에서) 명령 출력을 확인합니다.

## <a name="next-steps"></a>다음 단계
* [HPC 팩 REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)를 사용해도 Azure 클러스터에 작업을 제출할 수 있습니다.
* Linux 클라이언트에서 클러스터 작업을 제출하려는 경우 [HPC Pack 2012 R2 SDK 및 샘플 코드](https://www.microsoft.com/download/details.aspx?id=41633)의 Python 샘플을 참조하세요.

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
