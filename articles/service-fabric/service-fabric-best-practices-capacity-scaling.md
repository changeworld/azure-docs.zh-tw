---
title: Azure 服務結構的容量規劃和擴展
description: Service Fabric 叢集和應用程式規劃和調整的最佳做法。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258989"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Azure 服務結構的容量規劃和擴展

在創建承載群集的任何 Azure 服務結構群集或縮放計算資源之前，規劃容量非常重要。 如需規劃容量的詳細資訊，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。 有關群集可伸縮性的進一步最佳實踐指南，請參閱[服務結構可伸縮性注意事項](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)。

除了考慮節點類型和群集特徵外，還應預計生產環境的縮放操作需要超過一個小時才能完成。 無論要添加的 VM 數量如何，此注意事項都是正確的。

## <a name="autoscaling"></a>自動調整
應通過 Azure 資源管理器範本執行縮放操作，因為將[資源配置視為代碼]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)是最佳做法。 

通過虛擬機器縮放集進行自動縮放將使版本化資源管理器範本錯誤地定義虛擬機器縮放集的實例計數。 不准確的定義會增加未來部署將導致意外縮放操作的風險。 通常，在：

* 部署已宣告適當容量的 Resource Manager 範本，無法為您的使用案例提供支援。
     
   除了手動縮放之外，還可以通過使用 Azure[資源組部署專案在 Azure DevOps 服務中配置連續集成和傳遞管道](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)。 此管道通常由邏輯應用觸發，該應用使用從[Azure 監視器 REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)查詢的虛擬機器性能指標。 管道根據所需的任何指標有效地自動縮放，同時優化資源管理器範本。
* 一次只需水準縮放一個虛擬機器縮放集節點。
   
   要一次擴展三個或更多節點，應[通過添加虛擬機器規模集來橫向擴展 Service Fabric 群集](virtual-machine-scale-set-scale-node-type-scale-out.md)。 橫向擴展和橫向擴展虛擬機器規模集是最安全的，一次一個節點。
* 您的 Service Fabric 群集具有銀的可靠性或更高，在配置自動縮放規則的任何規模上具有銀的可靠性或更高。
  
   自動縮放規則的最低容量必須等於或大於五個虛擬機器實例。 它還必須等於或大於主節點類型的可靠性層最小值。

> [!NOTE]
> Service Fabric 有狀態的服務結構：/系統/infa結構服務/<NODE_TYPE_NAME>在每個具有銀型或更高耐久性的節點類型上運行。 它是支援在任何叢集節點類型的 Azure 中運行的唯一系統服務。

## <a name="vertical-scaling-considerations"></a>垂直調整考量

[垂直縮放](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)Azure 服務結構中的節點類型需要許多步驟和注意事項。 例如：

* 在調整之前，叢集必須處於良好狀態。 否則，您將進一步破壞群集的穩定。
* 承載有狀態服務的所有 Service Fabric 叢集節點類型都需要銀持久度或更高級別。

> [!NOTE]
> 承載有狀態服務交換矩陣系統服務的主節點類型必須是銀型持久性級別或更高級別。 啟用 Silver 持久性後，群集操作（如升級、添加或刪除節點等）將較慢，因為系統在操作速度方面優化了資料安全性。

垂直縮放虛擬機器比例集是一種破壞性操作。 相反，通過添加具有所需 SKU 的新比例集來水準縮放群集。 然後，將服務遷移到所需的 SKU 以完成安全的垂直縮放操作。 更改虛擬機器縮放集資源 SKU 是一種破壞性操作，因為它會重新映射主機，從而刪除所有本地持久狀態。

群集使用 Service Fabric[節點屬性和放置約束](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)來確定在何處託管應用程式的服務。 垂直縮放主節點類型時，請聲明 相同的`"nodeTypeRef"`屬性值。 您可以在虛擬機器擴展集的服務結構擴展中找到這些值。 

資源管理器範本的以下程式碼片段顯示您將聲明的屬性。 它對於要縮放到的新預配比例集具有相同的值，並且它僅作為群集的臨時狀態服務支援。

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 不要讓群集使用多個縮放集運行，這些縮放集使用相同的`nodeTypeRef`屬性值的時間比成功完成垂直縮放操作所需的時間長。
>
> 在嘗試更改生產環境之前，始終驗證測試環境中的操作。 預設情況下，Service Fabric 群集系統服務僅具有目標主節點類型的放置約束。

在宣告節點屬性和放置條件約束後，請逐一對各個 VM 執行個體執行下列步驟。 這允許系統服務（和有狀態服務）在您在其他地方創建新副本時刪除的 VM 實例上正常關閉。

