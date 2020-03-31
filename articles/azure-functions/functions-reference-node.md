---
title: Azure 函數的 JavaScript 開發人員引用
description: 了解如何使用 JavaScript 開發函式。
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 12/17/2019
ms.openlocfilehash: 345df8e1ea88caa6f8dbe941245c1f989c3e81c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276825"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 開發人員指南

本指南包含使用 JavaScript 撰寫 Azure Functions 的複雜性相關資訊。

JavaScript 函式是匯出的 `function`，會在觸發時執行 ([觸發程序是在 function.json 中設定](functions-triggers-bindings.md))。 傳遞給每個函數的第一個參數是一`context`個物件，用於接收和發送綁定資料、日誌記錄和與運行時通信。

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)。 使用[Visual Studio 代碼](functions-create-first-function-vs-code.md)或在[門戶中](functions-create-first-azure-function.md)完成函數快速入門以創建第一個函數。

本文還支援[TypeScript 應用程式開發](#typescript)。

## <a name="folder-structure"></a>資料夾結構

JavaScript 專案的必要資料夾結構如下所示。 此預設值可以變更。 如需詳細資訊，請參閱下面的 [scriptFile](#using-scriptfile) 一節。

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

在專案根目錄中，有共用的 [host.json](functions-host-json.md) 檔案可用來設定函式應用程式。 每個函式都有本身程式碼檔案 (.js) 和繫結設定檔 (function.json) 的資料夾。 `function.json` 的父目錄名稱一律是函式的名稱。

在函式執行階段的[版本 2.x](functions-versions.md) 中所需的繫結擴充功能，是以 `bin` 資料夾中的實際程式庫檔案在 `extensions.csproj` 檔案中所定義。 在本機開發時，您必須[註冊繫結擴充功能](./functions-bindings-register.md#extension-bundles)。 開發 Azure 入口網站中的函式時，就會為您完成這項註冊。

## <a name="exporting-a-function"></a>匯出函數

JavaScript 函數必須通過[`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports)（或[`exports`](https://nodejs.org/api/modules.html#modules_exports)） 匯出。 您匯出的函式應該是可經觸發而執行的 JavaScript 函式。

根據預設，Functions 執行階段會在 `index.js` 中尋找您的函式，其中 `index.js` 與對應的 `function.json` 會共用相同的父目錄。 在預設情況中，您匯出的函式應該是僅來自其檔案的匯出，或是名為 `run` 或 `index` 的匯出。 若要設定檔案位置，並匯出函式的名稱，請參閱以下的[設定您的函式進入點](functions-reference-node.md#configure-function-entry-point)。

您匯出的函式在執行時，會傳入多個引數。 它所採用的第一個引數一律為 `context` 物件。 如果函數是同步的（不返回承諾），則必須傳遞`context`物件，因為需要調用`context.done`才能正確使用。

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>匯出非同步函式
在函數運行時[`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function)的版本 2.x 中使用聲明或純 JavaScript[承諾](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)時，您無需顯式調用[`context.done`](#contextdone-method)回檔以發出函數已完成的信號。 在匯出的非同步函式/Promise 完成時，函式便會完成。 對於面向版本 1.x 運行時的函數，您仍必須在[`context.done`](#contextdone-method)執行完代碼後調用。

下列範例說明的簡單函式會記錄其已遭到觸發，並立即完成執行。

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

在匯出非同步函式時，您也可以將輸出繫結設定為採用 `return` 值。 如果您只有一個輸出繫結，建議使用此方式。

若要使用 `return` 來指派輸出，請在 `function.json` 中將 `name` 屬性變更為 `$return`。

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

在此情況下，您的函式會如下列範例所示：

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>繫結 
在 JavaScript 中，[繫結](functions-triggers-bindings.md)會設定並定義於函式的 function.json 中。 Functions 會透過數種方式與繫結互動。

### <a name="inputs"></a>輸入
在 Azure Functions 中輸入會分成兩個類別：一個是觸發程序輸入，另一個是額外的輸入。 函式可透過三種方式讀取觸發程序和其他輸入繫結 (`direction === "in"` 的繫結)：
 - **_[建議]_ 作為參數傳至您的函式。** 這些繫結會按照在 *function.json* 中定義的順序傳遞至函式。 在`name`*函數.json*中定義的屬性不需要匹配參數的名稱，儘管它應該匹配。
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **作為 [`context.bindings`](#contextbindings-property) 物件的成員。** 每個成員都會由 *function.json* 中定義的 `name` 屬性命名。
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **使用 JavaScript [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) 物件作為輸入。** 這基本上相當於將輸入傳入作為參數，但可讓您以動態方式處理輸入。
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>輸出
函式可透過數種方式寫入輸出 (`direction === "out"` 的繫結)。 在所有情況下，在 *function.json* 中為繫結定義的 `name` 屬性都會對應至在您的函式中寫入的物件成員名稱。 

您可以通過以下方法之一將資料分配給輸出綁定（不要組合這些方法）：

- **_[建議用於多個輸出]_ 傳回物件。** 如果使用非同步/承諾返回函數，則可以返回具有分配輸出資料的物件。 在下列範例中，輸出繫結在 *function.json* 中會命名為 "httpResponse" 和 "queueOutput"。

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  如果使用同步函數，則可以使用[`context.done`](#contextdone-method)返回此物件（請參閱示例）。
- **_[建議用於單一輸出]_ 直接傳回值並使用 $return 繫結名稱。** 這僅適用於非同步/Promise 傳回函式。 請參閱[匯出非同步函式](#exporting-an-async-function)中的範例。 
- **將值指派給 `context.bindings`** 您可以直接將值指派給 context.bindings。

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>繫結資料類型

若要定義輸入繫結的資料類型，請使用繫結定義中的 `dataType` 屬性。 例如，若要以二進位格式讀取 HTTP 要求的內容，請使用類型 `binary`：

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` 的選項是：`binary`、`stream` 和 `string`。

## <a name="context-object"></a>context 物件
執行階段使用 `context` 物件來將資料傳遞至函式並從中傳出，而且可讓您與執行階段進行通訊。 如果您匯出的函式是同步的，內容物件可用來讀取和設定繫結中的資料、寫入記錄，以及使用 `context.done` 回呼。

`context` 物件一律為函式的第一個參數。 它具有重要的方法 (像是 `context.done` 和 `context.log`)，因此應納入。 您可以任意方式命名物件 (例如 `ctx` 或 `c`)。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindings 屬性

```js
context.bindings
```

返回用於讀取或分配綁定資料的命名物件。 可以通過讀取 上的屬性`context.bindings`來訪問輸入和觸發器綁定資料。 輸出綁定資料可以通過將資料添加到`context.bindings`

例如，function.json 中的下列繫結定義可讓您使用 `context.bindings.myOutput` 從 `context.bindings.myInput` 存取佇列的內容並且將輸出指派到佇列。

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

您可以使用 `context.done` 方法選擇定義輸出繫結資料，而不使用 `context.binding` 物件 (如下所示)。

### <a name="contextbindingdata-property"></a>context.bindingData 屬性

```js
context.bindingData
```

傳回包含觸發程序中繼資料和函式引動過程資料的具名物件 (`invocationId`、`sys.methodName`、`sys.utcNow`、`sys.randGuid`)。 如需觸發程序中繼資料的範例，請參閱此[事件中樞範例](functions-bindings-event-hubs-trigger.md)。

### <a name="contextdone-method"></a>context.done 方法

```js
context.done([err],[propertyBag])
```

通知執行階段您的程式碼已完成。 當函數使用 聲明[`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function)時，不需要使用`context.done()`。 隱含地呼叫 `context.done` 回呼。 非同步函式可在 Node 8 或更新版本中使用，而這需要 2.x 版的 Functions 執行階段。

如果函數不是非同步函數，**則必須調用**`context.done`以通知運行時函數已完成。 如果沒有，執行將會逾時。

`context.done` 方法可讓您將使用者定義的錯誤傳回到執行階段，並傳回包含輸出繫結資料的 JSON 物件。 傳至 `context.done` 的屬性會覆寫 `context.bindings` 物件上設定的任何屬性。

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log 方法  

```js
context.log(message)
```

可讓您寫入預設追蹤層級的資料流函式記錄。 `context.log` 上有其他可用的記錄方法，可讓您在其他追蹤層級寫入函式記錄︰


| 方法                 | 描述                                |
| ---------------------- | ------------------------------------------ |
| **錯誤（_消息_）**   | 寫入錯誤層級或更低層級的記錄。   |
| **warn(_message_)**    | 寫入警告層級或更低層級的記錄。 |
| **info(_message_)**    | 寫入資訊層級或更低層級的記錄。    |
| **verbose(_message_)** | 寫入詳細資訊層級記錄。           |

下列範例會依警告追蹤層級寫入記錄︰

```javascript
context.log.warn("Something has happened."); 
```

您可以在 host.json 檔案中[設定用於記錄的追蹤層級閾值](#configure-the-trace-level-for-console-logging)。 如需寫入記錄的詳細資訊，請參閱以下的[寫入追蹤輸出](#writing-trace-output-to-the-console)。

參閱[監視 Azure Functions](functions-monitoring.md) 深入了解如何檢視和查詢函式記錄。

## <a name="writing-trace-output-to-the-console"></a>將追蹤輸出寫入主控台中 

在 Functions 中，您可以使用 `context.log` 方法，將追蹤輸出寫入主控台中。 在 Functions v2.x 中，會在函式應用程式層級擷取使用 `console.log` 的追蹤輸出。 這意味著 的`console.log`輸出不綁定到特定的函式呼叫，並且不顯示在特定函數的日誌中。 不過，這些輸出會傳播至 Application Insights。 在 Functions v1.x 中，您不能使用 `console.log` 來寫入主控台中。

當您呼叫 `context.log()` 時，會在預設追蹤層級 (也就是「資訊」__ 追蹤層級) 將您的訊息寫入主控台中。 下列程式碼會依資訊追蹤層級寫入主控台中︰

```javascript
context.log({hello: 'world'});  
```

此程式碼相當於上述程式碼：

```javascript
context.log.info({hello: 'world'});  
```

此程式碼會依錯誤層級寫入主控台中︰

```javascript
context.log.error("An error has occurred.");  
```

因為 _error_ 是最高追蹤層級，所以只要啟用記錄，這項追蹤就會寫入所有追蹤層級的輸出。

所有 `context.log` 方法都與 Node.js [util.format 方法 (英文)](https://nodejs.org/api/util.html#util_util_format_format) 支援相同的參數格式。 請考慮下列程式碼，它會使用預設追蹤層級寫入函式記錄︰

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

您也能以下列格式撰寫相同的程式碼：

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>設定用於主控台記錄的追蹤層級

Functions 1.x 讓您定義寫入至主控台的閾值追蹤層級，這可讓您輕鬆地控制追蹤從函式寫入主控台的方式。 若要設定寫入至主控台之所有追蹤的閾值，請使用 host.json 檔案中的 `tracing.consoleLevel` 屬性。 這個設定會套用到函式應用程式中的所有函式。 下列範例會設定追蹤閾值來啟用詳細資訊記錄︰

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

**consoleLevel** 的值對應至 `context.log` 方法的名稱。 若要停用主控台的所有追蹤記錄，請將 **consoleLevel** 設為 _off_。 如需詳細資訊，請參閱 [host.json 參考](functions-host-json-v1.md)。

## <a name="http-triggers-and-bindings"></a>HTTP 觸發程序和繫結

HTTP 和 Webhook 觸發程序以及 HTTP 輸出繫結會使用要求和回應物件來代表 HTTP 傳訊。  

### <a name="request-object"></a>要求物件

`context.req` (要求) 物件具有下列屬性：

| 屬性      | 描述                                                    |
| ------------- | -------------------------------------------------------------- |
| _身體_        | 包含要求本文的物件。               |
| _頭_     | 包含要求標頭的物件。                   |
| _方法_      | 要求的 HTTP 方法。                                |
| _originalUrl_ | 要求的 URL。                                        |
| _params_      | 包含要求之路由傳送參數的物件。 |
| _查詢_       | 包含查詢參數的物件。                  |
| _rawBody_     | 字串格式的訊息內文。                           |


### <a name="response-object"></a>回應物件

`context.res` (回應) 物件具有下列屬性：

| 屬性  | 描述                                               |
| --------- | --------------------------------------------------------- |
| _身體_    | 包含回應本文的物件。         |
| _頭_ | 包含回應標頭的物件。             |
| _isRaw_   | 表示略過回應的格式。    |
| _狀態_  | 回應的 HTTP 狀態碼。                     |
| _餅乾_ | 在回應中設置的 HTTP Cookie 物件陣列。 HTTP Cookie 物件具有`name` `value`、 和其他 Cookie 屬性，`maxAge`如`sameSite`或 。 |

### <a name="accessing-the-request-and-response"></a>存取要求和回應 

使用 HTTP 觸發程序時，您可以使用許多方式來存取 HTTP 要求和回應物件︰

+ **和`req``res``context`物件上的屬性。** 如此一來，您可以使用傳統模式來存取內容物件中的 HTTP 資料，而不需使用完整 `context.bindings.name` 模式。 下列範例示範如何存取 `context` 上的 `req` 和 `res` 物件：

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **從具名輸入和輸出繫結。** 如此一來，HTTP 觸發程序和繫結的運作方式會與任何其他繫結相同。 下列範例會使用具名 `response` 繫結來設定回應物件： 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[僅回應]_ 通過調用`context.res.send(body?: any)`。** HTTP 回應是以做為回應主體的輸入 `body` 所建立。 隱含地呼叫 `context.done()`。

+ **_[僅回應]_ 通過調用`context.done()`。** 特殊類型的 HTTP 綁定返回傳遞給方法的`context.done()`回應。 下列 HTTP 輸出繫結定義 `$return` 輸出參數︰

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>縮放和併發

預設情況下，Azure 函數會自動監視應用程式的負載，並根據需要為 Node.js 創建其他主機實例。 函數使用不同觸發器類型的內置（不可使用者可配置）閾值來確定何時添加實例，例如佇列觸發器的消息和佇列大小。 有關詳細資訊，請參閱[消費和高級計畫的工作原理](functions-scale.md#how-the-consumption-and-premium-plans-work)。

此縮放行為對於許多 Node.js 應用程式來說就足夠了。 對於 CPU 綁定的應用程式，可以使用多語言輔助進程進一步提高性能。

預設情況下，每個函數主機實例都有一個語言輔助進程。 通過使用[FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count)應用程式設定，可以增加每個主機的工作進程數（最多 10 個）。 然後，Azure 函數嘗試在這些輔助人員之間均勻地分配同時調用函數。 

FUNCTIONS_WORKER_PROCESS_COUNT適用于函數在擴展應用程式以滿足需求時創建的每個主機。 

## <a name="node-version"></a>節點版本

下表按作業系統顯示函數運行時每個主要版本的當前支援的 Node.js 版本：

| Functions 版本 | 節點版本（視窗） | 節點版本（Linux） |
|---|---| --- |
| 1.x | 6.11.2 (由執行階段鎖定) | n/a |
| 2.x  | +8<br/>+10 （推薦）<br/>Φ12<sup>*</sup> | +8 （推薦）<br/>Φ10  |
| 3.x | Φ10<br/>+12 （推薦）  | Φ10<br/>+12 （推薦） |

<sup>*</sup>節點 #12 當前允許在函數運行時的 2.x 版本上。 但是，為了獲得最佳性能，我們建議將函數執行階段版本 3.x 與 Node #12 一起使用。 

您可以藉由檢查以上的應用程式設定，或藉由從任何函式列印 `process.version`，來查看執行階段目前正在使用的版本。 通過將WEBSITE_NODE_DEFAULT_VERSION[應用設置](functions-how-to-use-azure-function-app-settings.md#settings)設置為受支援的 LTS 版本（如`~10`）來定位 Azure 中的版本。

## <a name="dependency-management"></a>相依性管理
若要使用 JavaScript 程式碼中的社群程式庫，如下列範例所示，您必須確定已在 Azure 中的函數應用程式上安裝所有的相依性。

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> 您應該定義函式應用程式根目錄的 `package.json` 檔案。 定義檔案可讓應用程式中的所有函式共用相同的快取套件，以提供最佳效能。 發生版本衝突時，您可以在特定函式的資料夾中新增 `package.json` 檔案來解決它。  

從原始檔控制部署函式應用程式時，存在於存放庫中的任何 `package.json` 檔案，都會在部署期間觸發其資料夾中的 `npm install`。 但是，在透過入口網站或 CLI 部署時，您就必須手動安裝套件。

有兩種方式可在您的函數應用程式上安裝套件： 

### <a name="deploying-with-dependencies"></a>使用相依性進行部署
1. 執行 `npm install` 在本機安裝所有必要的套件。

2. 部署您的程式碼，並確定 `node_modules` 資料夾包含在部署中。 


### <a name="using-kudu"></a>使用 Kudu
1. 移至 `https://<function_app_name>.scm.azurewebsites.net`。

2. 按一下**調試主控台** > **CMD**。

3. 移至 `D:\home\site\wwwroot`，然後將 package.json 檔案拖曳至頁面上半部的 **wwwroot** 資料夾。  
    您也可以使用其他方法將檔案上傳至函數應用程式。 如需詳細資訊，請參閱[如何更新函式應用程式檔案](functions-reference.md#fileupdate)。 

4. 上傳 package.json 檔案之後，請在 **Kudu 遠端執行主控台**中執行 `npm install` 命令。  
    此動作會下載 package.json 檔案中指出的套件，並重新啟動函數應用程式。

## <a name="environment-variables"></a>環境變數

在 Functions 中，[應用程式設定](functions-app-settings.md) (例如服務連接字串) 在執行期間會公開為環境變數。 您可以使用 訪問這些設置，`process.env`如此處所示，我們記錄`context.log()``AzureWebJobsStorage``WEBSITE_SITE_NAME`和環境變數的位置：

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

在本機執行時，應用程式設定會讀取自 [local.settings.json](functions-run-local.md#local-settings-file) 專案檔。

## <a name="configure-function-entry-point"></a>設定函式進入點

`function.json` 屬性 `scriptFile` 和 `entryPoint` 可用來對於匯出的函式設定位置和名稱。 如果已轉換 JavaScript，這些屬性就可能有其重要性。

### <a name="using-scriptfile"></a>使用 `scriptFile`

預設會從 `index.js` 執行 JavaScript 函式，這是與對應的 `function.json` 共用相同父目錄的檔案。

`scriptFile` 可用來取得如下列範例所示的資料夾結構：

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`myNodeFunction` 的 `function.json` 應該包含 `scriptFile` 屬性，這個屬性指向有匯出的函式要執行的檔案。

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>使用 `entryPoint`

在 `scriptFile` (或 `index.js`) 中，必須使用 `module.exports` 匯出函式，才能找到並執行該函式。 觸發時執行的函式預設是僅來自該檔案的匯出，名為 `run` 的匯出，或名為 `index` 的匯出。

這可以在 `function.json` 中使用 `entryPoint` 設定，如下列範例所示：

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

在 Functions v2.x (在使用者函式中支援 `this` 參數) 中，函式程式碼則可能如下列範例所示：

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

在此示例中，請務必注意，儘管正在匯出物件，但不能保證在執行之間保留狀態。

## <a name="local-debugging"></a>本地調試

使用 參數`--inspect`啟動時，Node.js 進程偵聽指定埠上的調試用戶端。 在 Azure 函數 2.x 中，可以指定要傳遞到 Node.js 進程中的參數，該進程通過添加環境變數或`languageWorkers:node:arguments = <args>`應用設置來運行代碼。 

要在本地調試，請`"languageWorkers:node:arguments": "--inspect=5858"`在`Values`[本地.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file)檔中添加，並將調試器附加到埠 5858。

使用 VS 代碼進行調試`--inspect`時，將使用專案的啟動.json 檔中`port`的值自動添加參數。

在版本 1.x`languageWorkers:node:arguments`中，設置將不起作用。 可以使用 Azure 函數核心工具上的[`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start)參數選擇調試埠。

## <a name="typescript"></a>TypeScript

當您定位函數運行時的版本 2.x 時[，Visual Studio 代碼的 Azure 函數](functions-create-first-function-vs-code.md)和 Azure[函數核心工具](functions-run-local.md)都允許您使用支援 TypeScript 函數應用專案的範本創建函數應用。 該範本生成`package.json`和`tsconfig.json`投影檔，使使用這些工具從 TypeScript 代碼中轉換、運行和發佈 JavaScript 函數變得更加容易。

生成的`.funcignore`檔用於指示在將專案發佈到 Azure 時排除哪些檔。  

TypeScript 檔 （.ts） 被轉用到輸出目錄中的`dist`JavaScript 檔 （.js）。 TypeScript 範本使用[`scriptFile`](#using-scriptfile)中的`function.json`參數來指示`dist`資料夾中相應 .js 檔的位置。 輸出位置由範本使用檔中的`outDir``tsconfig.json`參數設置。 如果更改此設置或資料夾的名稱，則運行時無法找到要運行的代碼。

> [!NOTE]
> 對 TypeScript 的實驗支援存在函數運行時的版本 1.x。 調用函數時，實驗版本將 TypeScript 檔轉換為 JavaScript 檔。 在版本 2.x 中，此實驗性支援已被工具驅動方法取代，該方法在初始化主機之前和部署過程中進行換用。

本地開發和部署 TypeScript 專案的方式取決於您的開發工具。

### <a name="visual-studio-code"></a>Visual Studio Code

["Visual Studio 代碼"擴展的 Azure 函數](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)允許您使用 TypeScript 開發函數。 核心工具是 Azure 函數擴展的要求。

要在 Visual Studio 代碼中創建 TypeScript`TypeScript`函數應用，請選擇創建函數應用時的語言。

當您按**F5**在本地運行應用時，在主機 （func.exe） 初始化之前完成轉印。 

當您使用 **"部署到函數應用..."** 按鈕將函數應用部署到 Azure 時，Azure 函數擴展程式首先從 TypeScript 原始檔案生成 JavaScript 檔的生成就緒版本。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

在使用核心工具時，TypeScript 專案與 JavaScript 專案有幾種不同的方式。

#### <a name="create-project"></a>建立專案

要使用核心工具創建 TypeScript 函數應用專案，必須在創建函數應用時指定 TypeScript 語言選項。 您可以通過以下方式之一執行此操作：

- 運行命令`func init`，選擇`node`作為語言堆疊，然後選擇`typescript`。

- 執行 `func init --worker-runtime typescript` 命令。

#### <a name="run-local"></a>運行本地

要使用核心工具在本地運行函數應用代碼，請使用以下命令而不是`func host start`： 

```command
npm install
npm start
```

該`npm start`命令等效于以下命令：

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>發佈至 Azure

在使用 該[`func azure functionapp publish`]命令部署到 Azure 之前，可以從 TypeScript 原始檔案創建 JavaScript 檔的生產就緒版本。 

以下命令使用核心工具準備和發佈 TypeScript 專案： 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

在此命令中，替換為`<APP_NAME>`函數應用的名稱。

## <a name="considerations-for-javascript-functions"></a>JavaScript 函式的考量

當您使用 JavaScript 函式時，請留意下列小節中的考量事項。

### <a name="choose-single-vcpu-app-service-plans"></a>選擇單一 vCPU App Service 方案

當您建立使用 App Service 方案的函數應用程式時，建議您選取單一 vCPU 方案，而非具有多個 vCPU 的方案。 目前 Functions 在單一 vCPU VM 上執行 JavaScript 函式會較有效率，而使用較大的 VM 並不會產生預期的效能改進。 如有必要，可以通過添加更多單 vCPU VM 實例手動橫向擴展，也可以啟用自動縮放。 如需詳細資訊，請參閱[手動或自動調整執行個體計數規模](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json)。

### <a name="cold-start"></a>冷啟動

在無伺服器裝載模型中開發 Azure Functions 時，可進行冷啟動。 *冷啟動*是指函數應用程式在閒置一段時間之後進行的第一次啟動，這需要較長的時間啟動。 尤其是對於大型相依性樹狀結構的 JavaScript 函式，冷啟動可能會有很大的影響。 若要加速執行冷啟動程序，請[盡可能以套件檔案的形式執行函式](run-functions-from-deployment-package.md)。 根據預設，許多部署方法都使用從套件執行的模式，但如果在進行許多冷啟動時未以此方式執行，此變更將可達到大幅改善的效果。

### <a name="connection-limits"></a>連線限制

在 Azure 函數應用程式中使用特定于服務的用戶端時，不要使用每個函式呼叫創建新用戶端。 而是在全域作用域中創建單個靜態用戶端。 有關詳細資訊，請參閱在[Azure 函數 中管理連接](manage-connections.md)。

### <a name="use-async-and-await"></a>使用`async`和`await`

在 JavaScript 中編寫 Azure 函數時，應`async`使用`await`和 關鍵字編寫代碼。 使用`async`而不是`await`回檔或`.then``.catch`與承諾編寫代碼有助於避免兩個常見問題：
 - 引發導致[Node.js 進程崩潰](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)的未捕獲異常，可能會影響其他函數的執行。
 - 意外行為，如上下文.log 中缺少日誌，由未正確等待的非同步調用引起。

在下面的示例中，使用錯誤優先回呼函數`fs.readFile`作為第二個參數調用非同步方法。 此代碼會導致上述兩個問題。 未明確捕獲在正確作用域中的異常會崩潰整個過程（問題#1）。 調用`context.done()`回呼函數範圍之外意味著函式呼叫可能在讀取檔之前結束（問題#2）。 在此示例中，調用`context.done()`太早會導致缺少以 開頭的`Data from file:`日誌條目。

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

使用`async`和`await`關鍵字有助於避免這兩個錯誤。 您應該使用 Node.js 實用程式函數[`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original)將錯誤優先回檔樣式函數轉換為可等待函數。

在下面的示例中，在函數執行期間引發的任何未處理的異常只會使引發異常的單個調用失敗。 關鍵字`await`表示，以下`readFileAsync`步驟僅在完成之後`readFile`執行。 使用`async``await`和，您也不需要調用`context.done()`回檔。

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

+ [Azure Functions 的最佳做法](functions-best-practices.md)
+ [Azure Functions 開發人員參考](functions-reference.md)
+ [Azure 函數觸發器和綁定](functions-triggers-bindings.md)

["func azure 函數應用發佈"]: functions-run-local.md#project-file-deployment
