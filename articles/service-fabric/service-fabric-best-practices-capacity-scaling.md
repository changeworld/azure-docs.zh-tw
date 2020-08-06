---
title: Azure Service Fabric 的容量規劃和調整
description: Service Fabric 叢集和應用程式規劃和調整的最佳做法。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 09c56646ffa9bcadcec821bcd83411077d6a55ae
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87824591"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Azure Service Fabric 的容量規劃和調整

在建立任何 Azure Service Fabric 叢集或調整裝載叢集的計算資源之前，請務必規劃容量。 如需規劃容量的詳細資訊，請參閱[規劃 Service Fabric 叢集容量](./service-fabric-cluster-capacity.md)。 如需叢集擴充性的進一步最佳做法指引，請參閱 Service Fabric 的擴充[性考慮](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)。

除了考慮節點類型和叢集特性，您應該預期調整作業需要超過一小時才能完成生產環境。 不論您要新增的 Vm 數目為何，這項考慮都是如此。

## <a name="autoscaling"></a>自動調整
您應該透過 Azure Resource Manager 範本執行調整作業，因為這是將資源設定視為程式[代碼](./service-fabric-best-practices-infrastructure-as-code.md)的最佳作法。 

透過虛擬機器擴展集使用自動調整，可讓您的版本設定 Resource Manager 範本不正確地定義虛擬機器擴展集的實例計數。 不正確的定義會增加未來部署造成非預期調整作業的風險。 一般來說，您應該在下列情況使用自動調整：

* 部署已宣告適當容量的 Resource Manager 範本，無法為您的使用案例提供支援。
     
   除了手動調整之外，您還可以[使用 Azure 資源群組部署專案，在 Azure DevOps Services 中設定持續整合和傳遞管線](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)。 此管線通常是由邏輯應用程式所觸發，該邏輯應用程式會使用從[Azure 監視器 REST API](../azure-monitor/platform/rest-api-walkthrough.md)查詢的虛擬機器效能計量。 管線會根據您想要的任何計量有效率地自動調整，同時針對 Resource Manager 範本進行優化。
* 您一次只需要水準調整一個虛擬機器擴展集節點。
   
   若要一次相應放大三個或多個節點，您應該[新增虛擬機器擴展集](virtual-machine-scale-set-scale-node-type-scale-out.md)來相應放大 Service Fabric 叢集。 最安全的方式是水準相應縮小和相應放大虛擬機器擴展集，一次一個節點。
* 您的 Service Fabric 叢集具有銀級或更高的可靠性，且在您設定自動調整規則的任何規模上都有銀級或更高的耐用性。
  
   自動調整規則的最小容量必須等於或大於五個虛擬機器實例。 它也必須等於或大於您主要節點類型的最低可靠性層級。

> [!NOTE]
> Service Fabric 具狀態 Service Fabric：/System/InfastructureService/<NODE_TYPE_NAME> 會在具有銀級或更高持久性的每個節點類型上執行。 這是唯一支援在 Azure 的任何叢集節點類型上執行的系統服務。

## <a name="vertical-scaling-considerations"></a>垂直調整考量

[垂直調整](./virtual-machine-scale-set-scale-node-type-scale-out.md)Azure Service Fabric 中的節點類型需要一些步驟和考慮。 例如：

* 在調整之前，叢集必須處於良好狀態。 否則，您將會進一步使叢集變得不穩定。
* 裝載具狀態服務的所有 Service Fabric 叢集節點類型都需要銀級耐久性等級或更大。

> [!NOTE]
> 裝載具狀態 Service Fabric 系統服務的主要節點類型必須是銀級或更高的耐用性。 啟用銀級持久性之後，叢集作業（例如升級、新增或移除節點等等）將會變慢，因為系統會透過作業速度來優化資料安全性。

垂直調整虛擬機器擴展集是一種破壞性操作。 相反地，請使用所需的 SKU 加入新的擴展集，以水準方式調整您的叢集。 然後，將您的服務遷移至您所需的 SKU，以完成安全的垂直調整作業。 變更虛擬機器擴展集資源 SKU 是一種破壞性作業，因為它會重新安裝您的主機，這會移除所有本機保存的狀態。

您的叢集會使用 Service Fabric[節點屬性和放置條件約束](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)來決定要裝載應用程式服務的位置。 當您垂直調整主要節點類型時，請為提供相同的屬性值 `"nodeTypeRef"` 。 您可以在虛擬機器擴展集的 Service Fabric 延伸模組中找到這些值。 

Resource Manager 範本的下列程式碼片段顯示您將宣告的屬性。 對於您要調整的新布建擴展集，其值相同，而且僅支援做為叢集的暫時具狀態服務。

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 請不要讓叢集執行的多個擴展集使用相同的 `nodeTypeRef` 屬性值超過所需的大小，以完成成功的垂直調整作業。
>
> 在您嘗試變更生產環境之前，請一律先驗證測試環境中的作業。 根據預設，Service Fabric 叢集系統服務只有目標主要節點類型的放置條件約束。

在宣告節點屬性和放置條件約束後，請逐一對各個 VM 執行個體執行下列步驟。 這可讓系統服務 (和您的具狀態服務) 在其他位置建立新複本時，在您要移除的 VM 實例上正常關閉。

