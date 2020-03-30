---
title: Azure Functions 執行階段版本概觀
description: Azure Functions 支援多個執行階段版本。 了解其間的差異以及如何選擇最適合您的版本。
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 21a7b25087efd5d4adf2154c935636c263df9afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276552"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 執行階段版本概觀

Azure 函數運行時的主要版本與運行時所基於的 .NET 版本相關。 下表指示運行時的當前版本、發佈級別和相關 .NET 版本。 

| 執行階段版本 | 發佈級別<sup>1</sup> | .NET 版本 | 
| --------------- | ------------- | ------------ |
| 3.x | GA | .NET 核心 3.1 | 
| 2.x | GA | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET 框架 4.6<sup>3</sup> |

生產方案支援<sup>1</sup>個 GA 版本。   
<sup>2</sup>版本 1.x 處於維護模式。 增強功能僅在更高版本中提供。   
<sup>3</sup>僅支援 Azure 門戶中的開發或在 Windows 電腦上本地進行開發。

本文詳細介紹了各種版本、如何創建每個版本以及如何更改版本之間的一些差異。

## <a name="languages"></a>Languages

從版本 2.x 開始，運行時使用語言擴展模型，函數應用中的所有函數必須共用相同的語言。 創建應用時選擇函數應用中的函數語言，並在["功能\_\_工作執行時間](functions-app-settings.md#functions_worker_runtime)"設置中維護。 

Azure 函數 1.x 實驗語言無法使用新模型，因此 2.x 不支援它們。 下表指出每個執行階段版本目前支援的程式設計語言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

有關詳細資訊，請參閱[支援的語言](supported-languages.md)。

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>在特定版本上運行

預設情況下，在 Azure 門戶和 Azure CLI 中創建的函數應用設置為版本 3.x。 您可以根據需要修改此版本。 只能在創建函數應用後但在添加任何函數之前將執行階段版本更改為 1.x。  即使在具有功能的應用時，允許在 2.x 和 3.x 之間移動，但仍建議先在新應用中進行測試。

## <a name="migrating-from-1x-to-later-versions"></a>從 1.x 遷移到更高版本

您可以選擇遷移編寫的現有應用，以使用版本 1.x 運行時，而不是使用較新版本。 您需要進行的大多數更改都與語言運行時的更改相關，例如 .NET 框架 4.7 和 .NET Core 之間的 C# API 更改。 您也必須確認您的程式碼和程式庫與所選語言執行階段相容。 最後，請務必記下下面所強調觸發程序、繫結及功能方面的所有變更。 為了獲得最佳遷移效果，應在新版本中創建新的函數應用，並將現有版本 1.x 函數代碼移植到新應用。  

雖然可以通過手動更新應用配置進行"就地"升級，但從 1.x 到更高版本包括一些重大更改。 例如，在 C# 中，調試物件從`TraceWriter`更改為`ILogger`。 通過創建新版本 3.x 專案，您可以開始基於最新版本 3.x 範本的更新函數。

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>版本 1.x 後觸發器和綁定中的更改

從版本 2.x 開始，必須安裝應用中函數使用的特定觸發器和綁定的擴展。 唯一的例外是 HTTP 和計時器觸發程序，這兩者不需要延伸模組。  如需詳細資訊，請參閱[註冊及安裝繫結延伸模組](./functions-bindings-register.md)。

*函數.json*或版本之間的函數屬性也有幾個更改。 例如，「事件中樞」的 `path` 屬性現在是 `eventHubName`。 如需每個繫結的文件連結，請參閱[現有的繫結表格](#bindings)。

### <a name="changes-in-features-and-functionality-after-version-1x"></a>版本 1.x 後特性和功能的更改

在版本 1.x 之後，刪除了、更新或替換了一些功能。 本節詳細介紹了在使用版本 1.x 後在更高版本中看到的更改。

2.x 版中做了下列變更：

* 用於呼叫 HTTP 端點的金鑰一律會以加密形式儲存在 Azure Blob 儲存體中。 在版本 1.x 中，預設情況下金鑰存儲在 Azure 檔存儲中。 將應用程式從 1.x 版升級至 2.x 版時，會重設檔案儲存體中的現有秘密。

* 2.x 版執行階段並未內建對 Webhook 提供者的支援。 進行此變更是為了提升效能。 您仍然可以使用 HTTP 觸發程序作為 Webhook 的端點。

* 主機設定檔 (host.json) 應該空白或含有 `"version": "2.0"` 字串。

* 為了改進監視，門戶中的 Web 作業儀表板（使用該[`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard)設置）替換為使用該設置的[`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey)Azure 應用程式見解。 如需詳細資訊，請參閱[監視 Azure Functions](functions-monitoring.md)。

* 函數應用程式中的所有函式都必須共用相同語言。 當您建立函數應用程式時，必須為應用程式選擇執行階段堆疊。 運行時堆疊由應用程式設定中[`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime)的值指定。 新增此需求是為了改善使用量和啟動時間。 在本機進行開發時，您必須在 [local.settings.json 檔案](functions-run-local.md#local-settings-file)中也包含此設定。

* App Service 方案中函式的預設逾時已變更為 30 分鐘。 您可以藉由在 host.json 中使用 [functionTimeout](functions-host-json.md#functiontimeout) 設定，將逾時手動變更回無限制。

* 預設情況下，使用計畫函數的 HTTP 併發限制實現，每個實例預設為 100 個併發請求。 您可以在 host.json[`maxConcurrentRequests`](functions-host-json.md#http)檔中的設置中更改此設置。

* 由於[.NET Core 的限制](https://github.com/Azure/azure-functions-host/issues/3414)，已刪除對 F# 腳本 （.fsx） 函數的支援。 仍然支援已編譯的 F# 函式 (.fs)。

* 「事件方格」觸發程序 Webhook 的 URL 格式已變更為 `https://{app}/runtime/webhooks/{triggerName}`。

## <a name="migrating-from-2x-to-3x"></a>從 2.x 遷移到 3.x

Azure 函數版本 3.x 與版本 2.x 高度向後相容。  許多應用應該能夠安全地升級到 3.x，而無需進行任何代碼更改。  在鼓勵遷移到 3.x 時，請確保在更改生產應用中的主要版本之前運行大量測試。

### <a name="breaking-changes-between-2x-and-3x"></a>在 2.x 和 3.x 之間分解更改

以下是在將 2.x 應用升級到 3.x 之前需要注意的更改。

#### <a name="javascript"></a>JavaScript

* 通過`context.done`或傳回值分配的輸出綁定現在與 在 中`context.bindings`設置相同。

* 計時器觸發器物件是駱駝事件，而不是 PascalCase

* 事件中心觸發的函數`dataType`與二進位將收到一`binary`個數組，`string`而不是 。

* 無法再通過`context.bindingData.req`訪問 HTTP 要求負載。  它仍然可以作為輸入參數訪問，`context.req`並在 中`context.bindings`訪問 。

* Node.js 8 不再受支援，不會在 3.x 函數中執行。

#### <a name="net"></a>.NET

* [預設情況下，同步伺服器操作被禁用](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers)。

### <a name="changing-version-of-apps-in-azure"></a>在 Azure 中變更應用程式版本

Azure 中已發佈應用使用的函數執行階段版本由[`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version)應用程式設定決定。 支援以下主要執行階段版本值：

| 值 | 運行時目標 |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> 不要任意更改此設置，因為可能需要更改其他應用設置並更改函數代碼。

### <a name="locally-developed-application-versions"></a>本地開發的應用程式版本

您可以進行以下更新，以使應用在本地更改目標版本。

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 執行階段版本

在 Visual Studio 中，您會在建立專案時選取執行階段版本。 Visual Studio 的 Azure 函數工具支援三個主要執行階段版本。 根據專案設定進行偵錯和發佈時，會使用正確的版本。 版本設定會在 `.csproj` 檔案中的下列屬性中定義：

##### <a name="version-1x"></a>1.x 版

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
> Azure 函數 3.x 和`Microsoft.NET.Sdk.Functions`.NET 要求`3.0.0`擴展至少為 。

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>將 2.x 應用程式更新為 Visual Studio 中的 3.x

您可以通過編輯`.csproj`檔並更新上述值來打開面向 2.x 的現有函數並移動到 3.x。  Visual Studio 會根據專案中繼資料自動為您管理執行階段版本。  但是，如果您從未在 Visual Studio 尚未在電腦上具有 3.x 的範本和運行時之前從未創建過 3.x 應用，則有可能。  這可能給自己帶來一個錯誤，如"沒有函數運行時可用，與專案中指定的版本匹配"。  要獲取最新的範本和運行時，請體驗以創建一個新的函數專案。  當您進入版本和範本選擇螢幕時，等待 Visual Studio 完成獲取最新範本。  一旦最新的 .NET Core 3 範本可用並顯示，您應該能夠運行和調試為版本 3.x 配置的任何專案。

> [!IMPORTANT]
> 只有在使用 Visual Studio 版本 16.4 或更高版本時，才能在 Visual Studio 中開發版本 3.x 功能。

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 與 Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) 除了用於命令列開發之外，也會供適用於 Visual Studio Code 的 [Azure Functions 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)使用。 要針對版本 3.x 進行開發，請安裝核心工具的版本 3.x。 版本 2.x 開發需要核心工具的版本 2.x，等等。 如需詳細資訊，請參閱[安裝 Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)。

針對 Visual Studio Code 開發，您可能必須一併更新 `azureFunctions.projectRuntime` 的使用者設定，以符合所安裝工具的版本。  此設定也會更新函數應用程式建立期間所使用的範本和語言。  要在 中`~3`創建應用，請`azureFunctions.projectRuntime`將使用者設置`~3`更新為 。

![Azure 函數擴展運行時設置](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven 和 JAVA 應用程式

您可以通過安裝本地運行所需的[3.x 核心工具的 3.x 版本](functions-run-local.md#install-the-azure-functions-core-tools)，將 JAVA 應用從版本 2.x 遷移到 3.x。  驗證應用在版本 3.x 上本地運行是否正確後，將應用`POM.xml`的檔更新以將`FUNCTIONS_EXTENSION_VERSION`設置修改為`~3`，如以下示例所示：

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

從版本 2.x 開始，運行時使用新的[綁定擴充性模型](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)，該模型具有以下優點：

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
