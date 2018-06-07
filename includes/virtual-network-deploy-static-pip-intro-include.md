Azure에서 VM(가상 머신)을 만들고 공용 IP 주소를 사용하여 공용 인터넷에 공개할 수 있습니다. 기본적으로 공용 IP는 동적이며 이와 연결된 주소는 VM이 삭제되거나 중지/할당 취소될 때 변경될 수 있습니다. VM이 항상 동일한 공용 IP 주소를 사용하도록 하려면 고정 공용 IP를 만들어야 합니다. 

VM에서 고정 공용 IP를 구현하려면 먼저 고정 공용 IP의 사용 가능 시점 및 사용 방법을 파악해야 합니다. Azure의 IP 주소 지정에 대한 자세한 내용은 [IP 주소 지정 개요](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) 를 참조하세요.

