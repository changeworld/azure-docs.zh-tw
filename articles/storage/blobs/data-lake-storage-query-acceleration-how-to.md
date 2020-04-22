---
title: 使用 Azure 資料湖儲存查詢加速(預覽)篩選數據 |微軟文件
description: 使用查詢加速(預覽)從存儲帳戶檢索數據子集。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: ae3dfc7681ef0d8ce3fcf679bddbd0ff195f4e3b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771842"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>使用 Azure 資料湖儲存查詢加速篩選資料(預覽)

本文介紹如何使用查詢加速(預覽)從存儲帳戶檢索數據子集。 

查詢加速(預覽)是 Azure 數據湖儲存的新功能,它使應用程式和分析框架能夠通過僅檢索執行給定操作所需的數據來顯著優化數據處理。 要瞭解更多資訊,請參閱[Azure 數據湖存儲查詢加速(預覽)。](data-lake-storage-query-acceleration.md)

> [!NOTE]
> 查詢加速功能處於公共預覽版中,並且在加拿大中部和法國中部區域可用。 要查看限制,請參閱[已知問題](data-lake-storage-known-issues.md)一文。 要在預覽版中註冊,請參閱[此表單](https://aka.ms/adls/qa-preview-signup)。  

## <a name="prerequisites"></a>Prerequisites

### <a name="net"></a>[.NET](#tab/dotnet)

- 若要存取 Azure 儲存體，您需要有 Azure 訂用帳戶。 如果您還沒有訂用帳戶，請先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。

- **通用 v2**儲存帳戶。 請參考[儲存帳號](../common/storage-quickstart-create-account.md)。

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- 若要存取 Azure 儲存體，您需要有 Azure 訂用帳戶。 如果您還沒有訂用帳戶，請先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。

- **通用 v2**儲存帳戶。 請參考[儲存帳號](../common/storage-quickstart-create-account.md)。

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) 第 8 版或更新版本。

- [阿帕奇·馬文](https://maven.apache.org/download.cgi)。 

  > [!NOTE] 
  > 本文假定您已使用 Apache Maven 創建了 Java 專案。 有關如何使用 Apache Maven 建立項目的範例,請參考[設定](storage-quickstart-blobs-java.md#setting-up)。
  
---

## <a name="install-packages"></a>安裝套件 

### <a name="net"></a>[.NET](#tab/dotnet)

1. 下載查詢加速包。 您可以使用此連結包含這些套件的壓縮 .zip 檔案[https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net): 。 

2. 將此檔的內容提取到專案目錄中。

3. 在文字編輯器中打開專案檔 *(.csproj*),並在\<Project\>元素中添加這些包引用。

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. 還原預覽 SDK 包。 此示例命令使用`dotnet restore`命令 還原預覽 SDK 包。 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. 從公共 NuGet 儲存庫還原所有其他依賴項。

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. 在專案的根目錄中創建目錄。 根目錄是包含**pom.xml**檔的目錄。

   > [!NOTE]
   > 本文中的範例的目錄名稱是**lib**。

2. 下載查詢加速包。 您可以使用此連結包含這些套件的壓縮 .zip 檔案[https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java): 。 

3. 將此 .zip 檔案中的檔案提取到您建立的目錄。 在我們的範例中,該目錄名為**lib**。 

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

## <a name="add-statements"></a>新增敘述


### <a name="net"></a>[.NET](#tab/dotnet)

將這些`using`語句添加到代碼文件的頂部。

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

查詢加速檢索 CSV 和 Json 格式化的數據。 因此,請確保為選擇使用的任何 CSV 或 Json 解析庫添加 using 語句。 本文中顯示的範例使用 NuGet 上可用的[CsvHelper](https://www.nuget.org/packages/CsvHelper/)函式庫解析 CSV 檔。 因此,我們將這些`using`語句添加到代碼文件的頂部。

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

要編譯本文中介紹的範例,您還需要添加這些`using`語句。

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

將這些`import`語句添加到代碼文件的頂部。

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

## <a name="retrieve-data-by-using-a-filter"></a>使用篩選器檢索資料

可以使用 SQL 在查詢加速請求中指定行篩選器謂詞和列投影。 以下代碼查詢儲存中的 CSV 檔,並傳回第三`Hemingway, Ernest`列與 值 匹配的所有資料行。 

- 在 SQL 查詢`BlobStorage`中, 關鍵字用於表示要查詢的檔。

- 列引用指定為`_N`第一欄的位置`_1`。 如果來源包含標題列,則可以按頭行中指定的名稱引用列。 

### <a name="net"></a>[.NET](#tab/dotnet)

非同步方法`BlobQuickQueryClient.QueryAsync`將查詢發送到查詢加速 API,然後將結果作為[Stream](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)物件流式傳輸回應用程式。

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
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
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

該方法`BlobQuickQueryClient.openInputStream()`將查詢發送到查詢加速 API,然後將結果流式傳輸回應用程式,作為物件,可以像`InputStream`任何其他 InputStream 物件一樣讀取。

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

## <a name="retrieve-specific-columns"></a>檢索特定欄

您可以將結果範圍限定為列的子集。 這樣,您只能檢索執行給定計算所需的列。 這提高了應用程式性能並降低了成本,因為通過網路傳輸的數據更少。 

此代碼僅檢索數據集中`PublicationYear`所有書籍的列。 它還使用源檔中的標頭行中的資訊來引用查詢中的列。


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

以下代碼將行篩選和列投影合併到同一查詢中。 

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

- [查詢加速註冊表單](https://aka.ms/adls/queryaccelerationpreview)    
- [Azure 資料湖儲存查詢加速(預覽)](data-lake-storage-query-acceleration.md)
- [查詢加速 SQL 語言參考(預覽)](query-acceleration-sql-reference.md)
- 查詢加速 REST API 參考
