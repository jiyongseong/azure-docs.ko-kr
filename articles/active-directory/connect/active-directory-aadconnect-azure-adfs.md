---
title: Azure의 Active Directory Federation Services | Microsoft Docs
description: 이 문서에서는 고가용성을 위해 Azure에서 AD FS를 배포하는 방법을 알아봅니다.
keywords: Azure에 AD FS 배포, Azure ADFS 배포, Azure ADFS, Azure AD FS, ADFS 배포, AD FS 배포, Azure에서 ADFS, Azure에서 ADFS 배포, Azure에서 AD FS배포, ADFS Azure, AD FS 소개, Azure, Azure에서 AD FS, IaaS, ADFS, Azure에 ADFS 이동
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: 692a188c-badc-44aa-ba86-71c0e8074510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: anandy; billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76ed05d55389e2c05b38fe1f2c239f544c6a5d38
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="deploying-active-directory-federation-services-in-azure"></a>Azure에서 Active Directory Federation Services 배포
AD FS는 간편하고 안전한 ID 페더레이션 및 웹 Single Sign-on(SSO) 기능을 제공합니다. 사용자는 Azure AD 또는 O365와 함께 페더레이션을 통해 온-프레미스 자격 증명을 사용하여 인증하고 클라우드에서 모든 리소스에 액세스할 수 있습니다. 결과적으로 온-프레미스 및 클라우드에서 리소스에 대한 액세스를 보장하는 항상 사용 가능한 AD FS 인프라가 있는 것이 중요합니다. Azure에서 AD FS를 배포하면 필요한 최소한의 노력으로 고가용성을 실현할 수 있습니다.
Azure에서 AD FS를 배포하는 여러 가지 이점의 일부는 다음과 같습니다.

* **고가용성** - Azure 가용성 집합의 기능을 사용하여 인프라를 항상 사용 가능하도록 합니다.
* **규모 조정 용이** – 더 좋은 성능이 필요한가요? Azure에서 몇 번 클릭하면 더 강력한 컴퓨터로 쉽게 마이그레이션합니다
* **지역간 중복성** – Azure 지역 중복성을 사용하여 인프라를 전 세계에 걸쳐 항상 사용 가능하도록 보장할 수 있습니다
* **관리 용이** – Azure Portal의 단순화된 관리 옵션을 사용하여 인프라를 관리하는 작업이 매우 간편하고 깔끔해 집니다. 

## <a name="design-principles"></a>디자인 원칙
![배포 설계](./media/active-directory-aadconnect-azure-adfs/deployment.png)

위의 다이어그램에서는 Azure에서 AD FS 인프라를 배포하기 시작하는 데 권장되는 기본 토폴로지를 보여 줍니다. 토폴로지의 다양한 구성 요소의 원리는 다음과 같습니다.

* **DC/AD FS 서버**: 사용자가 1,000명보다 적은 경우 도메인 컨트롤러에 AD FS 역할을 설치할 수 있습니다. 도메인 컨트롤러의 성능에 영향을 주지 않도록 하려는 경우 또는 사용자가 1,000명 이상인 경우 별도 서버에 AD FS를 배포합니다.
* **WAP 서버** – 사용자가 회사 네트워크에 있지 않은 경우 AD FS에 연결할 수 있도록 웹 응용 프로그램 프록시 서버를 배포해야 합니다.
* **DMZ**: 웹 응용 프로그램 프록시 서버는 DMZ에 배치되고 DMZ와 내부 서브넷 간에 TCP/443 액세스만이 허용됩니다.
* **부하 분산 장치**: AD FS 및 웹 응용 프로그램 프록시 서버의 고가용성을 보장하려면 AD FS 서버용 내부 부하 분산 장치 및 웹 응용 프로그램 프록시 서버용 Azure Load Balancer를 사용하는 것이 좋습니다.
* **가용성 집합**: AD FS 배포에 대한 중복성을 제공하려면 비슷한 워크로드에 대한 가용성 집합에 두 개 이상의 가상 머신을 그룹화하는 것이 좋습니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 머신을 사용할 수 있습니다.
* **Storage 계정**: 두 개의 Storage 계정을 사용하는 것이 좋습니다. 단일 저장소 계정을 만들면 단일 실패 지점이 생성될 수 있고 저장소 계정이 중단되는 가능성이 적은 시나리오에서 배포를 사용할 수 없게 될 수 있습니다. 두 개의 저장소 계정이 있으면 각 오류 줄에 하나의 저장소 계정을 연결할 수 있습니다.
* **네트워크 분리**: 웹 응용 프로그램 프록시 서버를 별도의 DMZ 네트워크에 배포해야 합니다. 하나의 가상 네트워크를 두 개의 서브넷으로 나누고 격리된 서브넷에는 웹 응용 프로그램 프록시 서버를 배포할 수 있습니다. 각 서브넷에 대한 네트워크 보안 그룹 설정을 구성하고 두 개의 서브넷 간에 필요한 통신만을 허용할 수 있습니다. 자세한 내용은 아래 배포 시나리오 별로 지정됩니다.

