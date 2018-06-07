---
title: "Azure Blob Storage 끝점에 대한 사용자 지정 도메인 이름 구성 | Microsoft Docs"
description: "Azure Portal을 사용하여 고유한 CNAME(정식 이름)을 Azure Storage 계정의 Blob Storage 끝점에 매핑합니다."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/25/2017
ms.author: tamram
ms.openlocfilehash: 2b776e8f40f6972a60f933b0104312b119439f38
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Blob 저장소 끝점에 대한 사용자 지정 도메인 이름 구성

Azure 저장소 계정에서 Blob 데이터에 액세스할 수 있도록 사용자 지정 도메인을 구성할 수 있습니다. Blob 저장소의 기본 끝점은 `<storage-account-name>.blob.core.windows.net`입니다. **www.contoso.com**과 같은 사용자 지정 도메인 및 하위 도메인을 저장소 계정의 Blob 끝점에 매핑하면 사용자도 해당 도메인을 사용하여 저장소 계정의 Blob 데이터에 액세스할 수 있습니다.

> [!IMPORTANT]
> Azure Storage는 아직 기본적으로 사용자 지정 도메인으로 HTTPS를 지원하지 않습니다. 현재 [Azure CDN을 사용하여 HTTP를 통한 사용자 지정 도메인으로 Blob에 액세스](storage-https-custom-domain-cdn.md)할 수 있습니다.
>

다음 표는 이름이 **mystorageaccount**인 저장소 계정에 있는 Blob 데이터에 액세스하기 위한 여러 샘플 URL을 보여 줍니다. 저장소 계정에 등록된 사용자 지정 도메인은 **www.contoso.com**입니다.

| 리소스 종류 | 기본 URL | 사용자 지정 도메인 URL |
| --- | --- | --- |
| Storage 계정 | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| 루트 컨테이너 | http://mystorageaccount.blob.core.windows.net/myblob 또는 http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob 또는 http://www.contoso.com/$root/myblob |

## <a name="direct-vs-intermediary-domain-mapping"></a>직접 도메인 매핑과 중간 도메인 매핑

저장소 계정의 Blob 끝점에 사용자 지정 도메인을 가리키는 방법에는 두 가지가 있으며 이는 직접 CNAME 매핑 방법과 *asverify* 중간 하위 도메인을 사용하는 것입니다.

### <a name="direct-cname-mapping"></a>직접 CNAME 매핑

첫 번째 가장 단순한 방법은 사용자 지정 도메인 및 하위 도메인을 Blob 끝점에 직접 매핑하는 CNAME(정식 이름) 레코드를 만드는 것입니다. CNAME 레코드는 원본 도메인을 대상 도메인에 매핑하는 DNS(Domain Name System) 기능입니다. 이 경우 원본 도메인은 고유한 사용자 지정 도메인과 하위 도메인입니다(예: *www.contoso.com*). 대상 도메인은 Blob service 끝점입니다(예: *mystorageaccount.blob.core.windows.net*).

