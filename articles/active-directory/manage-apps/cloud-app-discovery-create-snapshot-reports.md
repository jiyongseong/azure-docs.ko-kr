---
title: Azure Active Directory의 Cloud App Discovery 스냅숏 만들기 | Microsoft Docs
description: Cloud App Discovery를 사용한 응용 프로그램 찾기 및 관리, 이점 및 작동 방식에 대한 정보를 제공합니다.
services: active-directory
keywords: Cloud App Discovery, 응용 프로그램 관리
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/22/2017
ms.author: barbkess
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: 68585edad6e7d1b6b21a48f1cf4242875cea538a
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>Cloud App Discovery 스냅숏 보고서 만들기

자동 로그 수집기를 설정하기 전에 로그를 수동으로 업로드하여 Cloud App Security가 구문 분석하게 합니다.  아직 로그가 없고 로그의 형태에 관한 샘플을 보려면 아래 절차를 통해 샘플 로그 파일을 다운로드하여 로그가 대략 어떻게 보이는지 파악합니다.

## <a name="to-create-a-snapshot-report"></a>스냅숏 보고서를 만들려면

1. 조직 내 사용자가 인터넷에 액세스하기 위해 통과하는 방화벽과 프록시 서버에서 로그 파일을 수집합니다. 조직 내 사용자 활동의 특징을 나타내는 최대 트래픽 시간 중에 로그를 수집합니다.
2. [Cloud App Security 메뉴 모음](https://portal.cloudappsecurity.com)에서 **탐색**, **스냅숏 보고서 만들기**를 차례로 선택합니다.
  
  ![새 스냅숏 보고서 만들기](./media/cloud-app-discovery-create-snapshot-reports/create-snapshot-command.png)
3. **보고서 이름**과 **설명**을 입력합니다.
    
  ![새 스냅숏 보고서](./media/cloud-app-discovery-create-snapshot-reports/create-snapshot-form.png)
4. 로그 파일을 업로드한 **데이터 원본**을 선택합니다.
5. 로그 형식이 다운로드할 수 있는 샘플에 따라 올바른 형식으로 작성되었는지 확인합니다. **보기 및 확인**을 클릭하고 **샘플 로그 다운로드**를 클릭합니다. 그런 다음 로그를 제공된 샘플과 비교하여 호환되는지 확인합니다.
  
  ![로그 형식 확인](./media/cloud-app-discovery-create-snapshot-reports/create-snapshot-verify.png)
  >  [!NOTE]
  > FTP 샘플 형식은 스냅숏과 자동 업로드에서 지원되며 syslog는 자동 업로드에서만 지원됩니다. 샘플 로그를 다운로드하면 샘플 FTP 로그를 다운로드합니다.
6. 업로드할 **트래픽 로그**를 선택합니다. 한 번에 최대 20개의 파일을 업로드할 수 있습니다. 압축된 파일도 지원합니다.
  
7. **만들기**를 클릭합니다. 업로드가 완료되면 구문 분석과 분석 작업에 다소 시간이 소요될 수 있습니다. 이 경우 준비가 되면 Cloud App Discovery가 이메일 알림을 보냅니다.

8. **스냅숏 보고서 관리**를 선택하고 스냅숏 보고서를 선택합니다.
  
  ![스냅숏 보고서 관리](./media/cloud-app-discovery-create-snapshot-reports/create-snapshot-manage.png)

## <a name="next-steps"></a>다음 단계

* [Azure AD에서 Cloud App Discovery 사용 시작](cloud-app-discovery.md)
* [연속 보고를 위한 자동 로그 업로드 구성](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [사용자 지정 로그 구문 분석기 사용](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
