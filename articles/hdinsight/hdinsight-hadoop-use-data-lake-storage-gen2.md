---
title: 搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2
description: 瞭解如何搭配 Azure HDInsight 叢集使用 Azure Data Lake Storage Gen2。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: cd41c162d44320fc19af904118f202423e68e96a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195344"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2

Azure Data Lake Storage Gen2 是以 Azure Blob 儲存體為基礎的大規模資料分析專用的雲端儲存體服務。 Data Lake Storage Gen2 結合了 Azure Blob 儲存體和 Azure Data Lake Storage Gen1 的功能。 產生的服務會提供 Azure Data Lake Storage Gen1 的功能。 這些功能包括：檔案系統的語法、目錄層級和檔案層級的安全性，以及適應性。 以及來自 Azure Blob 儲存體的低成本、分層式儲存體、高可用性和嚴重損壞修復功能。

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 可用性

Data Lake Storage Gen2 可做為幾乎所有 Azure HDInsight 叢集類型的儲存選項，同時做為預設和額外的儲存體帳戶。 不過，HBase 只能有一個 Data Lake Storage Gen2 帳戶。

如需使用 Data Lake Storage Gen2 進行叢集建立選項的完整比較，請參閱[比較搭配 Azure HDInsight 叢集使用的儲存體選項](hdinsight-hadoop-compare-storage-options.md)。

> [!Note]  
> 當您選取 Data Lake Storage Gen2 做為**主要儲存體類型**後，就無法選取 Data Lake Storage Gen1 帳戶做為額外的儲存體。

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>透過 Azure 入口網站建立具有 Data Lake Storage Gen2 的叢集

若要建立使用 Data Lake Storage Gen2 來儲存儲存體的 HDInsight 叢集，請遵循下列步驟來設定 Data Lake Storage Gen2 帳戶。

### <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

建立使用者指派的受控識別 (如果您還沒有的話)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左上方按一下 [**建立資源**]。
1. 在搜尋方塊中，輸入 [**使用者指派**]，然後按一下 [**使用者指派的受控識別**]。
1. 按一下頁面底部的 [新增]  。
1. 輸入受控識別的 [名稱]，選取正確的訂用帳戶、資源群組和位置。
1. 按一下頁面底部的 [新增]  。

如需如何在 Azure HDInsight 中使用受控識別的詳細資訊，請參閱[Azure HDInsight 中的受控](hdinsight-managed-identities.md)識別。

