---
title: Azure Stack 가상 머신 소개
description: Azure 스택 가상 컴퓨터에 알아보기
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 967fcb86c1bf0c85517bc13c2066ed32e8fa28d9
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604134"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Azure Stack 가상 머신 소개

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 한 가지 유형의 요청 시, 확장 가능한 컴퓨팅 리소스와 가상 컴퓨터 (Vm)를 제공합니다. 다른 선택 사항 보다 더 많은 컴퓨팅 환경 제어 해야 할 때 VM을 선택할 수 있습니다. VM을 만들기 전에이 문서에서는 자세히 설명 합니다.

Azure 스택 VM 클러스터 또는 개별 컴퓨터를 관리할 필요 없이 가상화의 유연성을 제공 합니다. 그러나 여전히 해야 구성, 패치, 및를 실행 하는 소프트웨어를 설치 하는 등의 작업을 수행 하 여 VM을 유지 합니다.

다양 한 방법으로 Azure 스택 가상 컴퓨터를 사용할 수 있습니다. 예: 

- **개발 및 테스트**  
    Azure 스택 Vm 코드 하는 데 필요한 특정 구성으로는 컴퓨터를 만들고 응용 프로그램을 테스트 하는 빠르고 쉬운 방법을 제공 합니다.

- **클라우드에서 응용 프로그램**  
    응용 프로그램에 대 한 수요가 변동 수 때문에 좋을 수 있습니다 경제 스택에서 Azure VM에서 실행 되도록 합니다. 필요할 경우 여분의 VM에 대해 비용을 지불하고, 그렇지 않은 경우에는 해당 VM을 종료합니다.

- **확장 된 데이터 센터**  
    스택 Azure 가상 네트워크의 가상 컴퓨터 또는 Azure에 조직의 네트워크에 쉽게 연결할 수 있습니다.

응용 프로그램에 사용 수직 확장 또는 요구를 충족 하는 데 필요한 무엇이를 확장할 수 있는 Vm입니다.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM을 만들기 전의 고려 사항

항상 다양 한 디자인 고려 사항이 Azure 스택의 응용 프로그램 인프라를 빌드할 때 있습니다. VM의 이러한 측면은 인프라를 만들기를 시작 하기 전에 고려해 야 하는 것이 중요 합니다.

- 응용 프로그램 리소스의 이름입니다.
- VM의 크기입니다.
- 만들 수 있는 Vm의 최대 수입니다.
- VM이 실행 하는 운영 체제.
- 시작 된 후 VM의 구성입니다.
- Vm 관련된 리소스

### <a name="naming"></a>이름 지정

가상 컴퓨터에 할당 된 이름 및 운영 체제의 일부분으로 구성 된 컴퓨터 이름으로 바꿉니다. VM 이름은 최대 15자로 제한됩니다.

Azure 스택을 사용 하 여 운영 체제 디스크를 만드는 경우 컴퓨터 이름 및 가상 컴퓨터 이름이 동일 합니다. 업로드 하 고 이전에 구성 된 운영 체제가 포함 된 사용자 고유의 이미지 사용 및 사용 하 여 가상 컴퓨터를 만드는 경우 이름이 다를 수 있습니다. 사용자 고유의 이미지 파일을 업로드 하는 경우 운영 체제에서 컴퓨터 이름을 확인 하 고 가상 컴퓨터를 이름을 모범 사례와 동일 합니다.

### <a name="vm-size"></a>VM 크기

사용 하는 VM의 크기는 실행 하려는 작업에 의해 결정 됩니다. 그런 다음 선택하는 크기는 처리 성능, 메모리 및 저장소 용량 등의 요소를 결정합니다. Azure 스택 다양 한 다양 한 유형의 사용을 지원 하기 위해 크기를 제공 합니다.

### <a name="vm-limits"></a>VM 제한

구독 기본 할당량 한도 프로젝트에 대 한 많은 Vm의 배포 영향을 줄 수 있는 위치에 있습니다. 구독별 기준으로 현재 제한은 지역당 20대의 VM입니다.

### <a name="operating-system-disks-and-images"></a>운영 체제 디스크 및 이미지

가상 머신은 VHD(가상 하드 디스크)를 사용하여 해당 OS(운영 체제) 및 데이터를 저장합니다. VHD는 OS를 설치하도록 선택할 수 있는 이미지에도 사용됩니다.
Azure 스택 다양 한 버전 및 운영 체제의 종류와 함께 사용할 마켓플레이스를 제공 합니다. 마켓플레이스 이미지는 이미지 게시자, 제품, sku 및 버전 (일반적으로 버전으로 지정 됩니다 최신..)으로 식별 됩니다.

다음 표에서 이미지에 대 한 정보를 찾을 수 있도록 몇 가지 방법을 보여 줍니다.

