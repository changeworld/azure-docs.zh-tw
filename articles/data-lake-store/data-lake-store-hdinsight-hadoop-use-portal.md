---
title: 使用資料存儲第 1 代 - 門戶創建 Azure HDInsight 群集
description: 使用 Azure 入口網站建立和使用搭配 Azure Data Lake Storage Gen1 運作的 HDInsight 叢集
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265567"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>使用 Azure 入口網站建立搭配 Azure Data Lake Storage Gen1 的 HDInsight 叢集

> [!div class="op_single_selector"]
> * [使用 Azure 門戶](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell (針對預設儲存體)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell (針對額外儲存體)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用 Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

了解如何利用 Azure 入口網站，建立使用 Azure Data Lake Storage Gen1 帳戶作為預設儲存體或其他儲存體的 HDInsight 叢集。 即使 HDInsight 群集可以選擇其他存儲，但建議將業務資料存儲到其他存儲帳戶中。

## <a name="prerequisites"></a>Prerequisites

在開始之前，請確保您已滿足以下要求：

* **Azure 訂閱**。 請移至[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **資料存儲湖存儲第 1 代帳戶**。 遵循[使用 Azure 入口網站開始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) 的指示操作。 您也必須在此帳戶上建立根資料夾。  在本文中，使用名為 __/群集__的根資料夾。
* **Azure Active Directory 服務主體**。 本操作指南提供有關如何在 Azure 活動目錄 （Azure AD） 中創建服務主體的說明。 不過，您必須是 Azure AD 系統管理員，才能建立服務主體。 如果您是管理員，則可以跳過此先決條件並繼續。

>[!NOTE]
>僅當是 Azure AD 管理員時，才能創建服務主體。 您的 Azure AD 系統管理員必須先建立服務主體，您才能建立搭配 Data Lake Storage Gen1 的 HDInsight 叢集。 此外，必須使用憑證來建立服務主體，如[使用憑證來建立服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)所述。
>

## <a name="create-an-hdinsight-cluster"></a>建立 HDInsight 叢集

在此節中，您會建立一個使用 Data Lake Storage Gen1 帳戶作為預設或其他儲存體的 HDInsight 叢集。 本文僅重點介紹配置資料存儲庫存儲第 1 代帳戶。 如需一般叢集建立資訊和程序，請參閱 [在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>建立以 Data Lake Storage Gen1 做為預設儲存體的叢集

要創建具有資料存儲湖存儲 Gen1 帳戶的 HDInsight 群集，請作為預設存儲帳戶：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 請遵循[建立叢集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)，以取得建立 HDInsight 叢集的一般資訊。
3. 在 [儲存體]**** 刀鋒視窗的 [主要儲存體類型]**** 下，選取 [Azure Data Lake Storage Gen1]****，然後輸入下列資訊：

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "將服務主體新增至 HDInsight 叢集")

    * **選取 Data Lake Store 帳戶**： 選取現有的 Data Lake Storage Gen1 帳戶。 需要現有的 Data Lake Storage Gen1 帳戶。  請參閱[必要條件](#prerequisites)。
    * **根路徑**：輸入要儲存叢集特定檔案的路徑。 在螢幕擷取畫面上，它是 __/clusters/myhdiadlcluster/__，其中 __/clusters__ 資料夾必須已存在，而入口網站會建立 *myhdicluster* 資料夾。  *myhdicluster* 是叢集名稱。
    * **Data Lake Store 存取**：設定 Data Lake Storage Gen1 帳戶與 HDInsight 叢集之間的存取。 如需指示，請參閱[設定 Data Lake Storage Gen1 存取](#configure-data-lake-storage-gen1-access)。
    * **其他儲存體帳戶**：新增 Azure 儲存體帳戶作為叢集的其他儲存體帳戶。 若要新增其他 Data Lake Storage Gen1 帳戶，做法是設定某個 Data Lake Storage Gen1 帳戶作為主要儲存體類型，同時授與叢集存取其他 Data Lake Storage Gen1 帳戶資料的權限。 [設定 Data Lake Storage Gen1 存取](#configure-data-lake-storage-gen1-access)。

4. 在 [Data Lake Store 存取權]**** 上按一下 [選取]****，然後繼續進行叢集建立作業，如[在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)所述。

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>建立以 Data Lake Storage Gen1 做為額外儲存體的叢集

下列指示說明如何建立使用 Azure 儲存體帳戶作為預設儲存體，並使用 Data Lake Storage Gen1 帳戶作為其他儲存體的 HDInsight 叢集。

要使用資料存儲湖存儲 Gen1 帳戶創建 HDInsight 群集，請作為其他存儲帳戶：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 請遵循[建立叢集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)，以取得建立 HDInsight 叢集的一般資訊。
3. 在 [儲存體]**** 刀鋒視窗的 [主要儲存體類型]**** 下，選取 [Azure 儲存體]****，然後輸入下列資訊：

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "將服務主體新增至 HDInsight 叢集")

    * **選擇方法**- 要指定屬於 Azure 訂閱的存儲帳戶，請選擇 **"我的訂閱**"，然後選擇存儲帳戶。 若要指定 Azure 訂用帳戶外部的儲存體帳戶，請選取 [存取金鑰]****，然後提供外部儲存體帳戶資訊。

    * **預設容器**- 使用預設值或指定您自己的名稱。
    * **其他存儲帳戶**- 添加更多 Azure 存儲帳戶作為其他存儲。
    * **資料存儲湖訪問**- 配置資料存儲第 1 代帳戶和 HDInsight 群集之間的訪問。 有關說明，請參閱[配置資料存儲湖存儲第 1 代存取權限](#configure-data-lake-storage-gen1-access)。

## <a name="configure-data-lake-storage-gen1-access"></a>設定 Data Lake Storage Gen1 存取

在此節中，您將會使用 Azure Active Directory 服務主體設定 Data Lake Storage Gen1 與 HDInsight 叢集之間的存取。

### <a name="specify-a-service-principal"></a>指定服務主體

從 Azure 入口網站中，您可以使用現有的服務主體或建立一個新的服務主體。

要從 Azure 門戶創建服務主體：

1. 從存儲邊欄選項卡中選擇 **"資料湖存儲"存取權限**。
1. 在 **"資料存儲湖存儲第 1 代"訪問**邊欄選項卡上，選擇 **"創建新**"。
1. 選擇**服務主體**，然後按照說明創建服務主體。
1. 如果您決定要在未來再次使用它，請下載憑證。 如果您想要在建立其他 HDInsight 叢集時使用相同的服務主體，下載憑證非常實用。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "將服務主體新增至 HDInsight 叢集")

1. 選擇 **"訪問"** 以配置資料夾訪問。  請參閱[設定檔案權限](#configure-file-permissions)。

要使用 Azure 門戶中的現有服務主體：

1. 選擇**資料湖存儲訪問**。
1. 在 **"資料湖存儲第 1 代"訪問**邊欄選項卡上，選擇 **"使用現有**"。
1. 選擇**服務主體**，然後選擇服務主體。
1. 上傳與您所選取服務主體建立關聯的憑證 (.pfx 檔案)，然後輸入憑證密碼。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "將服務主體新增至 HDInsight 叢集")

1. 選擇 **"訪問"** 以配置資料夾訪問。  請參閱[設定檔案權限](#configure-file-permissions)。

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>設定檔案權限

配置因帳戶是用作預設存儲還是用作其他存儲帳戶而異：

* 作為預設儲存體

  * Data Lake Storage Gen1 帳戶根層級的權限
  * HDInsight 叢集儲存體根層級的權限。 例如，稍早在教學課程中使用的 __/clusters__ 資料夾。

* 作為其他儲存體

  * 您需要其檔案存取權之資料夾的權限。

要在資料存儲湖存儲第 1 代帳戶根級別分配許可權，請進行以下工作：

1. 在**資料湖存儲 Gen1 訪問**邊欄選項卡上，選擇 **"訪問**"。 隨即會開啟 [選取檔案權限]**** 刀鋒視窗。 其中列出您訂用帳戶中的所有 Data Lake Storage Gen1 帳戶。
1. 請將滑鼠游標暫留 (請勿按一下) 在 Data Lake Storage Gen1 帳戶的名稱上，以顯示核取方塊，然後選取此核取方塊。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "將服務主體新增至 HDInsight 叢集")

   預設情況下，__都選擇了"讀取__"、"__寫入____"和"執行__"。

1. 按一下頁面底部的 [選取]****。
1. 選擇 **"運行**"以分配許可權。
1. 選擇 **"完成**"。

要在 HDInsight 群集根級別分配許可權：：

1. 在**資料湖存儲 Gen1 訪問**邊欄選項卡上，選擇 **"訪問**"。 隨即會開啟 [選取檔案權限]**** 刀鋒視窗。 其中列出您訂用帳戶中的所有 Data Lake Storage Gen1 帳戶。
1. 在 **"選擇檔許可權"** 邊欄選項卡中，選擇"資料存儲庫 1"帳戶名稱以顯示其內容。
1. 選取 HDInsight 叢集儲存體根目錄左邊的核取方塊，即可選取該資料夾。 根據先前的螢幕擷取畫面，叢集儲存體根目錄為您在選取 Data Lake Storage Gen1 作為預設儲存體時所指定的 __/clusters__ 資料夾。
1. 設定資料夾的權限。  根據預設，讀取、寫入和執行會全部選取。
1. 按一下頁面底部的 [選取]****。
1. 選擇 **"運行**"。
1. 選擇 **"完成**"。

如果您使用 Data Lake Storage Gen1 作為額外儲存體，則必須僅針對您想要從 HDInsight 叢集存取的資料夾指派權限。 例如，在下方的螢幕擷取畫面中，您僅會將存取提供給 Data Lake Storage Gen1 帳戶中的 **mynewfolder** 資料夾。

![將服務主體許可權分配給 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "將服務主體許可權分配給 HDInsight 群集")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>驗證群集設置

叢集設定完成之後，在叢集刀鋒視窗上執行下列任一或這兩個步驟來驗證結果：

* 要驗證群集的關聯存儲是否為指定的 Data Lake 存儲 Gen1 帳戶，請在左側窗格中選擇 **"存儲帳戶**"。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "將服務主體新增至 HDInsight 叢集")

* 要驗證服務主體是否與 HDInsight 群集正確關聯，請在左側窗格中選擇 **"資料存儲湖存儲 Gen1"存取權限**。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "將服務主體新增至 HDInsight 叢集")

## <a name="examples"></a>範例

將資料存儲庫 Gen1 設置為存儲後，請參閱以下示例，瞭解如何使用 HDInsight 群集分析資料存儲在資料存儲 Gen1 中存儲的資料。

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>針對 Data Lake Storage Gen1 帳戶 (做為主要儲存體) 中的資料執行 Hive 查詢

若要執行 Hive 查詢，請使用 Ambari 入口網站中的 Hive 檢視介面。 如需有關如何使用 Ambari Hive 檢視的指示，請參閱[在 HDInsight 中搭配 Hadoop 使用 Hive 檢視](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)。

當您使用 Data Lake Storage Gen1 帳戶中的資料時，有幾個字串需要變更。

如果您使用以 Data Lake Storage Gen1 做為主要儲存體而建立的叢集，則資料路徑為︰adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file**。 從 Data Lake Storage Gen1 帳戶中儲存的範例資料建立資料表的 Hive 查詢，類似於下列陳述式：

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

說明：

* `adl://hdiadlsg1storage.azuredatalakestore.net/` 是 Data Lake Storage Gen1 帳戶的根。
* `/clusters/myhdiadlcluster` 是您在建立叢集時指定之叢集資料的根目錄。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` 是您在查詢中使用之範例檔案的位置。

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>針對 Data Lake Storage Gen1 帳戶 (做為額外儲存體) 中的資料執行 Hive 查詢

如果您建立的叢集使用 Blob 儲存體做為預設儲存體，則範例資料不會包含在做為額外儲存體的 Data Lake Storage Gen1 帳戶中。 在這種情況下，請先將資料從 Blob 儲存體傳送至 Data Lake Storage Gen1 帳戶，然後如上述範例所示執行查詢。

如需如何將資料從 Blob 儲存體複製到 Data Lake Storage Gen1 帳戶的詳細資訊，請參閱下列文章：

* [使用 Distcp 在 Azure 儲存體 Blob 與 Data Lake Storage Gen1 之間複製資料](data-lake-store-copy-data-wasb-distcp.md)
* [使用 AdlCopy 將資料從 Azure 儲存體 Blob 複製到 Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>使用 Data Lake Storage Gen1 搭配 Spark 叢集

您可以使用 Spark 叢集對 Data Lake Storage Gen1 中儲存的資料執行 Spark 作業。 有關詳細資訊，請參閱使用[HDInsight Spark 群集分析資料存儲第 1 代 中的資料](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)。

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>在 Storm 拓撲中使用 Data Lake Storage Gen1

您可以使用 Data Lake Storage Gen1 帳戶從 Storm 拓撲寫入資料。 如需有關如何達到這種情況的指示，請參閱[搭配使用 Azure Data Lake Storage Gen1 與 HDInsight 上的 Apache Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md)。

## <a name="see-also"></a>另請參閱

* [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell：建立 HDInsight 叢集以使用 Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
