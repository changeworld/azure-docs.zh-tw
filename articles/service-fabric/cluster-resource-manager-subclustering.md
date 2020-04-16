---
title: 子群集指標的平衡
description: 放置約束對平衡的影響及如何處理
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430639"
---
# <a name="balancing-of-subclustered-metrics"></a>子群集指標的平衡

## <a name="what-is-subclustering"></a>什麼是子群集

當具有不同放置約束的服務具有通用指標並且它們都報告其負載時,就會發生子群集。 如果服務報告的負載差異很大,則節點上的總負載將具有較大的標準偏差,並且即使具有最佳平衡,群集看起來也不平衡。

## <a name="how-subclustering-affects-load-balancing"></a>子群集如何影響負載平衡

如果服務在不同節點上報告的負載差異很大,則可能看起來存在一個不存在的大不平衡。 此外,如果子聚類引起的假不平衡大於實際不平衡,則有可能混淆資源管理器平衡演演演算法,並在群集中產生次優平衡。

例如,假設我們有四個服務,它們都報告指標公制1的負載:

* 服務 A = 具有放置約束「NodeType_Type1」,報告負載為 10
* 服務 B = 具有放置約束「NodeType_Type1」,報告負載為 10
* 服務 C = 具有放置約束「NodeType_Type2」,報告負載為 100
* 服務 D = 具有放置約束「NodeType_Type2」,報告負載為 100
* 我們有四個節點。 其中兩個將 NodeType 設置為"類型 1",另外兩個設置為"類型 2"

我們有以下位置:

<center>

![子叢集放置範例][Image1]
</center>

群集可能看起來不平衡,我們在節點 3 和 4 上負載較大,但在此情況下,此放置可以創造最佳平衡。

資源管理器可以識別子群集情況,在幾乎所有情況下,它可以為給定的情況產生最佳平衡。

對於某些特殊情況,當資源管理器無法以最佳方式平衡子群集指標時,它仍將檢測到子群集,並將生成運行狀況報告,建議您解決問題。

## <a name="types-of-subclustering-and-how-they-are-handled"></a>子叢集的類型及其處理方式

子群集情況可分為三個不同的類別。 特定子群集情況的類別決定了資源管理器將如何處理這種情況。

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>第一類 = 具有不相交節點群組的平面子群集

此類別具有最簡單的子群集形式,其中節點可以分隔成不同的組,並且每個服務只能放置在這些組中的節點上。 每個節點僅屬於一個組和一個組。 上述情況屬於此類別,大多數子群集情況都屬於此類別。 

對於此類別中的情況,資源管理器可以產生最佳平衡,無需進一步干預。

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>第二類 = 分層節點群組的子叢集

當一個服務允許的一組節點是另一個服務允許的節點組的子集時,就會發生這種情況。 這種情況的最常見示例是,某些服務定義了放置約束,而另一個服務沒有放置約束,可以放置在任何節點上。

範例：

* 服務 A:無放置約束
* 服務 B:放置約束"節點類型=類型 1"
* 服務 C:放置約束"節點類型=類型 2"

此配置在不同服務的節點組之間創建子集超集關係。

<center>

![子集超級集子群集][Image2]
</center>

在這種情況下,有可能取得次優平衡。

資源管理員將識別這種情況並生成運行狀況報告,建議您將服務 A 拆分為兩個服務 - 可放置在 Type1 節點的服務 A1 和服務 A2 可放置在 Type2 節點上。 這將使我們回到第一類情況,可以優化平衡。

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>第三類 – 節點集之間部分重疊的子群集

當節點集之間部分重疊時,就會發生這種情況,某些服務可以放在這些節點上。

例如,如果我們有一個節點屬性稱為 NodeColor,並且我們有三個節點:

* 節點 1:節點顏色=紅色
* 節點 2:節點顏色藍色
* 節點 2:節點顏色=綠色

我們有兩種服務:

* 服務 A:帶放置約束「顏色=紅色 |顏色=藍色"
* 服務 B:帶放置約束「顏色=藍色 |顏色=綠色"

因此,服務 A 可以放置在節點 1 和 2 上,服務 B 可以放置在節點 2 和 3 上。

在這種情況下,有可能取得次優平衡。

資源管理員將認識到這種情況,並生成運行狀況報告,建議您拆分某些服務。

對於這種情況,資源管理器無法給出如何拆分服務的建議,因為可以執行多個拆分,並且無法估計哪種方式是拆分服務的最佳方式。

## <a name="configuring-subclustering"></a>設定子叢集

可以透過修改以下設定參數來修改資源管理員有關子群集的行為:
* 子群集啟用 - 參數確定資源管理員在進行負載平衡時是否會考慮子群集。 如果關閉此參數,資源管理器將忽略子群集,並嘗試在全域級別上實現最佳平衡。 此參數的預設值為 false。
* 子叢集報告策略 - 確定資源管理員如何為分層和部分重疊子群集發出運行狀況報告。 值為零意味著關閉有關子群集的運行狀況報告,"1"表示將針對次優次群集情況生成警告運行狀況報告,並且值"2"將生成"確定"運行狀況報告。 此參數的預設值為"1"。

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
* 要瞭解如何將服務限制為僅放置在某些節點上,請參閱[Node 屬性和放置約束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
