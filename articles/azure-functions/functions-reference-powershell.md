---
title: Azure 函數的 PowerShell 開發人員參考
description: 瞭解如何使用 PowerShell 開發功能。
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276734"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure 函數 PowerShell 開發人員指南

本文詳細介紹了如何使用 PowerShell 編寫 Azure 函數。

PowerShell Azure 函數（函數）表示為 PowerShell 腳本，該腳本在觸發時執行。 每個函數腳本都有一個`function.json`相關檔，用於定義函數的運行方式，例如其觸發方式及其輸入和輸出參數。 要瞭解更多資訊，請參閱[觸發器和綁定文章](functions-triggers-bindings.md)。 

與其他類型的函數一樣，PowerShell 腳本函數採用的參數與`function.json`檔中定義的所有輸入綁定的名稱匹配。 還會`TriggerMetadata`傳遞一個參數，其中包含啟動函數的觸發器的其他資訊。

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)。 您還應完成[PowerShell 的功能快速啟動](functions-create-first-function-powershell.md)，以創建您的第一個 PowerShell 功能。

## <a name="folder-structure"></a>資料夾結構

PowerShell 專案所需的資料夾結構如下所示。 此預設值可以變更。 如需詳細資訊，請參閱下面的 [scriptFile](#configure-function-scriptfile) 一節。

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

在專案的根目錄，有一個共用[`host.json`](functions-host-json.md)檔，可用於配置函數應用。 每個函數都有一個資料夾，其中包含其自己的代碼檔 （.ps1）`function.json`和綁定設定檔 （）。 函數.json 檔的父目錄的名稱始終是函數的名稱。

某些綁定需要檔的存在`extensions.csproj`。 在`extensions.csproj`檔中定義在檔中所需的[2.x 版本和更高版本的](functions-versions.md)函數運行時所需的綁定副檔名，並隨`bin`資料夾中的實際庫檔一起定義。 在本機開發時，您必須[註冊繫結擴充功能](functions-bindings-register.md#extension-bundles)。 開發 Azure 入口網站中的函式時，就會為您完成這項註冊。

在 PowerShell 函數應用中，您可以選擇在函數應用`profile.ps1`開始運行時運行的 ，否則稱為*[冷啟動](#cold-start)*。 有關詳細資訊，請參閱[PowerShell 設定檔](#powershell-profile)。

## <a name="defining-a-powershell-script-as-a-function"></a>將 PowerShell 腳本定義為函數

根據預設，Functions 執行階段會在 `run.ps1` 中尋找您的函式，其中 `run.ps1` 與對應的 `function.json` 會共用相同的父目錄。

腳本在執行時傳遞了許多參數。 要處理這些參數，請向腳本`param`頂部添加一個塊，如以下示例所示：

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>觸發中繼資料參數

該`TriggerMetadata`參數用於提供有關觸發器的其他資訊。 其他中繼資料因綁定而異，但它們都包含包含以下資料`sys`的屬性：

```powershell
$TriggerMetadata.sys
```

| 屬性   | 描述                                     | 類型     |
|------------|-------------------------------------------------|----------|
| UtcNow     | 在 UTC 中觸發函數時        | Datetime |
| MethodName | 觸發的函數的名稱     | 字串   |
| 蘭德吉德   | 函數的此執行的唯一 guid | 字串   |

每個觸發器類型都有一組不同的中繼資料。 `$TriggerMetadata`例如，`QueueTrigger`的 包含`InsertionTime`， `Id` `DequeueCount`、 、 等。 有關佇列觸發器中繼資料的詳細資訊，請訪問[佇列觸發器的官方文檔](functions-bindings-storage-queue-trigger.md#message-metadata)。 檢查您正在使用[的觸發器](functions-triggers-bindings.md)的文檔，查看觸發器中繼資料中的內容。

## <a name="bindings"></a>繫結

在 PowerShell 中，[綁定](functions-triggers-bindings.md)在函數的函數中配置和定義。 Functions 會透過數種方式與繫結互動。

### <a name="reading-trigger-and-input-data"></a>讀取觸發程序和輸入資料

觸發器和輸入綁定作為傳遞給函數的參數讀取。 輸入綁定具有函數`direction``in`.json 中的集。 中`name``function.json`定義的屬性是`param`塊中參數的名稱。 由於 PowerShell 使用具名引數進行綁定，因此參數的順序並不重要。 但是，最佳做法是遵循 中定義的綁定的順序`function.json`。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>寫入輸出資料

在函數中，輸出綁定在`direction`函數.json 中具有一`out`個集。 您可以使用`Push-OutputBinding`Cmdlet 寫入輸出綁定，Cmdlet 可用於函數運行時。 `name`在所有情況下，`function.json`中定義的綁定的屬性對應于`Name``Push-OutputBinding`Cmdlet 的參數。

下面顯示了如何在函數腳本中`Push-OutputBinding`調用：

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

您還可以通過管道傳遞特定綁定的值。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`根據為`-Name`指定的值，行為方式不同：

* 當指定的名稱無法解析為有效的輸出綁定時，則引發錯誤。

* 當輸出綁定接受值集合時，可以重複調用`Push-OutputBinding`以推送多個值。

* 當輸出綁定僅接受單例值時，調用`Push-OutputBinding`第二次會產生錯誤。

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` 語法

以下是用於調用`Push-OutputBinding`的有效參數：

| 名稱 | 類型 | 位置 | 描述 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 要設置的輸出綁定的名稱。 |
| **`-Value`** | Object | 2 | 要設置的輸出綁定的值，從管道 ByValue 接受。 |
| **`-Clobber`** | SwitchParameter | 已命名 | （可選）指定時，強制為指定的輸出綁定設置值。 | 

還支援以下常見參數： 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

有關詳細資訊，請參閱[有關通用參數](https://go.microsoft.com/fwlink/?LinkID=113216)。

#### <a name="push-outputbinding-example-http-responses"></a>推送輸出綁定示例：HTTP 回應

HTTP 觸發器使用名為 的`response`輸出綁定返回回應。 在下面的示例中，的`response`輸出綁定具有"輸出#1"的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

由於輸出是 HTTP（僅接受單例值），因此在調用第二次時`Push-OutputBinding`引發錯誤。

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

對於僅接受單例值的輸出，可以使用 參數`-Clobber`覆蓋舊值，而不是嘗試添加到集合中。 下面的示例假定您已經添加了一個值。 通過使用`-Clobber`，以下示例中的回應將覆蓋現有值以返回"輸出#3"的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>推送輸出綁定示例：佇列輸出綁定

`Push-OutputBinding`用於將資料發送到輸出綁定，如[Azure 佇列存儲輸出綁定](functions-bindings-storage-queue-output.md)。 在下面的示例中，寫入佇列的消息的值是"輸出#1"：

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

存儲佇列的輸出綁定接受多個輸出值。 在這種情況下，在第一個寫入佇列後調用以下示例，一個清單具有兩個專案："輸出#1"和"輸出#2"。

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

以下示例在前兩個調用後再向輸出集合添加兩個值：

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

寫入佇列時，消息包含以下四個值："輸出#1"、"輸出#2"、"輸出#3"和"輸出#4"。

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` Cmdlet

可以使用`Get-OutputBinding`Cmdlet 檢索當前為輸出綁定設置的值。 此 Cmdlet 檢索包含輸出綁定的名稱及其相應值的雜湊表。 

以下是用於`Get-OutputBinding`返回當前綁定值的示例：

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`還包含一個參數，`-Name`稱為 ，可用於篩選返回的綁定，如以下示例所示：

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

中支援萬用字元`Get-OutputBinding`（*）

## <a name="logging"></a>記錄

在 PowerShell 中登錄功能的工作方式類似于常規 PowerShell 日誌記錄。 您可以使用日誌記錄 Cmdlet 寫入每個輸出流。 每個 Cmdlet 映射到函數使用的日誌級別。

| 函數日誌記錄級別 | 日誌記錄 Cmdlet |
| ------------- | -------------- |
| 錯誤 | **`Write-Error`** |
| 警告 | **`Write-Warning`**  | 
| 資訊 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | 資訊 | 寫入_資訊_級別日誌記錄。 |
| 偵錯 | **`Write-Debug`** |
| 追蹤 | **`Write-Progress`** <br /> **`Write-Verbose`** |

除了這些 Cmdlet 之外，寫入管道的任何內容都重定向到`Information`日誌級別，並顯示預設的 PowerShell 格式。

> [!IMPORTANT]
> 使用`Write-Verbose`或`Write-Debug`Cmdlet 不足以查看詳細和調試級別日誌記錄。 您還必須配置日誌級別閾值，該閾值聲明您真正關心的日誌級別。 要瞭解更多資訊，請參閱[配置函數應用日誌級別](#configure-the-function-app-log-level)。

### <a name="configure-the-function-app-log-level"></a>配置功能應用日誌級別

Azure 函數允許您定義閾值級別，以便輕鬆控制函數寫入日誌的方式。 要設置寫入主控台的所有跟蹤的閾值，請使用`logging.logLevel.default`[`host.json`檔][host.json 引用]中的屬性。 這個設定會套用到函式應用程式中的所有函式。

以下示例設置閾值以為所有函數啟用詳細日誌記錄，但設置閾值以啟用名為 的`MyFunction`函數的調試日誌記錄：

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

### <a name="viewing-the-logs"></a>查看日誌

如果函數應用在 Azure 中運行，則可以使用應用程式見解來監視它。 參閱[監視 Azure Functions](functions-monitoring.md) 深入了解如何檢視和查詢函式記錄。

如果您在本地運行函數應用以進行開發，則預設登錄到檔案系統。 要查看主控台中的日誌，請將其`AZURE_FUNCTIONS_ENVIRONMENT`環境變數設置為啟動函數應用`Development`之前。

## <a name="triggers-and-bindings-types"></a>觸發器和綁定類型

有許多觸發器和綁定可供您用於函數應用。 觸發器和綁定的完整清單[可以在此處找到](functions-triggers-bindings.md#supported-bindings)。

所有觸發器和綁定在代碼中都表示為幾個實際資料類型：

* 雜湊表
* 字串
* byte[]
* int
* double
* HttpRequestCoNtext
* Http回應上下文

此清單中的前五種類型是標準 .NET 類型。 最後兩個僅由[HttpTrigger 觸發器](#http-triggers-and-bindings)使用。

函數中的每個綁定參數都必須是這些類型的一個。

### <a name="http-triggers-and-bindings"></a>HTTP 觸發程序和繫結

HTTP 和 Webhook 觸發程序以及 HTTP 輸出繫結會使用要求和回應物件來代表 HTTP 傳訊。

#### <a name="request-object"></a>要求物件

傳遞到腳本的請求物件的類型為`HttpRequestContext`，具有以下屬性：

| 屬性  | 描述                                                    | 類型                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 包含要求本文的物件。 `Body`根據資料序列化為最佳類型。 例如，如果資料為 JSON，則資料將作為雜湊表傳入。 如果資料是字串，則以字串傳入。 | 物件 (object) |
| **`Headers`** | 包含請求標頭的字典。                | 字典<字串，字串><sup>*</sup> |
| **`Method`** | 要求的 HTTP 方法。                                | 字串                    |
| **`Params`**  | 包含要求之路由傳送參數的物件。 | 字典<字串，字串><sup>*</sup> |
| **`Query`** | 包含查詢參數的物件。                  | 字典<字串，字串><sup>*</sup> |
| **`Url`** | 要求的 URL。                                        | 字串                    |

<sup>*</sup>所有`Dictionary<string,string>`鍵都不區分大小寫。

#### <a name="response-object"></a>回應物件

應發送回的回應物件的類型為`HttpResponseContext`，具有以下屬性：

| 屬性      | 描述                                                 | 類型                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 包含回應本文的物件。           | 物件 (object)                    |
| **`ContentType`** | 用於設置回應的內容類型的短手。 | 字串                    |
| **`Headers`** | 包含回應標頭的物件。               | 字典或雜湊表   |
| **`StatusCode`**  | 回應的 HTTP 狀態碼。                       | 字串或整數             |

#### <a name="accessing-the-request-and-response"></a>存取要求和回應

使用 HTTP 觸發器時，可以使用任何其他輸入綁定訪問 HTTP 要求。 它在街區裡`param`

使用`HttpResponseContext`物件返回回應，如下所示：

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

調用此函數的結果是：

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>觸發器和綁定的類型轉換

對於某些綁定（如 blob 綁定），您可以指定參數的類型。

例如，要將 Blob 存儲中的資料作為字串提供，將以下類型強制轉換添加到我的`param`塊：

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 設定檔

在 PowerShell 中，有 PowerShell 設定檔的概念。 如果您不熟悉 PowerShell 設定檔，請參閱[關於設定檔](/powershell/module/microsoft.powershell.core/about/about_profiles)。

在 PowerShell 函數中，設定檔腳本在函數應用啟動時執行。 函數應用在首次部署時和閒置後啟動（[冷啟動](#cold-start)）。

使用視覺化工作室代碼和 Azure 函數核心工具等工具創建函數應用時，將為您創建預設值`profile.ps1`。 預設設定檔維護[在核心工具 GitHub 存儲庫上](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1)，並包含：

* 自動對 Azure 進行 MSI 身份驗證。
* 如果需要，可以打開 Azure PowerShell `AzureRM` PowerShell 別名。

## <a name="powershell-version"></a>PowerShell 版本

下表顯示了函數運行時的每個主要版本使用的 PowerShell 版本：

| Functions 版本 | PowerShell 版本                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1（由運行時鎖定） |
| 2.x               | 電源外殼核心 6                              |

您可以通過從任何函數列印`$PSVersionTable`來查看當前版本。

## <a name="dependency-management"></a>相依性管理

函數允許您利用[PowerShell 庫](https://www.powershellgallery.com)來管理依賴項。 啟用依賴項管理後，要求.psd1 檔用於自動下載所需的模組。 通過將`managedDependency`屬性設置為[在 host.json 檔的](functions-host-json.md)根`true`目錄中來啟用此行為，如以下示例所示：

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

創建新的 PowerShell 函數專案時，預設情況下啟用依賴項管理，並附帶 Azure[`Az`模組](/powershell/azure/new-azureps-module-az)。 當前支援的最大模組數為 10。 支援的語法是_`MajorNumber`_`.*`或精確的模組版本，如以下要求所示。

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

更新要求.psd1 檔時，重新開機後將安裝更新的模組。

> [!NOTE]
> 託管依賴項需要訪問www.powershellgallery.com才能下載模組。 在本地運行時，請確保運行時可以通過添加任何必需的防火牆規則訪問此 URL。 

以下應用程式設定可用於更改託管依賴項的下載和安裝方式。 你的應用升級將在`MDMaxBackgroundUpgradePeriod`中開始，升級過程大約在 內`MDNewSnapshotCheckPeriod`完成。

| 功能應用設置              | 預設值             | 描述                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`（7 天）     | 每個 PowerShell 工作進程都會在進程啟動時以及`MDMaxBackgroundUpgradePeriod`之後開始檢查 PowerShell 庫中的模組升級。 當 PowerShell 庫中有新的模組版本時，它已安裝到檔案系統中，並提供給 PowerShell 工作人員。 減小此值可使函數應用更快地獲得較新的模組版本，但也會增加應用資源使用率（網路 I/O、CPU、存儲）。 增加此值會降低應用的資源使用量，但也可能延遲向應用交付新的模組版本。 | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`（1 小時）       | 將新的模組版本安裝到檔案系統後，必須重新開機每個 PowerShell 工作進程。 重新開機 PowerShell 工作人員會影回應用的可用性，因為它可能會中斷當前功能執行。 在重新開機所有 PowerShell 工作進程之前，函式呼叫可以使用舊模組版本或新模組版本。 重新開機在 中`MDNewSnapshotCheckPeriod`完成的所有 PowerShell 工作人員。 增加此值可降低中斷的頻率，但也可能增加函式呼叫在非確定性上使用舊模組或新模組版本時的時間段。 |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`（1 天）     | 為了避免頻繁重新開機輔助角色時過度升級模組，當任何工作人員已啟動上次`MDMinBackgroundUpgradePeriod`簽入時，不會執行模組升級檢查。 |

利用自己的自訂模組與正常操作的方式略有不同。

在本地電腦上，模組將安裝在 中的`$env:PSModulePath`全域可用資料夾中之一。 在 Azure 中運行時，您無法訪問電腦上安裝的模組。 這意味著`$env:PSModulePath`PowerShell 函數應用與`$env:PSModulePath`常規 PowerShell 腳本不同。

在函數中`PSModulePath`，包含兩個路徑：

* 功能`Modules`應用根目錄處存在的資料夾。
* 由 PowerShell`Modules`語言工作人員控制的資料夾的路徑。

### <a name="function-app-level-modules-folder"></a>功能應用級`Modules`資料夾

要使用自訂模組，可以將函數所依賴的模組放在`Modules`資料夾中。 在此資料夾中，模組自動可用於函數運行時。 函數應用中的任何函數都可以使用這些模組。 

> [!NOTE]
> 在要求.psd1 檔中指定的模組會自動下載並包含在路徑中，因此您無需將它們包含在模組資料夾中。 當在雲中運行時，`$env:LOCALAPPDATA/AzureFunctions``/data/ManagedDependencies`這些存儲在本地資料夾中和資料夾中。

要利用自訂模組功能，請在函數應用的`Modules`根目錄中創建一個資料夾。 將函數中要使用的模組複製到此位置。

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

使用`Modules`資料夾時，函數應用應具有以下資料夾結構：

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

啟動函數應用時，PowerShell 語言工作人員會將此`Modules`資料夾添加到 中`$env:PSModulePath`，以便您可以像在常規 PowerShell 腳本中一樣相依模組自動載入。

### <a name="language-worker-level-modules-folder"></a>語言工作者級別`Modules`資料夾

PowerShell 語言工作人員通常使用多個模組。 這些模組是在 的最後位置`PSModulePath`定義的。 

當前模組清單如下：

* [微軟.PowerShell.存檔](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive)：用於處理存檔的模組，如`.zip`，`.nupkg`等。
* **執行緒作業**：基於執行緒的 PowerShell 作業 API 的實現。

預設情況下，函數使用這些模組的最新版本。 要使用特定的模組版本，請將該特定版本放在函數`Modules`應用的資料夾中。

## <a name="environment-variables"></a>環境變數

在 Functions 中，[應用程式設定](functions-app-settings.md) (例如服務連接字串) 在執行期間會公開為環境變數。 您可以使用 訪問這些設置，`$env:NAME_OF_ENV_VAR`如以下示例所示：

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

在本機執行時，應用程式設定會讀取自 [local.settings.json](functions-run-local.md#local-settings-file) 專案檔。

## <a name="concurrency"></a>並行

預設情況下，函數 PowerShell 運行時一次只能處理一個函數的調用。 但是，在以下情況下，此併發級別可能不夠：

* 當您嘗試同時處理大量調用時。
* 當您有調用同一函數應用內其他函數的函數時。

通過將以下環境變數設置為整數值，可以更改此行為：

```
PSWorkerInProcConcurrencyUpperBound
```

在函數應用[的應用設置](functions-app-settings.md)中設置此環境變數。

### <a name="considerations-for-using-concurrency"></a>使用併發的注意事項

預設情況下，PowerShell 是_一種執行緒_指令碼語言。 但是，可以通過在同一進程中使用多個 PowerShell 運行空間來添加併發。 創建的運行空間量將與 PSWorkerInConcConcConinins 上繫結應用程式設置匹配。 輸送量將受到所選計畫中可用的 CPU 和記憶體量的影響。

Azure PowerShell 使用某些_進程級_上下文和狀態來説明您避免過度鍵入。 但是，如果在函數應用中打開併發性並調用更改狀態的操作，則最終可能會使用競爭條件。 這些競爭條件很難調試，因為一個調用依賴于特定狀態，另一個調用更改了狀態。

與 Azure PowerShell 併發性具有巨大的價值，因為某些操作可能需要相當長的時間。 但是，您必須謹慎行事。 如果您懷疑您遇到競爭條件，將 PSWorkerInConcConininsUpperBound 應用設置設置為`1`，而是使用[語言輔助進程級別隔離](functions-app-settings.md#functions_worker_process_count)進行併發。

## <a name="configure-function-scriptfile"></a>配置功能`scriptFile`

預設情況下，PowerShell 函數從`run.ps1`執行，該檔與其對應`function.json`的共用同一父目錄。

中的`scriptFile``function.json`屬性可用於獲取如下所示的資料夾結構：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

在這種情況下， for`function.json``myFunction`包括一個`scriptFile`屬性，該屬性引用要運行的匯出函數的檔。

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>通過配置進入點使用 PowerShell 模組

本文在範本生成的預設`run.ps1`指令檔中顯示了 PowerShell 函數。
但是，您還可以在 PowerShell 模組中包括您的功能。 您可以使用 函數.json 的設定檔中的`scriptFile`和`entryPoint`欄位來引用模組中的特定函數代碼。

在這種情況下，`entryPoint`是 中`scriptFile`引用的 PowerShell 模組中的函數或 Cmdlet 的名稱。

請考慮以下資料夾結構：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

其中`PSFunction.psm1`包含：

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

在此示例中，的`myFunction`配置包括引用`scriptFile``PSFunction.psm1`的屬性，該屬性是另一個資料夾中的 PowerShell 模組。  屬性`entryPoint`引用函數`Invoke-PSTestFunc`，該函數是模組中的進入點。

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

使用此配置，`Invoke-PSTestFunc`將完全按照 將`run.ps1`執行。

## <a name="considerations-for-powershell-functions"></a>PowerShell 功能注意事項

使用 PowerShell 函數時，請注意以下各節中的注意事項。

### <a name="cold-start"></a>冷啟動

在[無伺服器託管模型中](functions-scale.md#consumption-plan)開發 Azure 函數時，冷啟動是一個現實。 *冷啟動*是指函數應用開始運行以處理請求所需的時間。 冷啟動在消耗計畫中更頻繁地發生，因為函數應用在非活動期間關閉。

### <a name="bundle-modules-instead-of-using-install-module"></a>捆綁模組，而不是使用`Install-Module`

每次調用時都會運行腳本。 避免`Install-Module`在腳本中使用。 而是在發佈`Save-Module`之前使用，這樣您的功能就不必浪費時間下載模組。 如果冷啟動會影響您的功能，請考慮將函數應用部署到設置為*始終打開*或部署到[高級計畫](functions-scale.md#premium-plan)[的應用服務](functions-scale.md#app-service-plan)計畫。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure 函數觸發器和綁定](functions-triggers-bindings.md)

[host.json 參考]: functions-host-json.md
