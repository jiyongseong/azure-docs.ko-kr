| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| [리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) 당 리소스(리소스 유형당) |800 |리소스 유형마다 다름 |
| 배포 기록의 리소스 그룹당 배포 |800 |800 |
| 배포당 리소스 |800 |800 |
| 관리 잠금(고유 범위당) |20 |20 |
| 태그 수(리소스당 또는 리소스 그룹당) |15 |15 |
| 태그 키 길이 |512 |512 |
| 태그 값 길이 |256 |256 |


#### <a name="template-limits"></a>템플릿 제한

| 값 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 매개 변수 |256 |256 |
| variables |256 |256 |
| 리소스(인쇄 매수 포함) |800 |800 |
| outputs |64 |64 |
| 템플릿 식 |24,576개 문자 |24,576개 문자 |
| 내보낸 템플릿의 리소스 |200 |200 | 
| 템플릿 크기 |1MB |1MB |
| 매개 변수 파일 크기 |64KB |64KB |

중첩된 템플릿을 사용하여 일부 템플릿 제한을 초과할 수 있습니다. 자세한 내용은 [Azure 리소스를 배포할 때 연결된 템플릿 사용](../articles/azure-resource-manager/resource-group-linked-templates.md)을 참조하세요. 매개 변수, 변수 또는 출력의 수를 줄이려면 개체에 여러 값을 결합할 수 있습니다. 자세한 내용은 [매개 변수로 개체 사용](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)을 참조하세요.

리소스 그룹당 800개 배포 제한에 도달하면 기록에서 더 이상 필요하지 않은 배포를 삭제합니다. Azure CLI의 경우 [az group deployment delete](/cli/azure/group/deployment#az_group_deployment_delete) 또는 PowerShell에서 [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment)를 사용하여 기록에서 항목을 삭제할 수 있습니다. 배포 기록에서 항목을 삭제해도 배포 리소스에는 영향을 주지 않습니다. 