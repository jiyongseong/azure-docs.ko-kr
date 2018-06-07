---
title: "다운로드 하 고 압축 Azure 스택 개발 키트 (ASDK) | Microsoft Docs"
description: "다운로드 하 여 Azure 스택 개발 키트 (ASDK)을 추출 하는 방법을 설명 합니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0cf389c9443a9cff477b884c277d72de27769afc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>다운로드 하 고 Azure 스택 개발 키트 (ASDK) 추출
을 개발 키트 호스트 컴퓨터가 ASDK를 설치 하기 위한 기본 요구 사항을 충족 하는지 확인 한 후 다음 단계를 다운로드 하 여 Cloudbuilder.vhdx 가져오려는 ASDK 배포 패키지를 추출할입니다.

## <a name="download-the-asdk"></a>ASDK 다운로드
1. 다운로드를 시작 하기 전에 컴퓨터가 다음 전제 조건을 충족 하는지 확인 합니다.

  - 컴퓨터에 적어도 60GB의 디스크 공간을 4 개의 개별와 동일한 논리 하드 드라이브에서 사용할 수 있는 뿐만 아니라 운영 체제 디스크에 있어야 합니다.
  - [.NET framework 4.6 (또는 이후 버전)](https://aka.ms/r6mkiy) 설치 해야 합니다.

2. [시작 페이지로 이동](https://azure.microsoft.com/overview/azure-stack/try/?v=try) 수 다운로드 Azure 스택 개발 키트, 프로그램 세부 정보를 제공 하 고 있는 클릭 **전송**합니다.
3. 다운로드 및 실행의 [Azure 스택 개발 키트용 배포 검사기](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) 필수 구성 요소 검사기 스크립트입니다. 이 독립 실행형 스크립트 Azure 스택 개발 키트에 대 한 설치 프로그램에서 수행 되는 필수 구성 요소 확인을 거칩니다. Azure 스택 개발 키트에 대 한 큰 패키지를 다운로드 하기 전에 하드웨어 및 소프트웨어 요구 사항을 충족 하는지 확인 하는 방법을 제공 합니다.
4. 아래 **소프트웨어 다운로드**, 클릭 **Azure 스택 개발 키트**합니다.

  > [!NOTE]
  > ASDK 다운로드 (AzureStackDevelopmentKit.exe)는 약 10GB입니다.

## <a name="extract-the-asdk"></a>ASDK 추출
1. 다운로드가 완료 된 후 클릭 **실행** ASDK 자동 압축 풀기 (AzureStackDevelopmentKit.exe)를 시작할 수 있습니다.
2. 검토 하 고에서 표시 된 사용권 계약에 동의 **사용권 계약** 클릭 한 다음 자동 압축 풀기 마법사의 페이지 **다음**합니다.
3. 에 표시 된 개인정보취급방침 정보를 검토는 **중요 알림** 클릭 한 다음 자동 압축 풀기 마법사의 페이지 **다음**합니다.
4. Azure 스택 설치 파일에서 압축을 풀에 대 한 위치를 선택 하는 **대상 위치 선택** 클릭 한 다음 자동 압축 풀기 마법사의 페이지 **다음**합니다. 기본 위치는 *현재 폴더*\Azure 스택 개발 키트입니다. 
5. 요약 대상 위치를 검토는 **추출 준비가** 을 클릭 한 다음 자동 압축 풀기 마법사의 페이지 **추출** CloudBuilder.vhdx (대략 25 GB)를 추출 하 고 ThirdPartyLicenses.rtf 파일입니다. 이 프로세스는 완료 하려면 다소 시간이 걸립니다.
6. 복사 하거나 ASDK 호스트 컴퓨터에서 CloudBuilder.vhdx 파일 (C:\CloudBuilder.vhdx) C:\ 드라이브의 루트로 이동 합니다.

> [!NOTE]
> 파일을 추출한 후 삭제할 수 있습니다는 합니다. EXE 및 합니다. BIN 파일 하드 디스크 공간을 복구 합니다. 또는를 백업할 수 있습니다 이러한 파일을 하는 ASDK 다시 배포 하는 경우 파일을 다시 다운로드할 필요가 없습니다.


## <a name="next-steps"></a>다음 단계
[ASDK 호스트 컴퓨터를 준비 합니다.](asdk-prepare-host.md)