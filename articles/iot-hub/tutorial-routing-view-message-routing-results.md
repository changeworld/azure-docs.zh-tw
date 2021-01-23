---
title: 教學課程-觀看 Azure IoT 中樞訊息路由結果 ( .NET) |Microsoft Docs
description: 教學課程-使用教學課程的第1部分設定所有資源之後，請新增將訊息路由至 Azure 串流分析的功能，並在 Power BI 中查看結果。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: fdfb2470e83cefaeb6df35115f7bb46149d8ffe6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733918"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>教學課程：第 2 部分 - 檢視路由的訊息

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>訊息的路由規則

以下是訊息的路由規則；這些規則已於本教學課程的第 1 部分完成設定，您將會在這個第二部分中看到其運作方式。

|值 |結果|
|------|------|
|level="storage" |寫入至 Azure 儲存體。|
|level="critical" |寫入至服務匯流排佇列。 邏輯應用程式會從佇列擷取訊息，並使用 Office 365 以電子郵件傳送訊息。|
|預設 |使用 Power BI 顯示此資料。|

現在，您要建立將作為訊息路由傳送目的地的資源、執行應用程式將訊息傳送至中樞，然後看看路由的運作方式。

## <a name="create-a-logic-app"></a>建立邏輯應用程式  

服務匯流排佇列會用來接收指定為 critical 的訊息。 設定邏輯應用程式來監視服務匯流排佇列，並在訊息新增至佇列時傳送電子郵件。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [+ 建立資源]  。 在 [搜尋] 方塊中輸入 **邏輯應用程式**，然後按一下 Enter。 從顯示的搜尋結果中選取邏輯應用程式，然後選取 [建立]  繼續前往 [建立邏輯應用程式]  窗格。 填寫欄位。

   **Name**：此欄位是邏輯應用程式的名稱。 本教學課程使用 **ContosoLogicApp**。

   訂用帳戶  ：選取 Azure 訂用帳戶。

   **資源群組**：選取 [使用現有的]  並選取您的資源群組。 本教學課程使用 **ContosoResources**。

   **位置**：使用您的位置。 本教學課程使用 **美國西部**。

   **啟用 Log Analytics**：應關閉此切換開關。

   ![[建立邏輯應用程式] 畫面](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   選取 [建立]  。 部署應用程式可能需要幾分鐘的時間。

2. 現在請移至邏輯應用程式。 取得邏輯應用程式最簡單的方式是選取 [資源群組]  ，選取您的資源群組 (本教學課程使用 **ContosoResources**)，然後從資源清單中選取邏輯應用程式。 

    Logic Apps 設計工具頁面隨即顯示 (您可能需要捲動至右側才能看到完整頁面)。 在 Logic Apps 設計工具頁面上，向下捲動直到您看到標示 [空白邏輯應用程式 +]  的圖格，然後加以選取。 預設索引標籤是 [提供給您]。 如果此窗格空無一物，請選取 [全部]  以查看所有可用的連接器和觸發程序。

3. 從連接器清單中選取 [服務匯流排]  。

   ![連接器清單](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. 觸發程序清單隨即顯示。 選取 [佇列中收到訊息時 (自動完成)/服務匯流排]  。

   ![服務匯流排的觸發程序清單](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. 在下一個畫面上，填寫連線名稱。 本教學課程使用 **ContosoConnection**。

   ![設定服務匯流排佇列的連線](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   選取 [服務匯流排命名空間]。 本教學課程使用 **ContosoSBNamespace**。 當您選取命名空間時，入口網站會查詢服務匯流排命名空間來擷取金鑰。 選取 [RootManageSharedAccessKey]  ，然後選取 [建立]  。

   ![完成連線的設定](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. 在下一個畫面上，從下拉式清單中選取佇列的名稱 (本教學課程使用 **contososbqueue**)。 其他欄位可使用預設值。

   ![佇列選項](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. 現在要設定在佇列中收到訊息時會傳送電子郵件的動作。 在 Logic Apps 設計工具中，選取 [+ 新增步驟]  以新增步驟，然後選取 [全部]  以查看所有可用的選項。 在 [選擇動作]  窗格中，尋找並選取 [Office 365 Outlook]  。 在 [動作] 畫面上，選取 [傳送電子郵件/Office 365 Outlook]  。  

   ![Office365 選項](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. 登入您的公司或學校帳戶來設定連線。 如果此程序逾時，請直接再試一次。 指定電子郵件收件者的電子郵件地址。 也請指定主旨，然後在本文中輸入要讓收件者看到的訊息。 為了進行測試，請填寫您自己的電子郵件地址作為收件者。

   選取 [新增動態內容]  會顯示您可以包含的訊息內容。 選取 [內容]  -- 訊息將會包含在電子郵件中。

   ![邏輯應用程式的電子郵件選項](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. 選取 [儲存]  。 然後關閉 Logic Apps 設計工具。

## <a name="set-up-azure-stream-analytics"></a>設定 Azure 串流分析

若要在 Power BI 視覺效果中查看資料，請先設定串流分析作業來擷取資料。 請記住，只有 **level** 是 **normal** 的訊息會傳送至預設的端點，並由串流分析作業擷取該訊息以用於 Power BI 視覺效果。

### <a name="create-the-stream-analytics-job"></a>建立串流分析作業

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]   > [物聯網]   > [串流分析作業]  。

2. 輸入作業的以下資訊。

   **作業名稱**：作業的名稱。 此名稱必須是全域唯一的。 本教學課程使用 **contosoJob**。

   訂用帳戶  ：您將在本教學課程中使用的 Azure 訂用帳戶。

   **資源群組**：使用 IoT 中樞所用的相同資源群組。 本教學課程使用 **ContosoResources**。

   **位置**：使用安裝指令碼中使用的相同位置。 本教學課程使用 **美國西部**。

   ![建立串流分析作業](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. 選取 [建立]  來建立作業。 部署可能需要幾分鐘的時間。

    若要回到該作業，請選取 [資源群組]  。 本教學課程使用 **ContosoResources**。 選取資源群組，然後選取資源清單中的串流分析作業。

### <a name="add-an-input-to-the-stream-analytics-job"></a>將輸入新增至串流分析作業

1. 在 [作業拓撲]  下方，選取 [輸入]  。

2. 在 [輸入]  窗格中，選取 [新增資料流輸入]  並選取 IoT 中樞。 在顯示的畫面上，填寫下列欄位：

   **輸入別名**：本教學課程使用 **contosoinputs**。

   **從訂用帳戶選取 IoT 中樞**：選取此選項按鈕選項。

   訂用帳戶  ：選取您要用於本教學課程的 Azure 訂用帳戶。

   **IoT 中樞**：選取 IoT 中樞。 本教學課程使用 **ContosoTestHub**。

   **端點**：選取 [傳訊]  。 (如果您選取「作業監視」，則您會取得有關 IoT 中樞的遙測資料，而不是您要傳送的資料。) 

   **共用存取原則名稱**：選取 [服務]  。 入口網站會為您填入共用存取原則金鑰。

   **取用者群組**：選取您在本教學課程的第 1 部分中設定的取用者群組。 本教學課程使用 **contosoconsumers**。
   
   對於其餘欄位，請採用預設值。 

   ![設定串流分析作業的輸入](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. 選取 [儲存]  。

### <a name="add-an-output-to-the-stream-analytics-job"></a>將輸出新增至串流分析作業

1. 在 [作業拓撲]  下方，選取 [輸出]  。

2. 在 [輸出]  窗格中，選取 [新增]  ，然後選取 [Power BI]  。 在顯示的畫面上，填寫下列欄位：

   **輸出別名**：輸出的唯一別名。 本教學課程使用 **contosooutputs**。 

   **資料集名稱**：Power BI 中要使用的資料集名稱。 本教學課程使用 **contosodataset**。 

   **資料表名稱**：Power BI 中要使用的資料表名稱。 本教學課程使用 **contosotable**。

  **驗證模式**：選取要使用的模式。

   對於其餘欄位，請採用預設值。

3. 選取 [授權]  ，然後登入您的 Power BI 帳戶。 (這可能需要嘗試多次)。

   ![設定串流分析作業的輸出](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. 選取 [儲存]  。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>設定串流分析作業的查詢

1. 在 [作業拓撲]  下方，選取 [查詢]  。

2. 使用作業的輸入別名取代 `[YourInputAlias]`。 本教學課程使用 **contosoinputs**。

3. 使用作業的輸出別名取代 `[YourOutputAlias]`。 本教學課程使用 **contosooutputs**。

   ![設定串流分析作業的查詢](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. 選取 [儲存]  。

5. 關閉 [查詢] 窗格。 您便會回到資源群組中的資源檢視。 選取該串流分析作業。 本教學課程將它稱為 **contosoJob**。

### <a name="run-the-stream-analytics-job"></a>執行串流分析作業

在串流分析作業中，選取 [啟動]   > [立即]   > [啟動]  。 成功啟動作業後，作業狀態會從 [已停止]  變更為 [執行中]  。

由於您需要資料來設定 Power BI 報表，因此您要先建立裝置並執行裝置模擬應用程式，然後再設定 Power BI。

## <a name="run-simulated-device-app"></a>執行模擬裝置應用程式

在本教學課程的第 1 部分中，您已設定裝置來模擬使用 IoT 裝置。 在本節中，您會下載 .NET 主控台應用程式，來模擬讓裝置將「裝置到雲端的訊息」傳送至 IoT 中樞 (假設您尚未下載第 1 部分中的應用程式和資源)。

此應用程式會針對每個不同的訊息路由方式傳送訊息。 該下載中也有一個資料夾，內含完整的 Azure Resource Manager 範本和參數檔案，以及 Azure CLI 和 PowerShell 指令碼。

如果您未從本教學課程第 1 部分中的存放庫下載檔案，請立即從 [IoT 裝置模擬](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)下載檔案。 選取此連結會下載內含數個應用程式的存放庫；您要尋找的解決方案是 iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln。 

對解決方案檔案 (IoT_SimulatedDevice.sln) 按兩下以在 Visual Studio 中開啟程式碼，然後開啟 Program.cs。 以 IoT 中樞的主機名稱取代 `{your hub name}`。 IoT 中樞主機名稱的格式是 **{iot-hub-name}.azure-devices.net**。 在此教學課程中，中樞主機名稱是 **ContosoTestHub.azure-devices.net**。 接下來，以您稍早設定模擬裝置時儲存的裝置金鑰取代 `{your device key}`。 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>執行和測試

執行主控台應用程式。 請等待數分鐘。 在應用程式的主控台畫面上，您可以看到正在傳送的訊息。

應用程式每一秒就會將最新裝置到雲端的訊息傳送至 IoT 中樞。 訊息包含 JSON 序列化的物件與裝置識別碼、溫度、溼度和訊息層級 (預設位置為 `normal`)。 應用程式會隨機指派 `critical` 或 `storage` 層級，讓訊息可路由至儲存體帳戶或服務匯流排佇列 (進而觸發邏輯應用程式以傳送電子郵件)。 預設 (`normal`) 的讀取將會顯示在稍後設定的 BI 報表中。

如果所有項目皆已正確設定，此時您應該會看到下列結果：

1. 您會開始收到關於重大訊息的電子郵件。

   ![產生的電子郵件](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   此結果表示下列陳述式為 true。 

   * 服務匯流排佇列的路由可正確運作。
   * 從服務匯流排佇列擷取訊息的邏輯應用程式可正常運作。
   * Outlook 的邏輯應用程式連接器可正常運作。 

2. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [資源群組]  ，然後選取您的資源群組。 本教學課程使用 **ContosoResources**。 

    選取儲存體帳戶，選取 [容器]  ，然後選取 [容器]。 本教學課程使用 **contosoresults**。 您應該會看到資料夾，然後您可以向下鑽研這些目錄，直到您看到一個或多個檔案。 開啟這些檔案的其中一個，其中會包含路由至儲存體帳戶的項目。 

   ![儲存體中的結果檔案](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

此結果表示下列陳述式為 true。

   * 儲存體帳戶的路由可正確運作。

趁著應用程式仍在執行時，現在請設定 Power BI 視覺效果以查看來自預設路由的訊息。

## <a name="set-up-the-power-bi-visualizations"></a>設定 Power BI 視覺效果

1. 登入您的 [Power BI](https://powerbi.microsoft.com/) 帳戶。

2. 請移至 **工作區**，然後選取建立串流分析作業輸出時所設定的工作區。 本教學課程使用 **My Workspace**。 

3. 選取 [資料集]  。 如果沒有資料集，請稍候幾分鐘，然後再檢查一次。

   您應該會看到在建立串流分析作業輸出時所指定的資料集。 本教學課程使用 **contosodataset**。 (第一次執行可能需要 5-10 分鐘才會出現資料集)。

4. 在 [動作]  之下，選取 第一個圖示以建立報告。

   ![Power BI 工作區與動作，以及醒目提示的 [報告] 圖示](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. 建立折線圖以顯示一段時間的即時溫度。

   * 在報表建立頁面上，選取折線圖的圖示來新增折線圖。

     ![視覺效果和欄位](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * 在 [欄位]  窗格上，展開您建立串流分析作業輸出時指定的資料表。 本教學課程使用 **contosotable**。

   * 將 **EventEnqueuedUtcTime** 拖放至 [視覺效果]  窗格上的 [軸]  。

   * 將 **temperature** 拖放至 [值]  。

   折線圖已建立。 x 軸會顯示 UTC 時區的日期和時間。 y 軸會顯示感應器的溫度。

6. 建立另一個折線圖以顯示一段時間的即時溼度。 若要設定第二個圖表，請依照第一個圖表的相同程序，將 **EventEnqueuedUtcTime** 放在 X 軸上 (**軸**)，並將 **humidity** 放在 Y 軸上 (**值**)。

   ![最終的 Power BI 報告與兩個圖表](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. 選取 [儲存]  ，並在出現提示時輸入報表的名稱，以儲存報表。

您應該會看到兩個圖表的資料。 此結果表示下列陳述式為 true：

   * 預設端點的路由可正確運作。
   * Azure 串流分析作業工作可正確串流。
   * Power BI 視覺效果已正確設定。

您可以選取 Power BI 視窗頂端的 [重新整理] 按鈕，以查看最新資料的圖表。 

## <a name="clean-up-resources"></a>清除資源 

如果您想要移除透過本教學課程的這兩個部分所建立的所有 Azure 資源，請刪除資源群組。 此動作會同時刪除群組內含的所有資源。 在此案例中，則會移除 IoT 中樞、服務匯流排命名空間和佇列、邏輯應用程式、儲存體帳戶和資源群組本身。 您也可以移除 Power BI 資源，並清除教學課程期間傳送的電子郵件。

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>清除 Power BI 視覺效果中的資源

登入您的 [Power BI](https://powerbi.microsoft.com/) 帳戶。 移至工作區。 本教學課程使用 **My Workspace**。 若要移除 Power BI 視覺效果，請移至 DataSets，然後選取 [垃圾桶] 圖示來刪除資料集。 本教學課程使用 **contosodataset**。 當您移除資料集時，報表也會一併移除。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 來清除資源

若要移除資源群組，請使用 [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete&preserve-view=true) 命令。 在本教學課程剛開始時，`$resourceGroup` 已設回 **ContosoResources**。

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>使用 PowerShell 來清除資源

若要移除資源群組，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令。 在本教學課程剛開始時，`$resourceGroup` 已設回 **ContosoResources**。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>清除測試電子郵件

您也可以從收件匣中刪除在裝置應用程式執行時透過邏輯應用程式產生的電子郵件數量。

## <a name="next-steps"></a>後續步驟

在這個有兩個部分的教學課程中，您已學會如何藉由執行下列工作，使用訊息路由將 IoT 中樞訊息路由至不同目的地。  

**第一部分：建立資源、設定訊息路由**
> [!div class="checklist"]
> * 建立資源 - IoT 中樞、儲存體帳戶、服務匯流排佇列和模擬裝置。
> * 在 IoT 中樞內設定儲存體帳戶和服務匯流排佇列的端點及訊息路由。

**第二部分：將訊息傳送至中樞、檢視路由的結果**
> [!div class="checklist"]
> * 建立邏輯應用程式，使其在訊息新增至服務匯流排佇列時觸發並傳送電子郵件。
> * 下載並執行模擬 IoT 裝置的應用程式，針對不同路由選項將訊息傳送至中樞。
> * 為傳送至預設端點的資料建立 Power BI 視覺效果。
> * 檢視結果...
> * ...在服務匯流排佇列和電子郵件中。
> * ...在儲存體帳戶中。
> * ...在 Power BI 視覺效果中。

前進至下一個教學課程，以了解如何管理 IoT 裝置的狀態。 

> [!div class="nextstepaction"]
> [搭配 IoT 中樞來設定及使用計量和診斷記錄](tutorial-use-metrics-and-diags.md)