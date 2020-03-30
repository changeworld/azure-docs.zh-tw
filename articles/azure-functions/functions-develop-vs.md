---
title: 使用 Visual Studio 來開發 Azure Functions
description: 瞭解如何使用 Visual Studio 2019 的 Azure 功能工具開發和測試 Azure 函數。
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: b3d683153a1e70f4c65dcc5e401f4ee702af8b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277098"
---
# <a name="develop-azure-functions-using-visual-studio"></a>使用 Visual Studio 來開發 Azure Functions  

Visual Studio 允許您開發、測試 C# 類庫功能並將其部署到 Azure。 如果這是您第一次體驗 Azure Functions，可至 [Azure Functions 簡介](functions-overview.md)深入了解。

視覺化工作室在開發功能時提供以下優勢： 

* 在本機開發電腦上編輯、建置及執行函數。 
* 將 Azure 函數專案直接發佈到 Azure，並根據需要創建 Azure 資源。 
* 使用 C# 屬性直接在 C# 代碼中聲明函數綁定。
* 開發及部署預先編譯的 C# 函數。 預先編譯的函數提供的冷啟動效能比 C# 指令碼型函數更好。 
* 在 C# 中編寫函數，同時享有 Visual Studio 開發的所有優點。 

本文詳細介紹了如何使用 Visual Studio 開發 C# 類庫函數並將其發佈到 Azure。 閱讀本文之前，您應該先完成[適用於 Visual Studio 的 Functions 快速入門](functions-create-your-first-function-visual-studio.md)。 

除非另有說明，否則所示過程和示例適用于 Visual Studio 2019。 

## <a name="prerequisites"></a>Prerequisites

Azure 功能工具組含在 Visual Studio 的 Azure 開發工作負荷中，從 Visual Studio 2017 開始。 請確保在視覺化工作室安裝中包括**Azure 開發**工作負荷。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

在發佈過程中，在訂閱中創建所需的其他資源（如 Azure 存儲帳戶）。

