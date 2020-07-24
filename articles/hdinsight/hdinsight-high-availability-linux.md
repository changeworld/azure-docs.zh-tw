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
ms.openlocfilehash: f97b4877d87acec923812fd0289d5161529dbe7a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081027"
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

若要取得叢集中不同節點類型的主機名稱，請使用[Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)。

> [!IMPORTANT]  
> 請勿將數值與節點 (不論是主要或次要) 相關聯。 數值只是用來為每個節點提供唯一名稱。

### <a name="nimbus-nodes"></a>Nimbus 節點

Nimbus 節點是 Apache Storm 叢集隨附的節點。 Nimbus 節點會透過在背景工作節點之間散佈並監視處理，來提供 Hadoop jobtracker 類似的功能。 HDInsight 為 Storm 叢集提供兩個 Nimbus 節點

### <a name="apache-zookeeper-nodes"></a>Apache ZooKeeper 節點

[ZooKeeper](https://zookeeper.apache.org/) 節點用於前端節點上主要服務的前置選擇。 它們也可用來確保服務、資料（背景工作）節點和閘道知道主要服務在哪一個前端節點上為作用中狀態。 根據預設，HDInsight 會提供三個 ZooKeeper 節點。

### <a name="worker-nodes"></a>背景工作節點

當作業提交至叢集時，背景工作節點會執行實際的資料分析。 如果背景工作節點失敗，它所執行的工作將會提交至另一個背景工作節點。 根據預設，HDInsight 會建立四個背景工作節點。 不過，您可以視需要在叢集建立期間和之後變更該數字。

### <a name="edge-node"></a>邊緣節點

邊緣節點不會主動參與叢集內的資料分析。 開發人員或資料科學家在使用 Hadoop 時，會使用它。 邊緣節點和叢集中的其他節點一樣，存在於相同的 Azure 虛擬網路中，也可直接存取其他所有節點。 使用邊緣節點時，不需從關鍵 Hadoop 服務或分析作業取走資源。

目前，HDInsight 上的 ML 服務是唯一可依預設提供邊緣節點的叢集類型。 對於 HDInsight 上的 ML 服務來說，邊緣節點是用來在提交至叢集進行分散式處理之前，在本機節點上測試 R 程式碼使用。

如需有關將邊緣節點搭配其他叢集類型使用的相關資訊，請參閱[在 HDInsight 中使用邊緣節點](hdinsight-apps-use-edge-node.md)文件。

## <a name="accessing-the-nodes"></a>存取節點

您可以透過公用閘道，經由網際網路存取叢集。 存取權僅限於連接到前端節點，如果有的話，則會有邊緣節點。 在前端節點上執行之服務的存取不會受到具有多個前端節點的影響。 公用閘道會將要求路由至託管已要求服務的前端節點。 例如，如果 Apache Ambari 目前裝載在次要前端節點上，閘道就會將針對 Ambari 的連入要求路由傳送至該節點。

公用網關的存取權僅限於埠443（HTTPS）、22和23。

|連接埠 |描述 |
|---|---|
|443|用來存取裝載于前端節點上的 Ambari 和其他 web UI 或 REST Api。|
|22|用來以 SSH 存取主要前端節點或邊緣節點。|
|23|用來存取具有 SSH 的次要前端節點。 例如，`ssh username@mycluster-ssh.azurehdinsight.net` 會連線到名為 **mycluster** 之叢集的主要前端節點。|

如需使用 SSH 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md) 文件。

### <a name="internal-fully-qualified-domain-names-fqdn"></a>內部完整網域名稱 (FQDN)

HDInsight 叢集中的節點具有只能自叢集存取的內部 IP 位址和 FQDN。 使用內部 FQDN 或 IP 位址存取叢集上的服務時，您應該使用 Ambari 來驗證要存取服務時所使用的 IP 或 FQDN。

