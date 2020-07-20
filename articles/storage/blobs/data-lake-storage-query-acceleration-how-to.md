---
title: 使用 Azure Data Lake Storage 查詢加速來篩選資料（預覽） |Microsoft Docs
description: 使用查詢加速（預覽），從您的儲存體帳戶中取出資料的子集。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: cc9235f07c0829abfb8be42e83d05d8428bc1806
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465859"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>使用 Azure Data Lake Storage 查詢加速來篩選資料（預覽）

本文說明如何使用查詢加速（預覽），從您的儲存體帳戶中取出資料的子集。 

查詢加速（預覽）是 Azure Data Lake Storage 的新功能，可讓應用程式和分析架構藉由僅抓取執行給定作業所需的資料，大幅優化資料處理。 若要深入瞭解，請參閱[Azure Data Lake Storage 查詢加速（預覽）](data-lake-storage-query-acceleration.md)。

> [!NOTE]
> 查詢加速功能處於公開預覽狀態，並可在加拿大中部和法國中部區域中取得。 若要查看限制，請參閱[已知問題](data-lake-storage-known-issues.md)一文。 若要註冊預覽，請參閱[此表單](https://aka.ms/adls/qa-preview-signup)。  

## <a name="prerequisites"></a>必要條件

### <a name="net"></a>[.NET](#tab/dotnet)

- 若要存取 Azure 儲存體，您需要有 Azure 訂用帳戶。 如果您還沒有訂用帳戶，請先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。

- **一般用途 v2**儲存體帳戶。 請參閱[建立儲存體帳戶](../common/storage-quickstart-create-account.md)。

- [.NET SDK](https://dotnet.microsoft.com/download)。 

### <a name="java"></a>[Java](#tab/java)

- 若要存取 Azure 儲存體，您需要有 Azure 訂用帳戶。 如果您還沒有訂用帳戶，請先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。

- **一般用途 v2**儲存體帳戶。 請參閱[建立儲存體帳戶](../common/storage-quickstart-create-account.md)。

- [JAVA 開發工具組（JDK）](/java/azure/jdk/?view=azure-java-stable)第8版或更新版本。

- [Apache Maven](https://maven.apache.org/download.cgi)。 

  > [!NOTE] 
  > 本文假設您已使用 Apache Maven 建立 JAVA 專案。 如需如何使用 Apache Maven 建立專案的範例，請參閱[設定](storage-quickstart-blobs-java.md#setting-up)。
  
---

## <a name="install-packages"></a>安裝套件 

### <a name="net"></a>[.NET](#tab/dotnet)

1. 下載查詢加速套件。 您可以使用下列連結，取得包含這些封裝的壓縮 .zip 檔案： [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net) 。 

2. 將此檔案的內容解壓縮至您的專案目錄。

3. 在文字編輯器中開啟您的專案檔（*.csproj*），並在元素內新增這些套件參考 \<Project\> 。

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. 還原預覽 SDK 套件。 此範例命令會使用命令來還原預覽 SDK 套件 `dotnet restore` 。 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. 從公用 NuGet 存放庫還原所有其他相依性。

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. 在專案的根目錄中建立目錄。 根目錄是包含**pom.xml**檔案的目錄。

   > [!NOTE]
   > 本文中的範例假設目錄的名稱為**lib**。

2. 下載查詢加速套件。 您可以使用下列連結，取得包含這些封裝的壓縮 .zip 檔案： [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java) 。 

3. 將這個 .zip 檔案中的檔案解壓縮至您所建立的目錄。 在我們的範例中，該目錄名為**lib**。 

4. 在文字編輯器中開啟 *pom.xml* 檔案。 將下列相依性元素新增至相依性群組。 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>新增語句


### <a name="net"></a>[.NET](#tab/dotnet)

將這些 `using` 語句新增至程式碼檔案的頂端。

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

查詢加速會捕獲 CSV 和 Json 格式的資料。 因此，請務必為您選擇使用的任何 CSV 或 Json 剖析程式庫新增 using 語句。 本文中所顯示的範例會使用可在 NuGet 上取得的[CsvHelper](https://www.nuget.org/packages/CsvHelper/)程式庫來剖析 CSV 檔案。 因此，我們會將這些 `using` 語句新增至程式碼檔案的頂端。

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

若要編譯本文中顯示的範例，您也必須新增這些 `using` 語句。

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

將這些 `import` 語句新增至程式碼檔案的頂端。

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>使用篩選器來取出資料

您可以使用 SQL 來指定查詢加速要求中的資料列篩選述詞和資料行投影。 下列程式碼會查詢儲存體中的 CSV 檔案，並傳回第三個數據行符合值的所有資料列 `Hemingway, Ernest` 。 

- 在 SQL 查詢中，關鍵字 `BlobStorage` 是用來表示正在查詢的檔案。

- 資料行參考會指定為， `_N` 其中第一個資料行是 `_1` 。 如果來源檔案包含標頭資料列，則您可以依據標題列中所指定的名稱來參考資料行。 

### <a name="net"></a>[.NET](#tab/dotnet)

非同步方法會 `BlobQuickQueryClient.QueryAsync` 將查詢傳送至查詢加速 API，然後再將結果以[資料流程](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)物件的形式串流回應用程式。

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

方法會將 `BlobQuickQueryClient.openInputStream()` 查詢傳送至查詢加速 API，然後再將結果以物件的形式串流回應用程式，以便 `InputStream` 如同任何其他 InputStream 物件一樣讀取。

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>取出特定資料行

您可以將結果的範圍設為數據行的子集。 如此一來，您只會取出執行指定計算所需的資料行。 這可改善應用程式效能並降低成本，因為網路上傳輸的資料較少。 

此程式碼只會 `PublicationYear` 針對資料集中的所有書籍，抓取資料行。 它也會使用來源檔案中標頭資料列的資訊，來參考查詢中的資料行。


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

下列程式碼會將資料列篩選和資料行投影結合成相同的查詢。 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>後續步驟

- [查詢加速註冊表單](https://aka.ms/adls/qa-preview-signup)    
- [Azure Data Lake Storage 查詢加速（預覽）](data-lake-storage-query-acceleration.md)
- [查詢加速 SQL 語言參考（預覽）](query-acceleration-sql-reference.md)
