---
title: 建立、編輯或擴充邏輯應用程式 JSON 工作流程定義
description: 如何在 Azure Logic Apps 中撰寫、編輯和擴充邏輯應用程式的 JSON 工作流程定義
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 0f5f01c757bf651beddaa76fc3eb8046b21b31eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75979384"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>在 Azure Logic Apps 中建立、編輯或擴充邏輯應用程式工作流程定義的 JSON

當您在 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 中使用自動化工作流程建立企業整合解決方案時，基礎邏輯應用程式定義會針對其描述和驗證，使用簡單的宣告式 JavaScript 物件標記法 (JSON) 及[工作流程定義語言 (WDL) 結構描述](../logic-apps/logic-apps-workflow-definition-language.md)。 這些格式讓邏輯應用程式定義更容易閱讀及了解，且無須很多程式碼方面的知識。
如果您想要自動化邏輯應用程式的建立及部署，您可以將邏輯應用程式定義納入 [Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md)中的 [Azure 資源](../azure-resource-manager/management/overview.md)。
然後您可以使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md) 或 [Azure Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/) 來建立、管理及部署邏輯應用程式。

若要以 JSON 編寫邏輯應用程式定義，可以在 Azure 入口網站或 Visual Studio 中開啟程式碼檢視編輯器，或將定義複製到任何您想要的編輯器中。
如果您還不熟悉邏輯應用程式，請先檢閱[如何建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

> [!NOTE]
> 有些 Azure Logic Apps 功能只支援 JSON，不支援 Logic Apps 設計工具，例如在邏輯應用程式定義中定義參數和多個觸發程序。
> 因此針對這些工作，您必須使用程式碼檢視或其他編輯器。

## <a name="edit-json---azure-portal"></a>編輯 JSON - Azure 入口網站

1. 登入 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>。

2. 在左側功能表中，選擇 [所有服務]****。
在搜尋方塊中尋找「邏輯應用程式」，然後從結果中選取您的邏輯應用程式。

3. 在邏輯應用程式功能表的 [開發工具]**** 底下，選取 [邏輯應用程式程式碼檢視]****。

   程式碼檢視編輯器隨即開啟，並以 JSON 格式顯示您的邏輯應用程式定義。

## <a name="edit-json---visual-studio"></a>編輯 JSON - Visual Studio

在 Visual Studio 中編寫邏輯應用程式定義之前，請先確定您已經[安裝必要的工具](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)。
若要使用 Visual Studio 建立邏輯應用程式，請檢閱[快速入門：使用 Azure Logic Apps 自動化工作和程序 - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

在 Visual Studio 中，您可以開啟直接從 Azure 入口網站建立或部署的邏輯應用程式，或開啟 Visual Studio 中以 Azure Resource Manager 專案形式建立或部署的邏輯應用程式。

1. 開啟包含邏輯應用程式的 Visual Studio 解決方案或 [Azure 資源群組](../azure-resource-manager/management/overview.md)專案。

2. 尋找並開啟邏輯應用程式的定義，根據預設，該定義在 [Resource Manager 範本](../azure-resource-manager/templates/overview.md)中會以 **LogicApp.json** 的名稱顯示。
您可以使用及自訂此範本，以部署至不同的環境。

3. 開啟邏輯應用程式定義和範本的捷徑功能表。
選取 [以邏輯應用程式設計工具開啟]****。

   ![開啟 Visual Studio 解決方案中的邏輯應用程式](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有最新的 Visual Studio 更新。

4. 在設計工具底部，選擇 [程式碼檢視]****。

   程式碼檢視編輯器隨即開啟，並以 JSON 格式顯示您的邏輯應用程式定義。

5. 若要返回設計工具檢視，請在程式碼檢視編輯器的底部，選擇 [設計]****。

## <a name="parameters"></a>參數

部署生命週期通常會有不同的環境來進行開發、測試、預備和生產。 如果您想要在沒有硬式編碼的情況下重複使用邏輯應用程式，或是根據您的部署需求而有所不同的值，您可以為工作流程定義建立[Azure Resource Manager 範本](../azure-resource-manager/management/overview.md)，如此一來，您也可以將邏輯應用程式部署自動化。

請遵循下列一般步驟*來參數*化或定義和使用這些值的參數。 接著，您可以在個別的參數檔案中提供值，將這些值傳遞至您的範本。 如此一來，您就可以更輕鬆地變更這些值，而不需要更新和重新部署邏輯應用程式。 如需完整詳細資料，請參閱[總覽：使用 Azure Resource Manager 範本自動部署邏輯應用程式](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。

1. 在您的範本中，定義範本參數和工作流程定義參數，分別接受部署和執行時間所使用的值。

   範本參數是在工作流程定義外的 parameters 區段中定義，而工作流程定義參數則是在工作流程定義內的 parameters 區段中定義。

1. 將硬式編碼的值取代為參考這些參數的運算式。 範本運算式會使用與工作流程定義運算式不同的語法。

   避免使用在部署時評估的範本運算式，在執行時間評估的工作流程定義運算式內，讓您的程式碼複雜化。 僅使用您工作流程定義以外的範本運算式。 僅使用工作流程定義內的工作流程定義運算式。

   當您指定工作流程定義參數的值時，您可以使用工作流程定義外的 parameters 區段，但仍在邏輯應用程式的資源定義中，來參考範本參數。 如此一來，您就可以將範本參數值傳遞至您的工作流程定義參數。

1. 將參數的值儲存在個別的[參數](../azure-resource-manager/templates/parameter-files.md)檔案中，並將該檔案包含在您的部署中。

## <a name="process-strings-with-functions"></a>使用函式處理字串

Logic Apps 具有各種函式可處理字串。
例如，假設您需要將公司名稱從訂單傳遞至另一個系統。
不過，您不確定字元編碼的正確處理方式。
您可以在這個字串上執行 base64 編碼，但若要避免在 URL 中逸出，您可改為取代數個字元。 此外，公司名稱只需要一個子字串，因為不會用到前五個字元。

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

下列步驟說明此範例從內部到外部處理這個字串的方式：

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. [`length()`](../logic-apps/logic-apps-workflow-definition-language.md)取得公司名稱的，讓您取得總字元數。

2. 若要取得較短的字串，請減去 `5`。

3. 現在取得[`substring()`](../logic-apps/logic-apps-workflow-definition-language.md)。
從索引 `5` 開始，並移至字串的其餘部分。

4. 將這個子字串轉換[`base64()`](../logic-apps/logic-apps-workflow-definition-language.md)成字串。

5. 現在[`replace()`](../logic-apps/logic-apps-workflow-definition-language.md)所有字元`+`都是`-`字元。

6. 最後， [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md)所有字元`/`都是`_`字元。

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>將清單項目對應到屬性值，然後使用對應作為參數

若要根據屬性值取得不同的結果，您可以建立可比對每個屬性值與結果的對應，然後使用該對應作為參數。

例如，此工作流程會將某些類別定義為參數，以及定義可比對這些類別與特定 URL 的對應。
首先，工作流程會取得文章清單。 接著，工作流程會使用此對應來尋找符合每篇文章之類別的 URL。

*   [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md)函式會檢查類別是否符合已知定義的分類。

*   取得相符的類別之後，此範例會使用方括號來提取對應中的項目：`parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>使用 Date 函式取得資料

若要從原生不支援「觸發程序」** 的資料來源取得資料，您可改為使用 Date 函式來處理時間和日期。
例如，這個運算式會尋找這個工作流程從內部到外部的步驟需要花費多少時間：

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. 從 `order` 動作，擷取 `startTime`。
2. 使用 `utcNow()` 取得目前時間。
3. 減去一秒：

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   您可以使用其他的時間單位，例如 `minutes` 或 `hours`。

3. 現在，您可以比較這兩個值。

   如果第一個值小於第二個值，則自從訂單最初提交以來已超過一秒。

若要將日期格式化，您可以使用字串格式器。 例如，若要取得 RFC1123，請使用[`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md)。
深入了解[日期格式化](../logic-apps/logic-apps-workflow-definition-language.md)。

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>後續步驟

* [根據條件 (條件陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [根據不同的值 (Switch 陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [執行並重複步驟 (迴圈)](../logic-apps/logic-apps-control-flow-loops.md)
* [執行或合併平行步驟 (分支)](../logic-apps/logic-apps-control-flow-branches.md)
* [根據分組的動作狀態執行步驟 (範圍)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* 深入了解 [Azure Logic Apps 的工作流程定義語言結構描述](../logic-apps/logic-apps-workflow-definition-language.md)
* 深入了解 [Azure Logic Apps 的工作流程動作和觸發程序](../logic-apps/logic-apps-workflow-actions-triggers.md)