|방법|설명|
|---------|---------|
|Azure 스택 포털|사용할 이미지를 선택할 때 사용자에 적합한 값이 자동으로 지정됩니다.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API     |[이미지 게시자 나열](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[이미지 제안 나열](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[이미지 Sku를 나열 합니다.](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

업로드 하 여 사용자 고유의 이미지를 사용 하 여 선택할 수 있습니다. 이렇게 하면 게시자 이름, 제안 및 sku 사용 되지 않습니다.

### <a name="extensions"></a>확장

VM 확장 사후 배포 구성 및 자동화 된 작업을 통해 사용자 VM 추가 기능을 제공합니다.
다음과 같은 일반 작업은 확장을 사용하여 수행할 수 있습니다.

- **사용자 지정 스크립트를 실행 합니다.**  
    사용자 지정 스크립트 확장 VM에서 VM 프로 비전 될 때 스크립트를 실행 하 여 작업 부하를 구성 하도록 도와줍니다.

- **배포 및 구성 관리**  
    PowerShell 필요한 상태 구성 (DSC) 확장을 사용 하면 VM에서 DSC 구성 / 환경 관리를 설정 합니다.

- **진단 데이터 수집**  
    Azure 진단 확장 응용 프로그램의 상태를 모니터링 하는 데 사용할 수 있는 진단 데이터를 수집 하는 VM을 구성 하도록 도와줍니다.

### <a name="related-resources"></a>관련 리소스

다음 표에서 리소스 VM에서 사용 되 고 존재 하거나 VM 만들어질 때 만들어집니다.


|리소스|필수|설명|
|---------|---------|---------|
|리소스 그룹|예|VM은 리소스 그룹에 포함되어야 합니다.|
|Storage 계정|예|가상 하드 디스크를 저장하기 위해 VM에 저장소 계정이 필요합니다.|
|가상 네트워크|예|VM은 가상 네트워크의 구성원이어야 합니다.|
|공용 IP 주소|아니요|원격으로 액세스하기 위해 VM에 할당된 공용 IP 주소가 있을 수 있습니다.|
|Linux|예|네트워크에서 통신하기 위해 VM에 네트워크 인터페이스가 필요합니다.|
|데이터 디스크|아니요|VM은 저장소 기능을 확장하기 위해 데이터 디스크를 포함할 수 있습니다.|

## <a name="create-your-first-vm"></a>첫 번째 VM 만들기

VM을 만드는 여러 가지 옵션이 있습니다. 선택한 사용자의 환경에 따라 달라 집니다.
다음 표에서 VM을 만들기 시작 하는 데는 정보를 제공 합니다.


|방법|문서|
|---------|---------|
|Azure 스택 포털|Azure 스택 포털과 Windows 가상 컴퓨터 만들기<br>[Azure 스택 포털을 사용 하 여 Linux 가상 컴퓨터 만들기](azure-stack-quick-linux-portal.md)|
|템플릿|Azure 스택 퀵 스타트 템플릿 위치에 있습니다.<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Azure 스택에서 PowerShell을 사용 하 여 Windows 가상 컴퓨터 만들기](azure-stack-quick-create-vm-windows-powershell.md)<br>[Azure 스택에서 PowerShell을 사용 하 여 Linux 가상 컴퓨터 만들기](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Azure 스택에서 CLI를 사용 하 여 Windows 가상 컴퓨터 만들기](azure-stack-quick-create-vm-windows-cli.md)<br>[Azure 스택에서 CLI를 사용 하 여 Linux 가상 컴퓨터 만들기](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>VM 관리

브라우저 기반 포털로, Api를 통해 직접 또는 스크립트에 대 한 지원 사용 하 여 명령줄 도구를 사용 하 여 Vm을 관리할 수 있습니다. 수행할 수 있는 몇 가지 일반적인 관리 작업 다음과 같습니다.

- VM에 대 한 정보 가져오기
- VM에 연결
- 가용성 관리
- 백업

### <a name="get-information-about-your-vm"></a>VM에 대 한 정보 가져오기

다음 표에서 보여 줍니다는 몇 가지는 VM에 대 한 정보를 얻을 수 있습니다.


|방법|설명|
|---------|---------|
|Azure 스택 포털|허브 메뉴에서 가상 컴퓨터를 클릭 하 고 목록에서 VM을 선택 합니다. VM에 대 한 페이지에서 값을 설정 하 고 모니터링 되는 메트릭 개요 정보에 액세스할 수 있어야 합니다.|
|Azure PowerShell|Vm을 관리 하는 것은 Azure 및 Azure 스택과 비슷합니다. PowerShell을 사용 하는 방법에 대 한 자세한 내용은 다음 Azure 항목을 참조 합니다.<br>[만들기 및 Azure PowerShell 모듈과 함께 Windows Vm 관리](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|클라이언트 SDK|사용 하 여 C# Vm을 관리 하는 것은 Azure 및 Azure 스택과 비슷합니다. 자세한 내용은 다음 Azure 항목을 참조 합니다.<br>[만들기 및 C#을 사용 하 여 Azure에 Windows Vm 관리](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>VM에 연결

사용할 수는 **연결** VM에 연결 하기 위해 Azure 스택 포털에서 단추입니다.

## <a name="next-steps"></a>다음 단계

- [Azure 스택의 가상 컴퓨터에 대 한 고려 사항](azure-stack-vm-considerations.md)