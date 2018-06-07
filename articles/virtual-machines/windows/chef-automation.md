---
title: Chef를 사용하여 Azure 가상 머신 배포 | Microsoft Docs
description: Chef를 사용하여 자동화된 가상 머신 배포 및 Microsoft Azure에서 구성하는 방법에 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: jeconnoc
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: 36293c41219a1b42d75850fa66d3c631637bb855
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Chef를 사용하여 Azure 가상 머신 배포 자동화
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef는 자동화 및 필요한 상태 구성을 제공하는 유용한 도구입니다.

최신 클라우드 api 릴리스에서 Chef는 Azure와의 원활한 통합을 통해 단일 명령으로 구성 상태를 프로비전 및 배포할 수 있는 기능을 제공합니다.

이 문서에서는 Chef 환경을 설정하여 Azure 가상 머신을 프로비전하고, 정책 또는 “CookBook”을 만든 다음 이 cookbook을 Azure 가상 머신에 배포하는 과정을 안내합니다.

시작해 보겠습니다.

## <a name="chef-basics"></a>Chef 기본 사항
시작하기 전에 [Chef의 기본 개념을 검토](http://www.chef.io/chef)하세요. 

다음 다이어그램에 대략적인 Chef 아키텍처가 나와 있습니다.

![][2]

Chef에는 Chef 서버, Chef 클라이언트(노드) 및 Chef 워크스테이션 등의 세 가지 주요 아키텍처 구성 요소가 있습니다.

Chef Server는 관리 지점이며, 호스트 솔루션과 온-프레미스 솔루션의 두 가지 옵션이 있습니다. 여기에서는 호스트 솔루션을 사용합니다.

Chef Client(노드)는 관리 중인 서버에 있는 에이전트입니다.

Chef Workstation은 정책을 만들고 관리 명령을 실행하는 관리 워크스테이션입니다. Chef Workstation에서 **knife** 명령을 실행하여 인프라를 관리합니다.

“Cookbook” 및 “Recipe” 개념도 있습니다. 이는 우리가 정의하고 서버에 적용하는 효과적인 정책입니다.

## <a name="preparing-the-workstation"></a>워크스테이션 준비
먼저 워크스테이션을 준비하겠습니다. 여기서는 표준 Windows 워크스테이션을 사용합니다. 구성 파일과 cookbook을 저장할 디렉터리를 만들어야 합니다.

먼저 C:\chef라는 디렉터리를 만듭니다.

그런 다음 c:\chef\cookbooks라는 두 번째 디렉터리를 만듭니다.

이제 Chef가 Azure 구독과 통신할 수 있도록 Azure 설정 파일을 다운로드해야 합니다.

PowerShell Azure [Get-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) 명령을 사용하여 게시 설정을 다운로드합니다. 

게시 설정 파일을 C:\chef에 저장합니다.

## <a name="creating-a-managed-chef-account"></a>관리되는 Chef 계정 만들기
[여기](https://manage.chef.io/signup)에 호스트되는 Chef 계정에 등록합니다.

등록 프로세스 중에 새 조직을 만들지 묻는 메시지가 나타납니다.

![][3]

조직을 만든 후 시작 키트를 다운로드합니다.

![][4]

> [!NOTE]
> 키가 재설정된다는 경고 메시지가 나타나는 경우, 아직 구성된 기존 인프라가 없으므로 계속 진행해도 됩니다.
> 
> 

이 시작 키트 zip 파일에는 조직 구성 파일 및 키가 포함되어 있습니다.

## <a name="configuring-the-chef-workstation"></a>Chef 워크스테이션 구성
chef-starter.zip 내용을 C:\chef에 추출합니다.

chef-starter\chef-repo\.chef의 모든 파일을 c:\chef 디렉터리에 복사합니다.

이제 디렉터리가 다음 예제와 같이 표시됩니다.

![][5]

이제 c:\chef 루트에 있는 Azure 게시 파일을 포함하여 네 개의 파일이 있습니다.

PEM 파일에는 조직 및 관리자의 통신용 개인 키가 들어 있고, knife.rb 파일에는 knife 구성이 들어 있습니다. knife.rb 파일을 편집해야 합니다.

원하는 편집기에서 파일을 열고 다음에 보듯이 표시되도록 경로에서 /../를 제거하여 “cookbook_path”를 수정합니다.

    cookbook_path  ["#{current_dir}/cookbooks"]

또한 Azure 게시 설정 파일의 이름을 나타내는 다음 줄을 추가합니다.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

이제 knife.rb 파일이 다음 예제와 유사하게 표시됩니다.

![][6]

이러한 줄은 Knife가 c:\chef\cookbooks 아래의 cookbooks 디렉터리에서 참조되고 Azure 작업 중 Azure 게시 설정 파일을 사용하도록 해줍니다.

## <a name="installing-the-chef-development-kit"></a>Chef Development Kit 설치
이제 ChefDK(Chef Development Kit)를 [다운로드 및 설치](http://downloads.getchef.com/chef-dk/windows) 하여 Chef Workstation을 설치합니다.

![][7]

기본 위치인 c:\opscode에 설치합니다. 설치하는 데 10분 정도 걸립니다.

PATH 변수에 C:\opscode\chefdk\bin;C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin에 대한 항목이 포함되어 있어야 합니다.

그렇지 않으면 이러한 경로를 추가해야 합니다.

*경로 순서가 중요합니다!* opscode 경로가 올바른 순서가 아니면 문제가 발생합니다.

계속하기 전에 워크스테이션을 다시 부팅하세요.

이제 Knife Azure 확장을 설치합니다. 이는 “Azure 플러그 인”이 포함된 Knife를 제공합니다.

다음 명령을 실행합니다.

    chef gem install knife-azure ––pre

> [!NOTE]
> –pre 인수는 최신 API 집합에 대한 액세스를 제공하는 최신 RC 버전의 knife azure 플러그인을 받을 수 있도록 해줍니다.
> 
> 

이와 동시에 여러 종속성도 설치될 수 있습니다.

![][8]

모든 것이 올바르게 구성되었는지 확인하려면 다음 명령을 실행합니다.

    knife azure image list

모든 것이 올바르게 구성되었으면 사용 가능한 Azure 이미지 목록이 표시됩니다.

축하합니다. 워크스테이션이 설정되었습니다!

## <a name="creating-a-cookbook"></a>Cookbook 만들기
Cookbook은 Chef에서 관리되는 클라이언트를 실행할 명령 집합을 정의하는 데 사용됩니다. Cookbook 만들기는 간단하며, **chef generate cookbook** 명령을 사용하여 Cookbook 템플릿을 생성할 수 있습니다. 저는 IIS를 자동으로 배포하는 정책을 좋아하므로 저의 Cookbook 웹 서버를 호출해 보겠습니다.

C:\Chef 디렉터리에서 다음 명령을 실행합니다.

    chef generate cookbook webserver

C:\Chef\cookbooks\webserver 디렉터리 아래에 파일 집합이 생성됩니다. 이제 관리되는 가상 머신에서 Chef 클라이언트를 실행할 명령 집합을 정의해야 합니다.

명령은 default.rb 파일에 저장되어 있습니다. 이 파일에서 IIS를 설치하고, IIS를 시작하며, 템플릿 파일을 wwwroot 폴더에 복사하는 명령 집합을 정의합니다.

C:\chef\cookbooks\webserver\recipes\default.rb를 수정하고 다음 줄을 추가합니다.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

작업이 완료되면 파일을 한 번 저장합니다.

## <a name="creating-a-template"></a>템플릿 만들기
앞서 설명한 바와 같이, default.html 페이지로 사용할 템플릿 파일을 생성해야 합니다.

다음 명령을 실행하여 템플릿을 생성합니다.

    chef generate template webserver Default.htm

이제 C:\chef\cookbooks\webserver\templates\default\Default.htm.erb 파일로 이동합니다. 간단한 “Hello World” HTML 코드를 추가하여 파일을 편집하고 파일을 저장합니다.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Chef Server에 Cookbook 업로드
이 단계에서는 로컬 컴퓨터에서 만든 Cookbook을 복사하여 Chef Hosted Server에 업로드합니다. 업로드가 완료되면 **정책** 탭에 Cookbook이 표시됩니다.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Knife Azure를 사용하여 가상 머신 배포
이제 Azure 가상 머신을 배포하고 IIS 웹 서비스 및 기본 웹 페이지를 설치할 “Webserver” Cookbook을 적용합니다.

이 작업을 수행하려면 **knife azure server create** 명령을 사용합니다.

명령의 예가 다음에 나타납니다.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

매개 변수는 설명이 필요 없습니다. 특정 변수를 대체하고 실행합니다.

> [!NOTE]
> 명령줄에서 –tcp-endpoints 매개변수를 사용하여 끝점 네트워크 필터 규칙을 자동화해 보겠습니다. 포트 80 및 3389를 열어 내 웹 페이지 및 RDP 세션에 대한 액세스를 제공합니다.
> 
> 

명령을 실행하고 나면 Azure 포털로 이동하며 프로비전을 시작할 컴퓨터가 표시됩니다.

![][13]

명령 프롬프트가 다음에 나타납니다.

![][10]

배포가 완료되면 포트 80을 통해 웹 서비스에 연결할 수 있어야 합니다. knife azure 명령으로 가상 컴퓨터를 프로비전할 때 이 포트를 이미 열었기 때문입니다. 이 가상 컴퓨터는 클라우드 서비스에 있는 유일한 가상 컴퓨터이므로 클라우드 서비스 url과 연결해 보겠습니다.

![][11]

보시는 바와 같이 HTML 코드를 독창적으로 수정했습니다.

포트 3389에서도 Azure Portal에서 RDP 세션을 통해 연결할 수 있다는 점을 기억하세요.

유익한 정보가 되셨기 바랍니다. 이제 Azure의 Infrastructure as Code 과정을 시작해 보세요.

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png


<!--Link references-->
