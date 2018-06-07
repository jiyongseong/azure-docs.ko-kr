---
title: Azure Blob 저장소를 Linux의 파일 시스템으로 탑재하는 방법 | Microsoft Docs
description: Linux에 FUSE가 있는 Azure Blob 저장소 컨테이너를 탑재합니다.
services: storage
documentationcenter: linux
author: seguler
manager: jahogg
ms.service: storage
ms.devlang: bash
ms.topic: article
ms.date: 05/10/2018
ms.author: seguler
ms.openlocfilehash: 1098eef15b559c30ef436d8e13bbe02bddb78649
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072095"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>blobfuse를 사용하여 Blob Storage를 파일 시스템으로 탑재하는 방법

## <a name="overview"></a>개요
[blobfuse](https://github.com/Azure/azure-storage-fuse)는 Azure Blob Storage용 가상 파일 시스템 드라이버로, Linux 파일 시스템을 통해 Storage 계정의 기존 블록 Blob 데이터에 액세스할 수 있습니다. Azure Blob Storage는 개체 저장소 서비스이므로 계층 구조 네임스페이스가 없습니다. blobfuse는 슬래시(/)를 구분 기호로 사용하는 가상 디렉터리 체계를 사용하여 이 네임스페이스를 제공합니다.  

이 가이드에서는 blobfuse를 사용하고, Blob 저장소 컨테이너를 Linux에 탑재하고, 데이터에 액세스하는 방법을 보여 줍니다. blobfuse에 대한 자세한 내용은 [blobfuse 리포지토리](https://github.com/Azure/azure-storage-fuse)의 세부 정보를 참조하세요.

> [!WARNING]
> blobfuse는 단순히 요청을 [Blob REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)로 변환하기 때문에 100% POSIX 준수를 보장하지 않습니다. 예를 들어 이름 바꾸기 작업은 POSIX에서 원자성이지만, blobfuse에서는 그렇지 않습니다.
> 네이티브 파일 시스템과 blobfuse의 차이점에 대한 전체 목록은 [blobfuse 소스 코드 리포지토리](https://github.com/azure/azure-storage-fuse)를 방문하세요.
> 

## <a name="install-blobfuse-on-linux"></a>Linux에 blobfuse 설치
blobfuse 이진 파일은 [Linux용 Microsoft 소프트웨어 리포지토리](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)에서 사용할 수 있습니다. blobfuse를 설치하려면 이러한 리포지토리 중 하나를 구성합니다.

### <a name="configure-the-microsoft-package-repository"></a>Microsoft 패키지 리포지토리 구성
[Microsoft 제품용 Linux 패키지 리포지토리](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)를 구성합니다.

예를 들어 Enterprise Linux 6 배포의 경우 다음과 같습니다.
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

마찬가지로 URL을 `.../rhel/7/...`로 변경하여 Enterprise Linux 7 배포를 가리킵니다.

또 다른 예로 Ubuntu 14.04의 경우 다음과 같습니다.
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

마찬가지로 URL을 `.../ubuntu/16.04/...`로 변경하여 Ubuntu 16.04 배포를 가리킵니다.

### <a name="install-blobfuse"></a>blobfuse 설치

Ubuntu/Debian 배포:
```bash
sudo apt-get install blobfuse
```

Enterprise Linux 배포:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>탑재 준비
blobfuse를 사용하려면 열려 있는 파일을 모두 버퍼링하고 캐시하기 위해 파일 시스템에 임시 경로가 필요하며, 이 경우 네이티브와 비슷한 성능이 제공됩니다. 이 임시 경로의 경우 성능이 가장 좋은 디스크를 선택하거나 최상의 성능을 위해 RAM 디스크를 사용합니다. 

> [!NOTE]
> blobfuse는 열려 있는 모든 파일 콘텐츠를 임시 경로에 저장합니다. 열려 있는 모든 파일을 수용할 수 있는 충분한 공간이 있는지 확인합니다. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(선택 사항) 임시 경로로 RAM 디스크 사용
다음 예제에서는 blobfuse에 대한 디렉터리를 만들 뿐만 아니라 16GB의 RAM 디스크를 만듭니다. 필요에 따라 크기를 선택합니다. 이 RAM 디스크를 사용하면 blobfuse에서 최대 16GB 크기의 파일을 열 수 있습니다. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>임시 경로로 SSD 사용
Azure에서는 VM에서 사용할 수 있는 임시 디스크(SSD)를 사용하여 blobfuse에 대기 시간이 짧은 버퍼를 제공할 수 있습니다. Ubuntu 배포에서는 이 임시 디스크가 '/mnt'에 탑재되는 반면, RedHat 및 CentOS 배포에서는 '/mnt/resource/'에 탑재됩니다.

사용자가 임시 경로에 액세스할 수 있는지 확인합니다.
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Storage 계정 자격 증명 구성
blobfuse를 사용하려면 자격 증명을 다음 형식의 텍스트 파일에 저장해야 합니다. 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

이 파일을 만든 후에는 다른 사용자가 읽을 수 없도록 액세스를 제한해야 합니다.
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>탑재할 빈 디렉터리 만들기
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>탑재

> [!NOTE]
> 탑재 옵션에 대한 전체 목록은 [blobfuse 리포지토리](https://github.com/Azure/azure-storage-fuse#mount-options)를 확인하세요.  
> 

blobfuse를 탑재하려면 사용자와 함께 다음 명령을 실행합니다. 이 명령은 '/path/to/fuse_connection.cfg'에 지정된 컨테이너를 '/mycontainer' 위치에 탑재합니다.

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

이제 일반 파일 시스템 API를 통해 블록 Blob에 액세스할 수 있습니다. 사용자가 디렉터리를 탑재한 경우에만 탑재된 해당 디렉터리에 액세스할 수 있으므로 액세스를 보호합니다. 모든 사용자에게 액세스를 허용하려면 ```-o allow_other``` 옵션을 통해 탑재하면 됩니다. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>다음 단계

* [blobfuse 홈페이지](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [blobfuse 문제 보고](https://github.com/Azure/azure-storage-fuse/issues) 

