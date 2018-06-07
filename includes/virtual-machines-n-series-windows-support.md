---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/03/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 689e45f96ed5e7aaea3aecd1193e9cd58f10e80d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2018
---
## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>NC, NCv2, NCv3 및 ND 시리즈 - NVIDIA Tesla(CUDA) 드라이버

다음 표의 드라이버 다운로드 링크는 게시 시점의 최신 링크입니다. 최신 드라이버에 대해서는 [NVIDIA](http://www.nvidia.com/) 웹 사이트를 참조하세요.

> [!TIP]
> Windows Server VM에서 수동 CUDA 드라이버를 설치하는 대신, Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) 이미지를 배포할 수 있습니다. Windows Server 2016용 DSVM 에디션은 NVIDIA CUDA 드라이버, CUDA 심층 신경망 네트워크 라이브러리 및 기타 도구를 사전 설치합니다.


| OS | 드라이버 |
| -------- |------------- |
| Windows Server 2016 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2016-international.exe)(.exe) |
| Windows Server 2012 R2 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2008-2012r2-64bit-international.exe)(.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>NV 시리즈 - NVIDIA GRID 드라이버

Microsoft는 NV VM용 NVIDIA GRID 드라이버 설치 관리자를 재배포합니다. Azure NV VM에 이 GRID 드라이버만 설치하십시오. 이 드라이버에는 Azure의 GRID 가상 GPU 소프트웨어에 대한 라이선스가 포함됩니다.

| OS | 드라이버 |
| -------- |------------- |
| Windows Server 2016<br/><br/>윈도우 10 | [GRID 6.0(391.03)](https://go.microsoft.com/fwlink/?linkid=836843)(.exe) |
| Windows Server 2012 R2 | [GRID 6.0(391.03)](https://go.microsoft.com/fwlink/?linkid=836844)(.exe)  |