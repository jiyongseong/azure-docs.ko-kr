---
title: "Azure Files 소개 | Microsoft Docs"
description: "업계 표준 SMB 프로토콜을 사용하여 클라우드에서 네트워크 파일 공유를 만들고 사용할 수 있게 해주는 서비스인 Azure Files를 간략히 설명합니다."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/08/2017
ms.author: renash
ms.openlocfilehash: f96aa9fe12aba28e1ac3429f012419341bdf92c1
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2018
---
# <a name="introduction-to-azure-files"></a>Azure Files 소개
Azure Files는 산업 표준 [SMB(서버 메시지 블록) 프로토콜](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)(일반 인터넷 파일 시스템 또는 CIFS라고도 함)을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. Azure File 공유는 Windows, Linux 및 macOS의 클라우드 또는 온-프레미스 배포를 통해 동시에 탑재될 수 있습니다. 또한 데이터가 사용되는 위치 가까이에 빠르게 액세스하기 위해 Azure File Sync(미리 보기)를 사용하여 Azure File 공유를 Windows Server에서 캐시할 수 있습니다.

## <a name="videos"></a>동영상
| Azure Files Sync 소개(2분) | Sync를 사용하는 Azure Files(Ignite 2017)(85분)  |
|-|-|
| [![Azure Files Sync 소개 비디오의 동영상 가이드 - 재생하려면 클릭하세요.](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Sync를 사용하는 Azure Files 프레젠테이션의 동영상 가이드 - 재생하려면 클릭하세요.](./media/storage-files-introduction/azure-files-ignite-2017-video.png)](https://www.youtube.com/watch?v=r26jWDGF_rg) |

## <a name="why-azure-files-is-useful"></a>Azure Files가 유용한 이유
Azure 파일 공유를 사용하여 다음을 수행할 수 있습니다.

* **온-프레미스 파일 서버 바꾸기 또는 보완**:  
    Azure Files는 기존의 온-프레미스 파일 서버 또는 NAS 장치를 완전히 바꾸거나 보완하는 데 사용할 수 있습니다. Windows, macOS 및 Linux와 같이 자주 사용되는 운영 체제는 전세계 어디서나 Azure File 공유를 직접 탑재할 수 있습니다. Azure File 공유는 사용되는 데이터의 성능 및 분산 캐싱을 위해 Azure File Sync를 사용하여 온-프레미스 또는 클라우드의 Windows Server에 복제될 수도 있습니다.

* **응용 프로그램 "전환"**:  
    Azure Files를 사용하면 파일 응용 프로그램 또는 사용자 데이터를 저장하기 위해 파일 공유를 사용하는 클라우드로 응용 프로그램을 쉽게 "전환"할 수 있습니다. Azure Files를 사용하면 응용 프로그램 및 데이터를 모두 Azure로 이동시키는 "클래식" 전환 시나리오 및 응용 프로그램 데이터를 Azure Files로 이동시키는 "하이브리드" 전환 시나리오를 모두 사용하고 응용 프로그램이 계속 온-프레미스에서 실행됩니다. 

* **클라우드 개발 간소화**:  
    새로운 클라우드 개발 프로젝트를 간소화하기 위해 다양한 방법으로 Azure Files를 사용할 수도 있습니다. 예: 
    * **공유 응용 프로그램 설정**:  
        분산 응용 프로그램의 일반적인 패턴은 여러 응용 프로그램 인스턴스에서 액세스할 수 있는 중앙 집중식 위치에 구성 파일을 저장하는 것입니다. 응용 프로그램 인스턴스는 File REST API를 통해 해당 구성을 로드하고 사용자는 필요에 따라 SMB 공유를 로컬로 탑재하여 해당 인스턴스에 액세스할 수 있습니다.

    * **진단 공유**:  
        Azure File 공유는 클라우드 응용 프로그램에서 로그, 메트릭 및 크래시 덤프를 쓸 수 있는 편리한 장소입니다. 로그는 File REST API를 통해 응용 프로그램 인스턴스에서 작성할 수 있고 개발자는 로컬 컴퓨터에 파일 공유를 탑재하여 해당 로그에 액세스할 수 있습니다. 이 기능은 뛰어난 유연성을 제공하여 개발자가 기존에 사용하던 도구를 중지하지 않고도 클라우드 개발을 지속할 수 있습니다.

    * **개발/테스트/디버그**:  
        개발자 또는 관리자가 클라우드의 VM에서 작업할 때 종종 도구 또는 유틸리티 모음이 필요합니다. 각 VM에 이러한 유틸리티와 도구를 복사하는 작업은 시간이 오래 걸릴 수 있습니다. 개발자와 관리자는 VM에서 로컬로 Azure File 공유를 탑재하여 복사할 필요 없이 해당 도구 및 유틸리티에 빠르게 액세스할 수 있습니다.

## <a name="key-benefits"></a>주요 이점
* **공유 액세스** Azure 파일 공유는 산업 표준 SMB 프로토콜을 지원합니다. 즉, 응용 프로그램 호환성에 대한 걱정 없이 온-프레미스 파일 공유를 Azure File 공유로 원활하게 바꿀 수 있습니다. 여러 컴퓨터, 응용 프로그램/인스턴스 간에 파일 시스템을 공유할 수 있다는 것은 공유성이 필요한 응용 프로그램에 Azure Files를 사용하는 중요한 이점입니다. 
* **완벽한 관리** - Azure 파일 공유는 하드웨어 또는 OS를 관리할 필요 없이 만들 수 있습니다. 즉 서버 OS를 중요한 보안 업그레이드로 패치하거나 결함이 있는 하드 디스크를 교체하지 않아도 된다는 것입니다.
* **스크립팅 및 도구 지원** - PowerShell cmdlet 및 Azure CLI를 사용하여 Azure 응용 프로그램 관리의 일부로 Azure File 공유를 만들고, 탑재하고, 관리할 수 있습니다. Azure Portal 및 Azure Storage 탐색기를 사용하여 Azure File 공유를 만들고 관리할 수 있습니다. 
* **복원력**. Azure Files는 처음부터 항상 사용할 수 있도록 빌드되었습니다. 온-프레미스 파일 공유를 Azure Files로 바꾸는 경우 로컬 정전 또는 네트워크 문제를 처리하기 위해 더 이상 주의할 필요가 없습니다. 
* **친숙한 프로그래밍** - Azure에서 실행 중인 응용 프로그램은 [파일 시스템 I/O API](https://msdn.microsoft.com/library/system.io.file.aspx)를 통해 공유 데이터에 액세스할 수 있습니다. 따라서 개발자는 기존의 코드와 기술을 이용하여 기존 응용 프로그램을 마이그레이션할 수 있습니다. 시스템 IO API 외에도 [Azure Storage 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn261237.aspx) 또는 [Azure Storage REST API](/rest/api/storageservices/file-service-rest-api)를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure 파일 공유 만들기](storage-how-to-create-file-share.md)
* [Windows에 연결 및 탑재](storage-how-to-use-files-windows.md)
* [Linux에 연결 및 탑재](storage-how-to-use-files-linux.md)
* [macOS에 연결 및 탑재](storage-how-to-use-files-mac.md)
