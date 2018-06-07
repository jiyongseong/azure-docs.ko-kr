---
title: "Azure CDN과 Azure Storage 계정 통합 | Microsoft Docs"
description: "Azure Storage에서 Blob을 캐시하여 고대역폭 콘텐츠를 배달하기 위해 Azure CDN(Content Delivery Network)을 사용하는 방법을 알아봅니다."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: mazha
ms.openlocfilehash: 022071f7825cb9184bd4c815c09e1c202a0a6f91
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Azure CDN과 Azure Storage 계정 통합
Azure CDN(콘텐츠 배달 네트워크) 기능이 Azure Storage에서 콘텐츠를 캐시하도록 할 수 있습니다. Azure CDN은 개발자에게 고대역폭 콘텐츠를 배달하기 위한 전역 솔루션을 제공합니다. 미국, 유럽, 아시아, 오스트레일리아 및 남아메리카의 물리적 노드에 계산 인스턴스의 Blob 및 정적 콘텐츠를 캐시할 수 있습니다.

## <a name="step-1-create-a-storage-account"></a>1 단계: 저장소 계정 만들기
다음 절차에 따라 Azure 구독에 대한 새 저장소 계정을 만듭니다. 저장소 계정을 통해 Azure Storage 서비스에 액세스할 수 있습니다. 저장소 계정은 Azure Blob, Queue Storage, Table Storage와 같은 각 Azure Storage 서비스 구성 요소에 액세스하기 위한 가장 높은 수준의 네임스페이스를 나타냅니다. 자세한 내용은 [Microsoft Azure Storage 소개](../storage/common/storage-introduction.md) 를 참조하세요.

저장소 계정을 만들려면 관련 구독에 대한 서비스 관리자 또는 공동 관리자여야 합니다.

> [!NOTE]
> Azure Portal 및 Powershell을 포함하는 여러 가지 방법을 사용하여 저장소 계정을 만들 수 있습니다. 이 자습서에서는 Azure Portal을 사용하는 방법을 설명합니다.   
> 

**Azure 구독에 대한 저장소 계정을 만들려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 위 구석에서 **리소스 만들기**를 선택합니다. **새로 만들기** 창에서 **저장소**를 선택한 후 **저장소 계정 - Blob, 파일, 테이블, 큐**를 선택합니다.
    
    **저장소 계정 만들기** 창이 나타납니다.   

    ![저장소 계정 만들기 창](./media/cdn-create-a-storage-account-with-cdn/cdn-create-new-storage-account.png)

3. **이름** 상자에 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다.
   
    이 값은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URI 내의 호스트 이름이 됩니다. Blob 저장소의 컨테이너 리소스 주소를 지정하려면 다음 형식의 URI를 사용합니다.
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    여기서 *&lt;StorageAccountLabel&gt;*은 **이름** 상자에 입력한 값을 나타냅니다.
   
    > [!IMPORTANT]    
    > URL 레이블은 저장소 계정 URI의 하위 도메인을 구성하며, Azure에서 호스트된 모든 서비스에서 고유해야 합니다.
   
    이 값은 프로그래밍 방식으로 이 계정에 액세스할 때 또는 포털에서 이 저장소 계정의 이름으로도 사용됩니다.
    
4. **배포 모델**, **계정 종류**, **성능** 및 **복제**에 대한 기본값을 사용합니다. 
    
5. **구독**에서 저장소 계정에 사용할 구독을 선택합니다.
    
6. **리소스 그룹**에서 리소스 그룹을 선택하거나 만듭니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요.
    
7. **위치**에서 저장소 계정의 위치를 선택합니다.
    
8. **만들기**를 선택합니다. 저장소 계정을 만드는 프로세스를 완료하려면 몇 분 정도 걸릴 수 있습니다.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>2단계: 저장소 계정에 대해 CDN 사용

저장소 계정에서 직접 저장소 계정에서 직접 CDN을 사용하도록 설정할 수 있습니다. 

