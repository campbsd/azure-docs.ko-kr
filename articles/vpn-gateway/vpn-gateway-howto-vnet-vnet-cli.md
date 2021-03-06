---
title: "가상 네트워크를 다른 VNet에 연결: Azure CLI | Microsoft Docs"
description: "이 문서에서는 Azure Resource Manager 및 Azure CLI를 사용하여 가상 네트워크를 함께 연결하는 과정을 안내합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: cherylmc
ms.openlocfilehash: ff859bd9dbbf30c461cdba8409c77b04ff97b1f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Azure CLI를 사용하여 VNet 간 VPN 게이트웨이 연결 구성

이 문서에서는 가상 네트워크 간에 VPN Gateway 연결을 만드는 방법을 보여 줍니다. 가상 네트워크는 같은 또는 다른 구독의 같은 지역에 있을 수도 있고 다른 지역에 있을 수도 있습니다. 다른 구독의 VNet을 연결할 때 구독은 동일한 Active Directory 테넌트와 연결될 필요가 없습니다. 

이 문서의 단계는 Resource Manager 배포 모델에 적용되며 Azure CLI를 사용합니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal(클래식)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [다양한 배포 모델 간 연결 - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [다양한 배포 모델 간 연결 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

가상 네트워크를 다른 가상 네트워크에 연결(VNet-VNet)하는 것은 VNet을 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다. VNet이 동일한 지역에 있는 경우 VNet 피어링을 사용하여 연결하려고 할 수 있습니다. VNet 피어링은 VPN Gateway를 사용하지 않습니다. 자세한 내용은 [VNet 피어링](../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

VNet-VNet 통신을 다중 사이트 구성과 결합할 수 있습니다. 이렇게 하면 다음 다이어그램에 표시된 것처럼 프레미스 간 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.

![연결 정보](./media/vpn-gateway-howto-vnet-vnet-cli/aboutconnections.png)

### <a name="why"></a>가상 네트워크에 연결하는 이유

다음과 같은 이유로 가상 네트워크에 연결할 수 있습니다.

* **지역 간 지리적 중복 및 지리적 상태**

  * 인터넷 연결 끝점으로 이동하지 않고도 보안 연결을 통해 지역에서 복제 또는 동기화를 직접 설정할 수 있습니다.
  * Azure Traffic Manager 및 부하 분산 장치를 사용하여 여러 Azure 지역 간의 지리적 중복을 통해 워크로드의 가용성을 높게 설정할 수 있습니다. 이러한 작업의 한 가지 주요 예는 여러 Azure 지역에 분산된 가용성 그룹을 사용하여 SQL AlwaysOn을 설정하는 것입니다.
* **분리 또는 관리 경계를 가진 지역별 다중 계층 응용 프로그램**

  * 같은 지역 내에서 분리 또는 관리 요구 사항 때문에 여러 가상 네트워크가 함께 연결된 다중 계층 응용 프로그램을 설정할 수 있습니다.

VNet 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [VNet 간 FAQ](#faq)를 참조하세요.

### <a name="which-set-of-steps-should-i-use"></a>어느 단계 집합을 사용해야 합니까?

이 문서에서는 서로 다른 두 집합의 단계를 볼 수 있습니다. 일련의 단계는 [동일한 구독에 상주하는 VNet](#samesub)이고 다른 단계는 [다른 구독에 상주하는 VNet](#difsub)입니다.

## <a name="samesub"></a>같은 구독에 있는 VNet 연결

![v2v 다이어그램](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

### <a name="before-you-begin"></a>시작하기 전에

시작하기 전에 최신 버전의 CLI 명령(2.0 이상)을 설치합니다. CLI 명령을 설치하는 방법에 대한 자세한 내용은 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

### <a name="Plan"></a>IP 주소 범위 계획

다음 단계에서는 두 개의 가상 네트워크와 해당 게이트웨이 서브넷 및 구성을 만듭니다. 그런 다음 두 VNet 간의 VPN 연결을 만듭니다. 네트워크 구성에 대한 IP 주소 범위를 계획하는 것이 중요합니다. 따라서 VNet 범위 또는 로컬 네트워크 범위가 겹치지 않는지 확인해야 합니다. 이 예에서는 DNS 서버를 포함하지 않습니다. 가상 네트워크의 이름 확인을 원하는 경우 [이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

예제에서 다음 값을 사용합니다.

**TestVNet1에 대한 값:**

* VNet 이름: TestVNet1
* 리소스 그룹: TestRG1
* 위치: 미국 동부
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* 프런트 엔드: 10.11.0.0/24
* 백 엔드: 10.12.0.0/24
* 게이트웨이 서브넷 = 10.12.255.0/27
* 게이트웨이 이름: VNet1GW
* 공용 IP: VNet1GWIP
* VpnType: 경로 기반
* 연결(1 대 4): VNet1 대 VNet4
* 연결(1 대 5): VNet1 대 VNet5
* 연결 유형: VNet 간

**TestVNet4에 대한 값:**

* VNet 이름: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* 프런트 엔드: 10.41.0.0/24
* 백 엔드: 10.42.0.0/24
* 게이트웨이 서브넷: 10.42.255.0/27
* 리소스 그룹: TestRG4
* 위치: 미국 서부
* 게이트웨이 이름: VNet4GW
* 공용 IP: VNet4GWIP
* VpnType: 경로 기반
* 연결: VNet4 대 VNet1
* 연결 유형: VNet 간


### <a name="Connect"></a>1단계 - 구독에 연결

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="TestVNet1"></a>2단계 - TestVNet1 만들기 및 구성

1. 리소스 그룹을 만듭니다.

  ```azurecli
  az group create -n TestRG1  -l eastus
  ```
2. TestVNet1 및 TestVNet1의 서브넷을 만듭니다. 이 예제에서는 TestVNet1이라는 가상 네트워크와 FrontEnd라는 서브넷을 만듭니다.

  ```azurecli
  az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
  ```
3. 백 엔드 서브넷에 대한 추가 주소 공간을 만듭니다. 이 단계에서는 앞에서 만든 주소 공간과 추가하려는 추가 주소 공간을 모두 지정합니다. [az network vnet update](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_update) 명령이 이전 설정을 덮어쓰기 때문입니다. 이 명령을 사용할 때 모든 주소 접두사를 지정해야 합니다.

  ```azurecli
  az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
  ```
4. 백 엔드 서브넷을 만듭니다.
  
  ```azurecli
  az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
  ```
5. 게이트웨이 서브넷을 만듭니다. 게이트웨이 서브넷의 이름은 'GatewaySubnet'입니다. 이 이름은 필수입니다. 이 예제에서 게이트웨이 서브넷은 /27을 사용합니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 적어도 /28 또는 /27을 선택하여 더 많은 주소를 포함하는 큰 서브넷을 만드는 것이 좋습니다. 이렇게 하면 나중에 필요할 수도 있는 추가 구성에 맞게 충분히 주소를 사용할 수 있습니다.

  ```azurecli 
  az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
  ```
6. VNet용으로 만들 게이트웨이에 할당할 공용 IP 주소를 요청합니다. AllocationMethod가 동적인지 확인합니다. 사용할 IP 주소를 지정할 수는 없습니다. IP 주소는 게이트웨이에 동적으로 할당됩니다.

  ```azurecli
  az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
  ```
7. TestVNet1용 가상 네트워크 게이트웨이를 만듭니다. VNet-VNet 구성에는 RouteBased VpnType이 필요합니다. '--no-wait' 매개 변수를 사용하여 이 명령을 실행하면 피드백 또는 출력이 보이지 않습니다. '--no-wait' 매개 변수는 백그라운드에서 게이트웨이를 만드는 것을 허용합니다. VPN 게이트웨이가 만들기를 즉시 완료한다는 의미는 아닙니다. 게이트웨이 만들기는 사용하는 게이트웨이 SKU에 따라 45분 이상이 걸릴 수 있습니다.

  ```azurecli
  az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="TestVNet4"></a>3단계 - TestVNet4 만들기 및 구성

1. 리소스 그룹을 만듭니다.

  ```azurecli
  az group create -n TestRG4  -l westus
  ```
2. TestVNet4 만들기.

  ```azurecli
  az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
  ```

3. TestVNet4의 추가 서브넷을 만듭니다.

  ```azurecli
  az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
  az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
  ```
4. 게이트웨이 서브넷을 만듭니다.

  ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
  ```
5. 공용 IP 주소를 요청합니다.

  ```azurecli
  az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
  ```
6. TestVNet4 가상 네트워크 게이트웨이를 만듭니다.

  ```azurecli
  az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="createconnect"></a>4단계 - 연결 만들기

이제 VPN 게이트웨이가 있는 VNet이 두 개 있습니다. 다음 단계는 가상 네트워크 게이트웨이간에 VPN 게이트웨이 연결을 만드는 것입니다. 위의 예제를 사용했다면 VNet 게이트웨이는 다른 리소스 그룹에 있습니다. 게이트웨이가 다른 리소스 그룹에 있는 경우, 연결할 때 각 게이트웨이의 리소스 ID를 식별하고 지정해야 합니다. VNet이 동일한 리소스 그룹에 있는 경우 리소스 ID를 지정할 필요가 없으므로 [두 번째 지침](#samerg)을 사용할 수 있습니다.

### <a name="diffrg"></a>다른 리소스 그룹에 상주하는 VNet을 연결하려면

1. 다음 명령의 출력에서 VNet1GW의 리소스 ID를 가져옵니다.

  ```azurecli
  az network vnet-gateway show -n VNet1GW -g TestRG1
  ```

  출력에서 "id:" 줄을 찾습니다. 따옴표 안의 값은 다음 섹션에서 연결을 만드는 데 필요합니다. 연결을 만들 때 쉽게 붙여 넣을 수 있도록 이 값을 텍스트 편집기(예: 메모장)에 복사합니다.

  예제 출력:

  ```
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
  "enableBgp": false, 
  "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
  "ipConfigurations":
  ```

  따옴표 안에 있는 **"id":** 뒤의 값을 복사합니다.

  ```
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
 ```

2. VNet4GW의 리소스 ID를 가져와서 텍스트 편집기에 값을 복사합니다.

  ```azurecli
  az network vnet-gateway show -n VNet4GW -g TestRG4
  ```

3. TestVNet1 대 TestVNet4 연결을 만듭니다. 이 단계에서는 TestVNet1에서 TestVNet4까지 연결을 만듭니다. 예제에 참조된 공유 키가 있습니다. 공유 키에 대해 고유한 값을 사용할 수 있습니다. 중요한 점은 두 연결에서 모두 공유 키가 일치해야 한다는 것입니다. 연결을 만드는 데는 시간이 걸립니다.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
  ```
4. TestVNet4 대 TestVNet1 연결을 만듭니다. 이 단계는 TestVNet4에서 TestVNet1까지 연결을 만드는 점을 제외하면 위의 비슷합니다. 공유된 키가 일치하는지 확인합니다. 연결을 설정하는 데 몇 분이 소요됩니다.

  ```azurecli
  az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
  ```
5. 연결을 확인합니다. [연결 확인](#verify)을 참조하세요.

### <a name="samerg"></a>동일한 리소스 그룹에 상주하는 VNet을 연결하려면

1. TestVNet1 대 TestVNet4 연결을 만듭니다. 이 단계에서는 TestVNet1에서 TestVNet4까지 연결을 만듭니다. 예제에 리소스 그룹이 동일하다는 점에 유의하세요. 예제에서 참조된 공유 키도 볼 수 있습니다. 공유 키에 대해 고유한 값을 사용할 수 있지만 공유 키는 두 연결에 모두 일치해야 합니다. 연결을 만드는 데는 시간이 걸립니다.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
  ```
2. TestVNet4 대 TestVNet1 연결을 만듭니다. 이 단계는 TestVNet4에서 TestVNet1까지 연결을 만드는 점을 제외하면 위의 비슷합니다. 공유된 키가 일치하는지 확인합니다. 연결을 설정하는 데 몇 분이 소요됩니다.

  ```azurecli
  az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
  ```
3. 연결을 확인합니다. [연결 확인](#verify)을 참조하세요.

## <a name="difsub"></a>다른 구독에 있는 VNet 연결

![v2v 다이어그램](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)

이 시나리오에서는 TestVNet1 및 TestVNet5를 연결합니다. VNet이 다른 구독에 상주합니다. 구독은 동일한 Active Directory 테넌트와 연결될 필요가 없습니다. 이 구성 단계에서는 TestVNet1을 TestVNet5에 연결하기 위해 VNet 간 연결을 추가합니다.

### <a name="TestVNet1diff"></a>5단계 - TestVNet1 만들기 및 구성

이 지침은 이전 섹션의 단계에서 계속됩니다. TestVNet1 및 TestVNet1용 VPN Gateway를 만들고 구성하려면 [1단계](#Connect) 및 [2단계](#TestVNet1)를 완료해야 합니다. 이 구성의 경우 이전 섹션에서 TestVNet4를 만들 필요가 없지만 만든다고 해도 이 단계와 충돌하지 않습니다. 1단계와 2단계를 완료하면 6단계(아래)를 계속 진행합니다.

### <a name="verifyranges"></a>6단계 - IP 주소 범위 확인

추가 연결을 만들 때 새 가상 네트워크의 IP 주소 공간이 다른 VNet 범위 또는 로컬 네트워크 게이트웨이 범위와 겹치지 않는지 확인하는 것이 중요합니다. 이 연습에서는 TestVNet5에 대해 다음 값을 사용할 수 있습니다.

**TestVNet5에 대한 값:**

* VNet 이름: TestVNet5
* 리소스 그룹: TestRG5
* 위치: 일본 동부
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* 프런트 엔드: 10.51.0.0/24
* 백 엔드: 10.52.0.0/24
* 게이트웨이 서브넷: 10.52.255.0.0/27
* 게이트웨이 이름: VNet5GW
* 공용 IP: VNet5GWIP
* VpnType: 경로 기반
* 연결: VNet5 대 VNet1
* 연결 유형: VNet 간

### <a name="TestVNet5"></a>7단계 - TestVNet5 만들기 및 구성

이 단계는 새 구독인 구독5의 상황에서 수행해야 합니다. 이 부분은 구독을 소유한 다른 조직의 관리자가 수행할 수 있습니다. 구독을 전환하려면 'az account list --all'을 사용하여 계정에서 사용할 수 있는 구독을 나열한 다음 'az account set –subscription <subscriptionID>'를 사용하여 사용할 구독으로 전환합니다.

1. 구독 5에 연결되어 있는지 확인한 다음 리소스 그룹을 만듭니다.

  ```azurecli
  az group create -n TestRG5  -l japaneast
  ```
2. TestVNet5 만들기.

  ```azurecli
  az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
  ```

3. 서브넷을 추가합니다.

  ```azurecli
  az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
  az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
  ```

4. 게이트웨이 서브넷을 추가합니다.

  ```azurecli
  az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
  ```

5. 공용 IP 주소를 요청합니다.

  ```azurecli
  az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
  ```
6. TestVNet5 게이트웨이 만들기

  ```azurecli
  az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="connections5"></a>8단계 - 연결 만들기

게이트웨이가 다른 구독에 있으므로 이 단계를 **[구독 1]** 및 **[구독 5]**로 표시된 두 개의 CLI 세션으로 분할합니다. 구독을 전환하려면 'az account list --all'을 사용하여 계정에서 사용할 수 있는 구독을 나열한 다음 'az account set –subscription <subscriptionID>'를 사용하여 사용할 구독으로 전환합니다.

1. **[구독 1]** 로그인하고 구독 1에 연결합니다. 다음 명령을 실행하여 출력에서 게이트웨이의 이름과 ID를 가져옵니다.

  ```azurecli
  az network vnet-gateway show -n VNet1GW -g TestRG1
  ```

  "id:"에 대한 출력을 복사합니다. 전자 메일 또는 다른 방법을 통해 VNet 게이트웨이(VNet1GW)의 ID와 이름을 구독 5 관리자에게 보냅니다.

  예제 출력:

  ```
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
  ```

2. **[구독 5]** 로그인하고 구독 5에 연결합니다. 다음 명령을 실행하여 출력에서 게이트웨이의 이름과 ID를 가져옵니다.

  ```azurecli
  az network vnet-gateway show -n VNet5GW -g TestRG5
  ```

  "id:"에 대한 출력을 복사합니다. 전자 메일 또는 다른 방법을 통해 VNet 게이트웨이(VNet5GW)의 ID와 이름을 구독 1 관리자에게 보냅니다.

3. **[구독 1]** 이 단계에서는 TestVNet1에서 TestVNet5까지 연결을 만듭니다. 공유 키에 대해 고유한 값을 사용할 수 있지만 공유 키는 두 연결에 모두 일치해야 합니다. 연결 만들기는 완료하는 데 꽤 오래 걸릴 수 있습니다. 구독 1에 연결해야 합니다.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```

4. **[구독 5]** 이 단계는 TestVNet5에서 TestVNet1까지 연결을 만드는 점을 제외하면 위와 비슷합니다. 공유 키가 일치하고 구독 5에 연결했는지 확인합니다.

  ```azurecli
  az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```

## <a name="verify"></a>연결 확인
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections v2v cli](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="faq"></a>VNet 간 FAQ
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>다음 단계

* 연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [Virtual Machines 설명서](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요.
* BGP에 대한 내용은 [BGP 개요](vpn-gateway-bgp-overview.md) 및 [BGP를 구성하는 방법](vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요.
