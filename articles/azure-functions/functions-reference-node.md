---
title: Azure Functions 的 JavaScript 開發人員參考
description: 了解如何使用 JavaScript 開發函式。
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 11/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3e99b156d220b4c24a368886b1c0ca0813ffdc51
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674128"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 開發人員指南

本指南包含的詳細資訊，可協助您使用 JavaScript 成功開發 Azure Functions。

Express.js、Node.js 或 JavaScript 開發人員，如果您不熟悉 Azure Functions，請考慮先閱讀下列其中一篇文章：

| 開始使用 | 概念| 引導式學習 |
| -- | -- | -- | 
| <ul><li>[ 使用 Visual Studio CodeNode.js 函數](./create-first-function-vs-code-node.md)</li><li>[ 使用終端機/命令提示字元Node.js 函式](./create-first-function-cli-node.md)</li></ul> | <ul><li>[開發人員指南](functions-reference.md)</li><li>[主機選項](functions-scale.md)</li><li>[TypeScript 函數](#typescript)</li><li>[效能 &nbsp; 考慮](functions-best-practices.md)</li></ul> | <ul><li>[建立無伺服器應用程式](/learn/paths/create-serverless-applications/)</li><li>[重構 Node.js 和 Express Api 到無伺服器 Api](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>JavaScript 函式基本概念

JavaScript ( # A0) 函式是匯出 `function` 的，會在) 的 [function.js中設定](functions-triggers-bindings.md) 觸發的 (觸發程式時執行。 傳遞至每個函式的第一個引數是 `context` 物件，用來接收和傳送系結資料、記錄，以及與執行時間通訊。

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

JavaScript 函數必須透過 [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (或) 匯出 [`exports`](https://nodejs.org/api/modules.html#modules_exports) 。 您匯出的函式應該是可經觸發而執行的 JavaScript 函式。

根據預設，Functions 執行階段會在 `index.js` 中尋找您的函式，其中 `index.js` 與對應的 `function.json` 會共用相同的父目錄。 在預設情況中，您匯出的函式應該是僅來自其檔案的匯出，或是名為 `run` 或 `index` 的匯出。 若要設定檔案位置，並匯出函式的名稱，請參閱以下的[設定您的函式進入點](functions-reference-node.md#configure-function-entry-point)。

您匯出的函式在執行時，會傳入多個引數。 它所採用的第一個引數一律為 `context` 物件。 如果您的函式是同步的 (不會傳回承諾) ，您必須傳遞 `context` 物件，因為 `context.done` 需要呼叫才能正確使用。

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>匯出非同步函式
在 2.x [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 版的函式運行[](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)時間中使用宣告或一般 JavaScript 保證時，您不需要明確呼叫 [`context.done`](#contextdone-method) 回呼來表示您的函式已完成。 在匯出的非同步函式/Promise 完成時，函式便會完成。 針對以1.x 版執行時間為目標的函式，您仍然必須 [`context.done`](#contextdone-method) 在程式碼執行完成時呼叫。

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
 - **_[建議]_ 作為參數傳至您的函式。** 這些繫結會按照在 *function.json* 中定義的順序傳遞至函式。 在 `name` *function.js* 中定義的屬性不需要符合您的參數名稱，雖然應該如此。
 
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
   
 - **使用 JavaScript [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) 物件作為輸入。** 這基本上相當於將輸入傳入作為參數，但可讓您以動態方式處理輸入。
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>輸出
函式可透過數種方式寫入輸出 (`direction === "out"` 的繫結)。 在所有情況下，在 *function.json* 中為繫結定義的 `name` 屬性都會對應至在您的函式中寫入的物件成員名稱。 

您可以用下列其中一種方式將資料指派給輸出系結 (不要將這些方法結合) ：

- **_[建議用於多個輸出]_ 傳回物件。** 如果您使用非同步/承諾傳回函式，您可以傳回具有所指派輸出資料的物件。 在下列範例中，輸出繫結在 *function.json* 中會命名為 "httpResponse" 和 "queueOutput"。

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

  如果您使用同步函式，您可以使用 [`context.done`](#contextdone-method) (，請參閱範例) 來傳回此物件。
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

執行時間會使用 `context` 物件，在您的函式和執行時間之間傳遞資料。 用來讀取和設定系結的資料，以及寫入記錄的資料， `context` 物件一律是傳遞至函式的第一個參數。

針對具有同步程式碼的函式，內容物件包含您在函式 `done` 完成處理時呼叫的回呼。 `done`撰寫非同步程式碼時，不需要明確呼叫，而是以隱含方式呼叫 `done` 回呼。

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

傳遞至您函式的內容 `executionContext` 會公開屬性，此屬性是具有下列屬性的物件：

| 屬性名稱  | 類型  | 描述 |
|---------|---------|---------|
| `invocationId` | String | 提供特定函式呼叫的唯一識別碼。 |
| `functionName` | String | 提供正在執行之函式的名稱 |
| `functionDirectory` | String | 提供函數應用程式目錄。 |

下列範例顯示如何傳回 `invocationId` 。

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>context.bindings 屬性

```js
context.bindings
```

傳回用來讀取或指派系結資料的命名物件。 您可以藉由讀取的屬性來存取輸入和觸發程式系結資料 `context.bindings` 。 您可以藉由將資料新增至來指派輸出系結資料 `context.bindings`

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

通知執行階段您的程式碼已完成。 當您的函數使用宣告時 [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) ，您不需要使用 `context.done()` 。 隱含地呼叫 `context.done` 回呼。 非同步函式可在 Node 8 或更新版本中使用，而這需要 2.x 版的 Functions 執行階段。

如果您的函式不是非同步函式， **您必須呼叫**， `context.done` 以通知執行時間您的函式已完成。 如果沒有，執行將會逾時。

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

可讓您寫入預設追蹤層級的串流函式記錄，以及其他可用的記錄層級。 下一節將詳細說明追蹤記錄。 

## <a name="write-trace-output-to-logs"></a>將追蹤輸出寫入至記錄

在函式中，您可以使用方法，將 `context.log` 追蹤輸出寫入至記錄檔和主控台。 當您呼叫時 `context.log()` ，您的訊息會寫入預設追蹤層級的記錄，也就是 _資訊_ 追蹤層級。 函數會與 Azure 應用程式 Insights 整合，以更妥善地捕捉您的函式應用程式記錄。 Application Insights （Azure 監視器的一部分）提供收集、視覺化轉譯以及分析應用程式遙測和追蹤輸出的功能。 若要深入瞭解，請參閱 [監視 Azure Functions](functions-monitoring.md)。

下列範例會在 info 追蹤層級寫入記錄，包括調用識別碼：

```javascript
context.log("Something has happened. " + context.invocationId); 
```

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

> [!NOTE]  
> 請勿使用 `console.log` 寫入追蹤輸出。 因為的輸出 `console.log` 是在函式應用層級所捕捉，所以它不會系結至特定的函式調用，也不會顯示在特定函式的記錄檔中。 此外，1.x 版的函式執行時間不支援使用 `console.log` 來寫入主控台。

### <a name="trace-levels"></a>追蹤層級

除了預設層級，下列記錄方法可讓您在特定的追蹤層級撰寫函數記錄。

| 方法                 | 描述                                |
| ---------------------- | ------------------------------------------ |
| **_訊息_ (錯誤)**   | 在記錄檔中寫入錯誤層級事件。   |
| **warn(_message_)**    | 將警告層級事件寫入記錄檔。 |
| **資訊 (_訊息_)**    | 寫入資訊層級或更低層級的記錄。    |
| **verbose(_message_)** | 寫入詳細資訊層級記錄。           |

下列範例會在警告追蹤層級寫入相同的記錄，而不是在資訊層級上：

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

因為 _error_ 是最高追蹤層級，所以只要啟用記錄，這項追蹤就會寫入所有追蹤層級的輸出。

### <a name="configure-the-trace-level-for-logging"></a>設定記錄的追蹤層級

函數可讓您定義寫入至記錄檔或主控台的閾值追蹤層級。 特定閾值設定取決於您的函數執行時間版本。

# <a name="v2x"></a>[v2. x +](#tab/v2)

若要設定寫入記錄檔之追蹤的臨界值，請使用 `logging.logLevel` host.json 檔案中的屬性。 此 JSON 物件可讓您定義函式應用程式中所有函式的預設臨界值，還可以定義個別函式的特定臨界值。 若要深入瞭解，請參閱 [如何設定 Azure Functions 的監視](configure-monitoring.md)。

# <a name="v1x"></a>[v1.x](#tab/v1)

若要設定寫入至記錄檔和主控台之所有追蹤的閾值，請使用 `tracing.consoleLevel` host.json 檔案中的屬性。 這個設定會套用到函式應用程式中的所有函式。 下列範例會設定追蹤閾值來啟用詳細資訊記錄︰

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

**consoleLevel** 的值對應至 `context.log` 方法的名稱。 若要停用主控台的所有追蹤記錄，請將 **consoleLevel** 設為 _off_。 如需詳細資訊，請參閱 [ v1. x 參考上的host.js](functions-host-json-v1.md)。

---

### <a name="log-custom-telemetry"></a>記錄自訂遙測

根據預設，函式會將輸出做為追蹤寫入 Application Insights。 如需更多控制，您可以改為使用 [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js) 將自訂遙測資料傳送至您的 Application Insights 實例。 

# <a name="v2x"></a>[v2. x +](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[v1.x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

`tagOverrides` 參數會將 `operation_Id` 設為函式的引動過程識別碼。 此設定能夠讓指定的函式引動過程中所有自動產生和自訂的遙測相互關聯。

## <a name="http-triggers-and-bindings"></a>HTTP 觸發程序和繫結

HTTP 和 Webhook 觸發程序以及 HTTP 輸出繫結會使用要求和回應物件來代表 HTTP 傳訊。  

### <a name="request-object"></a>要求物件

`context.req` (要求) 物件具有下列屬性：

| 屬性      | 描述                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 包含要求本文的物件。               |
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
| _body_    | 包含回應本文的物件。         |
| _頭_ | 包含回應標頭的物件。             |
| _isRaw_   | 表示略過回應的格式。    |
| _status_  | 回應的 HTTP 狀態碼。                     |
| _餅乾_ | 在回應中設定之 HTTP cookie 物件的陣列。 HTTP cookie 物件具有 `name` 、 `value` 和其他 cookie 屬性，例如 `maxAge` 或 `sameSite` 。 |

### <a name="accessing-the-request-and-response"></a>存取要求和回應 

使用 HTTP 觸發程序時，您可以使用許多方式來存取 HTTP 要求和回應物件︰

+ **From `req` 和 `res` 物件上的屬性 `context` 。** 如此一來，您可以使用傳統模式來存取內容物件中的 HTTP 資料，而不需使用完整 `context.bindings.name` 模式。 下列範例示範如何存取 `context` 上的 `req` 和 `res` 物件：

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
+ **_[僅回應]_ 藉由呼叫 `context.res.send(body?: any)` 。** HTTP 回應是以做為回應主體的輸入 `body` 所建立。 隱含地呼叫 `context.done()`。

+ **_[僅回應]_ 藉由呼叫 `context.done()` 。** 一種特殊類型的 HTTP 系結會傳回傳遞給方法的回應 `context.done()` 。 下列 HTTP 輸出繫結定義 `$return` 輸出參數︰

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

## <a name="scaling-and-concurrency"></a>調整和並行

根據預設，Azure Functions 會自動監視您應用程式的負載，並視需要為 Node.js 建立額外的主控制項實例。 Functions 會針對不同的觸發程序類型使用內建 (不是使用者可設定的) 閾值，以決定何時新增執行個體，例如訊息的存留期和 QueueTrigger 的佇列大小。 如需詳細資訊，請參閱[耗用量和進階方案的運作方式](event-driven-scaling.md)。

這項調整行為足以滿足許多 Node.js 的應用程式。 對於 CPU 系結的應用程式，您可以使用多個語言工作者進程進一步改善效能。

根據預設，每個 Functions 主機執行個體都有單一語言背景工作處理序。 您可以使用 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 應用程式設定，來增加每個主機的背景工作處理序數目 (最多10個)。 Azure Functions 接著會嘗試在這些背景工作中平均散發同時函式叫用。 

FUNCTIONS_WORKER_PROCESS_COUNT 適用於 Functions 在擴增應用程式以符合需求時所建立的每個主機。 

## <a name="node-version"></a>節點版本

下錶針對每個主要版本的函式執行時間（依作業系統），顯示目前支援的 Node.js 版本：

| Functions 版本 | Windows)  (節點版本 | Linux)  (節點版本 |
|---|---| --- |
| 1.x | 6.11.2 (由執行階段鎖定) | n/a |
| 2.x  | `~8`<br/>`~10` (建議的) <br/>`~12` | `node|8`<br/>`node|10` (建議的)   |
| 3.x | `~10`<br/>`~12` (建議的) <br/>`~14` (預覽)  | `node|10`<br/>`node|12` (建議的) <br/>`node|14` (預覽) |

您可以從任何函式進行記錄，以查看執行時間正在使用的目前版本 `process.version` 。

### <a name="setting-the-node-version"></a>設定節點版本

若為 Windows 函式應用程式，請將 `WEBSITE_NODE_DEFAULT_VERSION` [應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings) 設為支援的 LTS 版本（例如），以在 Azure 中將版本設為目標 `~12` 。

針對 Linux 函式應用程式，請執行下列 Azure CLI 命令以更新節點版本。

```bash
az functionapp config set --linux-fx-version "node|12" --name "<MY_APP_NAME>" --resource-group "<MY_RESOURCE_GROUP_NAME>"
```

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

2. 按一下 **偵錯主控台**  >  **CMD**]。

3. 移至 `D:\home\site\wwwroot`，然後將 package.json 檔案拖曳至頁面上半部的 **wwwroot** 資料夾。  
    您也可以使用其他方法將檔案上傳至函數應用程式。 如需詳細資訊，請參閱[如何更新函式應用程式檔案](functions-reference.md#fileupdate)。 

4. 上傳 package.json 檔案之後，請在 **Kudu 遠端執行主控台** 中執行 `npm install` 命令。  
    此動作會下載 package.json 檔案中指出的套件，並重新啟動函數應用程式。

## <a name="environment-variables"></a>環境變數

將您自己的環境變數新增至函式應用程式（在本機和雲端環境中），例如 (連接字串、金鑰和端點) 或環境設定 (例如程式碼剖析變數) 。 `process.env`在您的函式程式碼中使用來存取這些設定。

### <a name="in-local-development-environment"></a>在本機開發環境中

在本機執行時，您的函式專案會包含[ `local.settings.json` 檔案，您](./functions-run-local.md)可以在其中將環境變數儲存在 `Values` 物件中。 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "translatorTextEndPoint": "https://api.cognitive.microsofttranslator.com/",
    "translatorTextKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "languageWorkers__node__arguments": "--prof"
  }
}
```

### <a name="in-azure-cloud-environment"></a>在 Azure 雲端環境中

在 Azure 中執行時，函數應用程式可讓您設定使用 [應用程式設定](functions-app-settings.md)（例如服務連接字串），並在執行期間將這些設定公開為環境變數。 

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="access-environment-variables-in-code"></a>存取程式碼中的環境變數

使用的環境變數來存取應用程式設定 `process.env` ，如下所示，在第二和第三個呼叫中，我們會在這裡 `context.log()` 記錄 `AzureWebJobsStorage` 和 `WEBSITE_SITE_NAME` 環境變數：

```javascript
module.exports = async function (context, myTimer) {

    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

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

在此範例中，請務必注意，雖然正在匯出物件，但是無法保證在執行之間保留狀態。

## <a name="local-debugging"></a>本機調試

使用參數開始時 `--inspect` ，Node.js 進程會接聽指定埠上的偵錯工具用戶端。 在 Azure Functions 2.x 中，您可以藉由新增環境變數或應用程式設定，指定要傳遞至執行程式碼之 Node.js 進程的引數 `languageWorkers:node:arguments = <args>` 。 

若要在本機進行本機調試，請在檔案的 `"languageWorkers:node:arguments": "--inspect=5858"`local.settings.js下加入， `Values` 然後將偵錯工具附加到埠5858。 [](./functions-run-local.md#local-settings-file)

使用 VS Code 進行偵錯工具時，會 `--inspect` 使用專案 launch.js檔案中的值，自動新增參數 `port` 。

在1.x 版中，設定 `languageWorkers:node:arguments` 將無法運作。 您可以使用 Azure Functions Core Tools 上的參數來選取 debug 埠 [`--nodeDebugPort`](./functions-run-local.md#start) 。

## <a name="typescript"></a>TypeScript

當您以2.x 版的函式執行時間為目標時， [Visual Studio Code 的 Azure Functions](./create-first-function-cli-typescript.md) 和 [Azure Functions Core Tools](functions-run-local.md) 可讓您使用支援 TypeScript 函式應用程式專案的範本來建立函數應用程式。 範本會產生 `package.json` 和 `tsconfig.json` 專案檔，讓您更輕鬆地使用這些工具，從 TypeScript 程式碼中轉換、執行和發佈 JavaScript 函式。

產生的檔案 `.funcignore` 會用來指出將專案發行至 Azure 時所要排除的檔案。  

)  ( 的 TypeScript 檔案會在輸出目錄中，轉換至 JavaScript 檔案 ( .js) 。 `dist` TypeScript 範本會使用中的[ `scriptFile` 參數](#using-scriptfile) `function.json` 來表示資料夾中對應之 .js 檔案的位置 `dist` 。 輸出位置是由範本藉由在檔案中使用參數來設定 `outDir` `tsconfig.json` 。 如果您變更此設定或資料夾的名稱，則執行時間找不到要執行的程式碼。

從 TypeScript 專案進行本機開發和部署的方式取決於您的開發工具。

### <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code 擴充功能的 [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 可讓您使用 TypeScript 開發函式。 核心工具是 Azure Functions 擴充功能的需求。

若要在 Visual Studio Code 中建立 TypeScript 函式應用程式，請在 `TypeScript` 建立函數應用程式時，選擇您的語言。

當您按下 **F5** 以在本機執行應用程式時，轉譯會在主機 ( # A0) 初始化之前完成。 

當您使用 [ **部署至函式應用程式 ...** ] 按鈕將函數應用程式部署至 Azure 時，Azure Functions 擴充功能會先從 TypeScript 來源檔案產生可供生產環境使用的 JavaScript 檔案組建。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

使用 Core Tools 時，TypeScript 專案與 JavaScript 專案有幾種不同的方式。

#### <a name="create-project"></a>建立專案

若要使用 Core Tools 建立 TypeScript 函式應用程式專案，您必須在建立函數應用程式時指定 TypeScript 語言選項。 您可以透過下列其中一種方式來執行此動作：

- 執行 `func init` 命令，選取 `node` 做為您的語言堆疊，然後選取 `typescript` 。

- 執行 `func init --worker-runtime typescript` 命令。

#### <a name="run-local"></a>執行本機

若要使用 Core Tools 在本機執行函數應用程式程式碼，請使用下列命令，而不是 `func host start` ： 

```command
npm install
npm start
```

此 `npm start` 命令相當於下列命令：

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>發佈至 Azure

使用 [`func azure functionapp publish`] 命令部署至 Azure 之前，您可以從 TypeScript 來源檔案建立可供生產環境使用的 JavaScript 檔案。 

下列命令會使用 Core Tools 來準備及發佈您的 TypeScript 專案： 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

在此命令中， `<APP_NAME>` 以您的函數應用程式名稱取代。

## <a name="considerations-for-javascript-functions"></a>JavaScript 函式的考量

當您使用 JavaScript 函式時，請留意下列小節中的考量事項。

### <a name="choose-single-vcpu-app-service-plans"></a>選擇單一 vCPU App Service 方案

當您建立使用 App Service 方案的函數應用程式時，建議您選取單一 vCPU 方案，而非具有多個 vCPU 的方案。 目前 Functions 在單一 vCPU VM 上執行 JavaScript 函式會較有效率，而使用較大的 VM 並不會產生預期的效能改進。 如有必要，您可以藉由新增更多單一 vCPU 的 VM 實例來手動相應放大，也可以啟用自動調整。 如需詳細資訊，請參閱[手動或自動調整執行個體計數規模](../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json)。

### <a name="cold-start"></a>冷啟動

在無伺服器裝載模型中開發 Azure Functions 時，可進行冷啟動。 *冷啟動* 是指函數應用程式在閒置一段時間之後進行的第一次啟動，這需要較長的時間啟動。 尤其是對於大型相依性樹狀結構的 JavaScript 函式，冷啟動可能會有很大的影響。 若要加速執行冷啟動程序，請[盡可能以套件檔案的形式執行函式](run-functions-from-deployment-package.md)。 根據預設，許多部署方法都使用從套件執行的模式，但如果在進行許多冷啟動時未以此方式執行，此變更將可達到大幅改善的效果。

### <a name="connection-limits"></a>連線限制

當您在 Azure Functions 應用程式中使用服務特定用戶端時，請勿使用每次函式呼叫來建立新的用戶端。 相反地，請在全域範圍中建立單一的靜態用戶端。 如需詳細資訊，請參閱 [Azure Functions 中的管理連接](manage-connections.md)。

### <a name="use-async-and-await"></a>使用 `async` 和 `await`

在 JavaScript 中撰寫 Azure Functions 時，您應該使用和關鍵字撰寫程式碼 `async` `await` 。 使用來撰寫程式碼 `async` ， `await` 而不是回呼或 `.then` 和 `.catch` with 承諾，有助於避免兩個常見問題：
 - 擲回無法攔截的例外狀況，使 [Node.js 進程損毀](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)，可能會影響其他函式的執行。
 - 未正確等候的非同步呼叫所造成的非預期行為，例如來自內容的遺漏記錄。

在下列範例中， `fs.readFile` 會使用錯誤優先回呼函式來叫用非同步方法做為第二個參數。 此程式碼會造成上述兩個問題。 在正確的範圍內未明確攔截的例外狀況，會損毀整個進程 (#1) 的問題。 在回呼函式範圍之外呼叫，表示函式 `context.done()` 調用可能會在讀取檔案 (#2) 的問題之前結束。 在此範例中，呼叫 `context.done()` 過早的結果會導致從開始遺失的記錄專案 `Data from file:` 。

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

使用 `async` 和 `await` 關鍵字有助於避免這兩個錯誤。 您應該使用 Node.js 公用程式函式 [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) ，將錯誤優先的回呼樣式函式轉換成可等候函式。

在下列範例中，函數執行期間擲回的任何未處理例外狀況，只會使引發例外狀況的個別調用失敗。 `await`關鍵字表示下列步驟 `readFileAsync` 只會在完成之後執行 `readFile` 。 使用 `async` 和 `await` 時，您也不需要呼叫 `context.done()` 回呼。

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
+ [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)

[' func azure functionapp publish ']: functions-run-local.md#project-file-deployment