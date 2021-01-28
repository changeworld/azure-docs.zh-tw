---
title: 使用共用存取簽章限制存取 - Azure HDInsight
description: 瞭解如何使用共用存取簽章來限制 HDInsight 存取儲存在 Azure Blob 儲存體中的資料。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/28/2020
ms.openlocfilehash: 647ac363dd81604f0a5a1d750d98a3349beb19c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931722"
---
# <a name="use-azure-blob-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>使用 Azure Blob 儲存體共用存取簽章來限制對 HDInsight 中資料的存取

HDInsight 具有與叢集相關聯之 Azure Blob 儲存體帳戶中資料的完整存取權。 您可以在 Blob 容器上使用共用存取簽章來限制對資料的存取。  (SAS) 的共用存取簽章是 Azure Blob 儲存體帳戶的一項功能，可讓您限制資料的存取權。 例如，提供資料的唯讀存取。

> [!IMPORTANT]  
> 對於使用 Apache Ranger 的解決方案，請考慮使用已加入網域的 HDInsight。 如需詳細資訊，請參閱[設定已加入網域的 HDInsight](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) 文件。

> [!WARNING]  
> HDInsight 必須有叢集預設儲存體的完整存取權。

## <a name="prerequisites"></a>Prerequisites

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](./hdinsight-hadoop-linux-use-ssh-unix.md)。

* 現有的 [儲存體容器](../storage/blobs/storage-quickstart-blobs-portal.md)。  

* 如果使用 PowerShell，您將需要 [Az 模組](/powershell/azure/) \(部分機器翻譯\)。

* 如果您想要使用 Azure CLI，但尚未安裝，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

