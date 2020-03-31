---
title: 創建和管理用於存儲和傳遞值的變數
description: 瞭解如何使用使用 Azure 邏輯應用創建的自動化任務和工作流中的變數來存儲、管理、使用和傳遞值
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 55984082a6b287e9f7cdca005a24ef3c18032491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456690"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>使用 Azure 邏輯應用中的變數存儲和管理值

本文演示如何創建和使用用於在邏輯應用中存儲值的變數。 例如，變數可以説明您跟蹤迴圈運行的次數。 要反覆運算陣列或檢查特定項的陣列，可以使用變數引用每個陣列項的索引編號。

您可以為整數、浮點數、布林值、字串、陣列及物件等資料類型建立變數。 建立變數之後，您可以執行其他工作，例如：

* 取得或參考變數的值。
* 以常數值將變數增大或減小，也稱為「遞增」** 和「遞減」**。
* 為變數指派不同的值。
* 在字串或陣列中插入或「附加」** 變數的值作為最後一個項目。

變數只存在於建立它們的邏輯應用程式執行個體內，並且是全域性。 此外，它們只會在邏輯應用程式執行個體內跨任何迴圈反覆項目存留。 引用變數時，請使用變數的名稱作為標記，而不是操作的名稱，這是引用操作輸出的常用方法。