> [!NOTE]
> 在 Visual Studio 2017 中，Azure 開發工作負荷將 Azure 功能工具安裝為單獨的擴展。 更新 Visual Studio 2017 時，還要確保您使用的是[最新版本](#check-your-tools-version)的 Azure 函數工具。 以下各節介紹如何檢查和（如果需要）在 Visual Studio 2017 中更新 Azure 功能工具擴展。 
>
> 使用 Visual Studio 2019 時，請跳過這些部分。

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>在 Visual Studio 2017 中檢查您的工具版本

1. 在 [工具]**** 功能表中，選擇 [擴充功能和更新]****。 展開**已安裝** > **的工具**並選擇**Azure 函數和 Web 作業工具**。

    ![驗證 Functions 工具版本](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. 記下已安裝的**版本**。 您可以比較此版本[版本資訊中](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md)所列的最新版本。 

1. 如果您的版本較舊，請依照下一節中的說明在 Visual Studio 中更新您的工具。

### <a name="update-your-tools-in-visual-studio-2017"></a>在 Visual Studio 2017 中更新您的工具

1. 在 [延伸模組和更新]**** 對話方塊中，展開 [更新]**** > [Visual Studio Marketplace]****，選擇 [Azure Functions 與 Web 工作工具]****，然後選取 [更新]****。

    ![更新 Functions 工具版本](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. 下載工具更新之後，請關閉 Visual Studio，以使用 VSIX 安裝程式觸發工具更新。

1. 在安裝程式中選擇 [確定]**** 以啟動，然後選擇 [修改]**** 以更新工具。 

1. 更新完成後，請選擇 [關閉]**** 並重新啟動 Visual Studio。

> [!NOTE]  
> 在 Visual Studio 2019 及更高版本中，Azure 功能工具擴展將作為視覺化工作室的一部分進行更新。  

## <a name="create-an-azure-functions-project"></a>建立 Azure Functions 專案

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

專案範本會建立 C# 專案，安裝 `Microsoft.NET.Sdk.Functions` NuGet 套件，並設定目標架構。 新專案含有下列檔案：

* **host.json**：讓您設定 Functions 主機。 這些設定同時適用於在本機執行及在 Azure 中執行。 如需詳細資訊，請參閱 [host.json 參考](functions-host-json.md)。

* **local.settings.json**：維持在本機執行函數時所使用的設定。 在 Azure 中運行時，不使用這些設置。 有關詳細資訊，請參閱[本地設置檔](#local-settings-file)。

    >[!IMPORTANT]
    >由於 local.settings.json 檔案可以包含祕密，因此，您必須從專案原始檔控制中排除它。 這個檔案的 [複製到輸出目錄]**** 設定應該一律是 [有更新時才複製]****。 

如需詳細資訊，請參閱 [Functions 類別庫專案](functions-dotnet-class-library.md#functions-class-library-project)。

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

發佈專案時，不會自動上載本地.settings.json 中的設置。 為了確保這些設置也存在於 Azure 中的函數應用中，必須在發佈專案後上載這些設置。 要瞭解更多資訊，請參閱[功能應用設置](#function-app-settings)。

**ConnectionStrings** 中的值永遠不會發佈。

這些函數應用程式設定值在您的程式碼中也可以做為環境變數加以讀取。 有關詳細資訊，請參閱[環境變數](functions-dotnet-class-library.md#environment-variables)。

## <a name="configure-the-project-for-local-development"></a>設定專案以進行本機開發

函數執行階段會在內部使用 Azure 儲存體帳戶。 針對 HTTP 和 Webhook 以外的所有觸發程序類型，您必須將 **Values.AzureWebJobsStorage** 機碼設定為有效的 Azure 儲存體帳戶連接字串。 函式應用程式也可以針對專案所需的 **AzureWebJobsStorage** 連線設定，使用 [Azure 儲存體模擬器](../storage/common/storage-use-emulator.md)。 若要使用模擬器，將 **AzureWebJobsStorage** 的值設定為 `UseDevelopmentStorage=true`。 在部署之前將此設置更改為實際的存儲帳戶連接字串。

設定儲存體帳戶連接字串：

1. 在視覺化工作室中，打開**雲資源管理器**，展開**存儲帳戶** > **您的存儲帳戶**，然後在 **"屬性**"選項卡中複製**主連接字串**值。

2. 在您的專案中，開啟 local.settings.json 檔案並將 **AzureWebJobsStorage** 機碼的值設定為您所複製的連接字串。

3. 重複上一步，針對函數所需的其他任何連接，將唯一機碼新增至 [值]**** 陣列。 

## <a name="add-a-function-to-your-project"></a>將函式新增至您的專案

在 C# 類庫函數中，函數使用的綁定是通過在代碼中應用屬性來定義的。 從提供的範本創建函數觸發器時，將為您應用觸發器屬性。 

1. 在 [方案總管]**** 中，於專案節點上按一下滑鼠右鍵，然後選取 [新增]**** > [新增項目]****。 選取 [Azure 函數]****，輸入類別的 [名稱]****，然後按一下 [新增]****。

2. 選擇您的觸發程序，設定繫結屬性，然後按一下 [建立]****。 下列範例顯示建立佇列儲存體觸發之函數時的設定。 

    ![建立由佇列觸發的函式](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    這個觸發程序範例會使用連接字串，具有名為 **QueueStorage** 的索引鍵。 此連接字串設置必須在[本地.settings.json 檔中](functions-run-local.md#local-settings-file)定義。

3. 檢查新加入的類別。 您會看到靜態 [執行]**** 方法，它是使用 **FunctionName** 屬性來屬性化。 這個屬性指出該方法是函數的進入點。

    例如，下列 C# 類別代表基本佇列儲存體觸發的函數：

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

    會將繫結特定屬性套用至提供給進入點方法的每個繫結參數。 屬性會將繫結資訊作為參數使用。 在前面的示例中，第一個參數應用了**QueueTrigger**屬性，指示佇列觸發函數。 佇列名稱和連接字串設定名稱會作為參數傳遞至 **QueueTrigger** 屬性。 如需詳細資訊，請參閱 [Azure Functions 的 Azure 佇列儲存體繫結](functions-bindings-storage-queue-trigger.md)。

您可以使用上述程序，對函式應用程式專案新增更多函式。 專案中的每個函式都可以有不同的觸發程序，但函式必須只有一個觸發程序。 有關詳細資訊，請參閱[Azure 函數觸發器和綁定概念](functions-triggers-bindings.md)。

## <a name="add-bindings"></a>新增繫結

和觸發程序一樣，輸入和輸出繫結會新增至函式作為繫結屬性。 請將繫結新增至函式，如下所示：

1. 請確保您已[為本地開發配置了專案](#configure-the-project-for-local-development)。

2. 為特定繫結新增適當的 NuGet 擴充功能套件。 如需詳細資訊，請參閱＜觸發程序和繫結＞一文中的[使用 Visual Studio 的本機 C# 開發](./functions-bindings-register.md#local-csharp)。 繫結專屬的 NuGet 套件需求可於繫結的參考文章中找到。 例如，您可以在[事件中樞繫結參考文章](functions-bindings-event-hubs.md)中尋找事件中樞觸發程序的套件需求。

3. 如果有繫結所需的應用程式設定，請將其新增至[本機設定檔案](functions-run-local.md#local-settings-file)中的**值**集合。 當函式在本機執行時，就會使用這些值。 當函式在 Azure 中的函式應用程式中執行時，則會使用[函式應用程式設定](#function-app-settings)。

4. 在方法簽章中新增適當的繫結屬性。 在下列範例中，佇列訊息會觸發函式，而且輸出繫結會在不同的佇列中，使用相同文字建立新的佇列訊息。

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   佇列儲存體的連接是從 `AzureWebJobsStorage` 設定中取得的。 如需詳細資訊，請參閱特定繫結的參考文章。 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>測試函式

Azure Functions Core Tools 可讓您在本機開發電腦上執行 Azure Functions 專案。 第一次從 Visual Studio 啟動函式時，系統會提示您安裝這些工具。

若要測試您的函式，請按 F5。 如果出現提示，接受來自 Visual Studio 之下載及安裝 Azure Functions Core (CLI) 工具的要求。 您可能也需要啟用防火牆例外狀況，工具才能處理 HTTP 要求。

隨著專案的執行，您可以像測試部署函數一樣測試程式碼。 如需詳細資訊，請參閱[在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)。 在偵錯模式下執行時，會依預期在 Visual Studio 中遇到中斷點。 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

若要深入了解如何使用 Azure Functions Core Tools，請參閱[在本機撰寫和測試 Azure Functions 程式碼](functions-run-local.md)。

## <a name="publish-to-azure"></a>發佈至 Azure

從 Visual Studio 發佈時，將使用兩種部署方法之一：

* [Web 部署](functions-deployment-technologies.md#web-deploy-msdeploy)：打包並將 Windows 應用部署到任何 IIS 伺服器。
* [啟用運行包的 Zip 部署](functions-deployment-technologies.md#zip-deploy)：建議用於 Azure 函數部署。

使用以下步驟將專案發佈到 Azure 中的函數應用。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>函數應用程式設定

您在 local.settings.json 中所新增的所有設定，也必須新增至 Azure 中的函式應用程式。 發佈專案時，這些設置不會自動上載。

將必要設定上傳至 Azure 中函式應用程式的最簡單方式，是使用**管理應用程式設定...** 連結，該連結會在您成功發行專案之後顯示。

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

這會顯示函式應用程式的 [應用程式設定]**** 對話方塊，您可以在其中新增新的應用程式設定或修改現有的設定。

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

[本機]**** 代表 local.settings.json 檔案中的一個設定值，而 [遠端]**** 是 Azure 的函式應用程式中的目前設定。  選擇 **"添加設置"** 以創建新的應用設置。 使用 [從本機插入值]**** 連結，將設定值複製到 [遠端]**** 欄位。 當您選取 [確定]**** 時，暫止的變更會寫入至本機設定檔案和函式應用程式。

> [!NOTE]
> 預設情況下，本地.settings.json 檔不會簽入原始程式碼管理。 這意味著，當您從原始程式碼管理克隆本地函數專案時，該專案沒有 local.settings.json 檔。 在這種情況下，您需要在專案根目錄中手動創建 local.settings.json 檔，以便 **"應用程式設定"** 對話方塊按預期工作。 

您也可以使用下列其中一種方式管理應用程式設定：

* [使用 Azure 門戶](functions-how-to-use-azure-function-app-settings.md#settings)。
* [使用 Azure Functions Core Tools 中的 `--publish-local-settings` 發行選項](functions-run-local.md#publish)。
* [使用 Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)。

## <a name="monitoring-functions"></a>監視函式

若要監視您的函式的執行情況，建議您整合您的函式應用程式與 Azure Application Insights。 當您在 Azure 入口網站中建立函式應用程式時，系統會依預設為您完成這項整合。 不過，當您在 Visual Studio 發佈期間建立函式應用程式時，則不會完成您的函式應用程式在 Azure 中的整合。

若要為您的函式應用程式啟用 Application Insights：

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

若要深入了解，請參閱[監視 Azure Functions](functions-monitoring.md)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Functions Core Tools，請參閱[在本機撰寫和測試 Azure Functions 程式碼](functions-run-local.md)。

若要深入了解如何將函式開發為 .NET 類別庫，請參閱 [Azure Functions C# 開發人員參考](functions-dotnet-class-library.md)。 本文也會連結至範例以示範如何使用屬性宣告 Azure Functions 所支援的各種繫結類型。    
