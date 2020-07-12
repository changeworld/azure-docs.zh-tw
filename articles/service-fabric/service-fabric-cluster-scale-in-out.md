---
title: 將 Service Fabric 叢集相應縮小或相應放大
description: 針對每個節點類型/虛擬機器擴展集設定自動調整規模規則，以相應縮小或相應放大 Service Fabric 叢集以符合需求。 新增或移除 Service Fabric 叢集的節點
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c9393ca4531dea58859a4fc60509524e9c4a0b7f
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246481"
---
# <a name="scale-a-cluster-in-or-out"></a>將叢集相應縮小或相應放大

> [!WARNING]
> 在您調整規模之前，請先閱讀本節

調整計算資源來符合您的應用程式工作負載時，需要經過刻意規劃，生產環境幾乎一律需要超過一小時的時間才能完成規劃，而且會要求您了解您的工作負載和商務內容；事實上，如果您之前不曾執行這項活動，建議您先閱讀並了解 [Service Fabric 叢集容量規劃考量](service-fabric-cluster-capacity.md)，再繼續進行本文件的其餘部分。 此建議是為了避免非預期的 LiveSite 問題，此外，也建議您成功測試您決定要對非商業執行環境執行的作業。 您可以隨時[報告實際執行問題，或要求 Azure 的付費支援](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure)。 針對配置來執行這些作業且擁有適當內容的工程師，本文將說明調整作業，但您必須決定並了解哪些作業適合您的使用案例；例如要調整哪些資源 (CPU、儲存體、記憶體)、調整方向為何 (水平或垂直)，以及要執行哪些作業 (資源範本部署、入口網站、PowerShell/CLI)。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>使用自動調整規則或以手動方式相應縮小或放大 Service Fabric 叢集
虛擬機器擴展集是一個 Azure 計算資源，可以用來將一組虛擬機器當做一個集合加以部署和管理。 在 Service Fabric 叢集中定義的每個節點類型都會安裝為不同的虛擬機器擴展集。 然後每個節點類型可以獨立相應縮小或放大，可以開啟不同組的連接埠，並可以有不同的容量度量。 如需詳細資訊，請參閱[Service Fabric 節點類型](service-fabric-cluster-nodetypes.md)檔。 因為叢集中的 Service Fabric 節點類型是由後端的虛擬機器擴展集所組成，所以您必須為每個節點類型/虛擬機器擴展集設定自動調整規模規則。

> [!NOTE]
> 您的訂用帳戶必須要有足夠的核心，來新增構構成此叢集的虛擬機器。 目前沒有模型驗證，所以如果達到任一配額限制，就會收到部署時間失敗。 此外，單一節點類型不能只是每個 VMSS 超過 100 個節點。 您可能必須新增 VMSS 來達成目標的縮放，且自動縮放比例無法自動新增 VMSS。 就地將 VMSS 新增至即時叢集是富有挑戰性的工作，而通常這會導致使用者利用建立時所佈建的適當節點類型來佈建新叢集；據此[規劃叢集容量](./service-fabric-cluster-capacity.md)。 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>選擇要調整規模的節點類型/虛擬機器擴展集
目前，您無法使用入口網站指定虛擬機器擴展集的自動調整規則，以建立 Service Fabric 叢集，因此我們將使用 Azure PowerShell (1.0+) 列出節點類型，然後為其新增自動調整規則。

若要取得建立叢集的虛擬機器擴展集清單，請執行下列 Cmdlet：

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>設定節點類型/虛擬機器擴展集的自動調整規則
如果您的叢集有多個節點類型，請針對您想要 (相應縮小或放大) 的每個節點類型/虛擬機器擴展集重複此步驟。 請先考慮一定要有的節點數目，再設定自動調整規模。 主要節點類型一定要有的節點數目下限是由您已選擇的可靠性層級決定。 深入了解 [可靠性層級](service-fabric-cluster-capacity.md)。

> [!NOTE]
> 將主要節點類型相應縮小為小於最小數目，會使叢集變得不穩定，或甚至讓叢集變少。 這可能導致應用程式和系統服務資料遺失。
> 
> 

自動調整規模功能目前不是由應用程式可能向 Service Fabric 報告的負載所驅動。 所以，您現在取得的自動調整只由每個虛擬機器擴展集執行個體所發出的效能計數器驅動。  

請遵循這些指示[來設定每個虛擬機器擴展集的自動調整規模](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)。

> [!NOTE]
> 在規模的案例中，除非您的節點類型具有黃金或銀[級的耐久性等級][durability]，否則您必須使用適當的節點名稱呼叫[remove-servicefabricnodestate 指令程式](/powershell/module/servicefabric/remove-servicefabricnodestate)。 針對銅級持久性，不建議一次相應縮小一個以上的節點。
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>以手動方式將 Vm 新增至節點類型/虛擬機器擴展集

您相應放大時，會將多個虛擬機器執行個體新增到擴展集。 這些執行個體會成為 Service Fabric 使用的節點。 擴展集有多個新增的執行個體 (透過相應放大) 時，Service Fabric 會知道並自動回應。 

> [!NOTE]
> 新增 Vm 需要一些時間，因此不會預期會有瞬間的加入。 規劃預先新增容量，讓複本/服務實例有超過10分鐘的時間，才可使用 VM 容量來放置。
> 

