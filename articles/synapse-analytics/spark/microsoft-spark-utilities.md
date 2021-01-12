---
title: Microsoft Spark 公用程式簡介
description: 教學課程： Azure Synapse Analytics 筆記本中的 MSSparkutils
author: ruxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: c681195a60329320b875cc06919e9440b65eb9e5
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120235"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Microsoft Spark 公用程式簡介

Microsoft Spark 公用程式 (MSSparkUtils) 是一種內建套件，可協助您輕鬆地執行一般工作。 您可以使用 MSSparkUtils 來處理檔案系統、取得環境變數，以及處理秘密。 MSSparkUtils 可在 `PySpark (Python)` 、 `Scala` 和 `.NET Spark (C#)` 筆記本以及 Synapse 管線中使用。

## <a name="pre-requisites"></a>必要條件

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>設定 Azure Data Lake Storage Gen2 的存取權 

Synapse 筆記本使用 Azure active directory (Azure AD) 傳遞來存取 ADLS Gen2 帳戶。 您需要是 **Blob 儲存體參與者** ，才能存取 ADLS Gen2 帳戶 (或資料夾) 。 

Synapse 管線會使用 (MSI) 的工作區身分識別來存取儲存體帳戶。 若要在管線活動中使用 MSSparkUtils，您的工作區身分識別必須是 **Blob 儲存體參與者** ，才能存取 ADLS Gen2 帳戶 (或資料夾) 。

