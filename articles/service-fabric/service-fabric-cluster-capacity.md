---
title: Service Fabric 叢集容量規劃考量
description: 規劃 Service Fabric 叢集時，應考慮的節點類型、持久性、可靠性和其他事項。
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.openlocfilehash: 03ec9b411f13f22a74b864a745acfed922e78b12
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790693"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric 叢集容量規劃考量

叢集容量規劃對於每個 Service Fabric 生產環境都很重要。 重要考慮包括：

* **叢集 *節點類型* 的初始數目和屬性**

* **每個節點類型的 *持久性* 層級**，決定 Azure 基礎結構內 Service Fabric VM 許可權

* 叢集的 ***可靠性* 層級**，可判斷 Service Fabric 系統服務和整體叢集功能的穩定性

本文將逐步引導您瞭解每個區域的重要決策點。

## <a name="initial-number-and-properties-of-cluster-node-types"></a>叢集節點類型的初始數目和屬性

*節點類型* 會定義叢集中一組節點 (虛擬機器) 的大小、數目和屬性。 在 Service Fabric 叢集中定義的每個節點類型都會對應到一個[虛擬機器擴展集](../virtual-machine-scale-sets/overview.md)。

因為每個節點類型都是不同的擴展集，所以可以獨立相應增加或相應減少、開啟不同組的埠，並擁有不同的容量計量。 如需節點類型與虛擬機器擴展集之間關聯性的詳細資訊，請參閱 [Service Fabric 叢集節點類型](service-fabric-cluster-nodetypes.md)。

每個叢集都需要一個 **主要節點類型**，它會執行提供 Service Fabric 平臺功能的重要系統服務。 雖然您也可以使用主要節點類型來執行您的應用程式，但建議您只將它們專用於執行系統服務。

**非主要節點類型** 可以用來定義應用程式角色 (例如 *前端* 和 *後端* 服務) ，以及實際隔離叢集中的服務。 Service Fabric 叢集可以有零或多個非主要節點類型。

主要節點類型是使用 `isPrimary` Azure Resource Manager 部署範本中節點類型定義下的屬性來設定。 如需節點型別屬性的完整清單，請參閱 [NodeTypeDescription 物件](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) 。 如需使用方式，請在 [Service Fabric 叢集範例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/)中開啟檔案的任何 *AzureDeploy.js* ，並 *在頁面* 搜尋中尋找 `nodeTypes` 物件。

### <a name="node-type-planning-considerations"></a>節點類型規劃考慮

初始節點類型的數目取決於您叢集的用途和在其上執行的應用程式和服務。 請考量下列問題：

* ***您的應用程式是否有多個服務，而這些服務需要是公用或網際網路對向的？** _

    一般應用程式包含可接收用戶端輸入的前端閘道服務，以及一或多個與前端服務通訊的後端服務，前端和後端服務之間會有不同的網路功能。 這些情況通常需要三個節點類型：一個主要節點類型，以及兩個非主要節點類型分別 (前端和後端服務) 的節點類型。

_ ***執行應用程式的服務是否有不同的基礎結構需求，例如 RAM 更高或更高的 CPU 週期？** _

    Often, front-end service can run on smaller VMs (VM sizes like D2) that have ports open to the internet.  Computationally intensive back-end services might need to run on larger VMs (with VM sizes like D4, D6, D15) that are not internet-facing. Defining different node types for these services allow you to make more efficient and secure use of underlying Service Fabric VMs, and enables them to scale them independently. For more on estimating the amount of resources you'll need, see [Capacity planning for Service Fabric applications](service-fabric-capacity-planning.md)

_ ***您是否有任何應用程式服務需要向外延展到100節點以外的範圍？** _

    A single node type can't reliably scale beyond 100 nodes per virtual machine scale set for Service Fabric applications. Running more than 100 nodes requires additional virtual machine scale sets (and therefore additional node types).

_ ***您的叢集會跨可用性區域嗎？** _

    Service Fabric supports clusters that span across [Availability Zones](../availability-zones/az-overview.md) by deploying node types that are pinned to specific zones, ensuring high-availability of your applications. Availability Zones require additional node type planning and minimum requirements. For details, see [Recommended topology for primary node type of Service Fabric clusters spanning across Availability Zones](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones). 

