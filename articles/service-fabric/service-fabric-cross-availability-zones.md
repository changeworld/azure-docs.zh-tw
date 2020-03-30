---
title: 跨可用性區域部署群集
description: 瞭解如何跨可用性區域創建 Azure 服務結構群集。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609973"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>跨可用性區域部署 Azure 服務結構群集
Azure 中的可用性區域是一種高可用性產品，可保護應用程式和資料免受資料中心故障的影響。 可用性區域是一個獨特的物理位置，在 Azure 區域內配備獨立的電源、冷卻和網路。

Service Fabric 支援通過部署固定到特定區域的節點類型跨可用性區域的群集。 這將確保應用程式的高度可用性。 Azure 可用性區域僅在特定區域可用。 有關詳細資訊，請參閱[Azure 可用性區域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。

提供示例範本：[服務結構跨可用性區域範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>跨可用性區域跨越的 Azure 服務結構群集的主節點類型的建議拓撲
分佈在可用性區域的服務結構群集可確保群集狀態的高可用性。 要跨區域跨服務結構群集，必須在區域支援的每個可用區域中創建主節點類型。 這將在每個主節點類型之間均勻分佈種子節點。

主節點類型的建議拓撲需要下面概述的資源：

* 群集可靠性級別設置為白金級。
* 標記為主節點的三個節點類型。
    * 每個節點類型應映射到位於不同區域的自己的虛擬機器規模集。
    * 每個虛擬機器規模集應至少有五個節點（銀耐久性）。
* 使用標準 SKU 的單個公共 IP 資源。
* 使用標準 SKU 的單個負載等化器資源。
* 部署虛擬機器縮放集的子網引用的 NSG。

>[!NOTE]
> 虛擬機器縮放集單個放置組屬性必須設置為 true，因為 Service Fabric 不支援跨區域的單個虛擬機器規模集。

 ![Azure 服務結構可用性區域體系結構][sf-architecture]

## <a name="networking-requirements"></a>網路需求
### <a name="public-ip-and-load-balancer-resource"></a>公共 IP 和負載等化器資源
要在虛擬機器縮放集資源上啟用區域屬性，該虛擬機器規模集引用的負載等化器和 IP 資源必須同時使用*標準*SKU。 在沒有 SKU 屬性的情況下創建負載等化器或 IP 資源將創建基本 SKU，該功能庫不支援可用性區域。 預設情況下，標準 SKU 負載等化器將阻止來自外部的所有流量;為了允許外部流量，必須將 NSG 部署到子網。

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> 無法對公共 IP 和負載等化器資源進行 SKU 的就地更改。 如果要從具有基本 SKU 的現有資源進行遷移，請參閱本文的遷移部分。

### <a name="virtual-machine-scale-set-nat-rules"></a>虛擬機器規模集 NAT 規則
負載等化器入站 NAT 規則應與虛擬機器規模集中的 NAT 池匹配。 每個虛擬機器規模集必須具有唯一的入站 NAT 池。

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>標準 SKU 負載等化器出站規則
與使用基本 SKU 相比，標準負載等化器和標準公共 IP 引入了新的功能和不同的出站連接行為。 如果您想要在使用標準 SKU 時輸出連線，您必須使用標準公用 IP 位址或標準公用 Load Balancer 明確定義該連線。 有關詳細資訊，請參閱[出站連接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust)和[Azure 標準負載等化器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

>[!NOTE]
> 標準範本引用 NSG，預設情況下允許所有出站流量。 入站流量僅限於服務結構管理操作所需的埠。 可以修改 NSG 規則以滿足您的要求。

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>在虛擬機器規模集中啟用區域
要啟用區域，在虛擬機器規模集中，必須在虛擬機器縮放集資源中包括以下三個值。

* 第一個值是**區域**屬性，它指定虛擬機器縮放集將部署到哪個可用性區域。
* 第二個值是"單一放置組"屬性，必須設置為 true。
* 第三個值是服務結構虛擬機器擴展中的"故障域覆蓋"屬性。 此屬性的值應包括將放置此虛擬機器比例集的區域和區域。 示例："faultDomainOverride"："eastus/az1"所有虛擬機器縮放集資源都必須放置在同一區域中，因為 Azure 服務結構群集沒有跨區域支援。

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>在服務結構群集資源中啟用多個主節點類型
要將一個或多個節點類型設置為群集資源中的主節點類型，將"isPrimary"屬性設置為"true"。 跨可用區域部署服務結構群集時，應在不同的區域中有三個節點類型。

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>使用基本 SKU 負載等化器和基本 SKU IP 從群集遷移到使用可用性區域
要遷移群集（該群集使用負載等化器和 IP 與基本 SKU 一起），必須首先使用標準 SKU 創建全新的負載等化器和 IP 資源。 無法就地更新這些資源。

新的 LB 和 IP 應在要使用的新交叉可用區域節點類型中引用。 在上面的示例中，在區域 1、2 和 3 中添加了三個新的虛擬機器規模集資源。 這些虛擬機器規模集引用新創建的 LB 和 IP，並在服務結構群集資源中標記為主要節點類型。

首先，您需要將新資源添加到現有資源管理器範本。 這些資源包括：
* 使用標準 SKU 的公共 IP 資源。
* 使用標準 SKU 的負載等化器資源。
* 部署虛擬機器縮放集的子網引用的 NSG。
* 三個節點類型標記為主節點。
    * 每個節點類型都應映射到位於不同區域的自己的虛擬機器規模集。
    * 每個虛擬機器規模集應至少有五個節點（銀耐久性）。

這些資源的示例可以在[示例範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)中找到。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

資源完成部署後，即可開始從原始群集禁用主節點類型的節點。 禁用節點後，系統服務將遷移到上述步驟中已部署的新主節點類型。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

一旦所有節點都禁用，系統服務將在主節點類型上運行，主節點類型跨區域分佈。 然後，可以從群集中刪除禁用的節點。 刪除節點後，可以刪除原始 IP、負載等化器和虛擬機器縮放集資源。

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

然後，您應該從已部署的資源管理器範本中刪除對這些資源的引用。

最後一步將涉及更新 DNS 名稱和公共 IP。

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
