---
title: 工作流定義語言架構引用
description: 描述 Azure 邏輯應用中工作流的工作流定義語言的 JSON 架構和語法的參考指南
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283858"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Azure 邏輯應用中工作流定義語言的架構參考指南

在[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)中創建邏輯應用時，邏輯應用具有一個基本工作流定義，用於描述在邏輯應用中運行的實際邏輯。 該工作流定義使用[JSON](https://www.json.org/)並遵循由工作流定義語言架構驗證的結構。 此引用概述了此結構以及架構在工作流定義中定義屬性的方式。

## <a name="workflow-definition-structure"></a>工作流程定義結構

工作流定義始終包含具現化邏輯應用的觸發器，以及觸發器觸發後運行的一個或多個操作。

以下是工作流程定義的高階結構︰

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| 屬性 | 必要 | 描述 |
|-----------|----------|-------------|
| `definition` | 是 | 工作流程定義的起始元素 |
| `$schema` | 只有在外部參考工作流程定義時 | JSON 結構描述檔案的位置，該檔案說明工作流程定義語言版本，您可以在此找到此版本： <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | 否 | 在工作流運行時執行的一個或多個操作的定義。 有關詳細資訊，請參閱[觸發器和操作](#triggers-actions)。 <p><p>動作上限：250 |
| `contentVersion` | 否 | 您的工作流程定義版本號碼，預設為 "1.0.0.0"。 若要在部署工作流程時協助識別及確認正確的定義，請指定要使用的值。 |
| `outputs` | 否 | 從工作流運行中返回的輸出的定義。 有關詳細資訊，請參閱[輸出](#outputs)。 <p><p>輸出上限：10 |
| `parameters` | 否 | 傳遞要在邏輯應用運行時使用的值的一個或多個參數的定義。 如需詳細資訊，請參閱[參數](#parameters)。 <p><p>參數上限：50 |
| `staticResults` | 否 | 當在這些操作上啟用靜態結果時，操作返回的一個或多個靜態結果的定義為類比輸出。 在每個操作定義中，`runtimeConfiguration.staticResult.name`屬性引用 中的`staticResults`相應定義。 有關詳細資訊，請參閱[靜態結果](#static-results)。 |
| `triggers` | 否 | 一或多個觸發程序的定義，此觸發程序可具現化您的工作流程。 您可以定義多個觸發程序，但只能利用工作流程定義語言，而不會透過 Logic Apps 設計工具呈現。 有關詳細資訊，請參閱[觸發器和操作](#triggers-actions)。 <p><p>觸發程序上限：10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>觸發程序及動作

在工作流程定義中，`triggers` 和 `actions` 區段會定義在工作流程執行期間發生的呼叫。 如需這些區段的語法和詳細資訊，請參閱[工作流程觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

<a name="parameters"></a>

## <a name="parameters"></a>參數

部署生命週期通常具有不同的開發、測試、暫存和生產環境。 將邏輯應用部署到各種環境時，您可能需要根據部署需求使用不同的值（如連接字串）。 或者，您可能有要在整個邏輯應用中重用的值，而無需硬編碼或經常更改。 在工作流定義的`parameters`部分中，您可以為邏輯應用在運行時使用的值定義或編輯參數。 必須先定義這些參數，然後才能在工作流定義的其他位置引用這些參數。

以下是參數定義的一般結構︰

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| 屬性 | 必要 | 類型 | 描述 |
|-----------|----------|------|-------------|
| <*參數名稱*> | 是 | String | 要定義的參數的名稱 |
| <*參數類型*> | 是 | int， 浮動， 字串， 布林， 陣列， 物件， 安全字串， 安全物件 <p><p>**注意**：對於所有密碼、金鑰和機密，請使用`securestring`或`secureobject`類型，`GET`因為操作不會返回這些類型。 有關保護參數的詳細資訊，請參閱[操作和輸入參數的安全建議](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)。 | 參數的類型 |
| <*預設參數值*> | 是 | 與 `type` 相同 | 如果工作流具現化時未指定值，則要使用的預設參數值。 需要`defaultValue`該屬性，以便邏輯應用設計器可以正確顯示參數，但可以指定空值。 |
| <*具有允許參數值的陣列*> | 否 | Array | 具有參數可接受值的陣列 |
| <*參數描述*> | 否 | JSON 物件 | 任何其他參數詳細資訊，如參數的說明 |
||||

接下來，為工作流定義創建[Azure 資源管理器範本](../azure-resource-manager/templates/overview.md)，定義接受部署時所需值的範本參數，將硬編碼值替換為對範本或工作流定義參數的引用（視情況而定），並將要在部署時使用的值存儲在單獨的[參數檔中](../azure-resource-manager/templates/parameter-files.md)。 這樣，您可以通過參數檔更輕鬆地更改這些值，而無需更新和重新部署邏輯應用。 對於敏感或必須保護的資訊（如使用者名、密碼和機密），可以在 Azure 金鑰保存庫中存儲這些值，並讓參數檔從金鑰保存庫中檢索這些值。 有關在範本和工作流定義級別定義參數的詳細資訊和示例，請參閱[概述：使用 Azure 資源管理器範本自動部署邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。

<a name="static-results"></a>

## <a name="static-results"></a>靜態結果

在`staticResults`屬性中，定義操作的類比`outputs`，並在`status`操作的靜態結果設置打開時返回該操作。 在操作的定義中，`runtimeConfiguration.staticResult.name`屬性引用 中`staticResults`靜態結果定義的名稱。 瞭解如何[通過設置靜態結果來使用類比資料測試邏輯應用](../logic-apps/test-logic-apps-mock-data-static-results.md)。

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| 屬性 | 必要 | 類型 | 描述 |
|-----------|----------|------|-------------|
| <*靜態結果定義名稱*> | 是 | String | 操作定義可以通過`runtimeConfiguration.staticResult`物件引用的靜態結果定義的名稱。 如需詳細資訊，請參閱[執行階段組態設定](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options)。 <p>您可以使用所需的任何唯一名稱。 預設情況下，此唯一名稱追加一個數位，該數位根據需要遞增。 |
| <*返回的輸出屬性和值*> | 是 | 不定 | 這些屬性的要求因條件而異。 例如，當`status`是`Succeeded`時，`outputs`屬性包括操作作為類比輸出返回的屬性和值。 如果`status`是`Failed`，`outputs`則屬性包括`errors`屬性，該屬性是具有一個或多個具有錯誤資訊`message`的錯誤物件的陣列。 |
| <*標頭值*> | 否 | JSON | 操作返回的任何標頭值 |
| <*狀態碼返回*> | 是 | String | 操作返回的狀態碼 |
| <*操作狀態*> | 是 | String | 例如，操作的狀態，`Succeeded`或`Failed` |
|||||

例如，在此 HTTP 操作定義中，`runtimeConfiguration.staticResult.name`屬性引用`HTTP0``staticResults`定義操作的類比輸出的屬性內。 該`runtimeConfiguration.staticResult.staticResultOptions`屬性指定靜態結果設置位於`Enabled`HTTP 操作上。

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

HTTP 操作返回 定義`HTTP0``staticResults`中的輸出。 在此示例中，對於狀態碼，類比輸出為`OK`。 對於標頭值，類比輸出為`"Content-Type": "application/JSON"`。 對於操作的狀態，類比輸出為`Succeeded`。

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>運算式

使用 JSON，您可以擁有在設計階段存在的常值，例如：

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

您也可以擁有在執行階段才存在的值。 若要表示這些值，您可以使用會在執行階段評估的「運算式」**。 運算式是一個序列，可包含一或多個[函式](#functions)[運算子](#operators)、變數、明確值或常數。 在工作流程定義中，您可以在運算式前面加上 (\@) 符號，以便在 JSON 字串值中的任何地方使用運算式。 在評估代表 JSON 值的運算式時，移除 \@ 字元即可擷取運算式主體，而且一律會產生其他 JSON 值。

例如，對於先前定義的 `customerName` 屬性，您可以在運算式中使用 [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 函式來取得此屬性值，並將該值指派給 `accountName` 屬性：

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

「字串內插補點」** 也可讓您在字串內使用多個運算式，以 \@ 字元和大括號 ({}) 圍住。 語法如下：

```json
@{ "<expression1>", "<expression2>" }
```

結果一律是字串，讓這項功能類似於 `concat()` 函式，例如： 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

如果您有開頭為 \@ 字元的常值字串，請在 \@ 字元前面加上另一個 \@ 字元作為逸出字元：\@\@

下列範例顯示如何評估運算式：

| JSON 值 | 結果 |
|------------|--------|
| "Sophia Owen" | 傳回這些字元：'Sophia Owen' |
| "array[1]" | 傳回這些字元：'array[1]' |
| "\@\@" | 以一個字元的字串形式傳回這些字元：'\@' |
| " \@" | 以兩個字元的字串形式傳回這些字元：'\@' |
|||

在這些範例中，假設您定義 "myBirthMonth" 等於 "January" 以及 "myAge" 等於數字 42：

```json
"myBirthMonth": "January",
"myAge": 42
```

下列範例顯示如何評估下列運算式：

| JSON 運算式 | 結果 |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | 傳回這個字串："January" |
| "\@{parameters('myBirthMonth')}" | 傳回這個字串："January" |
| "\@parameters('myAge')" | 傳回這個數字：42 |
| "\@{parameters('myAge')}" | 以字串形式傳回這個數字："42" |
| "My age is \@{parameters('myAge')}" | 傳回這個字串："My age is 42" |
| "\@concat('My age is ', string(parameters('myAge')))" | 傳回這個字串："My age is 42" |
| "My age is \@\@{parameters('myAge')}" | 傳回這個字串，其中包含運算式："My age is \@{parameters('myAge')}` |
|||

當您在 Logic Apps 設計工具中以視覺化方式運作時，您可以透過運算式產生器建立運算式，例如：

![Logic Apps 設計工具 > 運算式產生器](./media/logic-apps-workflow-definition-language/expression-builder.png)

當您完成時，運算式會對工作流程定義中對應的屬性顯示，例如，以下的 `searchQuery` 屬性：

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="outputs"></a>

## <a name="outputs"></a>輸出

在 `outputs` 區段中，定義工作流程在完成執行時可傳回的資料。 例如，若要追蹤來自每次執行的特定狀態或值，請指定工作流程輸出會傳回該資料。

> [!NOTE]
> 當回應來自服務 REST API 的傳入要求時，請勿使用 `outputs`。 請改用 `Response` 動作類型。 如需詳細資訊，請參閱[工作流程觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

以下是輸出定義的一般結構：

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| 屬性 | 必要 | 類型 | 描述 |
|-----------|----------|------|-------------|
| <*鍵名*> | 是 | String | 輸出傳回值的索引鍵名稱 |
| <*鍵型*> | 是 | int、float、string、securestring、bool、array、JSON 物件 | 輸出傳回值的類型 |
| <*鍵值*> | 是 | 與<*鍵類型*相同> | 輸出傳回值 |
|||||

要從工作流運行中獲取輸出，請查看 Azure 門戶中的邏輯應用的執行歷程記錄和詳細資訊，或使用工作流 REST [API](https://docs.microsoft.com/rest/api/logic/workflows)。 您也可以將輸出傳遞至外部系統 (例如 PowerBI)，以便建立儀表板。

<a name="operators"></a>

## <a name="operators"></a>運算子

在[運算式](#expressions)和[函式](#functions)中，運算子會執行特定工作，例如參考屬性或陣列中的值。

| 運算子 | Task |
|----------|------|
| ' | 若要將字串常值作為輸入或使用於運算式和函式中，只用單引號圍住此字串，例如 `'<myString>'`。 請勿使用雙引號 ("")，這會與整個運算式的 JSON 格式設定發生衝突。 例如： <p>**是**：length('Hello') </br>**否**：length("Hello") <p>當您傳遞陣列或數字時，您不需要包圍標點符號。 例如： <p>**是**：length([1, 2, 3]) </br>**否**：length("[1, 2, 3]") |
| [] | 若要參考陣列中特定位置 (索引) 的值，請使用方括號。 例如，若要取得陣列中的第二個項目： <p>`myArray[1]` |
| . | 若要參考物件中的屬性，請使用點運算子。 例如，若要取得 `customer` JSON 物件的 `name` 屬性： <p>`"@parameters('customer').name"` |
| ? | 若要在不會發生執行階段錯誤的情況下，參考物件中的 null 屬性，請使用問號運算子。 例如，若要處理來自觸發程序的 null 輸出，您可以使用此運算式︰ <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>函式

某些運算式從運行時操作中獲取其值，當工作流定義開始運行時，這些操作可能還不存在。 若要在運算式中參考或使用這些值，您可以使用工作流程定義語言所提供的[*函式*](../logic-apps/workflow-definition-language-functions-reference.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解[工作流程定義語言動作和觸發程序](../logic-apps/logic-apps-workflow-actions-triggers.md)
* 深入了解如何透過 [Workflow REST API](https://docs.microsoft.com/rest/api/logic/workflows) 以程式設計方式建立及管理邏輯應用程式
