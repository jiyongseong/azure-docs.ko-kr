---
title: Azure 스택 Azure CLI를 사용 하 여 Windows 가상 컴퓨터 만들기 | Microsoft Docs
description: Azure CLI를 사용 하 여 Azure 스택에 Windows VM을 만드는 방법에 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 381c1c37b0675d97adc058979a5d9b5c4fd2cc8b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>빠른 시작: Azure 스택에서 Azure CLI를 사용 하 여 Windows Server 가상 컴퓨터 만들기

‎*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure CLI를 사용 하 여 Windows Server 2016 가상 컴퓨터를 만들 수 있습니다. 이 문서를 만들고 가상 컴퓨터를 사용의 단계를 수행 합니다. 이 문서는 다음 단계를 제공합니다.

* 가상 컴퓨터 원격 클라이언트와 연결 합니다.
* IIS 웹 서버를 설치 하 고 기본 홈 페이지를 봅니다.
* 리소스를 정리 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 스택 연산자 추가 되었는지 확인는 **Windows Server 2016** Azure 스택 마켓플레이스 이미지입니다.

* Azure 스택 특정 버전의 Azure CLI를 만들고 리소스를 관리 해야 합니다. Azure 스택에 대해 구성 된 Azure CLI를 설정 하지 않은 경우의 단계에 따라 [설치 및 구성 Azure CLI](azure-stack-version-profiles-azurecli2.md)합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 배포 하 고 Azure 스택 리소스를 관리할 수 있는 논리적 컨테이너입니다. Azure 스택 환경에서 실행 하는 [az 그룹 만들기](/cli/azure/group#az_group_create) 리소스 그룹을 만드는 명령입니다.

>[!NOTE]
 다음 코드 예제에서는 모든 변수에 값 할당 됩니다. 그러나 하려는 경우 새 값을 할당할 수 있습니다.

다음 예제에서는 로컬 위치에 myResourceGroup 명명 된 리소스 그룹을 만듭니다.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

가상 컴퓨터 (VM)를 사용 하 여 만들기는 [az vm 만들기](/cli/azure/vm#az_vm_create) 명령입니다. 다음 예제에서는 myVM이라는 VM을 만듭니다. 이 예제에서는 관리 사용자 이름에 대 한 Demouser 사용 및 Demouser@123 로 사용자 암호입니다. 사용자 환경에 적합 한 값으로 이러한 값을 변경 합니다.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

VM이 생성 하는 경우는 **PublicIPAddress** 출력에서 매개 변수는 가상 컴퓨터에 대 한 공용 IP 주소를 포함 합니다. 가상 컴퓨터에 액세스 하는 데 필요 하기 때문에이 주소를 적어 둡니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기

을 때문에이 VM은 IIS 웹 서버를 실행 하려면 인터넷 트래픽을 위해 포트 80을 열려면 해야 합니다.

사용 하 여는 [az vm-포트를 열](/cli/azure/vm#open-port) 포트 80을 열려면 명령입니다.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

다음 명령을 사용 하 여 가상 컴퓨터에 원격 데스크톱 연결을 만듭니다. "공용 IP 주소" 가상 컴퓨터의 IP 주소로 바꿉니다. 대화 상자가 나타나면 사용자 이름 및 가상 컴퓨터에 사용한 암호를 입력 합니다.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>PowerShell을 사용하여 IIS 설치

가상 컴퓨터에 로그인 한 경우 했으므로 IIS를 설치 하려면 PowerShell을 사용할 수 있습니다. 가상 컴퓨터에서 PowerShell을 시작 하 고 다음 명령을 실행 합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS 시작 페이지 보기

기본 IIS 시작 페이지를 보려면 사용자가 선택한 웹 브라우저를 사용할 수 있습니다. 기본 페이지를 방문 하 여 이전 섹션에 설명 된 공용 IP 주소를 사용 합니다.

![IIS 기본 사이트](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 리소스를 정리 합니다. 사용 하 여는 [az 그룹 삭제](/cli/azure/group#az_group_delete) 명령을 가상 컴퓨터 리소스 그룹을 제거 하 고 모든 관련 리소스입니다.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기본 Windows Server 가상 컴퓨터를 배포 합니다. Azure 스택 가상 컴퓨터에 대 한 자세한 내용은 계속 [스택 Azure의에서 가상 컴퓨터에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.
