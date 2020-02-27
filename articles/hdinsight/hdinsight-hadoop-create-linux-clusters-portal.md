---
title: 使用網頁瀏覽器建立 Apache Hadoop 叢集，Azure HDInsight
description: 瞭解如何使用網頁瀏覽器和 Azure 入口網站，在 Linux 上為 HDInsight 建立 Apache Hadoop、Apache HBase、Apache Storm 或 Apache Spark 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/19/2020
ms.openlocfilehash: bcc71b7244ba3498b2fcde023d372e67f41d6117
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623228"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>使用 Azure 入口網站在 HDInsight 中建立 Linux 型叢集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 入口網站是 Web 架構的管理工具，可用來管理裝載於 Microsoft Azure 雲端中的服務和資源。 在本文中，您會了解如何使用入口網站來建立 Linux 型 Azure HDInsight 叢集。 其他詳細資料可從[建立 HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)叢集取得。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure 入口網站會公開大部分的叢集屬性。 藉由使用 Azure Resource Manager 範本，您可以隱藏許多詳細資料。 如需詳細資訊，請參閱[使用 Resource Manager 範本在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-clusters"></a>建立叢集

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 從頂端功能表中，選取 [ **+ 建立資源**]。

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "在 Azure 入口網站中建立新的叢集")

1. 選取 [**分析** > ] **Azure HDInsight**移至 [**建立 HDInsight**叢集] 頁面。

## <a name="basics"></a>基本概念

![HDInsight 建立叢集基本概念](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "在 Azure 入口網站中建立新的叢集")

從 [**基本**] 索引標籤中，提供下列資訊：

