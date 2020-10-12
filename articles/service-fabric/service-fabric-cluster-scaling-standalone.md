---
title: Azure Service Fabric 獨立叢集調整規模
description: 了解 Service Fabric 獨立叢集的相應縮小、相應放大、相應增加或相應減少。
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 8184b2455e938fa0500308b462176e78b8dadcab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843026"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>調整 Service Fabric 獨立叢集
Service Fabric 叢集是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 屬於叢集一部分的機器或 VM 都稱為節點。 叢集有可能包含數千個節點。 在建立 Service Fabric 叢集之後，您可以水平調整叢集 (變更節點數目)，或以垂直方式調整 (變更節點的資源)。  您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。  在叢集進行調整時，您的應用程式也會自動調整。

為什麼要調整叢集？ 應用程式需求會隨著時間而變更。  您可能需要增加叢集資源以因應增加的應用程式工作負載或網路流量，或是在需要下降時減少叢集資源。

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>相應縮小和相應放大，或水平調整
變更叢集中的節點數目。  在新的節點加入叢集後，[叢集資源管理員](service-fabric-cluster-resource-manager-introduction.md)會將服務移至這些節點，而降低現有節點上的負載。  若叢集的資源未有效率地使用，您也可以減少節點數目。  當節點退出叢集時，服務即會從這些節點中移出，而其餘節點上的負載將會增加。  減少在 Azure 中執行之叢集中的節點數目可以節省成本，因為您只需對您所使用的 VM 數目付費，而不是對這些 VM 上的工作負載付費。  

- 優點：無限制的調整 (理論上)。  如果您的應用程式是針對延展性進行設計，您可以藉由新增更多節點而達到無限制的成長。  雲端環境中的工具使得節點的新增或移除更為簡便，因此您可以輕鬆地調整容量，且只需為使用的資源付費。  
- 缺點：應用程式必須[針對延展性進行設計](service-fabric-concepts-scalability.md)。  應用程式資料庫與持續性也可能需要仰賴更多架構工作來進行調整。  不過，Service Fabric 具狀態服務中的[可靠集合](service-fabric-reliable-services-reliable-collections.md)大幅降地了調整應用程式資料的難度。

獨立叢集可讓您在內部部署 Service Fabric 叢集，或是在您選擇的雲端提供者中部署。  節點類型包含實體機器或虛擬機器，視您的部署而定。 相較於在 Azure 中執行的叢集，調整獨立叢集的程序會稍微複雜一些。  您必須手動變更叢集中的節點數目，然後執行叢集組態升級。

移除節點可能會起始多個升級作業。 有些節點會標示 `IsSeedNode=”true”` 標記，而可藉由使用 [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) 來查詢叢集資訊清單而加以識別。 移除這類節點所需的時間可能比移除其他節點長，因為在這類案例中，需要將種子節點四處移動。 叢集必須至少維持三個主要節點類型的節點。

> [!WARNING]
> 我們建議不要使節點計數低於該叢集之[可靠性層級的叢集大小](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)。 這將會影響「Service Fabric 系統服務」複寫整個叢集的能力，而將使叢集變得不穩定或甚至損毀。
>

在調整獨立叢集時，請記住下列指導方針︰
- 應以逐一取代主要節點的方式來執行，而不是以批次方式移除後再加入。
- 移除節點之前，請檢查是否有任何節點參考該節點類型。 請先移除這些節點，然後才移除對應的節點類型。 移除所有對應的節點之後，您便可以將該 NodeType 自叢集組態中移除，然後使用 [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) 來開始組態升級。

如需詳細資訊，請參閱[調整獨立叢集](service-fabric-cluster-windows-server-add-remove-nodes.md)。

## <a name="scaling-up-and-down-or-vertical-scaling"></a>相應增加和相應放大，或垂直調整 
變更叢集中節點的資源 (CPU、記憶體或儲存體)。
- 優點：軟體和應用程式架構保持不變。
- 缺點：有限的調整，因為您可以在個別節點上增加的資源有數目上的限制。 停機時間，因為您必須使實體或虛擬機器離線，才能新增或移除資源。

## <a name="next-steps"></a>後續步驟
* 深入了解[應用程式延展性](service-fabric-concepts-scalability.md)。
* [將 Azure 叢集相應縮小或相應放大](service-fabric-tutorial-scale-cluster.md)。
* 使用 Fluent Azure 計算 SDK，[以程式設計方式調整 Azure 叢集](service-fabric-cluster-programmatic-scaling.md)。
* [將獨立叢集相應縮小或相應放大](service-fabric-cluster-windows-server-add-remove-nodes.md)。

