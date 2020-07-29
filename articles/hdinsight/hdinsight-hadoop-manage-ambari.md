---
title: 使用 Ambari Web UI 監視和管理 Azure HDInsight
description: 瞭解如何使用 Apache Ambari UI 來監視和管理 HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/16/2020
ms.openlocfilehash: ce078a3dad645f592bb33ed55ce508f68ce8f30a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281376"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>使用 Apache Ambari Web UI 管理 HDInsight 叢集

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari 可簡化 Apache Hadoop 叢集的管理和監視。 藉由提供容易使用的 web UI 和 REST API，即可簡化這項作業。 Ambari 包含在 HDInsight 叢集上，可用來監視叢集並進行設定變更。

在本文件中，您會學習如何搭配使用 Ambari Web UI 和 HDInsight 叢集。

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>什麼是 Apache Ambari？

[Apache Ambari](https://ambari.apache.org) 提供方便使用的 Web UI，簡化 Hadoop 管理。 您可以使用 Ambari 來管理及監視 Hadoop 叢集。 開發人員可以使用 [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)將這些功能整合到應用程式。

## <a name="connectivity"></a>連線能力

Ambari Web UI 可在您的 HDInsight 叢集上使用 `https://CLUSTERNAME.azurehdinsight.net` ，其中 `CLUSTERNAME` 是您的叢集名稱。

> [!IMPORTANT]  
> 連線到 HDInsight 上的 Ambari 需要 HTTPS。 當系統提示要驗證時，請使用您在叢集建立時所提供的系統管理帳戶名稱和密碼。 如果系統不會提示您輸入認證，請檢查您的網路設定，以確認用戶端與 Azure HDInsight 叢集之間沒有連線問題。

## <a name="ssh-tunnel-proxy"></a>SSH 通道 (Proxy)

雖然您叢集的 Ambari 可透過網際網路直接存取，但 Ambari Web UI （例如 JobTracker）的某些連結並不會在網際網路上公開。 若要存取這些服務，您必須建立 SSH 通道。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md)。

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]  
> 並非所有 Ambari Web UI 功能在 HDInsight 上都受到支援。 如需詳細資訊，請參閱本文件的 [不支援的作業](#unsupported-operations)一節。

當連接到 Ambari Web UI 時，系統會提示您向頁面進行驗證。 使用您在叢集建立期間使用的叢集管理使用者 (預設值是 Admin) 和密碼。

當頁面開啟時，請注意頂端的資訊列。 此列包含下列資訊和控制項：

![Apache Ambari 儀表板總覽](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|項目 |說明 |
|---|---|
|Ambari 標誌|開啟 [儀表板]，可以用來監視叢集。|
|叢集名稱 # ops|顯示進行中的 Ambari 作業數目。 選取叢集名稱或 [# 項作業]**** 會顯示背景作業清單。|
|警示數|顯示叢集的警告或重要警示（如果有的話）。|
|儀表板|顯示儀表板。|
|服務|叢集中服務的資訊和設定。|
|主機|叢集中節點的資訊和設定。|
|警示|資訊、警告和重要警示的記錄。|
|管理|安裝在叢集上的軟體堆疊/服務、服務帳戶資訊及 Kerberos 安全性。|
|[系統管理] 按鈕|Ambari 管理、使用者設定和登出。|

## <a name="monitoring"></a>監視

### <a name="alerts"></a>警示

下列清單包含 Ambari 常用的警示狀態︰

* **確定**
* **警告**
* **成**
* **UNKNOWN**

[確定]**** 以外的警示會導致頁面頂端出現 [# 個警示]**** 項目，以顯示警示數目。 選取此項目會顯示警示及其狀態。

警示分成數個預設群組，您可以從 [ **警示** ] 頁面進行檢視。

![Apache Ambari 警示頁面摘要](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

您可以使用 [動作]**** 功能表並選取 [管理警示群組]**** 來管理這些群組。

![Apache Ambari 管理警示群組](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

您可以透過選取 [__管理通知__] 來管理警示方法，並從 [**動作**] 功能表建立警示通知。 系統會顯示任何目前的通知。 從這裡建立通知。 在發生特定警示/嚴重性組合時，便可透過**電子郵件**或 **SNMP** 傳送通知。 例如，您可以在 [YARN 預設]**** 群組中的任何警示設為 [重要]**** 時傳送電子郵件訊息。

![Apache Ambari 建立警示通知](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

最後，從 [動作]____ 功能表選取 [管理警示設定]____ 可讓您設定必須發生幾次警示才會傳送通知。 這項設定可以用來防止暫時性錯誤的通知。

如需使用免費[SendGrid 帳戶](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)的警示通知教學課程，請參閱[在 Azure HDInsight 中設定 Apache Ambari 電子郵件通知](./apache-ambari-email.md)。

### <a name="cluster"></a>叢集

儀表板的 [ **度量** ] 索引標籤包含一系列的 Widget，可讓您一目了然地輕鬆監視叢集的狀態。 [ **CPU 使用量**] 等數個 Widget 可在點按後提供其他資訊。

![具有計量的 Apache Ambari 儀表板](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

[ **熱圖** ] 索引標籤會以綠色到紅色的彩色熱圖顯示度量。

![具有熱度圖的 Apache Ambari 儀表板](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

如需有關叢集中節點的詳細資訊，請選取 [主機]****。 然後選取您感興趣的特定節點。

![Apache Ambari 主機摘要詳細資料](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>服務

儀表板上的 [ **服務** ] 提要欄位可讓您快速了解叢集上執行之服務的狀態。 各種圖示用來指出狀態或應採取的動作。 例如，需要回收服務時會顯示黃色回收符號。


![Apache Ambari services 端列](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> 不同 HDInsight 叢集類型和版本之間會顯示不同的服務。 這裡顯示的服務可能會不同於您的叢集所顯示的服務。

選取服務便會顯示服務的詳細資訊。

![Apache Ambari 服務摘要資訊](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>快速連結

某些服務會在頁面頂端顯示 [ **快速連結** ] 連結。 此連結可用於存取服務特定的 web Ui，例如：

* **作業記錄** - MapReduce 作業記錄。
* **Resource Manager** YARN Resource Manager UI。
* **NameNode** - Hadoop 分散式檔案系統 (HDFS) NameNode UI。
* **Oozie Web UI** - Oozie UI。

選取任何一個連結便會在瀏覽器中開啟新索引標籤以顯示選取的頁面。

> [!NOTE]  
> 對服務選取 [快速連結]**** 項目可能會傳回「找不到伺服器」的錯誤。 如果您遇到這個錯誤，在對此服務使用 [快速連結]**** 項目時，您必須使用 SSH 通道。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md)。

## <a name="management"></a>管理性

### <a name="ambari-users-groups-and-permissions"></a>Ambari 使用者、群組和權限

支援使用使用者、群組和許可權。 如需本機系統管理，請參閱[為 Apache Ambari Views 授權使用者](./hdinsight-authorize-users-to-ambari.md)。 針對已加入網域的叢集，請參閱[管理已加入網域的 HDInsight](./domain-joined/hdinsight-security-overview.md)叢集。

> [!WARNING]  
> 請勿變更以 Linux 為基礎之 HDInsight 叢集上的 Ambari 看門狗 (hdinsightwatchdog) 密碼。 變更密碼會破壞在叢集上使用指令碼動作或執行調整作業的能力。

### <a name="hosts"></a>主機

[ **主機** ] 頁面會列出叢集中的所有主機。 若要管理主機，請遵循下列步驟。

![Apache Ambari 主機頁面總覽](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> 使用 HDInsight 叢集時，請勿新增、解除委任或重新委任主機。

1. 選取您想要管理的主機。

2. 使用 [**動作**] 功能表來選取您想要執行的動作：

    |項目 |說明 |
    |---|---|
    |啟動所有元件|啟動主機上的所有元件。|
    |停止所有元件|停止主機上的所有元件。|
    |重新開機所有元件|停止並啟動主機上的所有元件。|
    |開啟維護模式|抑制主機的警示。 如果您要執行會產生警示的動作，則應該啟用此模式。 例如，停止和啟動服務。|
    |關閉維護模式|將主機恢復正常警示。|
    |Stop|停止主機上的 DataNode 或 NodeManagers。|
    |開始|在主機上啟動 DataNode 或 NodeManagers。|
    |重新啟動|在主機上停止並啟動 DataNode 或 NodeManagers。|
    |解除委任|從叢集中移除主機。 **請勿在 HDInsight 叢集上使用此動作。**|
    |重新委任|將先前已解除委任的主機新增至叢集。 **請勿在 HDInsight 叢集上使用此動作。**|

### <a name="services"></a><a id="service"></a>服務

在 [儀表板]**** 或 [服務]**** 頁面中，使用服務清單底部的 [動作]**** 按鈕來停止和啟動所有服務。

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png" alt-text="Apache Ambari 服務動作清單。" border="true":::

> [!WARNING]  
> 您應該在叢集佈建期間，使用指令碼動作加入新服務。 如需使用指令碼動作的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

雖然 [ **動作** ] 按鈕可以重新啟動所有服務，但您想要啟動、停止或重新啟動的往往是特定服務。 請使用下列步驟來對個別服務執行動作：

1. 從 [儀表板]**** 或 [服務]**** 頁面選取服務。

2. 從 [摘要]**** 索引標籤頂端，使用 [服務動作]**** 按鈕，然後選取要採取的動作。 此動作會重新開機所有節點上的服務。

    ![Apache Ambari 個別服務動作](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > 在叢集執行時重新啟動某些服務可能會產生警示。 若要避免警示，您可以使用 [服務動作]**** 按鈕來啟用服務的 [維護模式]****，然後再執行重新啟動。

3. 一旦選取某個動作，頁面頂端的 [# 項作業]**** 項目便會遞增數字，指出正在進行背景作業。 如果設定為顯示，則會顯示背景作業的清單。

   > [!NOTE]  
   > 如果您已啟用服務的 [維護模式]****，請記得在作業完成後使用 [服務動作]**** 按鈕來將它停用。

若要設定服務，請使用下列步驟：

1. 從 [儀表板]**** 或 [服務]**** 頁面選取服務。

2. 選取 [**選項**] 索引標籤。目前的設定隨即顯示。 同時也會顯示先前組態的清單。

    ![Apache Ambari 服務設定](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. 使用顯示的欄位修改組態，然後選取 [ **儲存**]。 或選取先前的組態，然後選取 [ **設為現用** ] 以回復到先前的設定。

## <a name="ambari-views"></a>Ambari 檢視

Ambari 檢視可讓開發人員使用 Apache Ambari 檢視架構將 UI 元素插入 Ambari Web UI 中。 HDInsight 提供下列具有 Hadoop 叢集類型的檢視：

* Hive 檢視：Hive 檢視可讓您直接從網頁瀏覽器執行 Hive 查詢。 您可以儲存查詢、檢視結果、將結果儲存至叢集存放區，或將結果下載到您本機系統。 如需有關使用 Hive 檢視的詳細資訊，請參閱 [在 HDInsight 上使用 Apache Hive 檢視](hadoop/apache-hadoop-use-hive-ambari-view.md)。

* Tez 檢視︰[Tez 檢視] 可讓您進一步了解和最佳化工作。 您可以檢視有關 Tez 工作執行方式及使用哪些資源的資訊。

## <a name="unsupported-operations"></a>不支援的作業

HDInsight 不支援下列 Ambari 作業：

* __移動 Metrics Collector (計量收集器) 服務__。 檢視 Metrics Collector (計量收集器) 服務上的資訊時，[Service Actions] \(服務動作\) 功能表提供的其中一個動作是 [Move Metrics collector] \(移動計量收集器\)____。 HDInsight 不支援此動作。

## <a name="next-steps"></a>後續步驟

* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)搭配 HDInsight。
* [使用 Apache Ambari 將 HDInsight 叢集設定最佳化](./hdinsight-changing-configs-via-ambari.md)
* [調整 Azure HDInsight 叢集](./hdinsight-scaling-best-practices.md)
