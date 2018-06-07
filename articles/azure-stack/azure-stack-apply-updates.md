---
title: "Azure 스택에서 업데이트 적용 | Microsoft Docs"
description: "가져올 통합 Azure 스택 시스템에 대 한 Microsoft 업데이트 패키지를 설치 하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 0f23216c6aced60dc651a0f10179281bc9a29c2c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="apply-updates-in-azure-stack"></a>Azure 스택에서 업데이트 적용

*적용 대상: Azure 스택 시스템 통합*

Azure 스택 연산자로 관리자 포털에서 타일의 업데이트를 사용 하 여 Azure 스택에 대 한 패키지는 Microsoft 업데이트를 적용할 수 있습니다. Microsoft 업데이트 패키지를 다운로드 하 고, Azure 스택으로 패키지 파일을 가져오는 하 고, 다음 업데이트 패키지를 설치 해야 합니다. 

## <a name="download-the-update-package"></a>업데이트 패키지 다운로드

Azure 스택에 대 한 Microsoft 업데이트 패키지를 사용할 수 있는 Azure 스택에서 연결할 수 있는 위치에 패키지를 다운로드 하 고 패키지 콘텐츠를 검토 합니다. 업데이트 패키지는 일반적으로 다음 파일로 구성 됩니다.

- 자동 압축 풀기 *PackageName*.exe 파일입니다. 이 파일 예를 들어 최신 누적 업데이트 Windows Server 용 업데이트에 대 한 페이로드를 포함합니다.   
- 해당 *PackageName*.bin 파일입니다. 이러한 파일에 연결 된 페이로드에 대 한 압축 제공는 *PackageName*.exe 파일입니다. 
- Metadata.xml 파일입니다. 이 파일 예를 들어 게시자, 이름, 필수 구성 요소, 크기 및 지원 경로 URL 업데이트에 대 한 중요 한 정보를 포함합니다.

## <a name="import-and-install-updates"></a>가져오기 및 업데이트 설치

다음 절차에 가져오고 관리자 포털에서 업데이트 패키지를 설치 하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 모든 유지 관리 작업의 사용자에 게 알림 및 예약 하 일반 유지 관리 기간 동안 업무 외 시간을 가능한 것이 좋습니다. 유지 관리 작업 사용자 워크 로드와 포털 작업에 영향을 줄 수 있습니다.

1. 관리자 포털에서 선택 **더 많은 서비스**합니다. 그런 다음는 **데이터 + 저장소** 범주를 **저장소 계정은**합니다. (필터 상자에 입력을 시작 하거나 **저장소 계정은**를 선택 합니다.)

    ![포털에서 저장소 계정을 찾을 수 있는 위치를 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. 필터 상자에 입력 **업데이트**, 선택는 **updateadminaccount** 저장소 계정입니다.

    ![Updateadminaccount에 대 한 검색 하는 방법을 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. 저장소 세부 정보를 아래에서 계정 **서비스**선택, **Blob**합니다.
 
    ![저장소 계정의 Blob에 가져오는 방법을 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. 아래 **Blob 서비스**선택, **+ 컨테이너** 컨테이너를 만듭니다. 이름을 입력 하십시오 (예를 들어 *업데이트 1709*)를 선택한 후 **확인**합니다.
 
     ![저장소 계정에서 컨테이너를 추가 하는 방법을 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. 컨테이너를 만든 후 컨테이너 이름을 클릭 한 다음 클릭 **업로드** 를 컨테이너에 패키지 파일을 업로드 합니다.
 
    ![패키지 파일을 업로드 하는 방법을 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. 아래 **업로드 blob**, 폴더 아이콘을 클릭 하 여 업데이트 패키지.exe 파일을 찾은 및 클릭 **열려** 파일 탐색기 창에서.
  
7. 아래 **업로드 blob**, 클릭 **업로드**합니다. 
 
    ![각 패키지 파일을 업로드할 수 있는 위치를 보여 줍니다.](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. 단계 6과 7을 반복 하는 *PackageName*.bin 및 Metadata.xml 파일입니다. 포함 하는 경우 보조 Notice.txt 파일을 가져오지 않습니다.
9. 을 완료 한 후에 알림 (종 모양 아이콘 포털의 오른쪽 위 모퉁이)을 검토할 수 있습니다. 알림을은 업로드가 완료 된 것을 나타내야 합니다. 
10. 대시보드의 타일의 업데이트를 다시 탐색 합니다. 타일은 업데이트를 사용할 수 있음을 나타내야 합니다. 새로 추가 된 업데이트 패키지를 검토 하 여 타일을 클릭 합니다.
11. 업데이트를 설치 하려면로 표시 된 패키지를 선택 **준비** 패키지를 마우스 오른쪽 단추로 클릭 하 고 선택 **지금 업데이트**, 하거나 클릭 하 고 **지금 업데이트** 상단 근처에 있는 작업 .
12. 설치 업데이트 패키지를 클릭할 때의 상태를 볼 수 있습니다는 **업데이트 실행 정보** 영역입니다. 여기에서 클릭할 수도 있습니다 **전체 로그를 다운로드** 로그 파일을 다운로드 합니다.
13. 업데이트가 완료 되 면 업데이트 타일에는 업데이트 된 Azure 스택 버전을 표시 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 스택 개요에 대 한 업데이트 관리](azure-stack-updates.md)
- [정책 서비스는 azure 스택](azure-stack-servicing-policy.md)
