---
title: 使用共用存取簽章限制存取 - Azure HDInsight
description: 深入了解使用共用存取簽章限制 HDInsight 對儲存在 Azure 儲存體 blob 中的資料的存取。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: d68f7dc6368c2b3de7f26f2946c5fb47237a820d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313923"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>使用 Azure 儲存體共用存取簽章來限制 HDInsight 對資料的存取

HDInsight 對於與叢集建立關聯之 Azure 儲存體帳戶中的資料具有完整存取權。 您可以在 Blob 容器上使用共用存取簽章來限制對資料的存取。 共用存取簽章 (SAS) 是一種 Azure 儲存體帳戶功能，可讓您限制資料的存取權。 例如，提供資料的唯讀存取。

> [!IMPORTANT]  
> 對於使用 Apache Ranger 的解決方案，請考慮使用已加入網域的 HDInsight。 如需詳細資訊，請參閱[設定已加入網域的 HDInsight](./domain-joined/apache-domain-joined-configure.md) 文件。

> [!WARNING]  
> HDInsight 必須有叢集預設儲存體的完整存取權。

## <a name="prerequisites"></a>Prerequisites

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](./hdinsight-hadoop-linux-use-ssh-unix.md)。

* 現有的[儲存容器](../storage/blobs/storage-quickstart-blobs-portal.md)。  

