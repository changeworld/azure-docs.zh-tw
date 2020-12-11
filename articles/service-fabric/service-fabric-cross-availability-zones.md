---
title: 跨可用性區域部署叢集
description: 瞭解如何跨可用性區域建立 Azure Service Fabric 叢集。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: d8e4a9201c14e71520bd58ff1017b700ca47fa21
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109807"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>跨可用性區域部署 Azure Service Fabric 叢集
Azure 中的可用性區域是高可用性供應專案，可保護您的應用程式和資料不受資料中心失敗的影響。 可用性區域是唯一的實體位置，可在 Azure 區域內配備獨立的電源、冷卻和網路功能。

Service Fabric 藉由部署釘選到特定區域的節點類型，支援跨可用性區域的叢集。 這可確保應用程式的高可用性。 Azure 可用性區域僅適用于選取的區域。 如需詳細資訊，請參閱 [Azure 可用性區域總覽](../availability-zones/az-overview.md)。

可用的範例範本： [Service Fabric 的跨可用性區域範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>跨可用性區域的 Azure Service Fabric 叢集主要節點類型的建議拓撲
分散到可用性區域的 Service Fabric 叢集可確保叢集狀態的高可用性。 若要跨區域跨 Service Fabric 叢集，您必須在該區域所支援的每個可用性區域中建立主要節點類型。 這會將種子節點平均分散到每個主要節點類型。

主要節點類型的建議拓撲需要以下所述的資源：

* 叢集可靠性層級設定為 [白金]。
* 三個標示為主要的節點類型。
    * 每個節點類型都應該對應至位於不同區域的虛擬機器擴展集。
    * 每個虛擬機器擴展集至少應該有五個節點 (銀級持久性) 。
* 使用標準 SKU 的單一公用 IP 資源。
* 使用標準 SKU 的單一 Load Balancer 資源。
* 您要在其中部署虛擬機器擴展集的子網所參考的 NSG。

>[!NOTE]
> 虛擬機器擴展集單一放置群組屬性必須設定為 true，因為 Service Fabric 不支援跨越區域的單一虛擬機器擴展集。

 ![Azure Service Fabric 可用性區域架構][sf-architecture]

## <a name="networking-requirements"></a>網路需求
### <a name="public-ip-and-load-balancer-resource"></a>公用 IP 和 Load Balancer 資源
若要在虛擬機器擴展集資源上啟用區域屬性，該虛擬機器擴展集所參考的負載平衡器和 IP 資源必須同時使用 *標準* SKU。 建立負載平衡器或沒有 SKU 屬性的 IP 資源，將會建立不支援可用性區域的基本 SKU。 標準 SKU 負載平衡器預設會封鎖來自外部的所有流量;若要允許外部流量，必須將 NSG 部署到子網。

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
> 您無法在公用 IP 和負載平衡器資源上進行 SKU 的就地變更。 如果您要從具有基本 SKU 的現有資源進行遷移，請參閱本文的「遷移」一節。

### <a name="virtual-machine-scale-set-nat-rules"></a>虛擬機器擴展集 NAT 規則
負載平衡器的輸入 NAT 規則應符合虛擬機器擴展集的 NAT 集區。 每個虛擬機器擴展集都必須有唯一的輸入 NAT 集區。

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
相較于使用基本 Sku，Standard Load Balancer 和標準公用 IP 引進了新功能和輸出連線能力的不同行為。 如果您想要在使用標準 SKU 時輸出連線，您必須使用標準公用 IP 位址或標準公用 Load Balancer 明確定義該連線。 如需詳細資訊，請參閱 [輸出](../load-balancer/load-balancer-outbound-connections.md) 連線和 [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md)。

>[!NOTE]
> 標準範本會參考 NSG，以根據預設允許所有輸出流量。 輸入流量僅限於 Service Fabric 管理作業所需的埠。 您可以修改 NSG 規則以符合您的需求。

>[!NOTE]
> 任何使用標準 SKU SLB 的 Service Fabric 叢集都必須確保每個節點類型都有一個規則，允許埠443上的輸出流量。 這是完成叢集設定的必要步驟，而且沒有這類規則的部署將會失敗。


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>啟用虛擬機器擴展集上的區域
若要啟用區域，在虛擬機器擴展集上，您必須在虛擬機器擴展集資源中包含下列三個值。

* 第一個值是 **zone** 屬性，它會指定要將虛擬機器擴展集部署至哪個可用性區域。
* 第二個值是 "singlePlacementGroup" 屬性，必須設定為 true。
* 第三個值是 Service Fabric 虛擬機器擴展集擴充功能中的 "faultDomainOverride" 屬性。 這個屬性的值應該只包含將放置此虛擬機器擴展集的區域。 範例： "faultDomainOverride"： "az1" 所有虛擬機器擴展集資源都必須放在相同的區域中，因為 Azure Service Fabric 叢集沒有跨區域支援。

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
        "publisher": "Microsoft.Azure.ServiceFabric",
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
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>啟用 Service Fabric 叢集資源中的多個主要節點類型
若要將一或多個節點類型設定為叢集資源中的主要節點，請將 "isPrimary" 屬性設定為 "true"。 跨可用性區域部署 Service Fabric 叢集時，您應該在不同的區域中有三個節點類型。

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>從使用基本 SKU Load Balancer 和基本 SKU IP 的叢集中，遷移至使用可用性區域
若要遷移使用 Load Balancer 和 IP 與基本 SKU 的叢集，您必須先使用標準 SKU 建立全新的 Load Balancer 和 IP 資源。 不可能就地更新這些資源。

新的 LB 和 IP 應該在您要使用的新跨可用性區域節點類型中參考。 在上述範例中，區域1、2和3中新增了三個新的虛擬機器擴展集資源。 這些虛擬機器擴展集會參考新建立的 LB 和 IP，並在 Service Fabric 叢集資源中標示為主要節點類型。

若要開始，您必須將新的資源新增至現有的 Resource Manager 範本。 這些資源包括：
* 使用標準 SKU 的公用 IP 資源。
* 使用標準 SKU 的 Load Balancer 資源。
* 您要在其中部署虛擬機器擴展集的子網所參考的 NSG。
* 三個標示為主要的節點類型。
    * 每個節點類型都應該對應至位於不同區域的虛擬機器擴展集。
    * 每個虛擬機器擴展集至少應該有五個節點 (銀級持久性) 。

您可以在 [範例範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)中找到這些資源的範例。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

資源完成部署之後，您就可以開始從原始叢集停用主要節點類型中的節點。 當停用節點時，系統服務會遷移至上述步驟中已部署的新主要節點類型。

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

節點全部停用之後，系統服務將會在主要節點類型上執行，該類型會分散到不同區域。 然後，您可以從叢集中移除停用的節點。 移除節點之後，您可以移除原始 IP、Load Balancer 和虛擬機器擴展集資源。

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

然後，您應該從已部署的 Resource Manager 範本中移除這些資源的參考。

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a> (預覽版) 在單一虛擬機器擴展集中啟用多個可用性區域

先前所述的解決方案會使用每個 AZ 的一個 nodeType。 下列解決方案可讓使用者在相同的 nodeType 中部署3個 AZ。

完整範例範本存在 [此處](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)。

![Azure Service Fabric 可用性區域架構][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>在虛擬機器擴展集上設定區域
若要啟用虛擬機器擴展集上的區域，您必須在虛擬機器擴展集資源中包含下列三個值。

* 第一個值是 **zone** 屬性，它會指定虛擬機器擴展集中存在的可用性區域。
* 第二個值是 "singlePlacementGroup" 屬性，必須設定為 true。
* 第三個值是 ">zonebalance"，而且是選擇性的，如果設定為 true，則可確保嚴格的區域平衡。 閱讀有關 [zoneBalancing](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#zone-balancing)的資訊。
* 不需要設定 FaultDomain 和 UpgradeDomain 覆寫。

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": false
    }
}
```

>[!NOTE]
> * **SF 叢集應該至少有一個主要 nodeType。主要 nodeTypes 的 DurabilityLevel 應該是銀級或更高的版本。**
> * 無論 durabilityLevel，都應該設定至少3個可用性區域的 AZ 跨虛擬機器擴展集。
> * 具有銀級持久性 (或以上) 的 AZ 跨越虛擬機器擴展集至少應有15部 Vm。
> * 具有銅級持久性的 AZ 跨虛擬機器擴展集，至少應有6部 Vm。

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>啟用 Service Fabric nodeType 中多個區域的支援
必須啟用 Service Fabric nodeType，以支援多個可用性區域。

* 第一個值是 **multipleAvailabilityZones** ，其應針對 nodeType 設定為 true。
* 第二個值是 **sfZonalUpgradeMode** ，而且是選擇性的。 如果叢集中已有多個 AZ 的 nodetype，則無法修改這個屬性。
      屬性會控制升級網域中 Vm 的邏輯群組。
          如果值設定為 false (的平面模式) ：節點類型下的 Vm 將會以 UD 分組，並忽略5個 UD 中的區域資訊。
          如果值已省略或設為 true (階層模式) ： Vm 將會分組，以反映最多15個 ud 中的區域性散發。 3個區域中的每一個都有5個 ud。
          這個屬性只會定義 ServiceFabric 應用程式和程式碼升級的升級行為。 基礎虛擬機器擴展集升級在所有 AZ 中仍會平行進行。
      對於未啟用多個區域的節點類型，這個屬性不會有任何 UD 分佈的影響。
* 第三個值是 **vmssZonalUpgradeMode = Parallel**。 如果新增了具有多個 Az 的 nodeType，則這是要在叢集中 *設定的必要屬性。* 此屬性定義虛擬機器擴展集更新的升級模式，這些更新將會在所有 AZ 中平行發生。
      現在這個屬性只能設定為 parallel。
* Service Fabric 叢集資源 apiVersion 應該是 "2020-12-01-preview" 或更高版本。
* 叢集程式碼版本應為 "7.2.445" 或更高版本。

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * 公用 IP 和 Load Balancer 資源應該使用標準 SKU，如本文稍早所述。
> * nodeType 上的 "multipleAvailabilityZones" 屬性只能在建立 nodeType 時定義，且稍後無法修改。 因此，無法使用這個屬性來設定現有的 nodeTypes。
> * 當 "hierarchicalUpgradeDomain" 被省略或設為 true 時，叢集和應用程式部署的速度會較慢，因為叢集中有更多的升級網域。 請務必正確地調整升級原則超時，以納入15個升級網域的升級時間持續時間。
> * 建議將叢集可靠性層級設定為白金級，以確保叢集繼續生存一個區域關閉案例。

>[!NOTE]
> 基於最佳作法，建議將 hierarchicalUpgradeDomain 設為 true 或省略。 部署將會遵循 Vm 的區域分佈，進而影響較少量的複本及/或實例，使其更安全。
> 如果部署速度是優先順序，或只有在具有多個 AZ 的節點類型上執行無狀態工作負載，請使用 hierarchicalUpgradeDomain 設定為 false。 這會導致 UD 在所有 AZ 的過程中平行發生。

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>使用多個可用性區域遷移至節點類型
在所有的遷移案例中，必須加入新的 nodeType，以支援多個可用性區域。 無法遷移現有的 nodeType 以支援多個區域。
[這裡](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-primary-node-type )的文章會取得新增 nodetype 的詳細步驟，也會新增新的 nodetype （例如 IP 和 LB 資源）所需的其他資源。 相同的文章也會說明在將具有多個可用性區域的 nodeType 新增至叢集之後，立即淘汰現有的 nodeType。

* 從使用基本 LB 和 IP 資源的 nodeType 進行遷移：這已針對每個 AZ 有一個節點類型的[解決方案描述。](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) 
    針對新的節點類型，唯一的差別在於每個 AZ 只有1個虛擬機器擴展集和1個 nodetype，而不是每個 az 的1個節點。
* 從使用標準 SKU LB 和 IP 資源和 NSG 的 nodeType 進行遷移：遵循上述的相同程式，但不需要新增 LB、IP 和 NSG 資源，而且可以在新的 nodeType 中重複使用相同的資源。


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