當您在初始建立叢集時，判斷節點類型的數目和屬性時，請記住，在部署叢集之後，您隨時都可以新增、修改或移除 (的非主要) 節點類型。 您也可以在執行中的叢集內[修改主要節點類型](service-fabric-scale-up-primary-node-type.md) (不過這類作業需要在生產環境中進行大量的規劃和警告) 。

節點類型屬性的進一步考慮是持久性層級，可判斷節點類型的 Vm 在 Azure 基礎結構內的許可權。 使用您為叢集選擇的 Vm 大小，以及您為個別節點類型指派的實例計數，以協助為每個節點類型判斷適當的持久性層級，如下所述。

## <a name="durability-characteristics-of-the-cluster"></a>叢集的持久性特性

_Durability 層級 * 會指定 Service Fabric Vm 與基礎 Azure 基礎結構的許可權。 此許可權可讓 Service Fabric 暫停任何 VM 層級基礎結構要求 (例如，重新開機、重新安裝映射或遷移) ，而這些會影響 Service Fabric 系統服務和具狀態服務的仲裁需求。

> [!IMPORTANT]
> 持久性層級是針對每個節點類型所設定。 如果未指定，則會使用 *銅* 級層，但不會提供自動 OS 升級。 針對生產工作負載，建議使用 *銀* 級或 *金* 級持久性。

下表列出 Service Fabric 耐久性層、其需求和 affordances。

| 耐久性層級  | 所需的 VM 數目下限 | 支援的 VM 大小                                                                  | 您對虛擬機器擴展集所做的更新                               | Azure 所起始的更新和維護                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| 金卡             | 5                              | 專用於單一客戶 (的完整節點大小，例如 L32s、GS5、G5、DS15_v2 D15_v2)  | 可以延遲到 Service Fabric 叢集核准為止 | 每個升級網域可以暫停2小時，以允許更多時間讓複本從先前的失敗中復原 |
| 銀色           | 5                              | 具有至少 50 GB 本機 SSD 的單一核心或更高版本 Vm                      | 可以延遲到 Service Fabric 叢集核准為止 | 無法延遲很長的期間                                                    |
| 青銅卡          | 1                              | 至少具有 50 GB 本機 SSD 的 Vm                                              | Service Fabric 叢集不會延遲           | 無法延遲很長的期間                                                    |

> [!WARNING]
> 使用銅級持久性時，無法使用自動 OS 映射升級。 雖然 [修補程式協調流程應用程式](service-fabric-patch-orchestration-application.md) (僅適用于非 Azure 裝載的叢集) *不建議* 用於銀級或更高的持久性層級，但這是您唯一可根據 Service Fabric 升級網域自動執行 Windows update 的選項。

> [!IMPORTANT]
> 無論持久性層級為何，在虛擬機器擴展集上執行 [解除配置](/rest/api/compute/virtualmachinescalesets/deallocate) 作業都會損毀叢集。

### <a name="bronze"></a>青銅卡

執行 Bronze 持久性的節點類型「沒有權限」。 這表示會影響可設定狀態工作負載的基礎結構工作，不會停止或延遲。 針對僅執行無狀態工作負載的節點類型，請使用銅級持久性。 對於生產工作負載，建議執行 Silver 或以上的層級。

### <a name="silver-and-gold"></a>銀級和金級

使用銀級或金級持久性，以裝載您預期要相應縮小的具狀態服務的所有節點類型，以及您希望部署作業的延遲和容量減少，以簡化程式。 相應放大案例不會影響您選擇的持久性層級。

#### <a name="advantages"></a>優點

* 減少相應縮小作業的必要步驟數 (節點停用，且 Remove-ServiceFabricNodeState 會自動呼叫) 。
* 降低因就地 VM 大小變更作業和 Azure 基礎結構作業而造成資料遺失的風險。

#### <a name="disadvantages"></a>缺點

