---
title: Subclustered 計量的平衡
description: 放置條件約束對平衡和處理方式的影響
author: nipavlo
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: nipavlo
ms.openlocfilehash: fe41f45399670d5ac003e5da6c8da0fb1e0847b2
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081640"
---
# <a name="balancing-of-subclustered-metrics"></a>Subclustered 計量的平衡

## <a name="what-is-subclustering"></a>什麼是 subclustering

當具有不同放置條件約束的服務具有共同的計量，而且兩者都報告負載時，就會發生 Subclustering。 如果服務所報告的負載有很大的差異，節點上的總負載將會有一個大型的標準差，而它看起來像是不平衡叢集，即使它有最可能的平衡。

## <a name="how-subclustering-affects-load-balancing"></a>Subclustering 如何影響負載平衡

如果不同節點上的服務所報告的負載有很大的差異，可能會看起來有一個不平衡的情況，其中沒有任何一個。 此外，如果 subclustering 所造成的錯誤不平衡大於實際的不平衡，可能會混淆 Resource Manager 平衡演算法，並在叢集中產生較佳的平衡。

例如，假設我們有四個服務，而且它們都報告計量 Metric1 的負載：

* 服務 A –具有放置條件約束 "NodeType = = Type1"，報告的負載為10
* 服務 B –具有放置條件約束 "NodeType = = Type1"，報告的負載為10
* 服務 C –具有放置條件約束 "NodeType = = Type2"，報告100的負載
* 服務 D –具有放置條件約束 "NodeType = = Type2"，報告100的負載
* 而且我們有四個節點。 其中兩個將 NodeType 設定為 "Type1"，而其他兩個為 "Type2"

我們有下列位置：

<center>

![Subclustered 位置範例][Image1]
</center>

叢集看起來可能不對稱，我們在節點3和4上有很大的負載，但此位置在此情況下會產生最佳的平衡。

Resource Manager 可以辨識 subclustering 的情況，而在幾乎所有情況下，它可能會針對給定的情況產生最佳平衡。

在某些例外狀況下，當 Resource Manager 無法以最佳方式平衡 subclustered 度量時，仍會偵測到 subclustering，並產生健康情況報告，以建議您修正問題。

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Subclustering 的類型及其處理方式

Subclustering 的情況可以分類為三種不同的類別。 特定 subclustering 狀況的類別會決定 Resource Manager 處理的方式。

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>第一個類別–具有不相鄰節點群組的平面 subclustering

此類別具有最簡單的 subclustering 形式，其中節點可以分割成不同的群組，而且每個服務只能放在其中一個群組的節點上。 每個節點都屬於一個群組和一個群組。 上面所述的情況屬於此類別，如同大部分的 subclustering 情況。 

針對此類別中的情況，Resource Manager 可以產生最佳平衡，而不需要進一步的介入。

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>第二個類別–具有階層式節點群組的 subclustering

當某個服務允許的節點群組是另一個服務允許的節點群組子集時，就會發生這種情況。 這種情況最常見的範例是某些服務已定義放置條件約束，而另一個服務沒有放置條件約束，而且可以放在任何節點上。

範例：

* 服務 A：沒有放置條件約束
* 服務 B：放置條件約束 "NodeType = = Type1"
* 服務 C：放置條件約束 "NodeType = = Type2"

此設定會在不同服務的節點群組之間建立子集關聯性。

<center>

![子集超集合 subclusters][Image2]
</center>

在這種情況下，可能會產生不佳的平衡。

Resource Manager 將會辨識這種情況，並產生健康情況報告，建議您將服務 A 分割成兩個服務–服務 A1 可以放在可放在 Type2 節點上的 Type1 節點和服務 A2。 這會讓我們回到第一個類別的情況，而這種情況可以達到最佳平衡。

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>第三個類別–節點集之間具有部分重迭的 subclustering

這種情況發生在可以放置某些服務的節點集之間有部分重迭的情況。

例如，如果我們有一個稱為 NodeColor 的節點屬性，而且有三個節點：

* 節點1： NodeColor = 紅色
* 節點2： NodeColor = Blue
* 節點2： NodeColor = 綠色

我們有兩個服務：

* 服務 A：具有放置條件約束 "Color = = Red | |Color = = Blue "
* 服務 B：具有放置條件約束 "Color = = Blue | |Color = = 綠色」

因此，服務 A 可以放在節點1和2上，而服務 B 可以放在節點2和3上。

在這種情況下，可能會產生不佳的平衡。

Resource Manager 會辨識這種情況，並產生健康情況報告，建議您分割一些服務。

在此情況下，Resource Manager 無法提供如何分割服務的建議，因為可以完成多個分割，而且無法估計哪一種方法是分割服務的最佳方式。

## <a name="configuring-subclustering"></a>設定 subclustering

您可以藉由修改下列設定參數來修改 subclustering 的相關 Resource Manager 行為：
* SubclusteringEnabled-參數會決定在進行負載平衡時，Resource Manager 是否會將 subclustering 納入考慮。 如果此參數已關閉，Resource Manager 將會忽略 subclustering，並嘗試在全域層級達到最佳平衡。 此參數的預設值為 false。
* SubclusteringReportingPolicy-決定 Resource Manager 如何發出階層式和部分重迭 subclustering 的健康情況報告。 值為零表示已關閉關於 subclustering 的健康情況報告，"1" 表示將會針對較佳的 subclustering 情況產生警告健康情況報告，而 "2" 的值將會產生「確定」健康狀態報表。 此參數的預設值為 "1"。

ClusterManifest.xml：

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

獨立部署透過 ClusterConfig.json，Azure 託管叢集透過 Template.json：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>後續步驟
* 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於 [平衡負載](service-fabric-cluster-resource-manager-balancing.md)
* 若要瞭解如何將您的服務限制為僅放在特定節點上，請參閱[節點屬性和放置條件約束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/service-fabric-cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/service-fabric-cluster-resource-manager-subclustering/subset-superset-nodes.png
