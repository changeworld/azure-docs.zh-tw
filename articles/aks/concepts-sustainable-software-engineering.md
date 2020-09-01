---
title: '概念-Azure Kubernetes Services 中的持續性軟體工程 (AKS) '
description: 瞭解 Azure Kubernetes Service (AKS) 中的持續性軟體工程。
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: bc2240b3cb8508109f6dd25c4c80ff31ba197cfd
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083584"
---
# <a name="sustainable-software-engineering-principals-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS 中的持續性軟體工程主體) 

持續性軟體工程主體是一組專長認證，可協助您定義、建立及執行持續性的應用程式。 整體目標是要降低應用程式各個層面的碳足跡。 這些 [原則。環保專案][principals-green] 概述持續性軟體工程的主體。

瞭解持續性軟體工程的一個重要概念是，它是優先順序和焦點的轉移。 在許多情況下，軟體是以專注于快速效能和低延遲的方式來設計和執行。 持續性軟體工程著重于盡可能減少碳排放量。 在某些情況下，套用持續性軟體工程主體可提供更快速的效能或較低的延遲，例如降低總網路移動量。 在其他情況下，減少碳排放可能會導致效能變慢或增加延遲，例如延遲低優先順序工作負載。 在考慮將持續性軟體工程主體套用至您的應用程式之前，請先檢查應用程式的優先順序、需求和取捨。

## <a name="measure-and-optimize"></a>測量和優化

若要降低 AKS 叢集的碳足跡，您需要瞭解叢集資源的使用方式。 [Azure 監視器][azure-monitor] 提供叢集資源使用量的詳細資料，例如記憶體和 CPU 使用量。 這項資料可協助通知您的決策，減少叢集的碳足跡，並觀察變更的影響。 您也可以安裝 Microsoft 持續性 [計算機][sustainability-calculator] 來查看您所有 Azure 資源的碳足跡。

## <a name="increase-resource-utilization"></a>提高資源使用率

減少碳足跡的其中一種方法是減少計算資源的閒置時間量。 減少閒置時間需要增加計算資源的使用率。 例如，如果您的叢集中有四個節點，每個節點都在50% 的容量執行，則您的所有四個節點都會有50% 的未使用容量閒置。 如果您將叢集縮減為三個節點，則相同的工作負載會導致您的三個節點以67% 的容量執行，將您未使用的容量減少到每個節點上的33%，並增加您的使用率。

> [!IMPORTANT]
> 考慮對叢集中的資源進行變更時，請確認您的 [系統][system-pools] 集區具有足夠的資源，可維護叢集核心系統元件的穩定性。 請勿將叢集的資源減少到叢集可能不穩定的時間點。

檢查叢集的使用率之後，請考慮使用 [多個節點][multiple-node-pools]集區所提供的功能。 您可以使用 [節點大小調整][node-sizing] 來定義具有特定 CPU 和記憶體設定檔的節點集區，讓您根據工作負載需求量身打造您的節點。 將節點大小調整為您的工作負載需求，可協助您以更高的使用率執行幾個節點。 您也可以設定叢集的 [調整][scale] 方式，並使用 [水準 pod 自動調整程式][scale-horizontal] 和叢集 [自動調整程式][scale-auto] ，根據您的設定自動調整您的叢集。 控制叢集調整的方式，可協助您讓所有節點在高使用率的情況下執行，同時維持對叢集工作負載的變更。 針對工作負載可容忍突然中斷或終止的情況，您可以使用「 [點][spot-pools] 」集區來利用 Azure 中的閒置容量。 例如，「現成集區」適用于批次作業或開發環境。

提高使用率也可以減少多餘的節點，進而減少 [每個節點的資源保留][resource-reservations]所耗用的能源。

另請參閱應用程式 Kubernetes 資訊清單中的 CPU 和記憶體 *要求* 和 *限制* 。 當您降低這些記憶體和 CPU 的值時，叢集就可以使用更多的記憶體和 CPU 來執行其他工作負載。 當您執行更多具有較低 CPU 和記憶體的工作負載時，您的叢集會變得更密集配置，以增加您的使用率。 減少應用程式的 CPU 和記憶體時，如果您將這些值設得太低，應用程式的行為可能會變差或不穩定。 在變更 CPU 和記憶體 *要求* 和 *限制*之前，請考慮執行一些基準測試，以瞭解是否已適當設定這些值。 此外，當您的應用程式變得不穩定時，永遠不會將這些值降到點。

## <a name="reduce-network-travel"></a>減少網路移動

減少對您叢集的往返要求和回應，通常會減少網路裝置的電力耗用量，並減少碳排放量。 檢查網路流量之後，請考慮在接近您網路流量來源的 [區域中][regions] 建立叢集。 您也可以使用 [Azure 流量管理員][azure-traffic-manager] 來協助將流量路由至最接近的叢集和 [鄰近放置群組][proiximity-placement-groups] ，以協助減少 Azure 資源之間的距離。

> [!IMPORTANT]
> 當您考慮對叢集的網路進行變更時，絕對不會因為符合工作負載需求而減少網路移動的成本。 例如，使用 [可用性區域][availability-zones] 會導致您的叢集有更多網路移動，但可能需要使用該功能來處理工作負載需求。

## <a name="demand-shaping"></a>需求塑造

可能的話，請考慮將叢集資源的需求轉移至您可以使用超過容量的時間或區域。 例如，請考慮將批次作業的時間或區域變更為執行或使用 [點][spot-pools]集區。 也請考慮重構應用程式，以使用佇列來順延強制不需要立即處理的工作負載。

## <a name="next-steps"></a>後續步驟

深入瞭解本文中所述的 AKS 功能：

* [多個節點集區][multiple-node-pools]
* [節點大小調整][node-sizing]
* [調整叢集規模][scale]
* [水平 Pod 自動調整程式][scale-horizontal]
* [叢集自動調整程式][scale-auto]
* [找出集區][spot-pools]
* [系統集區][system-pools]
* [資源保留][resource-reservations]
* [鄰近位置群組][proiximity-placement-groups]
* [可用性區域][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principals-green]: https://principles.green/