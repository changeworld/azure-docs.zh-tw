---
title: 對資料執行作業
description: 在 Azure Logic Apps 中轉換、管理和操作資料輸出和格式
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283936"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>在 Azure Logic Apps 中執行資料作業

本文說明如何藉由新增下列工作的動作，在您的邏輯應用程式中使用資料：

* 從陣列建立資料表。
* 根據條件從其他陣列建立陣列。
* 從 JavaScript 物件標記法 (JSON) 物件屬性建立易記的權杖，以便在您的工作流程中輕鬆使用這些屬性。

如果您在這裡找不到想要的動作，請嘗試流覽 Azure Logic Apps 所提供的許多不同[資料操作函數](../logic-apps/workflow-definition-language-functions-reference.md)。

這些資料表彙整了您可以使用的資料作業，並根據作業所操作的來源資料類型進行編排，但每項描述會依字母順序顯示。

**陣列動作** 

下列動作可協助您使用陣列中的資料。

| 動作 | 描述 |
|--------|-------------|
| [**建立 CSV 資料表**](#create-csv-table-action) | 從陣列建立逗號分隔值 (CSV) 資料表。 |
| [**建立 HTML 資料表**](#create-html-table-action) | 從陣列建立 HTML 資料表。 |
| [**篩選陣列**](#filter-array-action) | 根據指定的篩選條件或條件，從陣列建立陣列子集。 |
| [**聯結**](#join-action) | 從陣列中的所有項目建立字串，並使用指定的字元來分隔每個項目。 |
| [**選取**](#select-action) | 從不同陣列中所有項目的指定屬性建立陣列。 |
||| 

**JSON 動作**

下列動作可協助您使用 JavaScript 物件標記法 (JSON) 格式的資料。

| 動作 | 描述 |
|--------|-------------|
| [**撰寫**](#compose-action) | 從可具有各種資料類型的多個輸入建立訊息或字串。 其後，您可以使用此字串作為單一輸入，而無須重複輸入相同的內容。 例如，您可以從多種輸入建立單一 JSON 訊息。 |
| [**剖析 JSON**](#parse-json-action) | 為 JSON 內容中的屬性建立易記的權杖，以便在您的邏輯應用程式中輕鬆使用這些屬性。 |
|||

若要建立更複雜的 JSON 轉換，請參閱[使用 Liquid 範本執行進階的 JSON 轉換](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 需透過作業來使用資料的邏輯應用程式

  如果您不熟悉邏輯應用程式，請參閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 作為邏輯應用程式中第一個步驟的[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  資料作業必須以動作的形式執行，因此您必須先以觸發程序啟動邏輯應用程式，並納入任何建立您所需輸出必須執行的其他動作，才能使用這些動作。

<a name="compose-action"></a>

## <a name="compose-action"></a>撰寫動作

若要從多個輸入中，建立單一輸出（例如 JSON 物件），您可以使用**撰寫**動作。 您的輸入可具有多種不同類型，例如整數、布林值、陣列、JSON 物件，以及 Azure Logic Apps 所支援的任何其他原生類型，例如二進位檔與 XML。 接著，您可以在**撰寫**動作之後的動作中使用其輸出。 **撰寫**動作也可讓您免於在建立邏輯應用程式的工作流程時重複輸入相同的內容。

例如，您可以從多個變數建構 JSON 訊息，例如，以字串變數儲存人員的名字和姓氏，並以整數變數儲存人員的年齡。 在此，**撰寫**動作會接受下列輸入：

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

並建立下列輸出：

`{"age":35,"fullName":"Owens,Sophie"}`

若要試用範例，請使用 Logic App 設計工具按照下列步驟操作。 或者，如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**撰寫**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 撰寫](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和數個**初始化變數**動作。 這些動作依設定會建立兩個字串變數和一個整數變數。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動「撰寫」動作的範例邏輯應用程式](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. 在您要建立輸出的邏輯應用程式中，依照下列其中一個步驟操作： 

   * 若要在最後一個步驟底下新增動作，請選取 [**新增步驟**]。

     ![選取 [新增步驟] 以進行「撰寫」動作](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * 若要在步驟之間新增動作，請將滑鼠移至 [連接] 箭號上，如此就會出現加號（ **+** ）。 選取加號，然後選取 [**新增動作**]。

1. 在 [選擇動作] 底下的搜尋方塊中，輸入 `compose` 作為篩選條件。 從 [動作] 清單中，選取 [**撰寫**] 動作。

   ![選取 [撰寫] 動作](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. 在 [輸入] 方塊中，提供您要用來建立輸出的輸入。

   在此範例中，當您按一下 [輸入] 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取要用於「撰寫」動作的輸入](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   以下是完成的**撰寫**動作範例： 

   ![完成「撰寫」動作的範例](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[撰寫動作](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**撰寫**動作建立的結果是否符合預期，請將包含**撰寫**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**撰寫**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 動態內容清單開啟時，請在**撰寫**動作下方選取 [輸出]。

   這個範例會使用 [**傳送電子郵件**] 動作，並在電子郵件的本文和主旨中包含**輸出**欄位：

   ![「撰寫」動作的「輸出」欄位](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計工具工具列上，選取 [**執行**]。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![含有 [撰寫] 動作結果的電子郵件](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>建立 CSV 資料表動作

若要建立逗號分隔值（CSV）資料表，其具有陣列中 JavaScript 物件標記法（JSON）物件的屬性和值，請使用 [**建立 CSV 資料表**] 動作。 接著，您可以在**建立 CSV 資料表**動作之後的動作中使用產生的資料表。

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**建立 CSV 資料表**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 建立 CSV 資料表](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為陣列，含有 JSON 格式的一些屬性和值。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動「建立 CSV 資料表」動作的範例邏輯應用程式](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. 在您要建立 CSV 資料表的邏輯應用程式中，依照下列其中一個步驟操作： 

   * 若要在最後一個步驟底下新增動作，請選取 [**新增步驟**]。

     ![針對 [建立 CSV 資料表] 動作選取 [新增步驟]](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * 若要在步驟之間新增動作，請將滑鼠移至 [連接] 箭號上，如此就會出現加號（ **+** ）。 選取加號，然後選取 [**新增動作**]。

1. 在 [選擇動作] 底下的搜尋方塊中，輸入 `create csv table` 作為篩選條件。 從 [動作] 清單中，選取 [**建立 CSV 資料表**] 動作。

   ![選取 [建立 CSV 資料表] 動作](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. 在 [來源] 方塊中，提供您要用來建立資料表的陣列或運算式。

   在此範例中，當您按一下 [來源] 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取用來建立 CSV 資料表的陣列輸出](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > 若要為 JSON 物件中的屬性建立易記的權杖，以便您可以選取這些屬性作為輸入，請先使用[剖析 JSON](#parse-json-action)，再呼叫**建立 CSV 資料表**動作。

   以下是完成的**建立 CSV 資料表**動作範例： 

   ![[建立 CSV 資料表] 動作的完成範例](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

### <a name="customize-table-format"></a>自訂資料表格式

根據預設，[資料**行**] 屬性會設定為依據陣列專案自動建立資料表資料行。 若要指定自訂標頭和值，請遵循下列步驟：

1. 開啟 [資料**行**] 清單，然後選取 [**自訂**]。

1. 在 [**標頭**] 屬性中，指定要改用的自訂標頭文字。

1. 在 [**值**] 屬性中，指定要改用的自訂值。

若要從陣列傳回值，您可以使用[`item()` 函數](../logic-apps/workflow-definition-language-functions-reference.md#item)搭配 [**建立 CSV 資料表**] 動作。 在 `For_each` 迴圈中，您可以使用[`items()` 函數](../logic-apps/workflow-definition-language-functions-reference.md#items)。

例如，假設您想要只具有屬性值的資料表資料行，而不是陣列中的屬性名稱。 若只要傳回這些值，請遵循下列步驟，在設計工具或程式碼視圖中工作。 以下是此範例傳回的結果：

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>在設計工具視圖中工作

在動作中，將 [**標頭] 資料**行保留空白。 在 [值] 資料行中的每個資料列上，**取值**您想要的每個陣列屬性。 [**值**] 底下的每個資料列都會傳回指定之陣列屬性的所有值，並成為您資料表中的資料行。

1. 在 [**值**] 下，于您想要的每個資料列中，按一下編輯方塊內部，讓動態內容清單出現。

1. 在動態內容清單中，選取 [運算式]。

1. 在 [運算式編輯器] 中，輸入此運算式來指定您想要的陣列屬性值，然後選取 **[確定]** 。

   `item()?['<array-property-name>']`

   例如：

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![「建立 CSV 資料表」的取值參照「描述」](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. 針對您想要的每個陣列屬性重複上述步驟。 當您完成時，您的動作會如下列範例所示：

   !["Create CSV table" 中的 "item （）" 函數](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. 若要將運算式解析成更具描述性的版本，請切換至程式碼視圖並回到設計工具視圖，然後重新開啟折迭的動作：

   [**建立 CSV 資料表**] 動作現在會如下列範例所示：

   ![「建立 CSV 資料表」-已解析的運算式，沒有標頭](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>在程式碼視圖中工作

在動作的 JSON 定義中，于 `columns` 陣列中，將 `header` 屬性設定為空字串。 針對每個 `value` 屬性，取值所需的每個陣列屬性。

1. 在設計工具工具列上，選取 [程式**代碼視圖**]。

1. 在 [程式碼編輯器] 中，于動作的 `columns` 陣列中，為您想要的陣列值的每一個資料行加入空的 `header` 屬性和此 `value` 運算式：

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   例如：

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. 切換回設計工具視圖，然後重新開啟折迭的動作。

   [**建立 CSV 資料表**] 動作現在會如下列範例所示，而且運算式已解析成更具描述性的版本：

   ![「建立 CSV 資料表」-解析的運算式和沒有標頭](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[資料表動作](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**建立 CSV 資料表**動作建立的結果是否符合預期，請將包含**建立 CSV 資料表**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**建立 CSV 資料表**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 動態內容清單開啟時，請在**建立 CSV 資料表**動作下方選取 [輸出]。 

   此範例使用 Office 365 Outlook 的 [**傳送電子郵件**] 動作，並在電子郵件的本文中包含 [**輸出**] 欄位：

   ![[建立 CSV 資料表] 動作的 [輸出] 欄位](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計工具工具列上，選取 [**執行**]。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![含有 [建立 CSV 資料表] 動作結果的電子郵件](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>建立 HTML 資料表動作

若要從陣列中的 JavaScript 物件標記法（JSON）物件建立具有屬性和值的 HTML 資料表，請使用 [**建立 HTML 資料表**] 動作。 接著，您可以在**建立 HTML 資料表**動作之後的動作中使用產生的資料表。

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**建立 HTML 資料表**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 建立 HTML 資料表](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為陣列，含有 JSON 格式的一些屬性和值。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動「建立 HTML 資料表」的範例邏輯應用程式](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. 在您要建立 HTML 資料表的邏輯應用程式中，依照下列其中一個步驟操作：

   * 若要在最後一個步驟底下新增動作，請選取 [**新增步驟**]。

     ![針對 [建立 HTML 資料表] 動作選取 [新增步驟]](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * 若要在步驟之間新增動作，請將滑鼠移至 [連接] 箭號上，如此就會出現加號（ **+** ）。 選取加號，然後選取 [**新增動作**]。

1. 在 [選擇動作] 底下的搜尋方塊中，輸入 `create html table` 作為篩選條件。 從 [動作] 清單中，選取 [**建立 HTML 資料表**] 動作。

   ![選取 [建立 HTML 資料表] 動作](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. 在 [來源] 方塊中，提供您要用來建立資料表的陣列或運算式。

   在此範例中，當您按一下 [來源] 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取用來建立 HTML 資料表的陣列輸出](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > 若要為 JSON 物件中的屬性建立易記的權杖，以便您可以選取這些屬性作為輸入，請先使用[剖析 JSON](#parse-json-action)，再呼叫**建立 HTML 資料表**動作。

   以下是完成的**建立 HTML 資料表**動作範例：

   ![「建立 HTML 資料表」的完成範例](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

### <a name="customize-table-format"></a>自訂資料表格式

根據預設，[資料**行**] 屬性會設定為依據陣列專案自動建立資料表資料行。 若要指定自訂標頭和值，請遵循下列步驟：

1. 開啟 [資料**行**] 清單，然後選取 [**自訂**]。

1. 在 [**標頭**] 屬性中，指定要改用的自訂標頭文字。

1. 在 [**值**] 屬性中，指定要改用的自訂值。

若要從陣列傳回值，您可以使用[`item()` 函數](../logic-apps/workflow-definition-language-functions-reference.md#item)搭配 [**建立 HTML 資料表**] 動作。 在 `For_each` 迴圈中，您可以使用[`items()` 函數](../logic-apps/workflow-definition-language-functions-reference.md#items)。

例如，假設您想要只具有屬性值的資料表資料行，而不是陣列中的屬性名稱。 若只要傳回這些值，請遵循下列步驟，在設計工具或程式碼視圖中工作。 以下是此範例傳回的結果：

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>在設計工具視圖中工作

在動作中，將 [**標頭] 資料**行保留空白。 在 [值] 資料行中的每個資料列上，**取值**您想要的每個陣列屬性。 [**值**] 底下的每個資料列都會傳回指定之屬性的所有值，並成為您資料表中的資料行。

1. 在 [**值**] 下，于您想要的每個資料列中，按一下編輯方塊內部，讓動態內容清單出現。

1. 在動態內容清單中，選取 [運算式]。

1. 在 [運算式編輯器] 中，輸入此運算式來指定您想要的陣列屬性值，然後選取 **[確定]** 。

   `item()?['<array-property-name>']`

   例如：

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![[建立 HTML 資料表] 動作中的取值屬性](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. 針對您想要的每個陣列屬性重複上述步驟。 當您完成時，您的動作會如下列範例所示：

   !["Create HTML table" 中的 "item （）" 函數](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. 若要將運算式解析成更具描述性的版本，請切換至程式碼視圖並回到設計工具視圖，然後重新開啟折迭的動作：

   [**建立 HTML 資料表**] 動作現在會如下列範例所示：

   ![「建立 HTML 資料表」-已解析的運算式，沒有標頭](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>在程式碼視圖中工作

在動作的 JSON 定義中，于 `columns` 陣列中，將 `header` 屬性設定為空字串。 針對每個 `value` 屬性，取值所需的每個陣列屬性。

1. 在設計工具工具列上，選取 [程式**代碼視圖**]。

1. 在 [程式碼編輯器] 中，于動作的 `columns` 陣列中，為您想要的陣列值的每一個資料行加入空的 `header` 屬性和此 `value` 運算式：

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   例如：

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. 切換回設計工具視圖，然後重新開啟折迭的動作。

   [**建立 HTML 資料表**] 動作現在會如下列範例所示，而且運算式已解析成更具描述性的版本：

   ![「建立 HTML 資料表」-解析的運算式和沒有標頭](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[資料表動作](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**建立 HTML 資料表**動作建立的結果是否符合預期，請將包含**建立 HTML 資料表**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**建立 HTML 資料表**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 動態內容清單開啟時，請在**建立 HTML 資料表**動作下方選取 [輸出]。 

   此範例使用 Office 365 Outlook 的 [**傳送電子郵件**] 動作，並在電子郵件的本文中包含 [**輸出**] 欄位：

   ![「建立 HTML 資料表」的「輸出」欄位](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > 在電子郵件動作中包含 HTML 資料表輸出時，請確定您在電子郵件動作的進階選項中，將 [是 HTML] 屬性設定為 [是]。 如此，電子郵件動作即會正確格式化 HTML 資料表。

1. 現在，以手動執行您的邏輯應用程式。 在設計工具工具列上，選取 [**執行**]。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![具有「建立 HTML 資料表」結果的電子郵件](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>篩選陣列動作

若要建立一個較小的陣列，其中包含符合特定準則的專案，請從現有的陣列使用**篩選陣列**動作。 接著，您可以在**篩選陣列**動作之後的動作中使用已篩選的陣列。

> [!NOTE]
> 任何在您的條件中使用篩選文字，都會區分大小寫。 此外，此動作不可變更陣列中各個項目的格式或元件。 
> 
> 若要讓動作使用**篩選陣列**動作的陣列輸出，這些動作必須將陣列作為輸入，或者您可能必須將輸出陣列轉換為其他相容的格式。
> 
> 如果您呼叫 HTTP 端點並接收 JSON 回應，請使用**剖析 json**動作來處理 json 回應。 
> 否則，**篩選陣列**動作只能讀取回應主體，而不是 JSON 裝載的結構。

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**篩選陣列**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 篩選陣列](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為陣列，含有某些範例整數。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   > [!NOTE]
   > 雖然此範例使用簡單的整數陣列，但在可根據物件的屬性和值進行篩選的 JSON 物件陣列中，此動作將特別有用。

   ![啟動「篩選陣列」動作的範例邏輯應用程式](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. 在您要建立已篩選陣列的邏輯應用程式中，依照下列其中一個步驟操作： 

   * 若要在最後一個步驟底下新增動作，請選取 [**新增步驟**]。

     ![針對 [篩選陣列] 動作選取 [新增步驟]](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * 若要在步驟之間新增動作，請將滑鼠移至 [連接] 箭號上，如此就會出現加號（ **+** ）。 選取加號，然後選取 [**新增動作**]。

1. 在搜尋方塊中，輸入 `filter array` 作為篩選條件。 從 [動作] 清單中，選取 [**篩選陣列**] 動作。

   ![選取 [篩選陣列] 動作](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. 在 [來源] 方塊中，提供您要篩選的陣列或運算式。

   在此範例中，當您按一下 [來源] 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取用來建立已篩選陣列的陣列輸出](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. 針對條件，請指定要比較的陣列項目、選取比較運算子，並指定比較值。

   這個範例會使用 `item()` 函數來存取陣列中的每個專案，而**篩選陣列**動作會搜尋值大於1的陣列專案：

   ![「篩選陣列」動作的完成範例](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[查詢動作](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**篩選陣列**動作建立的結果是否符合預期，請將包含**篩選陣列**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**篩選陣列**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 當動態內容清單開啟時，選取 [**運算式**]。 若要取得**篩選陣列**動作的陣列輸出，請輸入此運算式，並在其中包含**篩選陣列**動作的名稱：

   `@actionBody('Filter_array')`

   此範例使用 Office 365 Outlook 的 [**傳送電子郵件**] 動作，並在電子郵件的本文中包含**actionBody （' Filter_array '）** 運算式的輸出：

   ![來自「篩選陣列」動作的動作輸出](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計工具工具列上，選取 [**執行**]。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![含有 [篩選陣列] 動作結果的電子郵件](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>聯結動作

若要建立具有陣列中所有專案的字串，並以特定分隔符號分隔這些專案，請使用**聯結**動作。 接著，您可以在**聯結**動作之後的動作中使用該字串。

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**聯結**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 聯結](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為陣列，含有某些範例整數。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動「聯結」動作的範例邏輯應用程式](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. 在您要從陣列建立字串的邏輯應用程式中，依照下列其中一個步驟操作：

   * 若要在最後一個步驟底下新增動作，請選取 [**新增步驟**]。

     ![SSelect 「加入」動作的「新增步驟」](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * 若要在步驟之間新增動作，請將滑鼠移至 [連接] 箭號上，如此就會出現加號（ **+** ）。 選取加號，然後選取 [**新增動作**]。

1. 在搜尋方塊中，輸入 `join` 作為篩選條件。 從 [動作] 清單中，選取此動作：**聯結**

   ![選取 [聯結] 動作](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. 在 [來源] 方塊中，提供您要聯結為字串的項目所屬的陣列。

   在此範例中，當您按一下 [來源] 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：  

   ![選取用來建立字串的陣列輸出](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. 在 [聯結對象] 方塊中，輸入要用來分隔每個陣列項目的字元。 

   此範例使用冒號 (:) 作為分隔符號。

   ![提供分隔符號字元](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[聯結動作](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**聯結**動作建立的結果是否符合預期，請將包含**聯結**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**聯結**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 動態內容清單開啟時，請在**聯結**動作下方選取 [輸出]。 

   此範例使用 Office 365 Outlook 的 [**傳送電子郵件**] 動作，並在電子郵件的本文中包含 [**輸出**] 欄位：

   !["Join" 動作的 [輸出] 欄位](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計工具工具列上，選取 [**執行**]。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![含有 [聯結] 動作結果的電子郵件](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>剖析 JSON 動作

若要參考或存取 JavaScript 物件標記法（JSON）內容中的屬性，您可以使用 [**剖析 JSON** ] 動作，為這些屬性建立使用者易記的欄位或權杖。 如此，您即可在指定邏輯應用程式的輸入時從動態內容清單中選取這些屬性。 針對此動作，您可以提供 JSON 結構描述，或從範例 JSON 內容或承載產生 JSON 結構描述。

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**剖析 JSON** 和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 剖析 JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為剖析 JSON，含有屬性和值。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動「剖析 JSON」動作的範例邏輯應用程式](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. 在您要剖析 JSON 內容的邏輯應用程式中，依照下列其中一個步驟操作：

   * 若要在最後一個步驟底下新增動作，請選取 [**新增步驟**]。

     ![針對 [剖析 JSON] 動作選取 [新增步驟]](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * 若要在步驟之間新增動作，請將滑鼠移至 [連接] 箭號上，如此就會出現加號（ **+** ）。 選取加號，然後選取 [**新增動作**]。

1. 在搜尋方塊中，輸入 `parse json` 作為篩選條件。 從 [動作] 清單中，選取 [**剖析 JSON** ] 動作。

   ![選取 [剖析 JSON] 動作](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. 在 [內容] 方塊中，提供您想要剖析的 JSON 內容。

   在此範例中，當您按一下 [內容] 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取剖析 JSON 動作的 JSON 物件](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. 輸入 JSON 結構描述以說明您要剖析的 JSON 內容。

   此範例中的 JSON 結構描述如下：

   ![為您要剖析的 JSON 物件提供 JSON 結構描述](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   如果您沒有結構描述，您可以從要剖析的 JSON 內容 (或*承載*) 產生該結構描述。 
   
   1. 在**剖析 JSON** 動作中，選取 [使用範例承載來產生結構描述]。

   1. 在 [**輸入或貼上範例 JSON**承載] 底下，提供 json 內容，然後選取 [**完成**]。

      ![輸入用來產生結構描述的 JSON 內容](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[剖析 JSON 動作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**剖析 JSON** 動作建立的結果是否符合預期，請將包含**剖析 JSON** 動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**剖析 JSON** 動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 現在，當動態內容清單開啟時，您可以在**剖析 JSON** 動作下方，從已剖析的 JSON 內容中選取屬性。

   此範例使用 Office 365 Outlook 的 [**傳送電子郵件**] 動作，並在電子郵件的本文中包含**FirstName**、 **LastName**和**email**欄位：

   ![[傳送電子郵件] 動作中的 JSON 屬性](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   以下是完成的電子郵件動作：

   ![完成電子郵件動作的範例](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計工具工具列上，選取 [**執行**]。 

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![具有「剖析 JSON」動作結果的電子郵件](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>選取動作

若要建立陣列，其中包含從現有陣列中的值建立的 JSON 物件，請使用**選取**動作。 例如，您可以為整數陣列中的每個值建立 JSON 物件，只要指定每個 JSON 物件所必須具有的屬性，並指定如何將來源陣列中的值對應至這些屬性即可。 此外，雖然您可以變更這些 JSON 物件中的元件，但輸出陣列的項目數一律會與來源陣列相同。

> [!NOTE]
> 若要讓動作使用**選取**動作的陣列輸出，這些動作必須將陣列作為輸入，或者您可能必須將輸出陣列轉換為其他相容的格式。 

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**選取**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 選取](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為陣列，含有某些範例整數。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動「選取」動作的範例邏輯應用程式](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. 在您要建立陣列的邏輯應用程式中，依照下列其中一個步驟操作： 

   * 若要在最後一個步驟底下新增動作，請選取 [**新增步驟**]。

     ![針對 [選取] 動作選取 [新增步驟]](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * 若要在步驟之間新增動作，請將滑鼠移至 [連接] 箭號上，如此就會出現加號（ **+** ）。 選取加號，然後選取 [**新增動作**]。

1. 在 [選擇動作] 底下，選取 [內建]。 在搜尋方塊中，輸入 `select` 作為篩選條件。 從 [動作] 清單中，選取 [**選取**] 動作。

   ![選取 [選取] 動作](./media/logic-apps-perform-data-operations/select-select-action.png)

1. 在 [來源] 方塊中，指定您要的來源陣列。

   在此範例中，當您按一下 [來源] 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取 [選取] 動作的來源陣列](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. 在 [對應] 方塊左側的資料行中，提供您要在來源陣列中指派各個值的屬性名稱。 在右側的資料行中指定運算式，代表您要指派給屬性的值。

   這個範例會在存取每個陣列專案的運算式中使用 `item()` 函數，指定 "Product_ID" 做為屬性名稱，以指派整數陣列中的每個值。 

   ![指定要建立陣列的 JSON 物件屬性和值](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   以下是完成的動作：

   ![「選取」動作的完成範例](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[選取動作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**選取**動作建立的結果是否符合預期，請將包含**選取**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**選取**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 當動態內容清單開啟時，選取 [**運算式**]。 若要取得**選取**動作的陣列輸出，請輸入此運算式，並在其中包含**選取**動作的名稱：

   `@actionBody('Select')`

   此範例使用 Office 365 Outlook 的 [**傳送電子郵件**] 動作，並在電子郵件的本文中包含來自 `@actionBody('Select')` 運算式的輸出：

   ![來自「選取」動作的動作輸出](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計工具工具列上，選取 [**執行**]。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![含有 [選取] 動作結果的電子郵件](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>後續步驟

* 了解[Logic Apps 連接器](../connectors/apis-list.md)
