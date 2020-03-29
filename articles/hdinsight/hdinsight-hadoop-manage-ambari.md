---
title: 使用 Ambari Web UI 監視和管理 Azure HDInsight
description: 了解如何使用 Ambari 來監視和管理以 Linux 為基礎的 HDInsight 叢集。 在本文件中，您會學習如何使用 HDInsight 叢集隨附的 Ambari Web UI。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: bf780897317d41c7da85140f64313546cf5c31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064685"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>使用 Apache Ambari Web UI 管理 HDInsight 叢集

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari 提供容易使用的 Web UI 和 REST API，可簡化 Apache Hadoop 叢集的管理和監視。 Ambari 包含在 HDInsight 群集中，用於監視群集和進行配置更改。

在本文件中，您會學習如何搭配使用 Ambari Web UI 和 HDInsight 叢集。

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>什麼是 Apache Ambari？

[Apache Ambari](https://ambari.apache.org) 提供方便使用的 Web UI，簡化 Hadoop 管理。 您可以使用 Ambari 來管理及監視 Hadoop 叢集。 開發人員可以使用 [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)將這些功能整合到應用程式。

## <a name="connectivity"></a>連線能力

Ambari Web UI 在 HDInsight 群集`https://CLUSTERNAME.azurehdinsight.net`上可用`CLUSTERNAME`，群集的名稱位於此位置。

> [!IMPORTANT]  
> 連線到 HDInsight 上的 Ambari 需要 HTTPS。 當系統提示要驗證時，請使用您在叢集建立時所提供的系統管理帳戶名稱和密碼。 如果未提示您輸入憑據，請檢查網路設置以確認用戶端和 Azure HDInsight 群集之間沒有連接問題。

## <a name="ssh-tunnel-proxy"></a>SSH 通道 (Proxy)

雖然您的群集的 Ambari 可通過 Internet 直接存取，但安巴里 Web UI（如 JobTracker）的某些連結不會在互聯網上公開。 若要存取這些服務，您必須建立 SSH 通道。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md)。

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]  
> 並非所有 Ambari Web UI 功能在 HDInsight 上都受到支援。 如需詳細資訊，請參閱本文件的 [不支援的作業](#unsupported-operations)一節。

連接到 Ambari Web UI 時，系統會提示您對頁面進行身份驗證。 使用您在叢集建立期間使用的叢集管理使用者 (預設值是 Admin) 和密碼。

當頁面開啟時，請注意頂端的資訊列。 此列包含下列資訊和控制項：

![阿帕奇·安巴里儀表板概述](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Item |描述 |
|---|---|
|安巴里標誌|打開儀表板，該儀表板可用於監視群集。|
|群集名稱 = 操作|顯示正在進行的 Ambari 操作的數量。 選取叢集名稱或 [# 項作業]**** 會顯示背景作業清單。|
|• 警報|顯示群集的警告或嚴重警報（如果有）。|
|儀表板|顯示儀表板。|
|服務|群集中服務的資訊和配置設置。|
|主機|群集中節點的資訊和配置設置。|
|警示|資訊、警告和嚴重警報的日誌。|
|管理|群集上安裝的軟體堆疊/服務、服務帳戶資訊和 Kerberos 安全性。|
|管理按鈕|Ambari 管理、使用者設置和登出。|

## <a name="monitoring"></a>監視

### <a name="alerts"></a>警示

下列清單包含 Ambari 常用的警示狀態︰

* **確定**
* **警告**
* **關鍵**
* **UNKNOWN**

[確定]**** 以外的警示會導致頁面頂端出現 [# 個警示]**** 項目，以顯示警示數目。 選取此項目會顯示警示及其狀態。

警示分成數個預設群組，您可以從 [ **警示** ] 頁面進行檢視。

![阿帕奇·安巴里提醒頁面摘要](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

您可以使用 [動作]**** 功能表並選取 [管理警示群組]**** 來管理這些群組。

![阿帕奇·安巴里管理警報組](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

您還可以管理警報方法，並通過選擇 __"管理通知__"從 **"操作"** 功能表創建警報通知。 系統會顯示任何目前的通知。 您也可以從這裡建立通知。 在發生特定警示/嚴重性組合時，便可透過**電子郵件**或 **SNMP** 傳送通知。 例如，您可以在 [YARN 預設]**** 群組中的任何警示設為 [重要]**** 時傳送電子郵件訊息。

![阿帕奇·安巴里創建警報通知](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

最後，從 [動作]____ 功能表選取 [管理警示設定]____ 可讓您設定必須發生幾次警示才會傳送通知。 這項設定可以用來防止暫時性錯誤的通知。

有關使用免費[SendGrid 帳戶](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)的警報通知的教程，請參閱[在 Azure HDInsight 中配置 Apache Ambari 電子郵件通知](./apache-ambari-email.md)。

### <a name="cluster"></a>叢集

儀表板的 [ **度量** ] 索引標籤包含一系列的 Widget，可讓您一目了然地輕鬆監視叢集的狀態。 [ **CPU 使用量**] 等數個 Widget 可在點按後提供其他資訊。

![阿帕奇 Ambari 儀表板與指標](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

[ **熱圖** ] 索引標籤會以綠色到紅色的彩色熱圖顯示度量。

![阿帕奇安巴里儀表板與熱圖](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

如需有關叢集中節點的詳細資訊，請選取 [主機]****。 然後選擇您感興趣的特定節點。

![阿帕奇·安巴里主機摘要詳細資訊](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>服務

儀表板上的 [ **服務** ] 提要欄位可讓您快速了解叢集上執行之服務的狀態。 各種圖示用來指出狀態或應採取的動作。 例如，需要回收服務時會顯示黃色回收符號。

![阿帕奇安巴里服務側酒吧](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> 不同 HDInsight 叢集類型和版本之間會顯示不同的服務。 這裡顯示的服務可能會不同於您的叢集所顯示的服務。

選取服務便會顯示服務的詳細資訊。

![阿帕奇·安巴里服務摘要資訊](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>快速連結

某些服務會在頁面頂端顯示 [ **快速連結** ] 連結。 此連結可用來存取服務特定的 Web UI，例如：

* **作業記錄** - MapReduce 作業記錄。
* **資源管理員** - YARN ResourceManager UI。
* **NameNode** - Hadoop 分散式檔案系統 (HDFS) NameNode UI。
* **Oozie Web UI** - Oozie UI。

選取任何一個連結便會在瀏覽器中開啟新索引標籤以顯示選取的頁面。

> [!NOTE]  
> 對服務選取 [快速連結]**** 項目可能會傳回「找不到伺服器」的錯誤。 如果您遇到這個錯誤，在對此服務使用 [快速連結]**** 項目時，您必須使用 SSH 通道。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md)。

## <a name="management"></a>管理性

### <a name="ambari-users-groups-and-permissions"></a>Ambari 使用者、群組和權限

使用[已加入網域](./domain-joined/hdinsight-security-overview.md)的 HDInsight 叢集時，支援處理使用者、群組和權限。 如需在已加入網域之叢集上使用 Ambari 管理 UI 的相關資訊，請參閱[管理已加入網域的 HDInsight 叢集](./domain-joined/hdinsight-security-overview.md)。

> [!WARNING]  
> 請勿變更以 Linux 為基礎之 HDInsight 叢集上的 Ambari 看門狗 (hdinsightwatchdog) 密碼。 變更密碼會破壞在叢集上使用指令碼動作或執行調整作業的能力。

### <a name="hosts"></a>主機

[ **主機** ] 頁面會列出叢集中的所有主機。 若要管理主機，請遵循下列步驟。

![阿帕奇·安巴里主機頁面概述](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> 使用 HDInsight 叢集時，請勿新增、解除委任或重新委任主機。

1. 選取您想要管理的主機。

2. 使用 [ **動作** ] 功能表，選擇您想要執行的動作：

    |Item |描述 |
    |---|---|
    |啟動所有元件|啟動主機上的所有元件。|
    |停止所有元件|停止主機上的所有元件。|
    |重新開機所有元件|停止並啟動主機上的所有元件。|
    |打開維護模式|禁止主機警報。 如果您正在執行生成警報的操作，則應啟用此模式。 例如，停止和啟動服務。|
    |關閉維護模式|將主機返回到正常警報。|
    |Stop|停止主機上的資料節點或節點管理器。|
    |Start|在主機上啟動 DataNode 或節點管理器。|
    |重新啟動|在主機上停止並啟動 DataNode 或節點管理器。|
    |解除委任|從群集中刪除主機。 **請勿在 HDInsight 叢集上使用此動作。**|
    |重新調試|將以前已停用的主機添加到群集中。 **請勿在 HDInsight 叢集上使用此動作。**|

### <a name="services"></a><a id="service"></a>服務

在 [儀表板]**** 或 [服務]**** 頁面中，使用服務清單底部的 [動作]**** 按鈕來停止和啟動所有服務。

![阿帕奇·安巴里服務動作清單](./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png)

> [!WARNING]  
> 雖然 [新增服務]**** 列在此功能表中，但不應用來將服務新增 HDInsight 叢集。 您應該在叢集佈建期間，使用指令碼動作加入新服務。 如需使用指令碼動作的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

雖然 [ **動作** ] 按鈕可以重新啟動所有服務，但您想要啟動、停止或重新啟動的往往是特定服務。 使用下列步驟可對個別服務執行動作：

1. 從 [儀表板]**** 或 [服務]**** 頁面選取服務。

2. 從 [摘要]**** 索引標籤頂端，使用 [服務動作]**** 按鈕，然後選取要採取的動作。 這會重新啟動所有節點上的服務。

    ![阿帕奇·安巴里個人服務操作](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > 在叢集執行時重新啟動某些服務可能會產生警示。 若要避免警示，您可以使用 [服務動作]**** 按鈕來啟用服務的 [維護模式]****，然後再執行重新啟動。

3. 一旦選取某個動作，頁面頂端的 [# 項作業]**** 項目便會遞增數字，指出正在進行背景作業。 如果設定為顯示，則會顯示背景作業的清單。

   > [!NOTE]  
   > 如果您已啟用服務的 [維護模式]****，請記得在作業完成後使用 [服務動作]**** 按鈕來將它停用。

若要設定服務，請使用下列步驟：

1. 從 [儀表板]**** 或 [服務]**** 頁面選取服務。

2. 選擇"**配置"** 選項卡。將顯示當前配置。 同時也會顯示先前組態的清單。

    ![阿帕奇·安巴里服務配置](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. 使用顯示的欄位修改組態，然後選取 [ **儲存**]。 或選取先前的組態，然後選取 [ **設為現用** ] 以回復到先前的設定。

## <a name="ambari-views"></a>Ambari 檢視

Ambari 檢視可讓開發人員使用 [Apache Ambari 檢視架構](https://cwiki.apache.org/confluence/display/AMBARI/Views)將 UI 元素插入 Ambari Web UI 中。 HDInsight 提供下列具有 Hadoop 叢集類型的檢視：

* Hive 檢視：Hive 檢視可讓您直接從網頁瀏覽器執行 Hive 查詢。 您可以儲存查詢、檢視結果、將結果儲存至叢集存放區，或將結果下載到您本機系統。 如需有關使用 Hive 檢視的詳細資訊，請參閱 [在 HDInsight 上使用 Apache Hive 檢視](hadoop/apache-hadoop-use-hive-ambari-view.md)。

* Tez 檢視︰[Tez 檢視] 可讓您進一步了解和最佳化工作。 您可以檢視有關 Tez 工作執行方式及使用哪些資源的資訊。

## <a name="unsupported-operations"></a>不支援的作業

以下是 HDInsight 不支援的 Ambari 作業：

* __移動 Metrics Collector (計量收集器) 服務__。 檢視 Metrics Collector (計量收集器) 服務上的資訊時，[Service Actions] \(服務動作\) 功能表提供的其中一個動作是 [Move Metrics collector] \(移動計量收集器\)____。 HDInsight 並不支援此動作。

## <a name="next-steps"></a>後續步驟

了解如何將 [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) 與 HDInsight 搭配使用。
