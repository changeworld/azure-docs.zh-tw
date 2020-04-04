---
title: Azure Functions 的應用程式設定參考
description: Azure Functions 應用程式設定或環境變數的參考文件。
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: e2d168d8828d17e13f875e3b2555c7db0d4ba32d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656792"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions 的應用程式設定參考

函式應用程式中應用程式設定所包含的全域設定選項會影響該函式應用程式的所有函式。 當您在本機執行時，這些設定會當作本機[環境變數](functions-run-local.md#local-settings-file)來使用。 本文列出函式應用程式中可用的應用程式設定。

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

在 [host.json](functions-host-json.md) 檔案和 [local.settings.json](functions-run-local.md#local-settings-file) 檔案中，還有其他全域設定選項。

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

應用程式見解的檢測密鑰。 僅使用`APPINSIGHTS_INSTRUMENTATIONKEY``APPLICATIONINSIGHTS_CONNECTIONSTRING`或的一個。 如需詳細資訊，請參閱[監視 Azure Functions](functions-monitoring.md)。 

|Key|範例值|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

應用程式見解的連接字串。 使用`APPLICATIONINSIGHTS_CONNECTIONSTRING``APPINSIGHTS_INSTRUMENTATIONKEY`而不是當函數應用需要使用連接字串支援添加的自定義項時。 有關詳細資訊,請參閱[連接字串](../azure-monitor/app/sdk-connection-string.md)。 

|Key|範例值|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|檢測鍵[鍵];引入終結點[url];實時終結點\url;探查器終結點\url};快照終結點\url;;|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

在函數運行時的版本 2.x 和更高版本中,根據運行時環境配置應用行為。 這個值在[初始化期間讀取](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43)。 您可以`AZURE_FUNCTIONS_ENVIRONMENT`設定為任何值,但支援[三個值](/dotnet/api/microsoft.aspnetcore.hosting.environmentname):[開發](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development),[暫存](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)與生產 。 [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production) 未`AZURE_FUNCTIONS_ENVIRONMENT`設置時,它預設在`Development`本地環境和`Production`Azure 上。 應使用此設置而不是`ASPNETCORE_ENVIRONMENT`設置運行時環境。 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

選擇性儲存體帳戶連接字串，用於儲存記錄並將它們顯示在入口網站的 [監視器]**** 索引標籤中。 此設定僅適用於以 Azure 函數運行時版本 1.x 為目標的應用。 儲存體帳戶必須是一般用途的帳戶，支援 Blob、佇列和資料表。 要瞭解更多資訊,請參閱[儲存帳戶要求](storage-considerations.md#storage-account-requirements)。

|Key|範例值|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> 為了更好的性能和體驗,執行時版本 2.x 和更高版本使用APPINSIGHTS_INSTRUMENTATIONKEY和應用見解進行監視`AzureWebJobsDashboard`,而不是 。

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` 表示停用針對函式應用程式根 URL 所顯示的預設登陸頁面。 預設值為 `false`。

|Key|範例值|
|---|------------|
|AzureWebJobsDisableHomepage|true|

省略這個應用程式設定或將其設為 `false` 時，會顯示與下列範例類似的頁面，以回應 URL `<functionappname>.azurewebsites.net`。

![函式應用程式登陸頁面](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` 表示在編譯 .NET 程式碼時使用「釋放」模式；`false` 表示使用「偵錯」模式。 預設值為 `true`。

|Key|範例值|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

要啟用的搶鮮版 (Beta) 功能清單 (以逗號分隔)。 這些旗標所啟用的搶鮮版 (Beta) 功能還不適合在生產環境內使用，但在上線之前可以針對實驗使用予以啟用。

|Key|範例值|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

指定要用於金鑰儲存的存放庫或提供者。 目前支援的存放庫是 Blob 儲存體 ("Blob") 和本機檔案系統 ("Files")。 預設值在第 2 版中為 Blob，在第 1 版中則為檔案系統。

|Key|範例值|
|---|------------|
|AzureWebJobsSecretStorageType|檔案|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions 執行階段會將此儲存體帳戶連接字串用於所有函式 (由 HTTP 觸發的函式除外)。 儲存體帳戶必須是一般用途的帳戶，支援 Blob、佇列和資料表。 請參閱[儲存體帳戶](functions-infrastructure-as-code.md#storage-account)和[儲存體帳戶需求](storage-considerations.md#storage-account-requirements)。

|Key|範例值|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

用於 TypeScript 之編譯器的路徑。 可讓您覆寫預設值 (需要的話)。

|Key|範例值|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

指定是否啟用 Azure 門戶中的編輯。 有效值為 "readwrite" 和 "readonly"。

|Key|範例值|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

要在此函式應用程式中使用的 Functions 執行階段版本。 含主要版本的波狀符號表示使用該主要版本的最新版本 (例如，"~2")。 有相同主要版本的新版本可用時，會將它們自動安裝在函式應用程式中。 若要將應用程式釘選至特定版本，請使用完整版本號碼 (例如，"2.0.12345")。 預設值為 "~2"。 `~1` 的值會將您的應用程式釘選至 1.x 版執行階段。

|Key|範例值|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>功能\_\_V2\_相容模式

此設定使函數應用能夠在版本 3.x 運行時的 2.x 相容模式下運行。 僅當在[將函數應用從運行時的版本 2.x 升級到 3.x](functions-versions.md#migrating-from-2x-to-3x)時遇到問題時,才使用此設置。 

>[!IMPORTANT]
> 當您更新應用以在版本 3.x 上正確運行時,此設置僅用於短期解決方法。 只要[支援 2.x 運行時,就支援](functions-versions.md)此設置。 如果您在不使用此設定的情況下遇到阻止應用程式在版本 3.x 上執行的問題,請[回報您的問題](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)。

要求新增[裝置\_的\_裝置設定](functions-app-settings.md#functions_extension_version)為`~3`。

|Key|範例值|
|---|------------|
|功能\_\_V2\_相容模式|true|

## <a name="functions_worker_process_count"></a>函數\_\_工作人員\_流程 計數

指定語言的建立程序的最大數量,預設值`1`為 。 允許的最大值為`10`。 函數調用在語言輔助進程之間均勻分佈。 語言輔助行程每 10 秒產生一次,直到\_達到\_「\_功能 工作者 」計算計數設置的計數。 使用多種語言輔助進程與[縮放](functions-scale.md)不同。 當您的工作負載混合使用 CPU 綁定和 I/O 綁定調用時,請考慮使用此設置。 此設定適用於所有non-.NET語言。

|Key|範例值|
|---|------------|
|函數\_\_工作人員\_流程 計數|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

要在函式應用程式中載入的語言背景工作角色執行階段。  這會對應至您應用程式 (例如，"dotnet") 中所使用的語言。 對於使用多種語言的函式，您必須將其發佈到多個應用程式，每個都有對應的背景工作角色執行階段值。  有效值包括`dotnet``node``java``powershell`(C#/F#)、(JavaScript/TypeScript)、(Java)、(PowerShell)`python`和 (Python)。

|Key|範例值|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

僅用於消費&高級計劃。 函式應用程式碼和設定儲存所在之儲存體帳戶的連接字串。 請參閱[建立函式應用程式](functions-infrastructure-as-code.md#create-a-function-app)。

|Key|範例值|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

僅用於消費&高級計劃。 函式應用程式碼和設定的檔案路徑。 Used with WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. 預設值是開頭為函式應用程式名稱的唯一字串。 請參閱[建立函式應用程式](functions-infrastructure-as-code.md#create-a-function-app)。

|Key|範例值|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

函式應用程式可相應放大的執行個體數目上限。 預設值是無限制。

> [!NOTE]
> 這項設定是預覽功能 - 僅在值設為 <= 5 時才可靠

|Key|範例值|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_僅限視窗。_  
設置在 Windows 上運行函數應用時要使用的 Node.js 版本。 應使用波浪線 (*) 讓運行時使用目標主要版本的最新版本。 例如,當設置為`~10`時,將使用最新版本的 Node.js 10。 當主要版本以波浪線為目標時,您不必手動更新次要版本。 

|Key|範例值|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|Φ10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

可讓函式應用程式從掛接的套件檔案執行。

|Key|範例值|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

有效值為 URL (可解析為部署套件檔案的位置) 或 `1`。 設定為 `1` 時，套件必須位於 `d:\home\data\SitePackages` 資料夾。 搭配使用 ZIP 部署與這項設定時，系統會將套件自動上傳到這個位置。 在預覽中，這項設定命名為 `WEBSITE_RUN_FROM_ZIP`。 如需詳細資訊，請參閱[從套件檔案執行函式](run-functions-from-deployment-package.md)。

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

根據預設，Functions Proxy 將利用捷徑來將 API 呼叫從 Proxy 直接傳送到同一個函數應用程式中的函式，而不是建立新的 HTTP 要求。 此設定可讓您停用該行為。

|Key|值|描述|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|有指向本地函數應用中的函數的後端網址的呼叫將不再直接傳送到此函數,而是將定向回函數應用的 HTTP 前端介面|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|這是預設值。 有指向本地函數應用中的函數的後端網址的呼叫將直接轉寄到該函式|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

此設定控制在將路由參數插入到後端 URL 時，是否要將其中的 %2F 解碼為斜線。 

|Key|值|描述|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|若路由參數含有已編碼的斜線，就必須將它們解碼。 `example.com/api%2ftest` 將成為 `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|此為預設行為。 所有路由參數都將依原樣傳遞|

### <a name="example"></a>範例

以下是在 URL myfunction.com 上函數應用程式中的範例 proxies.json

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|URL 解碼|輸入|輸出|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>後續步驟

[了解如何更新應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)

[請參閱 host.json 檔案中的全域設定](functions-host-json.md)

[查看 App Service 應用程式的其他應用程式設定](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
