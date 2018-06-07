## <a name="scenario"></a>시나리오
단일 NIC가 있는 VM을 만들고 가상 네트워크에 연결합니다. VM은 세 개의 *개인* IP 주소와 두 개의 *공용* IP 주소가 필요합니다. IP 주소는 다음 IP 구성에 할당됩니다.

* **IPConfig-1:** *정적* 개인 IP 주소와 *정적* 공용 IP 주소를 할당합니다.
* **IPConfig-2:** *정적* 개인 IP 주소와 *정적* 공용 IP 주소를 할당합니다.
* **IPConfig-3:** *정적* 개인 IP 주소를 할당하고 공용 IP 주소를 할당하지 않습니다.
  
    ![여러 IP 주소](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

NIC를 만들 때 IP 구성이 NIC에 연결되고 VM을 만들 때 NIC가 VM에 연결됩니다. 시나리오에 사용되는 IP 주소 유형은 예시입니다. 필요한 모든 IP 주소 및 할당 유형을 지정할 수 있습니다.

> [!NOTE]
> 이 문서의 단계를 통해 모든 IP 구성을 단일 NIC에 할당하면 여러 IP 구성도 다중 NIC VM의 모든 NIC에 할당할 수 있습니다. 여러 NIC로 VM을 만드는 방법에 대해 자세히 알아보려면 [여러 NIC를 사용하여 VM 만들기](../articles/virtual-machines/windows/multiple-nics.md) 문서를 참조하세요.