1. 從 PowerShell， `Disable-ServiceFabricNode` 以意圖執行， `RemoveNode` 以停用您要移除的節點。 移除具有最高編號的節點類型。 例如，如果您有六個節點的叢集，請移除「MyNodeType_5」虛擬機器實例。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 這可能需要幾個小時的時間才能執行每個節點。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 將 Vm 的數目減少為該節點類型的其中一個。 此時將會移除最高的 VM 執行個體。
4. 視需要重複步驟1到3，但永遠不會相應縮小主要節點類型中的實例數目，而不是可靠性層級所保證的大小。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](./service-fabric-cluster-capacity.md)。
5. 一旦所有 Vm 都 (以「關閉」表示 ) fabric：/System/InfrastructureService/[node name] 就會顯示錯誤狀態。 然後，您可以更新叢集資源來移除節點類型。 您可以使用 ARM 範本部署，或透過[Azure resource manager](https://resources.azure.com)來編輯叢集資源。 這會啟動叢集升級，這會移除處於錯誤狀態的 fabric：/System/InfrastructureService/[node type] 服務。
 6. 在這之後，您可以選擇性地刪除 VMScaleSet，但您仍會看到 Service Fabric Explorer view 中的節點為「關閉」。 最後一個步驟是使用命令來清除它們 `Remove-ServiceFabricNodeState` 。

## <a name="horizontal-scaling"></a>水平調整規模

您可以[手動](./service-fabric-cluster-scale-in-out.md)或透過程式設計[方式](./service-fabric-cluster-programmatic-scaling.md)進行水準調整。

> [!NOTE]
> 如果您要調整具有銀級或金級耐久性的節點類型，則調整速度會變慢。

### <a name="scaling-out"></a>相應放大

藉由增加特定虛擬機器擴展集的實例計數，以相應放大 Service Fabric 叢集。 您可以使用來以程式設計方式向外延展 `AzureClient` ，並使用所需擴展集的識別碼來增加容量。

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

若要手動相應放大，請在所需的[虛擬機器擴展集](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)資源的 SKU 屬性中更新容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>相應縮小

相應放大所需的擴充功能需要更多考慮。例如：

* Service Fabric 系統服務會在叢集中的主要節點類型中執行。 請勿關閉或相應縮小該節點類型的實例數目，讓您的實例數量少於可靠性層級所需的大小。 
* 針對具狀態服務，您需要一定數目的節點，這些節點一律會維持可用性並保留服務的狀態。 您至少需要數個節點，其等於分割區或服務的目標複本集計數。

若要手動相應縮小，請遵循下列步驟︰

1. 從 PowerShell， `Disable-ServiceFabricNode` 以意圖執行， `RemoveNode` 以停用您要移除的節點。 移除具有最高編號的節點類型。 例如，如果您有六個節點的叢集，請移除「MyNodeType_5」虛擬機器實例。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 這可能需要幾個小時的時間才能執行每個節點。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 將 Vm 的數目減少為該節點類型的其中一個。 此時將會移除最高的 VM 執行個體。
4. 視需要重複步驟1到3，直到您布建所需的容量為止。 請勿將主要節點類型的實例數目相應縮小為小於可靠性層級所保證的大小。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](./service-fabric-cluster-capacity.md)。

若要手動相應縮小，請在所需的[虛擬機器擴展集](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)資源的 SKU 屬性中更新容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

您必須準備節點，以透過程式設計的方式進行關閉。 尋找要移除的節點， (最高實例節點) 。 例如：

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

在此情況下，使用相同的實例 (停用和移除節點 `FabricClient` `client`) 和節點實例 (`instanceIdString` 在此情況下，您會在先前的程式碼中使用) ：

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
> 當您在叢集中進行調整時，您會看到已移除的節點/VM 實例在 Service Fabric Explorer 中顯示為狀況不良狀態。 如需此行為的說明，請參閱[您可能會在 Service Fabric Explorer 中觀察](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer)到的行為。 您可以：
> * 以適當的節點名稱呼叫[remove-servicefabricnodestate 命令](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)。
> * 在您的叢集中部署[Service Fabric 自動調整 helper 應用程式](https://github.com/Azure/service-fabric-autoscale-helper/)。 此應用程式可確保從 Service Fabric Explorer 清除相應減少的節點。

## <a name="reliability-levels"></a>可靠性層級

[可靠性層級](./service-fabric-cluster-capacity.md)是 Service Fabric 叢集資源的屬性。 不能針對個別節點類型進行不同的設定。 它會控制叢集系統服務的複寫因子，是屬於叢集資源層級的設定。 

可靠性層級將決定您的主要節點類型必須具備的節點數目下限。 可靠性層級可以採用以下的值：

* 白金：執行系統服務，其目標複本集計數為7和9個種子節點。
* 黃金：執行系統服務，其目標複本集計數為七和七個種子節點。
* 銀級：執行系統服務，其目標複本集計數為五和五個種子節點。
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
> 執行 Bronze 持久性的節點類型「沒有權限」__。 影響無狀態工作負載的基礎結構工作將不會停止或延遲，這可能會影響您的工作負載。 
>
> 銅級持久性僅適用於執行無狀態工作負載的節點類型。 針對生產工作負載，執行銀級或更高版本以確保狀態一致性。 請根據[容量規劃文件](./service-fabric-cluster-capacity.md)中的指導方針選擇正確的可靠性。

持久性層級必須設定於兩個資源中。 其中一個是[虛擬機器擴展集資源](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)的延伸模組設定檔：

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

另一個資源位於 `nodeTypes` [ServiceFabric/叢集資源](/azure/templates/microsoft.servicefabric/2018-02-01/clusters)中： 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>後續步驟

* 在執行 Windows Server 的 Vm 或電腦上建立叢集：建立[Windows server 的 Service Fabric](service-fabric-cluster-creation-for-windows-server.md)叢集。
* 在執行 Linux 的 Vm 或電腦上建立叢集：[建立 linux](service-fabric-cluster-creation-via-portal.md)叢集。
* 了解 [Service Fabric 支援選項](service-fabric-support.md)。

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png