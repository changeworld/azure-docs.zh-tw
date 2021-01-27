---
title: 快速入門 - 部署您的第一個 Azure Spring Cloud 應用程式
description: 在本快速入門中，我們會將 Spring Cloud 應用程式部署至 Azure Spring Cloud。
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/23/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 416f997475e35f8e784679ca0826f7af38d756c7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880299"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>快速入門：部署您的第一個 Azure Spring Cloud 應用程式

::: zone pivot="programming-language-csharp"
本快速入門說明如何部署可在 Azure 上執行的簡單 Azure Spring Cloud 微服務應用程式。

>[!NOTE]
> Steeltoe 對於 Azure Spring Cloud 的支援目前是以公開預覽版的形式來提供。 公開預覽版供應項目可讓客戶在其正式發行前，先試驗新功能。  公開預覽功能和服務不適用於生產環境。  如需預覽期間的支援詳細資訊，請參閱[常見問題集](https://azure.microsoft.com/support/faq/)或提出[支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)。

藉由遵循本快速入門，您將了解如何：

> [!div class="checklist"]
> * 產生基本的 Steeltoe .NET Core 專案
> * 佈建 Azure Spring Cloud 服務執行個體
> * 使用公用端點建置及部署應用程式
> * 即時串流記錄

本快速入門所使用的應用程式程式碼是使用 .NET Core Web API 專案範本所建置的簡單應用程式。 當您完成此範例時，應用程式將可在線上存取，並可透過 Azure 入口網站和 Azure CLI 來管理。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。 Azure Spring Cloud 服務支援 .NET Core 3.1 和更新版本。
* [Azure CLI 2.0.67 版或更新版本](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。
* [Git](https://git-scm.com/)。

## <a name="install-azure-cli-extension"></a>安裝 Azure CLI 擴充功能

確認您的 Azure CLI 版本為 2.0.67 或更新版本：

```azurecli
az --version
```

使用下列命令安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能：

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>登入 Azure

1. 登入 Azure CLI

    ```azurecli
    az login
    ```

1. 如果您有多個訂用帳戶，請選擇您想要用於本快速入門的訂用帳戶。

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="generate-a-steeltoe-net-core-project"></a>產生 Steeltoe .NET Core 專案

在 Visual Studio 中，使用 API 專案範本建立名為「hello-world」的 ASP.NET Core Web 應用程式。 請注意，將會有一個自動產生的 WeatherForecastController 成為我們稍後的測試端點。

1. 建立專案原始程式碼的資料夾，並產生專案。
 
   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```dotnetcli
   dotnet new webapi -n hello-world --framework netcoreapp3.1
   ```

1. 瀏覽至專案目錄。

   ```console
   cd hello-world
   ```

1. 編輯 appSettings.json 檔案以新增下列設定：

   ```json
   "spring": {
     "application": {
       "name": "hello-world"
     }
   },
   "eureka": {
     "client": {
       "shouldFetchRegistry": true,
       "shouldRegisterWithEureka": true
     }
   }
   ```

1. 此外，在 *appsettings.json* 中，將 `Microsoft` 類別的記錄層級從 `Warning` 變更為 `Information`。 這項變更可確保當您在稍後的步驟中檢視串流記錄時，將會產生記錄。

   *appsettings.json* 檔案現在看起來會類似下列範例：

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Information",
         "Microsoft": "Information",
         "Microsoft.Hosting.Lifetime": "Information"
       }
     },
     "AllowedHosts": "*",
     "spring": {
       "application": {
         "name": "hello-world"
       }
     },
     "eureka": {
       "client": {
         "shouldFetchRegistry": true,
         "shouldRegisterWithEureka": true
       }
     }
   }
   ```
   
1. 將相依性和 `Zip` 工作新增至 *.csproj* 檔案：

   ```xml
   <ItemGroup>
     <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
     <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
   </ItemGroup>
   <Target Name="Publish-Zip" AfterTargets="Publish">
       <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/deploy.zip" Overwrite="true" />
   </Target>
   ```

   這些套件適用於 Steeltoe Service Discovery 和 Azure Spring Cloud 用戶端程式庫。 `Zip` 工作可用來進行目的地為 Azure 的部署。 當您執行 `dotnet publish` 命令時，其會在 *publish* 資料夾中產生二進位檔，而且此工作會將 *publish* 資料夾發佈到您上傳至 Azure 的 *.zip* 檔案。

3. 在 *Program.cs* 檔案中，新增 `using` 指示詞以及會使用 Azure Spring Cloud 用戶端程式庫的程式碼：

   ```csharp
   using Microsoft.Azure.SpringCloud.Client;
   ```

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
               Host.CreateDefaultBuilder(args)
                   .UseAzureSpringCloudService()
                   .ConfigureWebHostDefaults(webBuilder =>
                   {
                       webBuilder.UseStartup<Startup>();
                   });
   ```

