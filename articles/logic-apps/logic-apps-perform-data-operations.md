---
title: 對資料執行操作
description: 在 Azure Logic Apps 中轉換、管理和操作資料輸出和格式
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283936"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>在 Azure Logic Apps 中執行資料作業

本文說明如何藉由新增下列工作的動作，在您的邏輯應用程式中使用資料：

* 從陣列建立資料表。
* 根據條件從其他陣列建立陣列。
* 從 JavaScript 物件標記法 (JSON) 物件屬性建立易記的權杖，以便在您的工作流程中輕鬆使用這些屬性。

如果在此處找不到所需的操作，請嘗試流覽 Azure 邏輯應用提供的多種[資料操作功能](../logic-apps/workflow-definition-language-functions-reference.md)。

這些資料表彙整了您可以使用的資料作業，並根據作業所操作的來源資料類型進行編排，但每項描述會依字母順序顯示。

**陣列動作** 

下列動作可協助您使用陣列中的資料。

| 動作 | 描述 |
|--------|-------------|
| [**建立 CSV 資料表**](#create-csv-table-action) | 從陣列建立逗號分隔值 (CSV) 資料表。 |
| [**創建 HTML 表**](#create-html-table-action) | 從陣列建立 HTML 資料表。 |
| [**篩選陣列**](#filter-array-action) | 根據指定的篩選條件或條件，從陣列建立陣列子集。 |
| [**加入**](#join-action) | 從陣列中的所有項目建立字串，並使用指定的字元來分隔每個項目。 |
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

  如果您對邏輯應用是新加入的，請查看[什麼是 Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)和["快速入門：創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)"。

* 作為邏輯應用程式中第一個步驟的[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  資料作業必須以動作的形式執行，因此您必須先以觸發程序啟動邏輯應用程式，並納入任何建立您所需輸出必須執行的其他動作，才能使用這些動作。

<a name="compose-action"></a>

## <a name="compose-action"></a>撰寫動作

要從多個輸入構造單個輸出（如 JSON 物件），可以使用 **"撰寫"** 操作。 您的輸入可具有多種不同類型，例如整數、布林值、陣列、JSON 物件，以及 Azure Logic Apps 所支援的任何其他原生類型，例如二進位檔與 XML。 接著，您可以在**撰寫**動作之後的動作中使用其輸出。 **撰寫**動作也可讓您免於在建立邏輯應用程式的工作流程時重複輸入相同的內容。

例如，您可以從多個變數建構 JSON 訊息，例如，以字串變數儲存人員的名字和姓氏，並以整數變數儲存人員的年齡。 在此，**撰寫**動作會接受下列輸入：

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

並建立下列輸出：

`{"age":35,"fullName":"Owens,Sophie"}`

若要試用範例，請使用 Logic App 設計工具按照下列步驟操作。 或者，如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**撰寫**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 撰寫](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和數個**初始化變數**動作。 這些動作依設定會建立兩個字串變數和一個整數變數。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動用於"組合"操作的示例邏輯應用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. 在您要建立輸出的邏輯應用程式中，依照下列其中一個步驟操作： 

   * 要在最後一步下添加操作，請選擇 **"新建步驟**"。

     ![為"組合"操作選擇"新步驟"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * 要在步驟之間添加操作，請將滑鼠移到連接箭頭上，以便出現加號**+**（ ） 。 選擇加號，然後選擇 **"添加操作**"。

1. 在 [選擇動作]**** 底下的搜尋方塊中，輸入 `compose` 作為篩選條件。 從動作清單中選擇 **"撰寫"** 操作。

   ![選取 [撰寫] 動作](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. 在 [輸入]**** 方塊中，提供您要用來建立輸出的輸入。

   在此範例中，當您按一下 [輸入]**** 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選擇要用於"合成"操作的輸入](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   以下是完成的**撰寫**動作範例： 

   ![已完成的"合成"操作示例](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[撰寫動作](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**撰寫**動作建立的結果是否符合預期，請將包含**撰寫**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**撰寫**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 動態內容清單開啟時，請在**撰寫**動作下方選取 [輸出]****。

   此示例使用 **"發送電子郵件**"操作，並在電子郵件正文和主題中包括 **"輸出**"欄位：

   !["組合"操作的"輸出"欄位](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計器工具列上，選擇 **"運行**"。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![含有 [撰寫] 動作結果的電子郵件](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>建立 CSV 資料表動作

要創建具有陣列中 JavaScript 物件符號 （JSON） 物件的屬性和值的逗號分隔值 （CSV） 表，請使用 **"創建 CSV 表"** 操作。 接著，您可以在**建立 CSV 資料表**動作之後的動作中使用產生的資料表。

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**建立 CSV 資料表**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 建立 CSV 資料表](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為陣列，含有 JSON 格式的一些屬性和值。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動用於"創建 CSV 表"操作的示例邏輯應用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. 在您要建立 CSV 資料表的邏輯應用程式中，依照下列其中一個步驟操作： 

   * 要在最後一步下添加操作，請選擇 **"新建步驟**"。

     ![為"創建 CSV 表"操作選擇"新步驟"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * 要在步驟之間添加操作，請將滑鼠移到連接箭頭上，以便出現加號**+**（ ） 。 選擇加號，然後選擇 **"添加操作**"。

1. 在 [選擇動作]**** 底下的搜尋方塊中，輸入 `create csv table` 作為篩選條件。 從動作清單中選擇 **"創建 CSV 表**操作"。

   ![選取 [建立 CSV 資料表] 動作](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. 在 [來源]**** 方塊中，提供您要用來建立資料表的陣列或運算式。

   在此範例中，當您按一下 [來源]**** 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取用來建立 CSV 資料表的陣列輸出](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > 若要為 JSON 物件中的屬性建立易記的權杖，以便您可以選取這些屬性作為輸入，請先使用[剖析 JSON](#parse-json-action)，再呼叫**建立 CSV 資料表**動作。

   以下是完成的**建立 CSV 資料表**動作範例： 

   ![已完成"創建 CSV 表"操作的示例](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

### <a name="customize-table-format"></a>自訂表格格式

預設情況下，**將"列"** 屬性設置為根據陣列項自動創建表列。 要指定自訂標頭和值，請按照以下步驟操作：

1. 打開**列**清單，然後選擇 **"自訂**"。

1. 在 **"標頭"** 屬性中，指定要使用的自訂標頭文本。

1. 在**Value**屬性中，指定要使用的自訂值。

要從陣列傳回值，[`item()`可以將函數](../logic-apps/workflow-definition-language-functions-reference.md#item)與創建**CSV 表**操作一起使用。 在迴圈`For_each`中，可以使用[`items()`函數](../logic-apps/workflow-definition-language-functions-reference.md#items)。

例如，假設您希望表列僅具有屬性值，而不是陣列中的屬性名稱。 要僅返回這些值，請按照以下步驟在設計器視圖或代碼視圖中工作。 下面是此示例返回的結果：

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>在設計器視圖中工作

在操作中，保持**標題**列為空。 在 **"值**"列中的每一行上，取消引用所需的每個陣列屬性。 **"值"** 下的每一行都返回指定陣列屬性的所有值，並成為表中的列。

1. 在 **"值**"下，在所需的每行上按一下編輯方塊，以便顯示動態內容清單。

1. 在動態內容清單中，選取 [運算式]****。

1. 在運算式編輯器中，輸入指定所需陣列屬性值的運算式，然後選擇 **"確定**"。

   `item()?['<array-property-name>']`

   例如：

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![取消"創建 CSV 表"的"描述"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. 對所需的每個陣列屬性重複上述步驟。 完成後，您的操作如下所示：

   !["創建 CSV 表"中的"專案（）"功能](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. 要將運算式解析為更具描述性的版本，請切換到代碼視圖並返回設計器視圖，然後重新打開折疊的操作：

   **"創建 CSV 表**"操作現在如下所示：

   !["創建 CSV 表" - 解析的運算式，無標頭](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>在代碼視圖中工作

在操作的 JSON 定義中，在`columns`陣列中，將`header`屬性設置為空字串。 對於每個`value`屬性，取消引用所需的每個陣列屬性。

1. 在設計器工具列上，選擇 **"代碼"視圖**。

1. 在代碼編輯器中，在操作的`columns`陣列中，為所需的陣列值`header`的每一列`value`添加空屬性和此運算式：

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

1. 切換回設計器視圖，然後重新打開折疊的操作。

   **"創建 CSV 表**"操作現在如下所示，運算式已解析為更具描述性的版本：

   !["創建 CSV 表" - 解析的運算式，無標頭](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[資料表動作](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**建立 CSV 資料表**動作建立的結果是否符合預期，請將包含**建立 CSV 資料表**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**建立 CSV 資料表**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 動態內容清單開啟時，請在**建立 CSV 資料表**動作下方選取 [輸出]****。 

   此示例使用 Office 365 Outlook**發送電子郵件**操作，並在電子郵件正文中包括 **"輸出"** 欄位：

   !["創建 CSV 表"操作的"輸出"欄位](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計器工具列上，選擇 **"運行**"。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![含有 [建立 CSV 資料表] 動作結果的電子郵件](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>建立 HTML 資料表動作

要創建具有陣列中 JavaScript 物件符號 （JSON） 物件的屬性和值的 HTML 表，請使用 **"創建 HTML 表"** 操作。 接著，您可以在**建立 HTML 資料表**動作之後的動作中使用產生的資料表。

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**建立 HTML 資料表**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 建立 HTML 資料表](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為陣列，含有 JSON 格式的一些屬性和值。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動"創建 HTML 表"的示例邏輯應用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. 在您要建立 HTML 資料表的邏輯應用程式中，依照下列其中一個步驟操作：

   * 要在最後一步下添加操作，請選擇 **"新建步驟**"。

     ![為"創建 HTML 表"操作選擇"新步驟"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * 要在步驟之間添加操作，請將滑鼠移到連接箭頭上，以便出現加號**+**（ ） 。 選擇加號，然後選擇 **"添加操作**"。

1. 在 [選擇動作]**** 底下的搜尋方塊中，輸入 `create html table` 作為篩選條件。 從動作清單中選擇 **"創建 HTML 表**"操作。

   ![選取 [建立 HTML 資料表] 動作](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. 在 [來源]**** 方塊中，提供您要用來建立資料表的陣列或運算式。

   在此範例中，當您按一下 [來源]**** 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取用來建立 HTML 資料表的陣列輸出](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > 若要為 JSON 物件中的屬性建立易記的權杖，以便您可以選取這些屬性作為輸入，請先使用[剖析 JSON](#parse-json-action)，再呼叫**建立 HTML 資料表**動作。

   以下是完成的**建立 HTML 資料表**動作範例：

   ![已完成的"創建 HTML 表"示例](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

### <a name="customize-table-format"></a>自訂表格格式

預設情況下，**將"列"** 屬性設置為根據陣列項自動創建表列。 要指定自訂標頭和值，請按照以下步驟操作：

1. 打開**列**清單，然後選擇 **"自訂**"。

1. 在 **"標頭"** 屬性中，指定要使用的自訂標頭文本。

1. 在**Value**屬性中，指定要使用的自訂值。

要從陣列傳回值，[`item()`可以將函數](../logic-apps/workflow-definition-language-functions-reference.md#item)與創建 HTML**表**操作一起使用。 在迴圈`For_each`中，可以使用[`items()`函數](../logic-apps/workflow-definition-language-functions-reference.md#items)。

例如，假設您希望表列僅具有屬性值，而不是陣列中的屬性名稱。 要僅返回這些值，請按照以下步驟在設計器視圖或代碼視圖中工作。 下面是此示例返回的結果：

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>在設計器視圖中工作

在操作中，保持**標題**列為空。 在 **"值**"列中的每一行上，取消引用所需的每個陣列屬性。 **"值"** 下的每一行都返回指定屬性的所有值，並成為表中的列。

1. 在 **"值**"下，在所需的每行上按一下編輯方塊，以便顯示動態內容清單。

1. 在動態內容清單中，選取 [運算式]****。

1. 在運算式編輯器中，輸入指定所需陣列屬性值的運算式，然後選擇 **"確定**"。

   `item()?['<array-property-name>']`

   例如：

   * `item()?['Description']`
   * `item()?['Product_ID']`

   !["創建 HTML 表"操作中的取消引用屬性](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. 對所需的每個陣列屬性重複上述步驟。 完成後，您的操作如下所示：

   !["創建 HTML 表"中的"專案（）"函數](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. 要將運算式解析為更具描述性的版本，請切換到代碼視圖並返回設計器視圖，然後重新打開折疊的操作：

   **"創建 HTML 表"** 操作現在如下所示：

   !["創建 HTML 表" - 解析的運算式，無標頭](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>在代碼視圖中工作

在操作的 JSON 定義中，在`columns`陣列中，將`header`屬性設置為空字串。 對於每個`value`屬性，取消引用所需的每個陣列屬性。

1. 在設計器工具列上，選擇 **"代碼"視圖**。

1. 在代碼編輯器中，在操作的`columns`陣列中，為所需的陣列值`header`的每一列`value`添加空屬性和此運算式：

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

1. 切換回設計器視圖，然後重新打開折疊的操作。

   **"創建 HTML 表"** 操作現在如下所示，運算式已解析為更具描述性的版本：

   !["創建 HTML 表" - 解析的運算式，無標頭](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[資料表動作](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**建立 HTML 資料表**動作建立的結果是否符合預期，請將包含**建立 HTML 資料表**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**建立 HTML 資料表**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 動態內容清單開啟時，請在**建立 HTML 資料表**動作下方選取 [輸出]****。 

   此示例使用 Office 365 Outlook**發送電子郵件**操作，並在電子郵件正文中包括 **"輸出"** 欄位：

   !["創建 HTML 表"的"輸出"欄位](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > 在電子郵件動作中包含 HTML 資料表輸出時，請確定您在電子郵件動作的進階選項中，將 [是 HTML]**** 屬性設定為 [是]****。 如此，電子郵件動作即會正確格式化 HTML 資料表。

1. 現在，以手動執行您的邏輯應用程式。 在設計器工具列上，選擇 **"運行**"。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![具有"創建 HTML 表"結果的電子郵件](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>篩選陣列動作

要從現有陣列創建具有滿足特定條件的項的較小陣列，請使用**篩選器陣列**操作。 接著，您可以在**篩選陣列**動作之後的動作中使用已篩選的陣列。

> [!NOTE]
> 任何在您的條件中使用篩選文字，都會區分大小寫。 此外，此動作不可變更陣列中各個項目的格式或元件。 
> 
> 若要讓動作使用**篩選陣列**動作的陣列輸出，這些動作必須將陣列作為輸入，或者您可能必須將輸出陣列轉換為其他相容的格式。
> 
> 如果調用 HTTP 終結點並接收 JSON 回應，請使用**Parse JSON**操作來處理 JSON 回應。 
> 否則，**篩選器陣列**操作只能讀取回應正文，而不能讀取 JSON 負載的結構。

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**篩選陣列**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 篩選陣列](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為陣列，含有某些範例整數。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   > [!NOTE]
   > 雖然此範例使用簡單的整數陣列，但在可根據物件的屬性和值進行篩選的 JSON 物件陣列中，此動作將特別有用。

   ![啟動"篩選陣列"操作的示例邏輯應用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. 在您要建立已篩選陣列的邏輯應用程式中，依照下列其中一個步驟操作： 

   * 要在最後一步下添加操作，請選擇 **"新建步驟**"。

     ![為"篩選陣列"操作選擇"新步驟"](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * 要在步驟之間添加操作，請將滑鼠移到連接箭頭上，以便出現加號**+**（ ） 。 選擇加號，然後選擇 **"添加操作**"。

1. 在搜尋方塊中，輸入 `filter array` 作為篩選條件。 從動作清單中選擇 **"篩選器"陣列**操作。

   ![選取 [篩選陣列] 動作](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. 在 [來源]**** 方塊中，提供您要篩選的陣列或運算式。

   在此範例中，當您按一下 [來源]**** 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取用來建立已篩選陣列的陣列輸出](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. 針對條件，請指定要比較的陣列項目、選取比較運算子，並指定比較值。

   本示例使用`item()`函數訪問陣列中的每個項，而 **"篩選器"陣列**操作搜索其值大於 1 的陣列項：

   ![已完成"篩選器陣列"操作示例](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[查詢動作](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**篩選陣列**動作建立的結果是否符合預期，請將包含**篩選陣列**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**篩選陣列**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 打開動態內容清單時，選擇 **"運算式**"。 若要取得**篩選陣列**動作的陣列輸出，請輸入此運算式，並在其中包含**篩選陣列**動作的名稱：

   `@actionBody('Filter_array')`

   此示例使用 Office 365 Outlook**發送電子郵件**操作，並在電子郵件正文中包括**操作正文（Filter_array'）** 運算式的輸出：

   !["篩選器陣列"操作的操作輸出](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計器工具列上，選擇 **"運行**"。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![含有 [篩選陣列] 動作結果的電子郵件](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>聯結動作

要創建包含陣列中所有項的字串，並使用具有特定分隔符號字元分隔這些專案，請使用 **"加入"** 操作。 接著，您可以在**聯結**動作之後的動作中使用該字串。

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**聯結**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 聯結](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為陣列，含有某些範例整數。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動用於"加入"操作的示例邏輯應用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. 在您要從陣列建立字串的邏輯應用程式中，依照下列其中一個步驟操作：

   * 要在最後一步下添加操作，請選擇 **"新建步驟**"。

     ![選擇"加入"操作的"新步驟"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * 要在步驟之間添加操作，請將滑鼠移到連接箭頭上，以便出現加號**+**（ ） 。 選擇加號，然後選擇 **"添加操作**"。

1. 在搜尋方塊中，輸入 `join` 作為篩選條件。 從 [動作] 清單中，選取此動作：**聯結**

   ![選擇"加入"操作](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. 在 [來源]**** 方塊中，提供您要聯結為字串的項目所屬的陣列。

   在此範例中，當您按一下 [來源]**** 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：  

   ![選取用來建立字串的陣列輸出](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. 在 [聯結對象]**** 方塊中，輸入要用來分隔每個陣列項目的字元。 

   此範例使用冒號 (:) 作為分隔符號。

   ![提供分隔符號字元](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[聯結動作](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**聯結**動作建立的結果是否符合預期，請將包含**聯結**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**聯結**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 動態內容清單開啟時，請在**聯結**動作下方選取 [輸出]****。 

   此示例使用 Office 365 Outlook**發送電子郵件**操作，並在電子郵件正文中包括 **"輸出"** 欄位：

   !["加入"操作的"輸出"欄位](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計器工具列上，選擇 **"運行**"。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![含有 [聯結] 動作結果的電子郵件](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>剖析 JSON 動作

要引用或訪問 JavaScript 物件標記法 （JSON） 內容中的屬性，可以使用**Parse JSON**操作為這些屬性創建方便使用的欄位或權杖。 如此，您即可在指定邏輯應用程式的輸入時從動態內容清單中選取這些屬性。 針對此動作，您可以提供 JSON 結構描述，或從範例 JSON 內容或承載產生 JSON 結構描述。

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**剖析 JSON** 和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 剖析 JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為剖析 JSON，含有屬性和值。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動用於"分析 JSON"操作的示例邏輯應用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. 在您要剖析 JSON 內容的邏輯應用程式中，依照下列其中一個步驟操作：

   * 要在最後一步下添加操作，請選擇 **"新建步驟**"。

     ![為"分析 JSON"操作選擇"新步驟"](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * 要在步驟之間添加操作，請將滑鼠移到連接箭頭上，以便出現加號**+**（ ） 。 選擇加號，然後選擇 **"添加操作**"。

1. 在搜尋方塊中，輸入 `parse json` 作為篩選條件。 從動作清單中選擇 **"分析 JSON"** 操作。

   ![選取 [剖析 JSON] 動作](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. 在 [內容]**** 方塊中，提供您想要剖析的 JSON 內容。

   在此範例中，當您按一下 [內容]**** 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取剖析 JSON 動作的 JSON 物件](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. 輸入 JSON 結構描述以說明您要剖析的 JSON 內容。

   此範例中的 JSON 結構描述如下：

   ![為您要剖析的 JSON 物件提供 JSON 結構描述](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   如果您沒有結構描述，您可以從要剖析的 JSON 內容 (或*承載*) 產生該結構描述。 
   
   1. 在**剖析 JSON** 動作中，選取 [使用範例承載來產生結構描述]****。

   1. 在 **"輸入或粘貼 JSON 有效負載示例**"下，提供 JSON 內容，然後選擇 **"完成**"。

      ![輸入用來產生結構描述的 JSON 內容](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[剖析 JSON 動作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**剖析 JSON** 動作建立的結果是否符合預期，請將包含**剖析 JSON** 動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**剖析 JSON** 動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 現在，當動態內容清單開啟時，您可以在**剖析 JSON** 動作下方，從已剖析的 JSON 內容中選取屬性。

   此示例使用 Office 365 Outlook**發送電子郵件**操作，並在電子郵件正文中包括 **"名字**、**姓氏**"和 **"電子郵件**"欄位：

   ![[傳送電子郵件] 動作中的 JSON 屬性](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   以下是完成的電子郵件動作：

   ![已完成的電子郵件操作示例](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計器工具列上，選擇 **"運行**"。 

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![具有"Parse JSON"操作結果的電子郵件](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>選取動作

要創建由現有陣列中的值生成 JSON 物件的陣列，請使用 **"選擇"** 操作。 例如，您可以為整數陣列中的每個值建立 JSON 物件，只要指定每個 JSON 物件所必須具有的屬性，並指定如何將來源陣列中的值對應至這些屬性即可。 此外，雖然您可以變更這些 JSON 物件中的元件，但輸出陣列的項目數一律會與來源陣列相同。

> [!NOTE]
> 若要讓動作使用**選取**動作的陣列輸出，這些動作必須將陣列作為輸入，或者您可能必須將輸出陣列轉換為其他相容的格式。 

如果您偏好在程式碼檢視編輯器中工作，您可以將本文中的範例**選取**和**初始化變數**動作定義複製到您自己的邏輯應用程式基礎工作流程定義中：[資料作業程式碼範例 - 選取](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

   此範例使用 Azure 入口網站和邏輯應用程式，並搭配**週期性**觸發程序和**初始化變數**動作。 此動作依設定會建立一個變數，且其初始值為陣列，含有某些範例整數。 當您稍後測試邏輯應用程式時，您可以直接手動執行應用程式，而無需等待觸發程序引發。

   ![啟動用於"選擇"操作的示例邏輯應用](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. 在您要建立陣列的邏輯應用程式中，依照下列其中一個步驟操作： 

   * 要在最後一步下添加操作，請選擇 **"新建步驟**"。

     ![為"選擇"操作選擇"新步驟"](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * 要在步驟之間添加操作，請將滑鼠移到連接箭頭上，以便出現加號**+**（ ） 。 選擇加號，然後選擇 **"添加操作**"。

1. 在 [選擇動作]**** 底下，選取 [內建]****。 在搜尋方塊中，輸入 `select` 作為篩選條件。 從動作清單中選擇 **"選擇**操作"。

   ![選取 [選取] 動作](./media/logic-apps-perform-data-operations/select-select-action.png)

1. 在 [來源]**** 方塊中，指定您要的來源陣列。

   在此範例中，當您按一下 [來源]**** 方塊內部時，動態內容清單隨即出現，以供您選取先前建立的變數：

   ![選取 [選取] 動作的來源陣列](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. 在 [對應]**** 方塊左側的資料行中，提供您要在來源陣列中指派各個值的屬性名稱。 在右側的資料行中指定運算式，代表您要指派給屬性的值。

   此示例指定"Product_ID"作為屬性名稱，通過使用訪問每個陣列項的運算式中的`item()`函數來分配整數陣列中的每個值。 

   ![指定 JSON 物件屬性和值以創建陣列](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   以下是完成的動作：

   ![已完成的"選擇"操作示例](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

如需與基礎工作流程定義中的這個動作有關的詳細資訊，請參閱[選取動作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

若要確認**選取**動作建立的結果是否符合預期，請將包含**選取**動作輸出的通知傳送給自己。

1. 在您的邏輯應用程式中，新增可將**選取**動作的結果傳送給您自己的動作。

1. 在該動作中，按一下您想要顯示結果的任何位置。 打開動態內容清單時，選擇 **"運算式**"。 若要取得**選取**動作的陣列輸出，請輸入此運算式，並在其中包含**選取**動作的名稱：

   `@actionBody('Select')`

   此示例使用 Office 365 Outlook**發送電子郵件**操作，並包括電子郵件正文中`@actionBody('Select')`運算式的輸出：

   !["選擇"操作的操作輸出](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. 現在，以手動執行您的邏輯應用程式。 在設計器工具列上，選擇 **"運行**"。

   根據您所使用的電子郵件連接器，以下是您所取得的結果：

   ![含有 [選取] 動作結果的電子郵件](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>後續步驟

* 了解[Logic Apps 連接器](../connectors/apis-list.md)
