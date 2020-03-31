---
title: 搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2
description: 瞭解如何將 Azure 資料存儲第 2 代與 Azure HDInsight 群集一起使用。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272288"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2

Azure 資料湖存儲 Gen2 是一種雲存儲服務，專用於 Azure Blob 存儲，用於大資料分析。 資料存儲源 Gen2 結合了 Azure Blob 存儲和 Azure 資料存儲第 1 代的功能。 生成的服務提供了 Azure Data Lake 存儲 Gen1 的功能，例如檔案系統語義、目錄級和檔級安全性以及可擴充性，以及低成本、分層存儲、高可用性和災害復原功能從 Azure Blob 存儲。

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 可用性

資料存儲湖存儲 Gen2 可作為幾乎所有 Azure HDInsight 群集類型的存儲選項作為預設帳戶和其他存儲帳戶提供。 但是，HBase 只能有一個資料存儲第 2 代帳戶。

有關使用資料湖存儲 Gen2 的群集創建選項的完整比較，請參閱[比較存儲選項以與 Azure HDInsight 群集一起使用](hdinsight-hadoop-compare-storage-options.md)。

> [!Note]  
> 選擇資料存儲第 2 代作為**主存儲類型**後，不能選擇資料存儲第 1 代帳戶作為附加存儲。

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>通過 Azure 門戶創建具有資料存儲湖存儲 Gen2 的群集

要創建使用資料存儲庫 Gen2 進行存儲的 HDInsight 群集，請按照以下步驟配置資料湖存儲 Gen2 帳戶。

### <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

建立使用者指派的受控識別 (如果您還沒有的話)。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在左上角按一下"**創建資源**"。
1. 在搜索框中，鍵入**分配的使用者**，然後按一下 **"使用者分配託管標識**"。
1. 按一下 **[建立]**。
1. 輸入託管標識的名稱，選擇正確的訂閱、資源組和位置。
1. 按一下 **[建立]**。

有關託管標識在 Azure HDInsight 中的工作方式的詳細資訊，請參閱[Azure HDInsight 中的託管標識](hdinsight-managed-identities.md)。

