---
title: 在 Azure Data Factory 管線中分支
description: 了解如何將活動分支和鏈結來控制 Azure Data Factory 中的資料流程。
services: data-factory
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 77fa8f72d4d4d929d15859fde71f112de1ddd14e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81418723"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>在 Data Factory 管道中將活動分支和鏈結

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教學課程中，您會建立 Data Factory 管線來展示部分控制流程功能。 此管線會從 Azure Blob 儲存體中的一個容器複製到相同儲存體帳戶中的另一個容器。 如果複製活動成功，管線會在電子郵件中傳送成功複製作業的詳細資料。 該資訊可能包含寫入的資料量。 如果複製活動失敗，則管線會在電子郵件中傳送複製失敗的詳細資料，例如錯誤訊息。 在整個教學課程中，您會看到如何傳遞參數。

下圖提供此案例的概觀：

![概觀](media/tutorial-control-flow/overview.png)

本教學課程將說明如何執行下列工作：

> [!div class="checklist"]
> * 建立 Data Factory
> * 建立 Azure 儲存體連結服務
> * 建立 Azure Blob 資料集
> * 建立包含複製活動和網路活動的管道
> * 將活動的輸出傳送至後續的活動
> * 使用參數傳遞和系統變數
> * 啟動管道執行
> * 監視管道和活動執行

本教學課程使用 .NET SDK。 您可以使用其他機制與 Azure Data Factory 互動。 如需 Data Factory 的快速入門，請參閱 [5 分鐘快速入門](/azure/data-factory/quickstart-create-data-factory-portal)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

* Azure 儲存體帳戶。 您會使用 Blob 儲存體作為來源資料存放區。 如果您沒有 Azure 儲存體帳戶，請參閱 [建立儲存體帳戶](../storage/common/storage-account-create.md)。
* Azure 儲存體總管。 若要安裝此工具，請參閱 [Azure 儲存體總管](https://storageexplorer.com/)。
* Azure SQL Database。 您會使用資料庫作為接收資料存放區。 如果您沒有 Azure SQL Database，請參閱[建立 Azure SQL Database](../sql-database/sql-database-get-started-portal.md)。
* Visual Studio。 本文使用 Visual Studio 2019。
* Azure .NET SDK。 下載並安裝 [Azure .NET SDK](https://azure.microsoft.com/downloads/)。

如需目前可使用 Data Factory 的 Azure 區域，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。 資料存放區和計算可位於其他區域。 這些存放區包含 Azure 儲存體和 Azure SQL Database。 計算包含 Data Factory 所使用的 HDInsight。

依照[建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)中的說明建立應用程式。 依照同一篇文章中的指示，將應用程式指派給「參與者」  角色。 您在本教學課程的後續部分將需要數個值，例如**應用程式 (用戶端) 識別碼**和**目錄 (租用戶) 識別碼**。

### <a name="create-a-blob-table"></a>建立 Blob 資料表

1. 開啟文字編輯器。 複製下列文字，並在本機位置將其儲存為 *input.txt*。

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. 開啟 [Azure 儲存體總管]。 展開您的儲存體帳戶。 以滑鼠右鍵按一下 [Blob 容器]  ，然後選取 [建立 Blob 容器]  。
1. 將新容器命名為 *adfv2branch*，然後選取 [上傳]  將您的 *input.txt* 檔案新增至該容器。

## <a name="create-visual-studio-project"></a>建立 Visual Studio 專案<a name="create-visual-studio-project"></a>

建立 C# .NET 主控台應用程式：

1. 啟動 Visual Studio，然後選取 [建立新專案]  。
1. 在 [建立新專案]  中，針對 C# 選擇 [主控台應用程式 (.NET Framework)]  ，然後選取 [下一步]  。
1. 將專案命名為 *ADFv2BranchTutorial*。
1. 選取 [.NET 4.5.2 版]  或更新版本，然後選取 [建立]  。

### <a name="install-nuget-packages"></a>安裝 NuGet 套件

1. 選取 [工具]   > [NuGet 套件管理員]   > [套件管理員主控台]  。
1. 在 [套件管理員主控台]  中執行下列命令，以安裝套件。 如需詳細資訊，請參閱 [Microsoft.Azure.Management.DataFactory Nuget 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)。

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>建立資料處理站用戶端

1. 開啟 *Program.cs*，並新增下列陳述式：

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.DataFactory;
   using Microsoft.Azure.Management.DataFactory.Models;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```

1. 將這些靜態變數新增至 `Program` 類別。 將預留位置取代為您自己的值。

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container.
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. 將下列程式碼新增至 `Main` 方法。 此程式碼會建立 `DataFactoryManagementClient` 類別的執行個體。 接著，您會使用此物件來建立資料處理站、連結服務、資料集和管線。 您也可以使用此物件來監視管線執行的詳細資料。

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>建立 Data Factory

1. 將 `CreateOrUpdateDataFactory` 方法新增至您的 *Program.cs* 檔案：

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. 將以下這一行新增至 `Main` 方法，以建立資料處理站：

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>建立 Azure 儲存體連結服務

1. 將 `StorageLinkedServiceDefinition` 方法新增至您的 *Program.cs* 檔案：

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. 將以下這一行新增至 `Main` 方法，以建立 Azure 儲存體連結服務：

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

若要進一步了解支援的屬性和詳細資料，請參閱[連結服務屬性](connector-azure-blob-storage.md#linked-service-properties)。

## <a name="create-datasets"></a>建立資料集

在本節中，您會建立兩個資料集：一個用於來源，另一個用於接收。

### <a name="create-a-dataset-for-a-source-azure-blob"></a>建立來源 Azure Blob 的資料集

新增建立 *Azure Blob 資料集*的方法。 若要進一步了解支援的屬性和詳細資訊，請參閱 [Azure Blob 資料集屬性](connector-azure-blob-storage.md#dataset-properties)。

將 `SourceBlobDatasetDefinition` 方法新增至您的 *Program.cs* 檔案：

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

您可以定義資料集來代表 Azure Blob 中的來源資料。 此 Blob 資料集會參考先前的步驟中支援的 Azure 儲存體連結服務。 Blob 資料集會說明要從中複製 Blob 的位置：*FolderPath* 和 *FileName*。

請注意 *FolderPath* 的參數用法。 `sourceBlobContainer` 是參數的名稱，而運算式會取代為傳入管線執行中的值。 定義參數的語法是 `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>建立接收 Azure Blob 的資料集

1. 將 `SourceBlobDatasetDefinition` 方法新增至您的 *Program.cs* 檔案：

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. 將下列程式碼新增至 `Main` 方法，以建立 Azure Blob 來源和接收資料集。

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>建立 C# 類別：EmailRequest

在 C# 專案中，建立名為 `EmailRequest` 的類別。 此類別會定義在傳送電子郵件時，管線在本文要求中會傳送哪些屬性。 在本教學課程中，管道會將四個屬性從管道傳送至電子郵件：

* Message. 電子郵件本文。 如果複製成功，此屬性會包含寫入的資料量。 如果複製失敗，則此屬性會包含錯誤的詳細資料。
* Data Factory 名稱。 資料處理站的名稱。
* 管線名稱。 管線的名稱。
* 接收者。 傳遞的參數。 此屬性指定電子郵件的接收者。

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>建立電子郵件工作流程端點

若要觸發傳送電子郵件，您需要使用 [Logic Apps](../logic-apps/logic-apps-overview.md) 來定義工作流程。 如需關於建立 Logic Apps 工作流程的詳細資訊，請參閱[如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

### <a name="success-email-workflow"></a>成功電子郵件工作流程

在 [Azure 入口網站](https://portal.azure.com)中，建立名為 *CopySuccessEmail* 的 Logic Apps 工作流程。 將工作流程觸發程序定義為 `When an HTTP request is received`。 針對您的要求觸發程序，在 `Request Body JSON Schema` 中填寫下列 JSON：

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

您的工作流程會類似下列範例：

![成功電子郵件工作流程](media/tutorial-control-flow/success-email-workflow-trigger.png)

此 JSON 內容與您在上一節建立的 `EmailRequest` 類別一致。

新增 `Office 365 Outlook – Send an email` 的動作。 針對**傳送電子郵件**動作，使用傳入要求**本文** JSON 結構描述中的屬性，自訂您要格式化電子郵件的方式。 以下是範例：

![邏輯應用程式設計工具 - 傳送電子郵件動作](media/tutorial-control-flow/customize-send-email-action.png)

儲存工作流程後，請複製並儲存觸發程序中的 **HTTP POST URL** 值。

## <a name="fail-email-workflow"></a>失敗電子郵件工作流程

將 **CopySuccessEmail** 複製為名為 *CopyFailEmail* 的 Logic Apps 工作流程。 在要求觸發程序中，`Request Body JSON schema` 相同。 變更電子郵件的格式 (例如 `Subject`)，以調整為適合失敗電子郵件。 範例如下：

![邏輯應用程式設計工具 - 失敗電子郵件工作流程](media/tutorial-control-flow/fail-email-workflow.png)

儲存工作流程後，請複製並儲存觸發程序中的 **HTTP POST URL** 值。

您現在應該有兩個工作流程 URL，如下列範例所示：

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>建立管線

返回您在 Visual Studio 中的專案。 我們現在將新增程式碼，以建立具有複製活動和 `DependsOn` 屬性的管線。 在本教學課程中，此管線包含一個活動 (複製活動)，此活動以 Blob 資料集作為來源，並以另一個 Blob 資料集作為接收。 複製活動成功或失敗時，會呼叫不同的電子郵件工作。

在此管道中，您會使用下列功能：

* 參數
* Web 活動
* 活動相依性
* 使用一個活動的輸出作為其他活動的輸入

1. 將此方法新增至您的專案。 下列各節將提供更多詳細資料。

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. 將以下這一行新增至 `Main` 方法以建立管線：

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>參數

管線程式碼的第一個區段會定義參數。

* 第 1 課：建立 Windows Azure 儲存體物件`sourceBlobContainer`。 來源 Blob 資料集會在管線中使用此參數。
* 第 1 課：建立 Windows Azure 儲存體物件`sinkBlobContainer`。 接收 Blob 資料集會在管線中使用此參數。
* 第 1 課：建立 Windows Azure 儲存體物件`receiver`。 管線中將成功或失敗電子郵件傳送給接收者的兩個 Web 活動都會使用此參數。

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Web 活動

Web 活動允許呼叫任何 REST 端點。 如需活動的詳細資訊，請參閱 [Azure Data Factory 中的 Web 活動](control-flow-web-activity.md)。 這個管線會使用 Web 活動來呼叫 Logic Apps 電子郵件工作流程。 您會建立兩個 Web 活動：一個會呼叫 `CopySuccessEmail` 工作流程，另一個會呼叫 `CopyFailWorkFlow`。

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

在 `Url` 屬性中，從您的 Logic Apps 工作流程貼上 **HTTP POST URL** 端點。 在 `Body` 屬性中，傳遞 `EmailRequest` 類別的執行個體。 電子郵件要求包含下列屬性：

* Message. 傳遞 `@{activity('CopyBlobtoBlob').output.dataWritten` 的值。 存取先前複製活動的屬性，並傳遞 `dataWritten` 的值。 對於失敗案例，請傳遞錯誤輸出，而不是 `@{activity('CopyBlobtoBlob').error.message`。
* Data Factory 名稱。 傳遞 `@{pipeline().DataFactory}` 的值。此系統變數可讓您存取對應的資料處理站名稱。 如需系統變數的清單，請參閱[系統變數](control-flow-system-variables.md)。
* 管線名稱。 傳遞 `@{pipeline().Pipeline}` 的值。 此系統變數可讓您存取對應的管線名稱。
* 接收者。 傳遞 `"@pipeline().parameters.receiver"` 的值。 存取管線參數。

此程式碼會建立新的活動相依性，而此相依性取決於先前的複製活動。

## <a name="create-a-pipeline-run"></a>建立管線執行

將下列程式碼新增至 `Main` 方法以觸發管線執行。

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main 類別

最終的 `Main` 方法應該會顯示如下。

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

建置並執行您的程式來觸發管道執行！

## <a name="monitor-a-pipeline-run"></a>監視管線執行

1. 將下列程式碼新增至 `Main` 方法：

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

    此程式碼會持續檢查執行狀態，直到它完成資料複製為止。

1. 將下列程式碼新增至 `Main` 方法以擷取複製活動執行的詳細資料，例如讀取/寫入的資料大小：

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList();

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>執行程式碼

建置並啟動應用程式，然後確認管線執行。

應用程式會顯示建立資料處理站、連結服務、資料集、管線和管線執行的進度。 然後會檢查管線執行狀態。 請等待出現複製活動執行詳細資料及讀取/寫入的資料大小。 然後，使用 Azure 儲存體總管之類的工具，確認 Blob 已從 *inputBlobPath* 複製到 *outputBlobPath*，如同您在變數中的指定。

輸出應該會如下列範例所示：

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>後續步驟

您已在此教學課程中執行下列工作：

> [!div class="checklist"]
> * 建立 Data Factory
> * 建立 Azure 儲存體連結服務
> * 建立 Azure Blob 資料集
> * 建立包含複製活動和網路活動的管道
> * 將活動的輸出傳送至後續的活動
> * 使用參數傳遞和系統變數
> * 啟動管道執行
> * 監視管道和活動執行

您現在可以繼續閱讀「概念」一節，以了解 Azure Data Factory 的詳細資訊。
> [!div class="nextstepaction"]
>[管線和活動](concepts-pipelines-activities.md)
