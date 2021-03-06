---
title: "Azure에서 Windows Service Fabric 클러스터 만들기 | Microsoft Docs"
description: "PowerShell을 사용하여 기존 Azure 가상 네트워크에 Windows Service Fabric 클러스터를 배포하는 방법을 알아봅니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: b3bab57f5ca6627b4532284376a9809d5ab543f2
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Azure 가상 네트워크에 Service Fabric Windows 클러스터 배포
이 자습서는 시리즈의 1부입니다. PowerShell을 사용하여 기존 Azure VNET(가상 네트워크)에 Windows Service Fabric 클러스터를 배포하는 방법을 알아봅니다. 작업이 완료되면 응용 프로그램을 배포할 수 있는, 클라우드에서 실행되는 클러스터가 생깁니다.  Azure CLI를 사용하여 Linux 클러스터를 만들려면 [Azure에서 보안 Linux 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * PowerShell을 사용하여 Azure에서 VNET 만들기
> * 키 자격 증명 모음 만들기 및 인증서 업로드
> * Azure PowerShell에서 보안 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * PowerShell을 사용하여 클러스터에 연결
> * 클러스터 제거

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure에서 보안 클러스터 만들기
> * [Service Fabric을 사용하여 API Management 배포](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Service Fabric SDK 및 PowerShell 모듈](service-fabric-get-started.md)을 설치합니다.
- [Azure PowerShell 모듈 버전 4.1 이상](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 설치합니다.

다음 절차에서는 5노드 Service Fabric 클러스터를 만듭니다. Azure에서 Service Fabric 클러스터를 실행할 때 발생하는 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure에 로그인 및 구독 선택
이 가이드에서는 Azure PowerShell을 사용합니다. 새로 PowerShell 세션을 시작하려면 Azure 계정에 로그인한 후 Azure 명령을 실행하기 전에 구독을 선택합니다.
 
다음 스크립트를 실행하여 Azure 계정에 로그인하고 구독을 선택합니다.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
배포에 새 리소스 그룹을 만들고 이름과 위치를 지정합니다.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>네트워크 토폴로지를 배포합니다.
다음으로, API Management 및 Service Fabric 클러스터가 배포될 네트워크 토폴로지를 설정합니다. [network.json][network-arm] Resource Manager 템플릿은 VNET(가상 네트워크), Service Fabric에 대한 서브넷 및 NSG(네트워크 보안 그룹), API Management에 대한 서브넷 및 NSG를 만들도록 구성되었습니다. VNET, 서브넷 및 NSG에 대한 자세한 내용은 [여기](../virtual-network/virtual-networks-overview.md)를 참조하세요.

[network.parameters.json][network-parameters-arm] 매개 변수 파일에는 Service Fabric 및 API Management가 배포되는 서브넷 및 NSG의 이름이 포함되어 있습니다.  API Management는 [다음 자습서](service-fabric-tutorial-deploy-api-management.md)에서 배포됩니다. 이 가이드에서는 매개 변수 값을 변경할 필요가 없습니다. 이러한 값은 Service Fabric Resource Manager 템플릿에서 사용됩니다.  여기서 값은 수정하는 경우 이 자습서 및 [API Management 배포 자습서](service-fabric-tutorial-deploy-api-management.md)에서 사용된 다른 Resource Manager 템플릿에서 수정해야 합니다. 

다음 Resource Manager 템플릿 및 매개 변수 파일을 다운로드합니다.
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

다음 PowerShell 명령을 사용하여 네트워크 설정에 대해 Resource Manager 템플릿 및 매개 변수 파일을 배포합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Service Fabric 클러스터 배포
네트워크 리소스에서 배포가 완료되면 다음 단계는 Service Fabric 클러스터용으로 지정된 서브넷 및 NSG의 VNET에 Service Fabric 클러스터를 배포하는 것입니다. 이 문서의 앞부분에서 배포된 기존 VNET과 서브넷에 클러스터를 배포하려면 Resource Manager 템플릿이 필요합니다.  자세한 내용은 [Azure Resource Manager를 사용하여 클러스터 만드기](service-fabric-cluster-creation-via-arm.md)를 참조하세요. 이 자습서 시리즈에서는 이전 단계에서 설정한 VNET, 서브넷 및 NSG의 이름을 사용하도록 템플릿을 미리 구성했습니다.  다음 Resource Manager 템플릿 및 매개 변수 파일을 다운로드합니다.
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

인증서는 클러스터의 노드 간 통신을 보안하고 Service Fabric 클러스터에 대한 사용자 액세스를 관리하는 데 사용됩니다. 또한 API Management는 이 인증서를 사용하여 서비스 검색을 위해 Service Fabric 명명 서비스에 액세스합니다. 

다음 스크립트는 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) cmdlet을 사용하여 Azure에서 새 클러스터를 배포합니다. 또한 이 cmdlet은 Azure에서 Key Vault를 만들고, 자체 서명된 인증서 및 Key Vault를 만들고, 인증서 파일을 로컬로 다운로드합니다.   

```powershell
# Certificate variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"

# Variables for VM admin.
$adminuser="vmadmin"
$adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

# Variables for common values
$clustername = "mysfcluster"
$vmsku = "Standard_D2_v2"
$vaultname = "clusterkeyvault"
$vaultgroupname="clusterkeyvaultgroup"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Set the number of cluster nodes. Possible values: 1, 3-99
$clustersize=5 

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname `
-TemplateFile .\cluster.json -ParameterFile .\cluster.parameters.json
```

## <a name="connect-to-the-secure-cluster"></a>보안 클러스터에 연결
Service Fabric SDK가 설치된 Service Fabric PowerShell 모듈을 사용하여 클러스터에 연결합니다.  먼저 사용자 컴퓨터에서 현재 사용자의 개인(내) 저장소에 인증서를 설치합니다.  다음 PowerShell 명령을 실행합니다.

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

이제 보안 클러스터에 연결할 준비가 되었습니다.

**Service Fabric** PowerShell 모듈은 Service Fabric 클러스터, 응용 프로그램 및 서비스를 관리하기 위한 많은 cmdlet을 제공합니다.  [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet을 사용하여 보안 클러스터에 연결합니다. 인증서 지문 및 연결 끝점 세부 정보는 이전 단계의 출력에 있습니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

[Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet을 사용하여 사용자가 연결되어 있고 클러스터가 정상 상태인지 확인합니다.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>리소스 정리
이 자습서 시리즈의 다른 문서에서는 방금 만든 클러스터를 사용합니다. 다음 문서로 바로 이동하지 않는 경우 요금이 발생하지 않도록 클러스터 및 Key Vault를 삭제하는 것이 좋습니다. 클러스터 및 클러스터에서 사용하는 모든 리소스를 삭제하는 가장 간단한 방법은 리소스 그룹을 삭제하는 것입니다.

[Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 모든 클러스터 리소스를 삭제합니다.  또한 Key Vault를 포함하는 리소스 그룹을 삭제합니다.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * PowerShell을 사용하여 Azure에서 VNET 만들기
> * 키 자격 증명 모음 만들기 및 인증서 업로드
> * PowerShell을 사용하여 Azure에서 안전한 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * PowerShell을 사용하여 클러스터에 연결
> * 클러스터 제거

이제 다음 자습서를 진행하여 Service Fabric을 사용하여 API Management를 배포하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [API Management 배포](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
