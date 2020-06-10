---
title: 將資料從 Azure Blob 儲存體複製到 Azure SQL Database
description: 本教學課程提供逐步指示，將資料從 Azure Blob 儲存體複製到 Azure SQL Database。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: ad257d0bea38d03803bf2be44313a3e086e7654c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118156"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>使用 Azure Data Factory 將資料從 Azure Blob 複製到 Azure SQL Database

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教學課程中，您會建立 Data Factory 管道，將資料從 Azure Blob 儲存體複製到 Azure SQL Database。 本教學課程中的設定模式從以檔案為基礎的資料存放區複製到關聯式資料存放區。 如需支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

您會在本教學課程中執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立 Azure 儲存體和 Azure SQL Database 連結服務。
> * 建立 Azure Blob 和 Azure SQL Database 資料集。
> * 建立包含複製活動的管道。
> * 啟動管線執行。
> * 監視管線和活動執行。

本教學課程使用 .NET SDK。 您可以使用其他機制來與 Azure Data Factory 互動，請參閱**快速入門**下的範例。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

* *Azure 儲存體帳戶*。 您會使用 Blob 儲存體作為*來源*資料存放區。 如果您沒有 Azure 儲存體帳戶，請參閱[建立一般用途的儲存體帳戶](../storage/common/storage-account-create.md)。
* *Azure SQL Database*。 您會使用資料庫作為*接收*資料存放區。 如果您沒有 Azure SQL Database，請參閱[建立 Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)。
* *Visual Studio*。 本文中的逐步解說是使用 Visual Studio 2019。
* *[適用於 .NET 的 Azure SDK](/dotnet/azure/dotnet-tools)* 。
* *Azure Active Directory 應用程式*。 如果您沒有 Azure Active Directory 應用程式，請參閱[建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)一節 ([如何：使用入口網站來建立 Azure AD 應用程式](../active-directory/develop/howto-create-service-principal-portal.md))。 複製下列值，以便在後續步驟中使用：**應用程式 (用戶端) 識別碼**、**驗證金鑰**，以及**目錄 (租用戶) 識別碼**。 依照同一篇文章中的指示，將應用程式指派給「參與者」角色。

### <a name="create-a-blob-and-a-sql-table"></a>建立 Blob 和 SQL 資料表

現在，請建立來源 Blob 和接收 SQL 資料表，以準備本教學課程的 Azure Blob 和 Azure SQL Database。

#### <a name="create-a-source-blob"></a>建立來源 Blob

首先，建立容器並在其中上傳輸入文字檔，以建立來源 Blob：

1. 開啟記事本。 複製下列文字並將其儲存在名為 *inputEmp.txt* 的本機檔案。

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. 使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)之類的工具建立 *adfv2tutorial* 容器，然後將 *inputEmp.txt* 檔案上傳至該容器。

#### <a name="create-a-sink-sql-table"></a>建立接收 SQL 資料表

接下來，建立接收 SQL 資料表：

