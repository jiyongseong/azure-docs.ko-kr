---
title: "Azure PowerShell 샘플 - App Service | Microsoft Docs"
description: "Azure PowerShell 샘플 - App Service"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: ba2bd2b185c395e54f2f085317a424a2aa1b4421
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2018
---
# <a name="azure-powershell-samples"></a>Azure PowerShell 샘플

다음 테이블은 Azure PowerShell을 사용하여 빌드된 PowerShell 스크립트에 대한 링크를 포함합니다.

| | |
|-|-|
|**앱 만들기**||
| [GitHub의 배포를 사용하여 웹앱 만들기](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| GitHub의 코드를 가져오는 Azure 웹앱을 만듭니다. |
| [GitHub의 연속 배포를 사용하여 웹앱 만들기](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 연속적으로 GitHub의 코드를 배포하는 Azure 웹앱을 만듭니다. |
| [FTP를 사용하여 웹앱 만들기 및 코드 배포](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | FTP를 사용하여 로컬 디렉터리에서 Azure 웹앱을 만들고 파일을 업로드합니다. |
| [웹앱 만들기 및 로컬 Git 리포지토리의 코드 배포](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure 웹앱을 만들고 로컬 Git 리포지토리의 코드 푸시를 구성합니다. |
| [웹앱 만들기 및 스테이징 환경에 코드 배포](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 코드 변경 내용을 준비하기 위해 배포 슬롯을 사용하여 Azure 웹앱을 만듭니다. |
|**앱 구성**||
| [웹앱에 사용자 지정 도메인 매핑](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure 웹앱을 만들고 여기에 사용자 지정 도메인 이름을 매핑합니다. |
| [웹앱에 사용자 지정 SSL 인증서 바인딩](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure 웹앱을 만들고 여기에 사용자 지정 도메인 이름의 SSL 인증서를 바인딩합니다. |
|**앱 크기 조정**||
| [수동 웹앱 크기 조정](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure 웹앱을 만들고 2개의 인스턴스로 확장합니다. |
| [고가용성 아키텍처로 전 세계 웹앱 크기 조정](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 두 개의 지역에 두 개의 Azure 웹앱을 만들고 Azure Traffic Manager를 사용하여 단일 끝점을 통해 사용할 수 있습니다. |
|**리소스에 앱 연결**||
| [SQL Database에 웹앱 연결](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure 웹앱 및 SQL Database를 만들고 앱 설정에 데이터베이스 연결 문자열을 추가합니다. |
| [저장소 계정에 웹앱 연결](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure 웹앱 및 저장소 계정을 만들고 앱 설정에 저장소 연결 문자열을 추가합니다. |
|**백업 및 복원 앱**||
| [웹앱 백업](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure 웹앱을 만들고 이에 대한 일회성 백업을 만듭니다. |
| [웹앱에 대한 예약된 백업 만들기](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure 웹앱을 만들고 이에 대한 예약된 백업을 만듭니다. |
| [웹앱에 대한 백업 삭제](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 웹앱에 대한 기존 백업을 삭제합니다. |
|**앱 모니터링**||
| [웹 서버 로그로 웹앱 모니터링](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure 웹앱을 만들고 로깅을 사용하도록 설정하고 로그를 로컬 컴퓨터에 다운로드합니다. |
| | |
