---
title: Azure Service Fabric 的容量規劃和調整
description: Service Fabric 叢集和應用程式規劃和調整的最佳做法。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: d7d9ed8fa695c636e7aaf36fd034babb4de012d9
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784675"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Azure Service Fabric 的容量規劃和調整

在您建立任何 Azure Service Fabric 叢集或調整裝載叢集的計算資源之前，請務必規劃容量。 如需規劃容量的詳細資訊，請參閱[規劃 Service Fabric 叢集容量](./service-fabric-cluster-capacity.md)。 如需更多叢集擴充性的最佳作法指引，請參閱 Service Fabric 的擴充 [性考慮](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)。

除了考慮使用節點類型和叢集特性，您應該預期調整作業需要超過一小時的時間才能完成生產環境。 無論您要新增的 Vm 數目為何，此考慮都成立。

## <a name="autoscaling"></a>自動調整
您應該透過 Azure Resource Manager 範本執行調整規模作業，因為這是將資源設定視為程式 [代碼](./service-fabric-best-practices-infrastructure-as-code.md)的最佳做法。 

透過虛擬機器擴展集使用自動調整，可讓您設定版本的 Resource Manager 範本不准確地定義虛擬機器擴展集的實例計數。 不正確的定義會增加未來部署將造成非預期的調整作業的風險。 一般情況下，您應該在下列情況下使用自動調整：

* 部署已宣告適當容量的 Resource Manager 範本，無法為您的使用案例提供支援。
     
   除了手動調整，您也可以 [使用 Azure 資源群組部署專案，在 Azure DevOps Services 中設定持續整合和傳遞管線](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)。 此管線通常是由邏輯應用程式所觸發，該邏輯應用程式會使用從 [Azure 監視器 REST API](../azure-monitor/platform/rest-api-walkthrough.md)查詢的虛擬機器效能計量。 管線會根據您想要的任何計量有效自動調整，同時針對 Resource Manager 範本進行優化。
* 您一次只需要水準調整一個虛擬機器擴展集節點。
   
   若要一次向外延展三個或更多節點，您應該 [新增虛擬機器擴展集](virtual-machine-scale-set-scale-node-type-scale-out.md)，以相應放大 Service Fabric 叢集。 最安全的方式是以水準方式相應放大及相應放大虛擬機器擴展集，一次一個節點。
* 您的 Service Fabric 叢集有銀級或更高的可靠性，以及您在設定自動調整規則的任何規模上都有銀級持久性或更高的版本。
  
   自動調整規則的最小容量必須等於或大於五個虛擬機器實例。 它也必須等於或大於您主要節點類型的最低可靠性層級。

> [!NOTE]
> Service Fabric 具狀態 Service Fabric：/System/InfastructureService/<NODE_TYPE_NAME> 會在具有銀級或更高持久性的每個節點類型上執行。 這是唯一支援在任何叢集節點類型上于 Azure 中執行的系統服務。

> [!IMPORTANT]
> Service Fabric 自動 `Default` 調整支援和 `NewestVM` 虛擬機器擴展集相應 [放大](../virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy.md)設定。

## <a name="vertical-scaling-considerations"></a>垂直調整考量

在 Azure Service Fabric 中[垂直調整](./virtual-machine-scale-set-scale-node-type-scale-out.md)節點類型需要一些步驟和考慮。 例如：

* 在調整之前，叢集必須處於良好狀態。 否則，您會進一步使叢集不穩定。
* 裝載具狀態服務的所有 Service Fabric 叢集節點類型都需要銀級持久性層級或更高。

> [!NOTE]
> 裝載具狀態 Service Fabric 系統服務的主要節點類型必須是銀級持久性層級或更高。 啟用銀級耐久性之後，叢集作業（例如升級、新增或移除節點等）將會變慢，因為系統會針對資料安全的作業速度優化。

垂直調整虛擬機器擴展集的方式只是變更其資源 SKU 是一項破壞性作業，因為它會重新建立主機映射，因此會移除所有本機保存的狀態。 相反地，您會想要將新的擴展集新增至所需的 SKU，然後將服務遷移至新的擴展集，以完成安全的垂直調整作業，以水準調整叢集規模。

您的叢集會使用 Service Fabric [節點屬性和放置條件約束](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) 來決定要裝載應用程式服務的位置。 垂直調整主要節點類型時，您將部署第二個主要節點類型，然後在 `"isPrimary": false` 原始主要節點類型上設定 () ，然後繼續停用其節點，並移除其擴展集及其相關資源。 如需詳細資訊，請參閱 [擴大 Service Fabric 叢集的主要節點類型](service-fabric-scale-up-primary-node-type.md)。

> [!NOTE]
> 在您嘗試變更生產環境之前，請一律先驗證測試環境中的作業。 根據預設，Service Fabric 叢集系統服務只有目標主要節點類型的放置條件約束。

在宣告節點屬性和放置條件約束後，請逐一對各個 VM 執行個體執行下列步驟。 這可讓系統服務 (和您的具狀態服務) 在您要移除的 VM 實例上正常關閉，因為您會在其他位置建立新複本。