## <a name="steps-to-deploy-ad-fs-in-azure"></a>Azure에서 AD FS를 배포하는 단계
이 섹션에 설명한 단계는 아래와 같이 Azure에서 AD FS 인프라를 배포하는 가이드를 설명합니다.

### <a name="1-deploying-the-network"></a>1. 네트워크 배포
위에서 설명한 대로 단일 가상 네트워크에 두 개의 서브넷을 만들거나 두 개의 완전히 다른 가상 네트워크(VNet)를 만들 수 있습니다. 이 문서는 단일 가상 네트워크를 배포하는 데 집중하고 해당 네트워크를 두 개의 서브넷으로 나눕니다. 두 개의 별도 VNet에 통신용 VNet간 게이트웨이가 필요하기 때문에 현재로서는 쉬운 방법입니다.

**1.1 가상 네트워크 만들기**

![가상 네트워크 만들기](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)

한 번 클릭하여 Azure Portal에서 가상 네트워크를 선택하고 가상 네트워크와 서브넷 하나를 즉시 배포할 수 있습니다. INT 서브넷을 정의하여 이제 VM을 추가할 준비가 되었습니다.
다음 단계는 네트워크에 다른 서브넷, 즉 DMZ 서브넷을 추가하는 작업입니다. DMZ 서브넷을 만들려면

* 새로 만든 네트워크 선택
* 속성에서 서브넷 선택
* 서브넷 패널에서 추가 단추 클릭
* 서브넷을 만들려는 서브넷 이름 및 주소 공간 정보 제공

![서브넷](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)

