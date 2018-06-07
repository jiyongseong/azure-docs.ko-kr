---
title: "Azure 스택 데이터 센터 통합-DNS"
description: "Azure 스택 DNS DNS 데이터 센터와 통합 하는 방법에 알아봅니다"
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 5bdac2f3e6082f9449800fe2d4b303e2d59ade46
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-datacenter-integration---dns"></a>Azure 스택 데이터 센터 통합-DNS
Azure 스택 끝점에 액세스할 수 있게 되기를 (`portal`, `adminportal`, `management`, `adminmanagement`등.)  외부 Azure 스택에서 Azure 스택에서 사용 하려는 DNS 영역을 호스트 하는 DNS 서버와 Azure 스택 DNS 서비스를 통합 해야 합니다.

## <a name="azure-stack-dns-namespace"></a>Azure 스택 DNS 네임 스페이스
Azure 스택을 배포할 때 DNS와 관련 된 몇 가지 중요 한 정보를 제공 하면 됩니다.


|필드  |설명  |예|
|---------|---------|---------|
|지역|Azure 스택 배포의 지리적 위치입니다.|`east`|
|외부 도메인 이름|Azure 스택 배포에 사용할 영역을의 이름입니다.|`cloud.fabrikam.com`|
|내부 도메인 이름|스택에서 Azure 인프라 서비스에 사용 되는 내부 영역의 이름입니다.  디렉터리 서비스 통합 하 고 개인 (연결할 수 없음에서 외부 Azure 스택 배포).|`azurestack.local`|
|DNS 전달자|DNS 쿼리, DNS 영역 및 회사 인트라넷 또는 공용 인터넷에서 Azure 스택 외부에서 호스팅되는 레코드를 전달 하는 데 사용 되는 DNS 서버입니다.|`10.57.175.34`<br>`8.8.8.8`|
|(선택 사항) 접두사 이름 지정|Azure 스택 인프라 역할 인스턴스 컴퓨터 이름을 원하는 명명 접두사입니다.  을 지정 하지 않으면 기본값은 `azs`합니다.|`azs`|

Azure 스택 배포 및 끝점의 정규화 된 도메인 이름 (FQDN)은 영역 매개 변수 및 외부 도메인 이름 매개 변수 조합 합니다. 사용 하 여 예제에서 값은 앞의 표에서,이 Azure 스택 배포에 대 한 FQDN 것 다음 이름:

`east.cloud.fabrikam.com`

따라서이 배포에 대 한 끝점의 예는 다음 Url 같습니다.

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Azure 스택 배포에 대 한이 예제에서는 DNS 네임 스페이스를 사용 하려면 다음과 같은 요소가 필요 합니다.

- 영역 `fabrikam.com` 도메인 등록 기관, 회사 내부 DNS 서버 또는 사용자 이름 확인 요구 사항에 따라 둘 다를 사용 하 여 등록 됩니다.
- 자식 도메인 `cloud.fabrikam.com` 영역 아래 `fabrikam.com`합니다.
- 영역을 호스트 하는 DNS 서버 `fabrikam.com` 및 `cloud.fabrikam.com` Azure 스택 배포에서 연결할 수 있습니다.

Azure 스택 끝점 및 외부 Azure 스택에서 인스턴스에 대 한 DNS 이름을 확인할 수 있으려면 사용 하려면 부모 영역을 호스트 하는 DNS 서버와 함께 Azure 스택에 대 한 외부 DNS 영역을 호스팅하는 DNS 서버를 통합 해야 합니다.


## <a name="resolution-and-delegation"></a>확인 및 위임

DNS 서버에는 다음 두 가지 유형이 있습니다.

- 권한 있는 DNS 서버는 DNS 영역을 호스트 합니다. 해당 영역의 레코드에 대한 DNS 쿼리에만 대답합니다.
- 재귀 DNS 서버는 DNS 영역을 호스팅하지 않습니다. 권한이 있는 DNS 서버를 호출하고 필요한 데이터를 수집하여 모든 DNS 쿼리에 응답합니다.

Azure 스택 권한이 모두 포함 및 재귀 DNS 서버입니다. 재귀 서버는 해당 Azure 스택 배포에 대 한 내부 개인 영역과 외부 공용 DNS 영역을 제외한 모든 항목의 이름을 확인 하기 위해 사용 됩니다. 