|屬性 |描述 |
|---|---|
|訂用帳戶|從下拉式清單中，選取用於叢集的 Azure 訂用帳戶。|
|資源群組|從下拉式清單中選取現有的資源群組，或選取 **[新建]。**|
|叢集名稱|輸入全域唯一名稱。|
|區域|從下拉式清單中，選取要建立叢集的區域。|
|叢集類型|選取 [**選取叢集類型**] 以開啟清單。 從清單中選取所需的叢集類型。 HDInsight 叢集有各種不同類型。 它們與調整叢集時所針對的工作負載或技術對應。 目前沒有任何支援的方法可建立結合多個類型的叢集。|
|版本|從下拉式清單中選取**版本**。 如果您不知道要選擇哪一個項目，請使用預設版本。 如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。|
|叢集登入使用者名稱|提供使用者名稱，預設值為**admin**。|
|叢集登入密碼|提供密碼。|
|確認叢集登入密碼|重新輸入密碼|
|安全殼層 (SSH) 使用者名稱|提供使用者名稱，預設值為**sshuser**|
|將叢集登入密碼用於 SSH|如果您想要與您稍早指定的系統管理員密碼相同的 SSH 密碼，請選取 [**使用 ssh**的叢集登入密碼] 核取方塊。 如果不要，請提供 [密碼] 或 [公開金鑰]來驗證 SSH 使用者。 我們建議使用公開金鑰。 選擇底部的 [選取] 以儲存認證設定。  如需詳細資訊，請參閱[使用 SSH 連線到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。|

選取 **[下一步]：儲存體 > >** 繼續前往下一個索引標籤。

## <a name="storage"></a>儲存體

![HDInsight 建立叢集儲存體](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "在 Azure 入口網站儲存體中建立新的叢集")

### <a name="primary-storage"></a>主要儲存體

從 [**主要儲存體類型**] 的下拉式清單中，選取您的預設儲存體類型。 後續要完成的欄位會根據您的選擇而有所不同。 針對**Azure 儲存體**：

1. 針對 [**選取方法**]，選擇 [**從清單選取**] 或 [**使用存取金鑰**]。
    * 針對 [**從清單選取**]，然後從下拉式清單中選取您的**主要儲存體帳戶**，或選取 **[新建]。**
    * 針對 [**使用存取金鑰**]，輸入您的**儲存體帳戶名稱**。 然後提供**存取金鑰**。

1. 針對 [**容器**]，接受預設值，或輸入新的值。

### <a name="additional-azure-storage"></a>其他 Azure 儲存體

選擇性：選取 **新增 Azure 儲存體**以取得其他叢集存放裝置。 不支援在與 HDInsight 叢集不同的區域中使用其他儲存體帳戶。

### <a name="metastore-settings"></a>中繼存放區設定

選擇性：指定現有的 SQL Database 以儲存叢集以外的 Apache Hive、Apache Oozie 和/或 Apache Ambari 中繼資料。 用於中繼存放區的 Azure SQL Database 必須允許連接到其他 Azure 服務，包括 Azure HDInsight。 當您建立中繼存放區時，請勿使用包含破折號或連字號的資料庫名稱。 這些字元可能會導致叢集建立程序失敗。

選取 **[下一步]： [安全性 + 網路] > >** 繼續前往下一個索引標籤。

## <a name="security--networking"></a>安全性 + 網路

![HDInsight 建立叢集安全性網路功能](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight 建立叢集安全性網路功能")

從 [**安全性 + 網路**] 索引標籤中，提供下列資訊：

|屬性 |描述 |
|---|---|
|企業安全性套件|選擇性：選取要使用**企業安全性套件**的核取方塊。 如需詳細資訊，請參閱[使用 Azure Active Directory Domain Services 設定具有企業安全性套件的 HDInsight](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)叢集。|
|TLS|選擇性：從下拉式清單中選取 [TLS 版本]。 如需詳細資訊，請參閱[傳輸層安全性](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security)。|
|虛擬網路|選擇性：從下拉式清單中選取現有的虛擬網路和子網。 如需相關資訊，請參閱[規劃 Azure HDInsight 叢集的虛擬網路部署](hdinsight-plan-virtual-network-deployment.md)。 此文章包含虛擬網路的特定設定需求。|
|磁片加密設定|選擇性：選取要使用加密的核取方塊。 如需詳細資訊，請參閱[客戶管理的金鑰磁片加密](./disk-encryption.md)。|
|Kafka REST Proxy|此設定僅適用于叢集類型 Kafka。 如需詳細資訊，請參閱[使用 REST proxy](./kafka/rest-proxy.md)。|
|身分識別|選擇性：從下拉式清單中選取現有的使用者指派服務識別。 如需詳細資訊，請參閱[Azure HDInsight 中的受控](./hdinsight-managed-identities.md)識別。|

選取 **[下一步]： [設定 + 定價 >] >** 繼續前往下一個索引標籤。

## <a name="configuration--pricing"></a>設定 + 定價

![HDInsight 建立叢集設定](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "[設定和定價] 索引標籤")

從 [設定 **+ 定價**] 索引標籤中，提供下列資訊：

|屬性 |描述 |
|---|---|
|+ 新增應用程式|選擇性：選取您想要的任何應用程式。 Microsoft、獨立軟體廠商 (ISV) 或您可以開發這些應用程式。 如需詳細資訊，請參閱[在叢集建立期間安裝應用程式](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)。|
|節點大小|選擇性：選取不同大小的節點。|
|節點數目|選擇性：輸入指定節點類型的節點數目。 如果您計畫使用超過32個背景工作節點，請選取具有至少8個核心和 14 GB RAM 的前端節點大小。 請在建立叢集時規劃節點，或在建立後透過調整叢集規模的方式進行規劃。|
|啟用自動調整|選擇性：選取核取方塊以啟用功能。 如需詳細資訊，請參閱[自動調整 Azure HDInsight](./hdinsight-autoscale-clusters.md)叢集。|
|+ 新增腳本動作|選擇性：如果您想要在建立叢集時使用自訂腳本來自訂叢集，此選項適用于。 如需有關指令碼動作的詳細資訊，請參閱[使用指令碼動作來自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。|

選取 [**審查 + 建立 >] >** 來驗證叢集設定並前進至最後一個索引標籤。

## <a name="review--create"></a>檢閱 + 建立

![HDInsight 建立叢集摘要](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "指定叢集節點的數目")

檢查設定。 選取 [建立] 以建立叢集。

建立叢集需要一些時間，通常約 20 分鐘左右。 監視**通知**以檢查佈建程序。

## <a name="post-creation"></a>建立後

在建立程序完成之後，從 [已成功部署] 通知中選取 [前往資源]。 叢集視窗會提供以下資訊。

![HDI Azure 入口網站叢集總覽](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "叢集屬性")

視窗中的部分圖示說明如下：

|屬性 | 描述 |
|---|---|
|概觀|提供叢集的所有基本資訊。 例如名稱、其所屬資源群組、位置、作業系統，以及叢集儀表板的 URL。|
|叢集儀表板|會將您導向與叢集相關聯的 Ambari 入口網站。|
|SSH + 叢集登入|提供使用 SSH 存取叢集所需的資訊。|
|刪除|刪除 HDInsight 叢集。|

## <a name="customize-clusters"></a>自訂叢集

* [使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [使用指令碼動作來自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>選取叢集

請參閱[使用您的瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight](./hdinsight-delete-cluster.md)叢集。

## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](./hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>後續步驟

您已成功建立 HDInsight 叢集。 現在，請了解如何使用您的叢集。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 叢集

* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 叢集

* [開始使用 HDInsight 上的 Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md)
* [開發適用於 HDInsight 上 Apache HBase 的 Java 應用程式](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 叢集

* [開發適用於 HDInsight 上 Apache Storm 的 Java 拓撲](storm/apache-storm-develop-java-topology.md)
* [在 HDInsight 上的 Apache Storm 中使用 Python 元件](storm/apache-storm-develop-python-topology.md)
* [使用 Apache Storm on HDInsight 部署和監視拓撲](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark 叢集

* [使用 Scala 來建立獨立應用程式](spark/apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 叢集上從遠端執行作業](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](spark/apache-spark-machine-learning-mllib-ipython.md)
