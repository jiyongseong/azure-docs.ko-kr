---
title: Azure Import/Export를 사용하여 Azure Storage 간에 데이터 전송 | Microsoft Docs
description: Azure Portal에서 가져오기 및 내보내기 작업을 만들어 Azure Storage 간에 데이터를 전송하는 방법에 대해 알아봅니다.
author: muralikk
manager: syadav
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/22/2018
ms.author: muralikk
ms.openlocfilehash: f6dc104470ca2bfd738ca9bfc334a1c1325f7318
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Microsoft Azure Import/Export 서비스를 사용하여 Azure Storage로 데이터 전송
이 문서에서는 Azure Import/Export 서비스를 사용하여 디스크 드라이브를 Azure 데이터 센터에 발송하여 많은 양의 데이터를 안전하게 Azure Blob Storage로 전송하는 단계별 지침을 제공합니다. 이 서비스를 사용하여 데이터를 Azure 저장소에서 하드 디스크 드라이브로 전송하고 온-프레미스 사이트로 발송할 수도 있습니다. 단일 내부 SATA 디스크 드라이브의 데이터를 Azure Blob Storage나 Azure 파일로 가져올 수 있습니다. 

> [!IMPORTANT] 
> 이 서비스는 내부 SATA HDD 또는 SSD만 허용합니다. 다른 장치는 지원되지 않습니다. 외부 HDD나 NAS 장치 등은 가능한 경우 반품되거나 폐기되므로 보내지 않습니다.
>
>

디스크의 데이터를 Azure Storage로 가져와야 할 경우 아래 단계를 따릅니다.
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>1단계: WAImportExport 도구를 사용하여 드라이브를 준비하고 저널 파일을 생성합니다.

