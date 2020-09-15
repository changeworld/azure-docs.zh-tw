---
title: 教學課程：將事件中樞資料傳送至資料倉儲 - 事件方格
description: 教學課程：說明如何使用 Azure 事件方格和事件中樞將資料遷移至 Azure Synapse Analytics。 它會使用 Azure 函式來擷取「擷取」檔案。
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d45fcedb570e384b851a7ac815ca175c67cc00a0
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435026"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>教學課程：將巨量資料串流處理至資料倉儲
Azure [Event Grid](overview.md) 是一項智慧型事件路由服務，可讓您對應用程式和服務發出的通知 (事件) 做出回應。 例如，它可以觸發 Azure 函式以處理已擷取至 Azure Blob 儲存體或 Azure Data Lake Storage 的事件中樞資料，並將資料移轉至其他資料存放庫。 此[事件中樞和事件方格整合範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)說明如何使用事件中樞與事件方格，將已擷取的事件中樞資料從 Blob 儲存體順暢地遷移至 Azure Synapse Analytics (先前為 SQL 資料倉儲)。

![應用程式概觀](media/event-grid-event-hubs-integration/overview.png)

下圖說明您在本教學課程中建置的解決方案所採用的工作流程︰ 

1. 將傳送至 Azure 事件中樞的資料擷取到 Azure Blob 儲存體中。
2. 資料擷取完成時就會產生事件，並傳送至 Azure 事件方格。 
3. 事件方格會將此事件資料轉送至 Azure 函式應用程式。
4. 此函式應用程式會使用事件資料中的 Blob URL 來擷取儲存體中的 Blob。 
5. 函式應用程式會將 Blob 資料遷移至 Azure Synapse Analytics。 

在本文中，您會執行下列步驟：