* 如果使用 PowerShell,則需要[Az 模組](https://docs.microsoft.com/powershell/azure/overview)。

* 如果想要使用 Azure CLI,但尚未安裝它,請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

* 如果使用[Python](https://www.python.org/downloads/), 版本 2.7 或更高版本。

* 如果使用 C#,Visual Studio 必須是版本 2013 或更高版本。

* 儲存帳號的[URI 專案](./hdinsight-hadoop-linux-information.md#URI-and-scheme)。 此方案適用於`wasb://`Azure 儲存、Azure`abfs://`數據湖`adl://`儲存第 2 代或 Azure 數據湖儲存第 1 代。 如果已對 Azure 儲存體啟用安全傳輸，URI 會是 `wasbs://`。 另請參閱[安全傳輸](../storage/common/storage-require-secure-transfer.md)。

* 要向其添加共享訪問簽名的現有 HDInsight 群集。 如果沒有，您可以使用 Azure PowerShell 建立叢集，並在叢集建立期間新增共用存取簽章。

* 來自[https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)的範例檔。 此儲存機制包含下列項目：

  * Visual Studio 專案，可以建立儲存體容器、預存原則，以及搭配 HDInsight 使用的 SAS
  * Python 指令碼，可以建立儲存體容器、預存原則，以及搭配 HDInsight 使用的 SAS
  * PowerShell 指令碼，可以建立 HDInsight 叢集，並將它設定為使用 SAS。 更新的版本在下面進一步使用。
  * 範例檔:`hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>共用存取簽章

共用存取簽章有兩種格式：

* `Ad hoc`:SAS 的開始時間、到期時間和許可權都指定在 SAS URI 上。

* `Stored access policy`:儲存的存取策略在資源容器(如 blob 容器)上定義。 原則可以用來管理一或多個共用存取簽章的限制。 當您將 SAS 與預存存取原則建立關聯時，SAS 會繼承為該預存存取原則所定義的限制 (開始時間、過期時間和權限)。

這兩種格式間的差異對於以下這一個重要案例而言相當重要：撤銷。 SAS 是一個 URL,因此任何獲得 SAS 的人都可以使用它。 誰要求它開始並不重要。 如果是公開發佈 SAS，則全世界的人都可以使用此 SAS。 散佈的 SAS 在發生以下四個情況其中之一之前都會持續有效：

1. 已到達 SAS 上指定的過期時間。

2. 已到達 SAS 參考的預存存取原則所指定的到期時間。 下列情節會導致過期時間到達：

    * 時間間隔已過。
    * 預存存取原則之過期時間修改為過去的時間。 改變過期時間是撤銷 SAS 的方法之一。

3. 已刪除 SAS 所參考之預存存取原則，這是撤銷 SAS 的另外一種方法。 如果重新建立具有相同名稱的儲存存取策略,則上一個策略的所有 SAS 令牌都有效(如果 SAS 上的到期時間尚未過去)。 如果您打算撤銷 SAS，且如果您要使用未來的過期時間來重新建立存取原則，則務必使用不同的名稱。

4. 系統會重新產生用來建立 SAS 的帳戶金鑰。 重新產生金鑰會造成使用舊金鑰的所有應用程式驗證失敗。 將所有元件更新為新金鑰。

> [!IMPORTANT]  
> 共用存取簽章 URI 會與用來建立簽章的帳戶金鑰，以及相關聯的預存的存取原則 (如果有的話) 產生關聯。 如果未指定任何預存的存取原則，則撤銷共用存取簽章的唯一方式是變更帳戶金鑰。

建議您一律使用預存的存取原則。 使用預存原則時，可以視需求撤銷簽章或延長過期時間。 此文件的步驟使用預存存取原則來產生 SAS。

如需共用存取簽章的詳細資訊，請參閱 [了解 SAS 模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

## <a name="create-a-stored-policy-and-sas"></a>建立儲存的原則與 SAS

保存在每種方法末尾生成的 SAS 權杖。 權杖會類似於以下輸出:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>使用 PowerShell

將`RESOURCEGROUP``STORAGEACCOUNT`和`STORAGECONTAINER`替換為現有存儲容器的相應值。 將目錄更改為`hdinsight-dotnet-python-azure-storage-shared-access-signature-master`或`-File`修改 參數`Set-AzStorageblobcontent`以包含的絕對路徑。 輸入以下 PowerShell 指令:

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

本節中變數的使用基於 Windows 環境。 bash 或其他環境需要細微的變化。

1. 將`STORAGEACCOUNT`替換為`STORAGECONTAINER`和與現有存儲容器的相應值。

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

2. 將檢索到的主鍵設置為變數,以便以後使用。 取代為`PRIMARYKEY`上一步中檢索的值,然後輸入以下命令:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. 將目錄更改為`hdinsight-dotnet-python-azure-storage-shared-access-signature-master`或`--file`修改 參數`az storage blob upload`以包含的絕對路徑。 執行其餘指令:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>使用 Python

開啟檔案`SASToken.py`,`storage_container_name`用`storage_account_name`現有`storage_account_key`儲存容器的相應值取代和,然後執行文稿。

如果您收到錯誤訊息`ImportError: No module named azure.storage`,則可能`pip install --upgrade azure-storage`需要執行 。

### <a name="using-c"></a>使用 C\#

1. 在 Visual Studio 中開啟解決方案。

2. 在解決方案資源管理員中,右鍵按**一下 SASexample**專案並選擇**屬性**。

3. 選取 [設定] **** ，並新增下列項目的值：

    |Item |描述 |
    |---|---|
    |StorageConnectionString|您想要為其建立預存原則和 SAS 的儲存體帳戶的連接字串。 其格式應為 `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`，其中 `myaccount` 是儲存體帳戶名稱，而 `mykey` 是儲存體帳戶金鑰。|
    |ContainerName|您想要限制存取的儲存體帳戶中的容器。|
    |SAS 策略名稱|要建立的預存原則所要使用的名稱。|
    |檔案上傳|上傳至容器之檔案的路徑。|

4. 執行專案。 儲存 SAS 原則權杖、儲存體帳戶名稱和容器名稱。 將儲存體帳戶與您的 HDInsight 叢集相關聯時，會使用這些值。

## <a name="use-the-sas-with-hdinsight"></a>搭配 HDInsight 使用 SAS

創建 HDInsight 群集時,必須指定主存儲帳戶。 您還可以指定其他儲存帳戶。 這兩種新增儲存體的方法都需要所使用的儲存體帳戶和容器的完整存取權。

使用共享訪問簽名限制容器訪問。 向群集**的核心網站**配置添加自定義條目。 您可以使用 PowerShell 或在使用 Ambari 創建群集後添加該條目。

### <a name="create-a-cluster-that-uses-the-sas"></a>建立使用 SAS 的叢集

`RESOURCEGROUP`用`CLUSTERNAME`適當的值`DEFAULTSTORAGEACCOUNT``STORAGECONTAINER``STORAGEACCOUNT`替換`TOKEN`、、、、和。 輸入 PowerShell 指令:

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

如果您有現有群組,則可以使用以下步驟將 SAS 加入到**核心站點**設定:

1. 開啟叢集的 Ambari Web UI。 此頁面的位址是 `https://YOURCLUSTERNAME.azurehdinsight.net`。 出現提示時，使用您建立叢集時所使用的 admin 名稱 (admin) 和密碼來驗證叢集。

1. 瀏覽到**HDFS** > **設定** > **進階** > **自訂核心站台**。

1. 展開**自定義核心網站**部分,滾動到末尾,然後選擇 **"添加屬性..."****鍵**與**值**使用以下值:

    * **金鑰**:`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **值**: 由前面執行的方法之一返回的 SAS。

    替換為`CONTAINERNAME`與 C# 或 SAS 應用程式一起使用的容器名稱。 取代為`STORAGEACCOUNTNAME`您使用的儲存帳戶名稱。

    選擇 **「新增」** 以儲存此鍵與值

1. 選擇 **「儲存**」按鈕以儲存設定更改。 當出現提示時,添加更改的說明(例如"添加 SAS 儲存存取許可權"),然後選擇 **"儲存**"。

    完成更改後選擇 **"確定**」。

   > [!IMPORTANT]  
   > 您必須重新啟動數個服務，變更才會生效。

1. 將顯示 **「重新啟動**下拉清單」。 從下拉清單中**選擇「重新啟動所有受影響的」 ,** 然後__確認全部重新啟動__。

    對**MapReduce2**和**YARN**重複此過程。

1. 這些項目重新啟動之後，選取每一個項目，並從 [服務動作]**** 下拉式清單停用維護模式。

## <a name="test-restricted-access"></a>測試限制的存取

使用以下步驟驗證是否只能讀取和列出 SAS 儲存帳戶上的專案。

1. 連接到群集。 取代為`CLUSTERNAME`群組名稱,然後輸入以下指令:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 若要列出容器中的內容物，請使用提示中的下列命令：

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    替換為`SASCONTAINER`SAS 儲存帳戶創建的容器名稱。 替換為`SASACCOUNTNAME`用於 SAS 的儲存帳戶的名稱。

    此清單包含容器與 SAS 建立時上傳的檔案。

3. 使用下列命令以確認您可以讀取檔案的內容。 替換`SASCONTAINER``SASACCOUNTNAME`和 ,如上一步所述。 取代為`sample.log`上一指令中顯示的檔案名稱:

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

        put: java.io.IOException

    因為儲存體位置僅限讀取+列出，所以會發生此錯誤。 使用下列命令將資料放在叢集的預設儲存體，它是可寫入的：

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    此時，作業應該已順利完成。

## <a name="next-steps"></a>後續步驟

現在,您已經瞭解如何將受限存取儲存添加到 HDInsight 群集中,請學習其他處理群集上資料的方法:

* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [搭配 HDInsight 使用 MapReduce](hadoop/hdinsight-use-mapreduce.md)