직접 매핑하는 방법은 [사용자 지정 도메인 등록](#register-a-custom-domain)에서 설명합니다.

### <a name="intermediary-mapping-with-asverify"></a>*asverify*를 사용하여 중간 매핑

두 번째 방법 역시 CNAME 레코드를 사용하지만 Azure에서 인식하는 특수 하위 도메인 **asverify**를 사용하여 가동 중지 시간을 방지합니다.

사용자 지정 도메인을 Blob 끝점에 매핑하는 프로세스로 인해 [Azure Portal](https://portal.azure.com)에서 도메인을 등록하는 동안 도메인이 잠시 가동 중지될 수 있습니다. 사용자 지정 도메인에서 가동 중지 시간이 0이어야 하는 SLA(서비스 수준 계약)가 설정된 응용 프로그램을 현재 지원하고 있다면 Azure *asverify* 하위 도메인을 중간 등록 단계로 사용할 수 있습니다. 이 중간 단계를 통해 DNS 매핑이 진행되는 동안 사용자가 도메인에 액세스할 수 있습니다.

중간 방법은 [*asverify* 하위 도메인을 사용하여 사용자 지정 도메인 등록](#register-a-custom-domain-using-the-asverify-subdomain)에서 설명합니다.

## <a name="register-a-custom-domain"></a>사용자 지정 도메인 등록
사용자가 도메인을 잠시 사용할 수 없더라도 괜찮은 경우나 사용자 지정 도메인에서 현재 응용 프로그램을 호스트하지 않는 경우에 이 절차에 따라 사용자 지정 도메인을 등록하세요. Azure DNS를 사용하여 Azure Blob 저장소에 대한 사용자 지정 DNS 이름을 구성할 수 있습니다. 자세한 내용은 [Azure DNS를 사용하여 Azure 서비스에 대해 사용자 지정 도메인 설정 제공](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage)을 참조하세요.

사용자 지정 도메인이 가동 중지 시간이 없어야 하는 응용 프로그램을 현재 지원하고 있다면 [*asverify* 하위 도메인을 사용하여 사용자 지정 도메인 등록](#register-a-custom-domain-using-the-asverify-subdomain)에서 설명한 절차를 따르세요.

사용자 지정 도메인 이름을 구성하려면 DNS에서 새 CNAME 레코드를 만들어야 합니다. CNAME 레코드에서 도메인 이름의 별칭을 지정합니다. 이 경우에는 사용자 지정 도메인의 주소를 저장소 계정의 Blob Storage 끝점에 매핑합니다.

일반적으로 도메인 등록 기관의 웹 사이트에서 도메인의 DNS 설정을 관리할 수 있습니다. 각 등록 기관은 서로 유사하면서 약간 다른 방법으로 CNAME 레코드를 지정하지만 개념은 동일합니다. 일부 기본 도메인 등록 패키지에서 DNS 구성을 제공하지 않으므로 CNAME 레코드를 만들려면 먼저 도메인 등록 패키지를 업그레이드해야 할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 저장소 계정으로 이동합니다.
1. 메뉴 블레이드의 **BLOB SERVICE**에서 **사용자 지정 도메인**을 선택하여 *사용자 지정 도메인* 블레이드를 엽니다.
1. 도메인 등록 기관의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**와 같은 섹션에서 이를 찾을 수 있습니다.
1. CNAME을 관리하기 위한 섹션을 찾습니다. 고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인**과 같은 단어를 찾아야 할 수도 있습니다.
1. 새 CNAME 레코드를 만들어 **www** 또는 **photos**와 같은 하위 도메인 별칭을 지정합니다. 그런 다음 Blob service 끝점인 호스트 이름을 **mystorageaccount.blob.core.windows.net** 형식으로 지정합니다. 여기에서 *mystorageaccount*는 저장소 계정의 이름입니다. 사용할 호스트 이름은 [Azure Portal](https://portal.azure.com)에 *사용자 지정 도메인* 블레이드의 항목 #1에 표시됩니다.
1. [Azure Portal](https://portal.azure.com)에서 *사용자 지정 도메인* 블레이드의 텍스트 상자에 하위 도메인을 포함하여 사용자 지정 도메인 이름을 입력합니다. 예를 들어 도메인이 **contoso.com**이고 하위 도메인 별칭이 **www**이면 **www.contoso.com**을 입력합니다. 하위 도메인이 **photos**이면 **photos.contoso.com**을 입력합니다. 하위 도메인은 *필수*입니다.
1. *사용자 지정 도메인* 블레이드에서 **저장**을 선택하여 사용자 지정 도메인을 등록합니다. 등록에 성공한 경우 저장소 계정이 성공적으로 업데이트되었음을 알리는 포털 알림이 표시됩니다.

새 CNAME 레코드가 DNS를 통해 전파된 후 적절한 사용 권한이 있는 사용자는 사용자 지정 도메인을 사용하여 Blob 데이터를 볼 수 있습니다.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>*asverify* 하위 도메인을 사용하여 사용자 지정 도메인 등록
사용자 지정 도메인이 가동 중지 시간이 없어야 하는 SLA가 있는 응용 프로그램을 현재 지원하고 있는 경우 이 절차에 따라 사용자 지정 도메인을 등록하세요. `asverify.<subdomain>.<customdomain>`에서 `asverify.<storageaccount>.blob.core.windows.net`을 가리키는 CNAME을 만들어 Azure에 도메인을 미리 등록할 수 있습니다. 그런 다음 `<subdomain>.<customdomain>`에서 `<storageaccount>.blob.core.windows.net`을 가리키는 두 번째 CNAME을 만들 수 있습니다. 그러면 사용자 지정 도메인에 대한 지점 트래픽이 Blob 끝점으로 직접 보내집니다.

**asverify** 하위 도메인은 Azure에서 인식하는 특수한 하위 도메인입니다. 고유한 하위 도메인 앞에 `asverify`를 추가하면 도메인의 DNS 레코드를 수정하지 않아도 Azure에서 사용자 지정 도메인을 인식할 수 있습니다. 도메인의 DNS 레코드를 수정하면 가동 중지 시간 없이 Blob 끝점에 매핑됩니다.

1. [Azure Portal](https://portal.azure.com)의 저장소 계정으로 이동합니다.
1. 메뉴 블레이드의 **BLOB SERVICE**에서 **사용자 지정 도메인**을 선택하여 *사용자 지정 도메인* 블레이드를 엽니다.
1. DNS 공급자의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**와 같은 섹션에서 이를 찾을 수 있습니다.
1. CNAME을 관리하기 위한 섹션을 찾습니다. 고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인**과 같은 단어를 찾아야 할 수도 있습니다.
1. 새 CNAME 레코드를 만들어 *asverify* 하위 도메인을 포함한 하위 도메인 별칭을 제공합니다. 예를 들어 **asverify.www** 또는 **asverify.photos**입니다. 그런 다음 Blob service 끝점인 호스트 이름을 **asverify.mystorageaccount.blob.core.windows.net** 형식으로 지정합니다. 여기에서 **mystorageaccount**는 저장소 계정의 이름입니다. 사용할 호스트 이름이 [Azure Portal](https://portal.azure.com)에서 *사용자 지정 도메인* 블레이드의 항목 #2에 나타납니다.
1. [Azure Portal](https://portal.azure.com)에서 *사용자 지정 도메인* 블레이드의 텍스트 상자에 하위 도메인을 포함하여 사용자 지정 도메인 이름을 입력합니다. *asverify*를 포함하지 않습니다. 예를 들어 도메인이 **contoso.com**이고 하위 도메인 별칭이 **www**이면 **www.contoso.com**을 입력합니다. 하위 도메인이 **photos**이면 **photos.contoso.com**을 입력합니다. 하위 도메인은 필수입니다.
1. **간접 CNAME 유효성 검사 사용** 확인란을 선택합니다.
1. *사용자 지정 도메인* 블레이드에서 **저장**을 선택하여 사용자 지정 도메인을 등록합니다. 등록에 성공한 경우 저장소 계정이 성공적으로 업데이트되었음을 알리는 포털 알림이 표시됩니다. 이제 사용자 지정 도메인이 Azure에서 확인되었지만 도메인에 대한 트래픽은 아직 저장소 계정으로 라우팅되지 않습니다.
1. DNS 공급자의 웹 사이트로 돌아가 하위 도메인을 Blob service 끝점에 매핑한 CNAME 레코드를 하나 더 만듭니다. 예를 들어 하위 도메인을 **www** 또는 **photos**(*asverify* 없이)로 지정하고 호스트 이름을 **mystorageaccount.blob.core.windows.net**으로 지정합니다. 여기서 **mystorageaccount**는 저장소 계정의 이름입니다. 이 단계에서 사용자 지정 도메인 등록이 완료됩니다.
1. 마지막으로 **asverify** 하위 도메인을 포함하여 만든 CNAME 레코드는 중간 단계로만 필요하므로 삭제할 수 있습니다.

새 CNAME 레코드가 DNS를 통해 전파된 후 적절한 사용 권한이 있는 사용자는 사용자 지정 도메인을 사용하여 Blob 데이터를 볼 수 있습니다.

## <a name="test-your-custom-domain"></a>사용자 지정 도메인 테스트

사용자 지정 도메인이 Blob service 끝점에 실제로 매핑되었는지 확인하려면 저장소 계정 내의 공용 컨테이너에 Blob을 만듭니다. 그런 다음 웹 브라우저에서 다음 형식의 URI를 사용하여 Blob에 액세스합니다.

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

예를 들어 다음 URI를 사용하여 **photos.contoso.com** 사용자 지정 하위 도메인의 **myforms** 컨테이너에 대한 웹 양식에 액세스할 수 있습니다.

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>사용자 지정 도메인 등록 취소

Blob Storage 끝점에 대한 사용자 지정 도메인 등록을 취소하려면 다음 절차 중 하나를 사용합니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal에서 다음을 수행하여 사용자 지정 도메인 설정을 제거합니다.

1. [Azure Portal](https://portal.azure.com)의 저장소 계정으로 이동합니다.
1. 메뉴 블레이드의 **BLOB SERVICE**에서 **사용자 지정 도메인**을 선택하여 *사용자 지정 도메인* 블레이드를 엽니다.
1. 사용자 지정 도메인 이름이 포함된 텍스트 상자의 콘텐츠를 지웁니다.
1. **저장** 단추를 선택합니다.

사용자 지정 도메인이 성공적으로 제거된 경우 저장소 계정이 성공적으로 업데이트되었음을 알리는 포털 알림이 표시됩니다.

### <a name="azure-cli-20"></a>Azure CLI 2.0

[az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) CLI 명령을 사용하고 `--custom-domain` 인수 값에 빈 문자열(`""`)을 지정하여 사용자 지정 도메인 등록을 제거합니다.

* 명령 형식:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* 명령 예:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

[Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) PowerShell cmdlet을 사용하고 `-CustomDomainName` 인수 값에 빈 문자열(`""`)을 지정하여 사용자 지정 도메인 등록을 제거합니다.

* 명령 형식:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* 명령 예:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>다음 단계
* [사용자 지정 도메인을 Azure CDN(Content Delivery Network) 끝점에 매핑](../../cdn/cdn-map-content-to-custom-domain.md)
* [Azure CDN을 사용하여 HTTPS를 통한 사용자 지정 도메인으로 Blob 액세스](storage-https-custom-domain-cdn.md)