1. 使用下列 SQL 指令碼，在您的 Azure SQL Database 中建立 *dbo.emp* 資料表。

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. 允許 Azure 服務存取 SQL Database。 確定您允許存取伺服器中的 Azure 服務，讓 Data Factory 服務可以將資料寫入 SQL Database。 若要確認並開啟此設定，請執行下列步驟：

    1. 請移至 [Azure 入口網站](https://portal.azure.com)，以管理您的 SQL 伺服器。 搜尋並選取 [SQL 伺服器]。

    2. 選取您的伺服器。

    3. 在 [SQL 伺服器] 功能表的 [安全性] 標題下，選取 [防火牆和虛擬網路]。

    4. 在 [防火牆和虛擬網路] 頁面的 [允許 Azure 服務和資源存取此伺服器] 底下，選取 [開啟]。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

使用 Visual Studio，建立 C# .NET 主控台應用程式。

1. 開啟 Visual Studio。
2. 在 [開始] 視窗中，選取 [建立新專案]。
3. 在 [建立新專案] 視窗中，從專案類型清單中選擇 C# 版本的 [主控台應用程式 (.NET Framework)]。 然後選取 [下一步]。
4. 在 [設定新專案] 視窗中，輸入 *ADFv2Tutorial* 的 [專案名稱]。 針對 [位置]，瀏覽至或建立用來儲存專案的目錄。 然後選取 [建立]。 新的專案會出現在 Visual Studio IDE 中。

## <a name="install-nuget-packages"></a>安裝 NuGet 套件

接下來，使用 NuGet 套件管理員安裝必要的程式庫套件。

1. 在功能表列中，選擇 **工具** > **NuGet 套件管理員** > **套件管理器主控台**。
2. 在 [套件管理員主控台] 窗格中，執行下列命令以安裝套件。 如需 Azure Data Factory NuGet 套件的詳細資訊，請參閱 [Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)。

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>建立資料處理站用戶端

請依照下列步驟，建立資料處理站用戶端。

1. 開啟 *Program.cs*，然後使用下列程式碼來覆寫現有的 `using` 陳述式，以新增命名空間的參考。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. 將下列程式碼新增至 `Main`方法，以設定變數。 將 14 個預留位置取代為您自己的值。

    若要查看目前可使用 Data Factory 的 Azure 區域清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。 在 [產品] 下拉式清單下，選擇 [瀏覽] > [分析] > [Data Factory]。 然後在 [區域] 下拉式清單中，選擇您感興趣的區域。 方格隨即出現，其中顯示您所選區域的 Data Factory 產品可用性狀態。

    > [!NOTE]
    > Data Factory 所使用的資料存放區 (例如 Azure 儲存體和 Azure SQL Database) 和計算 (例如 HDInsight) 可位於您為 Data Factory 所選的其他區域中。

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. 將下列程式碼新增至 `Main` 方法，以建立 `DataFactoryManagementClient` 類別的執行個體。 您會使用此物件來建立資料處理站、連結服務、資料集和管道。 您也可以使用此物件來監視管線執行的詳細資料。

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>建立 Data Factory

將下列程式碼新增至 `Main` 方法，以建立「資料處理站」。

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>建立連結的服務

在本教學課程中，您會建立兩個連結服務，分別是針對來源和接收。

### <a name="create-an-azure-storage-linked-service"></a>建立 Azure 儲存體連結服務

將下列程式碼新增至 `Main` 方法，以建立「Azure 儲存體連結服務」。 若要進一步了解支援的屬性和詳細資料，請參閱 [Azure Blob 連結服務屬性](connector-azure-blob-storage.md#linked-service-properties)。

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>建立 Azure SQL Database 連結服務

將下列程式碼新增至 `Main` 方法，以建立「Azure SQL Database 連結服務」。 若要進一步了解支援的屬性和詳細資料，請參閱 [Azure SQL Database 連結服務屬性](connector-azure-sql-database.md#linked-service-properties)。

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>建立資料集

在本節中，您會建立兩個資料集：一個作為來源，另一個作為接收。

### <a name="create-a-dataset-for-source-azure-blob"></a>建立來源 Azure Blob 的資料集

將下列程式碼新增至 `Main` 方法，以建立「Azure Blob 資料集」。 若要進一步了解支援的屬性和詳細資訊，請參閱 [Azure Blob 資料集屬性](connector-azure-blob-storage.md#dataset-properties)。

您可以定義資料集來代表 Azure Blob 中的來源資料。 此 Blob 資料集會參考您在前一個步驟中建立的 Azure 儲存體連結服務，並描述：

- 複製的來源 Blob 位置：`FolderPath` 和 `FileName`
- Blob 格式，指出如何剖析內容：`TextFormat` 及其設定，例如資料行分隔符號。
- 資料結構，包括資料行名稱和資料類型，在此案例中對應至接收 SQL 資料表。

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>建立接收 Azure SQL Database 的資料集

將下列程式碼新增至 `Main` 方法，以建立「Azure SQL Database 資料集」。 若要進一步了解支援的屬性和詳細資訊，請參閱 [Azure SQL Database 資料集屬性](connector-azure-sql-database.md#dataset-properties)。

您可以定義資料集來代表 Azure SQL Database 中的接收資料。 此資料集會參考您在前一個步驟中建立的 Azure SQL Database 連結服務。 它也會指定 SQL 資料表，其中保存已複製的資料。

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>建立管線

將下列程式碼新增至 `Main` 方法，以建立「具有複製活動的管線」。 在本教學課程中，此管道包含一個活動：`CopyActivity`，可接受 Blob 資料集作為來源，也接受 SQL 資料集作為接收。 如需複製活動詳細資料的相關資訊，請參閱 [Azure Data Factory 中的複製活動](copy-activity-overview.md)。

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>建立管線執行

將下列程式碼新增至 `Main` 方法，以「觸發管線執行」。

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>監視管線執行

現在插入程式碼以檢查管線執行狀態，並取得複製活動執行的詳細資料。

1. 將下列程式碼新增至 `Main`方法，以持續檢查管線執行的狀態，直到完成複製資料為止。

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. 將下列程式碼新增至 `Main` 方法，以取出複製活動執行的詳細資料，例如被讀取或寫入的資料大小。

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>執行程式碼

選擇 [建置] > [建置解決方案] 來建置應用程式。 然後選擇 [偵錯] > [開始偵錯]，然後確認管線執行，以啟動應用程式。

在建立資料處理站、連結服務、資料集、管道和管道執行時，主控台會印出進度。 然後會檢查管線執行狀態。 請等待出現複製活動執行詳細資料及讀取/寫入的資料大小。 然後，使用諸如 SQL Server Management Studio (SSMS) 或 Visual Studio 等工具，連線到目的地 Azure SQL Database，並檢查您所指定的目的地資料表是否包含所複製的資料。

### <a name="sample-output"></a>範例輸出

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```

## <a name="next-steps"></a>後續步驟

在此範例中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 您已了解如何︰

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立 Azure 儲存體和 Azure SQL Database 連結服務。
> * 建立 Azure Blob 和 Azure SQL Database 資料集。
> * 建立包含複製活動的管線。
> * 啟動管線執行。
> * 監視管線和活動執行。

進入下列教學課程，以了解如何將資料從內部部署複製到雲端：

> [!div class="nextstepaction"]
>[將資料從內部部署複製到雲端](tutorial-hybrid-copy-powershell.md)
