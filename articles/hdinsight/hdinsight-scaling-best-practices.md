---
title: 調整叢集大小 - Azure HDInsight
description: 彈性擴展 Apache Hadoop 群集，以匹配 Azure HDInsight 中的工作負荷
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 96a72541255ad0059abe5ad280f1728518dbf68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234741"
---
# <a name="scale-azure-hdinsight-clusters"></a>縮放 Azure HDInsight 群集

HDInsight 具有彈性，可讓您選擇相應增加和相應減少叢集中的背景工作節點數。 這種彈性允許您在下班後或週末收縮群集，並在高峰業務需求期間擴展群集。

如果您有定期批次處理，可以在此操作前幾分鐘向上擴展 HDInsight 群集，以便群集具有足夠的記憶體和 CPU 電源。 之後，當處理完成且使用量再次降低時，您可以將 HDInsight 叢集相應減少為較少的背景工作節點。

您可以使用下面概述的方法之一手動縮放群集，或使用[自動縮放](hdinsight-autoscale-clusters.md)選項使系統自動向上和向下擴展以回應 CPU、記憶體和其他指標。

> [!NOTE]  
> 只支援使用 HDInsight 3.1.3 版或更高版本的叢集。 如果不確定您的叢集版本，您可以檢查 [屬性] 頁面。

## <a name="utilities-to-scale-clusters"></a>用於擴展群集的實用程式

Microsoft 提供以下實用程式來擴展群集：

