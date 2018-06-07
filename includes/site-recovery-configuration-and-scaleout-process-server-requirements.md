---
title: 포함 파일
description: 포함 파일
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 05/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 2464fa41deaa1e9c43e5f53e1a900ca11b582040
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33901298"
---
**구성/프로세스 서버 요구 사항**

**구성 요소** | **요구 사항** 
--- | ---
**하드웨어** | 
**CPU 코어** | 8 
**RAM** | 16GB
**디스크 수** | 3, OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브(장애 복구용) 포함 
**사용 가능한 디스크 공간(프로세스 서버 캐시)** | 600GB
**사용 가능한 디스크 공간(보존 디스크)** | 600GB
**소프트웨어** | 
**운영 체제** | Windows Server 2012 R2 <br> Windows Server 2016
**운영 체제 로케일** | 미국 영어(en-us)
**Windows Server 역할** | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V 
**그룹 정책** | 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세히 알아보기](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
**IIS** | - 기존의 기본 웹 사이트 없음 <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/응용 프로그램 없음 <br>- [익명 인증](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) 사용 <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 설정 사용 
**네트워크** | 
**IP 주소 유형** | 공용 
**인터넷 액세스** | 서버에서 이러한 URL에 액세스해야 합니다(직접 또는 프록시를 통해). <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi(구성 서버를 설정하는 경우) <br> - time.nist.gov <br> - time.windows.com 
**포트** | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송) 
**VMWARE(VMware VM으로 구성/프로세스 서버를 설정하는 경우)**
**NIC 유형** | VMXNET3  
**VM에서 실행 중인 VMware vSphere PowerCLI* | [PowerCLI 버전 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")

**구성/프로세스 서버 크기 요구 사항**

**CPU** | **메모리** | **캐시 디스크** | **데이터 변경률** | **복제된 컴퓨터**
--- | --- | --- | --- | ---
vCPU 8대<br/><br/> 2개 소켓 * 4코어 @ 2.5GHz | 16GB | 300GB | 500GB 이하 | 컴퓨터 100대 미만
vCPU 12대<br/><br/> 2개 소켓 * 6코어 @ 2.5GHz | 18GB | 600GB | 500GB-1TB | 컴퓨터 100-150대
vCPU 16대<br/><br/> 2개 소켓 * 8코어 @ 2.5GHz | 32GB | 1TB | 1-2TB | 컴퓨터 150-200대

