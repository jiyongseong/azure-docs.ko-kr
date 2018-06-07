---
title: '빠른 시작: Azure Virtual Machines에서 단일 인스턴스 SAP HANA 수동 설치 | Microsoft Docs'
description: Azure Virtual Machines에서 단일 인스턴스 SAP HANA를 수동으로 설치하기 위한 빠른 시작 가이드입니다.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 45c7e1b76f64db142fc8fdca85b1e1fa9aca6a42
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>빠른 시작: Azure VMs에서 단일 인스턴스 SAP HANA 수동 설치
## <a name="introduction"></a>소개
이 가이드는 SAP NetWeaver 7.5 및 SAP HANA 1.0 SP12를 수동으로 설치할 때 Azure VM(가상 머신)에서 단일 인스턴스 SAP HANA를 설정하는 데 유용합니다. 따라서 SAP HANA를 Azure에 배포하는 데 중점을 두고 있으며, SAP 설명서를 대체하지는 않습니다. 

>[!Note]
>이 가이드에서는 Azure VM에 SAP HANA를 배포하는 방법에 대해 설명합니다. HANA 큰 인스턴스에 SAP HANA를 배포하는 방법에 대한 자세한 내용은 [Azure VM(가상 머신)에서 SAP 사용](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)을 참조하세요.
 
## <a name="prerequisites"></a>필수 조건
여기서는 다음과 같은 IaaS(Infrastructure as a Service) 기본 사항에 대해 잘 알고 있다고 가정합니다.
 * Azure Portal 또는 PowerShell을 통해 가상 머신 또는 가상 네트워크를 배포하는 방법
 * JSON(JavaScript Object Notification) 템플릿을 사용할 수 있는 옵션을 포함한 Azure 플랫폼 간 CLI(명령줄 인터페이스)

