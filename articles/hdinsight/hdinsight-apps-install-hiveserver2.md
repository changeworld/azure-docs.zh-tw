---
title: Azure HDInsight 上調整 HiveServer2
description: 使用邊緣節點以水準方式調整 Azure HDInsight 叢集上的 HiveServer2，以提高容錯和可用性。
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227083"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>在 Azure HDInsight 叢集上調整 HiveServer2 以獲得高可用性

瞭解如何在您的叢集中部署額外的 HiveServer2，以提高可用性和負載分配。 當您不想要增加前端節點大小時，您也可以使用邊緣節點來部署 HiveServer2。 

> [!NOTE]
> 根據您的使用量，增加 HiveServer2 數目可能會增加與 Hive 中繼存放區的連接數目。 也請確定您的 Azure SQL 資料庫已正確調整大小。

## <a name="prerequisites"></a>必要條件

若要使用本指南，您必須瞭解下列文章：
- [在 HDInsight 中的 Apache Hadoop 叢集上使用空白邊緣節點](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>安裝 HiveServer2

在本節中，您會在目標主機上安裝額外的 HiveServer2。

1. 在您的瀏覽器中開啟 Ambari，然後按一下您的目標主機。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Ambari 的 [主機] 功能表。":::

2. 按一下 [新增] 按鈕，然後按一下 [HiveServer2]

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="Ambari 的 [主機] 功能表。":::

3. 確認，程式將會執行。 針對所有所需的主機重複1-3。

4. 當您完成安裝時，請以過時的進行重新開機所有服務，並開始 HiveServer2。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="Ambari 的 [主機] 功能表。":::

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何將 HiveServer2 安裝到叢集上。 若要深入瞭解邊緣節點和應用程式，請參閱下列文章：

* [安裝邊緣節點](hdinsight-apps-use-edge-node.md)：瞭解如何在您的 HDInsight 叢集上安裝邊緣節點。
* [安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md)：了解如何將 HDInsight 應用程式安裝到您的叢集。
* [AZURE SQL DTU 連接限制](../azure-sql/database/resource-limits-dtu-single-databases.md)：瞭解使用 DTU 的 azure sql database 限制。
* [AZURE Sql vCore 連線限制](../azure-sql/database/resource-limits-vcore-elastic-pools.md)：使用虛擬核心瞭解 azure sql database 的限制。