* 虛擬機器擴展集和其他相關 Azure 資源的部署可能會超時、延遲，或完全被叢集或基礎結構層級的問題封鎖。
* 因 Azure 基礎結構作業期間的自動化節點停用，而增加[複本生命週期事件](service-fabric-reliable-services-lifecycle.md) (例如主要交換) 的數目。
* 當 Azure 平台軟體更新或硬體維護活動正在進行時，使節點停止服務一段期間。 您可能會在這些活動期間看到含有正在停用/已停用狀態的節點。 這會暫時減少叢集的容量，但應該不會影響叢集或應用程式的可用性。

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>銀級和金級持久性節點類型的最佳作法

遵循下列建議來管理具有銀級或金級持久性的節點類型：

* 使叢集和應用程式持續保持良好的狀況，並確保應用程式會及時回應所有[服務複本生命週期事件](service-fabric-reliable-services-lifecycle.md) (例如當組建中的複本陷入停滯)。
* 採用更安全的方式來進行 VM 大小變更， (擴大/縮小) 。 變更虛擬機器擴展集的 VM 大小需要謹慎的規劃和警告。 如需詳細資訊，請參閱 [擴大 Service Fabric 節點類型](service-fabric-scale-up-primary-node-type.md)
* 針對所有啟用 Gold 或 Silver 持久性的虛擬機器擴展集維持至少五個節點。 如果您的縮減低於此閾值，您的叢集將會進入錯誤狀態，而且您必須手動清除 `Remove-ServiceFabricNodeState` 已移除節點的狀態 () 。
* 持久性層級為 Silver 或 Gold 的每個虛擬機器擴展集，都必須對應到它自己在 Service Fabric 叢集中的節點類型。 將多個虛擬機器擴展集對應到單一節點類型，將會使 Service Fabric 叢集與 Azure 基礎結構之間的協調無法正常運作。
* 請勿刪除隨機的 VM 實例，請一律使用虛擬機器擴展集的擴充功能。 刪除隨機 VM 實例可能會在跨 [升級網域](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) 和 [容錯網域](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains)的 VM 實例中建立失衡。 此不平衡可能會對系統在服務實例/服務複本之間進行適當負載平衡的能力造成負面影響。
* 如果使用自動調整，請設定規則，以 (移除 VM 實例，) 作業一次只執行一個節點。 一次相應減少超過一個執行個體並不安全。
* 如果刪除或解除配置主要節點類型上的 Vm，絕對不能將已配置的 Vm 計數減少到低於可靠性層級所需的數目。 在持久性層級為 Silver 或 Gold 的擴展集中，這些作業將會無限期地遭到封鎖。

### <a name="changing-durability-levels"></a>變更持久性層級

在某些條件約束中，可以調整節點類型持久性層級：

* 持久性層級為銀級或金級的節點類型無法降級為銅級。
* 從 Bronze 升級至 Silver 或 Gold 可能需要幾小時的時間。
* 變更持久性層級時，請務必在虛擬機器擴展集資源的 Service Fabric 延伸模組設定中，以及 Service Fabric 叢集資源的節點類型定義中更新它。 這些值必須相符。

容量規劃是您叢集的可靠性層級時的另一項考慮，它會決定系統服務和整體叢集的穩定性，如下一節所述。

## <a name="reliability-characteristics-of-the-cluster"></a>叢集的可靠性特性

叢集 *可靠性層級* 會決定在叢集的主要節點類型上執行的系統服務複本數目。 複本越多，系統服務 (也越可靠，因此整個) 。

> [!IMPORTANT]
> 可靠性層級是在叢集層級設定，並決定主要節點類型的節點數目下限。 生產工作負載需要 (大於或等於) 或更高的五個節點，才能使用銀級的銀級。  

可靠性層級可以採用以下的值：

* **白金** 級系統服務執行時的目標複本集計數為9
* 具 **金** 級的系統服務會以七個目標複本集計數執行
* **銀** 級系統服務執行時的目標複本集計數為五
* **銅牌** -系統服務執行時的目標複本集計數為三

以下是選擇可靠性層級的建議。 種子節點數目也會設定為適用於可靠性層的節點數目下限。