1.  Azure Storage로 가져올 데이터를 식별합니다. 로컬 서버 또는 네트워크 공유에서 디렉터리 및 독립 실행형 파일을 가져올 수 있습니다.
2.  전체 데이터 크기에 따라 필요한 2.5" SSD 또는 2.5"/3.5" SATA II/III 하드 디스크 드라이브 수를 확보합니다.
3.  SATA를 사용하여 하드 드라이브를 직접, 또는 외부 USB 어댑터를 통해 Windows 컴퓨터에 연결합니다.
1.  각 하드 드라이브에서 단일 NTFS 볼륨을 만들고 볼륨에 드라이브 문자를 할당합니다. 탑재 지점은 없습니다.
2.  Windows 컴퓨터에서 암호화를 사용하도록 설정하려면 NTFS 볼륨에 비트 보관 암호화를 사용하도록 설정합니다. https://technet.microsoft.com/library/cc731549(v=ws.10).aspx의 지침을 사용합니다.
3.  복사하여 붙여넣기, 끌어서 놓기 또는 Robocopy나 기타 도구를 사용하여 디스크에서 이러한 암호화된 단일 NTFS 볼륨에 데이터를 완전히 복사합니다.
7.  https://www.microsoft.com/en-us/download/details.aspx?id=42659에서 WAImportExport V1을 다운로드합니다.
8.  기본 폴더 waimportexportv1에 압축을 풉니다. 예를 들어 C:\WaImportExportV1입니다.  
9.  관리자로 실행하고 PowerShell 또는 명령줄을 연 다음 디렉터리를 압축을 푼 폴더로 변경합니다. 예를 들어 cd C:\WaImportExportV1입니다.
10. 다음 명령줄을 텍스트 편집기에 복사하고 편집하여 명령줄을 만듭니다.

    ```
    ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:***== /t:D /bk:*** /srcdir:D:\ /dstdir:ContainerName/ /skipwrite 
    ```
    
    명령줄 옵션은 다음 표에 설명되어 있습니다.

    |옵션  |설명  |
    |---------|---------|
    |/j:     |확장명이 .jrn인 저널 파일의 이름입니다. 저널 파일은 드라이브마다 생성됩니다. 디스크 일련 번호를 저널 파일 이름으로 사용하는 것이 좋습니다.         |
    |/sk:     |Azure Storage 계정 키입니다.         |
    |/t:     |배송할 디스크의 드라이브 문자입니다. 예: `D` 드라이브         |
    |/bk:     |드라이브의 BitLocker 키입니다. ` manage-bde -protectors -get D: ` 출력의 숫자 암호입니다.      |
    |/srcdir:     |`:\` 다음에 나오는 배송될 디스크의 드라이브 문자입니다. 예: `D:\`         |
    |/dstdir:     |Azure Storage의 대상 컨테이너 이름입니다.         |
    |/skipwrite:     |복사해야 하는 새 데이터가 없고 디스크의 기존 데이터가 준비되도록 지정하는 옵션입니다.         |
1. 배송할 디스크마다 10단계를 반복합니다.
2. 명령줄을 실행할 때마다 /j: 매개 변수와 함께 제공된 이름이 있는 저널 파일이 만들어집니다.

### <a name="step-2-create-an-import-job-on-azure-portal"></a>2단계: Azure Portal에서 가져오기 작업 만들기

1. https://portal.azure.com/에 로그온하고 다른 서비스 -> STORAGE -> "가져오기/내보내기 작업"에서 **가져오기/내보내기 작업 만들기**를 클릭합니다.

2. 기본 사항 섹션에서 "Azure로 가져오기"를 선택하고 작업 이름에 해당하는 문자열을 입력한 후 구독을 선택하고 리소스 그룹을 입력하거나 선택합니다. 가져오기 작업을 설명하는 이름을 입력합니다. 입력하는 이름에는 소문자, 숫자, 하이픈 및 밑줄만 포함될 수 있으며 문자로 시작해야 하고 공백이 포함될 수 없습니다. 작업이 진행 중인 동안 그리고 작업이 완료되면 선택한 이름을 사용하여 작업을 추적합니다.

3. 작업 세부 정보 섹션에서 드라이브 준비 단계 중 얻은 드라이브 저널 파일을 업로드합니다. waimportexport.exe version1을 사용한 경우 준비한 각 드라이브에 파일을 하나씩 업로드해야 합니다. "가져오기 대상" 저장소 계정 섹션에서 데이터를 가져올 저장소 계정을 선택합니다. 반납 위치는 선택한 저장소 계정의 지역에 따라 자동으로 채워집니다.
   
   ![가져오기 작업 만들기 - 3단계](./media/storage-import-export-service/import-job-03.png)
4. 반송 정보 섹션에서 드롭다운 목록에 있는 운송업체를 선택하고 해당 운송업체와 함께 생성한 유효한 운송업체 계정 번호를 입력합니다. 가져오기 작업이 완료되면 Microsoft는 이 계정을 사용하여 사용자에게 드라이브를 배송합니다. 완전하며 올바른 연락처 이름, 전화 번호, 전자 메일, 주소, 구/군/도시, 우편 번호, 시/도 및 국가/지역을 제공합니다.
   
5. 요약 섹션에서 Azure DC에 디스크를 배송하는 데 사용할 Azure Data Center 배송지 주소가 제공됩니다. 작업 이름 및 전체 주소가 배송 레이블에 언급되어 있는지 확인합니다. 

6. 요약 페이지에서 확인을 클릭하여 가져오기 작업 만들기를 완료합니다.

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>3단계: 2단계에서 제공한 Azure Datacenter 배송 주소로 드라이브를 보냅니다.
FedEx, UPS 또는 DHL을 통해 패키지를 Azure DC로 보낼 수 있습니다.

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>4단계: 배송 추적 번호를 통해 2단계에서 만든 작업을 업데이트합니다.
디스크를 배송한 후 Azure Portal의 **가져오기/내보내기** 페이지로 이동하여 a) 가져오기 작업을 찾아 클릭한 후 b) **드라이브가 배송되면 작업 상태 및 추적 정보를 업데이트합니다.**  단계를 통해 추적 번호를 업데이트합니다. c) 확인란 “배송됨으로 표시”를 선택합니다. d) 운송업체 및 추적 번호를 제공합니다.
작업을 만든 지 2 주 이내에 추적 번호를 업데이트하지 않으면, 작업이 만료됩니다. 포털 대시보드에서 작업 진행 상태를 추적할 수 있습니다. 각 작업 상태가 무엇을 의미하는지는 [작업 상태 보기](#viewing-your-job-status)에 대한 이전 섹션에서 확인하세요.

## <a name="when-should-i-use-the-azure-importexport-service"></a>언제 Azure Import/Export 서비스를 사용해야 하나요?
네트워크를 통한 데이터 업로드 또는 다운로드가 너무 느리거나 추가 네트워크 대역폭 비용이 너무 비싼 경우 Azure Import/Export 서비스 사용을 고려할 수 있습니다.

다음과 같은 시나리오에서 이 서비스를 사용할 수 있습니다.

* 클라우드로 데이터 마이그레이션: 많은 양의 데이터를 Azure로 신속하고 비용 효과적으로 이동합니다.
* 콘텐츠 배포: 고객 사이트로 데이터를 신속하게 전송합니다.
* Backup: 온-프레미스 데이터의 백업을 가져와 Azure Storage에 저장합니다.
* 데이터 복구: 저장소에 저장된 많은 양의 데이터를 복구하여 온-프레미스 위치에 배달합니다.

## <a name="prerequisites"></a>필수 조건
이 섹션에서는 이 서비스를 사용하는 데 필요한 필수 조건에 대해 설명합니다. 드라이브를 발송하기 전에 주의 깊게 검토하세요.

### <a name="storage-account"></a>Storage 계정
Import/Export 서비스를 사용하려면 기존 Azure 구독과 하나 이상의 저장소 계정이 있어야 합니다. Azure 가져오기/내보내기는 클래식, Blob Storage 계정 및 일반 용도 v1 저장소 계정만 지원합니다. 각 작업은 하나의 저장소 계정에서만 데이터 전송에 사용될 수 있습니다. 다시 말해, 하나의 가져오기/내보내기 작업이 여러 저장소 계정에서 사용될 수 없습니다. 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](storage-create-storage-account.md#create-a-storage-account)(영문)을 참조하세요.

> [!IMPORTANT] 
> Azure 가져오기 내보내기 서비스는 [Virtual Network 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md) 기능이 활성화된 저장소 계정을 지원하지 않습니다. 
> 
> 

### <a name="data-types"></a>데이터 형식
Azure Import/Export 서비스를 사용하여 데이터를 **블록** Blob, **페이지** Blob 또는 **파일**로 복사할 수 있습니다. 반대로 이 서비스를 사용하여 Azure 저장소에서 **블록** Blob, **페이지** Blob 또는 **추가** Blob을 내보내기만 할 수도 있습니다. 서비스는 Azure Storage로 Azure 파일 가져오기만 지원합니다. Azure 파일 내보내기는 현재 지원되지 않습니다.

### <a name="job"></a>작업
저장소에서 가져오기 또는 내보내기 프로세스를 시작하려면 먼저 작업을 만듭니다. 작업은 가져오기 작업 또는 내보내기 작업이 될 수 있습니다.

* 온-프레미스에 있는 데이터를 Azure 저장소 계정으로 전송하려면 가져오기 작업을 만듭니다.
* 저장소 계정에 현재 저장되어 있는 데이터를 Microsoft에 배송할 하드 드라이브로 전송하려면 내보내기 작업을 만듭니다. 작업을 만들 때 Azure 데이터 센터로 하나 이상의 하드 드라이브가 발송된다는 것을 Import/Export 서비스에 알립니다.

* 가져오기 작업의 경우 데이터가 포함된 하드 드라이브가 발송됩니다.
* 내보내기 작업의 경우 빈 하드 드라이브가 발송됩니다.
* 작업당 최대 10개의 하드 디스크 드라이브를 발송할 수 있습니다.

Azure Portal 또는 [Azure Storage Import/Export REST API](/rest/api/storageimportexport)를 사용하여 가져오기 또는 내보내기 작업을 만들 수 있습니다.

> [!Note]
> RDFE API는 2018년 2월 28일부터 지원되지 않습니다. 이 서비스를 계속 사용하려면 [ARM Import/Export REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/storageimportexport/resource-manager/Microsoft.ImportExport/stable/2016-11-01/storageimportexport.json)로 마이그레이션합니다. 

### <a name="waimportexport-tool"></a>WAImportExport 도구
**가져오기** 작업을 만드는 첫 번째 단계는 가져오기를 위해 운송될 드라이브를 준비하는 것입니다. 드라이브를 준비하려면 드라이브를 로컬 서버에 연결하고 로컬 서버에서 WAImportExport 도구를 실행해야 합니다. 이 WAImportExport 도구를 사용하면 데이터를 드라이브에 복사하고, 드라이브에 있는 데이터를 BitLocker로 암호화하며, 드라이브 저널 파일을 생성하는 작업을 손쉽게 수행할 수 있습니다.

저널 파일은 드라이브 일련 번호 및 저장소 계정 이름과 같은 작업 및 드라이브에 대한 기본 정보를 저장합니다. 이 저널 파일은 드라이브에 저장되지 않으며 가져오기 작업을 만드는 동안 사용됩니다. 작업 만들기의 단계별 세부 내용은 이 문서의 뒷부분에 제공됩니다.

WAImportExport 도구는 64비트 Windows 운영 체제에서만 호환됩니다. 지원되는 특정 OS 버전에 대해서는 [운영 체제](#operating-system) 섹션을 참조하세요.

최신 버전의 [WAImportExport 도구](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip)를 다운로드합니다. WAImportExport 도구 사용에 대한 자세한 내용은 [WAImportExport 도구 사용](storage-import-export-tool-how-to.md)(영문)을 참조하세요.

>[!NOTE]
>**이전 버전:** 도구의 [WAImportExpot V1 버전을 다운로드](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip)하고 [WAImportExpot V1 사용 가이드](storage-import-export-tool-how-to-v1.md)을 참조할 수 있습니다. 도구의 WAImportExpot V1 버전은 **이미 디스크에 데이터가 미리 기록되어 있을 때 디스크를 준비**하도록 지원합니다. 사용할 수 있는 유일한 키가 SAS 키인 경우에는 WAImportExpot V1 도구를 사용해야 합니다.

>

### <a name="hard-disk-drives"></a>하드 디스크 드라이브
2.5" SSD 또는 2.5"/3.5" SATA II/III 내부 HDD만 Import/Export 서비스에서 사용하도록 지원됩니다. 단일 가져오기/내보내기 작업에는 최대 10개의 HDD/SSD가 있을 수 있으며, 개별 HDD/SSD는 임의 크기일 수 있습니다. 다수의 드라이브가 여러 작업에 분산될 수 있으며, 만들 수 있는 작업 수에 대한 제한은 없습니다. 

가져오기 작업의 경우 드라이브의 첫 번째 데이터 볼륨만 처리됩니다. 데이터 볼륨은 NTFS로 포맷되어 있어야 합니다.

> [!IMPORTANT]
> 기본 제공 USB 어댑터와 함께 제공되는 외부 하드 디스크 드라이브는 이 서비스에서 지원하지 않습니다. 외장 HDD 케이스 안의 디스크도 사용할 수 없습니다. 외장 HDD를 발송하지 마세요.
> 
> 

다음은 데이터를 내부 HDD에 복사하는 데 사용되는 외부 USB 어댑터 목록입니다. Anker 68UPSATAA-02BU Anker 68UPSHHDS-BU Startech SATADOCK22UE Orico 6628SUS3-C-BK(6628 Series) Thermaltake BlacX 핫스왑 SATA 외부 하드 드라이브 도킹 스테이션(USB 2.0 및 eSATA)

### <a name="encryption"></a>암호화
드라이브의 데이터는 BitLocker 드라이브 암호화를 사용하여 암호화되어야 합니다. 이렇게 암호화하면 전송 중 데이터가 보호됩니다.

가져오기 작업의 경우 암호화를 수행하는 두 가지 방법이 있습니다. 첫 번째 방법은 드라이브 준비 중에 WAImportExport 도구를 실행하면서 데이터 집합 CSV 파일을 사용할 때 옵션을 지정하는 것입니다. 두 번째 방법은 드라이브 준비 중에 드라이브에서 BitLocker 암호화를 사용하도록 수동으로 설정하고 WAImportExport 도구 명령줄을 실행할 때 드라이브 집합 CSV에 암호화 키를 지정하는 것입니다.

내보내기 작업의 경우 데이터가 드라이브에 복사된 후 서비스에서 이를 다시 사용자에게 발송하기 전에 BitLocker를 사용하여 드라이브를 암호화합니다. 암호화 키는 Azure Portal을 통해 제공됩니다.  

### <a name="operating-system"></a>운영 체제
다음 64비트 운영 체제 중 하나를 사용하여 드라이브를 Azure로 운송하기 전에 WAImportExport 도구를 사용하여 하드 드라이브를 준비할 수 있습니다.

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. 이러한 모든 운영 체제는 BitLocker 드라이브 암호화를 지원합니다.

### <a name="locations"></a>위치
Azure Import/Export 서비스는 모든 공용 Azure 저장소 계정으로의 데이터 복사를 지원합니다. 나열된 위치 중 하나로 하드 디스크 드라이브를 발송할 수 있습니다. 저장소 계정이 여기에 지정되지 않은 공용 Azure 위치에 있는 경우 Azure Portal 또는 Import/Export REST API를 사용하여 작업을 만들 때 대체 운송 위치가 제공됩니다.

지원되는 발송 위치:

* 미국 동부
* 미국 서부
* 미국 동부 2
* 미국 서부 2
* 미국 중부
* 미국 중북부
* 미국 중남부
* 미국 중서부
* 북유럽
* 서유럽
* 동아시아
* 동남아시아
* 오스트레일리아 동부
* 오스트레일리아 남동부
* 일본 서부
* 일본 동부
* 인도 중부
* 인도 남부
* 인도 서부
* 캐나다 중부
* 캐나다 동부
* 브라질 남부
* 한국 중부
* 미국 정부 버지니아
* 미국 정부 아이오와
* 미국 국방부 동부
* 미국 국방부 중부
* 중국 동부
* 중국 북부
* 영국 남부
* 독일 중부
* 독일 북동부

### <a name="shipping"></a>발송
**데이터 센터에 드라이브 배송:**

가져오기 또는 내보내기 작업을 만들 때 지원되는 드라이브 발송 위치 중 하나의 배송지 주소를 제공합니다. 제공되는 배송지 주소는 저장소 계정 위치에 따라 달라지지만 저장소 계정 위치와 같지 않을 수도 있습니다.

FedEx, UPS 또는 DHL을 사용하여 드라이브를 배송지 주소로 배송할 수 있습니다.

**데이터 센터에서 드라이브 배송:**

가져오기 또는 내보내기 작업을 만들 때 작업이 완료된 후 드라이브를 다시 배송할 때 Microsoft가 사용할 반송 주소를 제공해야 합니다. 처리가 지연되지 않도록 유효한 반송 주소를 제공했는지 확인하세요.

운송업체는 관리 연속성을 유지하기 위해 적절히 추적해야 합니다. 드라이브를 다시 배송하기 위해 Microsoft에서 사용할 유효한 FedEx, UPS 또는 DHL 운송업체 계정 번호를 제공해야 합니다. 미국 또는 유럽 지역에서 드라이브를 다시 배송하려면 FedEx, UPS 또는 DHL 계정 번호가 필요합니다. 아시아 및 오스트레일리아 위치에서 드라이브를 다시 배송하려면 DHL 계정 번호가 필요합니다. 계정 번호가 없을 경우 [FedEx](http://www.fedex.com/us/oadr/)(미국 및 유럽) 또는 [DHL](http://www.dhl.com/)(아시아 및 오스트레일리아) 운송업체 계정을 만들 수 있습니다. 운송업체 계정 번호가 이미 있는 경우 이 계정 번호가 유효한지 확인하세요.

패키지를 배송할 때는 [Microsoft Azure 서비스 조건](https://azure.microsoft.com/support/legal/services-terms/)의 조항을 따라야 합니다.

> [!IMPORTANT]
> 배송하는 실제 미디어는 국경을 지나야 할 수 있습니다. 실제 미디어와 데이터를 관련 법률에 따라 가져오거나 내보내도록 해야 합니다. 실제 미디어를 배송하기 전에 관리자에게 미디어 및 데이터를 확인된 데이터 센터에 합법적으로 배송할 수 있는지 확인하세요. 이렇게 하면 해당 품목이 시기 적절하게 Microsoft에 도착될 수 있습니다. 예를 들어 국경을 통과하는 모든 소포에는 상업 송장이 함께 제공되어야 합니다(유럽 연합 내에서 국경을 통과하는 경우 제외). 통신 회사 웹 사이트에서 상업 송장의 완성된 사본을 인쇄할 수 있습니다. 상업 송장의 예로 [DHL 상업 송장](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf)과 [FedEx 상업 송장](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf)이 있습니다. Microsoft가 수출업체로 표시되지 않았는지 확인합니다.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Azure Import/Export 서비스는 어떻게 작동하나요?
Azure Import/Export 서비스를 통해 작업을 만들고 하드 디스크 드라이브를 Azure 데이터 센터에 발송함으로써 온-프레미스 사이트와 Azure Storage 간에 데이터를 전송할 수 있습니다. 발송하는 각 하드 디스크 드라이브는 단일 작업과 연결됩니다. 각 작업은 단일 저장소 계정과 연결됩니다. [필수 조건 섹션](#pre-requisites)을 주의 깊게 검토하여 지원되는 데이터 형식, 디스크 형식, 위치 및 발송과 같은 이 서비스의 세부 사항에 대해 알아봅니다.

이 섹션에서는 가져오기 및 내보내기 작업에 관련된 단계를 개략적으로 설명합니다. [빠른 시작 섹션](#quick-start)의 뒷부분에서 가져오기 및 내보내기 작업을 만드는 단계별 지침을 제공합니다.

### <a name="inside-an-import-job"></a>가져오기 작업 내부
가져오기 작업은 개략적으로 다음 단계를 포함합니다.

* 가져올 데이터와 필요한 드라이브 수를 결정합니다.
* Azure Storage에서 데이터의 대상 Blob 또는 파일 위치를 식별합니다.
* WAImportExport 도구를 사용하여 데이터를 하나 이상의 하드 디스크 드라이브에 복사하고 BitLocker로 암호화합니다.
* Azure Portal 또는 Import/Export REST API를 사용하여 대상 저장소 계정에 가져오기 작업을 만듭니다. Azure Portal을 사용하는 경우 드라이브 저널 파일을 업로드합니다.
* 드라이브를 다시 배송하는 데 사용될 반송 주소 및 운송업체 계정 번호를 제공합니다.
* 작업을 만드는 동안 제공된 배송지 주소로 하드 디스크 드라이브를 발송합니다.
* 가져오기 작업 세부 정보에서 배송 추적 번호를 업데이트하고 가져오기 작업을 제출합니다.
* 드라이브는 Azure 데이터 센터에서 수신 및 처리됩니다.
* 드라이브는 운송업체 계정을 사용하여 가져오기 작업에 제공된 반송 주소로 배송됩니다.
  
    ![그림 1: 가져오기 작업 흐름](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>내보내기 작업 내부
> [!IMPORTANT]
> 이 서비스는 Azure Blob 내보내기만 지원하며 Azure 파일 내보내기는 지원하지 않습니다.
> 
>

내보내기 작업은 개략적으로 다음 단계를 포함합니다.

* 내보낼 데이터와 필요한 드라이브 수를 결정합니다.
* Blob 저장소의 데이터 컨테이너 경로 또는 원본 Blob을 식별합니다.
* Azure Portal 또는 Import/Export REST API를 사용하여 원본 저장소 계정에 내보내기 작업을 만듭니다.
* 내보내기 작업에서 데이터 컨테이너 경로 또는 원본 Blob을 지정합니다.
* 드라이브를 다시 배송하는 데 사용될 반송 주소 및 운송업체 계정 번호를 제공합니다.
* 작업을 만드는 동안 제공된 배송지 주소로 하드 디스크 드라이브를 발송합니다.
* 내보내기 작업 세부 정보에서 배송 추적 번호를 업데이트하고 내보내기 작업을 제출합니다.
* 드라이브는 Azure 데이터 센터에서 수신 및 처리됩니다.
* 드라이브는 BitLocker로 암호화하고, 키는 Azure Portal을 통해 사용할 수 있습니다.  
* 드라이브는 운송업체 계정을 사용하여 가져오기 작업에 제공된 반송 주소로 배송됩니다.
  
    ![그림 2: 내보내기 작업 흐름](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>작업 및 드라이브 상태 보기
Azure Portal에서 가져오기 또는 내보내기 작업의 상태를 추적할 수 있습니다. **Import/Export** 탭을 클릭합니다. 작업 목록이 페이지에 나타납니다.

![작업 상태 보기](./media/storage-import-export-service/jobstate.png)

프로세스에서 드라이브 위치에 따라 다음 작업 상태 중 하나가 표시됩니다.

| 작업 상태 | 설명 |
|:--- |:--- |
| 만드는 중 | 작업이 만들어지면 작업 상태는 '만드는 중'으로 설정됩니다. 작업이 '만드는 중' 상태로 있는 동안에는 Import/Export 서비스에서 드라이브를 데이터 센터로 운송하지 않았다고 가정합니다. 직업은 최대 2주 동안 '만드는 중' 상태로 남아 있을 수 있으며 그 이후에는 서비스에서 자동으로 삭제합니다. |
| 발송 | 패키지를 운송한 후 Azure Portal에서 추적 정보를 업데이트해야 합니다.  그러면 작업이 '운송 중'으로 바뀝니다. 작업은 최대 2주 동안 '운송 중' 상태로 유지됩니다. 
| 수신됨 | 데이터 센터에서 모든 드라이브를 받은 후에는 작업 상태가 '수신됨'으로 설정됩니다. |
| 전송 중 | 하나 이상의 드라이브에서 처리를 시작하면 작업 상태가 '전송 중'으로 설정됩니다. 자세한 내용은 아래의 '드라이브 상태' 섹션을 참조하세요. |
| 패키징 | 모든 드라이브에서 처리를 완료한 후에 고객에게 드라이브를 반송할 때까지 작업은 '포장 중' 상태가 됩니다. |
| Completed | 고객에게 모든 드라이브를 반송한 후에 작업이 오류 없이 완료되면 작업은 '완료됨' 상태로 설정됩니다. '완료됨' 상태에서 90일이 경과하면 작업이 자동으로 삭제됩니다. |
| 닫힘 | 고객에게 모든 드라이브를 반송한 후 작업 처리 중에 오류가 발생하면 작업이 '종료됨' 상태로 설정됩니다. '종료됨' 상태에서 90일이 경과하면 작업이 자동으로 삭제됩니다. |

아래 표에서는 개별 드라이브가 가져오기 또는 내보내기 작업을 통해 전환할 때의 수명 주기를 설명합니다. 이제 Azure Portal에서 작업에 속한 각 드라이브의 현재 상태를 볼 수 있습니다.
다음 표에서는 작업에 속한 각 드라이브가 통과할 수 있는 각각의 상태를 설명합니다.

| 드라이브 상태 | 설명 |
|:--- |:--- |
| 지정됨 | 가져오기 작업의 경우 Azure Portal에서 작업을 만들 때 드라이브의 초기 상태는 '지정됨' 상태입니다. 내보내기 작업의 경우 작업을 만들 때 드라이브가 지정되지 않으므로 초기 드라이브 상태는 '수신됨' 상태입니다. |
| 수신됨 | Import/Export 서비스 운영자가 가져오기 작업을 위해 운송 회사로부터 받은 드라이브를 처리할 때 드라이브가 '수신됨' 상태로 전환합니다. 내보내기 작업의 경우 초기 드라이브 상태는 '수신됨' 상태입니다. |
| 수신되지 않음 | 작업 패키지가 도착했지만 패키지에 드라이브가 포함되어 있지 않으면 해당 드라이브가 '수신되지 않음' 상태로 전환합니다. 또한 서비스에서 운송 정보를 받은 후 2주가 경과했지만 패키지가 데이터 센터에 아직 도착하지 않은 경우에도 드라이브가 이 상태로 전환할 수 있습니다. |
| 전송 중 | 서비스에서 드라이브로부터 Microsoft Azure Storage로 데이터를 전송하기 시작하면 드라이브가 '전송 중' 상태로 전환합니다. |
| Completed | 서비스에서 모든 데이터를 오류 없이 성공적으로 전송하면 드라이브가 '완료됨' 상태로 전환합니다.
| 완료됨(추가 정보 필요) | 데이터를 드라이브에(서) 복사하는 동안 서비스에서 몇 가지 문제가 발생하면 드라이브가 '완료됨(추가 정보 필요)' 상태로 전환합니다. 정보에는 Blob 덮어쓰기에 대한 오류, 경고 또는 정보 메시지가 포함될 수 있습니다.
| 반송됨 | 드라이브가 데이터 센터에서 반송 주소로 반송되면 '반송됨' 상태로 전환합니다. |

Azure Portal의 이 이미지는 예제 작업의 드라이브 상태를 나타냅니다.

![드라이브 상태 보기](./media/storage-import-export-service/drivestate.png)

다음 표에서는 드라이브 오류 상태 및 각 상태에 대해 수행하는 작업을 설명합니다.

| 드라이브 상태 | 행사 | 해결 방법/다음 단계 |
|:--- |:--- |:--- |
| 수신되지 않음 | 작업 운송물의 일부로 받지 못하여 '수신되지 않음'으로 표시된 드라이브가 다른 운송을 통해 도착했습니다. | 운영 팀이 드라이브를 '수신됨' 상태로 전환합니다. |
| 해당 없음 | 작업에 포함되지 않은 드라이브가 다른 작업의 일부로 데이터 센터에 도착했습니다. | 드라이브가 추가 드라이브로 표시되며 원래 패키지와 관련된 작업이 완료되면 고객에게 반송됩니다. |

### <a name="time-to-process-job"></a>작업 처리 시간
가져오기/내보내기 작업을 처리하는 데 소요되는 시간은 발송 시간, 데이터 센터의 부하, 복사되는 데이터의 작업 유형 및 크기, 작업의 디스크 수와 같은 다양한 요소에 따라 다릅니다. Import/Export 서비스에는 SLA가 없지만 이 서비스는 디스크가 수신되고 7~10일 내에 복사를 완료하려고 합니다. Azure Portal에 게시된 상태 뿐만 아니라, REST API를 사용하여 작업 진행 상황을 추적할 수 있습니다. 작업 나열 작업의 API 호출에 포함된 완료율 매개 변수는 복사 진행률을 제공합니다.

### <a name="pricing"></a>가격
**드라이브 취급 수수료**

가져오기 또는 내보내기 작업의 일부로 처리되는 각 드라이브에 대해 드라이브 취급 수수료가 있습니다. [Azure Import/Export 가격 책정](https://azure.microsoft.com/pricing/details/storage-import-export/)에서 자세한 내용을 확인하세요.

**발송 비용**

Azure에 드라이브를 발송하는 경우 운송업체에 발송 비용을 지불합니다. Microsoft에서 드라이브를 반환할 때 발송 비용은 작업을 만들 때 제공한 운송업체 계정으로 청구됩니다.

**트랜잭션 비용**

데이터를 Azure Storage로 가져올 때 표준 저장소 트랜잭션 비용 이외에 필요한 트랜잭션 비용은 없습니다. Blob 저장소에서 데이터를 내보낼 때는 표준 송신 요금이 적용됩니다. 트랜잭션 비용에 대한 자세한 내용은 [데이터 전송 가격 책정](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>내부 SATA HDD와 SSD를 사용하여 Azure File Storage에 데이터를 가져오는 방법
디스크의 데이터를 Azure File Storage로 가져와야 할 경우 아래 단계를 따릅니다.
Azure Import/Export 서비스를 사용하여 데이터를 가져올 때 첫 번째 단계는 WAImportExport 도구를 사용하여 드라이브를 준비하는 것입니다. 아래 단계를 따라 드라이브를 준비합니다.

1. Azure File Storage로 가져올 데이터를 식별합니다. 이는 로컬 서버에 있는 디렉터리 및 독립 실행형 파일이거나 또는 네트워크 공유일 수 있습니다.  
2. 전체 데이터 크기에 따라 필요한 드라이브 수를 결정합니다. 필요한 2.5" SSD 또는 2.5"/3.5" SATA II/III 하드 디스크 드라이브 수를 확보합니다.
4. 각 하드 디스크 드라이브에 복사할 독립 실행형 파일 및/또는 디렉터리를 결정합니다.
5. 데이터 집합/드라이브 집합을 위한 CSV 파일을 만듭니다.
    
  다음은 Azure 파일로 데이터를 가져오기 위한 샘플 데이터 집합 CSV 파일 예제입니다.
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   위의 예제에서 100M_1.csv.txt는 "fileshare"의 루트에 복사됩니다. "fileshare"가 없으면 만듭니다. 50M_original 아래의 모든 파일과 폴더가 "fileshare"에 반복적으로 복사됩니다. 폴더 구조는 유지됩니다.

    [데이터 집합 CSV 파일 준비](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file)에 대해 자세히 알아보세요.
    


    **드라이브 집합 CSV 파일**

    드라이브 집합 플래그의 값은 도구에서 준비할 디스크 목록을 정확하게 선택할 수 있도록 드라이브 문자가 매핑된 디스크 목록을 포함하고 있는 CSV 파일입니다. 

    다음은 드라이브 집합 CSV 파일의 예제입니다.
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    위의 예제에서는 두 개의 디스크가 연결되어 있고 G:\ 및 H:\ 볼륨 문자를 사용하는 기본 NTFS 볼륨이 만들어 져 있다고 가정합니다. 이 도구는 H:\를 호스팅하는 디스크를 포맷하고 암호화하지만 G:\ 디스크 호스팅 볼륨은 포맷하거나 암호화하지 않습니다.

    [드라이브 집합 CSV 파일 준비](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file)에 대해 자세히 알아보세요.

6.  [WAImportExport 도구](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip)를 사용하여 하나 이상의 하드 드라이브에 데이터를 복사합니다.
7.  드라이브 집합 CSV의 Encryption 필드에 "Encrypt"를 지정하여 하드 디스크 드라이브에서 BitLocker 암호화를 사용하도록 설정할 수 있습니다. 또는 도구를 실행하는 동안 하드 디스크 드라이브에서 BitLocker 암호화를 사용하도록 수동으로 설정하고, 드라이브 집합 CSV에 "AlreadyEncrypted"를 지정한 다음 키를 제공할 수도 있습니다.

8. 디스크 준비를 완료한 후 하드 디스크 드라이브 또는 저널 파일에 있는 데이터를 수정하지 마세요.

> [!IMPORTANT]
> 준비하는 각 하드 디스크 드라이브는 저널 파일이 됩니다. Azure Portal을 사용하여 가져오기 작업을 만들 때 해당 가져오기 작업의 일부인 드라이브의 모든 저널 파일을 업로드해야 합니다. 저널 파일이 없는 드라이브는 처리되지 않습니다.
> 
>

다음은 WAImportExport 도구를 사용하여 하드 디스크 드라이브를 준비하기 위한 명령과 예제입니다.

새 복사 세션이 있는 디렉터리 및/또는 파일을 복사하는 첫 번째 복사 세션에 대한 WAImportExport 도구의 PrepImport 명령:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**가져오기 예제 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

**드라이브를 추가**하려면 새 드라이브 집합 파일을 만들고 아래 명령을 실행합니다. InitialDriveset .csv 파일에 지정된 것과 다른 디스크 드라이브에 대한 후속 복사 세션의 경우 새 드라이브 집합 CSV 파일을 지정하고 "AdditionalDriveSet" 매개 변수에 값으로 제공합니다. **동일한 저널 파일** 이름을 사용하고 **새 세션 ID**를 제공합니다. AdditionalDriveset CSV 파일의 형식은 InitialDriveSet 형식과 같습니다.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**가져오기 예제 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

추가 데이터를 동일한 드라이브 집합에 추가하려면 WAImportExport 도구의 PrepImport 명령을 호출하여 후속 복사 세션에서 추가 파일/디렉터리를 복사할 수 있습니다. InitialDriveset .csv 파일에 지정된 동일한 하드 디스크 드라이브에 복사하는 후속 복사 세션의 경우 **동일한 저널 파일** 이름을 사용하고 **새 세션 ID**를 제공합니다. 저장소 계정 키는 제공할 필요가 없습니다.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**가져오기 예제 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

WAImportExport 도구 사용에 대한 자세한 내용은 [가져오기 작업을 위한 하드 드라이브 준비](storage-import-export-tool-preparing-hard-drives-import.md)를 참조하세요.

또한 좀 더 자세한 단계별 지침에 대해서는 [가져오기 작업을 위해 하드 드라이브를 준비하는 예제 워크플로](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)를 참조하세요.  



## <a name="create-an-export-job"></a>내보내기 작업 만들기
내보내기 작업을 만들고 저장소 계정에서 드라이브로 데이터를 내보낸 다음 드라이브를 사용자에게 발송할 수 있도록 하나 이상의 빈 드라이브가 데이터 센터로 발송됨을 Import/Export 서비스에 알립니다.

### <a name="prepare-your-drives"></a>드라이브 준비
내보내기 작업을 위한 드라이브 준비를 위해 다음 사전 검사를 수행하는 것이 좋습니다.

1. WAImportExport 도구의 PreviewExport 명령을 사용하여 필요한 디스크 수를 확인합니다. 자세한 내용은 [내보내기 작업에 대한 드라이브 사용량 미리 보기](https://msdn.microsoft.com/library/azure/dn722414.aspx)를 참조하세요. 사용하고자 하는 드라이브의 크기에 따라 선택한 Blob에 대한 드라이브 사용량을 미리볼 수 있습니다.
2. 내보내기 작업에 대해 제공될 하드 드라이브에 읽거나/쓸 수 있는지 확인합니다.

### <a name="create-the-export-job"></a>내보내기 작업 만들기
1. 내보내기 작업을 만들려면 Azure Portal에서 더 많은 서비스 -> 저장소 -> "가져오기/내보내기 작업"으로 이동합니다. **가져오기/내보내기 작업 만들기**를 클릭합니다.
2. 1단계의 기본 사항에서 "Azure에서 내보내기"를 선택하고 작업 이름에 해당하는 문자열을 입력한 후 구독을 선택하고 리소스 그룹을 입력하거나 선택합니다. 가져오기 작업을 설명하는 이름을 입력합니다. 입력하는 이름에는 소문자, 숫자, 하이픈 및 밑줄만 포함될 수 있으며 문자로 시작해야 하고 공백이 포함될 수 없습니다. 작업이 진행 중인 동안 그리고 작업이 완료되면 선택한 이름을 사용하여 작업을 추적합니다. 해당 내보내기 작업을 담당하는 사용자의 연락처 정보를 제공합니다. 

3. 2단계의 작업 세부 정보에서 저장소 계정 섹션에서 데이터를 내보낼 저장소 계정을 선택합니다. 반납 위치는 선택한 저장소 계정의 지역에 따라 자동으로 채워집니다. 저장소 계정에서 하나 이상의 빈 드라이브로 내보낼 Blob 데이터를 지정합니다. 저장소 계정의 모든 Blob 데이터를 내보내도록 선택하거나 내보낼 Blob 또는 Blob 집합을 지정할 수 있습니다.
   
   내보낼 Blob을 지정하려면 **같음** 선택기를 사용하여 컨테이너 이름으로 시작하는 Blob의 상대 경로를 지정합니다. 루트 컨테이너를 지정하려면 *$root* 를 사용합니다.
   
   특정 접두사로 시작하는 Blob을 모두 지정하려면 **시작 단어** 선택기를 사용하고 슬래시 '/'로 시작하는 접두사를 지정합니다. 접두사는 컨테이너 이름의 접두사, 완전한 컨테이너 이름 또는 Blob 이름 접두사가 뒤에 오는 완전한 컨테이너 이름일 수 있습니다.
   
   다음 표에는 유효한 Blob 경로의 예가 있습니다.
   
   | 선택기 | Blob 경로 | 설명 |
   | --- | --- | --- |
   | 시작 단어 |/ |저장소 계정의 모든 Blob을 내보냄 |
   | 시작 단어 |/$root/ |루트 컨테이너의 모든 Blob을 내보냄 |
   | 시작 단어 |/book |접두사 **book** |
   | 시작 단어 |/music/ |컨테이너 **music** |
   | 시작 단어 |/music/love |접두사 **love**로 시작하는 컨테이너 **music**의 모든 Blob을 내보냄 |
   | 같음 |$root/logo.bmp |루트 컨테이너의 Blob **logo.bmp** 를 내보냄 |
   | 같음 |videos/story.mp4 |컨테이너 **videos**의 Blob **story.mp4**를 내보냄 |
   
   처리 중 오류 발생을 방지하려면 이 스크린샷에 표시된 것처럼 유효한 형식의 Blob 경로를 제공해야 합니다.
   
   ![내보내기 작업 만들기 - 3단계](./media/storage-import-export-service/export-job-03.png)

4. 3단계의 반송 정보에서 드롭다운 목록에 있는 운송업체를 선택하고 해당 운송업체와 함께 생성한 유효한 운송업체 계정 번호를 입력합니다. 가져오기 작업이 완료되면 Microsoft는 이 계정을 사용하여 사용자에게 드라이브를 배송합니다. 완전하며 올바른 연락처 이름, 전화 번호, 전자 메일, 주소, 구/군/도시, 우편 번호, 시/도 및 국가/지역을 제공합니다.
   
 5. 요약 페이지에서 Azure DC에 디스크를 배송하는 데 사용할 Azure Data Center 배송지 주소가 제공됩니다. 작업 이름 및 전체 주소가 배송 레이블에 언급되어 있는지 확인합니다. 

6. 요약 페이지에서 확인을 클릭하여 가져오기 작업 만들기를 완료합니다.

7. 디스크를 배송한 후 Azure Portal의 **가져오기/내보내기** 페이지로 이동하고 a) 가져오기 작업을 찾아 클릭한 후 b) **드라이브가 배송되면 작업 상태 및 추적 정보를 업데이트합니다.** 를 클릭합니다. 
     c) 확인란 “배송됨으로 표시”를 선택합니다. d) 운송업체 및 추적 번호를 제공합니다.
    
   작업을 만든 지 2 주 이내에 추적 번호를 업데이트하지 않으면, 작업이 만료됩니다.
   
8. 포털 대시보드에서 작업 진행 상태를 추적할 수 있습니다. 각 작업 상태가 무엇을 의미하는지는 [작업 상태 보기](#viewing-your-job-status)에 대한 이전 섹션에서 확인하세요.

   > [!NOTE]
   > 내보낼 Blob를 하드 드라이브에 복사 시 사용하는 경우 Azure Import/Export 서비스는 Blob의 스냅숏을 생성하고 스냅숏을 복사합니다.
   > 
   > 
 
9. 내보낸 데이터가 있는 드라이브를 받은 후 드라이브에 대해 서비스에서 생성한 BitLocker 키를 보고 복사할 수 있습니다. Azure Portal에서 내보내기 작업으로 이동하고 가져오기/내보내기 탭을 클릭합니다. 목록에서 내보내기 작업을 선택하고 BitLocker 키 옵션을 클릭합니다. BitLocker 키가 아래와 같이 표시됩니다.
   
   ![내보내기 작업의 BitLocker 키 보기](./media/storage-import-export-service/export-job-bitlocker-keys.png)

아래 FAQ 섹션은 이 서비스를 사용하면서 고객들이 가장 일반적으로 질문하는 내용이므로 확인하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

**Azure Import/Export 서비스를 사용하여 Azure File Storage를 복사할 수 있나요?**

예, Azure Import/Export 서비스는 Azure File Storage로 가져오기를 지원합니다. 지금은 Azure 파일의 내보내기는 지원하지 않습니다.

**Azure Import/Export 서비스를 CSP 구독에 사용할 수 있나요?**

Azure Import/Export 서비스는 CSP 구독을 지원합니다.

**가져오기 작업에서 드라이브 준비 단계를 건너뛰거나 복사하지 않고 드라이브를 준비할 수 있나요?**

데이터를 가져오기 위해 제공하려는 모든 드라이브는 Azure WAImportExport 도구를 사용하여 준비되어야 합니다. WAImportExport 도구를 사용하여 데이터를 드라이브에 복사해야 합니다.

**내보내기 작업을 만들 때 디스크를 준비해야 하나요?**

아니요, 하지만 일부 사전 검사를 수행하는 것이 좋습니다. WAImportExport 도구의 PreviewExport 명령을 사용하여 필요한 디스크 수를 확인합니다. 자세한 내용은 [내보내기 작업에 대한 드라이브 사용량 미리 보기](https://msdn.microsoft.com/library/azure/dn722414.aspx)를 참조하세요. 사용하고자 하는 드라이브의 크기에 따라 선택한 Blob에 대한 드라이브 사용량을 미리볼 수 있습니다. 또한 내보내기 작업에 대해 제공될 하드 드라이브에서 읽거나/쓸 수 있는지 확인합니다.

**지원 요구 사항에 맞지 않는 HDD를 실수로 보내면 어떻게 됩니까?**

Azure 데이터 센터에서는 지원 요구 사항에 맞지 않는 드라이브를 사용자에게 반환합니다. 패키지의 드라이브 중 일부만 지원 요구 사항에 맞는 경우 해당 드라이브는 처리되며 요구 사항에 맞지 않는 드라이브는 사용자에게 반환됩니다.

**작업을 취소할 수 있습니까?**

작업 상태가 만드는 중 또는 발송 중인 경우 작업을 취소할 수 있습니다.

**Azure Portal에서 완료된 작업의 상태는 얼마나 오랫동안 볼 수 있나요?**

완료된 작업의 상태는 최대 90일 동안 볼 수 있습니다. 90일이 지나면 완료된 작업이 삭제됩니다.

**10개가 넘는 드라이브를 가져오거나 내보내려면 어떻게 해야 합니까?**

Import/Export 서비스의 경우 하나의 가져오기 또는 내보내기 작업이 단일 작업에서 드라이브를 10개만 참조할 수 있습니다. 10개가 넘는 드라이브를 발송하려면 여러 개의 작업을 만듭니다. 동일한 작업과 연결된 드라이브는 동일한 패키지에 함께 발송되어야 합니다.
Microsoft는 데이터 용량이 여러 디스크 가져오기 작업에 걸쳐 있는 경우 지침과 지원을 제공합니다. 자세한 내용은 bulkimport@microsoft.com에게 문의하세요.

**서비스가 드라이브를 반환하기 전에 포맷하나요?**

번호 모든 드라이브는 BitLocker로 암호화되어 있습니다.

**가져오기/내보내기 작업에 사용할 드라이브를 Microsoft에서 구입할 수 있습니까?**

번호 가져오기 및 내보내기 작업 모두에 대해 사용자가 자체 드라이브를 발송해야 합니다.

** 이 서비스를 통해 가져오는 데이터에 액세스하는 방법 **

Azure Portal을 사용하거나 Storage Explorer라는 독립 실행형 도구를 사용하여 Azure Storage 계정으로 데이터에 액세스할 수 있습니다. https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**가져오기 작업 완료 후 저장소 계정에서 데이터는 어떻게 표시되나요? 디렉터리 계층 구조가 유지되나요?**

가져오기 작업을 위해 하드 드라이브를 준비할 때 대상은 데이터 집합 CSV의 DstBlobPathOrPrefix 필드로 지정됩니다. 이는 하드 드라이브로부터 데이터가 복사되는 저장소 계정의 대상 컨테이너입니다. 이 대상 컨테이너 내에 하드 드라이브의 폴더에 대해 가상 디렉터리가 만들어지며 파일에 대해 Blob이 만들어집니다. 

**저장소 계정에 이미 있는 파일이 드라이브에 있는 경우 서비스가 저장소 계정에 있는 기존 Blob 또는 파일을 덮어쓰나요?**

드라이브를 준비할 때 데이터 집합 CSV 파일에 있는 Disposition:<rename|no-overwrite|overwrite>라는 필드를 사용하여 대상 파일을 덮어쓰거나 무시해야 하는지를 지정할 수 있습니다. 기본적으로 서비스는 기존 Blob 또는 파일을 덮어쓰지 않고 새 파일의 이름을 변경합니다.

**WAImportExport 도구는 32비트 운영 체제와 호환되나요?**
번호 WAImportExport 도구는 64비트 Windows 운영 체제에서만 호환됩니다. 지원되는 OS 버전의 전체 목록은 [필수 구성 요소](#pre-requisites) 의 운영 체제 섹션을 참조하세요.

**하드 디스크 드라이브 외에 패키지에 포함해야 하는 다른 항목이 있나요?**

하드 드라이브만 배송하고 전원 공급 장치 케이블이나 USB 케이블과 같은 품목은 포함하지 마세요.

**FedEx 또는 DHL을 사용하여 드라이브를 발송해야 하나요?**

FedEx, DHL, UPS 또는 US 우편 서비스와 같이 알려진 모든 운송업체를 사용하여 데이터 센터에 드라이브를 발송할 수 있습니다. 그러나 데이터 센터에서 사용자에게 드라이브를 다시 배송하는 경우 미국 및 유럽에서는 FedEx 계정 번호를, 오스트레일리아 및 아시아 지역에서는 DHL 계정 번호를 제공해야 합니다.

**국제적으로 드라이브를 발송하는 데 제한 사항이 있나요?**

배송하는 실제 미디어는 국경을 지나야 할 수 있습니다. 실제 미디어와 데이터를 관련 법률에 따라 가져오거나 내보내도록 해야 합니다. 실제 미디어를 배송하기 전에 관리자에게 미디어 및 데이터를 확인된 데이터 센터에 합법적으로 배송할 수 있는지 확인하세요. 이렇게 하면 해당 품목이 시기 적절하게 Microsoft에 도착될 수 있습니다.

**작업을 만들 때 배송지 주소가 저장소 계정 위치와는 다른 위치입니다. 어떻게 해야 하나요?**

일부 저장소 계정 위치는 대체 배송 위치로 매핑됩니다. 이전에 사용 가능했던 배송 위치 또한 일시적으로 대체 위치로 매핑될 수 있습니다. 드라이브를 발송하기 전에 작업을 만들 때 제공한 배송지 주소를 항상 확인합니다.

**드라이브를 발송할 때 운송업체에서 데이터 센터 연락처 주소 및 전화 번호를 요청합니다. 무엇을 제공해야 하나요?**

전화 번호와 DC 주소는 작업을 만드는 동안 제공됩니다.

**Azure Import/Export 서비스를 사용하여 PST 사서함 및 SharePoint 데이터를 O365에 복사할 수 있나요?**

[Office 365에 PST 파일 또는 SharePoint 데이터 가져오기](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx)를 참조하세요.

**Azure Import/Export 서비스를 사용하여 오프라인 백업을 Azure Backup 서비스에 복사할 수 있나요?**

[Azure Backup의 오프라인 Backup 워크플로](../../backup/backup-azure-backup-import-export.md)를 참조하세요.

**각 배송에 허용되는 최대 HDD 수는 몇 개인가요?**

각 배송에 허용되는 HDD 수에 대한 제한은 없으며, 디스크가 여러 작업에 속하는 경우 a) 해당 작업 이름으로 디스크에 레이블을 지정하고, b) 추적 번호에 접미사 -1, -2 등을 추가하여 작업을 업데이트하는 것이 좋습니다.
  
**디스크 Import/Export에서 지원되는 최대 블록 Blob 및 페이지 Blob 크기는 무엇인가요?**

최대 블록 Blob 크기는 약 4.768TB 또는 5,000,000MB입니다.
최대 페이지 Blob 크기는 1TB입니다.

**디스크 Import/Export는 AES 256 암호화를 지원하나요?**

Azure Import/Export 서비스는 기본적으로 AES 128 bitlocker 암호화로 암호화되지만 데이터를 복사하기 전에 bitlocker를 사용하여 수동으로 암호화하여 AES 256으로 늘릴 수 있습니다. 

[WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip)을 사용하는 경우 샘플 명령은 아래와 같습니다.
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
[WAImportExport 도구](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip)를 사용하는 경우 "AlreadyEncrypted"를 지정하고 드라이브 집합 CSV에 해당 키를 제공합니다.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>다음 단계

* [WAImportExport 도구 설정](storage-import-export-tool-how-to.md)
* [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)
* [Azure Import/Export REST API 샘플](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

