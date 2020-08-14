---
title: Azure HDInsight 上調整規模的 HiveServer2
description: 使用邊緣節點在 Azure HDInsight 叢集上水準調整 HiveServer2，以提高容錯和可用性。
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227083"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>在 Azure HDInsight 叢集上調整 HiveServer2 以取得高可用性

瞭解如何將其他 HiveServer2 部署到您的叢集中，以提高可用性和負載分佈。 當您不想要增加前端節點大小時，您也可以使用邊緣節點來部署 HiveServer2。 

> [!NOTE]
> 根據您的使用量而定，增加 HiveServer2 數目可能會增加 Hive 中繼存放區的連線數目。 此外，請確定您的 Azure SQL 資料庫大小正確。

## <a name="prerequisites"></a>先決條件

若要使用本指南，您必須瞭解下列文章：
- [在 HDInsight 中的 Apache Hadoop 叢集上使用空白邊緣節點](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>安裝 HiveServer2

在本節中，您會在目標主機上安裝額外的 HiveServer2。

1. 在瀏覽器中開啟 Ambari，然後按一下目標主機。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Ambari 的 [主機] 功能表。":::

2. 按一下 [新增] 按鈕，然後按一下 [HiveServer2]

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="新增主機的 HiveServer2 面板。":::

3. 確認，此程式將會執行。 針對所有想要的主機重複1-3。

4. 當您完成安裝時，請重新開機具有過時的的所有服務，並啟動 HiveServer2。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="開始 HiveServer2 面板。":::

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何將 HiveServer2 安裝到您的叢集。 若要深入瞭解邊緣節點和應用程式，請參閱下列文章：

* [安裝邊緣節點](hdinsight-apps-use-edge-node.md)：瞭解如何在 HDInsight 叢集上安裝邊緣節點。
* [安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md)：了解如何將 HDInsight 應用程式安裝到您的叢集。
* [AZURE SQL DTU 連線限制](../azure-sql/database/resource-limits-dtu-single-databases.md)：瞭解使用 DTU 的 azure sql database 限制。
* [AZURE Sql vCore 連線限制](../azure-sql/database/resource-limits-vcore-elastic-pools.md)：瞭解使用虛擬核心的 azure sql database 限制。