또한 다음에 대해서도 잘 알고 있다고 가정합니다.
* SAP HANA, SAP NetWeaver 및 온-프레미스에 설치하는 방법
* Azure에서 SAP HANA 및 SAP 응용 프로그램 인스턴스 설치 및 조작
* 다음 개념 및 절차:
   * Azure에서의 SAP 배포 계획(Azure Virtual Network 계획 및 Azure Storage 사용 포함) - [Azure VMs(Virtual Machines)에서 SAP NetWeaver - 계획 및 구현 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 참조
   * Azure에 VM을 배포하는 배포 원칙 및 방법 - [SAP용 Azure Virtual Machines 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide) 참조
   * Azure의 SAP NetWeaver ASCS(ABAP SAP Central Services), SCS(SAP Central Services) 및 ERS(입고 기준 자동 정산)에 대한 고가용성 - [Azure VM에서 SAP NetWeaver에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide) 참조
   * Azure에서 ASCS/SCS의 다중 SID 설치를 활용하여 효율성을 향상시키는 방법에 대한 세부 정보 - [SAP NetWeaver 다중 SID 구성 만들기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid) 참조 
   * Azure에서 Linux 기반 VM에 기반한 SAP NetWeaver 실행 원칙 - [Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 실행](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart) 참조. 이 가이드에서는 Azure VM의 Linux에 대한 특정 설정과 Linux VM에 Azure 저장소 디스크를 Linux VM에 올바르게 연결하는 방법에 대해 자세히 설명합니다.

현재 Azure VM은 SAP HANA 강화 구성에 대해서만 SAP에서 인증되었습니다. SAP HANA 워크로드를 포함한 스케일 아웃 구성은 아직 지원되지 않습니다. 강화 구성 시의 SAP HANA 고가용성은 [Azure VMs(Virtual Machines)의 SAP HANA 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)을 참조하세요.

SAP HANA 인스턴스 또는 S/4HANA 또는 BW/4HANA 시스템을 매우 빠른 시간 내에 배포하려는 경우 [SAP 클라우드 어플라이언스 라이브러리](http://cal.sap.com)를 사용하는 것이 좋습니다. 예를 들어 Azure에서 SAP CAL을 통해 S/4HANA 시스템을 배포하는 방법에 대한 설명서는 [이 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)에서 찾을 수 있습니다. SAP 클라우드 어플라이언스 라이브러리에 등록할 수 있는 Azure 구독 및 SAP 사용자만 있으면 됩니다.

## <a name="additional-resources"></a>추가 리소스
### <a name="sap-hana-backup"></a>SAP HANA 백업
Azure VM에서 SAP HANA 데이터베이스를 백업하는 방법에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Virtual Machines의 SAP HANA 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [파일 수준의 SAP HANA Azure Backup](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [저장소 스냅숏에 기반한 SAP HANA 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>SAP 클라우드 어플라이언스 라이브러리
SAP 클라우드 어플라이언스 라이브러리를 사용하여 S/4HANA 또는 BW/4HANA를 배포하는 방법에 대한 내용은 [Microsoft Azure에서 SAP S/4HANA 또는 BW/4HANA 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)를 참조하세요.

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA 지원 운영 체제
SAP HANA 지원 운영 체제에 대한 내용은 [SAP Support Note #2235581 - SAP HANA: 지원되는 운영 체제](https://launchpad.support.sap.com/#/notes/2235581/E)를 참조하세요. Azure VM은 이러한 운영 체제의 하위 집합만 지원합니다. Azure에서 SAP HANA를 배포하는 데 지원되는 운영 체제는 다음과 같습니다. 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

SAP HANA 및 다른 Linux 운영 체제에 대한 SAP 추가 설명서는 다음을 참조하세요.

* [SAP Support Note #171356 – SAP Software on Linux: 일반 정보](https://launchpad.support.sap.com/#/notes/1984787)(영문)
* [SAP Support Note #1944799 – SLES 운영 체제 설치를 위한 SAP HANA 지침](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)(영문)
* [SAP Support Note #2205917 – SAP HANA DB: SLES 12 for SAP Applications를 위한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2205917/E)(영문)
* [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12: 설치 참고](https://launchpad.support.sap.com/#/notes/1984787)(영문)
* [SAP Support Note #1391070 – Linux UUID 솔루션](https://launchpad.support.sap.com/#/notes/1391070)(영문)
* [SAP Support Note #2009879 – RHEL(Red Hat Enterprise Linux) 운영 체제에 대한 SAP HANA 지침](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>Azure에서 SAP 모니터링
Azure에서 SAP를 모니터링하는 방법에 대한 내용은 다음을 참조하세요.

* [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)(영문) - Azure에서 Linux VM을 사용한 SAP "고급 모니터링"에 대해 설명합니다. 
* [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)(영문) - SAPOSCOL on Linux에 대해 설명합니다. 
* [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)(영문) - Microsoft Azure의 SAP용 주요 모니터링 메트릭에 대해 설명합니다.

### <a name="azure-vm-types"></a>Azure VM 유형
SAP HANA와 함께 사용되는 Azure VM 유형 및 SAP 지원 워크로드 시나리오는 [SAP 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)(영문)에서 설명하고 있습니다. 

SAP NetWeaver 또는 S/4HANA 응용 프로그램 계층에 대해 SAP에서 인증한 Azure VM 유형은 [SAP Note 1928533 - SAP Applications on Azure: 지원 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533/E)(영문)에서 설명하고 있습니다.

>[!Note]
>SAP-Linux-Azure 통합은 Azure Resource Manager에서만 지원하며, 클래식 배포 모델에서는 지원하지 않습니다. 

## <a name="manual-installation-of-sap-hana"></a>SAP HANA 수동 설치
이 가이드에서는 다음 두 가지 방법으로 Azure VM에 SAP HANA를 수동으로 설치하는 방법에 대해 설명합니다.

* "데이터베이스 인스턴스 설치" 단계에서 분산형 NetWeaver 설치의 일부로 SWPM(SAP Software Provisioning Manager) 사용
* SAP HANA 데이터베이스 수명 주기 관리자 도구인 HDBLCM 사용 후 NetWeaver 설치

또한 이 [SAP HANA 블로그 알림](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)에서 설명한 대로 SWPM을 사용하여 단일 VM에 모든 구성 요소(SAP HANA, SAP 응용 프로그램 서버 및 ASCS 인스턴스)를 설치할 수도 있습니다. 이 빠른 시작 가이드에서는 이 옵션을 설명하지 않지만 고려해야 하는 문제는 동일합니다.

설치를 시작하기 전에 이 안내서 뒷부분에 있는 "SAP HANA 수동 설치를 위한 Azure VM 준비" 섹션을 참조하는 것이 좋습니다. 이렇게 하면 기본 Azure VM 구성만 사용할 때 발생할 수 있는 몇 가지 기본적인 실수를 방지할 수 있습니다.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>SAP SWPM 사용 시 SAP HANA 설치를 위한 주요 단계
이 섹션에서는 SAP SWPM을 사용하여 분산형 SAP NetWeaver 7.5를 설치할 때의 단일 인스턴스 SAP HANA 수동 설치의 주요 단계를 나열합니다. 개별 단계는 이 가이드의 뒷부분에 나오는 스크린샷에서 자세히 설명합니다.

1. 2개 테스트 VM이 포함된 Azure 가상 네트워크를 만듭니다.
2. Azure Resource Manager 모델에 따라 운영 체제(이 예제에서는 SLES(SUSE Linux Enterprise Server) 및 SLES for SAP Applications 12 SP1)가 포함된 2개의 Azure VM을 배포합니다.
3. 응용 프로그램 서버 VM에 2개의 Azure 표준 또는 프리미엄 저장소 디스크(예: 75GB 및 500GB 디스크)를 연결합니다.
4. HANA DB 서버 VM에 프리미엄 저장소 디스크를 연결합니다. 자세한 내용은 이 설명서의 뒷부분에 나오는 "디스크 설정" 섹션을 참조하세요.
5. 크기 또는 처리량 요구 사항에 따라 여러 디스크를 연결한 다음 VM 내부의 OS 수준에서 논리 볼륨 관리 또는 MDADM(다중 장치 관리 도구)을 사용하여 스트라이프 볼륨을 만듭니다.
6. 연결된 디스크 또는 논리 볼륨에 XFS 파일 시스템을 만듭니다.
7. 새 XFS 파일 시스템을 OS 수준에서 탑재합니다. 모든 SAP 소프트웨어에 대해 하나의 파일 시스템을 사용합니다. 다른 소프트웨어(예: /sapmnt 디렉터리 및 백업)에는 다른 파일 시스템을 사용합니다. SAP HANA DB 서버에서 XFS 파일 시스템을 프리미엄 저장소 디스크(예: /hana 및 /usr/sap)에 탑재합니다. 이 프로세스는 Linux Azure VM에서 크지 않은 루트 파일 시스템이 가득 차지 않도록 방지하기 위해 필요합니다.
8. /etc/hosts 파일에 테스트 VM의 로컬 IP 주소를 입력합니다.
9. /etc/fstab 파일에 **nofail** 매개 변수를 입력합니다.
10. 사용 중인 Linux OS 릴리스에 따라 Linux 커널 매개 변수를 설정합니다. 자세한 내용은 HANA에 대해 설명하는 해당 SAP Note 및 이 가이드의 "커널 매개 변수" 섹션을 참조하세요.
11. 교환 공간 추가
12. 선택적으로 테스트 VM에 그래픽 데스크톱 설치, 그렇지 않은 경우 원격 SAPinst 설치 사용
13. SAP Service Marketplace에서 SAP 소프트웨어를 다운로드합니다.
14. 앱 서버 VM에 SAP ASCS 인스턴스 설치
15. NFS를 사용하여 테스트 VM 간에 /sapmnt 디렉터리 공유 - 응용 프로그램 서버 VM은 NFS 서버입니다.
16. DB 서버 VM에서 SWPM을 사용하여 HANA를 포함한 데이터베이스 인스턴스 설치
17. 응용 프로그램 서버 VM에 PAS(기본 응용 프로그램 서버)를 설치합니다.
18. SAP MC(SAP 관리 콘솔)를 시작합니다. 예를 들어 SAP GUI 또는 HANA Studio와 연결합니다.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>HDBLCM 사용 시 SAP HANA 설치를 위한 주요 단계
이 섹션에서는 SAP HDBLCM을 사용하여 분산형 SAP NetWeaver 7.5를 설치할 때의 단일 인스턴스 SAP HANA 수동 설치의 주요 단계를 나열합니다. 개별 단계는 이 가이드의 스크린샷에서 자세히 설명합니다.

1. 2개 테스트 VM이 포함된 Azure 가상 네트워크를 만듭니다.
2. Azure Resource Manager 모델에 따라 운영 체제(이 예제에서는 SLES 및 SLES for SAP Applications 12 SP1)가 포함된 2개의 Azure VM을 배포합니다.
3. 앱 서버 VM에 2개의 Azure 표준 또는 프리미엄 저장소 디스크(예: 75GB 및 500GB 디스크)를 연결합니다.
4. HANA DB 서버 VM에 프리미엄 저장소 디스크를 연결합니다. 자세한 내용은 이 설명서의 뒷부분에 나오는 "디스크 설정" 섹션을 참조하세요.
5. 크기 또는 처리량 요구 사항에 따라 여러 디스크를 연결하고 VM 내부의 OS 수준에서 LVM(논리 볼륨 관리) 또는 MDADM(다중 장치 관리 도구)을 사용하여 스트라이프 볼륨을 만듭니다.
6. 연결된 디스크 또는 논리 볼륨에 XFS 파일 시스템을 만듭니다.
7. 새 XFS 파일 시스템을 OS 수준에서 탑재합니다. 모든 SAP 소프트웨어에 대해 하나의 파일 시스템을 사용하고, 다른 소프트웨어(예: /sapmnt 디렉터리 및 백업)에는 다른 파일 시스템을 사용합니다. SAP HANA DB 서버에서 XFS 파일 시스템을 프리미엄 저장소 디스크(예: /hana 및 /usr/sap)에 탑재합니다. 이 프로세스는 Linux Azure VM에서 크지 않은 루트 파일 시스템이 가득 차지 않도록 방지하기 위해 필요합니다.
8. /etc/hosts 파일에 테스트 VM의 로컬 IP 주소를 입력합니다.
9. /etc/fstab 파일에 **nofail** 매개 변수를 입력합니다.
10. 사용 중인 Linux OS 릴리스에 따라 커널 매개 변수를 설정합니다. 자세한 내용은 HANA에 대해 설명하는 해당 SAP Note 및 이 가이드의 "커널 매개 변수" 섹션을 참조하세요.
11. 교환 공간 추가
12. 선택적으로 테스트 VM에 그래픽 데스크톱 설치, 그렇지 않은 경우 원격 SAPinst 설치 사용
13. SAP Service Marketplace에서 SAP 소프트웨어를 다운로드합니다.
14. HANA DB 서버 VM에 그룹 ID가 1001인 sapsys 그룹을 만듭니다.
15. HDBLCM(HANA 데이터베이스 수명 주기 관리자)을 사용하여 DB 서버 VM에 SAP HANA를 설치합니다.
16. 앱 서버 VM에 SAP ASCS 인스턴스 설치
17. NFS를 사용하여 테스트 VM 간에 /sapmnt 디렉터리 공유 - 응용 프로그램 서버 VM은 NFS 서버입니다.
18. HANA DB 서버 VM에 SWPM을 통해 HANA를 포함한 데이터베이스 인스턴스 설치
19. 응용 프로그램 서버 VM에 PAS(기본 응용 프로그램 서버)를 설치합니다.
20. SAP MC를 시작합니다. SAP GUI 또는 HANA Studio를 통해 연결합니다.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>SAP HANA 수동 설치를 위한 Azure VM 준비
이 섹션에서는 다음 항목을 다룹니다.

* OS 업데이트
* 디스크 설정
* 커널 매개 변수
* 파일 시스템
* /etc/hosts 파일
* /etc/fstab 파일

### <a name="os-updates"></a>OS 업데이트
추가 소프트웨어를 설치하기 전에 먼저 Linux OS 업데이트 및 픽스가 있는지 확인합니다. 패치를 설치하면 지원 센터에 문의할 필요가 없습니다.

다음을 사용하고 있는지 확인합니다.
* SUSE Linux Enterprise Server for SAP Applications
* Red Hat Enterprise Linux for SAP Applications 또는 Red Hat Enterprise Linux for SAP HANA 

아직 등록하지 않은 경우 Linux 공급업체의 Linux 구독으로 OS 배포를 등록합니다. SUSE에는 이미 서비스를 포함하고 있고 자동으로 등록되는 SAP 응용 프로그램용 OS 이미지가 있습니다.

다음은 **zypper** 명령을 사용하여 SUSE Linux에 사용할 수 있는 패치를 확인하는 예제입니다.

 `sudo zypper list-patches`

문제의 종류에 따라 패치는 범주 및 심각도별로 분류됩니다. 범주에 일반적으로 사용되는 값은 **security**, **recommended**, **optional**, **feature**, **document** 또는 **yast**입니다.
심각도에 일반적으로 사용되는 값은 **critical**, **important**, **moderate**, **low** 또는 **unspecified**입니다.

**zypper** 명령은 설치된 패키지에 필요한 업데이트만 찾습니다. 예를 들어 다음 명령은 사용할 수 있습니다.

`sudo zypper patch  --category=security,recommended --severity=critical,important`

실제로 시스템을 업데이트하지 않고 `--dry-run` 매개 변수를 추가하여 해당 업데이트를 테스트할 수 있습니다.


### <a name="disk-setup"></a>디스크 설정
Azure에서 Linux VM의 루트 파일 시스템의 크기는 제한됩니다. 따라서 SAP를 실행하기 위한 추가 디스크 공간을 Azure VM에 연결해야 합니다. SAP 응용 프로그램 서버 Azure VM의 경우 Azure 표준 저장소 디스크로도 충분할 수 있습니다. 그러나 SAP HANA DBMS Azure VM의 경우 프로덕션 및 비 프로덕션 구현에 대해 Azure Premium Storage 디스크를 반드시 사용해야 합니다.

[SAP HANA TDI 저장소 요구 사항](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)(영문)에 따라 다음과 같은 Azure Premium Storage 저장소 구성이 제안됩니다. 

| VM SKU | RAM |  /hana/data 및 /hana/log <br /> (LVM 또는 MDADM으로 스트라이프됨) | /hana/shared | /root 볼륨 | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

제안된 디스크 구성에서 HANA 데이터 볼륨 및 로그 볼륨은 LVM 또는 MDADM으로 스트라이프된 Azure Premium Storage 디스크의 동일한 집합에 배치됩니다. Azure Premium Storage는 중복성을 위해 세 개의 디스크 이미지를 유지하므로 RAID 중복성 수준을 정의할 필요가 없습니다. 충분한 저장소를 구성하려면 [SAP HANA TDI 저장소 요구 사항](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)(영문) 및 [SAP HANA 서버 설치 및 업데이트 가이드](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)(영문)를 참조하세요. 또한 [VM의 고성능 Premium Storage 및 관리 디스크](https://docs.microsoft.com/azure/storage/storage-premium-storage)에서 설명한 대로 다른 Azure Premium Storage 디스크의 다른 VHD(가상 하드 디스크) 처리량도 고려해 보세요. 

HANA DBMS VM에 추가 Premium Storage 디스크를 추가하여 데이터베이스 또는 트랜잭션 로그 백업을 저장할 수 있습니다.

스트라이핑 구성에 사용되는 두 가지 주요 도구에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Linux에서 소프트웨어 RAID 구성](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure에서 Linux VM에 LVM 구성](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Linux를 게스트 OS로 실행하는 Azure VM에 디스크를 연결하는 방법에 대한 자세한 내용은 [Linux VM에 디스크 추가](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

Azure Premium Storage에서는 디스크 캐싱 모드를 정의할 수 있습니다. /hana/data 및 /hana/log를 보유하는 스트라이프 집합의 경우 디스크 캐싱을 비활성화해야 합니다. 다른 볼륨(디스크)의 경우 캐싱 모드를 **ReadOnly**로 설정해야 합니다.

자세한 내용은 [Premium Storage: Azure Virtual Machine 워크로드를 위한 고성능 저장소](../../windows/premium-storage.md)를 참조하세요.

VM 만들기를 위한 샘플 JSON 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates)으로 이동합니다.
vm-simple-sles 템플릿은 기본 템플릿입니다. 여기에는 100GB 추가 데이터 디스크가 포함된 저장소 섹션이 있습니다. 이 템플릿을 기반으로 사용할 수 있습니다. 특정 구성에 맞게 템플릿을 조정할 수 있습니다.

>[!Note]
>[Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 실행](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 설명한 대로 UUID를 사용하여 Azure 저장소 디스크를 연결하는 것이 중요합니다.

테스트 환경에서 다음 스크린샷과 같이 2개 Azure 표준 저장소 디스크가 SAP 앱 서버 VM에 연결되었습니다. 한 디스크에서는 설치를 위한 모든 SAP 소프트웨어(NetWeaver 7.5, SAP GUI 및 SAP HANA 포함)를 저장했습니다. 다른 한 디스크에서는 추가 요구 사항(예: 백업 및 테스트 데이터)에 대비하여 충분한 여유 공간을 확보하고, 동일한 SAP 환경에 속한 모든 VM 간에 /sapmnt 디렉터리(즉 SAP 프로필)를 공유할 수 있도록 했습니다.

![2개 데이터 디스크 및 해당 크기를 보여 주는 SAP HANA 앱 서버 디스크 창](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>커널 매개 변수
SAP HANA에는 표준 Azure 갤러리 이미지의 일부가 아닌 특정 Linux 커널 설정이 필요하며, 수동으로 설정해야 합니다. SUSE 또는 Red Hat의 사용 여부에 따라 매개 변수가 다를 수 있습니다. 앞에서 나열한 SAP Note에서 이러한 매개 변수에 대한 정보를 제공합니다. 표시된 스크린샷에서 SUSE Linux 12 SP1이 사용되었습니다. 

SLES for SAP Applications 12 GA 및 SLES for SAP Applications 12 SP1에는 이전 **sapconf** 유틸리티를 대체하는 새로운 **tuned-adm** 도구가 있습니다. **tuned-adm**에 대한 특별한 SAP HANA 프로필을 사용할 수 있습니다. SAP HANA에 맞게 시스템을 튜닝하려면 루트 사용자로서 다음을 입력합니다.

   `tuned-adm profile sap-hana`

**tuned-adm**에 대한 자세한 내용은 [tuned-adm 관련 SUSE 설명서](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)(영문)를 참조하세요.

다음 스크린샷에서는 필요한 SAP HANA 설정에 따라 **tuned-adm**에서 `transparent_hugepage` 및 `numa_balancing` 값을 변경한 방식을 확인할 수 있습니다.

![tuned-adm 도구는 필수 SAP HANA 설정에 따라 값을 변경합니다.](./media/hana-get-started/image005.jpg)

SAP HANA 커널을 영구적으로 설정하려면 SLES 12에서 **grub2**를 사용합니다. **grub2**에 대한 자세한 내용은 SUSE 설명서의 [구성 파일 구조](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)(영문) 섹션을 참조하세요.

다음 스크린샷에서는 구성 파일에서 커널 설정을 변경한 다음 **grub2-mkconfig**를 사용하여 컴파일한 방식을 보여 줍니다.

![구성 파일에서 변경된 커널 설정 및 grub2-mkconfig를 통한 컴파일](./media/hana-get-started/image006.jpg)

또 다른 옵션은 YaST 및 **부팅 로더** > **커널 매개 변수** 설정을 사용하여 커널 설정을 변경하는 것입니다.

![YaST 부팅 로더의 커널 매개 변수 설정 탭](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>파일 시스템
다음 스크린샷에서는 2개 Azure 표준 저장소 디스크에 연결된 SAP 앱 서버 VM에 만들어진 2개 파일 시스템을 보여 줍니다. 두 파일 시스템은 모두 XFS 형식이며 /sapdata 및 /sapsoftware에 탑재됩니다.

파일 시스템을 반드시 이 방식으로 구조화하는 것은 아닙니다. 디스크 공간을 구조화하기 위한 다른 옵션이 있습니다. 가장 중요한 고려 사항은 루트 파일 시스템의 여유 공간이 부족하지 않도록 하는 것입니다.

![SAP 앱 서버 VM에 만든 2개 파일 시스템](./media/hana-get-started/image008.jpg)

SAP HANA DB VM과 관련하여 데이터베이스 설치 중에 SAPinst(SWPM) 및 **일반** 설치 옵션을 사용하면 모든 항목이 /hana 및 /usr/sap 아래에 설치됩니다. SAP HANA 로그 백업의 기본 위치는 /usr/sap 아래입니다. 즉 루트 파일 시스템의 저장소 공간이 부족하지 않도록 하는 것이 중요하기 때문에 SWPM을 사용하여 SAP HANA를 설치하기 전에 /hana 및 /usr/sap 아래에 충분한 여유 공간이 있는지 확인합니다.

SAP HANA의 표준 파일 시스템 레이아웃에 대한 설명은 [SAP HANA 서버 설치 및 업데이트 가이드](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)(영문)를 참조하세요.

![SAP 앱 서버 VM에 만든 추가 파일 시스템](./media/hana-get-started/image009.jpg)

표준 SLES/SLES for SAP Applications 12에 SAP NetWeaver를 설치하면 다음 스크린샷과 같이 스왑 공간이 없다는 메시지가 표시됩니다. 이 메시지를 해제하려면 **dd**, **mkswap** 및 **swapon**을 사용하여 스왑 파일을 수동으로 추가하면 됩니다. 자세한 내용은 SUSE 설명서의 [YaST Partitioner 사용](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)(영문) 섹션에서 "수동으로 스왑 파일 추가"를 검색해 보세요.

또 다른 옵션은 Linux VM 에이전트를 사용하여 교환 공간을 구성하는 것입니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](../../extensions/agent-linux.md)를 참조하세요.

![교환 공간이 부족하다고 알리는 팝업 메시지](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>/etc/hosts 파일
SAP 설치를 시작하기 전에 먼저 SAP VM의 호스트 이름과 IP 주소를 /etc/hosts 파일에 포함해야 합니다. 하나의 Azure 가상 네트워크 내에 모든 SAP VM을 배포한 다음 아래와 같은 내부 IP 주소를 사용합니다.

![/etc/hosts 파일에 나열된 SAP VM의 호스트 이름 및 IP 주소](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>/etc/fstab 파일

fstab 파일에 **nofail** 매개 변수를 추가하는 것이 좋습니다. 이렇게 하면 디스크에 오류가 발생하더라도 VM이 부팅 프로세스에서 중단되지 않습니다. 그러나 추가 디스크 공간을 사용할 수 없으며 프로세스가 루트 파일 시스템을 가득 채울 수 있습니다. /hana가 누락되면 SAP HANA가 시작되지 않습니다.

![fstab 파일에 nofail 매개 변수 추가](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES for SAP Applications 12의 그래픽 GNOME 데스크톱
이 섹션에서는 다음 항목을 다룹니다.

* SLES 12/SLES-for-SAP 응용 프로그램 12에 GNOME 데스크톱 및 xrdp 설치
* SLES 12/SLES for SAP Applications 12에서 Firefox를 사용하여 Java 기반 SAP MC 실행

Xterminal 또는 VNC와 같은 대안을 사용할 수도 있습니다(여기서는 설명하지 않음).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES-for-SAP 응용 프로그램 12에 GNOME 데스크톱 및 xrdp 설치
Windows 백그라운드에 있으면 SAP Linux VM 내에서 그래픽 데스크톱을 직접 사용하여, Firefox, SAPinst, SAP GUI, SAP MC 또는 HANA Studio를 실행하고, Windows 컴퓨터에서 RDP(원격 데스크톱 프로토콜)를 통해 VM에 연결할 수 있습니다 . 프로덕션 및 비프로덕션 Linux 기반 시스템에 그래픽 사용자 인터페이스를 추가하는 것과 관련된 회사 정책에 따라 서버에 GNOME을 설치하는 것이 좋습니다. Azure SLES 12/SLES for SAP Applications 12 VM에 GNOME 데스크톱을 설치하려면 다음을 수행합니다.

1. 다음 명령을 입력하여 GNOME 데스크톱을 설치합니다(예: PuTTY 창에서).

   `zypper in -t pattern gnome-basic`

2. xrdp를 설치하여 RDP를 통해 VM에 연결할 수 있도록 합니다.

   `zypper in xrdp`

3. /etc/sysconfig/windowmanager를 편집하고 기본 창 관리자를 GNOME으로 설정합니다.

   `DEFAULT_WM="gnome"`

4. **chkconfig**를 실행하여 다시 부팅한 후 xrdp가 자동으로 시작되도록 합니다.

   `chkconfig -level 3 xrdp on`

5. RDP 연결에 문제가 있으면 다시 시작합니다(예: PuTTY 창에서).

   `/etc/xrdp/xrdp.sh restart`

6. 이전 단계에서 언급한 xrdp 다시 시작이 작동하지 않으면 .pid 파일을 확인합니다.

   `check /var/run` 

   `xrdp.pid`를 찾아보세요. 찾은 다음 제거하고 다시 시작합니다.

### <a name="starting-sap-mc"></a>SAP MC 시작
GNOME 데스크톱을 설치한 후 Azure SLES 12/SLES for SAP Applications 12 VM에서 실행되는 Firefox에서 그래픽 Java 기반 SAP MC를 시작하면 누락된 Java 브라우저 플러그 인으로 인해 오류가 표시될 수 있습니다.

SAP MC를 시작할 URL은 `<server>:5<instance_number>13`입니다.

자세한 내용은 [웹 기반 SAP 관리 콘솔 시작](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)(영문)을 참조하세요.

다음 스크린샷에서는 Java 브라우저 플러그 인이 없는 경우 표시되는 오류 메시지를 보여 줍니다.

![누락된 Java 브라우저 플러그 인을 나타내는 오류 메시지](./media/hana-get-started/image013.jpg)

이 문제를 해결하는 한 가지 방법은 다음 스크린샷과 같이 YaST를 사용하여 누락된 플러그 인을 설치하는 것입니다.

![YaST를 사용하여 누락된 플러그 인 설치](./media/hana-get-started/image014.jpg)

SAP 관리 콘솔 URL을 다시 입력하면 플러그 인을 활성화하라는 메시지가 표시됩니다.

![플러그 인 활성화를 요청하는 대화 상자](./media/hana-get-started/image015.jpg)

누락된 javafx.properties 파일에 대한 오류 메시지가 표시될 수도 있습니다. 이 항목은 SAP GUI 7.4용 Oracle Java 1.8의 요구 사항과 관련이 있습니다. ([SAP Note 2059429](https://launchpad.support.sap.com/#/notes/2059424) 참조) SLES/SLES for SAP Applications 12와 함께 제공되는 IBM Java 버전 또는 openjdk 패키지에는 필요한 javafx.propertie파일이 포함되어 있지 않습니다. 따라서 Oracle에서 Java SE 8를 다운로드하고 설치하여 이 문제를 해결합니다.

openSUSE에서 openjdk와 비슷한 문제에 대한 내용은 [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306)(영문) 토론 스레드를 참조하세요.

## <a name="manual-installation-of-sap-hana-swpm"></a>SAP HANA 수동 설치: SWPM
이 섹션에서 보여 주는 일련의 스크린샷은 SWPM(SAPinst)을 사용할 때 SAP NetWeaver 7.5 및 SAP HANA SP12를 설치하기 위한 주요 단계를 보여 줍니다. NetWeaver 7.5 설치의 일부로 SWPM은 HANA 데이터베이스를 단일 인스턴스로 설치할 수도 있습니다.

샘플 테스트 환경에서는 ABAP(Advanced Business Application Programming) 앱 서버 하나만 설치했습니다. 다음 스크린샷에서는 **분산 시스템** 옵션을 사용하여 하나의 Azure VM에는 ASCS 및 기본 응용 프로그램 서버 인스턴스를, 다른 하나의 Azure VM에는 데이터베이스 시스템으로 SAP HANA를 설치했습니다.

![[분산 시스템] 옵션으로 설치된 ASCS 및 기본 응용 프로그램 서버 인스턴스](./media/hana-get-started/image012.jpg)

ASCS 인스턴스를 앱 서버 VM에 설치하고 SAP 관리 콘솔에서 "녹색"으로 설정한 후에는 SAP 프로필 디렉터리를 포함한 /sapmnt 디렉터리를 SAP HANA DB 서버 VM과 공유해야 합니다. DB 설치 단계에서 이 정보에 액세스해야 합니다. 액세스를 제공하는 가장 좋은 방법은 YaST를 사용하여 구성할 수 있는 NFS를 사용하는 것입니다.

![앱 서버 VM에 설치하되 "녹색"으로 설정된 ASCS 인스턴스를 보여 주는 SAP 관리 콘솔](./media/hana-get-started/image016.jpg)

앱 서버 VM에서 /sapmnt 디렉터리는 **rw** 및 **no_root_squash** 옵션을 사용하여 NFS를 통해 공유해야 합니다. 기본값은 **ro** 및 **root_squash**이며, 데이터베이스 인스턴스를 설치할 때 문제가 발생할 수 있습니다.

!["rw" 및 "no_root_squash" 옵션을 사용하여 NFS를 통해 /sapmnt 디렉터리 공유](./media/hana-get-started/image017b.jpg)

다음 스크린샷과 같이 **NFS 클라이언트**(및 YaST)를 사용하여 SAP HANA DB 서버 VM에 앱 서버 VM의 /sapmnt 공유를 구성해야 합니다.

![NFS 클라이언트를 사용하여 구성된 /sapmnt 공유](./media/hana-get-started/image018b.jpg)

다음 스크린샷과 같이 분산형 NetWeaver 7.5 설치(**데이터베이스 인스턴스**)를 수행하려면 SAP HANA DB 서버 VM에 로그인하고 SWPM을 시작합니다.

![SAP HANA DB 서버 VM에 로그인하고 SWPM을 시작하여 데이터베이스 인스턴스 설치](./media/hana-get-started/image019.jpg)

**일반** 설치 및 설치 미디어 경로를 선택한 다음 DB SID, 호스트 이름, 인스턴스 번호 및 DB 시스템 관리자 암호를 입력합니다.

![SAP HANA 데이터베이스 시스템 관리자 로그인 페이지](./media/hana-get-started/image035b.jpg)

DBACOCKPIT 스키마의 암호를 입력합니다.

![DBACOCKPIT 스키마의 암호 입력 상자](./media/hana-get-started/image036b.jpg)

SAPABAP1 스키마 암호에 대한 질문을 입력합니다.

![SAPABAP1 스키마 암호에 대한 질문 입력](./media/hana-get-started/image037b.jpg)

각 작업이 완료되면 DB 설치 프로세스의 각 단계 옆에 녹색 확인 표시가 나타납니다. 메시지 "데이터베이스... 인스턴스의 실행이 완료되었습니다."가 표시됩니다.

![확인 메시지가 표시된 작업 완료 창](./media/hana-get-started/image023.jpg)

성공적으로 설치되면 SAP 관리 콘솔에서 DB 인스턴스를 "녹색"으로 표시하고, SAP HANA 프로세스의 전체 목록(hdbindexserver, hdbcompileserver 등)도 표시해야 합니다.

![SAP HANA 프로세스 목록을 보여 주는 SAP 관리 콘솔 창](./media/hana-get-started/image024.jpg)

다음 스크린샷에서는 HANA 설치 중에 SWPM에서 만든 /hana/shared 디렉터리 아래의 파일 구조 일부를 보여 줍니다. 다른 경로를 지정하는 옵션이 없으므로 SWPM을 사용하여 SAP HANA를 설치하기 전에 먼저 /hana 디렉터리 아래에 추가 디스크 공간을 탑재해야 합니다. 이렇게 하면 루트 파일 시스템의 여유 공간이 부족하지 않도록 방지합니다.

![HANA 설치 중에 만든 /hana/shared 디렉터리 파일 구조](./media/hana-get-started/image025.jpg)

다음 스크린샷에서는 /usr/sap 디렉터리의 파일 구조를 보여 줍니다.

![/usr/sap 디렉터리 파일 구조](./media/hana-get-started/image026.jpg)

분산형 ABAP 설치의 마지막 단계는 기본 응용 프로그램 서버 인스턴스를 설치하는 것입니다.

![마지막 단계로 기본 응용 프로그램 서버 인스턴스를 보여 주는 ABAP 설치](./media/hana-get-started/image027b.jpg)

기본 응용 프로그램 서버 인스턴스 및 SAP GUI를 설치한 후에는 **DBA Cockpit** 트랜잭션을 사용하여 SAP HANA 설치가 올바르게 완료되었는지 확인합니다.

![성공적인 설치를 확인하는 DBA Cockpit 창](./media/hana-get-started/image028b.jpg)

마지막 단계로 HANA Studio를 SAP 앱 서버 VM에 설치한 다음 DB 서버 VM에서 실행 중인 SAP HANA 인스턴스에 연결할 수도 있습니다.

![SAP 앱 서버 VM에 SAP HANA Studio 설치](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>SAP HANA 수동 설치: HDBLCM
SWPM을 사용하여 분산 설치의 일부로 SAP HANA를 설치하는 것 외에도 먼저 HDBLCM을 사용하여 독립 실행형 HANA를 설치할 수 있습니다. 예를 들어 SAP NetWeaver 7.5를 설치할 수 있습니다. 이 섹션의 스크린샷에서는 이 프로세스가 작동하는 방식을 보여 줍니다.

HANA HDBLCM 도구에 대한 자세한 내용은 다음을 참조하세요.

* [Choosing the Correct SAP HANA HDBLCM for Your Task(작업을 위해 올바른 SAP HANA HDBLCM 선택)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools(SAP HANA 수명 주기 관리 도구)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA Server Installation and Update Guide(SAP HANA 서버 설치 및 업데이트 가이드)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

HDBLCM 도구로 만든 `\<HANA SID\>adm user`에 대한 기본 그룹 ID 설정에 문제가 발생하지 않도록 HDBLCM을 통해 SAP HANA를 설치하기 전에 `1001` 그룹 ID를 사용하여 `sapsys`라는 새 그룹을 정의합니다.

![1001 그룹 ID를 사용하여 정의한 새로운 "sapsys" 그룹](./media/hana-get-started/image030.jpg)

HDBLCM을 처음으로 시작하면 간단한 시작 메뉴가 표시됩니다. 다음 스크린샷과 같이 항목 1 **새 시스템 설치**를 선택합니다.

![HDBLCM 시작 창의 "새 시스템 설치" 옵션](./media/hana-get-started/image031.jpg)

다음 스크린샷에서는 이전에 선택한 주요 옵션을 모두 보여 줍니다.

> [!IMPORTANT]
> HANA 로그 및 데이터 볼륨뿐만 아니라 설치 경로(이 샘플의 경우 /hana/shared) 및 /usr/sap의 이름이 지정된 디렉터리는 루트 파일 시스템의 일부가 아니어야 합니다. 이러한 디렉터리는 "디스크 설정" 섹션에서 설명한 대로 VM에 연결된 Azure 데이터 디스크에 속합니다. 이 방법은 루트 파일 시스템의 공간이 부족하지 않도록 방지하는 데 유용합니다. 다음 스크린샷에서 HANA 시스템 관리자는 사용자 ID가 `1005`이고 설치하기 전에 정의한 `sapsys` 그룹(ID `1001`)에 속해 있음을 알 수 있습니다.

![이전에 선택한 주요 SAP HANA 구성 요소 전체 목록](./media/hana-get-started/image032.jpg)

/etc/passwd 디렉터리에서 `\<HANA SID\>adm user`(다음 스크린샷의 `azdadm`) 세부 정보를 확인할 수 있습니다.

![/etc/passwd 디렉터리에 나열된 HANA \<HANA SID\>adm 사용자 세부 정보](./media/hana-get-started/image033.jpg)

HDBLCM을 사용하여 SAP HANA를 설치하면 다음 스크린샷과 같이 SAP HANA Studio에서 파일 구조를 볼 수 있습니다. 모든 SAP NetWeaver 테이블을 포함하는 SAPABAP1 스키마는 아직 사용할 수 없습니다.

![SAP HANA Studio의 SAP HANA 파일 구조](./media/hana-get-started/image034.jpg)

SAP HANA를 설치한 후에는 이를 기반으로 하여 SAP NetWeaver를 설치할 수 있습니다. 다음 스크린샷과 같이 이전 섹션에서 설명한 대로 SWPM을 사용하여 "분산 설치"로 수행되었습니다. SWPM을 사용하여 데이터베이스 인스턴스를 설치하는 경우 HDBLCM을 사용하여 동일한 데이터(예: 호스트 이름, HANA SID 및 인스턴스 번호)를 입력합니다. 그러면 SWPM에서 기존 HANA 설치를 사용하고 더 많은 스키마를 추가합니다.

![SWPM을 사용하여 수행한 분산 설치](./media/hana-get-started/image035b.jpg)

다음 스크린샷에서는 DBACOCKPIT 스키마에 대한 데이터를 입력하는 SWPM 설치 단계를 보여 줍니다.

![DBACOCKPIT 스키마 데이터를 입력하는 SWPM 설치 단계](./media/hana-get-started/image036b.jpg)

SAPABAP1 스키마에 대한 데이터를 입력합니다.

![SAPABAP1 스키마에 대한 데이터 입력](./media/hana-get-started/image037b.jpg)

SWPM 데이터베이스 인스턴스 설치가 완료되면 SAP HANA Studio에서 SAPABAP1 스키마를 볼 수 있습니다.

![SAP HANA Studio의 SAPABAP1 스키마](./media/hana-get-started/image038b.jpg)

마지막으로 SAP 앱 서버 및 SAP GUI 설치가 완료되면 **DBA Cockpit** 트랜잭션을 사용하여 HANA DB 인스턴스를 확인할 수 있습니다.

![DBA Cockpit 트랜잭션으로 확인된 HANA DB 인스턴스](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP 소프트웨어 다운로드
다음 스크린샷과 같이 SAP Service Marketplace에서 소프트웨어를 다운로드할 수 있습니다.

Linux/HANA용 NetWeaver 7.5 다운로드:

 ![NetWeaver 7.5를 다운로드하기 위한 SAP 서비스 설치 및 업그레이드 창](./media/hana-get-started/image001.jpg)

HANA SP12 Platform Edition 다운로드:

 ![HANA SP12 Platform Edition을 다운로드하기 위한 SAP 서비스 설치 및 업그레이드 창](./media/hana-get-started/image002.jpg)

