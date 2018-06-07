---
title: Azure Storage 소개 - Azure의 클라우드 저장소 | Microsoft Docs
description: Azure Storage는 Microsoft의 클라우드 저장소 솔루션입니다. Azure Storage는 고가용성, 보안, 지속형, 확장성이 뛰어난 중복된 데이터 개체에 대한 저장소를 제공합니다.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: get-started-article
ms.date: 04/05/2018
ms.author: tamram
ms.openlocfilehash: 8fc94db3e44fc5388063b507cf240863fcc26a3c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="introduction-to-azure-storage"></a>Azure Storage 소개

Azure Storage는 최신 데이터 저장소 시나리오를 위한 Microsoft의 클라우드 저장소 솔루션입니다. Azure Storage는 데이터 개체에 대한 확장성 있는 개체 저장소, 클라우드에 대한 파일 시스템 서비스, 신뢰할 수 있는 메시징을 위한 메시징 저장소 및 NoSQL 저장소를 제공합니다. Azure Storage는:

- **내구성 및 고가용성** 중복성을 통해 일시적인 하드웨어 오류가 발생할 경우 데이터를 안전하게 보호합니다. 또한 로컬 재해 또는 자연 재해로부터 추가 보호를 위해 데이터 센터 또는 지리적 영역에서 데이터를 복제하도록 선택할 수도 있습니다. 이러한 방식으로 복제된 데이터는 예기치 않은 중단이 발생할 경우 항상 사용 가능한 상태로 유지됩니다. 
- **보안** Azure Storage에 기록된 모든 데이터는 서비스에 의해 암호화됩니다. Azure Storage는 데이터에 액세스할 수 있는 사용자에 대한 세분화된 제어를 제공합니다.
- **확장성** Azure Storage는 오늘날의 응용 프로그램의 데이터 저장소 및 성능 요구 사항을 충족하기 위해 확장성이 매우 뛰어나도록 설계되었습니다. 
- **관리** Microsoft Azure는 유지 관리 및 사용자에 대한 중요한 문제를 처리합니다.
- **액세스 가능** Azure Storage의 데이터는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. Microsoft는 완성도 높은 REST API 뿐만 아니라 .NET, Java, Node.js, Python, PHP, Ruby, Go 및 기타 다양한 언어로 Azure Storage용 SDK를 제공합니다. Azure Storage는 Azure PowerShell 또는 Azure CLI에서 스크립트를 지원합니다. 또한 Azure Portal 및 Azure Storage 탐색기는 데이터 작업을 위한 쉬운 시각적 솔루션을 제공합니다.  

## <a name="azure-storage-services"></a>Azure Storage 서비스

Azure Storage는 이러한 데이터 서비스를 포함합니다. 

- [Azure Blob](../blobs/storage-blobs-introduction.md): 텍스트 및 이진 데이터에 대한 확장성이 뛰어난 개체 저장소입니다.
- [Azure Files](../files/storage-files-introduction.md): 클라우드 또는 온-프레미스 배포에 대한 관리되는 파일 공유입니다.
- [Azure 큐](../queues/storage-queues-introduction.md): 응용 프로그램 구성 요소 간에 안정적인 메시징을 위한 메시징 저장소입니다. 
- [Azure 테이블](../../cosmos-db/table-storage-overview.md): 구조화된 데이터의 스키마 없는 저장소를 위한 NoSQL 저장소입니다.

각 서비스는 저장소 계정을 통해 액세스됩니다. 시작하려면 [저장소 계정 만들기](storage-quickstart-create-account.md)를 참조하세요.

## <a name="blob-storage"></a>Blob 저장소

Azure Blob 저장소는 클라우드를 위한 Microsoft의 개체 저장소 솔루션입니다. Blob 저장소는 텍스트 또는 이진 데이터와 같이 구조화되지 않은 대량의 데이터를 저장하는 데 최적화되어 있습니다. 

Blob 저장소는 다음에 이상적입니다.

* 브라우저에 이미지 또는 문서 직접 제공
* 분산 액세스용 파일 저장
* 동영상 및 오디오 스트리밍
* 백업/복원, 재해 복구 및 보관용 데이터 저장
* 온-프레미스 또는 Azure 호스티드 서비스에 의한 분석용 데이터 저장