例如，Apache Oozie 服務只能在一個前端節點上執行，而從 SSH 工作階段使用 `oozie` 命令則需要有該服務的 URL。 這個 URL 可以使用下列命令從 Ambari 擷取：

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

此命令會傳回類似下列的值，其中包含要搭配命令使用的內部 URL `oozie` ：

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

如需有關使用 Ambari REST API 的詳細資訊，請參閱[使用 Apache Ambari REST API 來監視和管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md)。

### <a name="accessing-other-node-types"></a>存取其他節點類型

您可以使用下列方法，連接到無法直接透過網際網路存取的節點：

|方法 |說明 |
|---|---|
|SSH|使用 SSH 連線到前端節點之後，您便可以接著從前端節點使用 SSH 來連線到叢集中的其他節點。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md) 文件。|
|SSH 通道|如果您需要存取裝載在其中一個節點上的 web 服務，但未公開到網際網路，您必須使用 SSH 通道。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md)文件。|
|Azure 虛擬網路|如果您的 HDInsight 叢集是「Azure 虛擬網路」的一部分，則任何在相同「虛擬網路」上的資源都可直接存取該叢集內的所有節點。 如需詳細資訊，請參閱[規劃 HDInsight 的虛擬網路](hdinsight-plan-virtual-network-deployment.md)檔。|

## <a name="how-to-check-on-a-service-status"></a>如何檢查服務狀態

若要檢查在前端節點執行的服務狀態，請使用 Ambari Web UI 或 Ambari REST API。

### <a name="ambari-web-ui"></a>Ambari Web UI

可以在 `https://CLUSTERNAME.azurehdinsight.net` 檢視 Ambari Web UI。 將**CLUSTERNAME**取代為您的叢集名稱。 如果出現提示，請輸入叢集的 HTTP 使用者認證。 預設 HTTP 使用者名稱為 **admin** ，密碼是您在建立叢集時輸入的密碼。

當您來到 Ambari 頁面上時，會在該頁面的左邊列出已安裝的服務。

