# [Azure Resource Manager 설명서](index.md)

# 개요
## [리소스 관리자란?](resource-group-overview.md)
## [리소스 공급자 및 형식](resource-manager-supported-services.md)
## [Resource Manager 및 클래식 배포](resource-manager-deployment-model.md)
## [구독 거버넌스](/azure/architecture/cloud-adoption-guide/subscription-governance)
## [관리 그룹으로 리소스 구성](management-groups-overview.md)

# 시작하기
## [템플릿 만들기 및 배포](resource-manager-create-first-template.md)
## [템플릿에 VS Code 확장](resource-manager-vscode-extension.md)
## [Resource Manager와 Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# 방법
## 템플릿 만들기
### [템플릿 섹션](resource-group-authoring-templates.md)
#### [매개 변수](resource-manager-templates-parameters.md)
#### [변수](resource-manager-templates-variables.md)
#### [리소스](resource-manager-templates-resources.md)
#### [Outputs](resource-manager-templates-outputs.md)
### [연결된 템플릿 및 중첩된 템플릿](resource-group-linked-templates.md)
### [리소스 간 종속성 정의](resource-group-define-dependencies.md)
### [여러 인스턴스 만들기](resource-group-create-multiple.md)
### [리소스 업데이트](/azure/architecture/building-blocks/extending-templates/update-resource)

## 배포
### Azure PowerShell
#### [템플릿 배포](resource-group-template-deploy.md)
#### [SAS 토큰으로 개인 템플릿 배포](resource-manager-powershell-sas-token.md)
#### [템플릿 내보내기 및 재배포](resource-manager-export-template-powershell.md)
### Azure CLI
#### [템플릿 배포](resource-group-template-deploy-cli.md)
#### [SAS 토큰으로 개인 템플릿 배포](resource-manager-cli-sas-token.md)
#### [템플릿 내보내기 및 재배포](resource-manager-export-template-cli.md)
### Azure portal
#### [리소스 배포](resource-group-template-deploy-portal.md)
#### [템플릿 내보내기](resource-manager-export-template.md)
### [REST API](resource-group-template-deploy-rest.md)
### [여러 리소스 그룹 또는 구독](resource-manager-cross-resource-group-deployment.md)
### [Visual Studio Team Services와의 지속적인 통합](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)

## 관리
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Azure Portal](resource-group-portal.md)
### [태그를 사용하여 리소스 구성](resource-group-using-tags.md)
### [새 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)
### [EA 구독을 프로그래밍 방식으로 만들기](programmatically-create-subscription.md)
### [관리 그룹 만들기](management-groups-create.md)
### [관리 그룹 관리](management-groups-manage.md)

## 액세스 제어
### 서비스 주체 만들기
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure Portal](resource-group-create-service-principal-portal.md)
### [구독에 액세스하는 인증 API](resource-manager-api-authentication.md)
### [리소스 잠금](resource-group-lock-resources.md)

## 감사
### [활동 로그 보기](resource-group-audit.md)
### [배포 작업 보기](resource-manager-deployment-operations.md)

## 문제 해결
### [배포 오류 문제 해결](resource-manager-common-deployment-errors.md)
### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
### [InvalidTemplate](resource-manager-invalid-template-errors.md)
### [Linux 배포 문제](../virtual-machines/linux/troubleshoot-deploy-vm.md)
### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
### [NotFound](resource-manager-not-found-errors.md)
### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
### [Linux의 프로비전 및 할당 문제](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
### [Windows의 프로비전 및 할당 문제](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
### [ReservedResourceName](resource-manager-reserved-resource-name.md)
### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### [Windows 배포 문제](../virtual-machines/windows/troubleshoot-deploy-vm.md)

# 참고 자료
## [템플릿 형식](/azure/templates/)
## [템플릿 함수](resource-group-template-functions.md)
### [배열 및 개체 함수](resource-group-template-functions-array.md)
### [비교 함수](resource-group-template-functions-comparison.md)
### [배포 함수](resource-group-template-functions-deployment.md)
### [논리 함수](resource-group-template-functions-logical.md)
### [숫자 함수](resource-group-template-functions-numeric.md)
### [리소스 함수](resource-group-template-functions-resource.md)
### [문자열 함수](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [개인 데이터 관리](resource-manager-personal-data.md)
## [제한 요청](resource-manager-request-limits.md)
## [비동기 작업 추적](resource-manager-async-operations.md)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
