---
title: 將叢集遷移至較新的版本
titleSuffix: Azure HDInsight
description: 瞭解將 Azure HDInsight 叢集遷移至較新版本的指導方針。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: f3bfd430023330d3a399a0a760fd353b6ee60941
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085883"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>將 HDInsight 叢集遷移至更新的版本

若要利用最新的 HDInsight 功能，建議將 HDInsight 叢集定期遷移至最新版本。 HDInsight 不支援就地升級，其中現有的叢集會升級至較新的元件版本。 您必須使用所需的元件和平臺版本來建立新的叢集，然後將應用程式遷移至使用新的叢集。 遵循下列指導方針來遷移您的 HDInsight 叢集版本。

> [!NOTE]  
> 如需受支援 HDInsight 版本的詳細資訊，請參閱 [HDInsight 元件版本](hdinsight-component-versioning.md#supported-hdinsight-versions)。

## <a name="migration-tasks"></a>移轉工作

升級 HDInsight 叢集的工作流程如下。
![HDInsight 升級工作流程圖表](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. 閱讀此文件的每一節，以了解在升級 HDInsight 叢集時，可能需要進行的變更。
2. 將叢集建立為測試/品質保證環境。 如需建立叢集的詳細資訊，請參閱[了解如何建立 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
3. 將現有的作業、資料來源與接收複製到新的環境。
4. 執行驗證測試以確保您的工作在新叢集上會如預期般運作。

當您已驗證一切都會如預期般運作之後，請為移轉排定停機時間。 在此停機期間，請執行下列動作：

1. 備份所有儲存在本機叢集節點上的暫時性資料。 例如，如果您的資料是直接儲存在前端節點上。
1. [刪除現有的](./hdinsight-delete-cluster.md)叢集。
1. 使用與先前叢集所使用之預設資料存放區相同的資料存放區，在具有最新 (或受支援) 之 HDI 版本的 VNET 子網路中建立叢集。 這將能允許新叢集針對現有的生產資料繼續運作。
1. 匯入任何已備份的暫時性資料。
1. 使用新叢集啟動工作/繼續處理。

## <a name="workload-specific-guidance"></a>工作負載特定指引

下列檔提供如何遷移特定工作負載的指引：

* [遷移 HBase](./hbase/apache-hbase-migrate-new-version.md)
* [遷移 Kafka](./kafka/migrate-versions.md)
* [遷移 Hive/Interactive Query](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>備份與還原

如需有關資料庫備份和還原的詳細資訊，請參閱 [使用自動資料庫備份復原 Azure SQL Database 中的資料庫](../azure-sql/database/recovery-using-backups.md)。

## <a name="next-steps"></a>接下來的步驟

* [了解如何建立 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
* [使用 SSH 連線到 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [使用 Apache Ambari 管理 Linux 型叢集](hdinsight-hadoop-manage-ambari.md)
* [HDInsight 版本資訊](./hdinsight-version-release.md)
