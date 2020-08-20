---
title: Azure Functions 的 PowerShell 開發人員參考
description: 瞭解如何使用 PowerShell 開發函數。
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 04/22/2019
ms.openlocfilehash: dd3978ee1f371d59119e406c5f023718d57ad99b
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642209"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell 開發人員指南

本文提供如何使用 PowerShell 撰寫 Azure Functions 的詳細資料。

PowerShell Azure function (函式) 會以 PowerShell 腳本的形式表示，此腳本會在觸發時執行。 每個函式腳本都有相關的檔案，可定義函式的 `function.json` 運作方式，例如其觸發方式及其輸入和輸出參數。 若要深入瞭解，請參閱 [觸發程式和](functions-triggers-bindings.md)系結文章。 

如同其他類型的函式，PowerShell 腳本函式會採用符合檔案中定義之所有輸入系結名稱的參數 `function.json` 。 `TriggerMetadata`也會傳遞一個參數，其中包含啟動函式之觸發程式的其他資訊。

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)。 您也應該完成 PowerShell 的函式 [快速入門](./functions-create-first-function-vs-code.md?pivots=programming-language-powershell) ，以建立您的第一個 powershell 函數。

## <a name="folder-structure"></a>資料夾結構

PowerShell 專案的必要資料夾結構看起來如下所示。 此預設值可以變更。 如需詳細資訊，請參閱下面的 [scriptFile](#configure-function-scriptfile) 一節。

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

在專案的根目錄中，有一個 [`host.json`](functions-host-json.md) 可用來設定函數應用程式的共用檔案。 每個函式都有一個資料夾，內含自己的程式碼檔 ( ps1) 和系結設定檔 (`function.json`) 。 檔案的上層目錄上 function.js的名稱一律是您函式的名稱。

某些系結需要有檔案存在 `extensions.csproj` 。 [版本2.x 和更新版本](functions-versions.md)的函式執行時間所需的系結延伸模組，會在檔案中定義 `extensions.csproj` ，其中包含資料夾中的實際程式庫檔案 `bin` 。 在本機開發時，您必須[註冊繫結擴充功能](functions-bindings-register.md#extension-bundles)。 開發 Azure 入口網站中的函式時，就會為您完成這項註冊。

在 PowerShell 函式應用程式中，您可以選擇是否要在函式 `profile.ps1` 應用程式開始執行時執行此功能 (否則即為 *[冷啟動](#cold-start)*。 如需詳細資訊，請參閱 [PowerShell 設定檔](#powershell-profile)。

## <a name="defining-a-powershell-script-as-a-function"></a>將 PowerShell 腳本定義為函式

根據預設，Functions 執行階段會在 `run.ps1` 中尋找您的函式，其中 `run.ps1` 與對應的 `function.json` 會共用相同的父目錄。

您的腳本在執行時傳遞了數個引數。 若要處理這些參數，請將 `param` 區塊新增至腳本頂端，如下列範例所示：

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata 參數

`TriggerMetadata`參數是用來提供有關觸發程式的其他資訊。 額外的中繼資料會因系結至系結而異，但它們都包含 `sys` 包含下列資料的屬性：

```powershell
$TriggerMetadata.sys
```

| 屬性   | 描述                                     | 類型     |
|------------|-------------------------------------------------|----------|
| UtcNow     | 在 UTC 時間內觸發函式的時間        | Datetime |
| MethodName | 已觸發之函式的名稱     | 字串   |
| RandGuid   | 此函數執行的唯一 guid | 字串   |

每個觸發程式類型都有一組不同的中繼資料。 例如，的會 `$TriggerMetadata` `QueueTrigger` 包含 `InsertionTime` 、，以及 `Id` `DequeueCount` 其他專案。 如需佇列觸發程式中繼資料的詳細資訊，請移至 [佇列觸發程式的官方檔](functions-bindings-storage-queue-trigger.md#message-metadata)。 請查看您正在使用之 [觸發](functions-triggers-bindings.md) 程式的相關檔，以查看觸發程式中繼資料內有哪些內容。

## <a name="bindings"></a>繫結

在 PowerShell 中 [，系](functions-triggers-bindings.md) 結是在函數的 function.js中設定和定義。 Functions 會透過數種方式與繫結互動。

### <a name="reading-trigger-and-input-data"></a>讀取觸發程序和輸入資料

觸發程式和輸入系結會讀取為傳遞給函式的參數。 輸入系結的 `direction` function.js的設定為 `in` 。 `name`中定義的屬性 `function.json` 是區塊中參數的名稱 `param` 。 因為 PowerShell 使用具名引數進行系結，所以參數的順序並不重要。 不過，最佳作法是遵循中定義的系結順序 `function.json` 。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>寫入輸出資料

在函式中，輸出系結的 `direction` function.js中的設定為 `out` 。 您可以使用 `Push-OutputBinding` Cmdlet （可供函式執行時間使用）寫入至輸出系結。 在所有情況下， `name` 如中所定義之系結的屬性會 `function.json` 對應至 `Name` Cmdlet 的參數 `Push-OutputBinding` 。

以下顯示如何在您的函式 `Push-OutputBinding` 腳本中呼叫：

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

您也可以透過管線傳遞特定系結的值。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` 會根據指定的值以不同方式運作 `-Name` ：

* 當指定的名稱無法解析為有效的輸出系結時，就會擲回錯誤。

* 當輸出系結接受值的集合時，您可以 `Push-OutputBinding` 重複呼叫來推送多個值。

* 當輸出系結只接受單一值時，呼叫 `Push-OutputBinding` 第二次時會引發錯誤。

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` 語法

以下是用於呼叫的有效參數 `Push-OutputBinding` ：

| 名稱 | 類型 | 位置 | 描述 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 您要設定之輸出系結的名稱。 |
| **`-Value`** | Object | 2 | 您要設定的輸出系結值，可從管線 ByValue 接受。 |
| **`-Clobber`** | SwitchParameter | 已命名 |  (選擇性) 指定時，會強制設定指定輸出系結的值。 | 

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

如需詳細資訊，請參閱 [關於 CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)。

#### <a name="push-outputbinding-example-http-responses"></a>OutputBinding 範例： HTTP 回應

HTTP 觸發程式會使用名為的輸出系結來傳迴響應 `response` 。 在下列範例中，的輸出系結 `response` 具有 "output #1" 的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

因為輸出為 HTTP （只接受單一值），所以 `Push-OutputBinding` 第二次呼叫時，會擲回錯誤。

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

針對只接受單一值的輸出，您可以使用 `-Clobber` 參數來覆寫舊的值，而不是嘗試將其加入至集合。 下列範例假設您已新增一個值。 藉由使用 `-Clobber` ，下列範例的回應會覆寫現有的值，以傳回 "output #3" 的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>OutputBinding 範例：佇列輸出系結

`Push-OutputBinding` 用來將資料傳送至輸出系結，例如 [Azure 佇列儲存體輸出](functions-bindings-storage-queue-output.md)系結。 在下列範例中，寫入佇列的訊息具有 "output #1" 的值：

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

儲存體佇列的輸出系結接受多個輸出值。 在此情況下，請在第一次將包含兩個專案的清單寫入佇列時，呼叫下列範例： "output #1" 和 "output #2"。

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

下列範例會在先前的兩個之後呼叫，將兩個以上的值新增至輸出集合：

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

寫入佇列時，訊息會包含下列四個值：「輸出 #1」、「輸出 #2」、「輸出 #3」和「輸出 #4」。

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` Cmdlet

您可以使用 `Get-OutputBinding` Cmdlet 來取得目前針對輸出系結所設定的值。 此 Cmdlet 會取出包含輸出系結名稱及其各自值的雜湊表。 

以下是使用來傳回目前系結值的範例 `Get-OutputBinding` ：

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` 也包含稱為的參數 `-Name` ，這個參數可以用來篩選傳回的系結，如下列範例所示：

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

支援萬用字元 ( * ) `Get-OutputBinding` 。

## <a name="logging"></a>記錄

PowerShell 函式中的記錄運作方式類似一般的 PowerShell 記錄。 您可以使用記錄 Cmdlet 來寫入每個輸出資料流程。 每個 Cmdlet 都會對應至函式所使用的記錄層級。

| 函數記錄層級 | 記錄 Cmdlet |
| ------------- | -------------- |
| 錯誤 | **`Write-Error`** |
| 警告 | **`Write-Warning`**  | 
| 資訊 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | 資訊 | 寫入 _資訊_ 層級記錄。 |
| 偵錯 | **`Write-Debug`** |
| 追蹤 | **`Write-Progress`** <br /> **`Write-Verbose`** |

除了這些 Cmdlet，寫入管線的任何事物都會重新導向至 `Information` 記錄層級，並以預設的 PowerShell 格式顯示。

> [!IMPORTANT]
> 使用 `Write-Verbose` 或 Cmdlet 並不足 `Write-Debug` 以查看詳細資訊和 debug 層級記錄。 您也必須設定記錄層級閾值，以宣告您真正在意的記錄層級。 若要深入瞭解，請參閱 [設定函數應用程式記錄層級](#configure-the-function-app-log-level)。

### <a name="configure-the-function-app-log-level"></a>設定函數應用程式記錄層級

Azure Functions 可讓您定義閾值層級，讓您輕鬆控制函式寫入至記錄的方式。 若要設定寫入至主控台之所有追蹤的閾值，請使用檔案 `logging.logLevel.default` 中的[ `host.json` ]屬性[host.js參考]。 這個設定會套用到函式應用程式中的所有函式。

下列範例會設定臨界值以啟用所有函式的詳細資訊記錄，但會設定臨界值以啟用名為的函式的 debug 記錄 `MyFunction` ：

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

如果您的函式應用程式正在 Azure 中執行，您可以使用 Application Insights 來監視它。 參閱[監視 Azure Functions](functions-monitoring.md) 深入了解如何檢視和查詢函式記錄。

如果您在本機執行函式應用程式進行開發，記錄檔會預設為檔案系統。 若要在主控台中查看記錄，請在 `AZURE_FUNCTIONS_ENVIRONMENT` 啟動函式應用程式之前將環境變數設定為 `Development` 。

## <a name="triggers-and-bindings-types"></a>觸發程式和系結類型

有幾個觸發程式和系結可供您用於函數應用程式。 您 [可以在這裡找到](functions-triggers-bindings.md#supported-bindings)觸發程式和系結的完整清單。

所有觸發程式和系結都是在程式碼中以一些實際的資料類型來表示：

* 雜湊表
* 字串
* byte[]
* int
* double
* HttpRequestCoNtext
* HttpResponseCoNtext

這份清單中的前五個類型是標準的 .NET 類型。 最後兩個僅供 [HttpTrigger 觸發](#http-triggers-and-bindings)程式使用。

您函式中的每個系結參數都必須是這些類型的其中之一。

### <a name="http-triggers-and-bindings"></a>HTTP 觸發程序和繫結

HTTP 和 Webhook 觸發程序以及 HTTP 輸出繫結會使用要求和回應物件來代表 HTTP 傳訊。

#### <a name="request-object"></a>要求物件

傳遞至腳本的要求物件為類型 `HttpRequestContext` ，具有下列屬性：

| 屬性  | 描述                                                    | 類型                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 包含要求本文的物件。 `Body` 會根據資料序列化為最佳型別。 例如，如果資料是 JSON，則會傳入做為雜湊表。 如果資料是字串，則會以字串形式傳遞。 | 物件 (object) |
| **`Headers`** | 包含要求標頭的字典。                | 字典<字串，字串><sup>*</sup> |
| **`Method`** | 要求的 HTTP 方法。                                | 字串                    |
| **`Params`**  | 包含要求之路由傳送參數的物件。 | 字典<字串，字串><sup>*</sup> |
| **`Query`** | 包含查詢參數的物件。                  | 字典<字串，字串><sup>*</sup> |
| **`Url`** | 要求的 URL。                                        | 字串                    |

<sup>*</sup> 所有索引 `Dictionary<string,string>` 鍵都不區分大小寫。

#### <a name="response-object"></a>回應物件

您應該送回的回應物件為類型 `HttpResponseContext` ，具有下列屬性：

| 屬性      | 描述                                                 | 類型                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 包含回應本文的物件。           | 物件 (object)                    |
| **`ContentType`** | 針對回應設定內容類型的簡短手勢。 | 字串                    |
| **`Headers`** | 包含回應標頭的物件。               | 字典或雜湊表   |
| **`StatusCode`**  | 回應的 HTTP 狀態碼。                       | 字串或整數             |

#### <a name="accessing-the-request-and-response"></a>存取要求和回應

當您使用 HTTP 觸發程式時，您可以使用與任何其他輸入系結相同的方式來存取 HTTP 要求。 它是在 `param` 區塊中。

使用 `HttpResponseContext` 物件傳迴響應，如下列所示：

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

叫用此函數的結果為：

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>觸發程式和系結的類型轉換

針對某些系結（例如 blob 系結），您可以指定參數的類型。

例如，若要讓 Blob 儲存體中的資料以字串的形式提供，請將下列類型轉換新增至我的 `param` 區塊：

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 設定檔

PowerShell 中有 PowerShell 設定檔的概念。 如果您不熟悉 PowerShell 設定檔，請參閱 [關於設定檔](/powershell/module/microsoft.powershell.core/about/about_profiles)。

在 PowerShell 函式中，當函式應用程式啟動時，就會執行設定檔腳本。 函數應用程式會在第一次部署時啟動，並在閒置之後 ([冷啟動](#cold-start)) 。

當您使用工具（例如 Visual Studio Code 和 Azure Functions Core Tools）來建立函數應用程式時， `profile.ps1` 系統會為您建立預設值。 預設設定檔會保留 [在 Core Tools GitHub 存放庫中](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) ，並包含：

* Azure 的自動 MSI 驗證。
* 如果您想要的話，也可以開啟 Azure PowerShell `AzureRM` PowerShell 別名。

## <a name="powershell-versions"></a>PowerShell 版本

下表顯示每個主要版本的函式執行時間所支援的 PowerShell 版本，以及所需的 .NET 版本：

| Functions 版本 | PowerShell 版本                               | .NET 版本  | 
|-------------------|--------------------------------------------------|---------------|
| 3. x (建議的)  | PowerShell 7 (建議的) <br/>PowerShell Core 6 | .NET Core 3.1<br/>.NET Core 3.1 |
| 2.x               | PowerShell Core 6                                | .NET Core 2.2 |

您可以藉由從任何函式列印來查看目前的版本 `$PSVersionTable` 。

## <a name="dependency-management"></a>相依性管理

函數可讓您利用 [PowerShell 資源庫](https://www.powershellgallery.com) 來管理相依性。 啟用相依性管理後，就會使用 requirements.psd1 檔案自動下載必要的模組。 您可以藉由在 `managedDependency`host.json 檔案的根目錄中將屬性設為，以啟用此行為 `true` ，如下列範例[host.json file](functions-host-json.md)所示：

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

當您建立新的 PowerShell 函式專案時，依預設會啟用相依性管理，並包含 Azure [ `Az` 模組](/powershell/azure/new-azureps-module-az)。 目前支援的模組數目上限為10。 支援的語法是 _`MajorNumber`_ `.*` 或確切的模組版本，如下列 requirements.psd1 範例所示：

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

當您更新 requirements.psd1 檔案時，會在重新開機之後安裝更新的模組。

> [!NOTE]
> 受控相依性需要存取 www.powershellgallery.com 才能下載模組。 在本機執行時，請確定執行時間可以藉由新增任何必要的防火牆規則來存取此 URL。 

您可以使用下列應用程式設定來變更如何下載及安裝管理的相依性。 您的應用程式升級會在中開始 `MDMaxBackgroundUpgradePeriod` ，而升級程式會在大約的範圍內完成 `MDNewSnapshotCheckPeriod` 。

| 函數應用程式設定              | 預設值             | 描述                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 天)      | 每個 PowerShell 工作者進程會在進程啟動時，以及每次啟動時，開始檢查 PowerShell 資源庫上的模組升級 `MDMaxBackgroundUpgradePeriod` 。 當 PowerShell 資源庫中有新的模組版本時，它會安裝到檔案系統，並提供給 PowerShell 背景工作。 降低此值可讓您的函數應用程式更快取得較新的模組版本，但也會增加應用程式資源使用量 (網路 i/o、CPU、儲存體) 。 提高此值可減少應用程式的資源使用方式，但也可能會延遲將新的模組版本傳遞至您的應用程式。 | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 小時)        | 將新模組版本安裝到檔案系統之後，每個 PowerShell 工作者進程都必須重新開機。 重新開機 PowerShell 背景工作會影響您應用程式的可用性，因為它可以中斷目前的函式執行。 在重新開機所有 PowerShell 工作者進程之前，函式呼叫可能會使用舊的或新的模組版本。 在中重新開機所有的 PowerShell 背景工作 `MDNewSnapshotCheckPeriod` 。 增加此值會減少中斷的頻率，但是也可能會增加函式呼叫使用舊的或新模組版本不具決定性的時間長度。 |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 天)      | 若要避免在頻繁的背景工作角色重新開機時過度升級模組，則不會在任何背景工作角色已起始最後一次檢查時執行模組升級檢查 `MDMinBackgroundUpgradePeriod` 。 |

利用您自己的自訂模組，與您一般的方式稍有不同。

在您的本機電腦上，模組會安裝在您的其中一個全球可用資料夾中 `$env:PSModulePath` 。 在 Azure 中執行時，您無法存取電腦上所安裝的模組。 這表示 PowerShell 函式 `$env:PSModulePath` 應用程式的與 `$env:PSModulePath` 一般 powershell 腳本中的不同。

在函數中， `PSModulePath` 包含兩個路徑：

* `Modules`存在於函數應用程式根目錄中的資料夾。
* `Modules`由 PowerShell 語言背景工作角色控制的資料夾路徑。

### <a name="function-app-level-modules-folder"></a>函數應用層級 `Modules` 資料夾

若要使用自訂模組，您可以放置函式相依于資料夾的模組 `Modules` 。 從這個資料夾中，模組會自動提供給函數執行時間使用。 函數應用程式中的任何函式都可以使用這些模組。 

> [!NOTE]
> requirements.psd1 檔案中指定的模組會自動下載並包含在路徑中，因此您不需要將它們包含在模組資料夾中。 在雲端中執行時，這些檔案會儲存在本機的 `$env:LOCALAPPDATA/AzureFunctions` 資料夾和 `/data/ManagedDependencies` 資料夾中。

若要利用自訂模組功能，請在函式 `Modules` 應用程式的根目錄中建立資料夾。 將您想要在函式中使用的模組複製到這個位置。

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

當您啟動函式應用程式時，PowerShell 語言背景工作角色會將此 `Modules` 資料夾新增至， `$env:PSModulePath` 如此您就可以相依模組自動載入功能，就像在一般的 PowerShell 腳本中一樣。

### <a name="language-worker-level-modules-folder"></a>語言背景工作角色層級 `Modules` 資料夾

PowerShell 語言背景工作角色通常會使用數個模組。 這些模組會定義在的最後一個位置 `PSModulePath` 。 

目前的模組清單如下所示：

* [封存[]：用於](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive)處理封存的模組，例如 `.zip` 、 `.nupkg` 和其他。
* **ThreadJob**： PowerShell 作業 api 的執行緒型執行。

根據預設，函數會使用這些模組的最新版本。 若要使用特定模組版本，請將該特定版本放在 `Modules` 函數應用程式的資料夾中。

## <a name="environment-variables"></a>環境變數

在 Functions 中，[應用程式設定](functions-app-settings.md) (例如服務連接字串) 在執行期間會公開為環境變數。 您可以使用來存取這些設定 `$env:NAME_OF_ENV_VAR` ，如下列範例所示：

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

在本機執行時，應用程式設定會讀取自 [local.settings.json](functions-run-local.md#local-settings-file) 專案檔。

## <a name="concurrency"></a>並行

根據預設，PowerShell 執行時間一次只能處理一個函式呼叫。 不過，在下列情況下，此並行層級可能不足：

* 當您嘗試同時處理大量調用時。
* 當您的函式會叫用相同函式應用程式內的其他函式時。

您可以藉由將下列環境變數設定為整數值，來變更此行為：

```
PSWorkerInProcConcurrencyUpperBound
```

您可以在函數應用程式的 [應用程式](functions-app-settings.md) 設定中設定此環境變數。

### <a name="considerations-for-using-concurrency"></a>使用並行的考慮

PowerShell 預設為 _單一線程_ 指令碼語言。 不過，您可以在相同的進程中使用多個 PowerShell 處理常式來新增平行存取。 建立的空間數量將符合 PSWorkerInProcConcurrencyUpperBound 應用程式設定。 輸送量會受到所選方案中可用的 CPU 和記憶體數量所影響。

Azure PowerShell 使用一些 _進程層級_ 的內容和狀態，以協助您避免過度輸入。 但是，如果您在函式應用程式中開啟平行存取，並叫用變更狀態的動作，最後可能會有競爭情況。 因為一個調用依賴特定狀態，而另一個調用變更了狀態，所以這些競爭條件很難進行調試。

因為某些作業可能需要相當長的時間，所以平行存取與 Azure PowerShell 有巨大的價值。 不過，您必須謹慎進行。 如果您懷疑遇到競爭情形，請將 PSWorkerInProcConcurrencyUpperBound 應用程式設定設為， `1` 而改為針對平行存取使用 [語言工作者進程層級隔離](functions-app-settings.md#functions_worker_process_count) 。

## <a name="configure-function-scriptfile"></a>設定函數 `scriptFile`

根據預設，會從執行的 PowerShell 函式 `run.ps1` ，這是共用與其對應之相同上層目錄的檔案 `function.json` 。

`scriptFile`中的屬性 `function.json` 可用來取得如下列範例所示的資料夾結構：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

在這種情況下，的會 `function.json` 包含屬性，該屬性會參考已匯出的函式 `myFunction` `scriptFile` 來執行。

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>藉由設定 entryPoint 來使用 PowerShell 模組

本文已在範本所產生的預設腳本檔案中顯示 PowerShell 函式 `run.ps1` 。
不過，您也可以在 PowerShell 模組中包含函式。 您可以使用 `scriptFile` `entryPoint` function.json ' 設定檔中的和欄位，參考模組中的特定函式程式碼。

在此情況下， `entryPoint` 是中所參考之 PowerShell 模組中的函式或 Cmdlet 名稱 `scriptFile` 。

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

在此範例中，的設定 `myFunction` 包括 `scriptFile` 參考的屬性 `PSFunction.psm1` ，也就是另一個資料夾中的 PowerShell 模組。  `entryPoint`屬性會參考函式 `Invoke-PSTestFunc` ，這是模組中的進入點。

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

使用此設定時， `Invoke-PSTestFunc` 會完全依照的方式執行 `run.ps1` 。

## <a name="considerations-for-powershell-functions"></a>PowerShell 函式的考慮

當您使用 PowerShell 函式時，請留意下列各節中的考慮事項。

### <a name="cold-start"></a>冷啟動

在 [無伺服器裝載模型](functions-scale.md#consumption-plan)中開發 Azure Functions 時，冷啟動是現實的。 *冷啟動* 是指您的函數應用程式開始執行以處理要求所需的時間。 因為您的函式應用程式會在非使用狀態期間關閉，所以在取用方案中會更頻繁地開始進行。

### <a name="bundle-modules-instead-of-using-install-module"></a>組合模組，而非使用 `Install-Module`

您的腳本會在每次叫用時執行。 避免 `Install-Module` 在您的腳本中使用。 請改 `Save-Module` 為在發行之前使用，讓您的函式不需要浪費時間下載模組。 如果冷啟動影響您的函式，請考慮將您的函數應用程式部署至設定為*always on*或[Premium 方案](functions-scale.md#premium-plan)的[App Service 計畫](functions-scale.md#app-service-plan)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)

[host.json 參考]: functions-host-json.md
