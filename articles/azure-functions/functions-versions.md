---
title: Azure Functions 執行階段版本概觀
description: Azure Functions 支援多個執行階段版本。 了解其間的差異以及如何選擇最適合您的版本。
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0989795d802b21e07ad9fea3bd417f0408df706c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83996715"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 執行階段版本概觀

Azure Functions 目前支援三種版本的執行時間主機：1.x、2.x 和3.x。 這三個版本都支援用於生產案例。  

> [!IMPORTANT]
> 1.x 版處於維護模式，而且只支援在 Azure 入口網站或在 Windows 電腦本機上進行開發。 只有在較新的版本中才會提供增強功能。 

本文會詳細說明各種版本之間的一些差異、如何建立每個版本，以及如何變更版本。

## <a name="languages"></a>語言

從2.x 版開始，執行時間會使用語言擴充性模型，而且函數應用程式中的所有函式都必須共用相同的語言。 在建立應用程式時，會選擇函式應用程式中的函式語言，並在函式背景[ \_ 工作 \_ 運行](functions-app-settings.md#functions_worker_runtime)時間設定中維護。 

下表指出每個執行階段版本目前支援的程式設計語言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

如需詳細資訊，請參閱[支援的語言](supported-languages.md)。

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>在特定版本上執行

根據預設，在 Azure 入口網站和 Azure CLI 中建立的函數應用程式會設定為版本3.x。 您可以視需要修改此版本。 您只能在建立函數應用程式之後，但在新增任何函式之前，將執行階段版本變更為1.x。  即使應用程式具有函式，也允許在2.x 和3.x 之間移動，但仍建議您先在新應用程式中進行測試。

## <a name="migrating-from-1x-to-later-versions"></a>從1.x 遷移至更新版本

您可以選擇遷移已撰寫的現有應用程式，以使用1.x 版執行時間，改為使用較新的版本。 您需要進行的大部分變更都與語言執行時間中的變更有關，例如 .NET Framework 4.7 和 .NET Core 之間的 c # API 變更。 您也必須確認您的程式碼和程式庫與所選語言執行階段相容。 最後，請務必記下下面所強調觸發程序、繫結及功能方面的所有變更。 若要獲得最佳的遷移結果，您應該在新版本中建立新的函式應用程式，並將您現有的1.x 版函數代碼移植到新的應用程式。  

雖然您可以手動更新應用程式設定來執行「就地」升級，但從1.x 到較高的版本會包含一些重大變更。 例如，在 c # 中，偵錯工具物件已從變更 `TraceWriter` 為 `ILogger` 。 藉由建立新的3.x 版專案，您可以開始使用以最新版本3.x 範本為基礎的更新功能。

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>版本1.x 之後的觸發程式和系結中的變更

從2.x 版開始，您必須針對應用程式中的函式所使用的特定觸發程式和系結，安裝擴充功能。 唯一的例外是 HTTP 和計時器觸發程序，這兩者不需要延伸模組。  如需詳細資訊，請參閱[註冊及安裝繫結延伸模組](./functions-bindings-register.md)。

在或版本之間，函式的*function.js上*也有一些變更。 例如，「事件中樞」的 `path` 屬性現在是 `eventHubName`。 如需每個繫結的文件連結，請參閱[現有的繫結表格](#bindings)。

### <a name="changes-in-features-and-functionality-after-version-1x"></a>版本1.x 之後功能的變更

在版本1.x 之後，已移除、更新或取代一些功能。 本節詳細說明使用1.x 版之後，您在更新版本中看到的變更。

2.x 版中做了下列變更：

* 用於呼叫 HTTP 端點的金鑰一律會以加密形式儲存在 Azure Blob 儲存體中。 在1.x 版中，金鑰預設會儲存在 Azure 檔案儲存體中。 將應用程式從 1.x 版升級至 2.x 版時，會重設檔案儲存體中的現有秘密。

* 2.x 版執行階段並未內建對 Webhook 提供者的支援。 進行此變更是為了提升效能。 您仍然可以使用 HTTP 觸發程序作為 Webhook 的端點。

* 主機設定檔 (host.json) 應該空白或含有 `"version": "2.0"` 字串。

* 為了改善監視功能，入口網站中使用設定的 [Webjob] 儀表板 [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) 會取代為使用設定的 Azure 應用程式 Insights [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) 。 如需詳細資訊，請參閱[監視 Azure Functions](functions-monitoring.md)。

* 函數應用程式中的所有函式都必須共用相同語言。 當您建立函數應用程式時，必須為應用程式選擇執行階段堆疊。 執行時間堆疊是由 [ [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) 應用程式設定] 中的值所指定。 新增此需求是為了改善使用量和啟動時間。 在本機進行開發時，您必須在 [local.settings.json 檔案](functions-run-local.md#local-settings-file)中也包含此設定。

* App Service 方案中函式的預設逾時已變更為 30 分鐘。 您可以藉由在 host.json 中使用 [functionTimeout](functions-host-json.md#functiontimeout) 設定，將逾時手動變更回無限制。

* 預設會針對取用方案函式執行 HTTP 並行節流，預設值為每個實例100個並行要求。 您可以在 host.json 檔案的設定中變更此值 [`maxConcurrentRequests`](functions-host-json.md#http) 。

* 由於[.Net Core 的限制](https://github.com/Azure/azure-functions-host/issues/3414)，已移除對 F # 腳本（. run.fsx）函數的支援。 仍然支援已編譯的 F# 函式 (.fs)。

* 「事件方格」觸發程序 Webhook 的 URL 格式已變更為 `https://{app}/runtime/webhooks/{triggerName}`。

## <a name="migrating-from-2x-to-3x"></a>從2.x 遷移至3。x

Azure Functions 版本3.x 與2.x 版具有高度回溯相容性。  許多應用程式應該能夠安全地升級至3.x，而不需要變更任何程式碼。  雖然建議改用3.x，但請務必先執行廣泛的測試，然後再變更生產應用程式中的主要版本。

### <a name="breaking-changes-between-2x-and-3x"></a>2.x 和3.x 之間的突破性變更

以下是將2.x 應用程式升級至3.x 之前，要注意的變更。

#### <a name="javascript"></a>JavaScript

* 透過或傳回值指派的輸出系結 `context.done` 現在的行為與中的設定相同 `context.bindings` 。

* 計時器觸發程式物件是 camelCase，而不是 PascalCase

* 事件中樞使用二進位觸發的函 `dataType` 式會接收的陣列， `binary` 而不是 `string` 。

* 無法再透過存取 HTTP 要求承載 `context.bindingData.req` 。  它仍然可以做為輸入參數、 `context.req` 和中的來存取 `context.bindings` 。

* Node.js 8 已不再受支援，而且不會在3.x 函式中執行。

#### <a name="net"></a>.NET

* [同步伺服器作業預設為停用](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers)。

### <a name="changing-version-of-apps-in-azure"></a>在 Azure 中變更應用程式版本

Azure 中已發佈應用程式所使用的函式執行時間版本，是由 [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) 應用程式設定所決定。 支援下列主要執行階段版本值：

| 值 | 執行時間目標 |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> 請勿任意變更此設定，因為可能需要其他應用程式設定變更和對您的函數代碼所做的變更。

### <a name="locally-developed-application-versions"></a>本機開發的應用程式版本

您可以對函數應用程式進行下列更新，以在本機變更目標版本。

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 執行階段版本

在 Visual Studio 中，您會在建立專案時選取執行階段版本。 適用于 Visual Studio 的 Azure Functions 工具支援三個主要的執行階段版本。 根據專案設定進行偵錯和發佈時，會使用正確的版本。 版本設定會在 `.csproj` 檔案中的下列屬性中定義：

##### <a name="version-1x"></a>1\.x 版

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>2.x 版

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>3.x 版

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3.x 和 .NET 要求 `Microsoft.NET.Sdk.Functions` 副檔名至少為 `3.0.0` 。

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>將2.x 應用程式更新為 Visual Studio 中的3。x

您可以開啟目標為2.x 的現有函式，然後藉由編輯檔案 `.csproj` 並更新上述值，移至3.x。  Visual Studio 會根據專案中繼資料自動為您管理執行階段版本。  不過，如果您從未建立過3.x 應用 Visual Studio 程式，但您的電腦上還沒有適用于3.x 的範本和執行時間，這是可行的。  這可能會出現錯誤，例如「沒有可用的函式執行時間符合專案中所指定的版本」。  若要提取最新的範本和執行時間，請完成建立新函數專案的體驗。  當您進入 [版本] 和 [範本] 選取畫面時，請等候 Visual Studio 完成提取最新的範本。  當最新的 .NET Core 3 範本可供使用並顯示時，您應該能夠執行並對版本3.x 所設定的任何專案進行偵錯工具。

> [!IMPORTANT]
> 只有在使用 Visual Studio 16.4 版或更新版本時，才能在 Visual Studio 中開發版本3.x 函數。

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 與 Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) 除了用於命令列開發之外，也會供適用於 Visual Studio Code 的 [Azure Functions 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)使用。 若要針對1.x 版進行開發，請安裝2.x 版的 Core Tools。 2.x 版的開發需要2.x 版的 Core Tools，依此類推。 如需詳細資訊，請參閱[安裝 Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)。

針對 Visual Studio Code 開發，您可能必須一併更新 `azureFunctions.projectRuntime` 的使用者設定，以符合所安裝工具的版本。  此設定也會更新函數應用程式建立期間所使用的範本和語言。  若要在中建立應用程式 `~3` ，請將 `azureFunctions.projectRuntime` 使用者設定更新為 `~3` 。

![Azure Functions 擴充功能執行時間設定](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven 和 JAVA 應用程式

您可以安裝在本機執行所需的 2.x[版核心工具](functions-run-local.md#install-the-azure-functions-core-tools)，將 JAVA 應用程式從版本2.x 遷移至3.x。  在確認您的應用程式正確地在版本3.x 的本機上執行之後，請更新應用程式的檔案，將 `POM.xml` `FUNCTIONS_EXTENSION_VERSION` 設定修改為 `~3` ，如下列範例所示：

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>繫結

從2.x 版開始，執行時間會使用新的系結擴充性[模型](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)，以提供下列優點：

* 支援第三方繫結延伸模組。

* 分開處理執行階段和繫結。 此變更可讓繫結延伸模組個別建立版本和發行。 舉例來說，您可以選擇升級至依賴較新版基礎 SDK 的延伸模組版本。

* 較輕便的執行環境，執行階段只會知道及載入使用中的繫結。

除了 HTTP 和計時器觸發程序之後，所有繫結都必須以明確方式新增至函數應用程式專案，或在入口網站中註冊。 如需詳細資訊，請參閱[註冊繫結延伸模組](./functions-bindings-expressions-patterns.md)。

下表顯示每個執行階段版本所支援的繫結。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [撰寫 Azure Functions 並在本機進行測試](functions-run-local.md)
* [如何設定 Azure Functions 執行階段目標版本](set-runtime-version.md)
* [版本資訊](https://github.com/Azure/azure-functions-host/releases)