1. 대시보드에서 저장소 계정을 선택하고 왼쪽 창에서 **Azure CDN**을 선택합니다. **Azure CDN** 단추가 바로 표시되지 않으면 왼쪽 창의 **검색** 상자에 CDN을 입력할 수 있습니다.
    
    **Azure 콘텐츠 배달 네트워크** 창이 나타납니다.

    ![CDN 끝점 만들기](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)
    
2. 필요한 정보를 입력하여 새 끝점을 만듭니다.
    - **CDN 프로필**: 새 CDN 프로필을 만들거나 기존 CDN 프로필을 사용합니다.
    - **가격 책정 계층**: CDN 프로필을 만드는 경우에만 가격 책정 계층을 선택합니다.
    - **CDN 끝점 이름**: CDN 끝점 이름을 입력합니다.

    > [!TIP]
    > 기본적으로 새 CDN 끝점은 저장소 계정의 호스트 이름을 원본 서버로 사용합니다.

3. **만들기**를 선택합니다. 만든 끝점은 끝점 목록에 나타납니다.

    ![저장소 새 CDN 끝점](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!NOTE]
> CDN 끝점에 대해 최적화 유형과 같은 고급 구성 설정을 지정하려는 경우, 대신 [Azure CDN 확장](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)을 사용하여 CDN 끝점 또는 CDN 프로필을 만들 수 있습니다.

## <a name="step-3-enable-additional-cdn-features"></a>3단계: 추가 CDN 기능 사용

저장소 계정의 **Azure CDN** 창에서 목록에 있는 CDN 끝점을 선택하여 CDN 구성 창을 엽니다. 전송에 대해 압축, 쿼리 문자열, 지역 필터링 등과 같은 추가 CDN 기능을 사용하도록 설정할 수 있습니다. CDN 끝점에 사용자 지정 도메인 매핑을 추가하고 사용자 지정 도메인 HTTPS를 사용하도록 설정할 수도 있습니다.
    
![저장소 CDN 끝점 구성](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)

## <a name="step-4-access-cdn-content"></a>4단계: CDN 콘텐츠 액세스
CDN에 캐시된 콘텐츠에 액세스하려면 포털에 제공된 CDN URL을 사용합니다. 캐시된 Blob에 대한 주소는 다음 형식을 갖습니다.

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> 저장소 계정에 대한 CDN 액세스를 사용하도록 설정하면 공개적으로 사용 가능한 모든 개체가 CDN 에지 캐싱에 적합합니다. 현재 CDN에 캐시된 개체를 수정하는 경우 캐시된 콘텐츠 TTL(Time-to-Live) 기간이 만료된 후에 CDN에서 해당 콘텐츠를 새로 고쳐야 CDN을 통해 새 콘텐츠를 사용할 수 있습니다.

## <a name="step-5-remove-content-from-the-cdn"></a>5단계: CDN에서 콘텐츠 제거
더 이상 Azure CDN에 개체를 캐시하지 않으려면 다음 단계 중 하나를 수행할 수 있습니다.

* 컨테이너를 공용 대신 전용으로 설정합니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../storage/blobs/storage-manage-access-to-resources.md)를 참조하세요.
* Azure Portal을 사용하여 CDN 끝점을 사용하지 않도록 설정하거나 삭제합니다.
* 더 이상 개체 요청에 응답하지 않도록 호스티드 서비스를 수정합니다.

Azure CDN에 이미 캐시된 개체는 개체의 TTL(Time-to-Live) 기간이 만료되거나 끝점이 삭제될 때까지 캐시된 상태로 유지됩니다. TTL(Time-to-Live)이 만료되면 Azure CDN에서 CDN 끝점이 여전히 유효하며 개체에 익명으로 액세스할 수 있는지 확인합니다. 그렇지 않으면 개체가 더 이상 캐시되지 않습니다.

## <a name="additional-resources"></a>추가 리소스
* [CDN 끝점에 사용자 지정 도메인 추가](cdn-map-content-to-custom-domain.md)
* [Azure CDN 사용자 지정 도메인에서 HTTPS 구성](cdn-custom-ssl.md).

