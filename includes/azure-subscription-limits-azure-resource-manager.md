| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md) |지역당 10,000 <sup>1</sup> |지역당 10,000 |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 VM 총 코어 수 |지역당 20<sup>1</sup> | 지원에 문의 |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 시리즈(Dv2, F 등) 코어당 VM |지역당 20<sup>1</sup> | 지원에 문의 |
| [공동 관리자](../articles/billing-add-change-azure-subscription-administrator.md)  |Unlimited |Unlimited |
| [Storage 계정](../articles/storage/common/storage-create-storage-account.md)  |200 |200<sup>2</sup> |
| 구독당 [리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md) |980 |980 |
| [가용성 집합](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)  |지역당 2,000개 |지역당 2,000개 |
| 리소스 관리자 API 읽기 |시간당 15,000개 |시간당 15,000개 |
| 리소스 관리자 API 쓰기 |시간당 1,200개 |시간당 1,200개 |
| 리소스 관리자 API 요청 크기 |4,194,304바이트 |4,194,304바이트 |
| 구독당 태그 수<sup>3</sup> |무제한 |무제한 |
| 구독당 고유한 태그 계산<sup>3</sup> | 10000 | 10000 |
| [클라우드 서비스](../articles/cloud-services/cloud-services-choose-me.md)  |해당 사항 없음<sup>4</sup> |해당 사항 없음<sup>4</sup> |
| [선호도 그룹](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md)  |해당 사항 없음<sup>4</sup> |해당 사항 없음<sup>4</sup> |

<sup>1</sup>기본 제한은 평가판, 종량제 및 Dv2, F, G 등의 시리즈와 같은 제품 범주 유형에 따라 다릅니다.

<sup>2</sup>표준 및 프리미엄 저장소 계정이 모두 포함됩니다. 저장소 계정이 200개 이상 필요한 경우 [Azure 지원](https://azure.microsoft.com/support/faq/)에 요청합니다. Azure Storage 팀이 비즈니스 사례를 검토하고 Storage 계정을 250개까지 승인할 수 있습니다.

<sup>3</sup>구독당 무제한의 태그를 적용할 수 있습니다. 리소스당 또는 리소스 그룹당 태그 수는 15개로 제한됩니다. 리소스 관리자는 태그 수가 10,000개 이하일 경우에만 구독에 [고유한 태그 이름 및 값의 목록](/rest/api/resources/tags#Tags_List)을 반환합니다. 하지만 이 숫자가 10,000을 초과하는 경우 여전히 태그로 리소스를 찾을 수 있습니다.  

<sup>4</sup>Azure 리소스 그룹 및 Azure Resource Manager를 사용하면 이러한 기능이 더 이상 필요하지 않습니다.

> [!NOTE]
> 가상 컴퓨터 코어에 별도로 적용되는 지역 크기 시리즈(Dv2, F 등)당 한도뿐만 아니라 지역 총 한도가 있다는 것을 강조하는 것이 중요합니다.  미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다.  이러한 구독은 A1 VM 30개 또는 D1 VM 30개 또는 총 코어가 30개를 초과하지 않는 두 가지의 조합(예: A1 VM 10, D1 VM 20)을 배포하도록 허용됩니다.  
> <!-- -->
> 
> 

