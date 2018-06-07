---
title: Azure Storage에서 진단 데이터 저장 및 보기 | Microsoft Docs
description: Azure Storage로 Azure 진단 데이터를 가져오고 보기
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: adegeo
ms.openlocfilehash: 248e1c87ec244649a296233bf2aaa323d3475a79
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Azure Storage에서 진단 데이터 저장 및 보기
진단 데이터를 Microsoft Azure 저장소 에뮬레이터 또는 Azure 저장소에 전송하지 않는 한 진단 데이터는 영구적으로 저장되지 않습니다. 저장소에서 사용할 수 있는 여러 도구 중 하나로 한 번 볼 수 있습니다.

## <a name="specify-a-storage-account"></a>저장소 계정 지정
ServiceConfiguration.cscfg 파일에서 사용하려는 저장소 계정을 지정합니다. 계정 정보는 구성 설정에서 연결 문자열로 정의됩니다. 다음 예제에서는 Visual Studio에서 새 클라우드 서비스 프로젝트에 대해 생성된 기본 연결 문자열을 보여 줍니다.

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

이 연결 문자열을 변경하여 Azure 저장소 계정에 대한 계정 정보를 제공할 수 있습니다.

수집되는 진단 데이터의 형식에 따라 Azure 진단은 Blob service 또는 Table service를 사용합니다. 다음 표에서 유지되는 데이터 원본 및 해당 형식을 보여 줍니다.

| 데이터 원본 | 저장소 형식 |
| --- | --- |
| Azure 로그 |테이블 |
| IIS 7.0 로그 |Blob |
| Azure 진단 인프라 로그 |테이블 |
| 실패한 요청 추적 로그 |Blob |
| Windows 이벤트 로그 |테이블 |
| 성능 카운터 |테이블 |
| 크래시 덤프 |Blob |
| 사용자 지정 오류 로그 |Blob |

## <a name="transfer-diagnostic-data"></a>진단 데이터 전송
SDK 2.5 이상의 경우 구성 파일을 통해 진단 데이터 전송 요청이 발생할 수 있습니다. 구성에서 지정된 예약된 간격으로 진단 데이터를 전송할 수 있습니다.

SDK 2.4 및 이전 버전의 경우 프로그래밍 방식으로 구성 파일을 통해 진단 데이터 전송을 요청할 수 있습니다. 프로그래밍 방식을 사용하면 주문형 전송을 수행할 수 있습니다.

> [!IMPORTANT]
> Azure 저장소 계정에 진단 데이터를 전송하는 경우 진단 데이터를 사용하는 저장소 리소스에 대한 비용이 발생합니다.
> 
> 

## <a name="store-diagnostic-data"></a>진단 데이터 저장
로그 데이터는 다음과 같은 이름의 Blob 또는 테이블 저장소에 저장됩니다.

**테이블**

* **WadLogsTable** - 추적 수신기를 사용하여 코드에서 작성된 로그입니다.
* **WADDiagnosticInfrastructureLogsTable** - 진단 모니터 및 구성 변경 내용입니다.
* **WADDirectoriesTable** – 진단 모니터를 모니터링하는 디렉터리입니다.  IIS 로그, IIS 실패한 요청 로그 및 사용자 지정 디렉터리를 포함합니다.  Blob 로그 파일의 위치는 Container 필드에 지정되고 Blob의 이름은 RelativePath 필드에 있습니다.  AbsolutePath 필드는 Azure 가상 머신에 존재했던 파일의 이름과 위치를 나타냅니다.
* **WADPerformanceCountersTable** – 성능 카운터입니다.
* **WADWindowsEventLogsTable** – Windows 이벤트 로그입니다.

**Blob**

* **wad-control-container** – (SDK 2.4 및 이전 버전의 경우만) Azure 진단을 제어하는 XML 구성 파일을 포함합니다.
* **wad-iis-failedreqlogfiles** – IIS 실패한 요청 로그에서 정보를 포함합니다.
* **wad-iis-logfiles** – IIS 로그에 관한 정보를 포함합니다.
* **"custom"** – 진단 모니터에 의해 모니터링되는 구성 디렉터리에 기반한 사용자 지정 컨테이너입니다.  이 Blob 컨테이너의 이름은 WADDirectoriesTable에 지정됩니다.

## <a name="tools-to-view-diagnostic-data"></a>진단 데이터를 볼 도구
여러 도구를 사용하여 저장소로 전송된 후 데이터를 볼 수 있습니다. 예: 

* Visual Studio의 서버 탐색기 - Microsoft Visual Studio용 Azure 도구를 설치한 경우 서버 탐색기에서 Azure Storage 노드를 사용하여 Azure Storage 계정에서 읽기 전용 Blob 및 테이블 데이터를 볼 수 있습니다. 로컬 저장소 에뮬레이터 계정 및 Azure용으로 만든 저장소 계정에서 데이터를 표시할 수 있습니다. 자세한 내용은 [서버 탐색기로 Storage 리소스 탐색 및 관리](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md)를 참조하세요.
* [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md) 는 Windows, OSX 및 Linux에서 Azure Storage 데이터로 손쉽게 작업할 수 있도록 해주는 독립 실행형 앱입니다.
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) 에는 Azure에서 실행되는 응용 프로그램으로 수집된 진단 데이터를 보고 다운로드하고 관리할 수 있는 Azure 진단 관리자가 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 진단으로 Cloud Services 응용 프로그램의 흐름 추적](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)

