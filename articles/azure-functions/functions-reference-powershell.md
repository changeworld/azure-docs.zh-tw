---
title: Azure Functions 的 PowerShell 開發人員參考
description: 瞭解如何使用 PowerShell 開發函式。
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276734"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell 開發人員指南

本文提供有關如何使用 PowerShell 撰寫 Azure Functions 的詳細資料。

PowerShell Azure 函式（函式）會以 PowerShell 腳本的形式表示，並在觸發時執行。 每個函式腳本都有相關的 `function.json` 檔案，可定義函式的行為，例如觸發程式的方式，以及其輸入和輸出參數。 若要深入瞭解，請參閱[觸發程式和](functions-triggers-bindings.md)系結一文。 

如同其他類型的函式，PowerShell 腳本函式會採用符合 `function.json` 檔案中所定義之所有輸入系結名稱的參數。 也會傳遞 `TriggerMetadata` 參數，其中包含啟動函數之觸發程式的其他資訊。

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)。 您也應該已經完成[powershell 的函數快速入門](functions-create-first-function-powershell.md)，以建立您的第一個 powershell 函式。

## <a name="folder-structure"></a>資料夾結構

PowerShell 專案的必要資料夾結構如下所示。 此預設值可以變更。 如需詳細資訊，請參閱下面的 [scriptFile](#configure-function-scriptfile) 一節。

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

在專案的根目錄中，有一個可用於設定函數應用程式的共用[`host.json`](functions-host-json.md)檔案。 每個函式都有一個資料夾，其中包含自己的程式碼檔案（ps1）和系結設定檔（`function.json`）。 函數. json 檔案的父目錄名稱一律是函式的名稱。

某些系結需要有 `extensions.csproj` 檔案。 [版本2.x 和更新版本](functions-versions.md)的函式執行時間中所需的系結延伸模組會定義在 `extensions.csproj` 檔案中，而實際的程式庫檔案則位於 `bin` 資料夾中。 在本機開發時，您必須[註冊繫結擴充功能](functions-bindings-register.md#extension-bundles)。 開發 Azure 入口網站中的函式時，就會為您完成這項註冊。

在 PowerShell 函式應用程式中，您可以選擇性地擁有在函式應用程式開始執行時執行的 `profile.ps1` （也就是所謂的 *[冷啟動](#cold-start)* 。 如需詳細資訊，請參閱[PowerShell 設定檔](#powershell-profile)。

## <a name="defining-a-powershell-script-as-a-function"></a>將 PowerShell 腳本定義為函式

根據預設，Functions 執行階段會在 `run.ps1` 中尋找您的函式，其中 `run.ps1` 與對應的 `function.json` 會共用相同的父目錄。

在執行時，會傳遞一些引數給您的腳本。 若要處理這些參數，請將 `param` 區塊新增至腳本的頂端，如下列範例所示：

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata 參數

`TriggerMetadata` 參數是用來提供觸發程式的其他相關資訊。 其他中繼資料會因系結而有所不同，但它們全都包含包含下列資料的 `sys` 屬性：

```powershell
$TriggerMetadata.sys
```

| 屬性   | 描述                                     | 類型     |
|------------|-------------------------------------------------|----------|
| UtcNow     | 當函式在 UTC 時間內觸發時        | Datetime |
| MethodName | 已觸發之函式的名稱     | 字串   |
| RandGuid   | 此函式執行的唯一 guid | 字串   |

每個觸發程式類型都有一組不同的中繼資料。 例如，`QueueTrigger` 的 `$TriggerMetadata` 包含 `InsertionTime`、`Id`、`DequeueCount`，還有其他專案。 如需佇列觸發程式中繼資料的詳細資訊，請移至[佇列觸發程式的官方檔](functions-bindings-storage-queue-trigger.md#message-metadata)。 請查看您正在使用之[觸發](functions-triggers-bindings.md)程式的相關檔，以查看觸發程式中繼資料內有哪些內容。

## <a name="bindings"></a>繫結

在[PowerShell 中，系結會在](functions-triggers-bindings.md)函式的函式中進行設定和定義。 Functions 會透過數種方式與繫結互動。

### <a name="reading-trigger-and-input-data"></a>讀取觸發程序和輸入資料

觸發程式和輸入系結會讀取為傳遞至您函式的參數。 在函數. json 中，輸入系結的 `direction` 設定為 `in`。 `function.json` 中定義的 `name` 屬性是 `param` 區塊中參數的名稱。 由於 PowerShell 會使用具名引數進行系結，因此參數的順序並不重要。 不過，最佳做法是遵循 `function.json`中定義的系結順序。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>寫入輸出資料

在函式中，輸出系結的 `direction` 設定為函數. json 中的 `out`。 您可以使用可用於函數執行時間的 `Push-OutputBinding` Cmdlet，寫入至輸出系結。 在所有情況下，`function.json` 中定義之系結的 `name` 屬性會對應至 `Push-OutputBinding` Cmdlet 的 `Name` 參數。

以下顯示如何在函數腳本中呼叫 `Push-OutputBinding`：

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

您也可以透過管線傳遞特定系結的值。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` 會根據針對 `-Name`指定的值而有不同的行為：

* 當指定的名稱無法解析為有效的輸出系結時，就會擲回錯誤。

* 當輸出系結接受值的集合時，您可以重複呼叫 `Push-OutputBinding` 來推送多個值。

* 當輸出系結只接受單一值時，第二次呼叫 `Push-OutputBinding` 會引發錯誤。

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` 語法

以下是用來呼叫 `Push-OutputBinding`的有效參數：

| 名稱 | 類型 | 位置 | 描述 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 您想要設定的輸出系結名稱。 |
| **`-Value`** | Object | 2 | 您想要設定的輸出系結值，這是從管線 ByValue 接受的。 |
| **`-Clobber`** | SwitchParameter | 已命名 | 選擇性當指定時，會強制針對指定的輸出系結設定值。 | 

也支援下列一般參數： 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

如需詳細資訊，請參閱[About CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)。

#### <a name="push-outputbinding-example-http-responses"></a>推播-OutputBinding 範例： HTTP 回應

HTTP 觸發程式會使用名為 `response`的輸出系結來傳迴響應。 在下列範例中，`response` 的輸出系結具有 "output #1" 的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

因為輸出是 HTTP （只接受單一值），所以第二次呼叫 `Push-OutputBinding` 時，就會擲回錯誤。

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

對於只接受單一值的輸出，您可以使用 `-Clobber` 參數來覆寫舊的值，而不是嘗試加入至集合。 下列範例假設您已經加入值。 藉由使用 `-Clobber`，下列範例的回應會覆寫現有的值，以傳回 "output #3" 的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>推播-OutputBinding 範例：佇列輸出系結

`Push-OutputBinding` 可用來將資料傳送到輸出系結，例如[Azure 佇列儲存體輸出](functions-bindings-storage-queue-output.md)系結。 在下列範例中，寫入佇列的訊息具有 "output #1" 的值：

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

儲存體佇列的輸出系結會接受多個輸出值。 在此情況下，請在第一次寫入佇列時呼叫下列範例，其中包含兩個專案的清單： "output #1" 和 "output #2"。

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

下列範例會在前兩個後呼叫之後，再將兩個值新增至輸出集合：

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

寫入佇列時，訊息會包含下列四個值：「輸出 #1」、「輸出 #2」、「輸出 #3」和「輸出 #4」。

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` Cmdlet

您可以使用 `Get-OutputBinding` Cmdlet 來抓取目前針對您的輸出系結所設定的值。 此 Cmdlet 會抓取雜湊表，其中包含輸出系結的名稱及其各自的值。 

以下是使用 `Get-OutputBinding` 傳回目前系結值的範例：

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` 也包含稱為 `-Name`的參數，可以用來篩選傳回的系結，如下列範例所示：

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

`Get-OutputBinding`支援萬用字元（*）。

## <a name="logging"></a>記錄

PowerShell 函式中的記錄運作方式與一般 PowerShell 記錄類似。 您可以使用記錄 Cmdlet 來寫入每個輸出資料流程。 每個 Cmdlet 都會對應至函式所使用的記錄層級。

| 函數記錄層級 | 記錄 Cmdlet |
| ------------- | -------------- |
| 錯誤 | **`Write-Error`** |
| 警告 | **`Write-Warning`**  | 
| 資訊 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | 資訊 | 寫入_資訊_層級記錄。 |
| 偵錯 | **`Write-Debug`** |
| 追蹤 | **`Write-Progress`** <br /> **`Write-Verbose`** |

除了這些 Cmdlet 之外，所有寫入管線的專案都會重新導向至 `Information` 記錄層級，並以預設的 PowerShell 格式來顯示。

> [!IMPORTANT]
> 使用 `Write-Verbose` 或 `Write-Debug` Cmdlet 並不足以查看詳細資訊和調試層級記錄。 您也必須設定記錄層級臨界值，以宣告您真正關心的記錄層級。 若要深入瞭解，請參閱[設定函數應用程式記錄層級](#configure-the-function-app-log-level)。

### <a name="configure-the-function-app-log-level"></a>設定函數應用程式記錄層級

Azure Functions 可讓您定義閾值層級，讓您輕鬆控制函數寫入記錄的方式。 若要為寫入主控台的所有追蹤設定閾值，請使用[`host.json`]檔案[主機. json 參考]中的 `logging.logLevel.default` 屬性。 這個設定會套用到函式應用程式中的所有函式。

下列範例會設定閾值以啟用所有函式的詳細資訊記錄，但設定閾值以啟用名為 `MyFunction`之函式的偵錯工具記錄功能：

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

如需詳細資訊，請參閱 [host.json 參考]。

### <a name="viewing-the-logs"></a>查看記錄

如果您的函數應用程式在 Azure 中執行，您可以使用 Application Insights 來進行監視。 參閱[監視 Azure Functions](functions-monitoring.md) 深入了解如何檢視和查詢函式記錄。

如果您是在本機執行函數應用程式以進行開發，記錄檔會預設為檔案系統。 若要查看主控台中的記錄，請將 `AZURE_FUNCTIONS_ENVIRONMENT` 環境變數設定為 `Development`，然後再啟動函數應用程式。

## <a name="triggers-and-bindings-types"></a>觸發程式和系結類型

有數個觸發程式和系結可供您用於函數應用程式。 您[可以在這裡找到](functions-triggers-bindings.md#supported-bindings)完整的觸發程式和系結清單。

所有的觸發程式和系結都是以程式碼表示，做為幾個實際的資料類型：

* 雜湊表
* 字串
* byte[]
* int
* double
* HttpRequestCoNtext
* HttpResponseCoNtext

這份清單中的前五個類型是標準的 .NET 類型。 最後兩個僅供[HttpTrigger 觸發](#http-triggers-and-bindings)程式使用。

函式中的每個系結參數都必須是這些類型的其中一種。

### <a name="http-triggers-and-bindings"></a>HTTP 觸發程序和繫結

HTTP 和 Webhook 觸發程序以及 HTTP 輸出繫結會使用要求和回應物件來代表 HTTP 傳訊。

#### <a name="request-object"></a>要求物件

傳入腳本的要求物件屬於 `HttpRequestContext`類型，其具有下列屬性：

| 屬性  | 描述                                                    | 類型                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 包含要求本文的物件。 `Body` 會根據資料序列化為最佳類型。 例如，如果資料是 JSON，則會以雜湊表的形式傳入。 如果資料是字串，則會以字串的形式傳入。 | 物件 (object) |
| **`Headers`** | 包含要求標頭的字典。                | 字典 < 字串，字串 ><sup>*</sup> |
| **`Method`** | 要求的 HTTP 方法。                                | 字串                    |
| **`Params`**  | 包含要求之路由傳送參數的物件。 | 字典 < 字串，字串 ><sup>*</sup> |
| **`Query`** | 包含查詢參數的物件。                  | 字典 < 字串，字串 ><sup>*</sup> |
| **`Url`** | 要求的 URL。                                        | 字串                    |

<sup>*</sup>所有 `Dictionary<string,string>` 的索引鍵都不區分大小寫。

#### <a name="response-object"></a>回應物件

您應該傳回的回應物件屬於 `HttpResponseContext`類型，其具有下列屬性：

| 屬性      | 描述                                                 | 類型                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 包含回應本文的物件。           | 物件 (object)                    |
| **`ContentType`** | 設定回應內容類型的簡短手勢。 | 字串                    |
| **`Headers`** | 包含回應標頭的物件。               | 字典或雜湊表   |
| **`StatusCode`**  | 回應的 HTTP 狀態碼。                       | 字串或整數             |

#### <a name="accessing-the-request-and-response"></a>存取要求和回應

當您使用 HTTP 觸發程式時，您可以使用與其他任何輸入系結相同的方式來存取 HTTP 要求。 它位於 `param` 區塊中。

使用 `HttpResponseContext` 物件來傳迴響應，如下所示：

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

叫用此函數的結果會是：

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>觸發程式和系結的類型轉換

對於 blob 系結之類的特定系結，您可以指定參數的類型。

例如，若要將 Blob 儲存體中的資料當做字串提供，請將下列類型轉換新增至我的 `param` 區塊：

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 設定檔

在 PowerShell 中，有 PowerShell 設定檔的概念。 如果您不熟悉 PowerShell 設定檔，請參閱[關於設定檔](/powershell/module/microsoft.powershell.core/about/about_profiles)。

在 PowerShell 函式中，設定檔腳本會在函數應用程式啟動時執行。 函式應用程式會在第一次部署和閒置之後啟動（[冷啟動](#cold-start)）。

當您使用工具（例如 Visual Studio Code 和 Azure Functions Core Tools）建立函數應用程式時，系統會為您建立預設 `profile.ps1`。 預設設定檔會保留[在 Core Tools GitHub 存放庫中](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1)，並包含：

* Azure 的自動 MSI 驗證。
* 如果您想要的話，可以開啟 Azure PowerShell `AzureRM` PowerShell 別名。

## <a name="powershell-version"></a>PowerShell 版本

下表顯示每個主要版本的函式執行時間所使用的 PowerShell 版本：

| Functions 版本 | PowerShell 版本                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 （由執行時間鎖定） |
| 2.x               | PowerShell Core 6                              |

您可以從任何函式列印 `$PSVersionTable`，以查看目前的版本。

## <a name="dependency-management"></a>相依性管理

函式可讓您利用[PowerShell 資源庫](https://www.powershellgallery.com)來管理相依性。 啟用相依性管理時，會使用 .psd1 檔案來自動下載所需的模組。 若要啟用此行為，請將 `managedDependency` 屬性設定為在[主機 json](functions-host-json.md)檔案的根目錄中 `true`，如下列範例所示：

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

當您建立新的 PowerShell 函式專案時，依預設會啟用相依性管理，其中包含 Azure [`Az` 模組](/powershell/azure/new-azureps-module-az)。 目前支援的模組數目上限為10。 支援的語法為 _`MajorNumber`_ `.*` 或確切的模組版本，如下列需求所示。 .psd1 範例：

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

當您更新 .psd1 檔案時，會在重新開機之後安裝更新的模組。

> [!NOTE]
> 受控相依性需要存取 www.powershellgallery.com 才能下載模組。 在本機執行時，請確定執行時間可以藉由新增任何必要的防火牆規則來存取此 URL。 

下列應用程式設定可以用來變更如何下載和安裝受控相依性。 您的應用程式升級會在 `MDMaxBackgroundUpgradePeriod`內開始，且升級程式會在大約的 `MDNewSnapshotCheckPeriod`內完成。

| 函數應用程式設定              | 預設值             | 描述                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` （7天）     | 每個 PowerShell 工作者進程都會在進程啟動時，起始檢查 PowerShell 資源庫上的模組升級，而每個 `MDMaxBackgroundUpgradePeriod` 之後。 當 PowerShell 資源庫中有新的模組版本可用時，它會安裝到檔案系統，並提供給 PowerShell 背景工作。 降低此值可讓您的函數應用程式更快取得較新的模組版本，但它也會增加應用程式資源使用量（網路 i/o、CPU、儲存體）。 增加此值會減少應用程式的資源使用量，但它也可能會延遲傳遞新的模組版本至您的應用程式。 | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` （1小時）       | 將新的模組版本安裝至檔案系統後，必須重新開機每個 PowerShell 背景工作進程。 重新開機 PowerShell 背景工作會影響您的應用程式可用性，因為它可以中斷目前的函式執行。 在重新開機所有 PowerShell 背景工作進程之前，函式呼叫可能會使用舊的或新的模組版本。 在 `MDNewSnapshotCheckPeriod`內重新開機所有的 PowerShell 背景工作角色。 增加這個值會減少中斷的頻率，但也可能會增加函式呼叫使用舊或新模組版本不具決定性的時間長度。 |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` （1天）     | 若要避免在頻繁的工作者重新開機時進行過多的模組升級，當任何工作者已經起始該簽入最後一個 `MDMinBackgroundUpgradePeriod`時，不會執行檢查模組升級。 |

利用您自己的自訂模組，與一般做法稍有不同。

在您的本機電腦上，模組會安裝在 `$env:PSModulePath`中的其中一個全域可用資料夾中。 在 Azure 中執行時，您無法存取電腦上所安裝的模組。 這表示 PowerShell 函數應用程式的 `$env:PSModulePath` 與一般 PowerShell 腳本中的 `$env:PSModulePath` 不同。

在 [函數] 中，`PSModulePath` 包含兩個路徑：

* 存在於函式應用程式根目錄中的 `Modules` 資料夾。
* PowerShell 語言背景工作角色所控制之 `Modules` 資料夾的路徑。

### <a name="function-app-level-modules-folder"></a>函數應用層級 `Modules` 資料夾

若要使用自訂模組，您可以將函式相依的模組放在 `Modules` 資料夾中。 從這個資料夾，模組會自動提供給函式執行時間使用。 函數應用程式中的任何函式都可以使用這些模組。 

> [!NOTE]
> .Psd1 檔案中指定的模組會自動下載並包含在路徑中，因此您不需要將它們包含在 [模組] 資料夾中。 在雲端執行時，這些會儲存在本機的 `$env:LOCALAPPDATA/AzureFunctions` 資料夾和 `/data/ManagedDependencies` 資料夾中。

若要利用自訂模組功能，請在函式應用程式的根目錄中建立 `Modules` 資料夾。 將您想要在函式中使用的模組複製到這個位置。

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

使用 `Modules` 資料夾時，您的函數應用程式應該具有下列資料夾結構：

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

當您啟動函式應用程式時，PowerShell 語言背景工作角色會將這個 `Modules` 資料夾新增至 `$env:PSModulePath`，讓您可以相依模組自動載入，就像在一般 PowerShell 腳本中一樣。

### <a name="language-worker-level-modules-folder"></a>語言背景工作角色層級 `Modules` 資料夾

PowerShell 語言背景工作角色通常會使用數個模組。 這些模組定義于 `PSModulePath`的最後一個位置。 

目前的模組清單如下所示：

* [ [Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive)]：用來處理封存的模組，例如 `.zip`、`.nupkg`及其他。
* **ThreadJob**： PowerShell 作業 api 的執行緒型執行。

根據預設，函數會使用這些模組的最新版本。 若要使用特定的模組版本，請將該特定版本放在函式應用程式的 [`Modules`] 資料夾中。

## <a name="environment-variables"></a>環境變數

在 Functions 中，[應用程式設定](functions-app-settings.md) (例如服務連接字串) 在執行期間會公開為環境變數。 您可以使用 `$env:NAME_OF_ENV_VAR`來存取這些設定，如下列範例所示：

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

在本機執行時，應用程式設定會讀取自 [local.settings.json](functions-run-local.md#local-settings-file) 專案檔。

## <a name="concurrency"></a>並行

根據預設，PowerShell 執行時間一次只能處理一個函式的調用。 不過，在下列情況下，此並行層級可能不足：

* 當您嘗試同時處理大量調用時。
* 當您的函式叫用相同函式應用程式內的其他函數時。

您可以藉由將下列環境變數設定為整數值，來變更此行為：

```
PSWorkerInProcConcurrencyUpperBound
```

您會在函數應用程式的[應用程式](functions-app-settings.md)設定中設定此環境變數。

### <a name="considerations-for-using-concurrency"></a>使用並行的考慮

根據預設，PowerShell 是_單一執行緒_指令碼語言。 不過，您可以在相同的進程中使用多個 PowerShell 自動程式來新增平行存取。 所建立的工作空間數量會符合 PSWorkerInProcConcurrencyUpperBound 應用程式設定。 輸送量會受到所選方案中可用的 CPU 和記憶體數量所影響。

Azure PowerShell 會使用一些_進程層級_的內容和狀態，協助您避免多餘的輸入。 不過，如果您在函式應用程式中開啟平行存取，並叫用變更狀態的動作，最後可能會有競爭條件。 這些競爭條件很容易進行調試，因為一個調用依賴特定狀態，而其他調用已變更狀態。

Azure PowerShell 的並行處理有極大的價值，因為有些作業可能需要相當長的時間。 不過，您必須謹慎進行。 如果您懷疑遇到競爭情形，請將 PSWorkerInProcConcurrencyUpperBound 應用程式設定設為 `1`，並改為使用[language worker 進程層級隔離](functions-app-settings.md#functions_worker_process_count)來進行並行。

## <a name="configure-function-scriptfile"></a>設定函數 `scriptFile`

根據預設，PowerShell 函式會從 `run.ps1`執行，這是共用相同上層目錄做為其對應 `function.json`的檔案。

`function.json` 中的 `scriptFile` 屬性可以用來取得如下列範例所示的資料夾結構：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

在這種情況下，`myFunction` 的 `function.json` 會包含一個 `scriptFile` 屬性，參考具有要執行之匯出函式的檔案。

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>藉由設定 entryPoint 來使用 PowerShell 模組

本文已在範本所產生的預設 `run.ps1` 腳本檔案中顯示 PowerShell 函式。
不過，您也可以在 PowerShell 模組中包含函數。 您可以使用函式. json ' 設定檔中的 `scriptFile` 和 `entryPoint` 欄位來參考模組中的特定函式程式碼。

在此情況下，`entryPoint` 是 `scriptFile`中所參考的 PowerShell 模組中的函式或 Cmdlet 的名稱。

請考慮下列資料夾結構：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

其中 `PSFunction.psm1` 包含：

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

在此範例中，`myFunction` 的設定包含參考 `PSFunction.psm1`的 `scriptFile` 屬性，這是另一個資料夾中的 PowerShell 模組。  `entryPoint` 屬性會參考 `Invoke-PSTestFunc` 函數，也就是模組中的進入點。

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

使用此設定時，`Invoke-PSTestFunc` 的執行方式完全如同 `run.ps1`。

## <a name="considerations-for-powershell-functions"></a>PowerShell 函式的考慮

當您使用 PowerShell 函數時，請注意下列各節中的考慮。

### <a name="cold-start"></a>冷啟動

在[無伺服器裝載模型](functions-scale.md#consumption-plan)中開發 Azure Functions 時，冷啟動是一項現實。 *「冷啟動」* 指的是您的函式應用程式開始執行以處理要求所需的一段時間。 因為您的函式應用程式會在非使用狀態期間關閉，所以取用方案中的冷啟動會變得更頻繁。

### <a name="bundle-modules-instead-of-using-install-module"></a>組合模組，而不是使用 `Install-Module`

您的腳本會在每次叫用時執行。 避免在您的腳本中使用 `Install-Module`。 請改為在發佈前使用 `Save-Module`，讓您的函式不需要浪費時間下載模組。 如果冷啟動會影響您的函式，請考慮將您的函數應用程式部署到[App Service 計畫](functions-scale.md#app-service-plan)設定為*Always on*或[Premium 方案](functions-scale.md#premium-plan)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)

[host.json 參考]: functions-host-json.md