請遵循下列步驟，以確定您的 Azure AD 和工作區 MSI 可以存取 ADLS Gen2 帳戶：
1. 開啟 [Azure 入口網站](https://portal.azure.com/) 以及您想要存取的儲存體帳戶。 您可以流覽至您想要存取的特定容器。
2. 從左面板中選取 [ **存取控制] (IAM)** 。
3. 將 **您的 Azure AD 帳戶** 和 **工作區身分識別** (與您的工作區名稱相同) 至儲存體帳戶上的 **儲存體 Blob 資料參與者** 角色（如果尚未指派）。 
4. 選取 [儲存]  。

您可以透過下列 URL 使用 Synapse Spark 存取 ADLS Gen2 上的資料：

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>設定 Azure Blob 儲存體的存取權 

Synapse 利用 **(SAS) 的共用存取** 簽章來存取 Azure Blob 儲存體。 為了避免在程式碼中公開 SAS 金鑰，建議您在 Synapse 工作區中建立新的連結服務，以存取您想要存取的 Azure Blob 儲存體帳戶。

請遵循下列步驟，為 Azure Blob 儲存體帳戶新增連結服務：

1. 開啟 [Azure Synapse Studio](https://web.azuresynapse.net/)。
2. 從左側面板中選取 [**管理**]，然後選取 [**外部連接**] 底下的 [**連結服務**]。
3. 在右側的 [**新增連結服務**] 面板中搜尋 **Azure Blob 儲存體**。
4. 選取 [繼續]。
5. 選取要存取的 Azure Blob 儲存體帳戶，並設定連結的服務名稱。 建議使用 **驗證方法** 的 **帳戶金鑰**。
6. 選取 [ **測試連接** ] 以驗證設定是否正確。
7. 選取 [先 **建立** ]，然後按一下 [ **全部發佈** ] 儲存變更。 

您可以透過下列 URL，透過 Synapse Spark 存取 Azure Blob 儲存體上的資料：

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

以下是程式碼範例：


:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end
 
###  <a name="configure-access-to-azure-key-vault"></a>設定 Azure Key Vault 的存取權

您可以將 Azure Key Vault 新增為連結服務，以在 Synapse 中管理您的認證。 遵循下列步驟將 Azure Key Vault 新增為 Synapse 連結服務：
1. 開啟 [Azure Synapse Studio](https://web.azuresynapse.net/)。
2. 從左側面板中選取 [**管理**]，然後選取 [**外部連接**] 底下的 [**連結服務**]。
3. 在右側的 [**新增連結服務**] 面板中搜尋 **Azure Key Vault** 。
4. 選取要存取的 Azure Key Vault 帳戶，並設定連結的服務名稱。
5. 選取 [ **測試連接** ] 以驗證設定是否正確。
6. 選取 [先 **建立** ]，然後按一下 [ **全部發行** ] 以儲存您的變更。 

Synapse 筆記本使用 Azure active directory (Azure AD) 傳遞來存取 Azure Key Vault。 Synapse 管線會使用 (MSI) 的工作區身分識別來存取 Azure Key Vault。 為了確保您的程式碼在筆記本和 Synapse 管線中都能運作，建議您授與 Azure AD 帳戶和工作區身分識別的秘密存取權限。

請遵循下列步驟，將密碼存取權授與您的工作區身分識別：
1. 開啟 [Azure 入口網站](https://portal.azure.com/) 以及您想要存取的 Azure Key Vault。 
2. 從左面板中選取 **存取原則** 。
3. 選取 [ **新增存取原則**]： 
    - 選擇 [ **金鑰]、[秘密]、[& 憑證管理** ] 作為設定範本。
    - 選取 **您的 Azure AD 帳戶** 和 **您的工作區身分識別** (與選取主體中的工作區名稱) 相同，或確定已獲指派。 
4. 選取 [ **選取** 並 **新增**]。
5. 選取 [ **儲存** ] 按鈕以認可變更。  

## <a name="file-system-utilities"></a>檔案系統公用程式

`mssparkutils.fs` 提供使用各種檔案系統的公用程式，包括 Azure Data Lake Storage Gen2 (ADLS Gen2) 和 Azure Blob 儲存體。 請務必設定 [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) 的存取權，並適當地 [Azure Blob 儲存體](#configure-access-to-azure-blob-storage) 。

執行下列命令，以取得可用方法的總覽：

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

產生結果：
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>列出檔案
列出目錄的內容。


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>檢視檔案內容
傳回檔案屬性，包括檔案名、檔案路徑、檔案大小，以及它是否為目錄和檔案。

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>建立新目錄

建立指定的目錄（如果不存在），以及任何必要的父目錄。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>複製檔案

複製檔案或目錄。 支援跨檔案系統複製。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>預覽檔案內容

傳回指定檔案的第一個 ' maxBytes ' 個位元組，以 UTF-8 編碼的字串。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>移動檔案

移動檔案或目錄。 支援跨檔案系統移動。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>寫入檔案

將指定的字串寫出至檔案，並以 UTF-8 編碼。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>將內容附加至檔案

將指定的字串附加至檔案，並以 UTF-8 編碼。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>刪除檔案或目錄

移除檔案或目錄。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>認證公用程式

您可以使用 MSSparkUtils 認證公用程式來取得連結服務的存取權杖，以及管理 Azure Key Vault 中的密碼。 

執行下列命令以取得可用方法的總覽：

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

取得結果：

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>取得權杖

傳回指定物件的 Azure AD 權杖，名稱 (選擇性) 。 下表列出所有可用的物件類型： 

|物件類型|物件金鑰|
|--|--|
|物件解析類型|收件者|
|儲存體物件資源|空間|
|資料倉儲物件資源|DW|
|Data Lake 物件資源|'AzureManagement'|
|保存庫物件資源|DataLakeStore|
|Azure OSSDB 物件資源|'AzureOSSDB'|
|Azure Synapse 資源|Synapse|
|Azure Data Factory 資源|放下|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>驗證權杖

如果 token 尚未過期，則傳回 true。

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>取得連結服務的連接字串或認證

傳回連結服務的連接字串或認證。 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>使用工作區身分識別取得秘密

使用工作區身分識別，針對指定的 Azure Key Vault 名稱、秘密名稱和連結的服務名稱，傳回 Azure Key Vault 秘密。 請確定您已適當地設定 [Azure Key Vault](#configure-access-to-azure-key-vault) 的存取權。

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>使用使用者認證取得秘密

使用使用者認證，傳回指定 Azure Key Vault 名稱、秘密名稱和連結服務名稱的 Azure Key Vault 秘密。 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

### <a name="put-secret-using-workspace-identity"></a>使用工作區身分識別來放置秘密

使用工作區身分識別，為指定的 Azure Key Vault 名稱、秘密名稱和連結服務名稱放置 Azure Key Vault 秘密。 請確定您已適當地設定 [Azure Key Vault](#configure-access-to-azure-key-vault) 的存取權。

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="put-secret-using-user-credentials"></a>使用使用者認證來放置秘密

使用使用者認證，為指定的 Azure Key Vault 名稱、秘密名稱和連結服務名稱放置 Azure Key Vault 秘密。 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


## <a name="environment-utilities"></a>環境公用程式 

執行下列命令以取得可用方法的總覽：

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

取得結果：
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>取得使用者名稱

傳回目前的使用者名稱。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>取得使用者識別碼

傳回目前的使用者識別碼。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>取得作業識別碼

傳回作業識別碼。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>取得工作區名稱

傳回工作區名稱。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>取得集區名稱

傳回 Spark 集區名稱。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>取得叢集識別碼

傳回目前的叢集識別碼。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>後續步驟

- [查看 Synapse 範例筆記本](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [快速入門：使用 Web 工具在 Azure Synapse Analytics 中建立 Apache Spark 集區](../quickstart-apache-spark-notebook.md)
- [什麼是 Azure Synapse Analytics 中的 Apache Spark](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