![Apache Ambari 已安裝的服務](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

服務旁可能會出現一系列圖示以表示狀態。 可以使用在頁面頂端的 [警示] **** 連結，檢視與服務相關的任何警示。  Ambari 提供數個預先定義的警示。

下列警示可協助監視叢集的可用性：

| 警示名稱                               | 描述                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 度量監視狀態                    | 此警示會指出計量監視器進程的狀態，由監視狀態腳本決定。                                                                                   |
| Ambari 代理程式的心跳                   | 如果伺服器已失去與代理程式的連線，就會觸發此警示。                                                                                                                        |
| ZooKeeper 伺服器進程                 | 如果無法判斷 ZooKeeper 伺服器進程是否已啟動並在網路上接聽，就會觸發此主機層級警示。                                                               |
| IOCache 中繼資料伺服器狀態           | 如果無法判斷 IOCache 的中繼資料伺服器是否已啟動並回應用戶端要求，就會觸發此主機層級警示。                                                            |
| JournalNode Web UI                       | 如果無法連線到 JournalNode Web UI，就會觸發此主機層級警示。                                                                                                                 |
| Spark2 Thrift 伺服器                     | 如果無法判斷 Spark2 Thrift 伺服器是否已啟動，則會觸發此主機層級警示。                                                                                                |
| 歷程記錄伺服器進程                   | 如果無法建立記錄伺服器進程以在網路上啟動及接聽，就會觸發此主機層級警示。                                                                |
| 歷程記錄伺服器 Web UI                    | 如果無法連線到歷程記錄伺服器 Web UI，就會觸發此主機層級警示。                                                                                                              |
| `ResourceManager`Web UI                   | 如果無法連線到 Web UI，就會觸發此主機層級警示 `ResourceManager` 。                                                                                                             |
| NodeManager 健全狀況摘要               | 如果有狀況不良的 NodeManagers，就會觸發此服務層級警示                                                                                                                    |
| 應用程式時間軸 Web UI                      | 如果無法連線到應用程式時間軸伺服器 Web UI，就會觸發此主機層級警示。                                                                                                         |
| DataNode 健全狀況摘要                  | 如果有狀況不良的 Datanode，就會觸發此服務層級警示                                                                                                                       |
| NameNode Web UI                          | 如果無法連線到 NameNode Web UI，就會觸發此主機層級警示。                                                                                                                    |
| ZooKeeper 容錯移轉控制器進程    | 如果無法確認 ZooKeeper 容錯移轉控制器進程是否已啟動並在網路上接聽，就會觸發此主機層級警示。                                                   |
| Oozie 伺服器 Web UI                      | 如果無法連線到 Oozie 伺服器 Web UI，就會觸發此主機層級警示。                                                                                                                |
| Oozie 伺服器狀態                      | 如果無法判斷 Oozie 伺服器是否已啟動並回應用戶端要求，就會觸發此主機層級警示。                                                                      |
| Hive 中繼存放區進程                   | 如果無法判斷 Hive 中繼存放區進程在網路上啟動和接聽，就會觸發此主機層級警示。                                                                 |
| HiveServer2 流程                      | 如果無法判斷 HiveServer 是否已啟動並回應用戶端要求，就會觸發此主機層級警示。                                                                        |
| WebHCat 伺服器狀態                    | 如果伺服器狀態狀況不良，則會觸發此主機層級警示 `templeton` 。                                                                                                            |
| 可用的 ZooKeeper 伺服器百分比      | 如果叢集中的下層 ZooKeeper 伺服器數目大於設定的重大閾值，就會觸發此警示。 它會匯總 ZooKeeper 流程檢查的結果。     |
| Spark2 Livy 伺服器                       | 如果無法判斷 Livy2 伺服器是否已啟動，則會觸發此主機層級警示。                                                                                                        |
| Spark2 歷程記錄伺服器                    | 如果無法判斷 Spark2 歷程記錄伺服器是否已啟動，就會觸發此主機層級警示。                                                                                               |
| 計量收集器進程                | 如果無法確認計量收集器已啟動，且在設定的埠上接聽的秒數等於閾值，就會觸發此警示。                                 |
| 計量收集器-HBase Master 進程 | 如果無法確認計量收集器的 HBase 主要進程在網路上啟動並接聽已設定的重大臨界值（以秒為單位），就會觸發此警示。 |
| 可用的計量監視百分比       | 如果計量監視進程的百分比未啟動，並在網路上接聽已設定的警告和重大臨界值，就會觸發此警示。                             |
| 可用的 NodeManagers 百分比           | 如果叢集中的關閉 NodeManagers 數目大於設定的重大閾值，就會觸發此警示。 它會匯總 NodeManager 流程檢查的結果。        |
| NodeManager 健全狀況                       | 此主機層級警示會檢查 NodeManager 元件中可用的節點健全狀況屬性。                                                                                              |
| NodeManager Web UI                       | 如果無法連線到 NodeManager Web UI，就會觸發此主機層級警示。                                                                                                                 |
| NameNode 高可用性健全狀況        | 如果作用中的 NameNode 或待命 NameNode 不在執行中，就會觸發此服務層級警示。                                                                                     |
| DataNode 流程                         | 如果無法建立個別 DataNode 進程以在網路上啟動及接聽，則會觸發此主機層級警示。                                                         |
| DataNode Web UI                          | 如果無法連線到 DataNode Web UI，就會觸發此主機層級警示。                                                                                                                    |
| 可用的 JournalNodes 百分比           | 如果叢集中的關閉 JournalNodes 數目大於設定的重大閾值，就會觸發此警示。 它會匯總 JournalNode 流程檢查的結果。        |
| 可用的 Datanode 百分比              | 如果叢集中的關閉 Datanode 數目大於設定的重大閾值，就會觸發此警示。 它會匯總 DataNode 流程檢查的結果。              |
| Zeppelin 伺服器狀態                   | 如果無法判斷 Zeppelin 伺服器是否已啟動並回應用戶端要求，就會觸發此主機層級警示。                                                                   |
| HiveServer2 互動式進程          | 如果無法判斷 HiveServerInteractive 是否已啟動並回應用戶端要求，就會觸發此主機層級警示。                                                             |
| LLAP 應用程式                         | 如果無法判斷 LLAP 應用程式是否已啟動並回應要求，就會觸發此警示。                                                                                    |

您可以選取每個服務來檢視其詳細資訊。

雖然服務頁面提供每個服務的狀態和設定資訊，但它並不會提供服務執行所在之前端節點的相關資訊。 若要檢視這項資訊，請使用在頁面頂端的 [主機] **** 連結。 此頁面會顯示叢集內的主機，包括前端節點在內。

![Apache Ambari 前端節點主機清單](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

選取其中一個前端節點的連結會顯示該節點上執行的服務與元件。

![Apache Ambari 元件狀態](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

如需有關使用 Ambari 的詳細資訊，請參閱[使用 Apache Ambari Web UI 來監視和管理 HDInsight](hdinsight-hadoop-manage-ambari.md)。

### <a name="ambari-rest-api"></a>Ambari REST API

Ambari REST API 可透過網際網路提供。 HDInsight 公用閘道器會處理路由要求，將其傳送到目前裝載 REST API 的前端節點。

您可以使用下列命令透過 Ambari REST API 來檢查服務的狀態：

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* 將**password**取代為 HTTP 使用者（系統管理員）帳戶密碼。
* 將**CLUSTERNAME**取代為叢集的名稱。
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

此 URL 會告訴我們服務目前正在名為 mycluster 的前端節點上執行。 **wutj3h4ic1zejluqhxzvckxq0g**。

該狀態會告訴我們此服務目前正在執行，或 **已啟動**。

如果您不知道叢集上安裝了哪些服務，可以使用下列命令來取出清單：

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

如需有關使用 Ambari REST API 的詳細資訊，請參閱[使用 Apache Ambari REST API 來監視和管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md)。

#### <a name="service-components"></a>服務元件

服務可能包含您想要個別檢查狀態的元件。 例如，HDFS 包含 NameNode 元件。 若要檢視在元件上的資訊，該命令為：

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

如果您不知道服務提供了哪些元件，您可以使用下列命令來取出清單：

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

連接之後，您會看到提示字元 `sftp>` 。 您可以從該提示變更目錄、上傳和下載檔案。 例如：下列命令會將目錄變更至 **/var/log/hadoop/hdfs** 目錄，然後在目錄中下載所有檔案。

```bash
cd /var/log/hadoop/hdfs
get *
```

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

建立叢集時，您可以指定節點的大小。 下列資訊提供如何使用 [ [Azure 入口網站](https://portal.azure.com/)]、[ [Azure PowerShell 模組 Az](/powershell/azure/)] 和 [ [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)] 來指定大小的指引：

* **Azure 入口網站**：建立叢集時，您可以設定叢集所使用的節點大小：

    ![可選取節點大小的 [叢集映像建立精靈]](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**：使用命令時 [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) ，您可以使用 `--headnode-size` 、和參數來設定 Head、worker 和 ZooKeeper 節點的大小 `--workernode-size` `--zookeepernode-size` 。

* **Azure PowerShell**：使用 new-azhdinsightcluster 指令[程式](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)時，您可以使用 `-HeadNodeSize` 、和參數來設定 head、worker 和 ZooKeeper 節點的大小 `-WorkerNodeSize` `-ZookeeperNodeSize` 。

## <a name="next-steps"></a>後續步驟

若要深入瞭解本文中所討論的專案，請參閱：

* [Apache Ambari REST 參考](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [安裝和設定 Azure CLI](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [安裝和設定 Azure PowerShell 模組 Az](/powershell/azure/)
* [使用 Apache Ambari 來管理 HDInsight](hdinsight-hadoop-manage-ambari.md)
* [佈建以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
