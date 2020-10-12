---
title: 資料移轉：內部部署 Apache Hadoop 至 Azure HDInsight
description: 了解將內部部署 Hadoop 叢集移轉到 Azure HDInsight 的資料移轉最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 9794dd47949dc7dea891893dbcf261808ab335fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86521372"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>將內部部署 Apache Hadoop 叢集移轉到 Azure HDInsight - 資料移轉最佳做法

本文提供將資料移轉至 Azure HDInsight 的建議。 本文是系列文章中的一篇，提供有助於將內部部署 Apache Hadoop 系統移轉至 Azure HDInsight 的最佳做法。

## <a name="migrate-on-premises-data-to-azure"></a>將內部部署的資料移轉到 Azure

若要將資料從內部部署移轉到 Azure 環境，有兩個主要選項：

* 使用 TLS 透過網路傳輸資料
    * 透過網際網路 - 您可以使用其中一項工具 (例如 Azure 儲存體總管、AzCopy、Azure Powershell 和 Azure CLI) 來透過一般的網際網路連線，將資料傳輸到 Azure 儲存體。 如需詳細資訊，請參閱 [將資料移入和移出 Azure 儲存體](../../storage/common/storage-moving-data.md)。

    * ExpressRoute - Express Route 是一項 Azure 服務，可讓您在 Microsoft 資料中心和內部部署或共置設施中的基礎結構之間建立私人連線。 ExpressRoute 連線不會經過公用網際網路，並提供比網際網路一般連線更高的安全性、可靠性和速度。 如需詳細資訊，請參閱[建立和修改 ExpressRoute 線路](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。

    * Data Box 線上資料傳輸 - Data Box Edge 與 Data Box Gateway 是線上資料轉送產品，等同於在您的網站與 Azure 之間管理資料的網路儲存體閘道。 Data Box Edge 是內部部署網路裝置，與 Azure 來回轉送資料，並使用具備人工智慧 (AI) 的邊緣計算來處理資料。 Data Box Gateway 是具備儲存體閘道功能的虛擬設備。 如需詳細資訊，請參閱 [Azure 資料箱文件 - 線上傳輸](https://docs.microsoft.com/azure/databox-online/)。

* 離線寄送資料

    資料箱離線資料傳輸 - 資料箱、資料箱磁碟和 Data Box Heavy 裝置可協助您在網路無法使用時，將大量資料傳輸至 Azure。 這些離線資料轉送裝置會在您的組織與 Azure 資料中心之間運送。 裝置使用 AES 加密來協助保護傳輸中的資料，而且會在上傳後經過徹底的清理過程，以從裝置中刪除您的資料。 如需資料箱離線傳送裝置的詳細資訊，請參閱 [Azure 資料箱檔-離線傳送](https://docs.microsoft.com/azure/databox/)。 如需有關遷移 Hadoop 叢集的詳細資訊，請參閱 [使用 Azure 資料箱從內部部署 HDFS 存放區遷移至 Azure 儲存體](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)。

下表根據資料磁碟區和網路頻寬提供粗略的資料傳輸持續時間。 若資料移轉預計要花費超過三週的時間，請使用資料箱。

|資料數量 | 網路頻寬<br>of<br>**45 Mbps (T3)**|網路頻寬<br>of<br>**100 Mbps**|網路頻寬<br>of<br>**1 Gbps**|網路頻寬<br>of<br>**10 Gbps**|
|---|:---:|:---:|:---:|:---:|
|1 TB|2 天|1 日| 2 小時|14 分鐘|
|10 TB|22 天|10 天|1 日|2 小時|
|35 TB|76 天|34 天|3 天|8 小時|
|80 TB|173 天|78 天|8 天|19 小時|
|100 TB|216 天|97 天|10 天|1 日|
|200 TB|1 年|194 天|19 天|2 天|
|500 TB|3 年|1 年|49 天|5 天|
|1 PB|6 年|3 年|97 天|10 天|
|2 PB|12 年|5 年|194 天|19 天|

Azure 的原生工具 (例如 Apache Hadoop DistCp、Azure Data Factory 和 AzureCp) 可用於透過網路傳輸資料。 第三方工具 WANDisco 也可用於相同用途。 Apache Kafka Mirrormaker 和 Apache Sqoop 可用於持續從內部部署將資料轉送至 Azure 儲存體系統。

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>使用 Apache Hadoop DistCp 時的效能考量

DistCp 是使用 MapReduce 對應作業的 Apache 專案，可以傳輸資料、處理錯誤，並從那些錯誤中復原。 它會將來源檔案清單指派給每個對應工作。 然後，對應工作會將其所有被指派的檔案複製到目的地。 有幾項技術可以改善 DistCp 的效能。

### <a name="increase-the-number-of-mappers"></a>增加對應程式數目

DistCp 會嘗試建立對應工作，讓每個對應工作大致上可複製相同的位元組數。 根據預設，DistCp 作業會使用 20 個對應程式。 使用更多的 Distcp 對應程式 (在命令列中搭配 'm' 參數) 會增加資料傳輸程序期間的平行處理原則，並減少資料傳輸的長度。 不過，在增加對應程式數目時，有兩件事要列入考量：

* DistCp 的最低資料粒度是單一檔案。 指定的對應程式數目數目超過原始程式檔的數目並不會有説明，而且將會浪費可用的叢集資源。

* 請考慮叢集上可用的 YARN 記憶體，來決定對應程式的數目。 每個對應工作會啟動為 YARN 容器。 假設叢集上沒有執行其他繁重的工作負載，對應程式的數目可以下列公式來決定：m = (背景工作角色節點數目 \* 每個背景工作角色的 YARN 記憶體) / YARN 容器大小。 不過，如果有其他應用程式在使用記憶體，則選擇讓 DistCp 作業只使用一部分 YARN 記憶體。

### <a name="use-more-than-one-distcp-job"></a>使用多個 DistCp 作業

當要移動的資料集大小大於 1 TB 時，可使用多個 DistCp 作業。 使用多個作業可降低失敗的影響。 如果有任何作業失敗，您只需要重新啟動該特定作業，而不是所有作業。

### <a name="consider-splitting-files"></a>考慮分割檔案

如果有少數大型檔案，可以考慮將它們分割為 256-MB 的檔案區塊，以取得使用更多對應程式並行處理的可能性。

### <a name="use-the-strategy-command-line-parameter"></a>使用 'strategy' 命令列參數

考慮在命令列中使用 `strategy = dynamic` 參數。 `strategy` 參數的預設值是 `uniform size`，在這種情況下，每個對應會複製大致上相同的位元組數目。 當此參數變更為 `dynamic` 時，清單檔會分割為數個「區塊檔案」。 區塊檔案的數目是對應數目的倍數。 每個對應工作會受指派處理其中一個區塊檔案。 處理區塊中的所有路徑之後，會刪除目前區塊並取得新的區塊。 此流程會持續到沒有可用的區塊為止。 此「動態」方法可讓較快的對應工作比起較慢的工作取用更多路徑，因此能提升 DistCp 作業的整體速度。

### <a name="increase-the-number-of-threads"></a>增加執行緒數目

查看增加 `-numListstatusThreads` 參數是否能改善效能。 此參數可控制用於建置檔案清單的執行緒數目，最大值為 40。

### <a name="use-the-output-committer-algorithm"></a>使用輸出認可者演算法

查看傳遞 `-Dmapreduce.fileoutputcommitter.algorithm.version=2` 參數是否能改善 DistCp 效能。 此輸出認可者演算法針對寫入輸出檔到目標已最佳化。 下列命令是顯示不同參數使用方式的範例：

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>中繼資料移轉

### <a name="apache-hive"></a>Apache Hive

您可以使用指令碼或 DB 複寫來移轉 Hive 中繼存放區。

#### <a name="hive-metastore-migration-using-scripts"></a>使用指令碼移轉 Hive 中繼存放區

1. 從內部部署 Hive 中繼存放區產生 Hive Ddl。 此步驟可使用[包裝函式 Bash 指令碼](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md) \(英文\) 來完成。
1. 編輯產生的 DDL，使用 WASB/ADLS/ABFS URL 來取代 HDFS URL。
1. 從 HDInsight 叢集的中繼存放區上執行更新的 DDL。
1. 確定 Hive 中繼存放區版本在內部部署和雲端之間都相容。

#### <a name="hive-metastore-migration-using-db-replication"></a>使用 DB 複寫移轉 Hive 中繼存放區

- 在內部部署 Hive 中繼存放區 DB 和 HDInsight 中繼存放區 DB 之間設定資料庫複寫。
- 使用 "Hive MetaTool" 將 HDFS URL 取代為 WASB/ADLS/ABFS URL，例如：

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- 將內部部署 Ranger 原則匯出至 XML 檔案。
- 使用 XSLT 之類的工具，將內部部署特定的 HDFS 路徑轉換成 WASB/ADLS。
- 將原則匯入在 HDInsight 上執行的 Ranger。

## <a name="next-steps"></a>接下來的步驟

閱讀此系列中的下一篇文章：

- [從內部部署移轉到 Azure HDInsight Hadoop 的安全性和 DevOps 最佳做法](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
