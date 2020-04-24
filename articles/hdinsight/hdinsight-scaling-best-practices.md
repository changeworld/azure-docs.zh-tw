---
title: 調整叢集大小 - Azure HDInsight
description: 調整 Apache Hadoop 叢集彈性以符合您在 Azure HDInsight 中的工作負載
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: aee0576654827351fa5ff9b3c29b57d0d4148d3e
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116599"
---
# <a name="scale-azure-hdinsight-clusters"></a>調整 Azure HDInsight 叢集

HDInsight 提供彈性選項，以相應增加和相應減少叢集中的背景工作節點數目。 此彈性可讓您在數小時或週末時縮減叢集。 並在業務需求尖峰期間進行擴充。

在定期批次處理之前相應增加您的叢集，讓叢集擁有足夠的資源。 處理完成且使用量下降時，請將 HDInsight 叢集相應減少至較少的背景工作節點。

您可以使用以下所述的其中一種方法來手動調整叢集。 您也[可以使用 [自動調整](hdinsight-autoscale-clusters.md)] 選項，自動相應增加和減少，以回應特定計量。

> [!NOTE]  
> 只支援使用 HDInsight 3.1.3 版或更高版本的叢集。 如果不確定您的叢集版本，您可以檢查 [屬性] 頁面。

## <a name="utilities-to-scale-clusters"></a>調整叢集的公用程式

Microsoft 提供下列公用程式來調整叢集：

