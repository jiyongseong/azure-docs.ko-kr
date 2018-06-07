---
title: "Linux VM에서 Ruby on Rails 웹 사이트 호스트 | Microsoft Docs"
description: "Azure에서 Linux 가상 머신을 사용하여 Ruby on Rails 기반 웹 사이트를 설정 및 호스트하는 방법에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: fa19f3dc7dded712102d4ba9b66dd4df1bfd20dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Azure VM의 Ruby on Rails 웹 응용 프로그램
이 자습서에서는 Azure에서 Linux 가상 머신을 사용하여 Ruby on Rails 웹 사이트를 호스트하는 방법을 보여줍니다.  

이 자습서의 내용은 Ubuntu Server 14.04 LTS를 사용하여 유효성이 검사되었습니다. 다른 Linux 배포를 사용하는 경우, Rails를 설치하는 단계를 수정해야 합니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Azure VM 만들기
Linux 이미지와 Azure VM을 만들어 시작합니다.

VM을 만들려면 Azure Portal 또는 Azure CLI(명령줄 인터페이스)를 사용할 수 있습니다.

### <a name="azure-portal"></a>Azure portal
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **리소스 만들기**를 클릭한 다음, 검색 상자에 “Ubuntu Server 14.04”를 입력합니다. 검색에서 반환된 항목을 클릭합니다. 배포 모델에서 **클래식**을 선택한 다음 "만들기"를 클릭합니다.
3. 기본 블레이드에서 이름(VM), 사용자 이름, 인증 형식과 해당 자격 증명, Azure 구독, 리소스 그룹 및 위치와 같은 필수 필드에 대한 값을 제공합니다.

   ![새 Ubuntu 이미지 만들기](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. VM을 프로비전한 후에 VM 이름을 클릭하고 **설정** 범주에서 **끝점**을 클릭합니다. **독립 실행형**에 나열된 SSH 끝점을 찾습니다.

   ![기본 끝점](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Azure CLI
[Linux를 실행하는 Virtual Machine 만들기][vm-instructions]의 단계를 따릅니다.

VM이 프로비전된 후, 다음 명령을 실행하여 SSH 끝점을 가져올 수 있습니다.

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Rails에 Ruby 설치
1. SSH를 사용하여 VM에 연결합니다.
2. SSH 세션에서 다음 명령을 사용하여 VM에 Ruby를 설치합니다.

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    설치는 몇 분 정도 걸릴 수 있습니다. 완료되면 다음 명령을 사용하여 Ruby가 설치되어 있는지 확인합니다.

        ruby -v

3. 다음 명령을 사용하여 Rails를 설치합니다.

        sudo gem install rails --no-rdoc --no-ri -V

    --no-rdoc 및 --no-ri 플래그를 사용하여 설명서 설치를 건너뜁니다. 이 방법이 더 빠릅니다.
    이 명령을 실행하려면 시간이 오래 걸릴 수 있으니 –V를 추가하여 설치 프로세스에 대한 정보를 표시합니다.

## <a name="create-and-run-an-app"></a>앱 만들기 및 실행
SSH를 통해 로그인한 경우.다음 명령을 실행합니다.

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

[새](http://guides.rubyonrails.org/command_line.html#rails-new) 명령은 새 Rails 앱을 만듭니다. [서버](http://guides.rubyonrails.org/command_line.html#rails-server) 명령은 Rails와 함께 제공되는 WEBrick 웹 서버를 시작합니다. (프로덕션에서 사용하기 위해, 유니콘 또는 승객과 같은 다른 서버를 사용할 수 있습니다.)

다음과 유사한 결과가 표시됩니다.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>끝점 추가
1. [Azure Portal] [https://portal.azure.com] 로 이동하고 VM을 선택합니다.

2. 페이지의 왼쪽 가장자리에 있는 **설정**에서 **끝점**을 선택합니다.

3. 페이지 위쪽에서 **ADD**를 클릭합니다.

4. **끝점 추가** 대화 페이지에서 다음 정보를 입력합니다.

   * **이름**: HTTP
   * **프로토콜**: - TCP
   * **공용 포트**: 80
   * **개인 포트**: 3000
   * **부동 PI 주소**: 사용 안 함
   * **액세스 제어 목록 - 순서**: 1001 또는 액세스 규칙의 우선 순위를 설정하는 다른 값
   * **액세스 제어 목록 - 이름**: allowHTTP
   * **액세스 제어 목록 - 작업**: 허용
   * **액세스 제어 목록 - 원격 서브넷**: 1.0.0.0/16

     이 끝점에는 트래픽을 개인 포트 3000으로 라우팅하는 공용 포트 80이 있고 여기서 Rails 서버가 수신 대기합니다. 액세스 제어 목록 규칙은 포트 80에서 공용 트래픽을 허용합니다.

     ![new-endpoint](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. 확인을 클릭하여 끝점을 저장합니다.

6. 메시지가 **가상 머신 끝점 저장**을 표시해야 합니다. 이 메시지가 사라지면 끝점이 활성 상태가 됩니다. 이제 가상 머신의 DNS 이름으로 이동하여 응용 프로그램을 테스트할 수 있습니다. 웹 사이트는 다음과 유사합니다.

    ![기본 Rails 페이지][default-rails-cloud]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 수동으로 대부분의 단계를 수행했습니다. 프로덕션 환경의 개발 컴퓨터에 앱을 작성하고 Azure VM에 배포합니다. 또한 대부분의 프로덕션 환경은 Rails 응용 프로그램을 다른 서버 프로세스(예: Apache 또는 NginX)와 함께 호스트하며 이러한 서버 프로세스는 Rails 응용 프로그램의 여러 인스턴스로 라우팅하여 정적 리소스를 제공하는 요청을 처리합니다. 자세한 내용은 http://rubyonrails.org/deploy/를 참조하세요.

Ruby on Rails에 대해 자세히 알아보려면 [Ruby on Rails 가이드][rails-guides](영문)를 방문하세요.

Ruby 응용 프로그램에서 Azure 서비스를 사용하려면 다음을 참조하세요.

* [BLOB을 사용하여 구조화되지 않은 데이터 저장][blobs]
* [테이블을 사용하여 키/값 쌍 저장][tables]
* [Content Delivery Network로 높은 대역폭 콘텐츠 제공][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal-classic.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