> [!IMPORTANT]
> 預設情況下，在"每個"迴圈中迴圈並行運行。 在迴圈中使用變數時，[按順序](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)運行迴圈，以便變數返回可預測的結果。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果沒有訂閱，[請註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 要創建變數的邏輯應用

  如果您對邏輯應用是新加入的，請查看[什麼是 Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)和["快速入門：創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)"。

* 作為邏輯應用程式中第一個步驟的[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)

  您的邏輯應用程式必須以觸發程序作為開頭，您才能新增用於建立和使用變數的動作。

<a name="create-variable"></a>

## <a name="initialize-variable"></a>將變數初始化

您可以建立變數並宣告其資料類型和初始值 - 全都在邏輯應用程式中的一個動作內完成。 您只能在全域層級宣告變數，而無法在範圍、條件及迴圈內宣告變數。

1. 在[Azure 門戶](https://portal.azure.com)或視覺化工作室中，在邏輯應用設計器中打開邏輯應用。

   此範例使用 Azure 入口網站和邏輯應用程式搭配現有的觸發程序。

1. 在邏輯應用程式中您想要新增變數的步驟底下，依照下列其中一個步驟進行操作： 

   * 要在最後一步下添加操作，請選擇 **"新建步驟**"。

     ![新增動作](./media/logic-apps-create-variables-store-values/add-action.png)

   * 要在步驟之間添加操作，請將滑鼠移到連接箭頭上，以便出現加號**+**（ ）。 選擇加號，然後選擇 **"添加操作**"。

1. 在 [選擇動作]**** 底下的搜尋方塊中，輸入 `variables` 作為篩選條件。 從動作清單中選擇 **"初始化變數**"。

   ![選取動作](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. 如下所述，提供有關變數的資訊：

   | 屬性 | 必要 | 值 |  描述 |
   |----------|----------|-------|--------------|
   | **名稱** | 是 | <*變數名稱*> | 要遞增的變數名稱 |
   | **類型** | 是 | <*變數類型*> | 變數的資料類型 |
   | **價值** | 否 | <*起始值*> | 您的變數初始值 <p><p>**提示**：雖然此值為選擇性，但最佳做法是設定此值，如此您便一律知道變數的開始值。 |
   |||||

   例如：

   ![將變數初始化](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. 現在繼續新增您想要的動作。 完成後，在設計器工具列上，選擇 **"保存**"。

如果從設計器切換到代碼視圖編輯器，下面是**初始化變數**操作在邏輯應用定義中顯示的方式，該定義採用 JavaScript 物件標記法 （JSON） 格式：

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

> [!NOTE]
> 儘管**初始化變數**操作具有一個`variables`以陣列結構的節，但該操作一次只能創建一個變數。 每個新變數都需要單個**初始化變數**操作。

以下是一些其他變數類型的範例：

*字串變數*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*布林值變數*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*含整數的陣列*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*含字串的陣列*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>取得變數的值

若要擷取或參考變數的內容，您也可以在「邏輯應用程式設計工具」和程式碼檢視編輯器中使用 [variables() 函式](../logic-apps/workflow-definition-language-functions-reference.md#variables)。 參考變數時，請使用變數的名稱作為語彙基元，而不是使用動作的名稱，動作名稱是參考動作輸出的實用方式。

例如，此運算式使用`variables()`函數從[本文中以前創建的](#append-value)陣列變數中獲取項。 函數`string()`以字串格式返回變數的內容：`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>遞增變數 

要按常量值增加或*遞增*變數，請向邏輯應用添加**增量變數**操作。 此動作僅適用於整數和浮點數變數。

1. 在邏輯應用設計器中，在要增加現有變數的步驟下，選擇 **"新建步驟**"。 

   例如，此邏輯應用程式已經有一個已建立變數的觸發程序和動作。 因此，請在這些步驟底下新增動作：

   ![新增動作](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   若要在現有步驟之間新增動作，請將滑鼠移至連接箭頭上，如此就會出現加號 (+)。 選擇加號，然後選擇 **"添加操作**"。

1. 在搜尋方塊中，輸入 "increment variable" 作為篩選條件。 在動作清單中，選擇**增量變數**。

   ![選取 [遞增變數] 動作](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. 提供下列資訊來遞增您的變數：

   | 屬性 | 必要 | 值 |  描述 |
   |----------|----------|-------|--------------|
   | **名稱** | 是 | <*變數名稱*> | 要遞增的變數名稱 |
   | **價值** | 否 | <*增量值*> | 用來遞增變數的值。 預設值為一。 <p><p>**提示**：雖然此值為選擇性，但最佳做法是設定此值，如此您便一律知道用來遞增變數的特定值。 |
   ||||

   例如：

   ![遞增值範例](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. 完成後，在設計器工具列上，選擇 **"保存**"。

如果從設計器切換到代碼視圖編輯器，以下是**增量變數**操作在邏輯應用定義中顯示的方式，該定義採用 JSON 格式：

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>範例：建立迴圈計數器

變數通常用來計算迴圈的執行次數。 此範例藉由建立一個會計算電子郵件中附件的迴圈，說明如何建立及使用變數來進行此工作。

1. 在 Azure 入口網站中，建立空白的邏輯應用程式。 新增一個會檢查是否有新電子郵件及任何附件的觸發程序。

   此範例使用 Office 365 Outlook 的 [收到新的電子郵件時]**** 觸發程序。 您可以將此觸發程序設定成只有當電子郵件含有附件時才觸發。 不過，您可以使用 Outlook.com 之類的連接器，檢查是否有含附件的新電子郵件。

1. 在觸發器中，要檢查附件並將這些附件傳遞到邏輯應用的工作流中，請為以下屬性選擇 **"是**"：

   * **具有附件**
   * **包括附件**

   ![檢查是否有附件並包含附件](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. 新增 [[初始化變數]**** 動作](#create-variable)。 創建名為具有`Count`零起始值的整數變數。

   ![新增 [初始化變數] 的動作](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. 要迴圈流覽每個附件，*為每個*迴圈添加 一個。

   1. 在 **"初始化變數**操作"下，選擇 **"新建步驟**"。

   1. 在 [選擇動作]**** 底下，選取 [內建]****。 在搜索框中，輸入`for each`為搜索篩選器，然後選擇 **"為每個**"。

      ![新增 [For each] 迴圈](./media/logic-apps-create-variables-store-values/add-loop.png)

1. 在迴圈中，按一下 [選取先前步驟中的輸出]**** 方塊內部。 當動態內容清單出現時，選取 [附件]****。

   ![取得「附件」](./media/logic-apps-create-variables-store-values/select-attachments.png)

   **附件**屬性將包含來自觸發器輸出的電子郵件附件的陣列傳遞到迴圈中。

1. 在 **"對於每個迴圈"** 中，選擇 **"添加操作**"。

   ![選取 [新增動作]](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. 在搜尋方塊中，輸入 "increment variable" 作為篩選條件。 從動作清單中，選擇**增量變數**。

   > [!NOTE]
   > 確保**增量變數**操作顯示在迴圈中。 如果此動作出現在迴圈外，請將此動作拖曳到迴圈中。

1. 在 [遞增變數]**** 動作中，從 [名稱]**** 清單中，選取 [Count]**** 變數。

   ![選取 [Count] 變數](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. 在迴圈底下，新增任何會將附件數目傳送給您的動作。 在您的動作中，包含來自 **Count** 變數的值，例如：

   ![新增會傳送結果的動作](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 如果邏輯應用未啟用，請在邏輯應用功能表上選擇 **"概述**"。 在工具列上，選擇 **"啟用**"。

1. 在邏輯應用設計器工具列上，選擇 **"運行**"。 此步驟會手動啟動您的邏輯應用程式。

1. 將一封含有一或多個附件的電子郵件傳送給您在此範例中使用的電子郵件帳戶。

   此步驟會觸發邏輯應用程式的觸發程序，它會為邏輯應用程式的工作流程建立並執行一個執行個體。 因此，邏輯應用程式會傳送訊息或電子郵件給您，當中顯示您所傳送電子郵件中的附件數目。

如果從設計器切換到代碼視圖編輯器，下面是**For 每個**迴圈與邏輯應用定義中的**增量變數**操作一起顯示的方式，該操作採用 JSON 格式。

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>遞減變數

要按常量值減小或*遞減*變數，請按照[增加變數](#increment-value)的步驟操作，但查找並選擇 **"遞減"變數**操作。 此動作僅適用於整數和浮點數變數。

以下是 [遞減變數]**** 動作的屬性：

| 屬性 | 必要 | 值 |  描述 |
|----------|----------|-------|--------------|
| **名稱** | 是 | <*變數名稱*> | 要遞減的變數名稱 | 
| **價值** | 否 | <*增量值*> | 用來遞減變數的值。 預設值為一。 <p><p>**提示**：雖然此值為選擇性，但最佳做法是設定此值，如此您便一律知道用來遞減變數的特定值。 |
||||| 

如果從設計器切換到代碼視圖編輯器，下面是 **"遞減"變數**操作在邏輯應用定義（以 JSON 格式）中顯示的方式。

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

<a name="assign-value"></a>

## <a name="set-variable"></a>設定變數

若要為現有變數指派不同的值，請依照[將變數增大](#increment-value)的步驟進行操作，只是其中要：

1. 查找並選擇 **"設置變數**操作"。

1. 提供變數名稱及您想要指派的值。 新的值和變數必須具有相同的資料類型。 必須提供值，因為此動作沒有預設值。

以下是 [設定變數]**** 動作的屬性：

| 屬性 | 必要 | 值 |  描述 |
|----------|----------|-------|--------------|
| **名稱** | 是 | <*變數名稱*> | 要變更的變數名稱 |
| **價值** | 是 | <*新值*> | 您想要指派給變數的值。 兩者必須具有相同的資料類型。 |
||||| 

> [!NOTE]
> 除非您是要將變數增大或減小，否則變更迴圈內的變數「可能」** 會造成非預期的結果，因為迴圈預設會以平行方式或並行方式執行。 針對這些情況，請嘗試將迴圈設定成以循序方式執行。 例如，當您想要參考迴圈內的變數值，並預期該迴圈執行個體的開始值和結束值相同時，請依照下列步驟來變更迴圈的執行方式： 
>
> 1. 在迴圈的右上角，選擇省略號 **（...）** 按鈕，然後選擇 **"設置**"。
> 
> 2. 在 [並行控制]**** 底下，將 [覆寫預設]**** 設定變更為 [開啟]****。
>
> 3. 將 [平行處理原則的程度]**** 滑桿拖曳至 [1]****。

如果從設計器切換到代碼視圖編輯器，下面是**Set 變數**操作顯示在邏輯應用定義（以 JSON 格式）中的方式。 此示例將`Count`變數的當前值更改為另一個值。

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>附加至變數

針對儲存字串或陣列的變數，您可以插入或「附加」** 變數的值作為這些字串或陣列的最後一個項目。 您可以依照[將變數增大](#increment-value)的步驟進行操作，只是其中要改為採用下列步驟： 

1. 視您的變數是字串還是陣列而定，尋找並選取下列其中一個動作： 

   * **字串變數的追加**
   * **追加到陣列變數** 

1. 提供要附加作為字串或陣列最後一個項目的值。 這是必要的值。

以下是 [附加至...]**** 動作的屬性：

| 屬性 | 必要 | 值 |  描述 |
|----------|----------|-------|--------------|
| **名稱** | 是 | <*變數名稱*> | 要變更的變數名稱 |
| **價值** | 是 | <*追加值*> | 您想要附加的值 (可以具有任何類型) |
|||||

如果從設計器切換到代碼視圖編輯器，下面是 **"追加到陣列變數操作"** 顯示在邏輯應用定義（以 JSON 格式）中的方式。 此範例會建立一個陣列變數，然後將另一個值新增為陣列中的最後一個項目。 結果會是一個含有下列陣列的已更新變數：`[1,2,3,"red"]`

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="next-steps"></a>後續步驟

* 了解[Logic Apps 連接器](../connectors/apis-list.md)