![建立使用者指派的受控識別](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>建立 Data Lake Storage Gen2 帳戶

建立 Azure Data Lake Storage Gen2 儲存體帳戶。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在左上角按一下"**創建資源**"。
1. 在搜索框中，鍵入**存儲**並按一下 **"存儲帳戶**"。
1. 按一下 **[建立]**。
1. 在 **"創建存儲帳戶"** 螢幕上：
    1. 選擇正確的訂閱和資源組。
    1. 輸入資料湖存儲第 2 代帳戶的名稱。 有關存儲帳戶命名約定的詳細資訊，請參閱為[Azure 資源命名約定](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage)。
    1. 按一下"**高級"** 選項卡。
    1. 按一下 **"在資料湖存儲 Gen2**下**階層命名空間**旁邊**啟用**"。
    1. 按一下 **"審閱" = 創建**。
    1. 按一下 **"創建"**

有關創建存儲帳戶期間的其他選項的詳細資訊，請參閱[快速入門：創建 Azure 資料湖存儲 Gen2 存儲帳戶](../storage/blobs/data-lake-storage-quickstart-create-account.md)。

![顯示在 Azure 入口網站建立儲存體帳戶的螢幕擷取畫面](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>設置資料湖存儲 Gen2 帳戶上的託管標識的許可權

將託管標識分配給存儲帳戶上的**存儲 Blob 資料擁有者**角色。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。
1. 選擇存儲帳戶，然後選擇 **"存取控制 "（IAM）** 以顯示帳戶的存取控制設置。 選取 [角色指派]**** 索引標籤，以查看角色指派的清單。

    ![顯示儲存體存取控制設定的螢幕擷取畫面](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. 選擇 **"添加角色指派"** 按鈕以添加新角色。
1. 在 **"添加角色指派"** 視窗中，選擇 **"存儲 Blob 資料擁有者"** 角色。 然後，選取包含受控識別和儲存體帳戶的訂用帳戶。 接著，搜尋並找出您先前建立的使用者指派受控識別。 最後，選擇託管標識，該標識將列在 **"選定成員**"下。

    ![顯示如何指派 RBAC 角色的螢幕擷取畫面](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. 選取 [儲存]****。 您選擇的使用者分配標識現在列在所選角色下。
1. 完成此初始設定後，您可以透過入口網站建立叢集。 此叢集必須與儲存體帳戶位在相同的 Azure 區域中。 在群集創建功能表的 **"存儲"** 選項卡中，選擇以下選項：

    * 對於**主存儲類型**，請選擇**Azure 資料存儲湖存儲 Gen2**。
    * 在**主存儲帳戶**下，搜索並選擇新創建的資料存儲存儲 Gen2 存儲帳戶。

    * 在 **"標識"** 下，選擇新創建的使用者分配的託管標識。

        ![搭配 Azure HDInsight 使用 Data Lake Storage Gen2 的儲存體設定](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * 要添加輔助資料存儲湖存儲 Gen2 帳戶，只需將之前創建的託管標識分配給要添加的新資料存儲湖存儲 Gen2 存儲帳戶即可。 請注意，不支援通過 HDInsight 上的"額外存儲帳戶"選項卡添加輔助資料存儲庫 Gen2 帳戶。
    > * 您可以在 HDInsight 使用的 Azure 存儲帳戶上啟用 RA-GRS 或 RA-ZRS。 但是，不支援針對 RA-GRS 或 RA-ZRS 輔助終結點創建群集。


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>通過 Azure CLI 創建具有資料存儲湖存儲第 2 代的群集

您可以[下載示例範本檔](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)並[下載示例參數檔](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)。 在使用下面的範本和 Azure CLI 程式碼片段之前，用正確的值替換以下預留位置：

| 預留位置 | 描述 |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure 訂閱的 ID |
| `<RESOURCEGROUPNAME>` | 希望創建新群集和存儲帳戶的資源組。 |
| `<MANAGEDIDENTITYNAME>` | 將在 Azure 資料湖存儲 Gen2 帳戶上授予許可權的託管標識的名稱。 |
| `<STORAGEACCOUNTNAME>` | 將創建的新 Azure 資料存儲庫存儲 Gen2 帳戶。 |
| `<CLUSTERNAME>` | 您的 HDInsight 叢集名稱。 |
| `<PASSWORD>` | 您選擇的密碼用於使用 SSH 和 Ambari 儀表板登錄到群集。 |

下面的程式碼片段執行以下初始步驟：

1. 登錄到 Azure 帳戶。
1. 設置將執行創建操作的活動訂閱。
1. 為新部署活動創建新的資源組。
1. 創建使用者分配的託管標識。
1. 向 Azure CLI 添加擴展，以便使用資料湖存儲 Gen2 的功能。
1. 使用`--hierarchical-namespace true`標誌創建新的資料存儲湖存儲 Gen2 帳戶。

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

接下來，登錄到門戶。 將新的使用者分配的託管標識添加到存儲帳戶上的**存儲 Blob 資料參與者**角色，如["使用 Azure 門戶](hdinsight-hadoop-use-data-lake-storage-gen2.md)"下的步驟 3 中所述。

為使用者分配的託管標識分配角色後，請使用以下程式碼片段部署範本。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>通過 Azure PowerShell 創建具有資料存儲湖存儲第 2 代的群集

當前不支援使用 PowerShell 創建具有 Azure 資料湖存儲 Gen2 的 HDInsight 群集。

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight 中資料存儲第 2 代的存取控制

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 支援哪些種類的權限？

資料存儲庫 Gen2 使用一種存取控制模型，該模型同時支援基於角色的存取控制 （RBAC） 和類似 POSIX 的存取控制清單 （ACL）。 資料存儲庫 Gen1 僅支援存取控制清單，用於控制對資料的訪問。

RBAC 使用角色指派有效地將許可權集應用於 Azure 資源的使用者、組和服務主體。 一般而言，這些 Azure 資源會限定於最上層資源 (例如 Azure 儲存體帳戶)。 對於 Azure 存儲以及資料存儲存儲 Gen2，此機制已擴展到檔案系統資源。

 有關使用 RBAC 的檔許可權的詳細資訊，請參閱[Azure 基於角色的存取控制 （RBAC）。](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)

有關使用 ACL 的檔許可權的詳細資訊，請參閱[檔和目錄上的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>如何控制對資料存儲 Gen2 中資料的訪問？

您的 HDInsight 群集訪問資料存儲 Gen2 中檔的能力通過託管標識進行控制。 託管標識是在 Azure 活動目錄 （Azure AD） 中註冊的標識，其憑據由 Azure 管理。 使用託管標識時，無需在 Azure AD 中註冊服務主體或維護憑據（如證書）。

Azure 服務有兩種類型的託管標識：系統分配和使用者分配。 HDInsight 使用使用者分配的託管標識來訪問資料湖存儲 Gen2。 使用者指派的受控識別會以獨立 Azure 資源的形式建立。 透過建立程序，Azure 會在所使用訂用帳戶信任的 Azure AD 租用戶中建立身分識別。 建立身分識別之後，即可將它指派給一個或多個 Azure 服務執行個體。

使用者指派的身分識別與獲指派此身分識別的 Azure 服務執行個體，兩者的生命週期分開管理。 有關託管標識的詳細資訊，請參閱 Azure[資源的託管標識如何工作？](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>如何設置 Azure AD 使用者使用 Hive 或其他服務在資料存儲第 2 代中查詢資料的許可權？

要設置使用者查詢資料的許可權，請使用 Azure AD 安全性群組作為 ACL 中分配的主體。 不要直接向單個使用者或服務主體分配檔存取權限。 使用 Azure AD 安全性群組控制許可權流時，可以添加和刪除使用者或服務主體，而無需將 ACL 重新應用於整個目錄結構。 您只需要從適當的 Azure AD 安全性群組新增或移除使用者即可。 ACL 不會繼承，因此重新應用 ACL 需要更新每個檔和子目錄中的 ACL。

## <a name="access-files-from-the-cluster"></a>從叢集存取檔案

有幾種方法可以從 HDInsight 群集訪問資料存儲 Gen2 中的檔。

* **使用完整格式名稱**。 使用這種方法，您可以針對想要存取的檔案提供完整路徑。

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **使用簡短路徑格式**。 使用此方法，您將群集根的路徑替換為：

    ```
    abfs:///<file.path>/
    ```

* **使用相對路徑**。 使用這種方法，您可以針對想要存取的檔案，只提供相對路徑。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>資料訪問示例

示例基於與群集頭節點的[ssh 連接](./hdinsight-hadoop-linux-use-ssh-unix.md)。 這些示例使用所有三個 URI 方案。 `STORAGEACCOUNT`替換為`CONTAINERNAME`相關值

#### <a name="a-few-hdfs-commands"></a>幾個 hdfs 命令

1. 在本機存放區上創建檔。

    ```bash
    touch testFile.txt
    ```

1. 在群集存儲上創建目錄。

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 將資料從本機存放區複製到群集存儲。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 在群集存儲上列出目錄內容。

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>創建蜂巢表

顯示三個檔位置以進行說明。 對於實際執行，僅使用其中一個`LOCATION`條目。

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
* [教程：使用 Azure HDInsight 中的互動式查詢擷取、轉換和下載資料](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
