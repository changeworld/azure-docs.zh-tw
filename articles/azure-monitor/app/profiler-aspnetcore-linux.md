---
title: 使用 Application Insights Profiler 來分析 ASP.NET Core Azure Linux Web 應用程式 | Microsoft Docs
description: 關於如何使用 Application Insights Profiler 的概念敍述及逐步教學課程。
ms.topic: conceptual
ms.custom: devx-track-csharp
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 652d63cbf71f7a998462780a5057ce8269463da4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930378"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>使用 Application Insights Profiler 來分析 ASP.NET Core Azure Linux Web 應用程式

此功能目前為預覽狀態。

了解在使用 [Application Insights ](./app-insights-overview.md) 時，即時 Web 應用程式的每個方法各使用了多少時間。 Application Insights Profiler 現在可在 Azure App Service 上供裝載於 Linux 中的 ASP.NET Core Web 應用程式使用。 本指南提供逐步指示，說明如何針對 ASP.NET Core Linux Web 應用程式收集分析工具追蹤。

完成此逐步解說之後，您的應用程式可以收集分析工具追蹤，例如圖中所示的追蹤。 在此範例中，分析工具追蹤會指出特定的 Web 要求變慢是因為將時間花在等候上。 程式碼中使應用程式變慢的「最忙碌路徑」** 之前會加上火焰圖示。 **HomeController** 區段中的 **About** 方法使 Web 應用程式速度變慢，因為此方法呼叫 **Thread.Sleep** 函式。

![分析工具追蹤](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>必要條件
下列指示適用於所有 Windows、Linux 和 Mac 開發環境：

* 安裝 [.NET Core SDK 2.1.2 或更新版本](https://dotnet.microsoft.com/download/archives)。
* 遵循[使用者入門 - 安裝 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 中的指示來安裝 Git。

## <a name="set-up-the-project-locally"></a>在本機設定專案

1. 在您的機器上開啟命令提示字元視窗。 下列指示適用於所有 Windows、Linux 和 Mac 開發環境。

1. 建立 ASP.NET Core MVC Web 應用程式：

   ```console
   dotnet new mvc -n LinuxProfilerTest
   ```

1. 將工作目錄變更為專案的根資料夾。

1. 新增 NuGet 套件來收集分析工具追蹤：

   ```console
   dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
   ```

1. 在 Program.cs 中啟用 Application Insights：

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```

1. 在 Startup.cs 中啟用 Profiler：

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. 在 **HomeController.cs** 區段中加入一行程式碼，以隨機延遲幾秒鐘的時間：

    ```csharp
    using System.Threading;
    ...

    public IActionResult About()
        {
            Random r = new Random();
            int delay = r.Next(5000, 10000);
            Thread.Sleep(delay);
            return View();
        }
    ```

1. 儲存並認可您對本機存放庫的變更：

    ```console
    git init
    git add .
    git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>建立 Linux Web 應用程式來裝載專案

1. 在 Linux 上使用 App Service 建立 Web 應用程式環境：

    ![建立 Linux Web 應用程式](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. 建立部署認證：

    > [!NOTE]
    > 記下您的密碼，供稍後部署 Web 應用程式時使用。

    ![建立部署認證](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. 選擇部署選項。 請遵循 Azure 入口網站上的指示，在 Web 應用程式中設定本機 Git 存放庫。 系統將會自動建立 Git 存放庫。

    ![設定 Git 存放庫](./media/profiler-aspnetcore-linux/setup-git-repo.png)

如需部署選項的詳細資訊，請參閱 [App Service 檔](https://docs.microsoft.com/azure/app-service)。

## <a name="deploy-your-project"></a>部署您的專案

1. 在命令提示字元視窗中，瀏覽至專案的根資料夾。 新增 Git 遠端存放庫以指向 App Service 上的存放庫：

    ```console
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * 使用您之前建立部署認證時所使用的**使用者名稱**。
    * 使用您之前在 Linux 上使用 App Service 建立 Web 應用程式時所使用的**應用程式名稱**。

2. 將變更推送至 Azure 來部署專案：

    ```console
    git push azure master
    ```

    您應該會看到類似下列範例的結果：

    ```output
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    ...
    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>新增 Application Insights 以監視您的 Web 應用程式

1. [建立 Application Insights 資源](./create-new-resource.md)。

2. 複製 Application Insights 資源的 **iKey** 值，並在您的 Web 應用程式中進行下列設定：

    `APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]`

    應用程式的設定變更時，網站會自動重新啟動。 一旦套用新的設定後，分析工具會立即執行 2 分鐘。 之後，分析工具會每小時執行兩分鐘。

3. 產生一些流向網站的流量。 您可以重新整理網站的 [關於]**** 網頁幾次以產生流量。

4. 等待 2-5 分鐘，讓事件彙總至 Application Insights。

5. 在 Azure 入口網站中瀏覽至 Application Insights [效能]**** 窗格。 您可以檢視在窗格右下角的分析工具追蹤。

    ![檢視分析工具追蹤](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>後續步驟
如果您使用 Azure App Service 所裝載的自訂容器，請依照＜[為容器化的 ASP.NET Core 應用程式啟用服務分析工具](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp)＞中的指示啟用 Application Insights Profiler。

如果您有任何問題或建議，請在 Application Insights 的 Github 存放庫中提出：[ApplicationInsights-Profiler-AspNetCore：問題](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues)。

