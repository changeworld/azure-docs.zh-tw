---
title: Azure HDInsight 上的自訂 Apache Ambari 資料庫
description: 瞭解如何使用您自己的自訂 Apache Ambari 資料庫來建立 HDInsight 叢集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/24/2019
ms.author: hrasheed
ms.openlocfilehash: 1858e06567a0ab0907e6d2cb60358ff4ac00f9a2
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086342"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>使用自訂 Ambari DB 設定 HDInsight 叢集

Apache Ambari 可簡化 Apache Hadoop 叢集的管理和監視。 Ambari 提供便於使用的 web UI 和 REST API。 Ambari 包含在 HDInsight 叢集上，可用來監視叢集並進行設定變更。

在一般叢集建立中（如在[hdinsight 中設定](hdinsight-hadoop-provision-linux-clusters.md)叢集之類的其他文章中所述），Ambari 會部署在由 hdinsight 管理且無法供使用者存取的[S0 Azure SQL Database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) 。

自訂 Ambari DB 功能可讓您在管理的外部資料庫中部署新的叢集，並設定 Ambari。 部署是使用 Azure Resource Manager 範本來完成。 這項功能有下列優點：

- 自訂-您可以選擇資料庫的大小和處理能力。 如果您有大型叢集處理密集型工作負載，較低規格的 Ambari 資料庫可能會成為管理作業的瓶頸。
- 彈性-您可以視需要調整資料庫，以符合您的需求。
- 控制-您可以使用符合組織需求的方式來管理資料庫的備份和安全性。

本文的其餘部分將討論下列幾點：

- 使用自訂 Ambari DB 功能的需求
- 使用您自己的 Apache Ambari 外部資料庫布建 HDInsight 叢集所需的步驟

## <a name="custom-ambari-db-requirements"></a>自訂 Ambari DB 需求

您可以部署具有所有叢集類型和版本的自訂 Ambari DB。 多個叢集無法使用相同的 Ambari DB。

自訂 Ambari DB 具有下列其他需求：

- 資料庫的名稱不能包含連字號或空格
- 您必須擁有現有的 Azure SQL DB 伺服器和資料庫。
- 您為 Ambari 安裝程式提供的資料庫必須是空的。 預設的 dbo 架構中應該沒有任何資料表。
- 用來連接到資料庫的使用者應該具有資料庫的 SELECT、CREATE TABLE 和 INSERT 許可權。
- 在您將裝載 Ambari 的伺服器上，開啟選項以[允許存取 Azure 服務](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps)。
- 必須在防火牆規則中允許來自 HDInsight 服務的管理 IP 位址。 如需必須新增至伺服器層級防火牆規則的 IP 位址清單，請參閱[HDInsight 管理 ip 位址](hdinsight-management-ip-addresses.md)。

當您在外部資料庫中裝載 Apache Ambari DB 時，請記住下列幾點：

- 您必須負責保留 Ambari 的 Azure SQL DB 額外成本。
- 定期備份您的自訂 Ambari DB。 Azure SQL Database 會自動產生備份，但備份保留時間框架會有所不同。 如需詳細資訊，請參閱[了解自動 SQL Database 備份](../azure-sql/database/automated-backups-overview.md)。

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>使用自訂 Ambari DB 部署叢集

若要建立使用您自己的外部 Ambari 資料庫的 HDInsight 叢集，請使用[自訂 AMBARI DB 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)。

編輯中的參數 `azuredeploy.parameters.json` ，以指定您的新叢集和將保留 Ambari 之資料庫的相關資訊。

您可以使用 Azure CLI 開始部署。 將取代為 `<RESOURCEGROUPNAME>` 您要在其中部署叢集的資源群組。

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>後續步驟

- [在 Azure HDInsight 中使用外部中繼資料存放區](hdinsight-use-external-metadata-stores.md)
