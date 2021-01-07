---
title: 使用 Azure Data Lake Storage 查詢加速來篩選資料 |Microsoft Docs
description: 使用查詢加速從儲存體帳戶取出資料的子集。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cc63a9787a3611f28e6466564735d029bf65b757
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968393"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>使用 Azure Data Lake Storage 查詢加速來篩選資料

本文說明如何使用查詢加速，從您的儲存體帳戶取出資料的子集。 

查詢加速可讓應用程式和分析架構藉由只抓取執行指定作業所需的資料，來大幅優化資料處理。 若要深入瞭解，請參閱 [Azure Data Lake Storage 查詢加速](data-lake-storage-query-acceleration.md)。

## <a name="prerequisites"></a>必要條件

- 若要存取 Azure 儲存體，您需要有 Azure 訂用帳戶。 如果您還沒有訂用帳戶，請先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。

- **一般用途 v2** 儲存體帳戶。 請參閱 [建立儲存體帳戶](../common/storage-account-create.md)。

- 選擇索引標籤以查看任何 SDK 特定的必要條件。

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  不適用

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java 開發套件 (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) 8 版或更新版本

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > 本文假設您已使用 Apache Maven 建立 JAVA 專案。 如需如何使用 Apache Maven 建立專案的範例，請參閱 [設定](storage-quickstart-blobs-java.md#setting-up)。
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 或更新版本。

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  使用 Node.js SDK 不需要額外的必要條件。

---

## <a name="enable-query-acceleration"></a>啟用查詢加速

若要使用查詢加速，您必須向您的訂用帳戶註冊查詢加速功能。 確認功能已註冊之後，您必須註冊 Azure 儲存體資源提供者。 

### <a name="step-1-register-the-query-acceleration-feature"></a>步驟1：註冊查詢加速功能

若要使用查詢加速，您必須先向訂用帳戶註冊查詢加速功能。 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 開啟 Windows PowerShell 命令視窗。

1. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

   ```powershell
   Connect-AzAccount
   ```

2. 如果您的身分識別與多個訂用帳戶相關聯，請設定您的有效訂用帳戶。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   使用訂閱識別碼取代 `<subscription-id>` 預留位置值。

3. 使用 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 命令註冊查詢加速功能。

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 開啟 [Azure Cloud Shell](../../cloud-shell/overview.md)，或如果您已在本機[安裝](/cli/azure/install-azure-cli) Azure CLI，請開啟命令主控台應用程式，例如 Windows PowerShell。

2. 如果您的身分識別與多個訂用帳戶相關聯，請將使用中的訂用帳戶設定為儲存體帳戶的訂用帳戶。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   使用訂閱識別碼取代 `<subscription-id>` 預留位置值。

3. 使用 [az feature register](/cli/azure/feature#az-feature-register) 命令註冊查詢加速功能。

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>步驟2：確認已註冊功能

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

若要確認註冊是否已完成，請使用 [AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要確認註冊是否完成，請使用 [az feature](/cli/azure/feature#az-feature-show) 命令。

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>步驟3：註冊 Azure 儲存體資源提供者

註冊經過核准之後，您必須重新註冊 Azure 儲存體資源提供者。 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

若要註冊資源提供者，請使用 [>register-azresourceprovider](/powershell/module/az.resources/register-azresourceprovider) 命令。

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要註冊資源提供者，請使用 [az provider register](/cli/azure/provider#az-provider-register) 命令。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>設定您的環境

### <a name="step-1-install-packages"></a>步驟1：安裝套件 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

安裝 Az module version 4.6.0 或更高版本。

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

若要從較舊的 Az 版本進行更新，請執行下列命令：

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. 開啟命令提示字元，並將目錄 () 變更為 `cd` 您的專案資料夾，例如：

   ```console
   cd myProject
   ```

2. `12.5.0-preview.6`使用命令安裝適用于 .net 套件的 Azure Blob 儲存體用戶端程式庫版本 `dotnet add package` 。 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.6.0
   ```

3. 本文中顯示的範例會使用 [CsvHelper](https://www.nuget.org/packages/CsvHelper/) 程式庫來剖析 CSV 檔案。 若要使用該程式庫，請使用下列命令。

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. 在文字編輯器中開啟專案的 *pom.xml* 檔案。 將下列相依性元素新增至相依性群組。 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

使用 [pip](https://pypi.org/project/pip/)安裝適用于 Python 的 Azure Data Lake Storage 用戶端程式庫。

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

開啟終端機視窗，然後輸入下列命令，以安裝適用于 JavaScript 的 Data Lake 用戶端程式庫。

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>步驟2：加入語句

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

不適用

#### <a name="net"></a>[.NET](#tab/dotnet)

將這些 `using` 語句新增至程式碼檔案的頂端。

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

查詢加速會抓取 CSV 和 Json 格式的資料。 因此，請務必為您選擇要使用的任何 CSV 或 Json 剖析程式庫加入 using 語句。 本文中顯示的範例會使用 NuGet 上提供的 [CsvHelper](https://www.nuget.org/packages/CsvHelper/) 程式庫來剖析 CSV 檔案。 因此，我們會將這些 `using` 語句新增至程式碼檔案的頂端。

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

若要編譯本文中所呈現的範例，您也必須加入這些 `using` 語句。

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

將這些 `import` 語句新增至程式碼檔案的頂端。

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

將這些 import 語句新增至程式碼檔案的頂端。

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

將 `storage-blob` 此語句放在程式碼檔案的頂端，以包含模組。 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

查詢加速會抓取 CSV 和 Json 格式的資料。 因此，請務必為您選擇要使用的任何 CSV 或 Json 剖析模組加入語句。 本文中顯示的範例會使用 [快速 CSV](https://www.npmjs.com/package/fast-csv) 模組來剖析 csv 檔案。 因此，我們會將此語句新增至程式碼檔案的頂端。

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>使用篩選器取出資料

您可以使用 SQL 來指定查詢加速要求中的資料列篩選述詞和資料行預測。 下列程式碼會查詢儲存體中的 CSV 檔案，並傳回第三個數據行符合值的所有資料列 `Hemingway, Ernest` 。 

- 在 SQL 查詢中，關鍵字 `BlobStorage` 是用來表示要查詢的檔案。

- 資料行參考是指定為 `_N` 第一個資料行的位置 `_1` 。 如果來源檔案包含標頭資料列，則您可以使用標頭資料列中所指定的名稱來參考資料行。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

Async 方法會 `BlobQuickQueryClient.QueryAsync` 將查詢傳送至查詢加速 API，然後將結果以 [資料流程](/dotnet/api/system.io.stream) 物件的形式串流回應用程式。

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Context.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

方法會 `BlobQuickQueryClient.openInputStream()` 將查詢傳送至查詢加速 API，然後將結果串流回至應用程式，做為 `InputStream` 可讀取的物件，就像任何其他 InputStream 物件一樣。

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

此範例會將查詢傳送至查詢加速 API，然後再將結果串流回來。 `blob`傳遞至 helper 函數的物件屬於 `queryHemingway` [BlockBlobClient](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient)類型。 若要深入瞭解如何取得 [BlockBlobClient](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient) 物件，請參閱 [快速入門：在 Node.js中使用 JavaScript V12 SDK 來管理 blob ](storage-quickstart-blobs-nodejs.md)。

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>取出特定資料行

您可以將結果的範圍設為數據行的子集。 如此一來，您就只會取出執行指定計算所需的資料行。 這可改善應用程式效能並降低成本，因為透過網路傳送的資料較少。 

此程式碼只會抓取 `BibNum` 資料集中所有書籍的資料行。 它也會使用來源檔案的標頭資料列中的資訊，來參考查詢中的資料行。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

下列程式碼會將資料列篩選和資料行投影合併成相同的查詢。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>後續步驟

- [Azure Data Lake Storage 查詢加速](data-lake-storage-query-acceleration.md)
- [查詢加速 SQL 語言參考](query-acceleration-sql-reference.md)