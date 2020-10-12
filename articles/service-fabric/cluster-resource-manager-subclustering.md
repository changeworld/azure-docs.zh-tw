---
title: Subclustered 計量的平衡
description: 放置條件約束對平衡的影響，以及如何處理它
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82183106"
---
# <a name="balancing-of-subclustered-metrics"></a>Subclustered 計量的平衡

## <a name="what-is-subclustering"></a>什麼是子叢集

當具有不同放置條件約束的服務具有共同的計量，而且它們都報告負載時，就會發生子叢集。 如果服務所報告的負載有很大的差異，節點的總負載將會有較大的標準差，且看起來像叢集不平衡，即使它有最可能的平衡。

## <a name="how-subclustering-affects-load-balancing"></a>子叢集如何影響負載平衡

如果不同節點上的服務所報告的負載有很大的差異，則在沒有任何情況下，可能會有很大的不平衡。 此外，如果子叢集所造成的錯誤不平衡大於實際的不平衡，則可能會將 Resource Manager 平衡演算法和產生叢集中的次平衡。

例如，假設我們有四個服務，而且它們都會報告計量 Metric1 的負載：

* 服務 A –具有放置條件約束 "NodeType = = 前端"，報告的負載為10
* 服務 B –具有放置條件約束 "NodeType = = 前端"，報告的負載為10
* Service C –具有放置條件約束 "NodeType = = 後端"，報告100的負載
* 服務 D –具有放置條件約束 "NodeType = = 後端"，報告100的負載
* 而且有四個節點。 其中兩個會將 NodeType 設定為 "前端"，而其他兩個則為 "後端"

我們有下列位置：

<center>

![Subclustered 放置範例][Image1]
</center>

叢集看起來可能不對稱、節點3和4有很大的負載，但在這種情況下，這項放置會產生最佳的平衡。

Resource Manager 可以辨識子叢集的情況，而且在幾乎所有情況下，它都可以針對指定的情況產生最佳平衡。

在某些特殊情況下 Resource Manager 無法以最佳方式平衡 subclustered 計量時，仍會偵測到子叢集，並且會產生健康情況報告，以建議您修正問題。

## <a name="types-of-subclustering-and-how-they-are-handled"></a>子叢集類型及其處理方式

子叢集的情況可以分為三種不同的類別。 特定子叢集狀況的類別會決定如何處理 Resource Manager。

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>第一個類別–具有斷續節點群組的一般子叢集

此類別具有最簡單的子叢集形式，其中節點可分成不同的群組，而且每個服務只能放在其中一個群組的節點上。 每個節點都屬於一個群組和一個群組。 以上所述的情況屬於這類子叢集的情況。 

針對此類別目錄中的情況，Resource Manager 可以產生最佳平衡，而不需要進一步的介入。

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>第二個類別–使用階層式節點群組子叢集

當某個服務允許的節點群組是另一個服務允許的節點群組子集時，就會發生這種情況。 這種情況最常見的範例是某些服務已定義放置條件約束，而另一個服務沒有放置條件約束，而且可以放在任何節點上。

範例：

* 服務 A：沒有放置條件約束
* 服務 B：放置條件約束 "NodeType = = 前端"
* 服務 C：放置條件約束 "NodeType = = 後端"

此設定會在不同服務的節點群組之間建立子集超集合的關聯性。

<center>

![子集超集合子群集][Image2]
</center>

在這種情況下，有可能會產生次佳平衡。

Resource Manager 將會辨識這種情況，並產生健康情況報告，告知您將服務 A 分割成兩個服務–服務 A1，可放置在可放置於後端節點的前端節點和服務 A2。 這會讓我們回到可獲得最佳平衡的第一種類別。

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>第三個類別–子叢集與節點集之間的部分重迭

當可放置某些服務的節點集合之間有部分重迭時，就會發生這種情況。

例如，如果我們有一個稱為 NodeColor 的節點屬性，而且有三個節點：

* 節點1： NodeColor = 紅色
* 節點2： NodeColor = 藍色
* 節點2： NodeColor = 綠色

另外還有兩個服務：

* 服務 A：具有放置條件約束 "Color = = Red | |Color = = Blue "
* 服務 B：具有放置條件約束 "Color = = Blue | |Color = = 綠色 "

因此，服務 A 可放置在節點1和2，而服務 B 則可以放置在節點2和3。

在這種情況下，有可能會產生次佳平衡。

Resource Manager 將會辨識這種情況並產生健康情況報告，建議您分割某些服務。

在這種情況下，Resource Manager 不能提供建議程式如何分割服務，因為可以完成多個分割，而且沒有辦法評估哪一種方法是分割服務的最佳方式。

## <a name="configuring-subclustering"></a>設定子叢集

您可以修改下列設定參數，以修改有關子叢集 Resource Manager 的行為：
* SubclusteringEnabled-參數會決定在進行負載平衡時，Resource Manager 是否會將子叢集列入考慮。 如果關閉此參數，Resource Manager 將會忽略子叢集，並嘗試在全域層級上達到最佳平衡。 此參數的預設值為 false。
* SubclusteringReportingPolicy-決定 Resource Manager 將如何發出階層式和部分重迭子叢集的健康情況報告。 值為零表示子叢集的健康情況報告已關閉，"1" 表示將會產生較佳子叢集情況的警告健康情況報告，且值為 "2" 將會產生「確定」健康情況報告。 此參數的預設值為 "1"。

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

## <a name="next-steps"></a>接下來的步驟
* 若要了解叢集資源管理員如何管理並平衡叢集中的負載，請查看關於 [平衡負載](service-fabric-cluster-resource-manager-balancing.md)
* 若要瞭解如何將服務限制為只放置在特定節點上，請參閱 [節點屬性和放置條件約束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