| **節點數目** | **可靠性層級** |
| --- | --- |
| 1 | *請勿指定 `reliabilityLevel` 參數：系統會計算參數。* |
| 3 | 青銅卡 |
| 5 或 6| 銀色 |
| 7 或 8 | 金卡 |
| 9 以上 | Platinum |

當您增加或減少叢集的大小 (所有節點類型中) 的 VM 實例總和，請考慮將叢集的可靠性從一個層級更新為另一個層級。 如此一來就會觸發變更系統服務複本集計數所需的叢集升級。 請先等候升級完成，再對叢集進行任何其他變更，例如新增節點。  您可以在 Service Fabric Explorer 上或執行 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) 監視升級的進度

### <a name="capacity-planning-for-reliability"></a>可靠性的容量規劃

叢集的容量需求取決於您的特定工作負載和可靠性需求。 本節提供可協助您開始進行容量規劃的一般指引。

#### <a name="virtual-machine-sizing"></a>虛擬機器大小

**針對生產工作負載，建議的 VM 大小 (SKU) 是 [標準 D2_V2](../virtual-machines/dv2-dsv2-series.md) (或對等的) ，最少 50 GB 的本機 SSD、2核心和 4 GiB 的記憶體。** 建議至少使用 50 GB 的本機 SSD，不過某些工作負載 (例如執行 Windows 容器的工作負載) 需要較大的磁片。 選擇生產工作負載的其他 [VM 大小時](../virtual-machines/sizes-general.md) ，請記住下列限制：

- 不支援如標準 A0 的部分核心 VM 大小。
- *A 系列* 基於效能考慮，不支援 VM 大小。
- 不支援低優先順序的 VM。

#### <a name="primary-node-type"></a>主要節點類型

Azure 上的 **生產工作負載** 至少需要五個主要節點 (VM 實例) 和您的銀級可靠性層。 建議將叢集主要節點類型專用於系統服務，並使用放置條件約束將應用程式部署到次要節點類型。

Azure 中的 **測試工作負載** 最少可執行一或三個主要節點。 若要設定一個節點叢集，請確定您的 `reliabilityLevel` Resource Manager 範本中的設定完全省略， (指定的空字串值的 `reliabilityLevel`) 不足。 如果您使用 Azure 入口網站設定一個節點叢集，則會自動完成此設定。

> [!WARNING]
> 單一節點叢集會以不具可靠性的特殊設定執行，不支援 scale out。

#### <a name="non-primary-node-types"></a>非主要節點類型

非主要節點類型的節點數目下限取決於節點類型的特定 [持久性層級](#durability-characteristics-of-the-cluster) 。 您應該根據您想要針對節點類型執行的應用程式或服務複本數目，以及根據工作負載是具狀態或無狀態，來規劃 (和持久性層級) 的節點數目。 請記住，在部署叢集之後，您可以隨時增加或減少節點類型中的 Vm 數目。

##### <a name="stateful-workloads"></a>具狀態工作負載

針對使用 Service Fabric [可靠集合或可靠](service-fabric-choose-framework.md)動作專案的具狀態生產工作負載，建議使用最小和目標複本計數5。 如此一來，在穩定狀態下，您最後會得到複本 (，從每個容錯網域和升級網域中的複本集) 。 一般情況下，請使用您針對「系統服務」所設定的可靠性層級，作為具狀態服務所使用之複本計數的指南。

##### <a name="stateless-workloads"></a>無狀態工作負載

針對無狀態的生產工作負載，支援的最小非主要節點類型大小為三個維護仲裁，但建議使用節點類型大小5。

## <a name="next-steps"></a>後續步驟

在設定叢集之前，請先檢查叢集 `Not Allowed` [升級原則](service-fabric-cluster-fabric-settings.md) ，以便在稍後因為無法變更系統組態設定而重新建立叢集。

如需叢集規劃的詳細資訊，請參閱：

* [計算規劃和調整](service-fabric-best-practices-capacity-scaling.md)
* [Service Fabric 應用程式的容量規劃](service-fabric-capacity-planning.md)
* [災害復原規劃](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