|公用程式 | 說明|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[`Set-AzHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) | [`az hdinsight resize`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure 傳統 CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure 入口網站](https://portal.azure.com)|開啟您的 HDInsight 叢集窗格，選取左側功能表上的 [叢集**大小**]，然後在 [叢集大小] 窗格上，輸入背景工作節點的數目，然後選取 [儲存]。|  

![Azure 入口網站 scale cluster 選項](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

只要使用上述任一方法，您即可在幾分鐘內相應增加或相應減少 HDInsight 叢集。

> [!IMPORTANT]  
> * Azure 傳統 CLI 已淘汰，應僅與傳統部署模型搭配使用。 針對所有其他部署，請使用[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。
> * PowerShell AzureRM 模組已被取代。  請盡可能使用[Az 模組](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)。

## <a name="impact-of-scaling-operations"></a>調整作業的影響

當您**將節點新增**至執行中的 HDInsight 叢集（相應增加）時，作業不會受到影響。 在調整程序執行的同時，您仍可放心地提交新的作業。 如果調整作業失敗，失敗會讓您的叢集處於正常運作狀態。

如果您**移除**節點（相應減少），則在調整作業完成時，擱置或執行中的作業將會失敗。 此失敗的原因是在調整程式期間重新開機某些服務。 在手動調整作業期間，您的叢集可能會卡在安全模式中。

變更資料節點數目的影響會因為 HDInsight 支援的各種類型叢集而有所不同：

* Apache Hadoop

    您可以順暢地增加正在執行的 Hadoop 叢集中的背景工作節點數目，而不會影響任何作業。 您也可以在作業進行當中提交新工作。 調整作業中的失敗會以適當的方式處理。 叢集一律會保持在正常運作狀態。

    當 Hadoop 叢集以較少的資料節點相應減少時，部分服務會重新開機。 此行為會導致所有執行中和擱置的工作在調整作業完成時失敗。 但您可以在作業完成後重新提交這些工作。

* Apache HBase (英文)

    當您的 HBase 叢集正在執行時，您可以順暢地新增或移除節點。 區域伺服器會在完成調整作業的數分鐘之內自動取得平衡。 不過，您可以手動平衡區域伺服器。 登入叢集前端節點，然後執行下列命令：

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    如需使用 HBase 殼層的詳細資訊，請參閱[開始使用 HDInsight 中的 Apache HBase 範例](hbase/apache-hbase-tutorial-get-started-linux.md)。

* Apache Storm

    當執行時，您可以順暢地新增或移除資料節點。 不過，在調整作業成功完成後，您必須重新平衡拓撲。

    您可以使用兩種方式來完成重新平衡作業：

  * Storm Web UI
  * 命令列介面 (CLI) 工具

    如需詳細資訊，請參閱[Apache Storm 檔](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

    HDInsight 叢集上有提供 Storm Web UI：

    ![HDInsight Storm 調整重新平衡](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    以下是用來重新平衡 Storm 拓撲的範例 CLI 命令：

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>如何安全地向下調整叢集

### <a name="scale-down-a-cluster-with-running-jobs"></a>使用執行中的作業向下擴充叢集

為避免執行中的工作在相應減少作業期間失敗，您可以嘗試三個專案：

1. 等待工作完成，再相應減少叢集。
1. 手動結束作業。
1. 在調整作業結束後重新提交工作。

若要查看擱置和執行中作業的清單，您可以使用 YARN **RESOURCE MANAGER UI**，請遵循下列步驟：

1. 從 [ [Azure 入口網站](https://portal.azure.com/)] 中，選取您的叢集。  叢集會在新的入口網站頁面中開啟。
2. 從主要視圖中，流覽至 [叢集**儀表板** > ]**Ambari home**。 輸入您的叢集認證。
3. 從 Ambari UI 中，選取左側功能表上的 [服務] 清單中的 [ **YARN** ]。  
4. 從 [YARN] 頁面選取 [**快速連結**]，並將滑鼠停留在使用中的前端節點上，然後選取 [ **Resource Manager UI**]。

    ![Apache Ambari 快速連結 Resource Manager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

您可以使用`https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`直接存取 Resource Manager UI。

您會看到作業及其目前狀態的清單。 在螢幕擷取畫面中，目前有一項作業正在執行：

![Resource Manager UI 應用程式](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

若要手動終止該執行中的應用程式，請從 SSH 殼層執行下列命令：

```bash
yarn application -kill <application_id>
```

例如：

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>進入安全模式

當您相應減少叢集時，HDInsight 會使用 Apache Ambari 管理介面來先解除委任額外的背景工作節點。 節點會將其 HDFS 區塊複寫至其他線上背景工作角色節點。 之後，HDInsight 會安全地向下調整叢集規模。 HDFS 會在調整作業期間進入安全模式。 在調整完成後，HDFS 應會出現。 不過，在某些情況下，在調整作業期間，HDFS 會停滯在安全模式中，因為複寫中的檔案區塊。

根據預設，HDFS 會`dfs.replication`設定為1，以控制每個檔案區塊可用的複本數目。 檔案區塊的每個複本都儲存在叢集的不同節點上。

當預期的區塊複本數目無法使用時，HDFS 會進入安全模式，而 Ambari 會產生警示。 HDFS 可進入安全模式進行調整作業。 如果未偵測到所需的節點數目來進行複寫，叢集可能會卡在安全模式中。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>安全模式開啟時的錯誤範例

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

您可以從 `/var/log/hadoop/hdfs/` 資料夾檢閱接近叢集調整時的名稱節點記錄，以查看其進入安全模式的時間。 記錄檔名稱為 `Hadoop-hdfs-namenode-<active-headnode-name>.*`。

根本原因是 Hive 相依于 HDFS 中的暫存檔案，同時執行查詢。 當 HDFS 進入安全模式時，Hive 無法執行查詢，因為它無法寫入 HDFS。 HDFS 中的暫存檔案位於掛接至個別背景工作角色節點 Vm 的本機磁片磁碟機中。 檔案會在三個複本的其他背景工作節點之間複寫（最少）。

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>如何防止 HDInsight 進入安全模式

有數種方式可避免讓 HDInsight 無法脫離安全模式：

* 先停止所有 Hive 作業，再相應減少 HDInsight。 或者，對相應減少程序進行排程，以免與執行中的 Hive 作業發生衝突。
* 先手動清除 Hive 在 HDFS 中的暫存 `tmp` 目錄檔案，再相應減少。
* 只將 HDInsight 相應減少為三個背景工作節點 (最小值)。 避免減少到只剩一個背景工作節點。
* 如有需要，則執行命令來脫離安全模式。

下列幾節會說明這些選項。

#### <a name="stop-all-hive-jobs"></a>停止所有 Hive 作業

先停止所有 Hive 作業，再相應減少為一個背景工作節點。 如果您已將工作負載排程，則請在 Hive 工作完成後執行相應減少。

在調整規模之前停止 Hive 作業，有助於將 tmp 資料夾中的暫存檔案數目降至最低（如果有的話）。

#### <a name="manually-clean-up-hives-scratch-files"></a>手動清除 Hive 的暫存檔案

如果 Hive 有遺留暫存檔案，則可以先手動清除那些檔案再相應減少，以避開安全模式。

1. 查看`hive.exec.scratchdir` configuration 屬性，檢查哪個位置正在用於 Hive 暫存檔案。 此參數設定于內`/etc/hive/conf/hive-site.xml`：

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. 停止 Hive 服務，並確定所有查詢和作業皆已完成。

1. 列出上方找到的臨時目錄內容， `hdfs://mycluster/tmp/hive/`以查看它是否包含任何檔案：

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

1. 如果您知道 Hive 不會再使用這些檔案，則可將其移除。 查看 Yarn Resource Manager UI 頁面，確定 Hive 沒有任何執行中的查詢。

    從 HDFS 中移除檔案的命令列範例：

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>將 HDInsight 調整為三個或多個背景工作節點

如果您的叢集在相應減少到三個背景工作節點時經常停滯在安全模式，請至少保留三個背景工作節點。

擁有三個背景工作角色節點比相應減少到只有一個背景工作節點來得昂貴。 不過，此動作會讓您的叢集無法進入安全模式。

### <a name="scale-hdinsight-down-to-one-worker-node"></a>將 HDInsight 向下調整為一個背景工作節點

即使叢集縮小為一個節點，背景工作節點0仍然會存留下來。 背景工作節點0永遠無法解除委任。

#### <a name="run-the-command-to-leave-safe-mode"></a>執行命令來脫離安全模式

最後一個選項是執行「離開安全模式」命令。 如果 HDFS 因為複寫中的 Hive 檔案而進入安全模式，請執行下列命令以離開安全模式：

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>相應減少 Apache HBase 叢集

區域伺服器會在完成調整作業之後的幾分鐘內自動達到平衡。 若要手動平衡區域伺服器，請完成下列步驟：

1. 使用 SSH 連線到 HDInsight 叢集。 如需詳細資訊，請參閱搭配[HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

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
