---
title: Hadoop 高可用性 - Azure HDInsight
description: 了解如何使用額外的前端節點，讓 HDInsight 叢集可以提高可靠性和可用性。 了解這會如何影響例如 Ambari 和 Hive 等 Hadoop 服務，以及如何使用 SSH 分別連線到每個前端節點。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop 高可用性
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 085933f9a74ee37779ce63ce499d89ea53a9f7d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198934"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>HDInsight 中 Apache Hadoop 叢集的可用性和可靠性

HDInsight 叢集提供兩個前端節點，可提升執行中 Apache Hadoop 服務和作業的可用性與可靠性。

Hadoop 藉由在叢集中的多個節點間複寫服務和資料，以達到高可用性和可靠性。 不過 Hadoop 的標準散佈功能通常只能有一個前端節點。 任何單一前端節點的中斷情況都可能導致叢集停止運作。 HDInsight 提供兩個前端節點，以改善 Hadoop 的可用性和可靠性。

## <a name="availability-and-reliability-of-nodes"></a>節點的可用性和可靠性

使用 Azure 虛擬機器在 HDInsight 叢集中的節點實作。 下列各節會討論個別的節點類型所搭配使用的 HDInsight。

> [!NOTE]  
> 並非所有的節點類型都可用於某個叢集類型。 例如，Hadoop 叢集類型就不會有任何 Nimbus 節點。 如需 HDInsight 叢集類型所使用的節點詳細資訊，請參閱[在 HDInsight 建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)文件的＜叢集類型＞一節。

### <a name="head-nodes"></a>前端節點

為了確保 Hadoop 服務的高可用性，HDInsight 提供兩個前端節點。 這兩個前端節點會同時為作用中和在 HDInsight 叢集中執行。 有些服務 (例如 Apache HDFS 或 Apache Hadoop YARN) 在任何指定的時間都只能在一個前端節點上處於「作用中」。 HiveServer2 或 Hive MetaStore 等其他服務可同時在這兩個前端節點上作用。

要獲取群集中不同節點類型的主機名稱，請使用[Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes)。

> [!IMPORTANT]  
> 請勿將數值與節點 (不論是主要或次要) 相關聯。 數值只是用來為每個節點提供唯一名稱。

### <a name="nimbus-nodes"></a>Nimbus 節點

Nimbus 節點是 Apache Storm 叢集隨附的節點。 Nimbus 節點會透過在背景工作節點之間散佈並監視處理，來提供 Hadoop jobtracker 類似的功能。 HDInsight 為 Storm 叢集提供兩個 Nimbus 節點

### <a name="apache-zookeeper-nodes"></a>Apache ZooKeeper 節點

