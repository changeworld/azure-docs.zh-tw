---
title: 工作流程定義語言架構參考
description: 描述 Azure Logic Apps 中工作流程之 JSON 架構和語法的參考指南
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283858"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Azure Logic Apps 中工作流程定義語言的架構參考指南

當您在[Azure Logic Apps](../logic-apps/logic-apps-overview.md)中建立邏輯應用程式時，邏輯應用程式會有基礎工作流程定義，以描述在邏輯應用程式中執行的實際邏輯。 該工作流程定義會使用[JSON](https://www.json.org/) ，並遵循工作流程定義語言架構所驗證的結構。 此參考提供此結構的總覽，以及架構如何定義工作流程定義中的屬性。

## <a name="workflow-definition-structure"></a>工作流程定義結構

工作流程定義一律包含具現化邏輯應用程式的觸發程式，以及觸發程式引發後執行的一或多個動作。

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
| `actions` | 否 | 要在工作流程執行時間執行的一或多個動作的定義。 如需詳細資訊，請參閱[觸發程式和動作](#triggers-actions)。 <p><p>動作上限：250 |
| `contentVersion` | 否 | 您的工作流程定義版本號碼，預設為 "1.0.0.0"。 若要在部署工作流程時協助識別及確認正確的定義，請指定要使用的值。 |
| `outputs` | 否 | 要從工作流程執行傳回的輸出定義。 如需詳細資訊，請參閱[輸出](#outputs)。 <p><p>輸出上限：10 |
| `parameters` | 否 | 一或多個參數的定義，會傳遞要在邏輯應用程式執行時間使用的值。 如需詳細資訊，請參閱[參數](#parameters)。 <p><p>參數上限：50 |
| `staticResults` | 否 | 動作所傳回的一或多個靜態結果的定義，會在這些動作上啟用靜態結果時做為模擬輸出。 在每個動作定義中，`runtimeConfiguration.staticResult.name` 屬性都會參考 `staticResults`內的對應定義。 如需詳細資訊，請參閱[靜態結果](#static-results)。 |
| `triggers` | 否 | 一或多個觸發程序的定義，此觸發程序可具現化您的工作流程。 您可以定義多個觸發程序，但只能利用工作流程定義語言，而不會透過 Logic Apps 設計工具呈現。 如需詳細資訊，請參閱[觸發程式和動作](#triggers-actions)。 <p><p>觸發程序上限：10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>觸發程序及動作

在工作流程定義中，`triggers` 和 `actions` 區段會定義在工作流程執行期間發生的呼叫。 如需這些區段的語法和詳細資訊，請參閱[工作流程觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

<a name="parameters"></a>

## <a name="parameters"></a>參數

部署生命週期通常會有不同的環境來進行開發、測試、預備和生產。 將邏輯應用程式部署到不同的環境時，您可能會想要根據您的部署需求，使用不同的值，例如連接字串。 或者，您可能會有想要在不硬式編碼或經常變更的情況下，于整個邏輯應用程式中重複使用的值。 在工作流程定義的 `parameters` 區段中，您可以定義或編輯邏輯應用程式在執行時間所使用之值的參數。 您必須先定義這些參數，才能在您的工作流程定義中的其他位置參考這些參數。

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
| <*參數名稱*> | 是 | String | 您想要定義之參數的名稱 |
| <*參數類型*> | 是 | int、float、string、bool、array、object、securestring、secureobject <p><p>**注意**：對於所有密碼、金鑰和秘密，請使用 `securestring` 或 `secureobject` 類型，因為 `GET` 作業不會傳回這些類型。 如需保護參數的詳細資訊，請參閱[動作和輸入參數的安全性建議](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)。 | 參數的類型 |
| <*預設-參數-值*> | 是 | 與 `type` 相同 | 當工作流程具現化時，若未指定任何值時，所要使用的預設參數值。 `defaultValue` 屬性是必要的，讓邏輯應用程式設計工具可以正確地顯示參數，但您可以指定空值。 |
| <*陣列-具有允許的參數值*> | 否 | Array | 具有參數可接受值的陣列 |
| <*參數-描述*> | 否 | JSON 物件 | 任何其他參數詳細資料，例如參數的描述 |
||||

接下來，為您的工作流程定義建立[Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md)、定義範本參數，以在部署時接受您想要的值，並適當地將已硬式編碼的值取代為範本或工作流程定義參數，並在個別的[參數](../azure-resource-manager/templates/parameter-files.md)檔案中儲存要用於部署的值。 如此一來，您就可以更輕鬆地透過參數檔案來變更這些值，而不需要更新和重新部署邏輯應用程式。 對於機密或必須受到保護的資訊，例如使用者名稱、密碼和秘密，您可以將這些值儲存在 Azure Key Vault 中，並讓您的參數檔案從您的金鑰保存庫中抓取這些值。 如需在範本和工作流程定義層級定義參數的詳細資訊和範例，請參閱[總覽：使用 Azure Resource Manager 範本自動部署邏輯應用程式](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。

<a name="static-results"></a>

## <a name="static-results"></a>靜態結果

在 [`staticResults`] 屬性中，定義動作的 [模擬] `outputs` 和 [動作的靜態結果] 設定開啟時，動作會傳回的 `status`。 在動作的定義中，`runtimeConfiguration.staticResult.name` 屬性會參考 `staticResults`內靜態結果定義的名稱。 瞭解如何藉[由設定靜態結果，以模擬資料來測試邏輯應用程式](../logic-apps/test-logic-apps-mock-data-static-results.md)。

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
| <*靜態結果定義名稱*> | 是 | String | 動作定義可以透過 `runtimeConfiguration.staticResult` 物件來參考的靜態結果定義名稱。 如需詳細資訊，請參閱[執行階段組態設定](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options)。 <p>您可以使用任何您想要的唯一名稱。 根據預設，這個唯一名稱會附加一個數位，視需要遞增。 |
| <*的輸出-屬性和值-傳回*> | 是 | 不定 | 這些屬性的需求會根據不同的條件而有所不同。 例如，當 `status` `Succeeded`時，`outputs` 屬性會包含由動作以模擬輸出傳回的屬性和值。 如果 `status` 是 `Failed`，`outputs` 屬性會包含 `errors` 屬性，這是具有一或多個錯誤的陣列，`message` 具有錯誤資訊的物件。 |
| <*header-values*> | 否 | JSON | 動作傳回的任何標頭值 |
| <*狀態-程式碼傳回*> | 是 | String | 動作所傳回的狀態碼 |
| <*動作-狀態*> | 是 | String | 動作的狀態，例如 `Succeeded` 或 `Failed` |
|||||

例如，在此 HTTP 動作定義中，`runtimeConfiguration.staticResult.name` 屬性會參考 `staticResults` 屬性內的 `HTTP0`，其中會定義動作的 mock 輸出。 `runtimeConfiguration.staticResult.staticResultOptions` 屬性會指定靜態結果設定是在 HTTP 動作上 `Enabled`。

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

HTTP 動作會傳回 `staticResults`內 `HTTP0` 定義中的輸出。 在此範例中，針對狀態碼，會 `OK`mock 輸出。 若為標頭值，則會 `"Content-Type": "application/JSON"`mock 輸出。 針對動作的狀態，模擬輸出為 `Succeeded`。

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

您也可以擁有在執行階段才存在的值。 若要表示這些值，您可以使用會在執行階段評估的「運算式」。 運算式是一個序列，可包含一或多個[函式](#functions)[運算子](#operators)、變數、明確值或常數。 在工作流程定義中，您可以在運算式前面加上 (\@) 符號，以便在 JSON 字串值中的任何地方使用運算式。 在評估代表 JSON 值的運算式時，移除 \@ 字元即可擷取運算式主體，而且一律會產生其他 JSON 值。

例如，對於先前定義的 `customerName` 屬性，您可以在運算式中使用 [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 函式來取得此屬性值，並將該值指派給 `accountName` 屬性：

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

「字串內插補點」也可讓您在字串內使用多個運算式，以 \@ 字元和大括號 ({}) 圍住。 語法如下：

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
| <*key-name*> | 是 | String | 輸出傳回值的索引鍵名稱 |
| <索引*鍵類型*> | 是 | int、float、string、securestring、bool、array、JSON 物件 | 輸出傳回值的類型 |
| <*key-value*> | 是 | 與 <*的索引鍵類型*相同> | 輸出傳回值 |
|||||

若要從工作流程執行取得輸出，請在 Azure 入口網站中檢查邏輯應用程式的執行歷程記錄和詳細資料，或使用[工作流程 REST API](https://docs.microsoft.com/rest/api/logic/workflows)。 您也可以將輸出傳遞至外部系統 (例如 PowerBI)，以便建立儀表板。

<a name="operators"></a>

## <a name="operators"></a>操作員

在[運算式](#expressions)和[函式](#functions)中，運算子會執行特定工作，例如參考屬性或陣列中的值。

| 運算子 | Task |
|----------|------|
| ' | 若要將字串常值作為輸入或使用於運算式和函式中，只用單引號圍住此字串，例如 `'<myString>'`。 請勿使用雙引號 ("")，這會與整個運算式的 JSON 格式設定發生衝突。 例如： <p>**是**：length('Hello') </br>**否**：length("Hello") <p>當您傳遞陣列或數字時，您不需要包圍標點符號。 例如： <p>**是**：length([1, 2, 3]) </br>**否**：length("[1, 2, 3]") |
| [] | 若要參考陣列中特定位置 (索引) 的值，請使用方括號。 例如，若要取得陣列中的第二個項目： <p>`myArray[1]` |
| 。 | 若要參考物件中的屬性，請使用點運算子。 例如，若要取得 `name` JSON 物件的 `customer` 屬性： <p>`"@parameters('customer').name"` |
| ? | 若要在不會發生執行階段錯誤的情況下，參考物件中的 null 屬性，請使用問號運算子。 例如，若要處理來自觸發程序的 null 輸出，您可以使用此運算式︰ <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

某些運算式會從執行工作流程定義開始時可能尚未存在的執行時間動作中取得其值。 若要在運算式中參考或使用這些值，您可以使用工作流程定義語言所提供的[*函式*](../logic-apps/workflow-definition-language-functions-reference.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解[工作流程定義語言動作和觸發程序](../logic-apps/logic-apps-workflow-actions-triggers.md)
* 深入了解如何透過 [Workflow REST API](https://docs.microsoft.com/rest/api/logic/workflows) 以程式設計方式建立及管理邏輯應用程式
