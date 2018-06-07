# 개요
## [Azure 네트워킹 정보](networking-overview.md)
## 아키텍처
### [가상 데이터 센터](networking-virtual-datacenter.md)
### [여러 네트워크 경로를 포함한 비대칭 라우팅](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [네트워크 설계 보안](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [허브-스포크 토폴로지](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
### [네트워크 보안 모범 사례](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [고가용성 네트워크 가상 어플라이언스](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [부하 분산 방법 결합](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
## 계획 및 디자인
### [가상 네트워크](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [크로스 프레미스 연결 - VPN](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [크로스 프레미스 연결 - 개별 전용](../expressroute/expressroute-workflows.md?toc=%2fazure%2fnetworking%2ftoc.json)

##  개념
### [가상 네트워크](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [네트워크 부하 분산](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [응용 프로그램 부하 분산](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS 기반 트래픽 분산](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [온-프레미스 연결 - VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [온-프레미스 연결 - 전용](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)


# 시작하기
## [첫 가상 네트워크 만들기](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# 방법
## 인터넷 연결
### [네트워크 부하 분산 공용 서버](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [응용 프로그램 부하 분산 공용 서버](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [웹 응용 프로그램 보호](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [위치 간 트래픽 배포](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## 내부 연결
### [네트워크 부하 분산 개인 서버](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [응용 프로그램 부하 분산 개인 서버](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [가상 네트워크 연결(동일한 위치)](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [가상 네트워크 연결(다른 위치)](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## 크로스 프레미스 연결
### [S2S VPN 연결(IPsec/IKE) 만들기](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [P2S VPN 연결(인증서와 SSTP) 만들기](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [개별 전용 연결(ExpressRoute) 만들기](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)

## 관리
### [네트워크 토폴로지 보기](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [패킷 캡처 관리](../network-watcher/network-watcher-packet-capture-manage-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [다음 홉 라우팅 확인](../network-watcher/network-watcher-check-next-hop-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [VM에 대한 IP 흐름 확인](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [네트워크 모니터링 개요](network-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)

## 샘플 스크립트
### [Azure CLI](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

## 자습서
### [VM 부하 분산](../virtual-machines/linux/tutorial-load-balance-nodejs.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [가상 네트워크에 컴퓨터 연결](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# 참고 자료
## [Azure CLI](https://docs.microsoft.com/cli/azure/network)
## [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.js](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# 리소스
## [작성자 템플릿](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=networking)
## [커뮤니티 템플릿](https://azure.microsoft.com/resources/templates/)
## [네트워킹 블로그](http://azure.microsoft.com/blog/topics/networking)
## [가격](https://azure.microsoft.com/pricing)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [국가별 가용성](https://azure.microsoft.com/regions/services/)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [비디오](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

