---
title: 跨可用性區域部署叢集
description: 瞭解如何跨可用性區域建立 Azure Service Fabric 叢集。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 54382e74899d2cbb56ccf424b0f39bd874e31630
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84259366"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>跨可用性區域部署 Azure Service Fabric 叢集
Azure 中的可用性區域是高可用性供應專案，可保護您的應用程式和資料不受資料中心失敗的影響。 「可用性區域」是一種獨特的實體位置，具備獨立的電源、冷卻和網路功能，可在 Azure 區域內使用。

Service Fabric 藉由部署釘選到特定區域的節點類型，支援跨可用性區域的叢集。 這可確保應用程式的高可用性。 Azure 可用性區域僅適用于選取的區域。 如需詳細資訊，請參閱[Azure 可用性區域總覽](https://docs.microsoft.com/azure/availability-zones/az-overview)。

可用的範例範本： [Service Fabric [跨可用性區域] 範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>跨可用性區域的 Azure Service Fabric 叢集主要節點類型的建議拓撲
分散在可用性區域的 Service Fabric 叢集可確保叢集狀態的高可用性。 若要跨區域跨越 Service Fabric 叢集，您必須在區域支援的每個可用性區域中建立主要節點類型。 這會將種子節點平均分散到每個主要節點類型。

主要節點類型的建議拓撲需要下面所述的資源：

* 叢集可靠性層級設定為 [白金]。
* 標示為主要的三個節點類型。
    * 每個節點類型都應該對應到位於不同區域中的專屬虛擬機器擴展集。
    * 每個虛擬機器擴展集至少應有五個節點（銀級耐久性）。
* 使用標準 SKU 的單一公用 IP 資源。
* 使用標準 SKU 的單一 Load Balancer 資源。
* 您用來部署虛擬機器擴展集的子網所參考的 NSG。

>[!NOTE]
> 虛擬機器擴展集的單一放置群組屬性必須設定為 true，因為 Service Fabric 不支援跨越區域的單一虛擬機器擴展集。

 ![Azure Service Fabric 可用性區域架構][sf-architecture]

## <a name="networking-requirements"></a>網路需求
### <a name="public-ip-and-load-balancer-resource"></a>公用 IP 和 Load Balancer 資源
若要在虛擬機器擴展集資源上啟用區域屬性，該虛擬機器擴展集所參考的負載平衡器和 IP 資源必須同時使用*標準*SKU。 建立不含 SKU 屬性的負載平衡器或 IP 資源，將會建立不支援可用性區域的基本 SKU。 標準 SKU 負載平衡器預設會封鎖來自外部的所有流量;若要允許外部流量，必須將 NSG 部署到子網。

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
> 您不能在公用 IP 和負載平衡器資源上執行 SKU 的就地變更。 如果您要從具有基本 SKU 的現有資源進行遷移，請參閱本文的「遷移」一節。

### <a name="virtual-machine-scale-set-nat-rules"></a>虛擬機器擴展集 NAT 規則
負載平衡器輸入 NAT 規則應符合虛擬機器擴展集中的 NAT 集區。 每個虛擬機器擴展集都必須有唯一的輸入 NAT 集區。

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>標準 SKU Load Balancer 輸出規則
相較于使用基本 Sku，Standard Load Balancer 和標準公用 IP 在輸出連線時引進了新的功能和不同的行為。 如果您想要在使用標準 SKU 時輸出連線，您必須使用標準公用 IP 位址或標準公用 Load Balancer 明確定義該連線。 如需詳細資訊，請參閱[輸出](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust)連線和[Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

>[!NOTE]
> 標準範本會參考 NSG，其預設允許所有輸出流量。 輸入流量僅限於 Service Fabric 管理作業所需的埠。 您可以修改 NSG 規則以符合您的需求。

>[!NOTE]
> 使用標準 SKU SLB 的任何 Service Fabric 叢集都必須確保每個節點類型都有規則允許埠443上的輸出流量。 這是完成叢集設定的必要步驟，而且沒有這類規則的任何部署將會失敗。


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>在虛擬機器擴展集上啟用區域
若要啟用區域，在虛擬機器擴展集上，您必須在虛擬機器擴展集資源中包含下列三個值。

* 第一個值是**zone**屬性，它會指定要將虛擬機器擴展集部署到哪個可用性區域。
* 第二個值為 "singlePlacementGroup" 屬性，必須設定為 true。
* 第三個值是 Service Fabric 虛擬機器擴展集擴充功能中的 "faultDomainOverride" 屬性。 此屬性的值應包含將放置此虛擬機器擴展集的區域和區域。 範例： "faultDomainOverride"： "eastus/az1" 所有虛擬機器擴展集資源都必須放在相同的區域中，因為 Azure Service Fabric 叢集沒有跨區域支援。

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>在 Service Fabric 叢集資源中啟用多個主要節點類型
若要在叢集資源中將一或多個節點類型設定為主要，請將 "isPrimary" 屬性設定為 "true"。 跨可用性區域部署 Service Fabric 叢集時，您應該在不同的區域中有三個節點類型。

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>使用基本 SKU Load Balancer 和基本 SKU IP，遷移至從叢集使用可用性區域
若要遷移使用 Load Balancer 和 IP 與基本 SKU 的叢集，您必須先使用標準 SKU 建立全新的 Load Balancer 和 IP 資源。 您不能就地更新這些資源。

在您要使用的新跨可用性區域節點類型中，應參考新的 LB 和 IP。 在上述範例中，區域1、2和3中新增了三個新的虛擬機器擴展集資源。 這些虛擬機器擴展集會參考新建立的 LB 和 IP，並在 Service Fabric 叢集資源中標示為主要節點類型。

若要開始，您必須將新資源新增至現有的 Resource Manager 範本。 這些資源包括：
* 使用標準 SKU 的公用 IP 資源。
* 使用標準 SKU 的 Load Balancer 資源。
* 您用來部署虛擬機器擴展集的子網所參考的 NSG。
* 標示為主要的三個節點類型。
    * 每個節點類型都應該對應到位於不同區域中的專屬虛擬機器擴展集。
    * 每個虛擬機器擴展集至少應有五個節點（銀級耐久性）。

如需這些資源的範例，請參閱[範例範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

資源完成部署之後，您就可以開始從原始叢集停用主要節點類型中的節點。 當節點停用時，系統服務將會遷移至先前步驟中已部署的新主要節點類型。

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

節點全部停用之後，系統服務就會在主要節點類型上執行，而這會分散到各個區域。 然後，您可以從叢集中移除已停用的節點。 移除節點之後，您可以移除原始 IP、Load Balancer 和虛擬機器擴展集資源。

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

接著，您應該從已部署的 Resource Manager 範本中移除這些資源的參考。

最後一個步驟將牽涉到更新 DNS 名稱和公用 IP。

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