![서브넷 DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. 네트워크 보안 그룹 만들기**

NSG(네트워크 보안 그룹)은 ACL(Access Control 목록)의 Virtual Network에 VM 인스턴스에 대한 허용 또는 거부 네트워크 트래픽 규칙의 목록을 포함합니다. Nsg는 서브넷 또는 서브넷 내의 개별 VM 인스턴스 중 하나와 연결될 수 있습니다. NSG를 서브넷과 연결한 경우 ACL 규칙은 해당 서브넷에 있는 모든 VM 인스턴스에 적용됩니다.
이 지침을 위해 DMZ와 내부 네트워크용으로 각각 하나씩 두 개의 NSG를 만듭니다. 각각 NSG_INT 및 NSG_DMZ라는 레이블이 지정됩니다.

![NSG 만들기](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

NSG를 만든 후에 인바운드 및 아웃 바운드 규칙이 없어집니다. 각 서버의 역할이 설치되고 작동하면 원하는 보안 수준에 따라 인바운드 및 아웃 바운드 규칙을 만들 수 있습니다.

![NSG 초기화](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

NSG를 만든 후에 NSG_INT를 서브넷 INT와 연결하고 NSG_DMZ를 서브넷 DMZ와 연결합니다. 예제 스크린샷은 다음과 같습니다.

![NSG 구성](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* 서브넷을 클릭하여 서브넷에 대한 패널을 엽니다.
* NSG와 연결할 서브넷을 선택합니다. 

구성한 후에 서브넷에 대한 패널은 다음과 같이 표시됩니다.

![NSG 이후 서브넷](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. 온-프레미스에 대한 연결 만들기**

Azure에서 DC(도메인 컨트롤러)를 배포하기 위해 온-프레미스에 대한 연결이 필요합니다. Azure에서는 Azure 인프라에 온-프레미스 인프라를 연결하는 다양한 연결 옵션을 제공합니다.

* 지점 및 사이트 간
* Virtual Network 사이트 간
* ExpressRoute

ExpressRoute를 사용하는 것이 좋습니다. ExpressRoute를 사용하면 온-프레미스 또는 공동 배치 환경의 인프라와 Azure 데이터 센터 간에 개인 연결을 만들 수 있습니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. Express 경로는 일반적인 인터넷을 통한 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.
ExpressRoute를 사용하는 것이 좋지만 조직에 가장 적합한 연결 방법을 선택할 수 있습니다. ExpressRoute 및 Express 경로를 사용하는 다양한 연결 옵션에 대해 자세히 알아보려면 [ExpressRoute 기술 개요](https://aka.ms/Azure/ExpressRoute)를 참고합니다.

### <a name="2-create-storage-accounts"></a>2. 저장소 계정 만들기
고가용성을 유지하고 단일 저장소 계정에 대한 종속성을 방지하기 위해 두 개의 저장소 계정을 만들 수 있습니다. 각 가용성 집합의 컴퓨터를 두 그룹으로 나누고 각 그룹에 별도 저장소 계정을 지정합니다.

![저장소 계정 만들기](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. 가용성 집합 만들기
각 역할(DC/AD FS 및 WAP)에 최소한 2대의 컴퓨터를 포함하는 가용성 집합을 만듭니다. 각 역할이 높은 가용성을 달성하는 데 도움이 됩니다. 가용성 집합을 만드는 동안 필수적으로 다음을 결정해야 합니다.

* **장애 도메인**: 동일한 장애 도메인에 있는 가상 머신은 동일한 전원 및 물리적 네트워크 스위치를 공유합니다. 최소 2개의 장애 도메인을 사용하는 것이 좋습니다. 기본값은 3개이며 배포하기 위해 그대로 둘 수 있습니다.
* **업데이트 도메인**: 업데이트하는 동안 동일한 업데이트 도메인에 속한 컴퓨터는 모두 다시 시작됩니다. 최소 2개의 업데이트 도메인이 있어야 합니다. 기본값은 5개이며 배포하기 위해 그대로 둘 수 있습니다.

![가용성 집합](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

다음과 같은 가용성 집합 만들기

| 가용성 집합 | 역할 | 장애 도메인 | 업데이트 도메인 |
|:---:|:---:|:---:|:--- |
| contosodcset |DC/ADFS |3 |5 |
| contosowapset |WAP |3 |5 |

### <a name="4-deploy-virtual-machines"></a>4. 가상 머신 배포
다음 단계는 인프라의 다양한 역할을 호스팅하는 가상 머신을 배포하는 것입니다. 각 가용성 집합에서 최소 두 대의 컴퓨터를 사용하는 것이 좋습니다. 기본 배포를 위해 4개의 가상 머신을 만듭니다.

| 컴퓨터 | 역할 | 서브넷 | 가용성 집합 | Storage 계정 | IP 주소 |
|:---:|:---:|:---:|:---:|:---:|:---:|
| contosodc1 |DC/ADFS |INT |contosodcset |contososac1 |공용 |
| contosodc2 |DC/ADFS |INT |contosodcset |contososac2 |공용 |
| contosowap1 |WAP |DMZ |contosowapset |contososac1 |공용 |
| contosowap2 |WAP |DMZ |contosowapset |contososac2 |공용 |

보시는 것처럼 NSG가 지정되지 않았습니다. Azure가 서브넷 수준에서 NSG를 사용할 수 있도록 하기 때문입니다. 그러면 서브넷 또는 NIC 개체와 연결된 개별 NSG를 사용하여 컴퓨터 네트워크 트래픽을 제어할 수 있습니다. 자세한 내용은 [NSG(네트워크 보안 그룹)란](https://aka.ms/Azure/NSG)을 참고하세요.
DNS를 관리하는 경우 고정 IP 주소를 사용하는 것이 좋습니다. 도메인에 대한 DNS 레코드 대신 Azure DNS를 사용할 수 있으며 해당 Azure FQDN에 의한 새 컴퓨터를 참조할 수 있습니다.
가상 컴퓨터 창은 배포가 완료된 후에 다음과 같이 표시됩니다.

![배포된 Virtual Machines](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. 도메인 컨트롤러/AD FS 서버 구성
 들어오는 모든 요청을 인증하기 위해 AD FS는 도메인 컨트롤러에 연결해야 합니다. 인증을 위해 Azure에서 온-프레미스 DC까지 비용이 많이 드는 여정을 저장하려면 Azure에서 도메인 컨트롤러의 복제본을 배포하는 것이 좋습니다. 고가용성을 달성하기 위해 최소 2개의 도메인 컨트롤러 가용성 집합을 만드는 것이 좋습니다.

| 도메인 컨트롤러 | 역할 | Storage 계정 |
|:---:|:---:|:---:|
| contosodc1 |복제본 |contososac1 |
| contosodc2 |복제본 |contososac2 |

* DNS을 사용하여 두 서버를 복제본 도메인 컨트롤러로 승격
* 서버 관리자를 사용하는 AD FS 역할을 설치하여 AD FS 서버를 구성합니다.

### <a name="6-deploying-internal-load-balancer-ilb"></a>6. ILB(내부 부하 분산 장치) 배포
**6.1. ILB 만들기**

ILB를 배포하려면 Azure Portal에서 부하 분산 장치를 선택하고 추가(+)를 클릭합니다.

> [!NOTE]
> 메뉴에 **부하 분산 장치**가 표시되지 않으면 포털의 왼쪽 아래에서 **찾아보기**를 클릭하고 **부하 분산 장치**가 표시될 때까지 스크롤을 내립니다.  노란색 별표를 클릭하여 메뉴에 추가합니다. 이제 새 부하 분산 장치 아이콘을 선택하여 패널을 열고 부하 분산 장치를 구성하기 시작합니다.
> 
> 

![부하 분산 장치 찾아보기](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **이름**: 부하 분산 장치에 적합한 이름을 지정합니다.
* **구성표**: 이 부하 분산 장치가 AD FS 서버 앞에 배치되고 내부 네트워크 연결에만 사용되기 때문에 "내부"를 선택합니다
* **Virtual Network**: AD FS를 배포할 가상 네트워크를 선택합니다.
* **서브넷**: 여기서 내부 서브넷을 선택합니다.
* **IP 주소 할당**: 정적

![내부 부하 분산 장치](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)

만들기를 클릭하여 ILB가 배포되면 부하 분산 장치 목록에 표시됩니다.

![ILB 이후 부하 분산 장치](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)

다음 단계는 백 엔드 풀 및 백 엔드 프로브를 구성하는 것입니다.

**6.2. ILB 백 엔드 풀 구성**

부하 분산 장치 패널에서 새로 만든 ILB를 선택합니다. 설정 패널이 열립니다. 

1. 설정 패널에서 백 엔드 풀을 선택합니다.
2. 백 엔드 풀 추가 패널에서 가상 머신 추가를 클릭합니다.
3. 가용성 집합을 선택할 수 있는 패널로 나타납니다.
4. AD FS 가용성 집합을 선택합니다.

![ILB 백 엔드 풀 구성](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)

**6.3. 프로브 구성**

ILB 설정 패널에서 프로브를 선택합니다.

1. 추가를 클릭합니다.
2. 프로브에 대한 세부 정보를 제공합니다. a. **이름**: 이름을 검색합니다. b. **프로토콜**: TCP c. **포트**: 443(HTTPS) d. **간격**: 기본값 5는 ILB가 백 엔드 풀에 있는 컴퓨터를 검색하는 간격입니다. e. **비정상 임계값 제한**: 기본값 2는 ILB가 백 엔드 풀에 있는 컴퓨터를 응답하지 않는다고 선언하고 트래픽을 보내지 않고 나서 연속 프로브 오류가 발생하는 임계값입니다.

![ILB 프로브 구성](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)

**6.4. 부하 분산 규칙 만들기**

트래픽을 효과적으로 분산하기 위해 ILB는 부하 분산 규칙을 사용하여 구성해야 합니다. 부하 분산 규칙을 만들려면 

1. ILB의 설정 패널에서 부하 분산 규칙을 선택합니다.
2. 부하 분산 규칙 패널에서 추가를 클릭합니다.
3. 부하 분산 규칙 추가 패널에서 a. **이름**: 규칙의 이름을 지정합니다. b. **프로토콜**: TCP를 선택합니다. c. **포트**: 443 d. **백 엔드 포트**: 443 e. **백 엔드 풀**: 이전에 AD FS 클러스터에 만든 풀을 선택합니다. f. **프로브**: 이전에 AD FS 서버에 만든 프로브를 선택합니다.

![ILB 분산 규칙 구성](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. ILB를 사용하여 DNS 업데이트**

DNS 서버로 이동하고 ILB에 대한 CNAME을 만듭니다. CNAME은 ILB의 IP 주소를 가리키는 IP 주소를 가진 페더레이션 서비스를 제공해야 합니다. 예를 들어 ILB DIP 주소가 10.3.0.8이고 설치된 페더레이션 서비스가 fs.contoso.com이면 10.3.0.8을 가리키는 fs.contoso.com에 대한 CNAME을 만듭니다.
이렇게 하면 fs.contoso.com과 관련된 모든 통신은 ILB로 끝나게 되고 적절하게 라우팅됩니다.

### <a name="7-configuring-the-web-application-proxy-server"></a>7. 웹 응용 프로그램 프록시 서버 구성
**7.1. AD FS 서버에 연결할 웹 응용 프로그램 프록시 서버 구성**

웹 응용 프로그램 프록시 서버가 ILB 다음에 AD FS 서버에 연결할 수 있도록 하려면 ILB용 %systemroot%\system32\drivers\etc\hosts에 레코드를 만듭니다. DN(고유 이름)은 페더레이션 서비스 이름(예: fs.contoso.com)이 되도록 해야 합니다. 또한 IP 항목은 ILB의 IP 주소(이 예제에서와 같이 10.3.0.8)여야 합니다.

**7.2. 웹 응용 프로그램 프록시 역할 설치**

웹 응용 프로그램 프록시 서버가 ILB 다음에 AD FS 서버에 연결할 수 있는지를 확인한 후에 웹 응용 프로그램 프록시 서버를 설치할 수 있습니다. 웹 응용 프로그램 프록시 서버는 도메인에 조인할 필요가 없습니다. 원격 액세스 역할을 선택하여 두 개의 웹 응용 프로그램 프록시 서버에 웹 응용 프로그램 프록시 역할을 설치합니다. 서버 관리자에서는 WAP 설치를 완료하도록 안내합니다.
WAP를 배포하는 방법에 대한 자세한 내용은 [웹 응용 프로그램 프록시 서버 설치 및 구성](https://technet.microsoft.com/library/dn383662.aspx)을 참고합니다.

### <a name="8--deploying-the-internet-facing-public-load-balancer"></a>8.  인터넷 연결 (공용) 부하 분산 장치 배포
**8.1.  인터넷 연결 (공용) 부하 분산 장치 만들기**

Azure Portal에서 부하 분산 장치를 선택하고 추가를 클릭합니다. 부하 분산 장치 만들기 패널에 다음 정보를 입력합니다.

1. **이름**: 부하 분산 장치의 이름
2. **구성표**: 공용 – 이 옵션에서는 이 부하 분산 장치에 공용 주소가 필요하다는 것을 Azure에 알립니다.
3. **IP 주소**: 새로운 IP 주소(동적) 만들기

![인터넷 연결 부하 분산 장치](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

배포한 후에 부하 분산 장치는 부하 분산 장치 목록에 표시됩니다.

![부하 분산 장치 목록](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)

**8.2. 공용 IP에 DNS 레이블 할당**

부하 분산 장치 패널에서 새로 만든 부하 분산 장치 항목을 클릭하여 구성에 대한 패널을 가져옵니다. 다음 단계를 수행하여 공용 IP에 DNS 레이블을 구성합니다.

1. 공용 IP 주소를 클릭합니다. 공용 IP 및 해당 설정에 대한 패널이 열립니다.
2. 구성을 클릭합니다.
3. DNS 레이블을 제공합니다. 예를 들어 contosofs.westus.cloudapp.azure.com과 같이 어디에서나 액세스할 수 있는 공용 DNS 레이블이 됩니다. 외부 부하 분산 장치(contosofs.westus.cloudapp.azure.com)의 DNS 레이블로 확인된 페더레이션 서비스(예: fs.contoso.com)에 대한 외부 DNS에 항목을 추가할 수 있습니다.

![인터넷 연결 부하 분산 장치 구성](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![인터넷 연결 부하 분산 장치 구성(DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. 인터넷 연결 (공용) 부하 분산 장치에 대한 백 엔드 풀 구성** 

내부 부하 분산 장치 만들기와 동일한 단계를 따라 WAP 서버에 대한 가용성 집합으로 인터넷 연결 (공용) 부하 분산 장치에 대한 백 엔드 풀을 구성합니다. 예를 들어 contosowapset입니다.

![인터넷 연결 부하 분산 장치의 백 엔드 풀 구성](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)

**8.4. 프로브 구성**

내부 부하 분산 장치를 구성하는 동일한 단계를 따라 WAP 서버의 백 엔드 풀에 대한 프로브를 구성합니다.

![인터넷 연결 부하 분산 장치의 프로브 구성](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)

**8.5. 부하 분산 규칙 만들기**

ILB와 같은 단계를 수행하여 TCP 443에 대한 부하 분산 규칙을 구성합니다.

![인터넷 연결 부하 분산 장치의 분산 규칙 구성](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)

### <a name="9-securing-the-network"></a>9. 네트워크 보안
**9.1. 내부 서브넷 보안**

전반적으로 내부 서브넷을 효율적으로 보호하기 위해 다음 규칙이 필요합니다(아래에 나열된 순서로).

| 규칙 | 설명 | 흐름 |
|:--- |:--- |:---:|
| AllowHTTPSFromDMZ |DMZ에서 HTTPS 통신을 허용합니다. |인바운드 |
| DenyInternetOutbound |인터넷에 액세스할 수 없습니다. |아웃바운드 |

![INT 액세스 규칙(인바운드)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

<!--
[comment]: <> (![INT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png))
[comment]: <> (![INT access rules (outbound)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
-->

**9.2. DMZ 서브넷 보안**

| 규칙 | 설명 | 흐름 |
|:--- |:--- |:---:|
| AllowHTTPSFromInternet |인터넷에서 DMZ까지 HTTPS를 허용합니다. |인바운드 |
| DenyInternetOutbound |인터넷에 대한 HTTPS를 제외한 항목을 차단합니다. |아웃바운드 |

![EXT 액세스 규칙(인바운드)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

<!--
[comment]: <> (![EXT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png))
[comment]: <> (![EXT access rules (outbound)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))
-->

> [!NOTE]
> 클라이언트 사용자 인증서 인증(X509 사용자 인증서를 사용하는 clientTLS 인증)이 필요한 경우 AD FS는 TCP 포트 49443를 인바운드 액세스에 사용하도록 설정해야 합니다.
> 
> 

### <a name="10-test-the-ad-fs-sign-in"></a>10. AD FS 로그인 테스트
가장 쉬운 방법은 IdpInitiatedSignon.aspx 페이지를 사용하여 AD FS를 테스트하는 것입니다. 이렇게 하려면 AD FS 속성에 IdpInitiatedSignOn을 사용해야 합니다. AD FS 설치를 확인하려면 다음 단계를 수행합니다.

1. 아래 AD FS 서버의 cmdlet을 실행하고 PowerShell을 사용하여 사용하도록 설정합니다.
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true 
2. 아무 외부 컴퓨터에서 https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx에 액세스  
3. 아래와 같은 AD FS 페이지를 참조해야 합니다.

![테스트 로그인 페이지](./media/active-directory-aadconnect-azure-adfs/test1.png)

성공적으로 로그인하면 아래와 같은 성공 메시지가 표시됩니다.

![테스트 성공](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Azure에서 AD FS를 배포하는 템플릿
템플릿은 각 도메인 컨트롤러, AD FS 및 WAP 당 2개, 총 6개의 컴퓨터 설정을 배포합니다.

[Azure 배포 템플릿에서 AD FS](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

이 템플릿을 배포하는 동안 기존 가상 네트워크를 사용하거나 새 VNET을 만들 수 있습니다. 배포를 사용자 지정하는 데 사용할 수 있는 다양한 매개 변수는 배포 프로세스의 매개 변수 사용에 대한 설명과 함께 아래에 나열됩니다. 

| 매개 변수 | 설명 |
|:--- |:--- |
| 위치 |리소스를 배포하는 영역입니다(예:미국 동부). |
| StorageAccountType |만든 Storage 계정 유형 |
| VirtualNetworkUsage |새 가상 네트워크를 만들지 아니면 기존 가상 네트워크를 사용할지를 나타냅니다. |
| VirtualNetworkName |기존 또는 새 가상 네트워크 사용에서 필수인 만들 Virtual Network의 이름입니다. |
| VirtualNetworkResourceGroupName |기존 가상 네트워크가 있는 리소스 그룹의 이름을 지정합니다. 기존 가상 네트워크를 사용하는 경우 필수 매개 변수가 되므로 배포 시 기존 가상 네트워크 ID를 찾을 수 있습니다. |
| VirtualNetworkAddressRange |새 가상 네트워크를 만드는 경우 필수인 새 VNET 주소 범위입니다. |
| InternalSubnetName |모든 가상 네트워크(신규 또는 기존) 사용 옵션에서 필수인 내부 서브넷의 이름입니다. |
| InternalSubnetAddressRange |새 가상 네트워크를 만드는 경우 필수인 도메인 컨트롤러 및 ADFS 서버를 포함하는 내부 서브넷의 주소 범위입니다. |
| DMZSubnetAddressRange |새 가상 네트워크를 만드는 경우 필수인 Windows 응용 프로그램 프록시 서버를 포함하는 DMZ 서브넷의 주소 범위입니다. |
| DMZSubnetName |모든 가상 네트워크(신규 또는 기존) 사용 옵션에서 필수인 내부 서브넷의 이름입니다. |
| ADDC01NICIPAddress |첫 번째 도메인 컨트롤러의 내부 IP 주소인 이 IP 주소는 DC에 정적으로 할당되고 내부 서브넷 내에서 유효한 IP 주소여야 합니다. |
| ADDC02NICIPAddress |두 번째 도메인 컨트롤러의 내부 IP 주소인 이 IP 주소는 DC에 정적으로 할당되고 내부 서브넷 내에서 유효한 IP 주소여야 합니다. |
| ADFS01NICIPAddress |첫 번째 ADFS 서버의 내부 IP 주소인 이 IP 주소는 ADFS 서버에 정적으로 할당되고 내부 서브넷 내에서 유효한 IP 주소여야 합니다. |
| ADFS02NICIPAddress |두 번째 ADFS 서버의 내부 IP 주소인 이 IP 주소는 ADFS 서버에 정적으로 할당되고 내부 서브넷 내에서 유효한 IP 주소여야 합니다. |
| WAP01NICIPAddress |첫 번째 WAP 서버의 내부 IP 주소인 이 IP 주소는 WAP 서버에 정적으로 할당되고 DMZ 서브넷 내에서 유효한 IP 주소여야 합니다. |
| WAP02NICIPAddress |두 번째 WAP 서버의 내부 IP 주소인 이 IP 주소는 WAP 서버에 정적으로 할당되고 DMZ 서브넷 내에서 유효한 IP 주소여야 합니다. |
| ADFSLoadBalancerPrivateIPAddress |ADFS 부하 분산 장치의 내부 IP 주소인 이 IP 주소는 부하 분산 장치에 정적으로 할당되고 내부 서브넷 내에서 유효한 IP 주소여야 합니다. |
| ADDCVMNamePrefix |도메인 컨트롤러의 Virtual Machine 이름 접두사입니다. |
| ADFSVMNamePrefix |ADFS 서버의 Virtual Machine 이름 접두사입니다. |
| WAPVMNamePrefix |WAP 서버의 Virtual Machine 이름 접두사입니다. |
| ADDCVMSize |도메인 컨트롤러의 VM 크기입니다. |
| ADFSVMSize |ADFS 서버의 VM 크기입니다. |
| WAPVMSize |WAP 서버의 VM 크기입니다. |
| AdminUserName |가상 머신의 로컬 관리자의 이름입니다. |
| AdminPassword |가상 머신의 로컬 관리자 계정의 암호입니다. |

## <a name="additional-resources"></a>추가 리소스
* [가용성 집합](https://aka.ms/Azure/Availability) 
* [Azure 부하 분산 장치](https://aka.ms/Azure/ILB)
* [내부 부하 분산 장치](https://aka.ms/Azure/ILB/Internal)
* [인터넷 연결 부하 분산 장치](https://aka.ms/Azure/ILB/Internet)
* [Storage 계정](https://aka.ms/Azure/Storage)
* [Azure Virtual Network](https://aka.ms/Azure/VNet)
* [AD FS 및 웹 응용 프로그램 프록시 링크](https://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
* [Azure AD Connect를 사용하여 AD FS 구성 및 관리](active-directory-aadconnectfed-whatis.md)
* [Azure Traffic Manager를 사용하여 Azure에서 고가용성 교차 지리적 AD FS 배포](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

