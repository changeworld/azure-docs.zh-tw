---
title: 使用 VS 開發與部署 Web 工作
description: 瞭解如何在可視化工作室中開發 Azure Web 作業並將其部署到 Azure 應用服務,包括創建計畫任務。
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 75e1f5fbfa41ac310d2a737dcfe28199715a094b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312868"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>使用 Visual Studio 開發和部署 WebJob - Azure App Service

本文介紹如何使用 Visual Studio 將主控台應用程式項目作為[Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226)將控制台應用程式專案部署到[應用服務](overview.md)中的 Web 應用。 如需如何使用 [Azure 入口網站](https://portal.azure.com)部署 WebJob 的相關資訊，請參閱[使用 WebJob 執行背景工作](webjobs-create.md)。

您可以將多個 Web 作業發表到單個 Web 應用。 確保 Web 應用中的每個 WebJob 都有唯一的名稱。

[Azure WebJobs SDK](webjobs-sdk-how-to.md)的版本 3.x 允許您開發以 .NET 核心應用或 .NET 框架應用運行的 Web 作業,而版本 2.x 僅支援 .NET 框架。 部署 WebJobs 專案的方式對於 .NET Core 專案與 .NET 框架專案不同。

## <a name="webjobs-as-net-core-console-apps"></a>Web 作業作為 .NET 核心控制台應用

使用 Web 作業的版本 3.x 時,您可以創建 Web 作業並將其發表為 .NET 核心控制台應用。 有關建立 .NET Core 主控台應用程式並將其發表到 Azure 作為 WebJob 的分步說明,請參閱[Azure Web 作業 SDK 開始進行事件驅動的後台處理](webjobs-sdk-get-started.md)。

> [!NOTE]
> .NET 核心 Web 作業不能與 Web 專案連結。 如果需要使用 Web 應用程式部署 WebJob,則應[將 WebJob 建立為 .NET 框架主控台應用](#webjobs-as-net-framework-console-apps)。  

### <a name="deploy-to-azure-app-service"></a>部署到 Azure App Service

從 Visual Studio 向應用服務發佈 .NET 核心 WebJob 使用與發佈 ASP.NET 核心應用相同的工具。

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>WebJob 類型

默認情況下,從 .NET Core 主控台專案發布的 WebJob 僅在觸發或按需運行時運行。 您還可以更新專案以[按計劃運行](#scheduled-execution)或連續運行。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>排程執行

將 .NET Core 主控台應用程式發布到 Azure 時,將添加新*的設置.job*檔添加到專案中。 使用此檔可為 WebJob 設置執行計畫。 有關詳細資訊,請參閱[計畫觸發的 WebJob](#scheduling-a-triggered-webjob)。

#### <a name="continuous-execution"></a>連續執行

您可以使用 Visual Studio 將 WebJob 更改為在 Azure 中啟用「始終打開」時連續運行。

1. 如果尚未這樣做,則[將項目發布到 Azure](#deploy-to-azure-app-service)。

1. 在 [方案總管]  中，以滑鼠右鍵按一下專案並選取 [發佈]  。

1. 在 **「發布」** 選項卡中,選擇 **「設定**」 。 

1. 在 **「設定檔設定」** 對話方塊中,為**Web 作業類型**選擇 **「連續」** 然後選擇 **「儲存**」 。

    ![發布 Web 工作的設定對話框](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. 選擇 **「發布」** 以使用更新的設定重新發佈 WebJob。

## <a name="webjobs-as-net-framework-console-apps"></a>Web 作業為 .NET 架構主控台應用程式  

當 Visual Studio 部署啟用 WebJobs 的 .NET 框架主控台應用程式專案時,它會將執行時檔複製到 Web 應用中的相應資料夾(連續 Web 作業*App_Data/作業 /連續*進行,並且為計畫或按需 Web 作業*App_Data/作業 / 觸發*)。

具有 WebJobs 功能的專案會新增下列項目：

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 封裝。
* 包含部署和排程器設定的 [webjob-publish-settings.json](#publishsettings) 檔案。 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

您可以將這些項目新增至現有的主控台應用程式專案，或使用範本建立具有 WebJobs 功能的新主控台應用程式專案。 

以 WebJob 的方式自我部署專案，或將專案連結到 Web 專案，因此每當您要部署 Web 專案時，專案便會自動部署。 若要連結專案，Visual Studio 會在 Web 專案的 [webjobs-list.json](#webjobslist) 檔案中加上具有 WebJobs 功能的專案名稱。

![Diagram showing WebJob project linking to web project](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Prerequisites

如果您是使用 Visual Studio 2015，請安裝 [Azure SDK for .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/)。

如果您是使用 Visual Studio 2017，請安裝 [Azure 開發工作負載](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)。

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>啟用現有主控台應用程式專案的 WebJobs 部署

您有兩個選擇：

* [透過 Web 專案啟用自動部署](#convertlink)。

  設定現有主控台應用程式專案，以便在您部署 Web 專案時，它會以 WebJob 的方式自動部署。 當您要在與執行相關 Web 應用程式相同的 Web 應用程式中執行 WebJob 時，請使用此選項。

* [不透過 Web 專案啟用部署](#convertnolink)。

  設定現有主控台應用程式專案以 WebJob 的方式自我部署，且不具 Web 專案的連結。 當您要在 Web 應用程式中讓應用程式自行執行 WebJob，且 Web 應用程式中沒有正在執行的 Web 應用程式時，請使用此選項。 為了調整不受 Web 應用程式資源影響的 WebJob 資源，您可能會想執行此動作。

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> 透過 Web 專案啟用自動 WebJobs 部署

1. 以滑鼠右鍵按一下 [方案總管]**** 中的 Web 專案，然後依序按一下 [新增]**** > [Existing Project as Azure WebJob]****。
   
    ![Existing Project as Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [[Add Azure WebJob]](#configure) 對話方塊隨即出現。
2. 在 [專案名稱] **** 下拉式清單中，選取要新增為 WebJob 的主控台應用程式專案。
   
    ![Selecting project in Add Azure WebJob dialog](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. 完成 [[Add Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]****。 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> 不透過 Web 專案啟用 WebJobs 部署
1. 以滑鼠右鍵按一下 [方案總管]**** 中的主控台應用程式專案，然後按一下 [發行為 Azure WebJob]****。 
   
    ![發行為 Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [[加入 Azure WebJob]](#configure) 對話方塊隨即出現，而且 [專案名稱]**** 方塊中已選取此專案。
2. 完成 [[Add Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]****。
   
   此時會出現 [發行 Web]**** 精靈。  如果您不打算立即發行，請關閉精靈。 您所輸入的設定會被儲存下來，以供[部署專案](#deploy)時使用。

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>建立具有 WebJobs 功能的新專案
若要建立具有 WebJobs 功能的新專案，您可以使用主控台應用程式專案範本，並如 [上一節](#convert)中所述來啟用 WebJobs 部署。 另一種方式是，您可以使用 WebJobs 新專案範本：

* [將 Web 工作新專案樣本用於獨立的 Web 作業](#createnolink)
  
    建立專案，並將專案設定為以 WebJob 的方式自我部署，且不具 Web 專案的連結。 當您要在 Web 應用程式中讓應用程式自行執行 WebJob，且 Web 應用程式中沒有正在執行的 Web 應用程式時，請使用此選項。 為了調整不受 Web 應用程式資源影響的 WebJob 資源，您可能會想執行此動作。
* [連結到 Web 專案的 Web 工作使用 Web 工作新專案範本](#createlink)
  
    建立專案，並設定在針對位於相同解決方案中的 Web 專案進行部署時，會自動以 WebJob 的方式部署此專案。 當您要在與執行相關 Web 應用程式相同的 Web 應用程式中執行 WebJob 時，請使用此選項。

> [!NOTE]
> WebJobs 新專案範本自動安裝 NuGet 套件，並包括適用於 *WebJobs SDK* 的 [Program.cs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)程式碼。 如果您不想使用 WebJobs SDK，請移除或變更 *Program.cs* 中的 `host.RunAndBlock` 陳述式。
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> 在獨立的 WebJob 中使用 WebJobs 新專案範本
1. 按下 **「檔案** > **新專案**」,然後在 **「新項目**」對話框中按一**下 雲端** > **Azure Web 作業 (.NET 框架)。**
   
    ![顯示 WebJob 範本的 [新增專案] 對話方塊](./media/webjobs-dotnet-deploy-vs/np.png)
2. 請依照稍早所示的指示，[將主控台應用程式專案設定成獨立的 WebJobs 專案](#convertnolink)。

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> 在連結至 Web 專案的 WebJob 中使用 WebJobs 新專案範本
1. 以滑鼠右鍵按一下 [方案總管]**** 中的 Web 專案，然後依序按一下 [新增]**** > [New Azure WebJob Project]****。
   
    ![New Azure WebJob Project menu entry](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [[Add Azure WebJob]](#configure) 對話方塊隨即出現。
2. 完成 [[Add Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]****。

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>新增 Azure Web 工作對話框
[加入 Azure WebJob]**** 對話方塊可讓您輸入 WebJob 名稱和 WebJob 的執行模式設定。 

![[加入 Azure WebJob] 對話方塊](./media/webjobs-dotnet-deploy-vs/aaw2.png)

此對話方塊中的欄位會對應至 Azure 入口網站中 [新增 WebJob]**** 對話方塊上的欄位。 如需詳細資訊，請參閱[使用 WebJobs 執行背景工作](webjobs-create.md)。

> [!NOTE]
> * 如需命令列部署的詳細資訊，請參閱[啟用 Azure WebJobs 的命令列或連續傳遞](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)。
> * 如果部署 WebJob,然後決定要更改 WebJob 和重新部署的類型,則需要刪除*Webjob-發布設定.json*檔。 這樣會讓 Visual Studio 再次顯示發佈選項，您才能夠變更 WebJob 的類型。
> * 如果部署 WebJob，並在稍後將執行模式從連續變更為非連續 (或相反情形)，則在您重新部署時，Visual Studio 會在 Azure 中建立新的 WebJob。 如果您變更其他排程設定但保持執行模式不變，或在排程和隨選模式之間切換，則 Visual Studio 會更新現有的工作，而非建立新的工作。
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
設定 WebJobs 部署的主控台應用程式時，Visual Studio 會安裝 [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 封裝，並將排程資訊儲存在 WebJobs 專案中專案 *Properties* 資料夾的 *webjob-publish-settings.json* 檔案。 以下是該檔案的範例：

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

您可以編輯此檔案目錄，而 Visual Studio 會提供 IntelliSense。 檔案架構儲存在,[https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json)並可在那裡查看。  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
當您將具有 WebJobs 功能的專案連結到 Web 專案時，Visual Studio 會將 WebJobs 專案的名稱儲存在 Web 專案中 *Properties* 資料夾的 *webjobs-list.json* 檔案。 此清單可能包含多個 WebJobs 專案，如下列範例所示：

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

您可以編輯此檔案目錄，而 Visual Studio 會提供 IntelliSense。 檔案架構儲存在,[https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json)並可在那裡查看。

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>部署 WebJobs 專案
已連結到 Web 專案的 WebJobs 專案會透過 Web 專案自動部署。 有關 Web 專案部署的資訊,請參閱左側**導航中的「部署應用」 指南** > **Deploy app**。

若要自我部署 WebJobs 專案，請以滑鼠右鍵按一下 [方案總管]**** 中的專案，然後按一下 [發行為 Azure WebJob]****。 

![發行為 Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

若是獨立的 WebJob，則 Web 專案所使用的相同 [發行 Web] **** 精靈隨即出現，但其中幾個設定可以變更。

## <a name="scheduling-a-triggered-webjob"></a>排程觸發式 WebJob

WebJob 使用*設定.job*檔來確定 Web 作業何時運行。 使用此檔可為 WebJob 設置執行計畫。 以下範例每小時從上午9點到下午5點運行:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

此檔案必須在 WebJob 資料夾的根目錄,沿 WebJob 的`wwwroot\app_data\jobs\triggered\{job name}`文`wwwroot\app_data\jobs\continuous\{job name}`稿(如或 ) 當您從 Visual Studio 部署 WebJob 時，請將您的 `settings.job` 檔案屬性標示為 [有更新時才複製]****。 

從[Azure 門戶建立 Web 作業](webjobs-create.md)時,將為您建立設定.job 檔。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON 運算式

Web作業使用與 Azure 函數中的計時器觸發器相同的 CRON 運算式進行調度。 要瞭解有關 CRON 支援的更多詳細資訊,請參閱[計時器觸發器參考文章](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)。

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>設定.工作參考

Web 工作支援以下設定:

| **設定** | **型別**  | **說明** |
| ----------- | --------- | --------------- |
| `is_in_place` | 全部 | 允許作業在未首先複製到臨時資料夾的情況下就地運行。 要瞭解更多資訊,請參閱[Web 工作工作目錄](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)。 |
| `is_singleton` | 連續 | 僅當橫向擴展時,僅在單個實例上運行 Web 作業。要瞭解更多資訊,請參閱[將連續作業設定為單例](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)。 |
| `schedule` | 觸發 | 在基於 CRON 的計畫上運行 WebJob。 要瞭解更多資訊,請參閱[計時器觸發器參考文章](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)。 |
| `stopping_wait_time`| 全部 | 允許控制關閉行為。 要瞭解更多資訊,請參閱["正常關閉](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)"。 |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 WebJobs SDK](webjobs-sdk-how-to.md)