![Azure 스택 DNS 아키텍처](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Azure 스택에서 외부 DNS 이름 확인

Azure 스택 외부 끝점에 대 한 DNS 이름을 확인 하기 위해 (예: www.bing.com), Azure 스택 Azure 스택 권한을 보유 하지 않은 DNS 요청을 전달 하는 데 사용할 수 있는 DNS 서버를 제공 해야 합니다. 배포의 경우 Azure 스택 요청을 전달 하는 DNS 서버 (DNS 전달자 필드)에 배포 워크시트에 필요 합니다. 내결함성에 대 한이 필드에 두 개 이상의 서버를 제공 합니다. 이러한 값이 없는 Azure 스택 배포가 실패합니다.

### <a name="configure-conditional-dns-forwarding"></a>조건부 DNS 전달 구성

> [!IMPORTANT]
> 이 AD FS 배포에만 적용 됩니다.

기존 DNS 인프라와 이름 확인을 사용 하도록 설정 하려면 조건부 전달을 구성 합니다.

조건 전달자를 추가 하려면 권한 있는 끝점을 사용 해야 합니다.

이 절차에서는 Azure 스택의 권한 있는 끝점과 통신할 수 있는 데이터 센터 네트워크의 컴퓨터를 사용 합니다.

1. 관리자 권한 Windows PowerShell 세션 (관리자 권한으로 실행)를 열고 권한 있는 끝점의 IP 주소에 연결 합니다. CloudAdmin 인증에 대 한 자격 증명을 사용 합니다.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. 권한 있는 끝점에 연결한 후 다음 PowerShell 명령을 실행 합니다. 도메인 이름 및 사용 하려는 DNS 서버의 IP 주소와 함께 제공 되는 샘플 값으로 대체 합니다.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>외부 Azure 스택에서 Azure 스택 DNS 이름 확인
권한 있는 서버가 외부 DNS 영역 정보를 포함 하는 것과 및 모든 사용자가 만든 영역입니다. 영역 위임 또는 외부 Azure 스택에서 Azure 스택 DNS 이름을 확인 하기 위해 조건부 전달 수 있도록 이러한 서버와 통합 합니다.

## <a name="get-dns-server-external-endpoint-information"></a>DNS 서버 외부 끝점 정보 가져오기

DNS 인프라와 Azure 스택 배포와 통합 하려면 다음 정보가 필요 합니다.

- DNS 서버 Fqdn
- DNS 서버 IP 주소

Azure 스택 DNS 서버에 대 한 Fqdn에는 다음 형식을 갖습니다.

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

샘플 값을 DNS에 대 한 Fqdn을 사용 하 여 서버는 같습니다.

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


이 정보는 라는 파일에 모든 Azure 스택 배포의 끝에 작성도 `AzureStackStampDeploymentInfo.json`합니다. 이 파일은 `C:\CloudDeployment\logs` 배포 가상 컴퓨터의 폴더입니다. Azure 스택 배포에 사용 된 어떤 값 확실 하지 않은 경우에 여기에서 값을 가져올 수 있습니다.

가상 컴퓨터 배포를 더 이상 사용할 수 없거나 액세스할 수, 하는 경우 권한 있는 끝점에 연결 하 고 실행 하 여 값을 얻을 수 없습니다는 `Get-AzureStackInfo` PowerShell cmdlet. 권한 있는 끝점에 대 한 자세한 내용은 (insert 여기 문서에 연결)을 참조 하십시오.

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Azure 스택 조건부 전달 설정

Azure 스택 DNS 인프라와 통합 하 간단 하 고 가장 안전한 방법은 부모 영역을 호스트 하는 서버에서 영역의 조건부 전달 작업을 수행 하는 것입니다. Azure 스택 외부 DNS 네임 스페이스에 대 한 DNS 서버에 대해 직접적인 제어 부모 영역을 호스트 하는 경우이 방법은 권장 됩니다.

Dns 조건부 전달 하는 방법을 잘 모르는 경우 다음 TechNet 문서 참조: [도메인 이름에 대 한 조건 전달자 할당](https://technet.microsoft.com/library/cc794735), 또는 DNS 솔루션에 특정 한 설명서입니다.

회사 도메인 이름의 하위 도메인 모양 외부 Azure 스택 DNS 영역이 지정 된 있는 시나리오에서는 조건부 전달을 사용할 수 없습니다. DNS 위임을 구성 해야 합니다.

예:

- 회사 DNS 도메인 이름: `contoso.com`
- Azure 스택 외부 DNS 도메인 이름: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Azure 스택 외부 DNS 영역 위임

DNS 이름은 Azure 스택 배포 외부에서 확인할 수에 대 한 DNS 위임을 설정 해야 합니다.

각 등록 기관에는 도메인에 대한 이름 서버 레코드를 변경하는 자체 DNS 관리 도구가 있습니다. 등록자의 DNS 관리 페이지에서 NS 레코드를 편집 하 고 Azure 스택에 있는 것으로 영역에 대 한 NS 레코드를 바꿉니다.

대부분의 DNS 등록 기관 최소 위임을 완료 하려면 두 명의 DNS 서버가 제공 하도록 요구 합니다.

## <a name="next-steps"></a>다음 단계

[방화벽 통합](azure-stack-firewall.md)