Blob 저장소의 개체는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. 사용자 또는 클라이언트 응용 프로그램은 URL, [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) 또는 Azure Storage 클라이언트 라이브러리를 통해 Blob에 액세스할 수 있습니다. 저장소 클라이언트 라이브러리는 [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/en/latest/index.html), [PHP](http://azure.github.io/azure-storage-php/) 및 [Ruby](http://azure.github.io/azure-storage-ruby)를 비롯한 여러 언어에서 사용할 수 있습니다.

Blob 저장소에 대한 자세한 내용은 [Azure에서 개체 저장소 소개](../blobs/storage-blobs-introduction.md)를 참조하세요.

## <a name="azure-files"></a>Azure 파일
[Azure Files](../files/storage-files-introduction.md)를 사용하면 표준 SMB(서버 메시지 블록) 프로토콜을 사용하여 액세스할 수 있는 고가용성 네트워크 파일 공유를 설정할 수 있습니다. 즉, 여러 VM이 읽기 및 쓰기 권한을 모두 사용하여 동일한 파일을 공유할 수 있습니다. 또한 REST 인터페이스 또는 저장소 클라이언트 라이브러리를 사용하여 파일을 읽을 수 있습니다.

Azure Files가 회사 파일 공유의 파일과 다른 점 한 가지는 파일을 가리키고 SAS(공유 액세스 서명) 토큰을 포함하고 있는 URL을 사용하여 전 세계 어디서나 파일에 액세스할 수 있다는 것입니다. SAS 토큰은 생성 가능하며 특정 기간에 개인 자산에 대한 특정 액세스를 허용합니다.

파일 공유는 다음과 같은 여러 가지 일반적인 시나리오에 사용할 수 있습니다.

* 여러 온-프레미스 응용 프로그램에서 파일 공유를 사용합니다. 이 기능을 사용하면 데이터를 공유하는 응용 프로그램을 Azure로 보다 쉽게 마이그레이션할 수 있습니다. 파일 공유를 온-프레미스 응용 프로그램에서 사용하는 것과 동일한 드라이브 문자에 탑재하면 파일 공유에 액세스하는 응용 프로그램의 일부가 최소한의 변경 내용(있는 경우)으로 작동해야 합니다.

* 구성 파일을 파일 공유에 저장하고 여러 VM에서 액세스할 수 있습니다. 그룹의 여러 개발자가 사용하는 도구 및 유틸리티를 파일 공유에 저장할 수 있으며, 이렇게 하면 모든 사람이 찾아서 동일한 버전을 사용할 수 있습니다.

* 파일 공유에 쓰고 나중에 처리하거나 분석할 수 있는 데이터의 세 가지 예로 진단 로그, 메트릭 및 크래시 덤프를 들 수 있습니다.

현재는 Active Directory 기반 인증과 ACL(액세스 제어 목록)이 지원되지 않지만 향후 지원할 예정입니다. 파일 공유 액세스에 대한 인증을 제공하기 위해 저장소 계정 자격 증명이 사용됩니다. 즉, 공유가 탑재된 모든 사용자는 공유에 대한 전체 읽기/쓰기 액세스 권한을 갖습니다.

Azure Files에 대한 자세한 내용은 [Azure Files 소개](../files/storage-files-introduction.md)를 참조하세요.

## <a name="queue-storage"></a>큐 저장소

Azure 큐 서비스는 메시지를 저장하고 검색하는 데 사용됩니다. 큐 메시지의 크기는 최대 64KB일 수 있고 큐에는 수 많은 메시지가 포함될 수 있습니다. 큐는 일반적으로 비동기적으로 처리될 메시지의 목록을 저장하는 데 사용됩니다.

예를 들어 고객이 사진을 업로드하여 각 사진에 대한 썸네일을 만들려고 한다고 가정하겠습니다. 고객이 사진을 업로드하는 동안 썸네일을 만들 때까지 기다리게 할 수 있습니다. 대신 큐를 사용할 수 있습니다. 고객이 업로드를 완료하면 큐에 메시지를 작성합니다. 그런 다음 Azure Function에서는 큐의 메시지를 검색하고 썸네일을 만듭니다. 이 프로세스의 일부는 각각 별도로 확장될 수 있으며 사용하기 위해 조정하는 경우 더 세밀하게 조정할 수 있습니다.

Azure 큐에 대한 자세한 내용은 [큐 소개](../queues/storage-queues-introduction.md)를 참조하세요.

## <a name="table-storage"></a>테이블 저장소

Azure Table Storage는 이제 Azure Cosmos DB의 일부입니다. Azure Table Storage 설명서를 보려면 [Azure Table Storage 개요](../../cosmos-db/table-storage-overview.md)를 참조하세요. 기존 Azure Table Storage 서비스 외에도 처리량 최적화 테이블, 전역 분산 및 자동 보조 인덱스를 제공하는 새로운 Azure Cosmos DB 테이블 API가 있습니다. 새로운 프리미엄 환경에 대해 알아보고 사용해 보려면 [Azure Cosmos DB 테이블 API](https://aka.ms/premiumtables)를 확인하세요.

Table 저장소에 대한 자세한 내용은 [Azure Table 저장소 개요](../../cosmos-db/table-storage-overview.md)를 참조하세요.

## <a name="disk-storage"></a>디스크 저장소

또한 Azure Storage는 가상 머신에서 사용하는 관리되는 디스크 및 관리되지 않는 디스크 기능을 포함합니다. 이러한 기능에 대한 자세한 내용은 [Compute Service 설명서](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요.

## <a name="types-of-storage-accounts"></a>저장소 계정 유형

이 표에서는 개체에서 사용할 수 있는 다양한 종류의 저장소 계정을 보여줍니다.

|**저장소 계정의 유형**|**범용 표준**|**범용 프리미엄**|**Blob Storage, 핫 및 쿨 액세스 계층**|
|-----|-----|-----|-----|
|**지원되는 서비스**| Blob, File, Queue 서비스 | Blob Service | Blob Service|
|**지원되는 Blob 유형**|블록 Blob, 페이지 Blob 및 추가 Blob | 페이지 Blob | 블록 Blob 및 추가 Blob|

### <a name="general-purpose-storage-accounts"></a>범용 저장소 계정

범용 저장소 계정에는 두 가지가 있습니다.

#### <a name="standard-storage"></a>Standard Storage

가장 널리 사용되는 저장소 계정은 모든 유형의 데이터에 사용할 수 있는 표준 저장소 계정입니다. 표준 저장소 계정은 자기 미디어를 사용하여 데이터를 저장합니다.

#### <a name="premium-storage"></a>Premium Storage

프리미엄 저장소는 VHD 파일에 주로 사용되는 페이지 Blob에 고성능 저장소를 제공합니다. 프리미엄 저장소 계정은 SSD를 사용하여 데이터를 저장합니다. 모든 VM에 Premium Storage를 사용하는 것이 좋습니다.

### <a name="blob-storage-accounts"></a>Blob Storage 계정

Blob Storage 계정은 블록 Blob 및 추가 Blob을 저장하는 데 사용되는 특별한 저장소 계정입니다. 이러한 계정에 페이지 Blob을 저장할 수 없습니다. 따라서 VHD 파일을 저장할 수 없습니다. 이러한 계정을 사용하면 액세스 계층을 핫 또는 쿨로 설정할 수 있습니다. 계층은 언제든지 변경할 수 있습니다.

핫 액세스 계층은 자주 액세스 하는 파일에 대해 사용합니다. 저장소의 경우 비용이 많이 들지만 Blob에 액세스하는 비용이 훨씬 낮습니다. 쿨 액세스 계층에 저장된 Blob의 경우 Blob에 액세스하기 위한 비용이 많이 들지만 저장소 비용이 훨씬 낮습니다.

## <a name="accessing-your-blobs-files-and-queues"></a>Blob, 파일 및 큐에 액세스

각 저장소 계정에는 모든 작업에 사용할 수 있는 두 개의 인증 키가 있습니다. 경우에 따라 보안을 강화하기 위해 키를 롤백할 수 있도록 구 개의 키가 있습니다. 키 소유권과 계정 이름이 있으면 저장소 계정의 모든 데이터에 무제한 액세스할 수 있으므로 이러한 키를 안전하게 보관하는 것이 중요합니다.

이 섹션에서는 저장소 계정 및 해당 데이터를 보호하는 두 가지 방법을 알아봅니다. 저장소 계정 및 데이터를 보호하는 방법에 대한 자세한 내용은 [Azure Storage 보안 가이드](storage-security-guide.md)를 참조하세요.

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Azure AD를 사용하여 저장소 계정에 대한 액세스 보호

저장소 데이터에 대한 액세스를 보호하는 한 가지 방법은 저장소 계정 키에 대한 액세스를 제어하는 것입니다. 리소스 관리자 RBAC(역할 기반 Access Control)를 사용하여 사용자, 그룹 또는 응용 프로그램에 역할을 할당할 수 있습니다. 이러한 역할은 허용되거나 허용되지 않는 작업의 특정 집합에 연결됩니다. RBAC를 사용하여 저장소 계정에 대한 액세스 권한을 부여하는 경우 액세스 계층을 변경하는 등 해당 저장소 계정에 대한 관리 작업만을 처리합니다. RBAC를 사용하여 특정 컨테이너 또는 파일 공유와 같은 데이터 개체에 대한 액세스 권한을 부여할 수 없습니다. 그러나 RBAC를 사용하여 저장소 계정 키에 대한 액세스 권한을 부여할 수 있습니다. 그러면 데이터 개체를 읽는 데 사용할 수 있습니다.

### <a name="securing-access-using-shared-access-signatures"></a>공유 액세스 서명을 사용하여 액세스 보호

공유 액세스 서명 및 저장된 액세스 정책을 사용하여 데이터 개체를 보호할 수 있습니다. SAS(공유 액세스 서명)은 자산의 URI에 추가할 수 있는 보안 토큰을 포함하는 문자열로, 특정 저장소 개체에 대한 액세스 권한을 위임하고 사용 권한 및 액세스의 날짜/시간 범위와 같은 제한을 지정할 수 있도록 합니다. 이 기능에는 광범위한 기능이 있습니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

### <a name="public-access-to-blobs"></a>Blob에 대한 공용 액세스

Blob Service를 사용하면 컨테이너와 해당 Blob 또는 특정 Blob에 대한 공용 액세스를 제공할 수 있습니다. 컨테이너 또는 Blob을 공개로 지정하면 모든 사용자가 이 컨테이너 또는 Blob를 익명으로 읽을 수 있으며 인증이 필요 없습니다. 이 작업을 수행하는 예제는 Blob Storage의 이미지, 비디오 또는 문서를 사용하는 웹 사이트가 있는 경우입니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/storage-manage-access-to-resources.md)를 참조하세요.

## <a name="encryption"></a>암호화

Storage 서비스에 사용할 수 있는 기본적인 종류의 두 가지 암호화가 있습니다. 보안 및 암호화에 대한 자세한 내용은 [Azure Storage 보안 가이드](storage-security-guide.md)를 참조하세요.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화

미사용 Azure SSE(저장소 서비스 암호화)를 사용하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 이 기능을 통해 Azure Storage는 저장소를 유지하기 전에 데이터를 자동으로 암호화하고 검색하기 전에 암호를 해독합니다. 암호화, 암호 해독 및 키 관리는 사용자에게 완전히 투명하게 처리됩니다.

SSE는 모든 성능 계층(표준 및 프리미엄), 모든 배포 모델(Azure Resource Manager 및 클래식) 및 모든 Azure Storage 서비스(Blob, 큐, 테이블 및 파일)의 데이터를 자동으로 암호화합니다. SSE는 Azure Storage 성능에 영향을 주지 않습니다.

미사용 SSE 암호화에 대한 자세한 내용은 [미사용 데이터에 대한 Azure 저장소 서비스 암호화](storage-service-encryption.md)를 참조하세요.

### <a name="client-side-encryption"></a>클라이언트 쪽 암호화

저장소 클라이언트 라이브러리에는 네트워크를 통해 데이터를 클라이언트에서 Azure로 보내기 전에 프로그래밍 방식으로 암호화하도록 호출할 수 있는 메서드가 있습니다. 암호화되어 저장됩니다. 즉, 휴지 시 암호화됩니다. 데이터를 다시 읽을 경우 받은 이후 정보의 암호를 해독합니다.

클라이언트 쪽 암호화에 대한 자세한 내용은 [Microsoft Azure Storage용 .NET을 사용하는 클라이언트 쪽 암호화](storage-client-side-encryption.md)를 참조하세요.

## <a name="replication"></a>복제

데이터가 지속되는지 확인하기 위해 Azure Storage는 여러 데이터 복사본을 복제합니다. 저장소 계정을 설정할 때 복제 형식을 선택합니다. 대부분의 경우 저장소 계정을 만든 후 이 설정을 수정할 수 있습니다. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

재해 복구 정보는 [Azure Storage 중단이 발생할 경우 수행할 작업](storage-disaster-recovery-guidance.md)을 참조하세요.

## <a name="transferring-data-to-and-from-azure-storage"></a>Azure Storage 간에 데이터 전송

AzCopy 명령줄 유틸리티를 사용하여 저장소 계정 내에서 또는 저장소 계정 간에 Blob 및 파일 데이터를 복사할 수 있습니다. 도움말은 다음 문서 중 하나를 참조하세요.

* [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md)
* [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md)

AzCopy는 [Azure 데이터 이동 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)를 기반으로 구축되며 현재 미리 보기에서 사용할 수 있습니다.

Azure Import/Export 서비스는 저장소 계정 간에 대량의 Blob 데이터를 가져오거나 내보내는 데 사용할 수 있습니다. 여러 하드 드라이브를 준비하고 Azure 데이터 센터에 전자 메일로 보내면 여기에서 하드 드라이브 간에 데이터를 전송하고 하드 드라이브를 다시 사용자에게 보냅니다. Import/Export 서비스에 대한 자세한 내용은 [Microsoft Azure Import/Export 서비스를 사용하여 Blob Storage에 데이터 전송](../storage-import-export-service.md)을 참조하세요.

## <a name="pricing"></a>가격

Azure Storage에서 가격 책정에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="storage-apis-libraries-and-tools"></a>Storage API, 라이브러리 및 도구
Azure Storage 리소스는 HTTP/HTTPS 요청을 수행할 수 있는 모든 언어로 액세스할 수 있습니다. 또한 Azure storage는 많이 사용되는 몇 가지 언어를 위한 프로그래밍 라이브러리를 제공합니다. 이 라이브러리는 동기/비동기 호출, 작업 일괄 처리, 예외 관리, 자동 재시도, 작업자 동작 등과 같은 세부 사항을 처리하여 Azure Storage 작업의 많은 측면을 간소화합니다. 현재 이 라이브러리는 파이프라인의 다른 라이브러리와 함께 다음 언어 및 플랫폼에 대해 사용할 수 있습니다.

### <a name="azure-storage-data-api-and-library-references"></a>Azure Storage 데이터 API 및 라이브러리 참조
* [Storage 서비스 REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [.NET용 Storage 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
* [Java/Android용 Storage 클라이언트 라이브러리](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js용 Storage 클라이언트 라이브러리](https://docs.microsoft.com/javascript/api/azure-storage)
* [Python용 Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-python)
* [PHP용 Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-php)
* [Ruby용 Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-ruby)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure Storage 관리 API 및 라이브러리 참조
* [Storage 리소스 공급자 REST API](https://docs.microsoft.com/rest/api/storagerp/)
* [.NET용 Storage 리소스 공급자 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
* [Storage 서비스 관리 REST API(클래식](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure Storage 데이터 이동 API 및 라이브러리 참조
* [저장소 Import/Export 서비스 REST API](https://docs.microsoft.com/rest/api/storageimportexport/)
* [.NET용 저장소 데이터 이동 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement)

### <a name="tools-and-utilities"></a>도구 및 유틸리티
* [Storage용 Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/azure.storage)
* [Storage용 Azure CLI Cmdlet](https://docs.microsoft.com/cli/azure/storage)
* [AzCopy 명령줄 유틸리티](http://aka.ms/downloadazcopy)
* [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
* [Azure Storage 클라이언트 도구](../storage-explorers.md)
* [Azure 개발자 도구](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>다음 단계

Azure Storage를 사용하여 가동하고 실행하려면 [저장소 계정 만들기](storage-quickstart-create-account.md)를 참조하세요.