1. 從 PowerShell 以 `Disable-ServiceFabricNode` 意圖執行， `RemoveNode` 以停用您要移除的節點。 移除具有最高編號的節點類型。 例如，如果您有六個節點的叢集，請移除 "MyNodeType_5" 虛擬機器實例。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 每個節點可能需要幾小時的時間。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 減少該節點類型中的 Vm 數目。 此時將會移除最高的 VM 執行個體。
4. 視需要重複步驟1到3，但永遠不會調整主要節點類型中的實例數目，而小於可靠性層級所保證的數目。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](./service-fabric-cluster-capacity.md)。
5. 一旦所有 Vm 都消失 (表示為「關閉」 ) 網狀架構：/System/InfrastructureService/[node name] 將會顯示錯誤狀態。 然後，您可以更新叢集資源以移除節點類型。 您可以使用 ARM 範本部署，或透過 [Azure resource manager](https://resources.azure.com)編輯叢集資源。 這將會啟動叢集升級，以移除處於錯誤狀態的 fabric：/System/InfrastructureService/[node type] 服務。
 6. 之後，您可以選擇性地刪除 VMScaleSet，不過您仍然可以從 Service Fabric Explorer view 看到節點「關閉」。 最後一個步驟就是使用命令來清除它們 `Remove-ServiceFabricNodeState` 。

## <a name="horizontal-scaling"></a>水平調整規模

您可以 [手動](./service-fabric-cluster-scale-in-out.md) 或以程式設計 [方式](./service-fabric-cluster-programmatic-scaling.md)進行水準調整。

> [!NOTE]
> 如果您正在調整具有銀級或金級持久性的節點類型，則調整速度會變慢。

### <a name="scaling-out"></a>相應放大

藉由增加特定虛擬機器擴展集的實例計數，來向外擴充 Service Fabric 叢集。 您可以使用來以程式設計方式相應放大 `AzureClient` ，並使用所需擴展集的識別碼來增加容量。

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

若要手動相應放大，請在所需的 [虛擬機器擴展集](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 資源的 SKU 屬性中更新容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>相應縮小

調整規模比相應放大需要更多的考慮。例如：

* Service Fabric 系統服務會在叢集中的主要節點類型上執行。 請勿關閉或縮減該節點類型的實例數目，如此您的實例就會比可靠性層級所保證的還要少。 
* 針對具狀態服務，您需要一定數量的節點，這些節點永遠保持可用狀態，並保留服務的狀態。 您至少需要一些等於磁碟分割或服務之目標複本集計數的節點。

若要手動相應縮小，請遵循下列步驟︰

1. 從 PowerShell 以 `Disable-ServiceFabricNode` 意圖執行， `RemoveNode` 以停用您要移除的節點。 移除具有最高編號的節點類型。 例如，如果您有六個節點的叢集，請移除 "MyNodeType_5" 虛擬機器實例。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 每個節點可能需要幾小時的時間。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 減少該節點類型中的 Vm 數目。 此時將會移除最高的 VM 執行個體。
4. 視需要重複步驟1到3，直到您布建所需的容量為止。 請勿將主要節點類型中的實例數目調整為小於可靠性層級所保證的數目。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](./service-fabric-cluster-capacity.md)。

若要手動調整，請在所需的 [虛擬機器擴展集](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 資源的 SKU 屬性中更新容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

您必須準備節點以便關機，以程式設計方式調整。 尋找要移除的節點， (最高實例節點) 。 例如：

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

使用相同的實例來停用和移除節點 (在此案例中， `FabricClient` `client`) 和節點實例 (`instanceIdString` 在此案例中，) 您在先前的程式碼中使用：

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> 當您調整叢集時，您會看到已移除的節點/VM 實例在 Service Fabric Explorer 中顯示為狀況不良的狀態。 如需此行為的說明，請參閱 [您可能在 Service Fabric Explorer 中觀察](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer)到的行為。 您可以：
> * 使用適當的節點名稱呼叫 [>remove-servicefabricnodestate 命令](/powershell/module/servicefabric/remove-servicefabricnodestate) 。
> * 在您的叢集上部署 [Service Fabric 自動調整 helper 應用程式](https://github.com/Azure/service-fabric-autoscale-helper/) 。 此應用程式可確保從 Service Fabric Explorer 清除相應減少的節點。

## <a name="reliability-levels"></a>可靠性層級

[可靠性層級](./service-fabric-cluster-capacity.md)是 Service Fabric 叢集資源的屬性。 無法針對個別節點類型進行不同的設定。 它會控制叢集系統服務的複寫因子，是屬於叢集資源層級的設定。 

可靠性層級將決定您的主要節點類型必須具備的節點數目下限。 可靠性層級可以採用以下的值：

* 白金：執行系統服務，其目標複本集計數為7和九個種子節點。
* 金級：執行系統服務，其目標複本集計數為7和7種子節點。
* 銀級：以五個和五個種子節點的目標複本集計數執行系統服務。
* 銅級：執行具有三個和三個種子節點的目標複本集計數的系統服務。

建議的最低可靠性層級為銀級。

可靠性層級設定於 [Microsoft.ServiceFabric/clusters resource](/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 的屬性區段中，如下所示：

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>持久性層級

> [!WARNING]
> 執行 Bronze 持久性的節點類型「沒有權限」。 影響無狀態工作負載的基礎結構作業將不會停止或延遲，這可能會影響您的工作負載。 
>
> 銅級持久性僅適用於執行無狀態工作負載的節點類型。 針對生產工作負載，請執行銀級或更高版本以確保狀態一致性。 請根據[容量規劃文件](./service-fabric-cluster-capacity.md)中的指導方針選擇正確的可靠性。

持久性層級必須設定於兩個資源中。 其中一個是 [虛擬機器擴展集資源](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)的延伸模組設定檔：

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

其他資源則位於 `nodeTypes` [ServiceFabric/叢集資源](/azure/templates/microsoft.servicefabric/2018-02-01/clusters)內： 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>後續步驟

* 在執行 Windows Server 的 Vm 或電腦上建立叢集： [Service Fabric 建立 Windows server 的](service-fabric-cluster-creation-for-windows-server.md)叢集。
* 在 Vm 或執行 Linux 的電腦上建立叢集： [建立 linux](service-fabric-cluster-creation-via-portal.md)叢集。
* 了解 [Service Fabric 支援選項](service-fabric-support.md)。

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
