---
title: 使用 Web 瀏覽器 Azure HDInsight 建立 Apache Hadoop 叢集
description: 瞭解如何在 HDInsight 上創建阿帕奇哈達普、阿帕奇 HBase、阿帕奇風暴或阿帕奇火花群集。 使用 Web 瀏覽器和 Azure 門戶。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: 88392e4e61c28d4fe821c8176242246d10e52b23
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770902"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>使用 Azure 入口網站在 HDInsight 中建立 Linux 型叢集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 入口網站是 Web 架構的管理工具，可用來管理裝載於 Microsoft Azure 雲端中的服務和資源。 在本文中，您會了解如何使用入口網站來建立 Linux 型 Azure HDInsight 叢集。 創建[HDInsight 群集](./hdinsight-hadoop-provision-linux-clusters.md)中提供了其他詳細資訊。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure 入口網站會公開大部分的叢集屬性。 藉由使用 Azure Resource Manager 範本，您可以隱藏許多詳細資料。 如需詳細資訊，請參閱[使用 Resource Manager 範本在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-clusters"></a>建立叢集

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 從頂部功能表中,選擇 **「創建資源**」。

    ![在 Azure 門戶中建立新叢集](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "在 Azure 入口網站中建立新的叢集")

1. 選擇 **「分析** > **Azure HDInsight」** 以轉到 **「創建 HDInsight」群集**頁面。

## <a name="basics"></a>基本概念

![HDInsight 建立叢集基礎知識](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "在 Azure 入口網站中建立新的叢集")

從 **「基礎知識」** 選項卡中,提供以下資訊:

|屬性 |描述 |
|---|---|
|訂用帳戶|從下拉清單中選擇用於群集的 Azure 訂閱。|
|資源群組|從下拉清單中選擇現有資源組,或選擇 **「創建新**」。|
|叢集名稱|輸入全域唯一名稱。|
|區域|從下拉清單中選擇創建群集的區域。|
|叢集類型|按下 **「選擇群集類型**」以打開清單。 從清單中選擇想要的群集類型。 HDInsight 集群有不同類型的類型。 它們與調整叢集時所針對的工作負載或技術對應。 目前沒有任何支援的方法可建立結合多個類型的叢集。|
|版本|從下拉清單中選擇**版本**。 如果您不知道要選擇哪一個項目，請使用預設版本。 如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。|
|叢集登入使用者名稱|提供使用者名稱,預設為**管理員**。|
|叢集登入密碼|提供密碼。|
|確認群組登入密碼|重新輸入密碼|
|安全殼層 (SSH) 使用者名稱|提供使用者名稱,預設為**ssuser**|
|將叢集登入密碼用於 SSH|如果想要與之前指定的管理員密碼相同的 SSH 密碼,請選擇**SSH 的「使用群集登錄密碼**」複選框。 如果不要，請提供 [密碼]**** 或 [公開金鑰]**** 來驗證 SSH 使用者。 我們建議使用公開金鑰。 選擇底部的 [選取]**** 以儲存認證設定。  如需詳細資訊，請參閱[使用 SSH 連線到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。|

選擇 **「下一步」「儲存 >>」** 到下一個選項卡。

## <a name="storage"></a>儲存體

![HDInsight 建立叢集儲存](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "在 Azure 門戶建立新叢集 - 儲存")

### <a name="primary-storage"></a>主要儲存

從**儲存型態的**下拉清單中,選擇預設儲存類型。 要完成的後續欄位將根據您的選擇而變化。 對於**Azure 儲存**:

1. 對於**選擇方法**,選擇「**從清單中選擇**」或 **「使用存取鍵**」。
    * 對於**從清單中選擇**,然後從下拉清單中選擇**您的主儲存帳戶**,或選擇"**創建新**"。
    * 您**要輸入您的儲存****帳號名稱**。 請提供**存取金鑰**。

1. 對於**容器**,接受預設值,或輸入新的值。

### <a name="additional-azure-storage"></a>其他 Azure 儲存

可選:選擇 **「添加 Azure 存儲**以用於其他群集存儲」。 不支援在不同於 HDInsight 群集的不同區域中使用其他存儲帳戶。

### <a name="metastore-settings"></a>中繼設定

可選:指定現有的 SQL 資料庫以保存群集外的 Apache Hive、Apache Oozie 和 Apache Ambari 中繼資料。 用於中儲存的 Azure SQL 資料庫必須允許連接到其他 Azure 服務,包括 Azure HDInsight。 當您建立中繼存放區時，請勿使用包含破折號或連字號的資料庫名稱。 這些字元可能會導致叢集建立程序失敗。

選擇 **「下一步」:安全 + 網路 >>** 往下一個選項卡。

## <a name="security--networking"></a>安全性+ 網路

![HDInsight 建立叢集安全網路](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight 建立叢集安全網路")

在 **「安全+ 網路」** 選項卡中,提供以下資訊:

|屬性 |描述 |
|---|---|
|企業安全性套件|可選:選擇使用**企業安全包**的複選框。 有關詳細資訊,請參閱使用[Azure 活動目錄域服務使用企業安全包配置 HDInsight 叢集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)。|
|TLS|可選:從下拉清單中選擇 TLS 版本。 有關詳細資訊,請參閱[傳輸層安全](./transport-layer-security.md)。|
|虛擬網路|可選:從下拉清單中選擇現有虛擬網路和子網。 有關詳細資訊,請參閱[規劃 Azure HDInsight 叢集的虛擬網路部署](hdinsight-plan-virtual-network-deployment.md)。 此文章包含虛擬網路的特定設定需求。|
|磁碟加密設定|可選:選擇使用加密的複選框。 關於詳細資訊,請參考[客戶管理金鑰磁碟加密](./disk-encryption.md)。|
|Kafka REST Proxy|此設置僅適用於群集類型 Kafka。 有關詳細資訊,請參閱使用[REST 代理](./kafka/rest-proxy.md)程式 。|
|身分識別|可選:從下拉清單中選擇現有使用者分配的服務標識。 有關詳細資訊,請參閱[Azure HDInsight 中的託管識別](./hdinsight-managed-identities.md)。|

選擇 **「下一步」:配置 + 定價 >>** 以前進到下一個選項卡。

## <a name="configuration--pricing"></a>設定 + 定價

![HDInsight 建立叢集設定](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "設定與訂點選項卡")

在 **「設定 + 定價**」選項卡中,提供以下資訊:

|屬性 |描述 |
|---|---|
|• 新增應用程式|可選:選擇所需的任何應用程式。 Microsoft、獨立軟體廠商 (ISV) 或您可以開發這些應用程式。 如需詳細資訊，請參閱[在叢集建立期間安裝應用程式](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)。|
|節點大小|可選:選擇不同大小的節點。|
|節點數目|可選:輸入指定節點類型的節點數。 如果計劃處理超過 32 個輔助節點,請選擇具有至少 8 個內核和 14 GB RAM 的頭節點大小。 請在建立叢集時規劃節點，或在建立後透過調整叢集規模的方式進行規劃。|
|啟用自動調整|可選:選擇複選框以啟用該功能。 有關詳細資訊,請參閱[自動縮放 Azure HDInsight 叢集](./hdinsight-autoscale-clusters.md)。|
|• 新增文稿操作|可選:如果要在創建群集時使用自定義腳本自定義群集,此選項有效。 如需有關指令碼動作的詳細資訊，請參閱[使用指令碼動作來自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。|

選擇 **「檢視」 建立 >>** 以驗證群組設定並前進到最終選項卡。

## <a name="review--create"></a>檢閱 + 建立

![HDInsight 建立叢集摘要](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "指定叢集節點數")

查看設置。 選取 [建立]**** 以建立叢集。

建立叢集需要一些時間，通常約 20 分鐘左右。 監視**通知**以檢查佈建程序。

## <a name="post-creation"></a>帖子創建

在建立程序完成之後，從 [已成功部署]**** 通知中選取 [前往資源]****。 叢集視窗會提供以下資訊。

![HDI Azure 門戶群集概述](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "叢集屬性")

視窗中的一些圖示如下:

|屬性 | 描述 |
|---|---|
|概觀|提供有關群集的所有基本資訊。 例如名稱、其所屬資源群組、位置、作業系統，以及叢集儀表板的 URL。|
|叢集儀表板|將您定向到與群集關聯的 Ambari 門戶。|
|SSH = 群組登入|提供使用 SSH 訪問群集所需的資訊。|
|刪除|刪除 HDInsight 叢集。|

## <a name="customize-clusters"></a>自訂叢集

* [使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [使用指令碼動作來自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>選取叢集

請參考[使用瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight 叢集](./hdinsight-delete-cluster.md)。

## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](./hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>後續步驟

您已成功建立 HDInsight 叢集。 現在，請了解如何使用您的叢集。

* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [開始使用 HDInsight 上的 Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md)
* [開發適用於 HDInsight 上 Apache Storm 的 Java 拓撲](storm/apache-storm-develop-java-topology.md)
* [使用 Scala 來建立獨立應用程式](spark/apache-spark-create-standalone-application.md)