### <a name="add-vms-using-a-template"></a>使用範本新增 Vm
請遵循[快速入門範本資源庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)中的範例/指示來變更每個節點類型中的 vm 數目。 

### <a name="add-vms-using-powershell-or-cli-commands"></a>使用 PowerShell 或 CLI 命令新增 Vm
下列程式碼會按照名稱取得擴展集，並將擴展集的**容量**增加 1。

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

這段程式碼將容量設定為 6。

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>手動從節點類型/虛擬機器擴展集移除 Vm
當您調整節點類型時，會從擴展集移除 VM 實例。 如果節點類型是銅級耐久性層級，Service Fabric 不知道發生了什麼事，並報告節點已遺失。 Service Fabric 接著會報告叢集狀況不良的狀態。 若要避免這種錯誤狀態，您必須明確地從叢集移除節點，並移除節點狀態。

Service fabric 系統服務會在叢集中的主要節點類型上執行。 在主要節點類型中進行調整時，永遠不會相應縮小實例數目，而不會比[可靠性層級](service-fabric-cluster-capacity.md)所保證的還要少。 
 
如果是具狀態服務，您需要一些永遠啟動的節點來維持可用性，以及維持服務的狀態。 您至少需要與資料分割/服務的目標複本集計數相等的節點數目。

### <a name="remove-the-service-fabric-node"></a>移除 Service Fabric 節點

手動移除節點狀態的步驟僅適用于具有*銅*級耐久性層的節點類型。  針對*銀*級和*黃金*耐久性層，這些步驟會由平臺自動完成。 如需持久性的詳細資訊，請參閱 [Service Fabric 叢集容量規劃][durability]。

為了讓叢集節點在升級和容錯網域之間平均分配，因而讓它們的使用率更加平均，應該先移除最近建立的節點。 換句話說，節點的移除順序應該與建立順序相反。 最近建立的節點具有最大的 `virtual machine scale set InstanceId` 屬性值。 下列程式碼範例會傳回最近建立的節點。

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Service Fabric 叢集必須知道將移除此節點。 您需要採取三個步驟：

1. 停用節點，以免節點再成為資料的複本。  
PowerShell：`Disable-ServiceFabricNode`  
sfctl：`sfctl node disable`

2. 停止節點，以便 Service Fabric 執行階段正常關閉，而且應用程式取得終止要求。  
PowerShell：`Start-ServiceFabricNodeTransition -Stop`  
sfctl：`sfctl node transition --node-transition-type Stop`

2. 從叢集移除該節點。  
PowerShell：`Remove-ServiceFabricNodeState`  
sfctl：`sfctl node remove-state`

這三個步驟套用至節點之後，即可從擴展集移除節點。 如果在 [bronze][durability] 之外使用任何持久性層，則移除擴展集執行個體時，將完成這些步驟。

下列程式碼區塊會取得、停用、停止最後建立的節點，並從叢集移除該節點。

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

在下方的 **sfctl** 程式碼中，會使用下列命令來取得最後建立節點的 **node-name** 值：`sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> 使用下列 **sfctl** 查詢，檢查每個步驟的狀態
>
> **檢查停用狀態**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **檢查停止狀態**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>相應縮小擴展集

已從叢集移除 Service Fabric 節點之後，即可相應縮小虛擬機器擴展集。 在下列範例中，擴展集容量減少 1。

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

這段程式碼將容量設定為 5。

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Service Fabric Explorer 可能出現的行為
當您相應放大叢集時，Service Fabric Explorer 會反映屬於叢集一部分的 (虛擬機器擴展集) 實例的節點數目。  不過，當您在中調整叢集時，除非您使用適當的節點名稱呼叫[remove-servicefabricnodestate cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) ，否則會看到顯示為狀況不良狀態的已移除節點/VM 實例。   

以下是這種行為的說明。

Service Fabric Explorer 列出的節點會反映出 Service Fabric 系統服務 (特別是 FM) 所知叢集曾經有過/現有擁有的節點數目。 當您調整中的虛擬機器擴展集時，會刪除 VM，但 FM 系統服務仍然認為對應至已) 刪除之 VM 的節點 (將會回復。 所以 Service Fabric Explorer 會繼續顯示該節點 (雖然健全狀況狀態可能是錯誤或未知)。

為確保移除 VM 時也移除節點，您有兩個選項︰

1. 為叢集中的節點類型選擇 Gold 或 Silver 持久性層級，提供您基礎結構的整合。 然後，當您相應縮小時，就會自動從我們的系統服務 (FM) 狀態移除節點。
請參閱 [持久性層級的詳細資訊](service-fabric-cluster-capacity.md)

2. 一旦將 VM 實例相應縮小，您必須呼叫[remove-servicefabricnodestate 指令程式](/powershell/module/servicefabric/remove-servicefabricnodestate)。

> [!NOTE]
> Service Fabric 叢集需要有一定數量的節點隨時都處於啟動，以維持可用性並維持狀態 - 稱為「維持仲裁」。 因此，除非您已先執行 [狀態的完整備份](service-fabric-reliable-services-backup-restore.md)，否則關閉叢集中的所有電腦通常並不安全。
> 
> 

## <a name="next-steps"></a>後續步驟
亦請參閱下列文章，了解規劃叢集容量、升級叢集和分割服務︰

* [規劃叢集容量](service-fabric-cluster-capacity.md)
* [叢集升級](service-fabric-cluster-upgrade.md)
* [分割具狀態服務以達最大規模](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
