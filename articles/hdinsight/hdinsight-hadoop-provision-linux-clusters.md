---
title: 使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他工具在 HDInsight 中設定叢集
description: 從瀏覽器、Azure 傳統 CLI、Azure PowerShell、REST 或 SDK 為 HDInsight 設定 Hadoop、Kafka、Spark、HBase、R 伺服器或 Storm 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 04/10/2020
ms.openlocfilehash: ed2fe1736c887fd8aa866c8d0c71a5df0aa4fb31
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232831"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他工具在 HDInsight 中設定叢集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

瞭解如何在 HDInsight 中安裝和設定 Apache Hadoop、Apache Spark、Apache Kafka、互動式查詢、Apache HBase、ML 服務或 Apache Storm。 此外，了解如何自訂叢集，並將叢集加入網域以提升安全性。

Hadoop 叢集由數個虛擬機器 (節點) 組成，可用於分散處理作業。 Azure HDInsight 會處理個別節點所安裝和設定的實作細節，您只需要提供一般設定資訊即可。

> [!IMPORTANT]  
> HDInsight 叢集的計費起自叢集建立時，終至叢集刪除時。 計費是以每分鐘按比例計算，因此不再使用時，請一律刪除您的叢集。 了解如何[刪除叢集。](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>叢集設定方法

下表顯示可用來設定 HDInsight 叢集的不同方法。

| 叢集建立方法 | Web 瀏覽器 | 命令列 | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure 入口網站](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [彎曲](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure 資源管理員範本](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

這篇文章會逐步引導您完成[Azure 入口網站](https://portal.azure.com)中的設定，您可以在其中建立 HDInsight 叢集。

## <a name="basics"></a>基本概念

![hdinsight 建立選項自訂快速](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>專案詳細資料

[Azure Resource Manager](../azure-resource-manager/management/overview.md)可協助您將應用程式中的資源做為群組使用，稱為 Azure[資源群組](../azure-resource-manager/management/overview.md#resource-groups)。 您可以在單一、協調的作業中，將應用程式的所有資源進行部署、更新、監視或刪除。

### <a name="cluster-details"></a>叢集詳細資料

#### <a name="cluster-name"></a>叢集名稱

HDInsight 叢集名稱具有下列限制：

* 允許的字元：a-z、0-9、A-Z
* 最大長度：59
* 保留名稱：apps
* 叢集命名範圍適用於所有 Azure，橫跨所有訂用帳戶。 因此，叢集名稱在全球必須是唯一的。
* 虛擬網路中的前六個字元必須是唯一的

#### <a name="region"></a>區域

不需明確指定叢集位置：叢集位於和預設儲存體相同的位置。 如需支援的區域清單，請選取 [ [HDInsight 定價](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)] 上的 [**區域**] 下拉式清單。

#### <a name="cluster-type"></a>叢集類型

Azure HDInsight 目前提供下列的叢集類型，每種都有一組提供特定功能的元件。

> [!IMPORTANT]  
> HDInsight 叢集有多種類型，每種類型各適合單一工作負載或技術。 沒有任何支援方法可建立結合多個類型的叢集，例如在一個叢集上並存 Storm 和 HBase。 如果您的解決方案需要會分散到多個 HDInsight 叢集類型的技術，[Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network)可以連接必要的叢集類型。

| 叢集類型 | 功能 |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |批次查詢和分析儲存的資料 |
| [hbase](hbase/apache-hbase-overview.md) |處理大量無綱要的 NoSQL 資料 |
| [互動式查詢](./interactive-query/apache-interactive-query-get-started.md) |更快速之互動式 Hive 查詢的記憶體內快取 |
| [Kafka](kafka/apache-kafka-introduction.md) | 可用來建置即時串流資料管線和應用程式的分散式串流平台 |
| [ML 服務](r-server/r-server-overview.md) |各種巨量資料統計資料、預測模型和機器學習功能 |
| [Spark](spark/apache-spark-overview.md) |記憶體內處理、互動式查詢、微批次串流處理 |
| [Storm](storm/apache-storm-overview.md) |即時事件處理 |

#### <a name="version"></a>版本

選擇此叢集的 HDInsight 版本。 如需詳細資訊，請參閱[支援的 HDInsight 版本](hdinsight-component-versioning.md#supported-hdinsight-versions)。

### <a name="cluster-credentials"></a>叢集認證

使用 HDInsight 叢集，您可以在建立叢集期間設定兩個使用者帳戶：

* 叢集登入使用者名稱：預設使用者名稱為*admin*。它會使用 Azure 入口網站上的基本設定。 有時稱為「叢集使用者」或「HTTP 使用者」。
* 安全殼層（SSH）使用者名稱：用來透過 SSH 連接到叢集。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

HTTP 使用者名稱具有下列限制：

* 允許的特殊字元`_` ：和`@`
* 不允許的字元： #;。 "'\/，： '！ *？ $ （{}） [] <>|&--= +% ~ ^ space
* 最大長度：20

SSH 使用者名稱具有下列限制：

* 允許的特殊字元`_` ：和`@`
* 不允許的字元： #;。 "'\/，： '！ *？ $ （{}） [] <>|&--= +% ~ ^ space
* 最大長度：64
* 保留名稱： hadoop、使用者、oozie、hive、mapred.max.split.size、ambari-qa、zookeeper、tez、hdfs、sqoop，yarn，hcat，ams，hbase，風暴，管理員，管理員，使用者，user1，測試，使用者2，test1，user3，admin1，1，123，a，actuser，adm，admin2，aspnet，備份，主控台，david，來賓，john，，support_388945a0，sys，test2，test3，user4，user5，spark

## <a name="storage"></a>儲存體

![叢集儲存設定： HDFS 相容端點](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

內部部署安裝的 Hadoop 叢集使用 Hadoop 分散式檔案系統 (HDFS) 作為叢集上的儲存體，但在雲端中，您可以使用已連接到叢集的儲存體端點。 使用雲端儲存體表示您可以安全地刪除用於計算的 HDInsight 叢集，同時仍保留您的資料。

HDInsight 叢集可以使用下列儲存體選項：

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure 儲存體一般用途 v2
* Azure 儲存體一般用途 v1
* Azure 儲存體區塊 blob （**僅支援做為次要儲存體**）

如需 HDInsight 儲存選項的詳細資訊，請參閱[比較與 Azure HDInsight 叢集搭配使用的儲存體選項](hdinsight-hadoop-compare-storage-options.md)。

> [!WARNING]  
> 不支援在與 HDInsight 叢集不同的位置中使用其他儲存體帳戶。

在設定期間，您要為預設儲存體端點指定 Azure 儲存體帳戶的 Blob 容器或 Data Lake Storage。 預設儲存體包含應用程式與系統記錄。 您也可以選擇指定叢集可存取的其他已連結 Azure 儲存體帳戶和 Data Lake Storage 帳戶。 HDInsight 叢集與相依的儲存體帳戶必須位於相同的 Azure 位置。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> 在建立叢集之後啟用安全儲存體傳輸，可能會導致使用您的儲存體帳戶發生錯誤，因此不建議您這麼做。 最好是使用已啟用安全傳輸的儲存體帳戶來建立新的叢集。

### <a name="metastore-settings"></a>中繼存放區設定

您可以建立選擇性的 Hive 或 Apache Oozie 中繼存放區。 不過，並非所有叢集類型都支援中繼存放區，且 Azure SQL 資料倉儲不相容於中繼存放區。

如需詳細資訊，請參閱[在 Azure HDInsight 中使用外部中繼資料存放區](./hdinsight-use-external-metadata-stores.md)。

> [!IMPORTANT]  
> 在建立自訂中繼存放區時，資料庫名稱請勿使用破折號、連字號或空格。 這可能會導致叢集建立程序失敗。

#### <a name="sql-database-for-hive"></a>適用于 Hive 的 SQL 資料庫

如果想要在刪除 HDInsight 叢集之後保留 Hive 資料表，請使用自訂的中繼存放區。 您可以接著將中繼存放區附加至另一個 HDInsight 叢集。

針對某個 HDInsight 叢集版本建立的 HDInsight 中繼存放區，不能在不同的 HDInsight 叢集版本之間共用。 如需 HDInsight 版本清單，請參閱[支援的 HDInsight 版本](hdinsight-component-versioning.md#supported-hdinsight-versions)。

#### <a name="sql-database-for-oozie"></a>適用于 Oozie 的 SQL database

為提升使用 Oozie 時的效能，請使用自訂的中繼存放區。 在您刪除叢集後，中繼存放區也可提供 Oozie 作業資料的存取。

#### <a name="sql-database-for-ambari"></a>適用于 Ambari 的 SQL database

Ambari 是用來監視 HDInsight 叢集、進行設定變更，以及儲存叢集管理資訊以及作業歷程記錄。 自訂 Ambari DB 功能可讓您在管理的外部資料庫中部署新的叢集，並設定 Ambari。 如需詳細資訊，請參閱[自訂 AMBARI DB](./hdinsight-custom-ambari-db.md)。

> [!IMPORTANT]  
> 您無法重複使用自訂的 Oozie 中繼存放區。 若要使用自訂的 Oozie 中繼存放區，您必須在建立 HDInsight 叢集時提供空的 Azure SQL Database。

## <a name="security--networking"></a>安全性 + 網路

![hdinsight 建立選擇企業安全性套件的選項](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>企業安全性套件

對於 Hadoop、Spark、HBase、Kafka 互動式查詢叢集類型，您可以選擇啟用 [企業安全性套件]****。 此套件使用 Apache Ranger 並與 Azure Active Directory 整合，讓您可選擇更安全的叢集設定。 如需詳細資訊，請參閱[Azure HDInsight 中的企業安全性總覽](./domain-joined/hdinsight-security-overview.md)。

企業安全性套件可讓您整合 HDInsight 與 Active Directory 及 Apache Ranger。 使用企業安全性套件可以建立多個使用者。

如需建立已加入網域之 HDInsight 叢集的詳細資訊，請參閱[建立已加入網域的 HDInsight 沙箱環境](./domain-joined/apache-domain-joined-configure.md)。

### <a name="tls"></a>TLS

如需詳細資訊，請參閱[傳輸層安全性](./transport-layer-security.md)

### <a name="virtual-network"></a>虛擬網路

如果您的解決方案需要會分散到多個 HDInsight 叢集類型的技術，[Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network)可以連接必要的叢集類型。 此組態可讓叢集以及其中部署的任何程式碼直接彼此通訊。

如需搭配 HDInsight 使用 Azure 虛擬網路的詳細資訊，請參閱[規劃 hdinsight 的虛擬網路](hdinsight-plan-virtual-network-deployment.md)。

如需在 Azure 虛擬網路內使用兩個叢集類型的範例，請參閱[使用 Apache Spark 結構化串流搭配 Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md)。 如需搭配虛擬網路使用 HDInsight 的詳細資訊，包括虛擬網路的特定設定需求，請參閱[規劃 HDInsight 的虛擬網路](hdinsight-plan-virtual-network-deployment.md)。

### <a name="disk-encryption-setting"></a>磁片加密設定

如需詳細資訊，請參閱[客戶管理的金鑰磁片加密](./disk-encryption.md)。

### <a name="kafka-rest-proxy"></a>Kafka REST Proxy

此設定僅適用于叢集類型 Kafka。 如需詳細資訊，請參閱[使用 REST proxy](./kafka/rest-proxy.md)。

### <a name="identity"></a>身分識別

如需詳細資訊，請參閱[Azure HDInsight 中的受控](./hdinsight-managed-identities.md)識別。

## <a name="configuration--pricing"></a>設定 + 定價

![HDInsight 選擇您的節點大小](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

只要叢集存在，您就必須支付節點使用量的費用。 建立叢集後就開始計費，並在叢集刪除後停止計費。 叢集無法解除配置或暫停。

### <a name="node-configuration"></a>節點組態

每個叢集類型都有自己的節點數目、節點術語和預設 VM 大小。 下表中各節點類型的節點數目位於括號中。

| 類型 | 節點 | 圖表 |
| --- | --- | --- |
| Hadoop |前端節點（2）、背景工作節點（1 +） |![HDInsight Hadoop 叢集節點](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| hbase |前端伺服器 (2)、區域伺服器 (1+)、主要/Zookeeper 節點 (3) |![HDInsight HBase 叢集類型設定](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus 節點 (2)、監督員伺服器 (1+)、Zookeeper 節點 (3) |![HDInsight 風暴叢集類型設定](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |前端節點（2）、背景工作節點（1 +）、ZooKeeper 節點（3）（A1 ZooKeeper VM 大小免費） |![HDInsight spark 叢集類型設定](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

如需詳細資訊，請參閱＜HDInsight 中的 Hadoop 元件和版本是什麼？＞中的[叢集的預設節點設定和虛擬機器大小](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)。

HDInsight 叢集的成本是由節點數和節點的虛擬機器大小來決定。

不同的叢集類型具有不同的節點類型、節點數目和節點大小：
* Hadoop 叢集類型的預設值：
    * 兩個*前端節點*  
    * 四個背景*工作節點*
* Storm 叢集類型的預設值：
    * 兩個 *Nimbus 節點*
    * 三個 *ZooKeeper 節點*
    * 四個*監督員節點*

如果您只是想要試用 HDInsight，建議您使用一個背景工作節點。 如需 HDInsight 價格的詳細資訊，請參閱[hdinsight 定價](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)。

> [!NOTE]  
> 叢集大小限制會隨著 Azure 訂用帳戶而有所不同。 若要提高限制，請與 [Azure 帳務支援人員](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)連絡。

當您使用 Azure 入口網站設定叢集時，可以透過 [設定 **+ 定價**] 索引標籤取得節點大小。在入口網站中，您也可以查看與不同節點大小相關聯的成本。

### <a name="virtual-machine-sizes"></a>虛擬機器大小

當您部署叢集時，請依據計劃部署的解決方案選擇計算資源。 下列 VM 可用於 HDInsight 叢集：

* A 和 D1-4 系列 VM：[一般用途 Linux VM 大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11-14 系列 VM：[記憶體最佳化 Linux VM 大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

如需使用不同的 SDK 或使用 Azure PowerShell 建立叢集時應用來指定 VM 大小的值，請參閱[使用於 HDInsight 叢集的 VM 大小](../cloud-services/cloud-services-sizes-specs.md#size-tables)。 在此連結的文件中，請使用資料表中 **Size (大小)** 資料行的值。

> [!IMPORTANT]  
> 如果您在叢集中需要超過32個背景工作節點，則必須選取具有至少8個核心和 14 GB RAM 的前端節點大小。

如需相關資訊，請參閱[虛擬機器的大小](../virtual-machines/windows/sizes.md)。 如需各式大小的價格資訊，請參閱 [HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight)。

### <a name="add-application"></a>新增應用程式

HDInsight 應用程式是使用者可以在以 Linux 為基礎的 HDInsight 叢集上安裝的應用程式。 您可以使用由 Microsoft、協力廠商所提供或您自己開發的應用程式。 如需詳細資訊，請參閱[在 Azure HDInsight 上安裝第三方 Apache Hadoop 應用程式](hdinsight-apps-install-applications.md)。

大部分的 HDInsight 應用程式會安裝在空白的邊緣節點。  空白的邊緣節點是一部 Linux 虛擬機器，其中已安裝及設定和前端節點相同的用戶端工具。 您可以使用邊緣節點來存取叢集、測試用戶端應用程式，以及裝載用戶端應用程式。 如需詳細資訊，請參閱 [Use empty edge nodes in HDInsight (在 HDInsight 中使用空白的邊緣節點)](hdinsight-apps-use-edge-node.md)。

### <a name="script-actions"></a>指令碼動作

您可以在建立期間使用指令碼來安裝其他元件或自訂組態。 這類指令碼可透過 **指令碼動作**叫用，指令碼動作是一個組態選項，其可從 Azure 入口網站、HDInsight Windows PowerShell Cmdlet 或 HDInsight .NET SDK 使用。 如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

您可以使用 JAVA 封存 (JAR) 檔案形式在叢集上執行一些原生 JAVA 元件 (例如 Apache Mahout 和 Cascading)。 這些 JAR 檔案可以配送至 Azure 儲存體，並透過 Hadoop 作業提交機制提交至 HDInsight 叢集。 如需詳細資訊，請參閱[以程式設計方式提交 Apache Hadoop 作業](hadoop/submit-apache-hadoop-jobs-programmatically.md)。

> [!NOTE]  
> 如果您在將 JAR 檔案部署至 HDInsight 叢集，或在 HDInsight 叢集上呼叫 JAR 檔案時發生問題，請連絡 [Microsoft 支援](https://azure.microsoft.com/support/options/)。
>
> Cascading 不受 HDInsight 支援，而且不符合「Microsoft 支援」的資格。 如需支援的元件清單，請參閱[HDInsight 所提供叢集版本的新功能](hdinsight-component-versioning.md)。

有時候，您可能要在建立程序期間設定下列組態檔：

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

如需詳細資訊，請參閱 [使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Azure HDInsight 疑難排解叢集建立失敗](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [什麼是 HDInsight、Apache Hadoop 生態系統以及 Hadoop 叢集？](hadoop/apache-hadoop-introduction.md)
* [開始使用 HDInsight 中的 Apache Hadoop](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [從 Windows PC 在 HDInsight 上的 Hadoop 生態系統中作業](hdinsight-hadoop-windows-tools.md)