1. 從 PowerShell`Disable-ServiceFabricNode`中運行`RemoveNode`，旨在禁用要刪除的節點。 移除具有最高編號的節點類型。 例如，如果您有六節點群集，請刪除"MyNodeType_5"虛擬機器實例。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 對於每個節點，這可能需要幾個小時。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 將該節點類型中的 VM 數減少一個。 此時將會移除最高的 VM 執行個體。
4. 視需要重複步驟 1 到 3，但是請永遠不要將主要節點類型的執行個體數目相應減少到少於可靠性層級所需的數目。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。
5. 一旦所有 VM 都消失（表示為"向下"），結構：/系統/基礎結構服務/[節點名稱]將顯示錯誤狀態。 然後，您可以更新群集資源以刪除節點類型。 可以使用 ARM 範本部署，或通過[Azure 資源管理器](https://resources.azure.com)編輯群集資源。 這將啟動群集升級，這將刪除處於錯誤狀態的結構：/系統/基礎結構服務/[節點類型]服務。
 6. 之後，您可以選擇刪除 VMScaleSet，您仍然會從服務結構資源管理器視圖中看到節點為"向下"。 最後一步是用`Remove-ServiceFabricNodeState`命令清理它們。

## <a name="horizontal-scaling"></a>水平調整

您可以[手動](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down)或[以程式設計方式](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)執行水準縮放。

> [!NOTE]
> 如果要縮放具有銀或金的持久性的節點類型，縮放速度將很慢。

### <a name="scaling-out"></a>相應放大

通過增加特定虛擬機器規模集的實例計數來擴展服務結構群集。 您可以使用 和`AzureClient`設置為所需比例的 ID 以增加容量，以程式設計方式橫向擴展。

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

要手動橫向擴展，請更新所需[虛擬機器縮放集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)資源的 SKU 屬性中的容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>相應縮小

擴展需要比橫向擴展更多的考慮。例如：

* 服務結構系統服務以群集中的主節點類型運行。 切勿關閉該節點類型的執行個體，或將其數目相應減少到低於可靠性層級保證所需的執行個體數目。 
* 對於有狀態服務，您需要一定數量的節點，這些節點始終處於維護可用性和維護服務狀態的狀態。 至少需要多個節點，這些節點等於分區或服務的目標複本集計數。

若要手動相應縮小，請遵循下列步驟︰

1. 從 PowerShell`Disable-ServiceFabricNode`中運行`RemoveNode`，旨在禁用要刪除的節點。 移除具有最高編號的節點類型。 例如，如果您有六節點群集，請刪除"MyNodeType_5"虛擬機器實例。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 對於每個節點，這可能需要幾個小時。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 將該節點類型中的 VM 數減少一個。 此時將會移除最高的 VM 執行個體。
4. 根據需要重複步驟 1 到 3，直到預配所需的容量。 切勿將主要節點類型的執行個體數目相應減少到低於可靠性層級保證所需的數目。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

要手動擴展，請更新所需[虛擬機器縮放集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)資源的 SKU 屬性中的容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

您必須準備關閉節點以程式設計方式進行縮放。 查找要刪除的節點（最高實例節點）。 例如：

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

使用與前面代碼中使用的相同`FabricClient`實例（`client`在本例中）和節點實例（`instanceIdString`在本例中）停用和刪除節點：

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
> 縮小群集時，您將看到已刪除的節點/VM 實例在 Service Fabric 資源管理器中顯示為不正常狀態。 有關此行為的說明，請參閱[在服務結構資源管理器中可能觀察到的行為](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)。 您可以：
> * 使用相應的節點名稱調用[刪除服務FabricNodeState命令](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)。
> * 在群集上部署[Service Fabric 自動縮放協助程式應用程式](https://github.com/Azure/service-fabric-autoscale-helper/)。 此應用程式可確保從服務結構資源管理器中清除縮小的節點。

## <a name="reliability-levels"></a>可靠性層級

[可靠性級別](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)是服務結構群集資源的屬性。 不能針對單個節點類型以不同的方式配置它。 它會控制叢集系統服務的複寫因子，是屬於叢集資源層級的設定。 

可靠性層級將決定您的主要節點類型必須具備的節點數目下限。 可靠性層級可以採用以下的值：

* 白金：使用目標複本集計數為 7 個和 9 個種子節點運行系統服務。
* Gold：使用目標複本集計數為 7 個和 7 個種子節點運行系統服務。
* Silver：使用目標複本集計數為 5 個和 5 個種子節點運行系統服務。
* 銅牌：使用目標複本集計數為三個和三個種子節點運行系統服務。

建議的最低可靠性層級為銀級。

可靠性層級設定於 [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 的屬性區段中，如下所示：

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>持久性層級

> [!WARNING]
> 執行 Bronze 持久性的節點類型「沒有權限」__。 影響無狀態工作負載的基礎結構作業不會停止或延遲，這可能會影響您的工作負載。 
>
> 銅級持久性僅適用於執行無狀態工作負載的節點類型。 對於生產工作負載，運行 Silver 或更高版本以確保狀態一致性。 請根據[容量規劃文件](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)中的指導方針選擇正確的可靠性。

持久性層級必須設定於兩個資源中。 一個是[虛擬機器縮放集資源的](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)擴展設定檔：

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

另一個資源位於`nodeTypes` [Microsoft.ServiceFabric/群集資源](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)中： 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>後續步驟

* 在 VM 或運行 Windows 伺服器的電腦上創建群集：[為 Windows 伺服器創建服務結構群集](service-fabric-cluster-creation-for-windows-server.md)。
* 在 VM 或運行 Linux 的電腦上創建群集：[創建 Linux 群集](service-fabric-cluster-creation-via-portal.md)。
* 了解 [Service Fabric 支援選項](service-fabric-support.md)。

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