[ZooKeeper](https://zookeeper.apache.org/) 節點用於前端節點上主要服務的前置選擇。 它們還用於確保服務、資料（輔助節點）節點和閘道知道主服務在哪個頭節點處於活動狀態。 根據預設，HDInsight 會提供三個 ZooKeeper 節點。

### <a name="worker-nodes"></a>背景工作節點

當作業提交至叢集時，背景工作節點會執行實際的資料分析。 如果背景工作節點失敗，它所執行的工作將會提交至另一個背景工作節點。 根據預設，HDInsight 會建立四個背景工作節點。 不過，您可以視需要在叢集建立期間和之後變更該數字。

### <a name="edge-node"></a>邊緣節點

邊緣節點不積極參與群集中的資料分析。 開發人員或資料科學家在使用 Hadoop 時使用它。 邊緣節點和叢集中的其他節點一樣，存在於相同的 Azure 虛擬網路中，也可直接存取其他所有節點。 使用邊緣節點時，不需從關鍵 Hadoop 服務或分析作業取走資源。

目前，HDInsight 上的 ML 服務是唯一可依預設提供邊緣節點的叢集類型。 對於 HDInsight 上的 ML 服務來說，邊緣節點是用來在提交至叢集進行分散式處理之前，在本機節點上測試 R 程式碼使用。

如需有關將邊緣節點搭配其他叢集類型使用的相關資訊，請參閱[在 HDInsight 中使用邊緣節點](hdinsight-apps-use-edge-node.md)文件。

## <a name="accessing-the-nodes"></a>存取節點

您可以透過公用閘道，經由網際網路存取叢集。 訪問僅限於連接到頭節點，如果存在，則連接到邊緣節點。 對頭節點上運行的服務的訪問不受具有多個頭節點的影響。 公用閘道會將要求路由至託管已要求服務的前端節點。 例如，如果 Apache Ambari 目前裝載在次要前端節點上，閘道就會將針對 Ambari 的連入要求路由傳送至該節點。

通過公共閘道的訪問僅限於埠 443 （HTTPS）、22 和 23。

|連接埠 |描述 |
|---|---|
|443|用於訪問安巴里和其他託管在頭節點上的 Web UI 或 REST API。|
|22|用於使用 SSH 訪問主頭節點或邊緣節點。|
|23|用於使用 SSH 訪問輔助頭節點。 例如，`ssh username@mycluster-ssh.azurehdinsight.net` 會連線到名為 **mycluster** 之叢集的主要前端節點。|

如需使用 SSH 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md) 文件。

### <a name="internal-fully-qualified-domain-names-fqdn"></a>內部完整網域名稱 (FQDN)

HDInsight 叢集中的節點具有只能自叢集存取的內部 IP 位址和 FQDN。 使用內部 FQDN 或 IP 位址存取叢集上的服務時，您應該使用 Ambari 來驗證要存取服務時所使用的 IP 或 FQDN。

例如，Apache Oozie 服務只能在一個前端節點上執行，而從 SSH 工作階段使用 `oozie` 命令則需要有該服務的 URL。 這個 URL 可以使用下列命令從 Ambari 擷取：

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

此命令返回類似于以下內容的值，該值包含要與`oozie`命令一起使用的內部 URL：

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

如需有關使用 Ambari REST API 的詳細資訊，請參閱[使用 Apache Ambari REST API 來監視和管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md)。

### <a name="accessing-other-node-types"></a>存取其他節點類型

您可以使用以下方法連接到無法通過互聯網直接存取的節點：

|方法 |描述 |
|---|---|
|SSH|使用 SSH 連線到前端節點之後，您便可以接著從前端節點使用 SSH 來連線到叢集中的其他節點。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md) 文件。|
|SSH隧道|如果需要訪問託管在未暴露到 Internet 的節點之一上的 Web 服務，則必須使用 SSH 隧道。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md)文件。|
|Azure 虛擬網路|如果您的 HDInsight 叢集是「Azure 虛擬網路」的一部分，則任何在相同「虛擬網路」上的資源都可直接存取該叢集內的所有節點。 有關詳細資訊，請參閱[為 HDInsight 文檔規劃虛擬網路](hdinsight-plan-virtual-network-deployment.md)。|

## <a name="how-to-check-on-a-service-status"></a>如何檢查服務狀態

若要檢查在前端節點執行的服務狀態，請使用 Ambari Web UI 或 Ambari REST API。

### <a name="ambari-web-ui"></a>Ambari Web UI

可以在 `https://CLUSTERNAME.azurehdinsight.net` 檢視 Ambari Web UI。 將**CLUSTERNAME**替換為群集的名稱。 如果出現提示，請輸入叢集的 HTTP 使用者認證。 預設 HTTP 使用者名稱為 **admin** ，密碼是您在建立叢集時輸入的密碼。

當您來到 Ambari 頁面上時，會在該頁面的左邊列出已安裝的服務。

