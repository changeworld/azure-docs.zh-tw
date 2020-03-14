---
title: Azure HDInsight 虛擬網路架構
description: 瞭解當您在 Azure 虛擬網路中建立 HDInsight 叢集時可用的資源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b3f622b360f565ef5b16d5376cb1aa2498655017
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272145"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 虛擬網路架構

本文說明當您將 HDInsight 叢集部署至自訂 Azure 虛擬網路時，所出現的資源。 這項資訊可協助您將內部部署資源連接到 Azure 中的 HDInsight 叢集。 如需 Azure 虛擬網路的詳細資訊，請參閱[什麼是 azure 虛擬網路？](../virtual-network/virtual-networks-overview.md)。

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight 叢集中的資源類型

Azure HDInsight 叢集具有不同類型的虛擬機器或節點。 每種節點類型在系統的作業中扮演著角色。 下表摘要說明這些節點類型及其在叢集中的角色。

| 類型 | 描述 |
| --- | --- |
| 前端節點 |  對於除了 Apache Storm 以外的所有叢集類型，前端節點會主控管理分散式應用程式執行的進程。 前端節點也是您可以透過 SSH 連線並執行應用程式的節點，然後協調以在叢集資源上執行。 所有叢集類型的前端節點數目固定為兩個。 |
| ZooKeeper 節點 | Zookeeper 會在執行資料處理的節點之間協調工作。 它也會對前端節點進行前導選擇，並追蹤哪個前端節點正在執行特定的主要服務。 ZooKeeper 節點的數目固定為三。 |
| 背景工作節點 | 表示支援資料處理功能的節點。 您可以在叢集中新增或移除背景工作節點，以調整運算能力和管理成本。 |
| R 伺服器邊緣節點 | R Server edge 節點代表您可以透過 SSH 連線並執行應用程式的節點，然後協調以在叢集資源上執行。 邊緣節點不會參與叢集內的資料分析。 此節點也會裝載 R Studio 伺服器，讓您可以使用瀏覽器執行 R 應用程式。 |
| 區域節點 | 對於 HBase 叢集類型，[區域] 節點（也稱為資料節點）會執列區域伺服器。 區域伺服器會提供並管理由 HBase 管理的部分資料。 您可以在叢集中新增或移除區域節點，以調整運算能力和管理成本。|
| Nimbus 節點 | 若為風暴叢集類型，Nimbus 節點會提供類似于前端節點的功能。 Nimbus 節點會透過 Zookeeper 將工作指派給叢集中的其他節點，這會協調執行中的風暴拓撲。 |
| 監督員節點 | 對於「風暴」叢集類型，監督員節點會執行 Nimbus 節點所提供的指示，以執行所需的處理。 |

## <a name="resource-naming-conventions"></a>資源命名慣例

當您為叢集中的節點定址時，請使用完整功能變數名稱（Fqdn）。 您可以使用[AMBARI API](hdinsight-hadoop-manage-ambari-rest-api.md)，取得叢集中各種節點類型的 fqdn。 

這些 Fqdn 的形式會是 `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`。

`<node-type-prefix>` 將針對前端節點、 *w)* 背景工作角色節點和*zn* （zookeeper 節點）進行*hn* 。

如果您只需要主機名稱，請只使用 FQDN 的第一個部分： `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>基本虛擬網路資源

下圖顯示 HDInsight 節點和網路資源在 Azure 中的位置。

![在 Azure 自訂 VNET 中建立的 HDInsight 實體圖表](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

將 HDInsight 部署到 Azure 虛擬網路時所顯示的預設資源，包括上表所述的叢集節點類型，以及支援虛擬網路與外部網路之間通訊的網路裝置。

下表摘要說明將 HDInsight 部署至自訂 Azure 虛擬網路時所建立的九個叢集節點。

| 資源類型 | 有數位 | 詳細資料 |
| --- | --- | --- |
|前端節點 | two |    |
|Zookeeper 節點 | three | |
|背景工作節點 | two | 此數目會根據叢集設定和調整而有所不同。 Apache Kafka 需要至少三個背景工作角色節點。  |
|閘道節點 | two | 閘道節點是在 Azure 上建立的 Azure 虛擬機器，但在您的訂用帳戶中看不到。 如果您需要重新開機這些節點，請聯絡支援人員。 |

下列網路資源會自動建立在與 HDInsight 搭配使用的虛擬網路內：

| 網路資源 | 有數位 | 詳細資料 |
| --- | --- | --- |
|負載平衡器 | three | |
|網路介面 | 份 | 這個值是以一般叢集為基礎，其中每個節點都有自己的網路介面。 這九個介面適用于這兩個前端節點、三個 zookeeper 節點、兩個背景工作節點，以及上表中所述的兩個閘道節點。 |
|公用 IP 位址 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>連接到 HDInsight 的端點

您可以透過三種方式存取您的 HDInsight 叢集：

- 位於虛擬網路外部的 HTTPS 端點，位於 `CLUSTERNAME.azurehdinsight.net`。
- 可在 `CLUSTERNAME-ssh.azurehdinsight.net`直接連接到前端節點的 SSH 端點。
- 虛擬網路內的 HTTPS 端點 `CLUSTERNAME-int.azurehdinsight.net`。 請注意此 URL 中的 "-int"。 此端點會解析為該虛擬網路中的私人 IP，而且無法從公用網際網路存取。

這三個端點會分別指派負載平衡器。

公用 IP 位址也會提供給允許從虛擬網路外部連線的兩個端點。

1. 從網際網路 `CLUSTERNAME.azurehdinsight.net`連線到叢集時，會將一個公用 IP 指派給負載平衡器，以供使用完整功能變數名稱（FQDN）。
1. 第二個公用 IP 位址僅用於 SSH 的功能變數名稱 `CLUSTERNAME-ssh.azurehdinsight.net`。

## <a name="next-steps"></a>後續步驟

- [以私人端點保護虛擬網路中 HDInsight 叢集的連入流量](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