4. 在 *Startup.cs* 檔案中，新增 `using` 指示詞以及會在 `ConfigureServices` 和 `Configure` 方法的結尾使用 Steeltoe Service Discovery 的程式碼：

   ```csharp
   using Steeltoe.Discovery.Client;
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       // Template code not shown.

       services.AddDiscoveryClient(Configuration);
   }
   ```

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       // Template code not shown.

       app.UseDiscoveryClient();
   }
   ```

1. 建置專案以確定沒有任何編譯錯誤。

   ```dotnetcli
   dotnet build
   ```
 
## <a name="provision-a-service-instance"></a>佈建服務執行個體

下列程序會使用 Azure 入口網站來建立 Azure Spring Cloud 的執行個體。

1. 開啟 [Azure 入口網站](https://ms.portal.azure.com/)。 

1. 從頂端的搜尋方塊，搜尋 *Azure Spring Cloud*。

1. 從結果中選取 *Azure Spring Cloud*。

   ![ASC 圖示啟動](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

1. 在 [Azure Spring Cloud] 頁面上，選取 [+ 新增]。

   ![ASC 圖示新增](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

1. 填寫 Azure Spring Cloud [建立] 頁面上的表單。  請參考下列指引：

   * 訂用帳戶：選取您想要用來支付此資源費用的訂用帳戶。
   * **資源群組**：建立新的資源群組。 您在此處輸入的名稱將會用於後續步驟中作為 **\<resource group name\>** 。
   * **服務詳細資料/名稱**：指定 **\<service instance name\>** 。  名稱長度必須為 4 到 32 個字元，且只能包含小寫字母、數字及連字號。  服務名稱的第一個字元必須是字母，且最後一個字元必須是字母或數字。
   * **區域**：選取您服務執行個體的區域。

   ![ASC 入口網站啟動](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. 選取 [檢閱及建立]  。

## <a name="build-and-deploy-the-app"></a>建置並部署應用程式

下列程序會建置並部署您稍早建立的專案。

1. 確定命令提示字元仍在專案資料夾中。

1. 執行下列命令來建置專案、發佈二進位檔，並將二進位檔儲存在專案資料夾的 *.zip* 檔案內。

   ```dotnetcorecli
   dotnet publish -c release -o ./publish
   ```

1. 在您的 Azure Spring Cloud 執行個體中，建立已指派公用端點的應用程式。 使用您在 *appsettings.json* 中指定的相同應用程式名稱「hello-world」。

   ```console
   az spring-cloud app create -n hello-world -s <service instance name> -g <resource group name> --is-public --runtime-version NetCore_31
   ```

1. 將 *.zip* 檔案部署到應用程式。

   ```azurecli
   az spring-cloud app deploy -n hello-world -s <service instance name> -g <resource group name> --runtime-version NetCore_31 --main-entry hello-world.dll --artifact-path ./deploy.zip
   ```

   `--main-entry` 選項會識別包含應用程式進入點的 *.dll* 檔案。 在服務上傳 *.zip* 檔案之後，其會解壓縮所有檔案和資料夾，並嘗試執行 *.dll* 檔案中 `--main-entry` 所指定的進入點。

   系統需要幾分鐘的時間來完成部署應用程式。 若要確認其已部署完成，請移至 Azure 入口網站的 [應用程式] 刀鋒視窗。

## <a name="test-the-app"></a>測試應用程式

部署完成之後，請存取位於下列 URL 的應用程式：

```http
https://<service instance name>-hello-world.azuremicroservices.io/weatherforecast
```

應用程式會傳回類似下列範例的 JSON 資料：

```json
[{"date":"2020-09-08T21:01:50.0198835+00:00","temperatureC":14,"temperatureF":57,"summary":"Bracing"},{"date":"2020-09-09T21:01:50.0200697+00:00","temperatureC":-14,"temperatureF":7,"summary":"Bracing"},{"date":"2020-09-10T21:01:50.0200715+00:00","temperatureC":27,"temperatureF":80,"summary":"Freezing"},{"date":"2020-09-11T21:01:50.0200717+00:00","temperatureC":18,"temperatureF":64,"summary":"Chilly"},{"date":"2020-09-12T21:01:50.0200719+00:00","temperatureC":16,"temperatureF":60,"summary":"Chilly"}]
```

## <a name="stream-logs-in-real-time"></a>即時串流記錄

使用下列命令，從應用程式取得即時記錄。

```azurecli
az spring-cloud app logs -n hello-world -s <service instance name> -g <resource group name> --lines 100 -f
```

記錄會出現在輸出中：

```output
[Azure Spring Cloud] The following environment variables are loaded:
2020-09-08 20:58:42,432 INFO supervisord started with pid 1
2020-09-08 20:58:43,435 INFO spawned: 'event-gather_00' with pid 9
2020-09-08 20:58:43,436 INFO spawned: 'dotnet-app_00' with pid 10
2020-09-08 20:58:43 [Warning] No managed processes are running. Wait for 30 seconds...
2020-09-08 20:58:44,843 INFO success: event-gather_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2020-09-08 20:58:44,843 INFO success: dotnet-app_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
←[40m←[32minfo←[39m←[22m←[49m: Steeltoe.Discovery.Eureka.DiscoveryClient[0]
      Starting HeartBeat
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://[::]:1025
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /netcorepublish/6e4db42a-b160-4b83-a771-c91adec18c60
2020-09-08 21:00:13 [Information] [10] Start listening...
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://asc-svc-hello-world.azuremicroservices.io/weatherforecast
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "Get", controller = "WeatherForecast"}. Executing controller action with signature System.Collections.Generic.IEnumerable`1[hello_world.WeatherForecast] Get() on controller hello_world.Controllers.WeatherForecastController (hello-world).
info: Microsoft.AspNetCore.Mvc.Infrastructure.ObjectResultExecutor[1]
      Executing ObjectResult, writing value of type 'hello_world.WeatherForecast[]'.
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action hello_world.Controllers.WeatherForecastController.Get (hello-world) in 1.8902ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 4.2591ms 200 application/json; charset=utf-8
```

> [!TIP]
> 使用 `az spring-cloud app logs -h` 探索更多參數和記錄串流功能。

如需進階記錄分析功能，請從 [Azure 入口網站](https://portal.azure.com/)的功能表中，瀏覽至 [記錄] 索引標籤。 這裡的記錄會有幾分鐘的延遲。
[ ![記錄分析](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
本快速入門說明如何部署可在 Azure 上執行的簡單 Azure Spring Cloud 微服務應用程式。 

本教學課程使用的應用程式程式碼是以 Spring Initializr 建立的簡單應用程式。 當您完成此範例時，應用程式將可在線上存取，並可透過 Azure 入口網站來管理。

本快速入門說明如何：

> [!div class="checklist"]
> * 產生基本的 Spring Cloud 專案
> * 佈建服務執行個體
> * 使用公用端點建置及部署應用程式
> * 即時串流記錄

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門：

* [安裝 JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)
* (選擇性) [安裝 Azure CLI 版本 2.0.67 或更高版本](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)，並使用下列命令安裝 Azure Spring Cloud 擴充功能：`az extension add --name spring-cloud`
* (選擇性) [安裝 Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) 並[登入](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>產生 Spring Cloud 專案

從 [Spring Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client) 開始，使用 Azure Spring Cloud 的建議相依性來產生範例專案。 下圖顯示為此範例專案所設定的 Initializr。
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client
```

  ![Initializr 頁面](media/spring-cloud-quickstart-java/initializr-page.png)

1. 當所有相依性都已設定後，按一下 [產生]。 下載並解壓縮套件，然後藉由新增 `src/main/java/com/example/hellospring/HelloController.java` 來建立簡易 Web 應用程式的 Web 控制器，如下所示：

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>佈建 Azure Spring Cloud 的執行個體

下列程序會使用 Azure 入口網站來建立 Azure Spring Cloud 的執行個體。

1. 在新的索引標籤中，開啟 [Azure 入口網站](https://ms.portal.azure.com/)。 

2. 從頂端的搜尋方塊，搜尋 *Azure Spring Cloud*。

3. 從結果中選取 *Azure Spring Cloud*。

    ![ASC 圖示啟動](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. 在 Azure Spring Cloud 頁面上，按一下 [+ 新增]。

    ![ASC 圖示新增](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. 填寫 Azure Spring Cloud [建立] 頁面上的表單。  請參考下列指引：
    - 訂用帳戶：選取您想要用來支付此資源費用的訂用帳戶。
    - **資源群組**：為新資源建立新資源群組是最佳做法。 稍後將會使用該資訊作為 **\<resource group name\>** 。
    - **服務詳細資料/名稱**：指定 **\<service instance name\>** 。  名稱長度必須為 4 到 32 個字元，且只能包含小寫字母、數字及連字號。  服務名稱的第一個字元必須是字母，且最後一個字元必須是字母或數字。
    - **位置**：選取您服務執行個體的區域。

    ![ASC 入口網站啟動](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. 按一下 [檢閱及建立]。

## <a name="build-and-deploy-the-app"></a>建置並部署應用程式
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
下列程序會使用 Azure CLI 來建置和部署應用程式。 從專案根目錄中，執行下列命令。

1. 使用 Maven 建置專案：

    ```console
    mvn clean package -DskipTests
    ```

1. 安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能 (如果尚未安裝)：

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. 建立已指派公用端點的應用程式：

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. 部署應用程式的 Jar 檔案 (在 Windows 上為 `target\hellospring-0.0.1-SNAPSHOT.jar`)：

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path <jar file path>
    ```
    
1. 系統需要幾分鐘的時間來完成部署應用程式。 若要確認其已部署完成，請移至 Azure 入口網站的 [應用程式] 刀鋒視窗。 您應該會看到應用程式的狀態。

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

下列程序會使用適用於 Azure Spring Cloud 的 IntelliJ 外掛程式，在 IntelliJ IDEA 中部署範例應用程式。  

### <a name="import-project"></a>匯入專案

1. 開啟 IntelliJ [歡迎] 對話方塊，然後選取 [匯入專案] 以開啟匯入精靈。
1. 選取 `hellospring` 資料夾。

    ![匯入專案](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>部署應用程式
若要部署至 Azure，您必須使用您的 Azure 帳戶登入，然後選擇您的訂用帳戶。  如需登入詳細資訊，請參閱[安裝和登入](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)。

1. 在 IntelliJ 專案總管中以滑鼠右鍵按一下專案，然後選取 [Azure] ->  [部署至 Azure Spring Cloud]。

    [ ![部署至 Azure 1](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. 在 [名稱] 欄位中，接受應用程式的名稱。 [名稱] 是指設定，而不是應用程式名稱。 使用者通常不需要加以變更。
1. 在 [成品] 文字方塊中，選取 [hellospring-0.0.1-SNAPSHOT.jar]。
1. 在 [訂用帳戶] 文字方塊中，驗證您的訂用帳戶。
1. 在 [Spring Cloud] 文字方塊中，選取您在[佈建 Azure Spring Cloud 執行個體](./spring-cloud-quickstart-provision-service-instance.md)中所建立的 Azure Spring Cloud 執行個體。
1. 將 [公用端點] 設定為 [啟用]。
1. 在 [應用程式：] 文字方塊中，選取 [建立應用程式...]。
1. 輸入 hellospring，然後按一下 [確定]。

    [ ![部署至 Azure OK](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png) ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. 按一下 [部署 Azure Spring Cloud 應用程式] 對話方塊底部的 [執行] 按鈕來開始部署。 外掛程式會在 `hellospring` 應用程式上執行 `mvn package` 命令，並部署 `package` 命令所產生的 jar。
---

一旦部署完成後，您就可以在 `https://<service instance name>-hellospring.azuremicroservices.io/` 上存取應用程式。

  [ ![從瀏覽器存取應用程式](media/spring-cloud-quickstart-java/access-app-browser.png) ](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>即時串流記錄

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

使用下列命令，從應用程式取得即時記錄。

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
記錄會出現在結果中：

[ ![串流記錄](media/spring-cloud-quickstart-java/streaming-logs.png) ](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> 使用 `az spring-cloud app logs -h` 探索更多參數和記錄串流功能。

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. 選取 [Azure Explorer]，然後選取 [Spring Cloud]。
1. 以滑鼠右鍵按一下正在執行的應用程式。
1. 從下拉式清單中選取 [串流記錄]。
1. 選取執行個體。

    [ ![ 選取串流記錄](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. 串流記錄會顯示在輸出視窗中。

    [ ![串流記錄輸出](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

如需進階記錄分析功能，請從 [Azure 入口網站](https://portal.azure.com/)的功能表中，瀏覽至 [記錄] 索引標籤。 這裡的記錄會有幾分鐘的延遲。

[ ![記錄分析](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

## <a name="clean-up-resources"></a>清除資源

在前面的步驟中，您已建立 Azure 資源，這些資源如果留在訂用帳戶中，將會繼續產生費用。 如果您在未來不需要這些資源，請從入口網站刪除資源群組，或在 Azure CLI 中執行下列命令來加以刪除：

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

> [!div class="checklist"]
> * 產生基本的 Azure Spring Cloud 專案
> * 佈建服務執行個體
> * 使用公用端點建置及部署應用程式
> * 即時串流記錄

若要了解如何使用更多的 Azure Spring 功能，請前進至會將範例應用程式部署至 Azure Spring Cloud 的快速入門系列：

> [!div class="nextstepaction"]
> [建置和執行微服務](spring-cloud-quickstart-sample-app-introduction.md)

GitHub 上可用的其他範例：[Azure Spring Cloud 範例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)。