|公用程式 | 描述|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[設置-AzHDInsight 群集大小](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize)-\<群集名稱群集名稱> \<-目標實例計數新大小>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[設置-AzureRmHDInsight 群集大小](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize)-\<群集名稱群集名稱> \<-目標實例計數新大小>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight 調整大小](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize)—\<資源組資源組\<> -- 名稱群集名稱\<> -- 工作節點計數 NewSize>|
|[Azure 經典 CLI](hdinsight-administer-use-command-line.md)|azure hdinsight 群集\<調整群集名稱\<>目標實例計數> |
|[Azure 門戶](https://portal.azure.com)|打開 HDInsight 群集窗格，在左側功能表上選擇 **"群集大小**"，然後在"群集大小"窗格中選擇"群集大小"，鍵入輔助節點的數量，然後選擇"保存"。|  

![Azure 門戶縮放群集選項](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

只要使用上述任一方法，您即可在幾分鐘內相應增加或相應減少 HDInsight 叢集。

> [!IMPORTANT]  
> * Azure 經典 CLI 已棄用，應僅與經典部署模型一起使用。 對於所有其他部署，請使用 Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。  
> * 已棄用 PowerShell AzureRM 模組。  請盡可能使用[Az 模組](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)。

## <a name="impact-of-scaling-operations"></a>縮放操作的影響

當您向正在運行的 HDInsight 群集**添加**節點（向上擴展）時，任何掛起或正在運行的作業都不會影響。 在調整程序執行的同時，您仍可放心地提交新的作業。 如果縮放操作由於任何原因失敗，則將處理失敗，使群集處於功能狀態。

如果**刪除**節點（縮小），則縮放操作完成後，任何掛起或正在運行的作業都將失敗。 此故障是由於縮放過程中某些服務重新開機。 在手動縮放操作期間，群集還可能會陷入安全模式。

變更資料節點數目的影響會因為 HDInsight 支援的各種類型叢集而有所不同：

* Apache Hadoop

    您可以順暢地增加正在執行的 Hadoop 叢集中背景工作節點數目，而不會影響任何擱置或執行中的工作。 您也可以在作業進行當中提交新工作。 系統會順暢處理失敗的調整作業，讓叢集永保正常運作狀態。

    減少資料節點數目以縮減 Hadoop 叢集時，系統會重新啟動叢集中的部分服務。 此行為會導致所有執行中和擱置的工作在調整作業完成時失敗。 但您可以在作業完成後重新提交這些工作。

* Apache HBase (英文)

    您可以順暢地在 HBase 叢集運作時對其新增或移除資料節點。 區域伺服器會在完成調整作業的數分鐘之內自動取得平衡。 但是，您也可以手動平衡區域伺服器，方法是登入叢集的前端節點，然後從命令提示字元視窗執行下列命令：

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    如需使用 HBase 殼層的詳細資訊，請參閱[開始使用 HDInsight 中的 Apache HBase 範例](hbase/apache-hbase-tutorial-get-started-linux.md)。

* Apache Storm

    您可以順暢地在 Storm 叢集運作時對其新增或移除資料節點。 不過，在調整作業順利完成後，您需要重新平衡拓撲。

    您可以使用兩種方式來完成重新平衡作業：

  * Storm Web UI
  * 命令列介面 (CLI) 工具

    如需詳細資訊，請參閱 [Apache Storm 文件](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) 。

    HDInsight 叢集上有提供 Storm Web UI：

    ![HDInsight Storm 調整重新平衡](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    以下是用來重新平衡 Storm 拓撲的範例 CLI 命令：

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>如何安全地縮小群集

### <a name="scale-down-a-cluster-with-running-jobs"></a>縮小具有正在運行的作業的群集

為了避免在縮減操作期間運行作業失敗，可以嘗試三件事：

1. 在縮小群集之前，等待作業完成。
1. 手動結束作業。
1. 縮放操作完成後重新提交作業。

要查看掛起和正在運行的作業的清單，可以使用 YARN**資源管理器 UI，** 按照以下步驟操作：

1. 從[Azure 門戶](https://portal.azure.com/)中選擇群集。  請參閱[列出和顯示叢集](./hdinsight-administer-use-portal-linux.md#showClusters)以取得指示。 群集在新門戶頁中打開。
2. 從主視圖導航到**群集儀表板** > **Ambari 主頁**。 輸入群集憑據。
3. 從 Ambari UI 中，在左側功能表上的服務清單中選擇**YARN。**  
4. 從 YARN 頁面中，選擇 **"快速連結"** 並將滑鼠懸停在活動頭節點上，然後選擇**資源管理器 UI**。

    ![阿帕奇·安巴里快速連結資源管理器 UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

您可以使用 `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` 直接存取 ResourceManager UI。

您會看到作業及其目前狀態的清單。 在螢幕截圖中，當前正在運行一個作業：

![ResourceManager UI 應用程式](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

若要手動終止該執行中的應用程式，請從 SSH 殼層執行下列命令：

```bash
yarn application -kill <application_id>
```

例如：

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>卡在安全模式下

縮小群集時，HDInsight 使用 Apache Ambari 管理介面首先停用額外的輔助節點，這些節點將其 HDFS 塊複製到其他連線輔助節點。 之後，HDInsight 安全地將群集縮小。 HDFS 在縮放操作期間進入安全模式，一旦縮放完成，應該會出來。 但是，在某些情況下，HDFS 在縮放操作期間由於檔塊複製不足而陷入安全模式。

預設情況下，HDFS 配置的`dfs.replication`設置為 1，用於控制每個檔塊可用的副本數。 檔塊的每個副本都存儲在群集的不同節點上。

當 HDFS 檢測到預期的塊副本數無法接通，HDFS 進入安全模式，Ambari 會生成警報。 如果 HDFS 進入縮放操作的安全模式，但隨後無法退出安全模式，因為無法檢測到複製所需的節點數，群集可能會卡在安全模式下。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>安全模式開啟時的錯誤範例

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

您可以從 `/var/log/hadoop/hdfs/` 資料夾檢閱接近叢集調整時的名稱節點記錄，以查看其進入安全模式的時間。 記錄檔名稱為 `Hadoop-hdfs-namenode-<active-headnode-name>.*`。

先前錯誤的根本原因是 Hive 在執行查詢時會仰賴 HDFS 中的暫存檔案。 當 HDFS 進入安全模式時，Hive 因為無法寫入到 HDFS 而無法執行查詢。 HDFS 中的暫存檔案位於掛接到個別背景工作節點 VM 的本機磁碟機，並且會在其他背景工作節點之間複寫到至少三個複本。

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>如何防止 HDInsight 陷入安全模式

有數種方式可避免讓 HDInsight 無法脫離安全模式：

* 先停止所有 Hive 作業，再相應減少 HDInsight。 或者，對相應減少程序進行排程，以免與執行中的 Hive 作業發生衝突。
* 先手動清除 Hive 在 HDFS 中的暫存 `tmp` 目錄檔案，再相應減少。
* 只將 HDInsight 相應減少為三個背景工作節點 (最小值)。 避免減少到只剩一個背景工作節點。
* 如有需要，則執行命令來脫離安全模式。

下列幾節會說明這些選項。

#### <a name="stop-all-hive-jobs"></a>停止所有 Hive 作業

先停止所有 Hive 作業，再相應減少為一個背景工作節點。 如果您已將工作負載排程，則請在 Hive 工作完成後執行相應減少。

在縮放之前停止 Hive 作業有助於最小化 tmp 資料夾中的暫存檔數（如果有）。

#### <a name="manually-clean-up-hives-scratch-files"></a>手動清除 Hive 的暫存檔案

如果 Hive 有遺留暫存檔案，則可以先手動清除那些檔案再相應減少，以避開安全模式。

1. 通過查看配置屬性，`hive.exec.scratchdir`檢查 Hive 暫存檔案正在使用的位置。 此參數設置在`/etc/hive/conf/hive-site.xml`：

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. 停止 Hive 服務，並確定所有查詢和作業皆已完成。
2. 列出上面找到的臨時目錄的內容，`hdfs://mycluster/tmp/hive/`以查看它是否包含任何檔：

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    以下是有檔案存在時的輸出範例：

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. 如果您知道 Hive 不會再使用這些檔案，則可將其移除。 請查看 Yarn ResourceManager UI 頁面，確定 Hive 沒有任何執行中的查詢。

    從 HDFS 中移除檔案的命令列範例：

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>將 HDInsight 縮放為三個或更多輔助節點

如果群集在向下擴展到少於三個輔助節點時經常陷入安全模式，並且前面的步驟不起作用，則可以通過保留至少三個輔助節點來避免群集完全進入安全模式。

保留三個輔助節點比僅縮減到一個輔助節點的成本更高，但它會防止群集陷入安全模式。

### <a name="scale-hdinsight-down-to-one-worker-node"></a>將 HDInsight 縮放到一個輔助節點

即使群集縮減為 1 個節點，輔助節點 0 仍將存活。 輔助節點 0 永遠不會停用。

#### <a name="run-the-command-to-leave-safe-mode"></a>執行命令來脫離安全模式

最後一個選項是執行離開安全模式命令。 如果您知道 HDFS 進入安全模式的原因是 Hive 檔案複製不足，則可以執行以下命令以離開安全模式：

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>縮小 Apache HBase 群集

區域伺服器在完成縮放操作後幾分鐘內自動平衡。 要手動平衡區域伺服器，完成以下步驟：

1. 使用 SSH 連線到 HDInsight 叢集。 有關詳細資訊，請參閱將[SSH 與 HDInsight 一起使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 啟動 HBase 殼層：

    ```bash
    hbase shell
    ```

3. 使用下列命令手動平衡區域伺服器：

    ```bash
    balancer
    ```

## <a name="next-steps"></a>後續步驟

* [自動調整 Azure HDInsight 叢集規模](hdinsight-autoscale-clusters.md)
* [Azure HDInsight 簡介](hadoop/apache-hadoop-introduction.md)
