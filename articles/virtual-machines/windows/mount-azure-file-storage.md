---
title: "Azure Windows VM에서 Azure File Storage 탑재 | Microsoft Docs"
description: "Azure File Storage를 사용하여 클라우드에 파일을 저장하고 Azure VM(Virtual Machine)에서 클라우드 파일 공유를 탑재합니다."
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 8d537bdc882487784baef9f693e4677c76d3bd8d
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2018
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Windows VM과 Azure 파일 공유 사용 

VM에서 파일을 저장하고 액세스하는 방법으로 Azure 파일 공유를 사용할 수 있습니다. 예를 들어 모든 VM을 공유하려는 스크립트 또는 응용 프로그램 구성 파일을 저장할 수 있습니다. 이 문서에서는 Azure 파일 공유를 만들고 탑재하는 방법과 파일을 업로드 및 다운로드하는 방법을 설명합니다.

## <a name="connect-to-a-file-share-from-a-vm"></a>VM에서 파일 공유에 연결

이 섹션에서는 연결하려는 파일 공유가 이미 있다고 가정합니다. 하나 만들어야 하는 경우 이 문서의 뒷부분에 나오는 [파일 공유 만들기](#create-a-file-share)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **Storage 계정**을 클릭합니다.
3. 저장소 계정 선택
4. **개요** 페이지에 있는 **서비스**에서 **파일**을 선택합니다.
5. 파일 공유를 선택하거나 **+ 파일 공유**를 클릭하여 사용할 새 파일 공유를 만듭니다.
6. **연결**을 클릭하여 Windows 또는 Linux에서 파일 공유를 탑재하는 명령줄 구문을 보여 주는 페이지를 엽니다.
7. **드라이브 문자**에서 드라이브를 식별하는 데 사용할 문자를 선택합니다.
8. 사용할 구문을 선택하고 오른쪽의 [복사] 단추를 선택하여 클립보드에 복사합니다. 쉽게 액세스할 수 있는 위치에 붙여넣습니다. 
8. VM에 연결하고 명령 프롬프트를 엽니다.
9. 편집된 연결 구문에 붙여넣고 **Enter** 키를 누릅니다.
10. 연결이 생성되면 **명령이 성공적으로 완료되었습니다.**라는 메시지가 표시됩니다.
11. 해당 드라이브로 전환되도록 드라이브 문자를 입력하여 연결을 확인하고, **dir**를 입력하여 파일 공유의 내용을 확인합니다.



## <a name="create-a-file-share"></a>파일 공유 만들기 
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **Storage 계정**을 클릭합니다.
3. 저장소 계정 선택
4. **개요** 페이지에 있는 **서비스**에서 **파일**을 선택합니다.
5. 파일 서비스 페이지에서 **+ 파일 공유**를 클릭합니다.
6. 파일 공유 이름을 입력합니다. 파일 공유 이름은 소문자, 숫자 및 단일 하이픈을 사용할 수 있습니다. 이름은 하이픈으로 시작할 수 없으며 여러 개 하이픈을 연속하여 사용할 수 없습니다. 
7. 최대 5120GB까지 파일 크기에 관한 제한을 입력합니다.
8. **확인**을 클릭하여 파일 공유를 만듭니다.
   
## <a name="upload-files"></a>파일 업로드
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **Storage 계정**을 클릭합니다.
3. 저장소 계정 선택
4. **개요** 페이지에 있는 **서비스**에서 **파일**을 선택합니다.
5. 파일 공유를 선택합니다.
6. **업로드**를 클릭하여 **파일 업로드** 페이지를 엽니다.
7. 폴더 아이콘을 클릭하여 업로드할 파일에 대한 로컬 파일 시스템을 찾아봅니다.   
8. **업로드**를 클릭하여 파일 공유에 파일을 업로드합니다.

## <a name="download-files"></a>파일 다운로드
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **Storage 계정**을 클릭합니다.
3. 저장소 계정 선택
4. **개요** 페이지에 있는 **서비스**에서 **파일**을 선택합니다.
5. 파일 공유를 선택합니다.
6. 파일 하나를 마우스 오른쪽 단추로 클릭하고 **다운로드**를 선택하여 로컬 컴퓨터에 다운로드합니다.
   

## <a name="next-steps"></a>다음 단계

또한 PowerShell을 사용하여 파일 공유를 만들고 관리할 수 있습니다. 자세한 내용은 [Windows에서 Azure File storage 시작](../../storage/files/storage-dotnet-how-to-use-files.md)을 참조하세요.
