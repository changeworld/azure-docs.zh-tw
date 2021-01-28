---
title: 建立 Azure HDInsight-Azure Data Lake Storage Gen2-入口網站
description: 瞭解如何使用入口網站搭配 Azure HDInsight 叢集的 Azure Data Lake Storage Gen2。
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: d54c005ffb17571be172b5716723febb742253a3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945386"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>使用 Azure 入口網站建立具有 Data Lake Storage Gen2 的叢集

Azure 入口網站是 Web 架構的管理工具，可用來管理裝載於 Microsoft Azure 雲端中的服務和資源。 在本文中，您會了解如何使用入口網站來建立 Linux 型 Azure HDInsight 叢集。 您可以從 [建立 HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)叢集取得其他詳細資料。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

若要建立使用 Data Lake Storage Gen2 儲存的 HDInsight 叢集，請遵循下列步驟來設定具有階層命名空間的儲存體帳戶。

## <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

建立使用者指派的受控識別 (如果您還沒有的話)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左上方按一下 [ **建立資源**]。
1. 在 [搜尋] 方塊中，輸入 **使用者指派** ，然後按一下 [ **使用者指派的受控識別**]。
1. 按一下頁面底部的 [新增]  。
1. 輸入受控識別的名稱，然後選取正確的訂用帳戶、資源群組和位置。
1. 按一下頁面底部的 [新增]  。

如需有關受控識別如何在 Azure HDInsight 中運作的詳細資訊，請參閱 [Azure HDInsight 中的受控](hdinsight-managed-identities.md)識別。

![建立使用者指派的受控識別](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>建立要與 Data Lake Storage Gen2 搭配使用的儲存體帳戶

建立要與 Azure Data Lake Storage Gen2 搭配使用的儲存體帳戶。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左上方按一下 [ **建立資源**]。
1. 在搜尋方塊中，輸入 **儲存體** ，然後按一下 [ **儲存體帳戶**]。
1. 按一下頁面底部的 [新增]  。
1. 在 [ **建立儲存體帳戶** ] 畫面上：
    1. 選取正確的訂用帳戶和資源群組。
    1. 輸入具有 Data Lake Storage Gen2 之儲存體帳戶的名稱。
    1. 按一下 [ **Advanced （Advanced** ）] 索引標籤。
    1. 按一下 [ **Data Lake Storage Gen2**] 下 **階層命名空間** 旁的 [**已啟用**]。
    1. 按一下 [檢閱 + 建立]。
    1. 按一下 [建立] 

如需建立儲存體帳戶期間其他選項的詳細資訊，請參閱 [快速入門：建立 Azure Data Lake Storage Gen2 的儲存體帳戶](../storage/blobs/create-data-lake-storage-account.md)。

![顯示在 Azure 入口網站建立儲存體帳戶的螢幕擷取畫面](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>在 Data Lake Storage Gen2 上設定受控識別的許可權

將受控識別指派給儲存體帳戶上的 **儲存體 Blob 資料擁有** 者角色。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。
1. 選取您的儲存體帳戶，然後選取 [ **存取控制] (IAM)** ，以顯示帳戶的存取控制設定。 選取 [角色指派] 索引標籤，以查看角色指派的清單。

    ![顯示儲存體存取控制設定的螢幕擷取畫面](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. 選取 [ **+ 新增角色指派** ] 按鈕以新增角色。
1. 在 [ **新增角色指派** ] 視窗中，選取 **儲存體 Blob 資料擁有** 者角色。 然後，選取包含受控識別和儲存體帳戶的訂用帳戶。 接著，搜尋並找出您先前建立的使用者指派受控識別。 最後，選取受控識別，它會列在 [選取的 **成員**] 底下。

    ![顯示如何指派 Azure 角色的螢幕擷取畫面](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. 選取 [儲存]。 您選取的使用者指派身分識別現在會列在選取的角色底下。
1. 完成此初始設定後，您可以透過入口網站建立叢集。 此叢集必須與儲存體帳戶位在相同的 Azure 區域中。 在叢集建立功能表的 [ **儲存體** ] 索引標籤中，選取下列選項：

    * 針對 [ **主要儲存體類型**]，選取 [ **Azure Data Lake Storage Gen2**]。
    * 在 [ **主要儲存體帳戶**] 下，搜尋並選取具有 Data Lake Storage Gen2 儲存體的新建立儲存體帳戶。

    * 在 [身分 **識別**] 下，選取新建立的使用者指派受控識別。

        ![搭配 Azure HDInsight 使用 Data Lake Storage Gen2 的儲存體設定](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * 若要使用 Data Lake Storage Gen2 新增次要儲存體帳戶，請在儲存體帳戶層級，只將稍早建立的受控識別指派給您想要新增的新 Data Lake Storage Gen2。 請注意，不支援透過 HDInsight 上的 [其他儲存體帳戶] 分頁新增具有 Data Lake Storage Gen2 的次要儲存體帳戶。
    > * 您可以在 HDInsight 所使用的 Azure Blob 儲存體帳戶上啟用 GRS 或 ZRS。 但是，不支援針對 GRS 或 RA-ZRS 次要端點建立叢集。

## <a name="delete-the-cluster"></a>選取叢集

請參閱 [使用您的瀏覽器、PowerShell 或 Azure CLI 來刪除 HDInsight](./hdinsight-delete-cluster.md)叢集。

## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](./hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>後續步驟

您已成功建立 HDInsight 叢集。 現在，請了解如何使用您的叢集。

### <a name="apache-spark-clusters"></a>Apache Spark 叢集

* [使用指令碼動作來自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)
* [使用 Scala 建立獨立應用程式](spark/apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 叢集

* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [搭配 HDInsight 使用 MapReduce](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 叢集

* [開始使用 HDInsight 上的 Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md)
* [開發適用於 HDInsight 上 Apache HBase 的 Java 應用程式](hbase/apache-hbase-build-java-maven-linux.md)