![建立使用者指派的受控識別](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>建立 Data Lake Storage Gen2 帳戶

建立 Azure Data Lake Storage Gen2 儲存體帳戶。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左上方按一下 [**建立資源**]。
1. 在搜尋方塊中，輸入**儲存體**，然後按一下 [**儲存體帳戶**]。
1. 按一下頁面底部的 [新增]  。
1. 在 [**建立儲存體帳戶**] 畫面上：
    1. 選取正確的訂用帳戶和資源群組。
    1. 輸入 Data Lake Storage Gen2 帳戶的名稱。
    1. 按一下 [ **Advanced** ] 索引標籤。
    1. 按一下 [ **Data Lake Storage Gen2**] 底下 [**階層命名空間**] 旁的 [**已啟用**]。
    1. 按一下 [檢閱 + 建立]  。
    1. 按一下 [建立] 

如需建立儲存體帳戶期間其他選項的詳細資訊，請參閱[快速入門：建立 Azure Data Lake Storage Gen2 儲存體帳戶](../storage/blobs/data-lake-storage-quickstart-create-account.md)。

![顯示在 Azure 入口網站建立儲存體帳戶的螢幕擷取畫面](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>在 Data Lake Storage Gen2 帳戶上設定受控識別的許可權

將受控識別指派給儲存體帳戶上的**儲存體 Blob 資料擁有**者角色。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。
1. 選取您的儲存體帳戶，然後選取 [**存取控制（IAM）** ] 以顯示帳戶的存取控制設定。 選取 [角色指派]  索引標籤，以查看角色指派的清單。

    ![顯示儲存體存取控制設定的螢幕擷取畫面](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. 選取 [ **+ 新增角色指派**] 按鈕以新增角色。
1. 在 [**新增角色指派**] 視窗中，選取 [**儲存體 Blob 資料擁有**者] 角色。 然後，選取包含受控識別和儲存體帳戶的訂用帳戶。 接著，搜尋並找出您先前建立的使用者指派受控識別。 最後，選取受控識別，它會列在 [選取的**成員**] 底下。

    ![顯示如何指派 RBAC 角色的螢幕擷取畫面](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. 選取 [儲存]  。 您選取的使用者指派身分識別現在會列在選取的角色底下。
1. 完成此初始設定後，您可以透過入口網站建立叢集。 此叢集必須與儲存體帳戶位在相同的 Azure 區域中。 在 [叢集建立] 功能表的 [**儲存體**] 索引標籤中，選取下列選項：

    * 針對 [**主要儲存體類型**]，選取 [ **Azure Data Lake Storage Gen2**]。
    * 在 [**主要儲存體帳戶**] 底下，搜尋並選取新建立的 Data Lake Storage Gen2 儲存體帳戶。

    * 在 [身分**識別**] 底下，選取新建立的使用者指派受控識別。

        ![搭配 Azure HDInsight 使用 Data Lake Storage Gen2 的儲存體設定](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * 若要新增次要 Data Lake Storage Gen2 帳戶，請在儲存體帳戶層級，只將稍早建立的受控識別指派給您要新增的新 Data Lake Storage Gen2 儲存體帳戶。 請注意，不支援透過 HDInsight 上的 [其他儲存體帳戶] 分頁來新增次要 Data Lake Storage Gen2 帳戶。
    > * 您可以在 HDInsight 使用的 Azure 儲存體帳戶上啟用 GRS 或 RA ZRS。 不過，不支援針對 GRS 或 RA ZRS 次要端點建立叢集。


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>透過 Azure CLI 建立具有 Data Lake Storage Gen2 的叢集

您可以[下載範例範本](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)檔案，並[下載範例參數](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)檔案。 使用範本和以下的 Azure CLI 程式碼片段之前，請將下列預留位置取代為正確的值：

| 預留位置 | 描述 |
|---|---|
| `<SUBSCRIPTION_ID>` | 您 Azure 訂用帳戶的識別碼 |
| `<RESOURCEGROUPNAME>` | 您想要在其中建立新叢集和儲存體帳戶的資源群組。 |
| `<MANAGEDIDENTITYNAME>` | 受控識別的名稱，將會在您的 Azure Data Lake Storage Gen2 帳戶上取得許可權。 |
| `<STORAGEACCOUNTNAME>` | 將建立的新 Azure Data Lake Storage Gen2 帳戶。 |
| `<CLUSTERNAME>` | 您的 HDInsight 叢集名稱。 |
| `<PASSWORD>` | 您選擇的密碼，用來登入使用 SSH 和 Ambari 儀表板的叢集。 |

下面的程式碼片段會執行下列初始步驟：

1. 登入您的 Azure 帳戶。
1. 設定使用中的訂用帳戶，將會在其中執行建立作業。
1. 為新的部署活動建立新的資源群組。
1. 建立使用者指派的受控識別。
1. 新增 Azure CLI 的延伸模組，以使用 Data Lake Storage Gen2 的功能。
1. 使用`--hierarchical-namespace true`旗標建立新的 Data Lake Storage Gen2 帳戶。

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

接下來，登入入口網站。 將使用者指派的新受控識別新增至儲存體帳戶上的**儲存體 Blob 資料參與者**角色。 此步驟將在[使用 Azure 入口網站](hdinsight-hadoop-use-data-lake-storage-gen2.md)下的步驟3中說明。

為使用者指派的受控識別指派角色之後，請使用下列程式碼片段來部署範本。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>透過 Azure PowerShell 建立具有 Data Lake Storage Gen2 的叢集

目前不支援使用 PowerShell 來建立具有 Azure Data Lake Storage Gen2 的 HDInsight 叢集。

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight 中 Data Lake Storage Gen2 的存取控制

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 支援哪些種類的權限？

Data Lake Storage Gen2 使用支援角色型存取控制（RBAC）和 POSIX 型存取控制清單（Acl）的存取控制模型。 Data Lake Storage Gen1 僅支援存取控制清單來控制資料的存取。

RBAC 會使用角色指派，有效地將許可權集套用至 Azure 資源的使用者、群組和服務主體。 一般而言，這些 Azure 資源會限定於最上層資源 (例如 Azure 儲存體帳戶)。 針對 Azure 儲存體，而且也 Data Lake Storage Gen2，此機制已擴充至檔案系統資源。

 如需具有 RBAC 之檔案許可權的詳細資訊，請參閱[Azure 角色型存取控制（RBAC）](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)。

如需具有 Acl 之檔案許可權的詳細資訊，請參閱檔案[和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>如何? 控制 Data Lake Storage Gen2 中資料的存取權嗎？

您的 HDInsight 叢集在 Data Lake Storage Gen2 中存取檔案的能力是透過受控識別來控制。 受控識別是在 Azure Active Directory （Azure AD）中註冊的身分識別，其認證是由 Azure 管理。 使用受控識別時，您不需要在 Azure AD 中註冊服務主體。 或維護認證，例如憑證。

Azure 服務有兩種類型的受控識別：系統指派和使用者指派。 HDInsight 會使用使用者指派的受控識別來存取 Data Lake Storage Gen2。 `user-assigned managed identity`會建立為獨立的 Azure 資源。 透過建立程序，Azure 會在所使用訂用帳戶信任的 Azure AD 租用戶中建立身分識別。 建立身分識別之後，即可將它指派給一個或多個 Azure 服務執行個體。

使用者指派的身分識別與獲指派此身分識別的 Azure 服務執行個體，兩者的生命週期分開管理。 如需受控識別的詳細資訊，請參閱適用[于 Azure 資源的受控識別如何使用？](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>如何? 將 Azure AD 使用者的許可權設定為使用 Hive 或其他服務來查詢 Data Lake Storage Gen2 中的資料嗎？

若要設定使用者查詢資料的許可權，請使用 Azure AD 安全性群組作為 Acl 中指派的主體。 請勿直接將檔案存取權限指派給個別使用者或服務主體。 透過 Azure AD 安全性群組來控制許可權的流程，您可以新增和移除使用者或服務主體，而不需要將 Acl 重新套用至整個目錄結構。 您只需要從適當的 Azure AD 安全性群組新增或移除使用者即可。 Acl 不會繼承，因此重新套用 Acl 必須更新每個檔案和子目錄的 ACL。

## <a name="access-files-from-the-cluster"></a>從叢集存取檔案

有數種方式可供您從 HDInsight 叢集存取 Data Lake Storage Gen2 中的檔案。

* **使用完整格式名稱**。 使用這種方法，您可以針對想要存取的檔案提供完整路徑。

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **使用簡短路徑格式**。 使用這種方法，您可以將路徑取代為叢集根目錄，如下所示：

    ```
    abfs:///<file.path>/
    ```

* **使用相對路徑**。 使用這種方法，您可以針對想要存取的檔案，只提供相對路徑。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>資料存取範例

範例是以連至叢集前端節點的[ssh](./hdinsight-hadoop-linux-use-ssh-unix.md)連線為基礎。 這些範例會使用這三個 URI 配置。 將`CONTAINERNAME`和`STORAGEACCOUNT`取代為相關的值

#### <a name="a-few-hdfs-commands"></a>一些 hdfs 命令

1. 在本機存放裝置上建立檔案。

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

1. 列出叢集存放區上的目錄內容。

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>建立 Hive 資料表

顯示三個檔案位置以供說明之用。 若是實際執行，請只使用其中一個`LOCATION`專案。

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