![阿帕奇·安巴里安裝服務](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

服務旁可能會出現一系列圖示以表示狀態。 可以使用在頁面頂端的 [警示] **** 連結，檢視與服務相關的任何警示。  Ambari 提供了多個預定義的警報。

以下警報有助於監視群集的可用性：

| 警示名稱                               | 描述                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 指標監視器狀態                    | 此警報指示由監視器狀態腳本確定的指標監視器進程的狀態。                                                                                   |
| 安巴里代理心跳                   | 如果伺服器與代理失去聯繫，將觸發此警報。                                                                                                                        |
| 動物園管理員伺服器流程                 | 如果無法確定 ZooKeeper 伺服器進程在網路上啟動並偵聽，則觸發此主機級警報。                                                               |
| IOCache 中繼資料伺服器狀態           | 如果無法確定 IOCache 中繼資料伺服器是啟動並回應用戶端請求，則會觸發此主機級警報                                                            |
| 日誌節點 Web UI                       | 如果無法訪問 JournalNode Web UI，將觸發此主機級警報。                                                                                                                 |
| Spark2 節儉伺服器                     | 如果無法確定 Spark2 節儉伺服器已啟動，則觸發此主機級警報。                                                                                                |
| 歷史記錄伺服器進程                   | 如果無法將歷史記錄伺服器進程建立為在網路上啟動和偵聽，則觸發此主機級警報。                                                                |
| 歷史記錄伺服器 Web UI                    | 如果無法訪問歷史記錄伺服器 Web UI，將觸發此主機級警報。                                                                                                              |
| `ResourceManager`Web UI                   | 如果無法訪問 Web UI，`ResourceManager`將觸發此主機級警報。                                                                                                             |
| 節點管理器運行狀況摘要               | 如果存在不正常的節點管理器，將觸發此服務等級警報                                                                                                                    |
| 應用時間表 Web UI                      | 如果無法訪問應用時間軸伺服器 Web UI，將觸發此主機級警報。                                                                                                         |
| 資料節點運行狀況摘要                  | 如果存在不正常的資料節點，將觸發此服務等級警報                                                                                                                       |
| 名稱節點 Web UI                          | 如果 NameNode Web UI 無法訪問，將觸發此主機級警報。                                                                                                                    |
| 動物園管理員容錯移轉控制器流程    | 如果無法確認 ZooKeeper 容錯移轉控制器進程在網路上啟動並偵聽，則觸發此主機級警報。                                                   |
| Oozie 伺服器 Web UI                      | 如果無法訪問 Oozie 伺服器 Web UI，將觸發此主機級警報。                                                                                                                |
| Oozie 伺服器狀態                      | 如果無法確定 Oozie 伺服器是啟動並回應用戶端請求，則觸發此主機級警報。                                                                      |
| 蜂巢元存儲流程                   | 如果無法確定 Hive Metastore 進程在網路上啟動並偵聽，則觸發此主機級警報。                                                                 |
| 蜂巢2進程                      | 如果無法確定 HiveServer 是啟動並回應用戶端請求，則觸發此主機級警報。                                                                        |
| WebHCat 伺服器狀態                    | 如果伺服器狀態不正常，`templeton`將觸發此主機級警報。                                                                                                            |
| 可用的動物園管理員伺服器百分比      | 如果群集中關閉的 ZooKeeper 伺服器數大於配置的關鍵閾值，則觸發此警報。 它聚合動物園守護者過程檢查的結果。     |
| Spark2 利維伺服器                       | 如果無法確定 Livy2 伺服器已啟動，則觸發此主機級警報。                                                                                                        |
| Spark2 歷史記錄伺服器                    | 如果無法確定 Spark2 歷史記錄伺服器已啟動，則觸發此主機級警報。                                                                                               |
| 指標收集器流程                | 如果無法確認指標收集器在配置的埠上啟動並偵聽等於閾值的秒數，則觸發此警報。                                 |
| 指標收集器 - HBase 主流程 | 如果無法確認指標收集器的 HBase 主進程在網路上啟動並偵聽配置的關鍵閾值（以秒為單位），則觸發此警報。 |
| 可用的指標百分比監視器       | 如果指標監視器進程未啟動百分比，並且在網路上偵聽配置的警告和關鍵閾值，則觸發此警報。                             |
| 可用節點管理器的百分比           | 如果群集中的下節點管理器數大於配置的關鍵閾值，則觸發此警報。 它聚合節點管理器流程檢查的結果。        |
| 節點管理器運行狀況                       | 此主機級警報檢查 NodeManager 元件提供的節點運行狀況屬性。                                                                                              |
| 節點管理器 Web UI                       | 如果 NodeManager Web UI 無法訪問，將觸發此主機級警報。                                                                                                                 |
| 名稱節點高可用性運行狀況        | 如果活動 NameNode 或備用 NameNode 未運行，則觸發此服務等級警報。                                                                                     |
| 資料節點流程                         | 如果無法將單個 DataNode 進程建立為在網路上啟動和偵聽，則觸發此主機級警報。                                                         |
| 資料節點 Web UI                          | 如果 DataNode Web UI 無法訪問，將觸發此主機級警報。                                                                                                                    |
| 可用的日誌節點百分比           | 如果群集中的日記節點數量大於配置的關鍵閾值，則觸發此警報。 它聚合日記本節點流程檢查的結果。        |
| 可用資料節點的百分比              | 如果群集中的資料節點的向下資料節點數大於配置的關鍵閾值，則觸發此警報。 它聚合 DataNode 過程檢查的結果。              |
| 澤佩林伺服器狀態                   | 如果無法確定 Zeppelin 伺服器是啟動並回應用戶端請求，則觸發此主機級警報。                                                                   |
| 蜂巢2互動式流程          | 如果無法確定 HiveServerInteractive 是啟動並回應用戶端請求，則觸發此主機級警報。                                                             |
| LLAP 應用程式                         | 如果無法確定 LLAP 應用程式已啟動並回應請求，則觸發此警報。                                                                                    |

您可以選取每個服務來檢視其詳細資訊。

雖然服務頁提供有關每個服務的狀態和配置的資訊，但它不提供有關服務運行的主管節點的資訊。 若要檢視這項資訊，請使用在頁面頂端的 [主機] **** 連結。 此頁面會顯示叢集內的主機，包括前端節點在內。

![阿帕奇安巴里頭節點主機清單](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

選取其中一個前端節點的連結會顯示該節點上執行的服務與元件。

![阿帕奇安巴里元件狀態](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

如需有關使用 Ambari 的詳細資訊，請參閱[使用 Apache Ambari Web UI 來監視和管理 HDInsight](hdinsight-hadoop-manage-ambari.md)。

### <a name="ambari-rest-api"></a>Ambari REST API

Ambari REST API 可透過網際網路提供。 HDInsight 公用閘道器會處理路由要求，將其傳送到目前裝載 REST API 的前端節點。

您可以使用下列命令透過 Ambari REST API 來檢查服務的狀態：

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* 將**PASSWORD**替換為 HTTP 使用者（管理員）帳戶密碼。
* 將**CLUSTERNAME**替換為群集的名稱。
* 將 **SERVICENAME** 取代為您要檢查其狀態的服務名稱。

例如，若要檢查在名為 **mycluster** 的叢集上的 **HDFS** 服務狀態，其中密碼是 **password**，您可使用下列命令：

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

回應如下列 JSON 所示：

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

URL 告訴我們，該服務目前運行在名為**mycluster.wutj3h4ic1zejluqhxqq0g**的頭節點上運行。

該狀態會告訴我們此服務目前正在執行，或 **已啟動**。

如果您不知道群集上安裝了哪些服務，則可以使用以下命令檢索清單：

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

如需有關使用 Ambari REST API 的詳細資訊，請參閱[使用 Apache Ambari REST API 來監視和管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md)。

#### <a name="service-components"></a>服務元件

服務可能包含您想要個別檢查狀態的元件。 例如，HDFS 包含 NameNode 元件。 若要檢視在元件上的資訊，該命令為：

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

如果您不知道服務提供哪些元件，則可以使用以下命令檢索清單：

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>如何存取前端節點上的記錄檔

### <a name="ssh"></a>SSH

透過 SSH 連接至前端節點時，可以在 **/var/log**找到記錄檔。 例如， **/var/log/hadoop-yarn/yarn** 包含 YARN 的記錄。

每個前端節點可以有唯一的記錄項目，所以您應該檢查兩者的記錄。

### <a name="sftp"></a>SFTP

您也可以使用 SSH 檔案傳輸通訊協定或安全檔案傳輸通訊協定 (SFTP)，來連線至前端節點，並直接下載記錄檔。

類似於使用 SSH 用戶端，當連接到叢集時，您必須提供 SSH 的使用者帳戶名稱和叢集的 SSH 位址。 例如： `sftp username@mycluster-ssh.azurehdinsight.net` 。 出現提示時，請提供帳戶密碼或使用 `-i` 參數提供公開金鑰。

連接後，將顯示提示`sftp>`。 您可以從該提示變更目錄、上傳和下載檔案。 例如：下列命令會將目錄變更至 **/var/log/hadoop/hdfs** 目錄，然後在目錄中下載所有檔案。

    cd /var/log/hadoop/hdfs
    get *

如需可用命令清單，請在 `sftp>` 提示中輸入 `help`。

> [!NOTE]  
> 使用 SFTP 連線時，也提供圖形化介面可讓您以視覺化方式檢視檔案系統。 例如： [MobaXTerm](https://mobaxterm.mobatek.net/) 可讓您使用類似於「Windows 檔案總管」的介面瀏覽檔案系統。

### <a name="ambari"></a>Ambari

> [!NOTE]  
> 若要使用 Ambari 存取記錄檔，您必須使用 SSH 通道。 個別服務的 Web 介面不會在網際網路上公開。 如需使用 SSH 通道的詳細資訊，請參閱[使用 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md)文件。

從 Ambari Web UI 中，選取您想要檢視記錄的服務 (例如，YARN)。 然後使用 [快速連結]**** 來選取要檢視記錄的前端節點。

![使用快速連結檢視記錄](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>如何設定節點大小

只能在叢集建立期間選取節點的大小。 您可以在 [HDInsight 價格頁面](https://azure.microsoft.com/pricing/details/hdinsight/)找到 HDInsight 可用之不同 VM 大小的清單。

建立叢集時，您可以指定節點的大小。 以下資訊提供有關如何使用[Azure 門戶](https://portal.azure.com/)[、Azure PowerShell 模組 Az](/powershell/azureps-cmdlets-docs)和 Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)指定大小的指導：

* **Azure 入口網站**：建立叢集時，您可以設定叢集所使用的節點大小：

    ![可選取節點大小的 [叢集映像建立精靈]](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**：使用[`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)命令時，可以使用`--headnode-size`、`--workernode-size`和`--zookeepernode-size`參數設置頭、輔助角色和 ZooKeeper 節點的大小。

* **Azure PowerShell**：使用[New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) Cmdlet 時，可以使用`-HeadNodeSize`、`-WorkerNodeSize`和`-ZookeeperNodeSize`參數設置頭、輔助角色和 ZooKeeper 節點的大小。

## <a name="next-steps"></a>後續步驟

要瞭解有關本文中討論的專案的更多內容，請參閱：

* [Apache Ambari REST 參考](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [安裝和配置 Azure CLI](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [安裝和配置 Azure PowerShell 模組 Az](/powershell/azure/overview)
* [使用 Apache Ambari 來管理 HDInsight](hdinsight-hadoop-manage-ambari.md)
* [佈建以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
