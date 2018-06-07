---
title: "Azure CDN을 사용하여 HTTPS를 통한 사용자 지정 도메인으로 Blob 액세스"
description: "Azure CDN을 Blob Storage와 통합하여 HTTPS를 통한 사용자 지정 도메인으로 Blob에 액세스하는 하는 방법에 알아봅니다."
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mihauss
ms.openlocfilehash: 6bad04df324a374f6e8473890345cf516322abd6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Azure CDN을 사용하여 HTTPS를 통한 사용자 지정 도메인으로 Blob 액세스

이제 Azure CDN(콘텐츠 배달 네트워크)은 사용자 지정 도메인 이름에 대한 HTTPS를 지원합니다.
이 기능을 활용하여 HTTPS를 통한 사용자 지정 도메인을 사용하여 저장소 Blob에 액세스할 수 있습니다. 이렇게 하려면 먼저 Blob 끝점에서 Azure CDN을 사용하도록 설정하고 CDN을 사용자 지정 도메인 이름으로 매핑해야 합니다. 이러한 단계를 수행하면 사용자 지정 도메인에 대해 HTTPS를 사용하도록 설정하는 것은 한 번 클릭, 완전한 인증서 관리를 통해 간소화되며 이 모든 것을 일반 CDN 가격 책정에 비용을 추가할 필요 없이 수행할 수 있습니다.

이는 전송 중일 때 개인 정보 및 민감한 웹 응용 프로그램 데이터의 데이터 무결성을 보호하므로 중요한 기능입니다. SSL 프로토콜을 사용하여 HTTPS를 통해 트래픽을 제공하면 인터넷 간에 전송되는 데이터를 암호화할 수 있습니다. HTTPS는 신뢰할 수 있는 인증을 제공하며 공격으로부터 웹 응용 프로그램을 보호합니다.

> [!NOTE]
> 사용자 지정 도메인 이름에 대한 SSL 지원뿐 아니라 Azure CDN은 전 세계 고대역폭 콘텐츠를 제공하는 응용 프로그램의 규모를 확장하는 데 도움이 됩니다.
> 자세한 내용은 [Azure CDN 개요](../../cdn/cdn-overview.md)를 확인하세요.
>
>

## <a name="quick-start"></a>빠른 시작

다음은 사용자 지정 Blob Storage 끝점에 대한 HTTPS를 사용하도록 설정하는 데 필요한 단계입니다.

1.  [Azure Storage 계정과 Azure CDN을 통합합니다](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    이 문서에서는 아직 수행하지 않은 경우 Azure Portal에서 저장소 계정을 만드는 과정을 안내합니다.
2.  [Azure CDN 콘텐츠를 사용자 지정 도메인에 매핑합니다](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Azure CDN 사용자 지정 도메인에서 HTTPS를 사용하도록 설정합니다](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>공유 액세스 서명

익명 읽기 액세스를 허용하지 않도록 Blob Storage 끝점을 구성한 경우 [SAS(공유 액세스 서명)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 토큰을 사용자 지정 도메인에 사용자가 만든 각 요청에 제공해야 합니다. 기본적으로 Blob Storage 끝점은 익명 읽기 액세스를 허용하지 않습니다. 공유 액세스 서명에 관한 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](storage-manage-access-to-resources.md)를 참조하세요.

Azure CDN은 SAS 토큰에 추가된 제한 사항을 준수하지 않습니다. 예를 들어 모든 SAS 토큰은 만료 시간이 있습니다. 즉, 콘텐츠가 CDN 에지 노드에서 제거될 때까지는 만료된 SAS로 콘텐츠에 여전히 액세스할 수 있습니다. 캐시 응답 헤더를 설정하여 데이터를 CDN에 얼마나 오래 캐시할 것인지 제어할 수 있습니다. 지침에 관해서는 [Azure CDN에서 Azure Storage Blob 만료 관리](../../cdn/cdn-manage-expiration-of-blob-content.md)를 참조하세요.

동일한 Blob 끝점에 대한 여러 개의 SAS URL을 만드는 경우 Azure CDN에 대해 쿼리 문자열 캐싱을 설정하는 것이 좋습니다. 이는 각 URL이 고유한 엔터티로 처리되었는지 확인하기 위함입니다. 자세한 내용은 [쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어](../../cdn/cdn-query-string.md)를 참조하세요.

## <a name="http-to-https-redirection"></a>HTTP에서 HTTPS로 리디렉션

HTTP 트래픽을 HTTPS로 리디렉션하도록 선택할 수 있습니다. 그러려면 Verizon에서 제공하는 Azure CDN 프리미엄을 사용해야 합니다. 다음 규칙을 사용하여 [Azure CDN 규칙 엔진을 사용하여 HTTP 동작을 재정의](../../cdn/cdn-rules-engine.md)해야 합니다.

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

“Cdn-endpoint-name”은 CDN 끝점에 대해 구성한 이름을 참조합니다. 드롭다운에서 이 값을 선택할 수 있습니다. “Origin-path”는 정적 콘텐츠가 있는 원본 저장소 계정 내 경로를 참조합니다.
단일 컨테이너의 모든 정적 콘텐츠를 호스팅하는 경우 “origin-path”를 해당 컨테이너의 이름으로 바꿉니다.

규칙에 자세히 알아보려면 [Azure CDN 규칙 엔진 기능](../../cdn/cdn-rules-engine-reference-features.md)을 참조하세요.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Azure CDN을 통해 Blob에 액세스할 때 에지 노드와 원본(Blob Storage) 사이의 트래픽에 대해 [Blob Storage 가격](https://azure.microsoft.com/pricing/details/storage/blobs/)을, 에지 노드에서 액세스하는 데이터에 대해 [CDN 가격](https://azure.microsoft.com/pricing/details/cdn/)을 지불합니다.

예를 들어, Azure CDN을 사용하여 액세스되는 저장소 계정이 미국 서부에 있습니다. 영국에서 CDN을 통해 해당 저장소 계정에 있는 Blob 중 하나에 액세스하려고 시도하는 경우 Azure는 먼저 해당 Blob에 대한 영국 가장 가까이에 있는 에지 노드를 확인합니다. 발견한 경우 Blob의 해당 복사본에 액세스하고, CDN에서 액세스하였으므로 CDN 가격 책정을 사용하게 됩니다. 찾을 수 없는 경우, Azure는 Blob을 에지 노드에 복사합니다. 이는 Blob Storage 가격 책정에 지정된 대로 송신 및 트랜잭션 비용이 발생됩니다. 그런 다음, CDN 결제로 이어지는 에지 노드의 파일에 액세스합니다.

[CDN 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cdn/)에서 살펴볼 때 사용자 지정 도메인 이름을 위한 HTTPS 지원은 Verizon 제품(표준 및 프리미엄)의 Azure CDN에만 사용할 수 있다는 점에 유의하세요.

## <a name="next-steps"></a>다음 단계

[Blob Storage 끝점에 대한 사용자 지정 도메인 이름 구성](storage-custom-domain-name.md)
