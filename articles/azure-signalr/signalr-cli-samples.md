---
title: Azure CLI 샘플 - Azure SignalR Service | Microsoft Docs
description: Azure CLI 샘플 - Azure SignalR Service
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 6e76ee89160c84ff0f1033590d14c288f023f201
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cli-samples"></a>Azure CLI 샘플

다음 표에는 Azure CLI를 사용하는 Azure SignalR Service용 bash 스크립트에 대한 링크가 포함되어 있습니다.

| | |
|-|-|
|**만들기**||
| [새 SignalR Service 및 리소스 그룹 만들기](scripts/signalr-cli-create-service.md) | 임의의 이름이 있는 새 리소스 그룹에 새 Azure SignalR Service 리소스를 만듭니다.  |
|**통합**||
| [SignalR을 사용하도록 구성된 새 SignalR Service 및 Web App 만들기](scripts/signalr-cli-create-with-app-service.md) | 임의의 이름이 있는 새 리소스 그룹에 새 Azure SignalR Service 리소스를 만듭니다. 또한 SignalR Service를 사용하는 ASP.NET Core Web App을 호스팅하기 위한 새 Web App 및 App Service 계획을 추가합니다. 웹앱은 새 SignalR Service 리소스에 연결하는 앱 설정으로 구성됩니다. |
| [SignalR 및 GitHub OAuth를 사용하도록 구성된 새 SignalR Service 및 Web App 만들기](scripts/signalr-cli-create-with-app-service-github-oauth.md) | 임의의 이름이 있는 새 리소스 그룹에 새 Azure SignalR Service 리소스를 만듭니다. 또한 SignalR Service를 사용하는 ASP.NET Core Web App을 호스팅하기 위한 새 Azure Web App 및 호스팅 계획을 추가합니다. 웹앱은 새 SignalR Service 리소스의 연결 문자열에 대한 앱 설정과 [인증 자습서](signalr-authenticate-oauth.md)에서 설명한 대로 [GitHub 인증](https://developer.github.com/v3/guides/basics-of-authentication/)을 지원하는 클라이언트 비밀로 구성됩니다. 웹앱은 로컬 git 리포지토리 배포 원본도 사용하도록 구성됩니다. |
| | |
