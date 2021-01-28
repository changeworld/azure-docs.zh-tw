---
title: 搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2
description: 瞭解如何搭配使用 Azure Data Lake Storage Gen2 與 Azure HDInsight 叢集。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/24/2020
ms.openlocfilehash: 2bbfbd2d953ea663453f0092ff366e95f6dd5ea7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945382"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 是以 [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)為基礎的大型資料分析專用的雲端儲存體服務。 Data Lake Storage Gen2 結合了 Azure Blob 儲存體和 Azure Data Lake Storage Gen1 的功能。 產生的服務提供來自 Azure Data Lake Storage Gen1 的功能，包括：檔案系統的語法、目錄層級和檔案層級安全性，以及適應性。 以及 Azure Blob 儲存體的低成本、分層式儲存體、高可用性和嚴重損壞修復功能。

如需使用 Data Lake Storage Gen2 的叢集建立選項的完整比較，請參閱 [比較與 Azure HDInsight 叢集搭配使用的儲存體選項](hdinsight-hadoop-compare-storage-options.md)。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 可用性

Data Lake Storage Gen2 作為儲存體選項，幾乎所有 Azure HDInsight 叢集類型都可作為預設和額外的儲存體帳戶。 不過，HBase 只能有一個具有 Data Lake Storage Gen2 的帳戶。

> [!Note]  
> 選取 Data Lake Storage Gen2 作為 **主要儲存體類型** 之後，您就無法選取 Data Lake Storage Gen1 作為額外的儲存體。

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>使用 Data Lake Storage Gen2 建立 HDInsight 叢集

如需有關如何建立可存取 Data Lake Storage Gen2 的 HDInsight 叢集的詳細指示，請使用下列連結。

* [使用入口網站](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [使用 Azure CLI](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* 目前不支援使用 PowerShell 建立具有 Azure Data Lake Storage Gen2 的 HDInsight 叢集。

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight 中 Data Lake Storage Gen2 的存取控制

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 支援哪些種類的權限？

Data Lake Storage Gen2 使用支援 Azure 角色型存取控制的存取控制模型 (Azure RBAC) 以及類似 POSIX 的存取控制清單 (Acl) 。 Data Lake Storage Gen1 僅支援控制資料存取的存取控制清單。

Azure RBAC 會使用角色指派，有效地將許可權集套用至 Azure 資源的使用者、群組和服務主體。 一般而言，這些 Azure 資源會限制為最上層資源 (例如，Azure Blob 儲存體帳戶) 。 針對 Azure Blob 儲存體，也 Data Lake Storage Gen2，此機制已擴充至檔案系統資源。

如需有關 Azure RBAC 檔案許可權的詳細資訊，請參閱 azure [rbac)  (的 azure 角色型存取控制 ](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)。

如需有關 Acl 的檔案許可權的詳細資訊，請參閱檔案 [和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md)。

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>如何? 在 Data Lake Storage Gen2 中控制資料的存取權嗎？

您的 HDInsight 叢集在 Data Lake Storage Gen2 中存取檔案的能力是透過受控識別來控制。 受控識別是在 Azure Active Directory (Azure AD) 的身分識別，其認證是由 Azure 管理。 使用受控識別時，您不需要在 Azure AD 中註冊服務主體。 或維護憑證之類的認證。

Azure 服務有兩種類型的受控識別：系統指派和使用者指派。 HDInsight 會使用使用者指派的受控識別來存取 Data Lake Storage Gen2。 `user-assigned managed identity`會建立為獨立的 Azure 資源。 透過建立程序，Azure 會在所使用訂用帳戶信任的 Azure AD 租用戶中建立身分識別。 建立身分識別之後，即可將它指派給一個或多個 Azure 服務執行個體。

使用者指派的身分識別與獲指派此身分識別的 Azure 服務執行個體，兩者的生命週期分開管理。 如需受控識別的詳細資訊，請參閱 [什麼是適用于 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>如何? 將 Azure AD 使用者的許可權設定為使用 Hive 或其他服務查詢 Data Lake Storage Gen2 中的資料？

若要設定使用者的許可權以查詢資料，請使用 Azure AD 安全性群組作為 Acl 中指派的主體。 請勿直接將檔案存取權限指派給個別使用者或服務主體。 利用 Azure AD 安全性群組來控制許可權的流程，您可以新增和移除使用者或服務主體，而不需要將 Acl 重新套用至整個目錄結構。 您只需要從適當的 Azure AD 安全性群組新增或移除使用者即可。 Acl 不會被繼承，因此重新套用 Acl 需要更新每個檔案和子目錄的 ACL。

## <a name="access-files-from-the-cluster"></a>從叢集存取檔案

您可以透過數種方式從 HDInsight 叢集存取 Data Lake Storage Gen2 中的檔案。

* **使用完整格式名稱**。 使用這種方法，您可以針對想要存取的檔案提供完整路徑。

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **使用簡短路徑格式**。 使用這種方法，您可以利用以下方式取代到叢集根目錄的路徑：

    ```
    abfs:///<file.path>/
    ```

* **使用相對路徑**。 使用這種方法，您可以針對想要存取的檔案，只提供相對路徑。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>資料存取範例

範例是以叢集前端節點的 [ssh 連線](./hdinsight-hadoop-linux-use-ssh-unix.md)為基礎。 這些範例會使用這三個 URI 配置。 請將 `CONTAINERNAME` 和 `STORAGEACCOUNT` 取代為相關值

#### <a name="a-few-hdfs-commands"></a>一些 hdfs 命令

1. 在本機儲存體上建立檔案。

    ```bash
    touch testFile.txt
    ```

1. 在叢集儲存體上建立目錄。

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 將資料從本機儲存體複製到叢集儲存體。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 列出叢集儲存體上的目錄內容。

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>建立 Hive 資料表

顯示三個檔案位置以供說明之用。 若為實際執行，請僅使用其中一個 `LOCATION` 項目。

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 與 Data Lake Storage Gen2 預覽版的整合 - ACL 和安全性更新](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)
* [教學課程：使用 Azure HDInsight 上的互動式查詢來擷取、轉換和載入資料](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
