실행하는 응용 프로그램에 대해 VM을 안전하게 유지하는 것은 중요합니다. VM 보안은 VM에 대한 보안 액세스 및 데이터의 보안 저장소를 포함하는 하나 이상의 Azure 서비스 및 기능을 포함할 수 있습니다. 이 문서에서는 VM 및 응용 프로그램을 안전하게 유지할 수 있도록 하는 정보를 제공합니다.

## <a name="antimalware"></a>맬웨어 방지

클라우드 환경에 대한 최신 위협 환경은 동적이며 규정 준수 및 보안 요구 사항을 충족하기 위해 압력을 늘려서 효과적으로 보호를 유지합니다. [Azure를 위한 Microsoft 맬웨어 방지 프로그램](../articles/security/azure-security-antimalware.md)은 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 및 제거하는 데 도움이 되는 무료 실시간 보호 기능입니다. 알려진 악성 또는 원치 않는 소프트웨어가 VM에서 스스로의 설치나 실행을 시도하는 경우 알리도록 경고를 구성할 수 있습니다.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md)를 통해 VM에 대한 위협을 예방하고 감지하며 대응할 수 있습니다. Security Center는 Azure 구독을 통해 통합된 보안 모니터링 및 정책 관리를 제공하고, 달리 발견되지 않을 수도 있는 위협을 검색하는 데 도움이 되며, 보안 솔루션의 광범위한 에코시스템에서 작동합니다.

## <a name="encryption"></a>암호화

[Windows VM](../articles/virtual-machines/windows/encrypt-disks.md) 및 [Linux VM](../articles/virtual-machines/linux/encrypt-disks.md)의 보안과 규정 준수 상태를 향상시키기 위해 Azure에서 가상 디스크를 암호화할 수 있습니다. Windows VM의 가상 디스크는 미사용 시 Bitlocker를 사용하여 암호화됩니다. Linux VM의 가상 디스크는 미사용 시 dm-crypt를 사용하여 암호화됩니다. 

Azure에서 가상 디스크 암호화는 무료입니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이러한 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다. Azure Active Directory 서비스 사용자는 VM이 켜지고 꺼지는 경우 이러한 암호화 키 발급을 위한 보안 메커니즘을 제공합니다.

## <a name="key-vault-and-ssh-keys"></a>Key Vault 및 SSH 키

비밀 및 인증서는 리소스로 모델링되고 [Key Vault](../articles/key-vault/key-vault-whatis.md)로 제공될 수 있습니다. Azure PowerShell을 사용하여 [Windows VM](../articles/virtual-machines/windows/key-vault-setup.md)에 대한 키 자격 증명 모음 및 [Linux VM](../articles/virtual-machines/linux/key-vault-setup.md)에 대한 Azure CLI를 만들 수 있습니다. 암호화에 대한 키를 만들 수도 있습니다.

키 자격 증명 모음 액세스 정책은 키, 비밀 및 인증서에 대한 권한을 별도로 부여합니다. 예를 들어 사용자에게 키에 대한 액세스 권한만 부여하고 암호에 대해서는 권한을 부여하지 않을 수 있습니다. 그러나 키, 암호 또는 인증서에 대한 액세스 권한은 자격 증명 모음 수준에 있습니다. 즉 [키 자격 증명 모음 액세스 정책](../articles/key-vault/key-vault-secure-your-key-vault.md)에서는 개체 수준 권한을 지원하지 않습니다.

VM에 연결할 때 public-key cryptography를 사용하면 더욱 안전한 방법으로 VM에 로그인할 수 있습니다. 이 프로세스는 사용자를 인증하는 데 있어 사용자 이름과 암호가 아니라 SSH(보안 셸) 명령을 사용하는 공용 및 개인 키의 교환과 관계됩니다. 암호는 무차별 암호 대입 공격, 특히 웹 서버와 같은 인터넷 연결 VM에 대한 공격에 취약합니다. SSH(보안 셸) 키 쌍을 사용하면 인증을 위해 SSH 키를 사용하는 [Linux VM](../articles/virtual-machines/linux/mac-create-ssh-keys.md)을 만들 수 있으며 로그인하기 위해 암호가 필요하지 않게 됩니다. SSH 키를 사용하여 [Windows VM](../articles/virtual-machines/linux/ssh-from-windows.md)에서 Linux VM에 연결할 수도 있습니다.

## <a name="policies"></a>정책

[Azure 정책](../articles/azure-policy/azure-policy-introduction.md)을 사용하여 조직의 [Windows VM](../articles/virtual-machines/windows/policy.md) 및 [Linux VM](../articles/virtual-machines/linux/policy.md)에 대해 원하는 동작을 정의할 수 있습니다. 조직은 정책을 사용하여 엔터프라이즈 전체에 다양한 규칙을 적용할 수 있습니다. 원하는 동작을 적용하여 조직의 성공에 기여함과 동시에 위험을 완화할 수 있습니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

[RBAC(역할 기반 액세스 제어)](../articles/role-based-access-control/overview.md)를 사용하면 팀 내에서 업무를 분리하고 VM에서 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다. VM에서 모든 사람에게 무제한 권한을 제공하는 대신 특정 작업만 허용할 수 있습니다. [Azure CLI](https://docs.microsoft.com/cli/azure/role) 또는[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md)을 사용하여 [Azure Portal](../articles/role-based-access-control/role-assignments-portal.md)에서 VM에 대한 액세스 제어를 구성할 수 있습니다.


## <a name="next-steps"></a>다음 단계
- [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) 또는 [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md)용 Azure Security Center를 사용하여 가상 컴퓨터 보안을 모니터링하는 단계를 안내합니다.