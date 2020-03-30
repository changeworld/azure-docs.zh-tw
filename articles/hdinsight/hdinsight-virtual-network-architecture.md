---
title: Azure HDInsight 虛擬網路架構
description: 瞭解在 Azure 虛擬網路中創建 HDInsight 群集時可用的資源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b3f622b360f565ef5b16d5376cb1aa2498655017
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272145"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 虛擬網路架構

本文介紹將 HDInsight 群集部署到自訂 Azure 虛擬網路時存在的資源。 此資訊將説明您將本地資源連接到 Azure 中的 HDInsight 群集。 有關 Azure 虛擬網路的詳細資訊，請參閱[什麼是 Azure 虛擬網路？](../virtual-network/virtual-networks-overview.md)

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight 群集中的資源類型

Azure HDInsight 群集具有不同類型的虛擬機器或節點。 每個節點類型在系統操作中起著一定的作用。 下表總結了這些節點類型及其在群集中的角色。

| 類型 | 描述 |
| --- | --- |
| 前端節點 |  對於除 Apache Storm 之外的所有群集類型，頭節點承載管理分散式應用程式執行的進程。 頭節點也是可以將 SSH 引入並執行應用程式，然後協調以跨群集資源運行。 對於所有群集類型，頭節點數固定為兩個。 |
| 動物園守護者節點 | Zookeeper 協調執行資料處理的節點之間的任務。 它還執行頭節點的引線選擇，並跟蹤運行特定主服務的頭節點。 ZooKeeper 節點的數量固定為 3 個。 |
| 背景工作節點 | 表示支援資料處理功能的節點。 可以從群集添加或刪除輔助節點，以擴展計算功能和管理成本。 |
| R 伺服器邊緣節點 | R Server 邊緣節點表示可以將 SSH 引入並執行應用程式，然後協調這些應用程式以跨群集資源運行。 邊緣節點不參與群集中的資料分析。 此節點還承載 R Studio Server，使您能夠使用瀏覽器運行 R 應用程式。 |
| 區域節點 | 對於 HBase 群集類型，區域節點（也稱為資料節點）運列區域伺服器。 區域伺服器服務和管理由 HBase 管理的一部分資料。 可以從群集添加或刪除區域節點，以擴展計算功能和管理成本。|
| 尼姆布斯節點 | 對於 Storm 群集類型，Nimbus 節點提供類似于頭節點的功能。 Nimbus 節點通過 Zookeeper 將任務分配給群集中的其他節點，Zookeeper 協調 Storm 拓撲的運行。 |
| 主管節點 | 對於 Storm 群集類型，主管節點執行 Nimbus 節點提供的指令以執行所需的處理。 |

## <a name="resource-naming-conventions"></a>資源命名約定

在定址群集中的節點時，請使用完全限定的功能變數名稱 （FQDN）。 您可以使用[Ambari API](hdinsight-hadoop-manage-ambari-rest-api.md)獲取群集中各種節點類型的 FQDN。 

這些 FQDN 將的形式`<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`。

對於`<node-type-prefix>`頭節點，將為*輔助*節點 *，為*輔助節點，為動物園管理員節點的*zn。*

如果只需要主機名稱，則僅使用 FQDN 的第一部分：`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>基本虛擬網路資源

下圖顯示了 HDInsight 節點和網路資源在 Azure 中的放置位置。

![在 Azure 自訂 VNET 中創建的 HDInsight 實體圖](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

將 HDInsight 部署到 Azure 虛擬網路時存在的預設資源包括上表中提及的叢集節點類型，以及支援虛擬網路與外部網路之間通信的網路設備。

下表總結了在將 HDInsight 部署到自訂 Azure 虛擬網路時創建的九個叢集節點。

| 資源類型 | 存在的數量 | 詳細資料 |
| --- | --- | --- |
|前端節點 | two |    |
|Zookeeper 節點 | three | |
|背景工作節點 | two | 此數位可能因群集配置和縮放而異。 Apache Kafka 至少需要三個輔助節點。  |
|閘道節點 | two | 閘道節點是在 Azure 上創建的 Azure 虛擬機器，但在訂閱中不可見。 如果需要重新開機這些節點，請與支援部門聯繫。 |

在與 HDInsight 一起使用的虛擬網路內自動創建以下網路資源：

| 網路資源 | 存在的數量 | 詳細資料 |
| --- | --- | --- |
|負載平衡器 | three | |
|網路介面 | 九 | 此值基於一個普通群集，其中每個節點都有自己的網路介面。 這九個介面用於上表中提到的兩個頭節點、三個 Zookeeper 節點、兩個輔助節點和兩個閘道節點。 |
|公用 IP 位址 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>連接到 HDInsight 的終結點

您可以通過三種方式訪問 HDInsight 群集：

- 在`CLUSTERNAME.azurehdinsight.net`的虛擬網路外部的 HTTPS 終結點。
- 用於直接連接到 頭節點的`CLUSTERNAME-ssh.azurehdinsight.net`SSH 終結點。
- 虛擬網路中`CLUSTERNAME-int.azurehdinsight.net`的 HTTPS 終結點。 請注意此 URL 中的"int"。 此終結點將解析為該虛擬網路中的專用 IP，並且無法從公共 Internet 訪問。

這三個端點都分配了一個負載等化器。

公共 IP 位址也提供給允許從虛擬網路外部連接的兩個終結點。

1. 一個公共 IP 分配給負載等化器，用於完全限定的功能變數名稱 （FQDN） 在從 Internet`CLUSTERNAME.azurehdinsight.net`連接到群集時使用。
1. 第二個公共 IP 位址用於僅 SSH`CLUSTERNAME-ssh.azurehdinsight.net`功能變數名稱。

## <a name="next-steps"></a>後續步驟

- [使用專用終結點在虛擬網路中保護對 HDInsight 群集的傳入流量](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
