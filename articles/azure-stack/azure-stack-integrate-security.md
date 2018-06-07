---
title: "Azure 스택 데이터 센터 통합-보안"
description: "Azure 스택 보안 데이터 센터의 보안을 통합 하는 방법에 알아봅니다"
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 8ce9045a3e4fd12d61e9b1600ee98880762bc544
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-datacenter-integration---security"></a>Azure 스택 데이터 센터 통합-보안
Azure 스택 설계 및 보안 염두에서에 두고 작성 되었습니다. Azure 스택 잠금 시스템 이므로 소프트웨어 보안 에이전트 설치가 지원 되지 않습니다.

이 문서를 사용 하면 Azure 스택 보안 기능 데이터 센터에 이미 배포 된 보안 솔루션을 통합할 수 있습니다.

## <a name="security-logs"></a>보안 로그

Azure 스택 2 분 마다 운영 체제 및 역할과 배율 단위 노드를 인프라에 대 한 보안 이벤트 수집합니다. 로그는 저장소 계정 blob 컨테이너에 저장 됩니다.

인프라 역할당 하나의 저장소 계정 및 모든 일반적인 운영 체제 이벤트에 대 한 일반 저장소 계정을 하나 있습니다.

Blob 컨테이너에 대 한 URL를 검색할 REST 프로토콜을 통해 상태 리소스 공급자를 호출할 수 있습니다. 제 3 자 보안 솔루션 API 및 저장소 계정을 사용 처리에 대 한 이벤트를 검색할 수 있습니다.

### <a name="use-azure-storage-explorer-to-view-events"></a>Azure 저장소 탐색기를 사용 하 여 이벤트를 보려면

Azure 저장소 탐색기 라는 도구를 사용 하 여 Azure 스택에 의해 수집 된 이벤트를 검색할 수 있습니다. Azure 저장소 탐색기에서 다운로드할 수 있습니다 [http://storageexplorer.com](http://storageexplorer.com)합니다.

다음 절차는 Azure 스택에 대 한 Azure 저장소 탐색기를 구성 하는 데 예:

1. 운영자 Azure 스택 관리자 포털에 로그인 합니다.
2. 찾아보기 **저장소 계정은** 를 찾아서 **frphealthaccount**합니다. **frphealthaccount** 계정은 모든 운영 체제 이벤트를 저장 하는 데 일반적인 저장소 계정입니다.

   ![Storage 계정](media/azure-stack-integrate-security/storage-accounts.png)

3. 선택 **frphealthaccount**, 클릭 **선택 키**합니다.

   ![액세스 키](media/azure-stack-integrate-security/access-keys.png)

4. 선택 키를 클립보드에 복사 합니다.
5. Azure Storage 탐색기를 엽니다.
6. 에 **편집** 메뉴 선택 **대상 Azure 스택**합니다.
7. 선택 **계정 추가**를 선택한 후 **저장소 계정 이름과 키를 사용 하 여**합니다.

   ![저장소에 연결](media/azure-stack-integrate-security/connect-storage.png)

8. **다음**을 클릭합니다.
9. 에 **외부 저장소 연결** 페이지:

   a. 계정 이름을 입력 **frphealthaccount**합니다.

   나. 저장소 계정 액세스 키를 붙여 넣습니다.

   다. 아래 **저장소 끝점 도메인**선택, **다른**, 저장소 끝점을 지정 하 고 **[Region]. [ DomainName]**합니다.

   d. 선택 된 **HTTP 사용** 확인란 합니다.

   ![외부 저장소에 연결](media/azure-stack-integrate-security/attach-storage.png)

10. 클릭 **다음**, 요약 정보를 검토 하 고 **마침** 마법사.
11. 이제 개별 blob 컨테이너 찾아보기 및 이벤트를 다운로드할 수 있습니다.

   ![Blob 검색](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>프로그래밍 언어 액세스 이벤트를 사용 하 여

저장소 계정에 액세스 하려면 다양 한 프로그래밍 언어를 사용할 수 있습니다. 다음 설명서를 사용 하 여 사용자의 언어와 일치 하는 예를 선택:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>장치 액세스 감사

Azure 스택의 모든 물리적 장치가 TACACS 또는 RADIUS의 사용을 지원 합니다. 베이스 보드 관리 컨트롤러 (BMC) 및 네트워크 스위치에 대 한 액세스를 포함 합니다.

Azure 스택 솔루션 RADIUS 또는 기본 제공 TACACS과 함께 제공 되지 않습니다. 그러나 솔루션 검증 된 시장에서 사용할 수 있는 RADIUS 또는 TACACS 솔루션 기존의 사용을 지원 하도록 합니다.

RADIUS를 MSCHAPv2 유효성이 검사 되었습니다. RADIUS를 사용 하 여 가장 안전한 구현을 나타냅니다.
Azure 스택 솔루션에 포함 된 장치에서 TACAS 이나 RADIUS를 사용 하도록 설정 하려면 OEM 하드웨어 공급 업체에 문의 하십시오.

## <a name="syslog"></a>syslog

Azure 스택의 모든 물리적 장치가 Syslog 메시지를 보낼 수 있습니다. Azure 스택 솔루션 Syslog 서버와 함께 제공 되지 않습니다. 그러나 솔루션 검증 된 시장에서 제공 되는 기존 Syslog 솔루션에 메시지 보내기 지원 하도록 합니다.

Syslog 대상 주소는 배포의 경우 수집 되는 선택적 매개 변수 되었지만 배포 후 추가 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[서비스 정책](azure-stack-servicing-policy.md)