> [!div class="checklist"]
> * 使用 Azure Resource Manager 範本部署基礎結構：事件中樞、儲存體帳戶、函式應用程式、Synapse Analytics。
> * 在資料倉儲中建立資料表。
> * 將程式碼新增至函式應用程式。
> * 訂閱事件。 
> * 執行應用程式，將資料傳送至事件中樞。
> * 在資料倉儲中檢視移轉的資料。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要完成本教學課程，您必須具備：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* [Visual Studio 2019](https://www.visualstudio.com/vs/)，搭配適用於 .NET 桌面開發、Azure 開發、ASP.NET 和 Web 開發、Node.js 開發及 Python 開發的工作負載。
* 將 [EventHubsCaptureEventGridDemo 範例專案](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)下載到您的電腦。

## <a name="deploy-the-infrastructure"></a>部署基礎結構
在此步驟中，您會使用 [Resource Manager 範本](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)來部署必要的基礎結構。 在部署範本時，會建立下列資源：

* 已啟用擷取功能的事件中樞。
* 已擷取的檔案適用的儲存體帳戶。 
* 用來裝載函式應用程式的 App Service 方案
* 函式應用程式，用來處理事件
* SQL Server，用來裝載資料倉儲
* 用於儲存已遷移資料的 Azure Synapse Analytics

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>在 Azure 入口網站中啟動 Azure Cloud Shell

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 選取位於最上方的 [Cloud Shell] 按鈕。

    ![Azure 入口網站](media/event-grid-event-hubs-integration/azure-portal.png)
3. 您會看到 Cloud Shell 在瀏覽器底部開啟。

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. 在 Cloud Shell 中，如果您看到選取 **Bash** 或 **PowerShell** 的選項，請選取 **Bash**。 
5. 如果您是第一次使用 Cloud Shell，請選取 [建立儲存體] 以建立儲存體帳戶。 Azure Cloud Shell 需要以 Azure 儲存體帳戶儲存某些檔案。 

    ![建立 Cloud Shell 的儲存體](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. 等待 Cloud Shell 完成初始化。 

    ![建立 Cloud Shell 的儲存體](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>使用 Azure CLI

1. 執行下列 CLI 命令以建立 Azure 資源群組： 
    1. 複製下列命令並貼到 Cloud Shell 視窗中。 視需要變更資源群組名稱和位置。

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. 按 ENTER 鍵。 

        範例如下：
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. 執行下列 CLI 命令，以部署上一節中提到的所有資源 (事件中樞、儲存體帳戶、函式應用程式、Azure Synapse Analytics)： 
    1. 複製下列命令並貼到 Cloud Shell 視窗中。 或者，您可以複製/貼到自己選擇的編輯器中，並在設定值之後將命令複製到 Cloud Shell 中。 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. 指定下列實體的值：
        1. 您先前建立的資源群組名稱。
        2. 事件中樞命名空間的名稱。 
        3. 事件中樞的名稱。 您可以保留原有的值 (hubdatamigration)。
        4. SQL 伺服器的名稱。
        5. SQL 使用者的名稱和密碼。 
        6. Azure Synapse Analytics 的名稱
        7. 儲存體帳戶的名稱。 
        8. 函式應用程式的名稱。 
    3.  在 Cloud Shell 視窗中按 **ENTER** 鍵，以執行命令。 此程序可能需要一些時間，因為您會建立大量資源。 在命令的結果中，確定沒有任何失敗的狀況。 
    

### <a name="use-azure-powershell"></a>使用 Azure PowerShell

1. 在 Azure Cloud Shell 中，切換為 PowerShell 模式。 選取位於 Azure Cloud Shell 左上角的向下箭號，然後選取 [PowerShell]。

    ![切換為 PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. 執行下列命令以建立 Azure 資源群組： 
    1. 複製下列命令並貼到 Cloud Shell 視窗中。

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location eastus
        ```
    2. 指定**資源群組**的名稱。
    3. 按 ENTER 鍵。 
3. 執行下列命令，以部署上一節中提到的所有資源 (事件中樞、儲存體帳戶、函式應用程式、Azure Synapse Analytics)：
    1. 複製下列命令並貼到 Cloud Shell 視窗中。 或者，您可以複製/貼到自己選擇的編輯器中，並在設定值之後將命令複製到 Cloud Shell 中。 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. 指定下列實體的值：
        1. 您先前建立的資源群組名稱。
        2. 事件中樞命名空間的名稱。 
        3. 事件中樞的名稱。 您可以保留原有的值 (hubdatamigration)。
        4. SQL 伺服器的名稱。
        5. SQL 使用者的名稱和密碼。 
        6. Azure Synapse Analytics 的名稱
        7. 儲存體帳戶的名稱。 
        8. 函式應用程式的名稱。 
    3.  在 Cloud Shell 視窗中按 **ENTER** 鍵，以執行命令。 此程序可能需要一些時間，因為您會建立大量資源。 在命令的結果中，確定沒有任何失敗的狀況。 

### <a name="close-the-cloud-shell"></a>關閉 Cloud Shell 
選取入口網站中的 **Cloud Shell** 按鈕 (或) Cloud Shell 視窗右上角的 **X** 按鈕，以關閉 Cloud Shell。 

### <a name="verify-that-the-resources-are-created"></a>確認資源已建立

1. 在 Azure 入口網站中，選取左側功能表上的 [資源群組]。 
2. 在搜尋方塊中輸入您資源群組的名稱，以篩選資源群組的清單。 
3. 在清單中選取您的資源群組。

    ![選取您的資源群組](media/event-grid-event-hubs-integration/select-resource-group.png)
4. 確認您在資源群組中看到下列資源：

    ![資源群組中的資源](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中建立資料表
執行 [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) 指令碼，在您的資料倉儲中建立資料表。 若要執行指令碼，您可以在入口網站中使用 Visual Studio 或查詢編輯器。 下列步驟說明如何使用查詢編輯器： 

1. 在資源群組中的資源清單內，選取您的 **Synapse SQL 集區 (資料倉儲)** 。 
2. 在 Azure Synapse Analytics 頁面中，選取左側功能表中的 [查詢編輯器 (預覽)]。 

    ![Azure Synapse Analytics 頁面](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. 輸入 SQL 伺服器的**使用者名稱**和**密碼**，然後選取 [確定]。 您可能需要將您的用戶端 IP 位址新增到防火牆，才能成功登入 SQL 伺服器。 

    ![SQL 伺服器驗證](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. 在查詢視窗中，複製並執行下列 SQL 指令碼： 

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

    ![執行 SQL 查詢](media/event-grid-event-hubs-integration/run-sql-query.png)
5. 讓此索引標籤或視窗保持開啟，以便您在本教學課程結尾處確認資料已建立。 

### <a name="update-the-function-runtime-version"></a>更新函式執行階段版本

1. 在 Azure 入口網站中，選取左側功能表上的 [資源群組]。
2. 選取函數應用程式所在的資源群組。 
3. 在資源群組中的資源清單內，選取 **App Service** 類型的函數應用程式。
4. 選取左側功能表上 [設定] 下方的 [設定]。 
5. 切換到右側窗格的 [函式執行階段設定] 索引標籤。 
5. 將 [執行階段版本] 更新為 **~3**。 

    ![更新函式執行階段版本](media/event-grid-event-hubs-integration/function-runtime-version.png)
    

## <a name="publish-the-azure-functions-app"></a>發佈 Azure Functions 應用程式

1. 啟動 Visual Studio。
2. 開啟您為了符合必要條件而從 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) 下載的 **EventHubsCaptureEventGridDemo.sln** 解決方案。
3. 在 [方案總管] 中，以滑鼠右鍵按一下 [FunctionEGDWDumper]，然後選取 [發佈]。

   ![發佈函數應用程式](media/event-grid-event-hubs-integration/publish-function-app.png)
4. 如果您看到下列畫面，請選取 [開始]。 

   ![開始發佈按鈕](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. 在 [發佈] 對話方塊中，針對 [目標] 選取 [Azure]，然後選取 [下一步]。 

   ![開始發佈按鈕](media/event-grid-event-hubs-integration/publish-select-azure.png)
6. 選取 [Azure 函數應用程式 (Windows)]，然後選取 [下一步]。 

   ![選取 Azure 函數應用程式 - Windows](media/event-grid-event-hubs-integration/select-azure-function-windows.png)
7. 在 [函式執行個體] 索引標籤上，選取您的 Azure 訂用帳戶，展開資源群組，選取您的函數應用程式，然後選取 [完成]。 您需要登入 Azure 帳戶 (若您尚未登入)。 

   ![選取函式應用程式](media/event-grid-event-hubs-integration/publish-select-function-app.png)
8. 在 [服務相依性] 區段中，選取 [設定]。
9. 在 [設定相依性] 頁面上，選取您稍早建立的儲存體帳戶，然後選取 [下一步]。 
10. 保留連接字串名稱和值的設定，然後選取 [下一步]。
11. 清除 [祕密存放區] 選項，然後選取 [完成]。  
8. 當 Visual Studio 完成設定檔設定時，選取 [發佈]。

   ![Select publish](media/event-grid-event-hubs-integration/select-publish.png)

發行函式之後，您已準備好訂閱事件。

## <a name="subscribe-to-the-event"></a>訂閱事件

1. 在網頁瀏覽器的新索引標籤或新視窗中，瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站中，選取左側功能表上的 [資源群組]。 
3. 在搜尋方塊中輸入您資源群組的名稱，以篩選資源群組的清單。 
4. 在清單中選取您的資源群組。

    ![選取您的資源群組](media/event-grid-event-hubs-integration/select-resource-group.png)
4. 在資源群組中的資源清單內，選取 App Service 方案 (而非 App Service)。 
5. 在 [App Service 方案] 頁面中，選取左側功能表中的 [應用程式]，然後選取函式應用程式。 

    ![選取您的函式應用程式](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. 展開函式應用程式、展開函式，然後選取您的函式。 
7. 選取工具列上的 [新增事件方格訂用帳戶]。 

    ![選取您的 Azure 函式](media/event-grid-event-hubs-integration/select-function-add-button.png)
8. 在 [建立事件方格訂用帳戶] 頁面中，執行下列動作： 
    1. 在 [事件訂用帳戶詳細資料] 頁面中，輸入訂用帳戶的名稱 (例如 captureEventSub)，然後選取 [建立]。 
    2. 在 [主題詳細資料] 區段中，執行下列動作：
        1. 針對 [主題類型] 選取 [事件中樞命名空間]。 
        2. 選取 Azure 訂用帳戶。
        2. 選取 Azure 資源群組。
        3. 選取您的事件中樞命名空間。
    3. 在 [事件類型] 區段中，確認已針對 [篩選至事件類型] 選取 [擷取建立的檔案]。 
    4. 在 [端點詳細資料] 區段中，確認 [端點類型] 已設為 [Azure 函式]，且 [端點] 已設為 [Azure 函式]。 
    
        ![建立事件方格訂用帳戶](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>執行應用程式以產生資料
您已完成事件中樞、Azure Synapse Analytics、Azure 函式應用程式及事件訂閱的設定。 在執行產生事件中樞資料的應用程式之前，您需要設定幾個值。

1. 在 Azure 入口網站中，再次瀏覽至您的資源群組。 
2. 選取事件中樞命名空間。
3. 在 [事件中樞命名空間] 頁面中，選取左側功能表上的 [共用存取原則]。
4. 選取原則清單中的 **RootManageSharedAccessKey**。 
5. 選取 [連接字串 - 主要金鑰] 文字方塊旁的複製按鈕。 

    ![事件中樞命名空間的連接字串](media/event-grid-event-hubs-integration/get-connection-string.png)
1. 返回您的 Visual Studio 解決方案。 
2. 在 WindTurbineDataGenerator 專案中，開啟 **program.cs**。
5. 取代兩個常數值。 使用複製的值來取代 **EventHubConnectionString**。 使用 **hubdatamigration** 作為事件中樞名稱。 如果您的事件中樞使用不同的名稱，請指定該名稱。 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. 建置方案。 執行 **WindTurbineGenerator.exe** 應用程式。 
7. 在幾分鐘之後，查詢資料倉儲中的資料表來找出移轉的資料。

    ![查詢結果](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>事件中樞所產生的事件資料
Event Grid 會將事件資料散發給訂閱者。 下列範例顯示在 Blob 中擷取透過事件中樞串流的資料時所產生的事件資料。 請特別留意，`data` 物件中的 `fileUrl` 屬性會指向儲存體中的 Blob。 函式應用程式會使用此 URL 來擷取包含已擷取資料的 Blob 檔案。

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>後續步驟

* 若要了解 Azure 傳訊服務的差異，請參閱[在傳遞訊息的 Azure 服務之間做選擇](compare-messaging-services.md)。
* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 如需事件中樞擷取的簡介，請參閱[使用 Azure 入口網站啟用事件中樞擷取](../event-hubs/event-hubs-capture-enable-through-portal.md)。
* 如需設定及執行範例的詳細資訊，請參閱[事件中樞擷取和 Event Grid 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)。