* 如果使用 [Python](https://www.python.org/downloads/)，版本2.7 或更高版本。

* 如果使用 c #，Visual Studio 必須是2013版或更高版本。

* 儲存體帳戶的 URI 配置。 此配置 `wasb://` 適用于 Azure Blob 儲存體、 `abfs://` 適用于 Azure Data Lake Storage Gen2 或 `adl://` Azure Data Lake Storage Gen1。 如果已針對 Azure Blob 儲存體啟用安全傳輸，則 URI 會是 `wasbs://` 。

* 要新增共用存取簽章的現有 HDInsight 叢集。 如果沒有，您可以使用 Azure PowerShell 建立叢集，並在叢集建立期間新增共用存取簽章。

* 中的範例檔案 [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) 。 此儲存機制包含下列項目：

  * Visual Studio 專案，可以建立儲存體容器、預存原則，以及搭配 HDInsight 使用的 SAS
  * Python 指令碼，可以建立儲存體容器、預存原則，以及搭配 HDInsight 使用的 SAS
  * PowerShell 指令碼，可以建立 HDInsight 叢集，並將它設定為使用 SAS。 後續會使用更新的版本。
  * 範例檔案： `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>共用存取簽章

共用存取簽章有兩種格式：

* `Ad hoc`： SAS 的開始時間、到期時間和許可權都是在 SAS URI 上指定。

* `Stored access policy`：儲存的存取原則會在資源容器（例如 blob 容器）上定義。 原則可以用來管理一或多個共用存取簽章的限制。 當您將 SAS 與預存存取原則建立關聯時，SAS 會繼承為該預存存取原則所定義的限制 (開始時間、過期時間和權限)。

這兩種格式間的差異對於以下這一個重要案例而言相當重要：撤銷。 SAS 是一個 URL，因此取得 SAS 的任何人都可以使用它。 從誰開始要求它並不重要。 如果是公開發佈 SAS，則全世界的人都可以使用此 SAS。 散佈的 SAS 在發生以下四個情況其中之一之前都會持續有效：

1. 已到達 SAS 上指定的過期時間。

2. 已到達 SAS 參考的預存存取原則所指定的到期時間。 下列情節會導致過期時間到達：

    * 時間間隔已過。
    * 預存存取原則之過期時間修改為過去的時間。 改變過期時間是撤銷 SAS 的方法之一。

3. 已刪除 SAS 所參考之預存存取原則，這是撤銷 SAS 的另外一種方法。 如果您以相同的名稱重新建立預存存取原則，則先前原則的所有 SAS 權杖都是有效的 (如果 SAS 的到期時間尚未通過) 。 如果您打算撤銷 SAS，且如果您要使用未來的過期時間來重新建立存取原則，則務必使用不同的名稱。

4. 系統會重新產生用來建立 SAS 的帳戶金鑰。 重新產生金鑰會造成使用舊金鑰的所有應用程式驗證失敗。 將所有元件更新為新金鑰。

> [!IMPORTANT]  
> 共用存取簽章 URI 會與用來建立簽章的帳戶金鑰，以及相關聯的預存的存取原則 (如果有的話) 產生關聯。 如果未指定任何預存的存取原則，則撤銷共用存取簽章的唯一方式是變更帳戶金鑰。

建議您一律使用預存的存取原則。 使用預存原則時，可以視需求撤銷簽章或延長過期時間。 此文件的步驟使用預存存取原則來產生 SAS。

如需共用存取簽章的詳細資訊，請參閱 [了解 SAS 模型](../storage/common/storage-sas-overview.md)。

## <a name="create-a-stored-policy-and-sas"></a>建立預存原則和 SAS

儲存在每個方法結束時產生的 SAS 權杖。 權杖看起來會與下列輸出類似：

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>使用 PowerShell

`RESOURCEGROUP`將、 `STORAGEACCOUNT` 和取代為 `STORAGECONTAINER` 您現有儲存體容器的適當值。 將目錄變更為 `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` 或修改 `-File` 參數，以包含的絕對路徑 `Set-AzStorageblobcontent` 。 輸入下列 PowerShell 命令：

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>使用 Azure CLI

此區段中的變數使用是以 Windows 環境為基礎。 Bash 或其他環境將需要稍微變化。

1. 以 `STORAGEACCOUNT` `STORAGECONTAINER` 現有儲存體容器的適當值取代和。

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. 將取出的主鍵設定為變數，以供稍後使用。 取代為 `PRIMARYKEY` 先前步驟中的抓取值，然後輸入下列命令：

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. 將目錄變更為 `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` 或修改 `--file` 參數，以包含的絕對路徑 `az storage blob upload` 。 執行其餘的命令：

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name %AZURE_STORAGE_CONTAINER% --policy-name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>使用 Python

開啟檔案， `SASToken.py` 並 `storage_account_name` 將、和取代為 `storage_account_key` `storage_container_name` 您現有儲存體容器的適當值，然後執行腳本。

`pip install --upgrade azure-storage`如果您收到錯誤訊息，您可能需要執行 `ImportError: No module named azure.storage` 。

### <a name="using-c"></a>使用 C\#

1. 在 Visual Studio 中開啟解決方案。

2. 在方案總管中，以滑鼠右鍵按一下 **SASExample** 專案，然後選取 [ **屬性**]。

3. 選取 [設定]  ，並新增下列項目的值：

    |項目 |描述 |
    |---|---|
    |StorageConnectionString|您想要為其建立預存原則和 SAS 的儲存體帳戶的連接字串。 其格式應為 `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`，其中 `myaccount` 是儲存體帳戶名稱，而 `mykey` 是儲存體帳戶金鑰。|
    |ContainerName|您想要限制存取的儲存體帳戶中的容器。|
    |SASPolicyName|要建立的預存原則所要使用的名稱。|
    |FileToUpload|上傳至容器之檔案的路徑。|

4. 執行專案。 儲存 SAS 原則權杖、儲存體帳戶名稱和容器名稱。 將儲存體帳戶與您的 HDInsight 叢集相關聯時，會使用這些值。

## <a name="use-the-sas-with-hdinsight"></a>搭配 HDInsight 使用 SAS

建立 HDInsight 叢集時，您必須指定主要儲存體帳戶。 您也可以指定其他儲存體帳戶。 這兩種新增儲存體的方法都需要所使用的儲存體帳戶和容器的完整存取權。

使用共用存取簽章來限制容器存取。 將自訂專案新增至叢集的 **核心網站** 設定。 您可以使用 PowerShell 或在使用 Ambari 建立叢集之後，在叢集建立期間新增專案。

### <a name="create-a-cluster-that-uses-the-sas"></a>建立使用 SAS 的叢集

將、、、、 `CLUSTERNAME` `RESOURCEGROUP` 和取代 `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER` `STORAGEACCOUNT` `TOKEN` 為適當的值。 輸入 PowerShell 命令：

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> 出現 HTTP/s 或 SSH 使用者名稱和密碼提示時，您必須提供符合下列準則的密碼：
>
> * 長度必須小於 10 個字元。
> * 必須包含至少一個數字。
> * 必須包含至少一個非英數字元。
> * 必須包含至少一個大寫或小寫字元。

需要一段時間讓此指令碼完成，通常大約是 15 分鐘。 指令碼完成且沒有發生任何錯誤時，叢集即已建立。

### <a name="use-the-sas-with-an-existing-cluster"></a>對現有的叢集使用 SAS

如果您有現有的叢集，您可以使用下列步驟，將 SAS 新增至 **核心網站** 設定：

1. 開啟叢集的 Ambari Web UI。 此頁面的位址是 `https://YOURCLUSTERNAME.azurehdinsight.net`。 出現提示時，使用您建立叢集時所使用的 admin 名稱 (admin) 和密碼來驗證叢集。

1. 流覽至 **HDFS**  >    >    >  **預先定義的自訂核心網站**。

1. 展開 [ **自訂核心網站** ] 區段，並將它滾動至結尾，然後選取 [ **新增屬性 ...**]。使用下列值作為索引 **鍵** 和 **值**：

    * **機碼**：`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **值**：稍早執行的其中一個方法所傳回的 SAS。

    取代 `CONTAINERNAME` 為您搭配 c # 或 SAS 應用程式使用的容器名稱。 `STORAGEACCOUNTNAME`以您使用的儲存體帳戶名稱取代。

    選取 [ **新增** ] 以儲存此索引鍵和值

1. 選取 [ **儲存** ] 按鈕以儲存設定變更。 出現提示時，請新增變更的描述 ( [新增 SAS 儲存體存取權]，例如) 然後選取 [ **儲存**]。

    當變更完成時，請選取 **[確定]** 。

   > [!IMPORTANT]  
   > 您必須重新啟動數個服務，變更才會生效。

1. [ **重新開機** ] 下拉式清單隨即出現。 從下拉式清單中選取 [ **重新開機所有受影響** 的]，然後 __確認 [全部重新開機__]。

    針對 **MapReduce2** 和 **YARN** 重複此程式。

1. 這些項目重新啟動之後，選取每一個項目，並從 [服務動作] 下拉式清單停用維護模式。

## <a name="test-restricted-access"></a>測試限制的存取

使用下列步驟來確認您只能讀取和列出 SAS 儲存體帳戶上的專案。

1. 連接到叢集。 `CLUSTERNAME`以您的叢集名稱取代，然後輸入下列命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 若要列出容器中的內容物，請使用提示中的下列命令：

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    `SASCONTAINER`以為 SAS 儲存體帳戶所建立的容器名稱取代。 取代為 `SASACCOUNTNAME` 用於 SAS 的儲存體帳戶名稱。

    此清單包含容器與 SAS 建立時上傳的檔案。

3. 使用下列命令以確認您可以讀取檔案的內容。 取代 `SASCONTAINER` 和 `SASACCOUNTNAME` 上一個步驟中的。 取代 `sample.log` 為先前命令中所顯示的檔案名：

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    此命令會列出檔案的內容。

4. 使用下列命令將檔案下載到本機檔案系統：

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    此命令會將檔案下載到本機檔案，名為 **testfile.txt**。

5. 使用下列命令將本機檔案上傳至 SAS 儲存體上的新檔案，名為 **testupload.txt** ：

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    您會收到類似以下文字的訊息：

    ```output
    put: java.io.IOException
    ```

    因為儲存體位置僅限讀取+列出，所以會發生此錯誤。 使用下列命令將資料放在叢集的預設儲存體，它是可寫入的：

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    此時，作業應該已順利完成。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何將受限存取儲存體新增至您的 HDInsight 叢集，瞭解如何在您的叢集上使用資料的其他方法：

* [搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [授權 Apache Ambari 檢視的使用者](hdinsight-authorize-users-to-ambari.md)
