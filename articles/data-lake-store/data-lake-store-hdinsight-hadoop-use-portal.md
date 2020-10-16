---
title: 使用 Data Lake Storage Gen1 建立 Azure HDInsight 叢集-入口網站
description: 使用 Azure 入口網站建立和使用搭配 Azure Data Lake Storage Gen1 運作的 HDInsight 叢集
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 420efd653ef6218b5a1d5a8c70ca268b7185fc30
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103538"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>使用 Azure 入口網站建立搭配 Azure Data Lake Storage Gen1 的 HDInsight 叢集

> [!div class="op_single_selector"]
> * [使用 Azure 入口網站](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell (針對預設儲存體)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell (針對額外儲存體)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用 Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

瞭解如何使用 Azure 入口網站建立 Azure Data Lake Storage Gen1 作為預設儲存體或額外儲存體的 HDInsight 叢集。 雖然 HDInsight 叢集的額外儲存體是選擇性的，但建議將您的商務資料儲存在其他儲存體帳戶中。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您已符合下列需求：

* **Azure 訂用帳戶**。 請移至[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帳戶**。 遵循[使用 Azure 入口網站開始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) 的指示操作。 您也必須在此帳戶上建立根資料夾。  在本文中，會使用名為 __/clusters__ 的根資料夾。
* **Azure Active Directory 的服務主體**。 本操作指南提供如何在 Azure Active Directory (Azure AD) 中建立服務主體的指示。 不過，您必須是 Azure AD 系統管理員，才能建立服務主體。 如果您是系統管理員，可以略過這項必要條件並繼續。

>[!NOTE]
>只有當您是 Azure AD 系統管理員時，才可以建立服務主體。 您的 Azure AD 系統管理員必須先建立服務主體，您才能建立搭配 Data Lake Storage Gen1 的 HDInsight 叢集。 此外，必須使用憑證來建立服務主體，如[使用憑證來建立服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)所述。
>

## <a name="create-an-hdinsight-cluster"></a>建立 HDInsight 叢集

在本節中，您會建立一個 HDInsight 叢集，其 Data Lake Storage Gen1 為預設或額外的儲存體。 本文僅著重于設定 Data Lake Storage Gen1 的一部分。 如需一般叢集建立資訊和程序，請參閱 [在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>建立以 Data Lake Storage Gen1 做為預設儲存體的叢集

使用 Data Lake Storage Gen1 作為預設儲存體帳戶來建立 HDInsight 叢集：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 請遵循[建立叢集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)，以取得建立 HDInsight 叢集的一般資訊。
3. 在 [儲存體]**** 刀鋒視窗的 [主要儲存體類型]**** 下，選取 [Azure Data Lake Storage Gen1]****，然後輸入下列資訊：

    ![HDInsight 儲存體帳戶設定](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png)

    * **選取 Data Lake Store 帳戶**： 選取現有的 Data Lake Storage Gen1 帳戶。 需要現有的 Data Lake Storage Gen1 帳戶。  請參閱[必要條件](#prerequisites)。
    * **根路徑**：輸入要儲存叢集特定檔案的路徑。 在螢幕擷取畫面上，它是 __/clusters/myhdiadlcluster/__，其中 __/clusters__ 資料夾必須已存在，而入口網站會建立 *myhdicluster* 資料夾。  *myhdicluster* 是叢集名稱。
    * **Data Lake Store 存取**：設定 Data Lake Storage Gen1 帳戶與 HDInsight 叢集之間的存取。 如需指示，請參閱[設定 Data Lake Storage Gen1 存取](#configure-data-lake-storage-gen1-access)。
    * **其他儲存體帳戶**：新增 Azure 儲存體帳戶作為叢集的其他儲存體帳戶。 若要新增其他 Data Lake Storage Gen1 帳戶，做法是設定某個 Data Lake Storage Gen1 帳戶作為主要儲存體類型，同時授與叢集存取其他 Data Lake Storage Gen1 帳戶資料的權限。 [設定 Data Lake Storage Gen1 存取](#configure-data-lake-storage-gen1-access)。

4. 在 [Data Lake Store 存取權]**** 上按一下 [選取]****，然後繼續進行叢集建立作業，如[在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)所述。

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>建立以 Data Lake Storage Gen1 做為額外儲存體的叢集

下列指示會建立以 Azure Blob 儲存體帳戶作為預設儲存體的 HDInsight 叢集，以及使用 Data Lake Storage Gen1 作為額外儲存體的儲存體帳戶。

使用 Data Lake Storage Gen1 作為額外的儲存體帳戶來建立 HDInsight 叢集：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 請遵循[建立叢集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)，以取得建立 HDInsight 叢集的一般資訊。
3. 在 [儲存體]**** 刀鋒視窗的 [主要儲存體類型]**** 下，選取 [Azure 儲存體]****，然後輸入下列資訊：

    ![HDInsight 儲存體帳戶設定額外的儲存體](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png)

    * **選取方法** -若要指定屬於您 Azure 訂用帳戶一部分的儲存體帳戶，請選取 [ **我**的訂用帳戶]，然後選取儲存體帳戶。 若要指定 Azure 訂用帳戶外部的儲存體帳戶，請選取 [存取金鑰]****，然後提供外部儲存體帳戶資訊。

    * **預設容器** -請使用預設值或指定您自己的名稱。
    * **其他儲存體帳戶** ：新增其他 Azure 儲存體帳戶作為額外的儲存體。
    * **Data Lake Store 存取** -設定 Data Lake Storage Gen1 帳戶和 HDInsight 叢集之間的存取權。 如需相關指示，請參閱 [設定 Data Lake Storage Gen1 存取](#configure-data-lake-storage-gen1-access)。

## <a name="configure-data-lake-storage-gen1-access"></a>設定 Data Lake Storage Gen1 存取

在此節中，您將會使用 Azure Active Directory 服務主體設定 Data Lake Storage Gen1 與 HDInsight 叢集之間的存取。

### <a name="specify-a-service-principal"></a>指定服務主體

從 Azure 入口網站中，您可以使用現有的服務主體或建立一個新的服務主體。

若要從 Azure 入口網站建立服務主體：

1. 從儲存體分頁選取 **Data Lake Store 存取** 。
1. 在 [ **Data Lake Storage Gen1 存取** ] 分頁上，選取 [ **建立新**的]。
1. 選取 [ **服務主體**]，然後依照指示來建立服務主體。
1. 如果您決定要在未來再次使用它，請下載憑證。 如果您想要在建立其他 HDInsight 叢集時使用相同的服務主體，下載憑證非常實用。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png)

1. 選取 [ **存取** ] 以設定資料夾存取。  請參閱[設定檔案權限](#configure-file-permissions)。

若要從 Azure 入口網站使用現有的服務主體：

1. 選取 [ **Data Lake Store 存取**]。
1. 在 [ **Data Lake Storage Gen1 存取** ] 分頁上，選取 [ **使用現有**的]。
1. 選取 [ **服務主體**]，然後選取服務主體。
1. 上傳與您所選取服務主體建立關聯的憑證 (.pfx 檔案)，然後輸入憑證密碼。

[將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png)

1. 選取 [ **存取** ] 以設定資料夾存取。  請參閱[設定檔案權限](#configure-file-permissions)。

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>設定檔案權限

根據帳戶是作為預設儲存體或額外的儲存體帳戶，設定會有所不同：

* 作為預設儲存體

  * Data Lake Storage Gen1 帳戶根層級的權限
  * HDInsight 叢集儲存體根層級的權限。 例如，稍早在教學課程中使用的 __/clusters__ 資料夾。

* 作為其他儲存體

  * 您需要其檔案存取權之資料夾的權限。

若要在具有根層級 Data Lake Storage Gen1 的儲存體帳戶指派許可權：

1. 在 [ **Data Lake Storage Gen1 存取** ] 分頁上，選取 [ **存取**]。 隨即會開啟 [選取檔案權限]**** 刀鋒視窗。 它會列出您訂用帳戶中的所有儲存體帳戶。
1. 將滑鼠停留 (不要按一下) 將滑鼠游標移到 Data Lake Storage Gen1 的帳戶名稱上方，然後選取核取方塊。

    ![選取檔案許可權](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png)

   依預設，會選取 [ __讀取__]、[ __寫入__] 和 [ __執行__ ]。

1. 按一下頁面底部的 [選取]****。
1. 選取 [ **執行** ] 以指派許可權。
1. 選取 [完成]。

若要在 HDInsight 叢集根層級指派許可權：

1. 在 [ **Data Lake Storage Gen1 存取** ] 分頁上，選取 [ **存取**]。 隨即會開啟 [選取檔案權限]**** 刀鋒視窗。 它會列出您訂用帳戶中具有 Data Lake Storage Gen1 的所有儲存體帳戶。
1. 從 [ **選取檔案許可權** ] 分頁中，選取具有 Data Lake Storage Gen1 名稱的儲存體帳戶，以顯示其內容。
1. 選取 HDInsight 叢集儲存體根目錄左邊的核取方塊，即可選取該資料夾。 根據先前的螢幕擷取畫面，叢集儲存體根目錄為您在選取 Data Lake Storage Gen1 作為預設儲存體時所指定的 __/clusters__ 資料夾。
1. 設定資料夾的權限。  根據預設，讀取、寫入和執行會全部選取。
1. 按一下頁面底部的 [選取]****。
1. 選取 [執行]****。
1. 選取 [完成]。

如果您使用 Data Lake Storage Gen1 作為額外儲存體，則必須僅針對您想要從 HDInsight 叢集存取的資料夾指派權限。 例如，在下列螢幕擷取畫面中，您只會提供使用 Data Lake Storage Gen1 之儲存體帳戶中 **mynewfolder** 資料夾的存取權。

![將服務主體許可權指派給 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png)

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>驗證叢集設定

叢集設定完成之後，在叢集刀鋒視窗上執行下列任一或這兩個步驟來驗證結果：

* 若要驗證叢集的相關儲存體是否為您指定 Data Lake Storage Gen1 的帳戶，請選取左窗格中的 [ **儲存體帳戶** ]。

    ![確認相關聯的儲存體](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png)

* 若要驗證服務主體是否正確地與 HDInsight 叢集相關聯，請選取左窗格中的 [ **Data Lake Storage Gen1 存取** ]。

    ![確認服務主體](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png)

## <a name="examples"></a>範例

在您將 Data Lake Storage Gen1 設定為儲存體的叢集之後，請參閱下列範例，以瞭解如何使用 HDInsight 叢集來分析儲存在 Data Lake Storage Gen1 中的資料。

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-primary-storage"></a>針對 Data Lake Storage Gen1 (中的資料執行 Hive 查詢作為主要儲存體) 

若要執行 Hive 查詢，請使用 Ambari 入口網站中的 Hive 檢視介面。 如需有關如何使用 Ambari Hive 檢視的指示，請參閱[在 HDInsight 中搭配 Hadoop 使用 Hive 檢視](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)。

當您使用 Data Lake Storage Gen1 中的資料時，有幾個字串需要變更。

如果您使用以 Data Lake Storage Gen1 做為主要儲存體而建立的叢集，則資料路徑為︰adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file**。 從儲存在 Data Lake Storage Gen1 中的範例資料建立資料表的 Hive 查詢，看起來會像下面的語句：

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

說明：

* `adl://hdiadlsg1storage.azuredatalakestore.net/` 這是具有 Data Lake Storage Gen1 之帳戶的根。
* `/clusters/myhdiadlcluster` 是您在建立叢集時指定之叢集資料的根目錄。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` 是您在查詢中使用之範例檔案的位置。

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-additional-storage"></a>針對 Data Lake Storage Gen1 (中的資料執行 Hive 查詢作為額外的儲存體) 

如果您建立的叢集使用 Blob 儲存體做為預設儲存體，則範例資料不會包含在儲存體帳戶中，並使用 Data Lake Storage Gen1 作為額外的儲存體。 在這種情況下，請先使用 Data Lake Storage Gen1 將 Blob 儲存體中的資料傳輸至儲存體帳戶，然後再執行上述範例所示的查詢。

如需如何將資料從 Blob 儲存體複製到具有 Data Lake Storage Gen1 之儲存體帳戶的詳細資訊，請參閱下列文章：

* [使用 Distcp 在 Azure Blob 儲存體與 Data Lake Storage Gen1 之間複製資料](data-lake-store-copy-data-wasb-distcp.md)
* [使用 AdlCopy 將資料從 Azure Blob 儲存體複製到 Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>使用 Data Lake Storage Gen1 搭配 Spark 叢集

您可以使用 Spark 叢集對儲存在 Data Lake Storage Gen1 中的資料執行 Spark 作業。 如需詳細資訊，請參閱 [使用 HDInsight Spark 叢集來分析 Data Lake Storage Gen1 中的資料](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)。

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>在 Storm 拓撲中使用 Data Lake Storage Gen1

您可以使用具有 Data Lake Storage Gen1 的儲存體帳戶，從風暴拓撲寫入資料。 如需有關如何達到這種情況的指示，請參閱[搭配使用 Azure Data Lake Storage Gen1 與 HDInsight 上的 Apache Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md)。

## <a name="see-also"></a>另請參閱

* [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [PowerShell：建立 HDInsight 叢集以使用 Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx