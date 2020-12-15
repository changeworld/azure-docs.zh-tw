---
title: 包含檔案
description: 包含檔案
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854097"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="應用程式概觀":::

下圖說明您在本教學課程中建置的解決方案所採用的工作流程︰ 

1. 將傳送至 Azure 事件中樞的資料擷取到 Azure Blob 儲存體中。
2. 資料擷取完成時就會產生事件，並傳送至 Azure 事件方格。 
3. 事件方格會將此事件資料轉送至 Azure 函式應用程式。
4. 此函式應用程式會使用事件資料中的 Blob URL 來擷取儲存體中的 Blob。 
5. 函式應用程式會將 Blob 資料遷移至 Azure Synapse Analytics。 

在本文中，您會執行下列步驟：

> [!div class="checklist"]
> - 部署教學課程所需的基礎結構
> - 將程式碼發佈至 Functions 應用程式
> - 建立事件格線訂用帳戶 
> - 將範例資料串流至事件中樞
> - 驗證 Azure Synapse Analytics 中擷取的資料

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您必須具備：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- [Visual Studio 2019](https://www.visualstudio.com/vs/)，搭配適用於 .NET 桌面開發、Azure 開發、ASP.NET 和 Web 開發、Node.js 開發及 Python 開發的工作負載。
- 將 [EventHubsCaptureEventGridDemo 範例專案](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)下載到您的電腦。
    - WindTurbineDataGenerator – 一個簡單的發行者，會將範例風力發電機資料傳送至已啟用擷取功能的事件中樞
    - FunctionDWDumper – 一個 Azure 函式，會在 Avro 檔案擷取至 Azure 儲存體 Blob 時收到事件格線通知。 此函式會接收 Blob 的 URI 路徑、讀取其內容，並將此資料推送至 Azure Synapse Analytics (專用 SQL 集區)。

## <a name="deploy-the-infrastructure"></a>部署基礎結構
在此步驟中，您會使用 [Resource Manager 範本](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)來部署必要的基礎結構。 在部署範本時，會建立下列資源：

* 已啟用擷取功能的事件中樞。
* 已擷取的檔案適用的儲存體帳戶。 
* 用來裝載函式應用程式的 App Service 方案
* 函式應用程式，用來處理事件
* SQL Server，用來裝載資料倉儲
* 用來儲存已遷移資料的 Azure Synapse Analytics (專用 SQL 集區)

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>使用 Azure CLI 來部署基礎結構

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 選取位於最上方的 [Cloud Shell] 按鈕。

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure 入口網站":::
3. 您會看到 Cloud Shell 在瀏覽器底部開啟。

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. 在 Cloud Shell 中，如果您看到選取 **Bash** 或 **PowerShell** 的選項，請選取 **Bash**。 
5. 如果您是第一次使用 Cloud Shell，請選取 [建立儲存體] 以建立儲存體帳戶。 Azure Cloud Shell 需要以 Azure 儲存體帳戶儲存某些檔案。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="建立 Cloud Shell 的儲存體":::
6. 等待 Cloud Shell 完成初始化。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell 初始化":::
1. 執行下列 CLI 命令以建立 Azure 資源群組： 
    1. 複製下列命令並貼到 Cloud Shell 視窗中。 視需要變更資源群組名稱和位置。

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. 按 ENTER 鍵。 

        範例如下：
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. 執行下列 CLI 命令，以部署上一節中提到的所有資源 (事件中樞、儲存體帳戶、函式應用程式、Azure Synapse Analytics)： 
    1. 複製下列命令並貼到 Cloud Shell 視窗中。 或者，您可以複製/貼到自己選擇的編輯器中，並在設定值之後將命令複製到 Cloud Shell 中。 

        > [!IMPORTANT]
        > 執行命令之前，請先指定下列實體的值： 
        > - 您先前建立的資源群組名稱。
        > - 事件中樞命名空間的名稱。 
        > - 事件中樞的名稱。 您可以保留原有的值 (hubdatamigration)。
        > - SQL 伺服器的名稱。
        > - SQL 使用者的名稱和密碼。 
        > - 資料庫的名稱。
        > - 儲存體帳戶的名稱。 
        > - 函式應用程式的名稱。 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  在 Cloud Shell 視窗中按 **ENTER** 鍵，以執行命令。 此程序可能需要一些時間，因為您會建立大量資源。 在命令的結果中，確定沒有任何失敗的狀況。 
1. 選取入口網站中的 **Cloud Shell** 按鈕 (或) Cloud Shell 視窗右上角的 **X** 按鈕，以關閉 Cloud Shell。 

### <a name="verify-that-the-resources-are-created"></a>確認資源已建立

1. 在 Azure 入口網站中，選取左側功能表上的 [資源群組]。 
2. 在搜尋方塊中輸入您資源群組的名稱，以篩選資源群組的清單。 
3. 在清單中選取您的資源群組。

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="選取您的資源群組":::
4. 確認您在資源群組中看到下列資源：

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="資源群組中的資源" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中建立資料表
執行 [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) 指令碼，在您的資料倉儲中建立資料表。 若要執行指令碼，您可以在入口網站中使用 Visual Studio 或查詢編輯器。 下列步驟說明如何使用查詢編輯器： 

1. 在資源群組中的資源清單內，選取您的 **專用 SQL 集區**。 
2. 在 [專用 SQL 集區] 頁面上，從左側功能表的 [一般工作] 區段中選取 [查詢編輯器 (預覽)]。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Azure Synapse Analytics 頁面":::
2. 輸入 SQL 伺服器的 **使用者名稱** 和 **密碼**，然後選取 [確定]。 如果您看到關於允許您的用戶端存取 SQL Server 的訊息，請依照下列步驟操作：
    1. 選取連結：**設定伺服器防火牆**。 
    2. 在 [防火牆設定] 頁面上，選取工具列上的 [新增用戶端 IP]，然後選取工具列上的 [儲存]。 
    3. 在成功訊息上選取 [確定]。
    4. 瀏覽回 [專用 SQL 集區] 頁面，然後選取左側功能表上的 [查詢編輯器 (預覽)]。 
    5. 輸入 **使用者名稱** 和 **密碼**，然後選取 [確定]。 
1. 在查詢視窗中，複製並執行下列 SQL 指令碼： 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="執行 SQL 查詢":::
5. 讓此索引標籤或視窗保持開啟，以便您在本教學課程結尾處確認資料已建立。 

### <a name="update-the-function-runtime-version"></a>更新函式執行階段版本

1. 在網頁瀏覽器中開啟另一個索引標籤，然後瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站中，選取左側功能表上的 [資源群組]。
1. 選取函數應用程式所在的資源群組。 
1. 在資源群組的資源清單中，選取 [函式應用程式]。
1. 選取左側功能表上 [設定] 下方的 [設定]。 
1. 切換到右側窗格的 [函式執行階段設定] 索引標籤。 
1. 將 [執行階段版本] 更新為 **~3**。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="更新函式執行階段版本":::
6. 在工具列上選取 [儲存]。 
1. 在 [儲存變更] 確認快顯視窗中，選取 [繼續]。 

## <a name="publish-the-azure-functions-app"></a>發佈 Azure Functions 應用程式

1. 啟動 Visual Studio。
2. 開啟您為了符合必要條件而從 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) 下載的 **EventHubsCaptureEventGridDemo.sln** 解決方案。 您可以在 `/samples/e2e/EventHubsCaptureEventGridDemo` 資料夾中找到該解決方案。 
3. 在 [方案總管] 中，以滑鼠右鍵按一下 **FunctionEGDWDumper** 專案，然後選取 [發佈]。
4. 如果您看到下列畫面，請選取 [開始]。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="[發佈] 區段中的 [啟動] 按鈕。":::
5. 在 [發佈] 對話方塊中，針對 [目標] 選取 [Azure]，然後選取 [下一步]。 
6. 選取 [Azure 函數應用程式 (Windows)]，然後選取 [下一步]。
7. 在 [函式執行個體] 索引標籤上，選取您的 Azure 訂用帳戶，展開資源群組，選取您的函數應用程式，然後選取 [完成]。 您需要登入 Azure 帳戶 (若您尚未登入)。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="選取函式應用程式":::
8. 在 [發佈] 頁面的 [服務相依性] 區段中，針對 [儲存體] 選取 [設定]。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="選取儲存體服務相依性的設定連結":::
1. 在 [設定相依性] 頁面上，依照下列步驟操作： 
    1. 選取您先前建立的 **儲存體帳戶**，然後選取 [下一步]。 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="選取儲存體帳戶":::
    10. 指定 **連接字串的名稱**，並針對 [儲存連接字串] 選項選取 [無]，然後選取 [下一步]。 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="指定連接字串名稱":::      
    1. 清除 [C# 程式碼檔案] 和 [秘密存放區] 選項，然後選取 [完成]。  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="檢閱變更摘要":::
1. 當 Visual Studio 完成設定檔設定時，選取 [發佈]。

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Select publish":::
2. 在開啟 [Azure 函式] 頁面的索引標籤中，選取左側功能表上的 [函式]。 確認 **EventGridTriggerMigrateData** 函式顯示在清單中。 若未看到，請嘗試從 Visual Studio 重新發佈，然後重新整理入口網站中的頁面。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="確認函式建立":::    

發行函式之後，您已準備好訂閱事件。

## <a name="subscribe-to-the-event"></a>訂閱事件

1. 在網頁瀏覽器的新索引標籤或新視窗中，瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站中，選取左側功能表上的 [資源群組]。 
3. 在搜尋方塊中輸入您資源群組的名稱，以篩選資源群組的清單。 
4. 在清單中選取您的資源群組。
1. 從資源清單中選取 [事件中樞命名空間]。
1. 在 [事件中樞命名空間] 頁面上，選取左側功能表上的 [事件]，然後在工具列上選取 [+ 事件訂閱]。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="事件中樞命名空間的 [事件] 頁面上的 [新增事件訂閱] 連結":::
1. 在 [建立事件訂閱]  頁面上，遵循下列步驟：
    1. 輸入 **事件訂閱** 的名稱。 
    1. 輸入系統主題的 **名稱**。 系統主題會提供一個端點供傳送者傳送事件。 如需詳細資訊，請參閱[系統主題](../articles/event-grid/system-topics.md)
    1. 針對 [端點類型]，選取 [Azure 函式]。
    1. 針對 [端點]，選取連結。
    1. 在 [選取 Azure 函式] 頁面上，如果未自動填入內容，請依照下列步驟操作。
        1. 選取具有 Azure 函式的 Azure 訂用帳戶。 
        1. 選取函式的資源群組。 
        1. 選取函式應用程式。
        1. 選取部署位置。 
        1. 選取 **EventGridTriggerMigrateData** 函式。 
    1. 在 [選取 Azure 函式] 頁面上，選取 [確認選取項目]。
    1. 然後回到 [建立事件訂閱] 頁面，選取 [建立]。 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="使用函式建立事件訂閱" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. 確認已建立事件訂閱。 在事件中樞命名空間的 [事件] 頁面上，切換至 [事件訂閱] 索引標籤。 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="確認事件訂閱" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. 在資源群組中的資源清單內，選取 App Service 方案 (而非 App Service)。 

## <a name="run-the-app-to-generate-data"></a>執行應用程式以產生資料
您已完成事件中樞、專用 SQL 集區 (先前稱為 SQL 資料倉儲)、Azure 函式應用程式及事件訂閱的設定。 在執行產生事件中樞資料的應用程式之前，您需要設定幾個值。

1. 在 Azure 入口網站中，再次瀏覽至您的資源群組。 
2. 選取事件中樞命名空間。
3. 在 [事件中樞命名空間] 頁面中，選取左側功能表上的 [共用存取原則]。
4. 選取原則清單中的 **RootManageSharedAccessKey**。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="事件中樞命名空間的 [共用存取原則] 頁面":::    
1. 選取 [連接字串 - 主要金鑰] 文字方塊旁的複製按鈕。 
1. 返回您的 Visual Studio 解決方案。 
1. 以滑鼠右鍵按一下 **WindTurbineDataGenerator** 專案，然後選取 [設定為啟始專案]。 
1. 在 WindTurbineDataGenerator 專案中，開啟 **program.cs**。
1. 將 `<EVENT HUBS NAMESPACE CONNECTION STRING>` 取代為您從入口網站複製的連接字串。 
1. 將 `<EVENT HUB NAME>` 取代為事件中樞的名稱。 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. 建置方案。 執行 **WindTurbineGenerator.exe** 應用程式。 
7. 經過幾分鐘之後，在已開啟查詢視窗的另一個瀏覽器索引標籤中，查詢資料倉儲中的資料表以找出遷移的資料。

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![查詢結果](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>監視解決方案
本節可協助您進行解決方案的監視或疑難排解。 

### <a name="view-captured-data-in-the-storage-account"></a>在儲存體帳戶中檢視已擷取的資料
1. 瀏覽至資源群組，然後選取用來擷取事件資料的儲存體帳戶。 
1. 在 [儲存體帳戶] 頁面上，選取左側功能表上的 [儲存體總管 (預覽)]。
1. 展開 [BLOB 容器]，然後選取 [windturbinecapture]。 
1. 在右窗格中，開啟與您的 **事件中樞命名空間** 同名的資料夾。 
1. 開啟與您的事件中樞同名的資料夾 (**hubdatamigration**)。 
1. 深入瀏覽資料夾，您會看到 AVRO 檔案。 以下是範例：

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="儲存體中已擷取的檔案" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>確認事件方格觸發程序已叫用函式
1. 瀏覽至資源群組，然後選取函式應用程式。 
1. 選取左側功能表上的 [函式]。
1. 從清單中選取 **EventGridTriggerMigrateData** 函式。 
1. 在 [函式] 頁面上，選取左側功能表上的 [監視器]。 
1. 選取 [設定] 進行 Application Insights 設定，以擷取叫用記錄。 
1. 建立新的 **Application Insights** 資源，或使用現有的資源。 
1. 瀏覽回函式的 [監視器] 頁面。 
1. 確認正在傳送事件的用戶端應用程式 (**WindTurbineDataGenerator**) 仍在執行中。 若非如此，請執行應用程式。 
1. 等候幾分鐘 (5 分鐘或更久)，然後選取 [重新整理] 按鈕以查看函式叫用。    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="函式叫用":::
1. 選取叫用以查看詳細資料。

    Event Grid 會將事件資料散發給訂閱者。 下列範例顯示在 Blob 中擷取透過事件中樞串流的資料時所產生的事件資料。 請特別留意，`data` 物件中的 `fileUrl` 屬性會指向儲存體中的 Blob。 函式應用程式會使用此 URL 來擷取包含已擷取資料的 Blob 檔案。

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>確認資料已儲存在專用 SQL 集區中
在已開啟查詢視窗的瀏覽器索引標籤中，查詢專用 SQL 集區中的資料表以找出遷移的資料。

![查詢結果](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

