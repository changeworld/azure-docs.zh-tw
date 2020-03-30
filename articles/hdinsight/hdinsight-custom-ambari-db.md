---
title: Azure HDInsight 上的自訂 Apache Ambari 資料庫
description: 瞭解如何使用您自己的自訂 Apache Ambari 資料庫創建 HDInsight 群集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240165"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>使用自訂 Ambari DB 設置 HDInsight 群集

阿帕奇·安巴里簡化了阿帕奇哈多普集群的管理和監視。 Ambari 提供了一個便於使用的 Web UI 和 REST API。 Ambari 包含在 HDInsight 群集中，用於監視群集和進行配置更改。

在正常群集創建中，如在[HDInsight 中設置群集](hdinsight-hadoop-provision-linux-clusters.md)等其他文章中所述，Ambari 部署在由 HDInsight 管理的[S0 Azure SQL 資料庫中](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier)，使用者無法訪問。

自訂 Ambari 資料庫功能允許您部署新群集，並在您管理的外部資料庫中設置 Ambari。 部署使用 Azure 資源管理器範本完成。 此功能具有以下優點：

- 自訂 - 選擇資料庫的大小和處理容量。 如果具有處理密集型工作負載的大型群集，則規格較低的 Ambari 資料庫可能成為管理操作的瓶頸。
- 靈活性 - 您可以根據需要擴展資料庫，以滿足您的要求。
- 控制 - 您可以以符合組織要求的方式管理資料庫的備份和安全。

本文的其餘部分將討論以下幾點：

- 使用自訂 Ambari DB 功能的要求
- 使用您自己的 Apache Ambari 外部資料庫預配 HDInsight 群集所需的步驟

## <a name="custom-ambari-db-requirements"></a>自訂 Ambari 資料庫要求

您可以部署具有所有群集類型和版本的自訂 Ambari DB。 多個群集不能使用相同的 Ambari DB。

自訂 Ambari DB 具有以下其他要求：

- 您必須具有現有的 Azure SQL 資料庫伺服器和資料庫。
- 為 Ambari 設置提供的資料庫必須為空。 預設 dbo 架構中不應有表。
- 用於連接到資料庫的使用者應具有資料庫的"選擇"、"創建表"和"插入"許可權。
- 打開"[允許訪問](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps)Azure SQL 伺服器上的 Azure 服務"選項，您將在 Azure SQL 伺服器上承載 Ambari。
- 需要允許 SQL 伺服器中允許來自 HDInsight 服務的管理 IP 位址。 有關必須添加到 SQL 伺服器防火牆的 IP 位址的清單，請參閱[HDInsight 管理 IP 位址](hdinsight-management-ip-addresses.md)。

在外部資料庫中託管 Apache Ambari DB 時，請記住以下幾點：

- 您負責容納 Ambari 的 Azure SQL DB 的額外費用。
- 定期備份自訂 Ambari DB。 Azure SQL 資料庫會自動生成備份，但備份保留時間範圍會有所不同。 如需詳細資訊，請參閱[了解自動 SQL Database 備份](../sql-database/sql-database-automated-backups.md)。

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>使用自訂 Ambari DB 部署群集

要創建使用您自己的外部 Ambari 資料庫的 HDInsight 群集，請使用[自訂 Ambari DB 快速啟動範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)。

編輯 中的參數`azuredeploy.parameters.json`以指定有關新群集和將保存 Ambari 的資料庫的資訊。

可以使用 Azure CLI 開始部署。 替換為`<RESOURCEGROUPNAME>`要部署群集的資源組。

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>後續步驟

- [在 Azure HDInsight 中使用外部中繼資料存放區](hdinsight-use-external-metadata-stores.md)