---
title: 調整叢集大小 - Azure HDInsight
description: 彈性延伸 Apache Hadoop 群集,以符合 Azure HDInsight 中的工作負荷
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 0fc067f0be4ac5d2b8fa7db9ad7999efe06625a0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804498"
---
# <a name="scale-azure-hdinsight-clusters"></a>縮放 Azure HDInsight 叢集

HDInsight 提供了彈性,提供了擴展和縮小群集中輔助節點數量的選項。 這種彈性允許您在下班后或周末收縮群集。 並在高峰業務需求期間擴展它。

在定期批處理之前向上擴展群集,以便群集具有足夠的資源。 處理完成後,使用量下降,將 HDInsight 群集縮減為更少的輔助節點。

您可以使用下面概述的方法之一手動縮放群集。 您還可以使用[自動縮放](hdinsight-autoscale-clusters.md)選項來自動向上和向下擴展以回應某些指標。

> [!NOTE]  
> 只支援使用 HDInsight 3.1.3 版或更高版本的叢集。 如果不確定您的叢集版本，您可以檢查 [屬性] 頁面。

## <a name="utilities-to-scale-clusters"></a>擴充叢集的實用程式

Microsoft 提供以下實用程式來擴展群集:

|公用程式 | 描述|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[`Set-AzHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) | [`az hdinsight resize`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure 經典 CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure 入口網站](https://portal.azure.com)|打開 HDInsight 群集窗格,在左側功能表上選擇 **「群集大小**」,然後在「群集大小」窗格中選擇"群集大小」,鍵入輔助節點的數量,然後選擇"保存"。|  

![Azure 門戶縮放叢集選項](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

只要使用上述任一方法，您即可在幾分鐘內相應增加或相應減少 HDInsight 叢集。

> [!IMPORTANT]  
> * Azure 經典 CLI 已棄用,應僅與經典部署模型一起使用。 對所有其他部署,請使用 Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。
> * 已棄用 PowerShell AzureRM 模組。  請盡可能使用[Az 模組](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)。

## <a name="impact-of-scaling-operations"></a>調整操作

當您**將節點添加到**正在運行的 HDInsight 群集(向上擴展)時,作業不會受到影響。 在調整程序執行的同時，您仍可放心地提交新的作業。 如果縮放操作失敗,故障將使群集處於功能狀態。

如果**刪除**節點(縮小),則掛起或正在運行的作業將在縮放操作完成後失敗。 此失敗是由於縮放過程中某些服務重新啟動。 在手動縮放操作期間,群集可能會卡在安全模式。

變更資料節點數目的影響會因為 HDInsight 支援的各種類型叢集而有所不同：

* Apache Hadoop

    您可以無縫地增加正在運行的 Hadoop 群集中的輔助節點數,而不會影響任何作業。 您也可以在作業進行當中提交新工作。 縮放操作中的故障得到正常處理。 群集始終處於功能狀態。

    當 Hadoop 群集以較少的資料節點進行縮減時,某些服務將重新啟動。 此行為會導致所有執行中和擱置的工作在調整作業完成時失敗。 但您可以在作業完成後重新提交這些工作。

* Apache HBase (英文)

    您可以在 HBase 叢集執行時無縫添加或刪除節點。 區域伺服器會在完成調整作業的數分鐘之內自動取得平衡。 但是,您可以手動平衡區域伺服器。 登入群組節點並執行以下指令:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    如需使用 HBase 殼層的詳細資訊，請參閱[開始使用 HDInsight 中的 Apache HBase 範例](hbase/apache-hbase-tutorial-get-started-linux.md)。

* Apache Storm

    您可以在 Storm 執行時無縫添加或刪除資料節點。 但是,在成功完成縮放操作后,您需要重新平衡拓撲。

    您可以使用兩種方式來完成重新平衡作業：

  * Storm Web UI
  * 命令列介面 (CLI) 工具

    有關詳細資訊,請參閱[阿帕奇風暴文檔](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

    HDInsight 叢集上有提供 Storm Web UI：

    ![HDInsight Storm 調整重新平衡](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    以下是用來重新平衡 Storm 拓撲的範例 CLI 命令：

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>如何安全地縮小叢集

### <a name="scale-down-a-cluster-with-running-jobs"></a>縮小具有正在執行的工作的叢集

為了避免在縮減操作期間運行作業失敗,可以嘗試三件事:

1. 在縮小群集之前,等待作業完成。
1. 手動結束作業。
1. 縮放操作完成後重新提交作業。

要檢視掛起與正在執行的工作的清單,可以使用 YARN**資源管理員 UI,** 按照以下步驟操作:

1. 從[Azure 門戶](https://portal.azure.com/)中選擇群集。  請參閱[列出和顯示叢集](./hdinsight-administer-use-portal-linux.md#showClusters)以取得指示。 群集在新門戶頁中打開。
2. 從主檢視導覽到**叢集儀表板** > **Ambari 首頁**。 輸入群集認證。
3. 從 Ambari UI 中,在左側功能表上的服務清單中選擇**YARN。**  
4. 從 YARN 頁面中,選擇 **「快速連結」** 並將滑鼠懸停在作用節點上,然後選擇**資源管理員 UI**。

    ![阿帕奇·安巴里快速連結資源管理員 UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

您可以直接使用`https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`存取資源管理員 UI。

您會看到作業及其目前狀態的清單。 在螢幕截圖中,目前正在運行一個作業:

![資源管理員 UI 應用程式](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

若要手動終止該執行中的應用程式，請從 SSH 殼層執行下列命令：

```bash
yarn application -kill <application_id>
```

例如：

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>卡在安全模式下

縮小群集時,HDInsight 使用 Apache Ambari 管理介面首先停用額外的輔助節點。 節點將其 HDFS 塊複製到其他連線輔助節點。 之後,HDInsight 安全地將群集縮小。 HDFS 在縮放操作期間進入安全模式。 HDFS 應該在縮放完成後出來。 但是,在某些情況下,HDFS 在縮放操作期間由於檔塊複製不足而陷入安全模式。

預設情況下,HDFS 配置`dfs.replication`的 設置為 1,用於控制每個檔塊可用的複本數。 檔案塊的每個複本都存儲在群集的不同節點上。

當預期的塊副本數不可用時,HDFS 進入安全模式,Ambari 生成警報。 HDFS 可能進入縮放操作的安全模式。 如果未檢測到複製所需的節點數,群集可能會卡在安全模式下。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>安全模式開啟時的錯誤範例

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

您可以從 `/var/log/hadoop/hdfs/` 資料夾檢閱接近叢集調整時的名稱節點記錄，以查看其進入安全模式的時間。 記錄檔名稱為 `Hadoop-hdfs-namenode-<active-headnode-name>.*`。

根本原因是 Hive 在運行查詢時依賴於 HDFS 中的臨時檔。 當 HDFS 進入安全模式時,Hive 無法運行查詢,因為它無法寫入 HDFS。 HDFS 中的暫存檔案位於載入到單個輔助節點 VM 的本地驅動器中。 這些檔在三個副本的其他輔助節點之間複製,最小。

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>如何防止 HDInsight 陷入安全模式

有數種方式可避免讓 HDInsight 無法脫離安全模式：

* 先停止所有 Hive 作業，再相應減少 HDInsight。 或者，對相應減少程序進行排程，以免與執行中的 Hive 作業發生衝突。
* 先手動清除 Hive 在 HDFS 中的暫存 `tmp` 目錄檔案，再相應減少。
* 只將 HDInsight 相應減少為三個背景工作節點 (最小值)。 避免減少到只剩一個背景工作節點。
* 如有需要，則執行命令來脫離安全模式。

下列幾節會說明這些選項。

#### <a name="stop-all-hive-jobs"></a>停止所有 Hive 作業

先停止所有 Hive 作業，再相應減少為一個背景工作節點。 如果您已將工作負載排程，則請在 Hive 工作完成後執行相應減少。

在縮放之前停止 Hive 作業有助於最小化 tmp 資料夾中的暫存檔數(如果有)。

#### <a name="manually-clean-up-hives-scratch-files"></a>手動清除 Hive 的暫存檔案

如果 Hive 有遺留暫存檔案，則可以先手動清除那些檔案再相應減少，以避開安全模式。

1. 以檢視設定屬性,`hive.exec.scratchdir`檢查 Hive 暫存檔正在使用的位置。 這裡設定值`/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. 停止 Hive 服務，並確定所有查詢和作業皆已完成。

1. 列出上面找到的暫存目錄的內容,`hdfs://mycluster/tmp/hive/`以檢視它是否包含任何檔案:

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

1. 如果您知道 Hive 不會再使用這些檔案，則可將其移除。 通過在 Yarn 資源管理員 UI 頁中尋找,確保 Hive 沒有任何查詢運行。

    從 HDFS 中移除檔案的命令列範例：

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>將 HDInsight 縮放為三個或更多輔助節點

如果群集在向下擴展到少於三個輔助節點時經常陷入安全模式,則至少保留三個輔助節點。

擁有三個輔助節點比僅縮減到一個輔助節點的成本更高。 但是,此操作將防止群集陷入安全模式。

### <a name="scale-hdinsight-down-to-one-worker-node"></a>將 HDInsight 縮放到一個輔助節點

即使群集縮小到一個節點,輔助節點 0 仍將存活。 輔助節點 0 永遠不會停用。

#### <a name="run-the-command-to-leave-safe-mode"></a>執行命令來脫離安全模式

最後一個選項是執行離開安全模式命令。 如果 HDFS 由於 Hive 檔案複製不足而進入安全模式,請執行以下指令以離開安全模式:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>縮小 Apache HBase 叢集

區域伺服器在完成縮放操作後幾分鐘內自動平衡。 要手動平衡區域伺服器,完成以下步驟:

1. 使用 SSH 連線到 HDInsight 叢集。 有關詳細資訊,請參閱將[SSH 與 HDInsight 一起使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

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
