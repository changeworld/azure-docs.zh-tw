---
title: 依範圍群組和執行動作
description: 建立以 Azure Logic Apps 中的群組狀態為基礎執行的範圍動作
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: b84db69f79b1611347a4c55d929e5426141e7ac6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74791484"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>使用 Azure Logic Apps 中的範圍根據群組狀態執行動作

若要在另一個群組成功或失敗之後才執行步驟，請將那些步驟群組在「範圍」** 中。 如果您想要將動作組織為邏輯群組、評估該群組的狀態，以及執行以範圍狀態為基礎的動作，此結構將會十分實用。 當範圍中的所有動作都執行完成之後，範圍也會取得自己的狀態。 例如，您可以在想要實作[例外狀況和錯誤處理](../logic-apps/logic-apps-exception-handling.md#scopes)時使用範圍。 

若要檢查範圍的狀態，您可以使用判斷邏輯應用程式執行狀態時所用的相同準則，例如「Succeeded (成功)」、「Failed (失敗)」、「Cancelled (取消)」等。 根據預設，當範圍的所有動作都成功時，範圍的狀態會標示為「Succeeded (成功)」。 但如果範圍中有任何動作失敗或取消，範圍的狀態會標示為「Failed (失敗)」。 如需範圍的限制，請參閱[限制和設定](../logic-apps/logic-apps-limits-and-config.md)。 

例如，以下高階邏輯應用程式會使用範圍來執行特定動作，以及使用條件來檢查範圍的狀態。 如果範圍內的任何動作失敗或非預期地結束，範圍會分別標示為「失敗」或「中止」，而邏輯應用程式會傳送「範圍失敗」的訊息。 如果範圍內的所有動作都成功，則邏輯應用程式會傳送「範圍成功」的訊息。

![設定「排程 - 週期」觸發程序](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Prerequisites

若要遵循本文中的範例，您需要以下項目：

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 電子郵件帳戶 (來自任何 Logic Apps 所支援的電子郵件提供者)。 此範例使用 Outlook.com。 如果您使用不同的提供者，一般流程維持不變，但您的 UI 會有所不同。

* Bing 地圖金鑰。 若要取得此金鑰，請參閱<a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">取得 Bing 地圖金鑰</a>。

* [如何建立邏輯應用程式的](../logic-apps/quickstart-create-first-logic-app-workflow.md)基本知識

## <a name="create-sample-logic-app"></a>建立範例邏輯應用程式

首先，建立此範例邏輯應用程式，以便您可以在稍後新增範圍：

![建立範例邏輯應用程式](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* **排程 - 週期**觸發程序，以您指定的時間間隔檢查 Bing 地圖服務
* **Bing 地圖 - 取得路線**動作，檢查兩個位置之間的旅行時間
* 條件陳述式，檢查旅遊時間是否超過指定的旅行時間
* 動作，傳送電子郵件給您，告知您目前的旅行時間超過指定的時間

您可以隨時儲存您的邏輯應用程式，所以請經常儲存您的工作。

1. 如果尚未登入<a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>，請先登入。 建立空白邏輯應用程式。

1. 使用下列設定新增**排程 - 週期**觸發程序：**間隔** =「1」，以及**頻率** =「分鐘」

   ![設定「排程 - 週期」觸發程序](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > 若要以視覺化方式來簡化您的檢視，並在設計工具中隱藏每個動作的詳細資料，請在進行這些步驟時摺疊每個動作圖形。

1. 新增 [Bing 地圖 - 取得路線]**** 動作。 

   1. 如果您尚未與 Bing 地圖連線，系統會要求您建立連線。

      | 設定 | 值 | 描述 |
      | ------- | ----- | ----------- |
      | **連線名稱** | BingMapsConnection | 為您的連線提供一個名稱。 | 
      | **API 金鑰** | <*您的-Bing 對應鍵*> | 輸入您先前收到的 Bing 地圖服務金鑰。 | 
      ||||  

   1. 設定 [取得路線]**** 動作，如下圖中的資料表所示：

      ![設定 [Bing 地圖服務 - 取得路線] 動作](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      如需這些參數的詳細資訊，請參閱[計算路線](https://msdn.microsoft.com/library/ff701717.aspx)。

      | 設定 | 值 | 描述 |
      | ------- | ----- | ----------- |
      | **導航點 1** | <*「*> | 輸入您的路線起點。 | 
      | **導航點 2** | <*成品*> | 輸入您路線的目的地。 | 
      | **避開** | None | 輸入路線所要避開的項目，例如高速公路、收費站等等。 如需可能使用的值，請參閱[計算路線](https://msdn.microsoft.com/library/ff701717.aspx)。 | 
      | **最優化** | timeWithTraffic | 選取可將路線最佳化的參數，例如距離、和使用目前交通資訊的時間等等。 此範例會使用此值："timeWithTraffic" | 
      | **距離單位** | <*您的喜好設定*> | 輸入要計算您路線的距離單位。 此範例會使用此值：「英里」 | 
      | **行進模式** | 開車 | 輸入路線的旅行模式。 此範例會使用此值：「開車」 | 
      | **運輸日期時間** | None | 僅適用於運輸模式。 | 
      | **運輸日期類型** | None | 僅適用於運輸模式。 | 
      ||||  

1. [新增條件](../logic-apps/logic-apps-control-flow-conditional-statement.md)以檢查目前交通下的旅行時間是否超過指定時間。 
   針對此範例，請遵循下列步驟：

   1. 以此描述重新命名條件：**如果交通時間超過指定時間**

   1. 在最左邊的資料行中，按一下 [選擇值]**** 方塊，即可顯示動態內容清單。 從清單中，選取 [旅行期間交通]**** 欄位 (以秒為單位)。 

      ![建置條件](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. 在中間的方塊中，選取此運算子：**大於**

   1. 在最右邊的資料行中，輸入此比較值（以秒為單位），相當於10分鐘： **600**

      當您完成時，您的條件看起來就像下面這個範例︰

      ![已完成的條件](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. 在 [若為 true]**** 分支中，新增適用於您電子郵件提供者的「傳送電子郵件」動作。 
   請遵循此影像下的步驟來設定此動作：

   ![將「傳送電子郵件」動作新增至「若為 true」分支](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. 在 [收件者]**** 欄位中，輸入您的電子郵件地址以作為測試用。

   1. 在 [主旨]**** 欄位中，輸入下列文字：

      ```Time to leave: Traffic more than 10 minutes```

   1. 在 [內文]**** 欄位中，輸入此文字並加上尾端空格： 

      ```Travel time:```

      當您的游標出現在 [內文]**** 欄位時，動態內容清單會保持開啟狀態，因此您可以選取任何目前可用的參數。

   1. 在動態內容清單中，選擇 [運算式]****。

   1. 尋找並選取 **div( )** 函式。 
      將游標置於函式的括號內。

   1. 當游標位於函式的括號內時，選擇 [動態內容]****，動態內容清單會隨即顯示。 
   
   1. 從 [取得路線]**** 區段中，選取 [旅行期間交通]**** 欄位。

      ![選取 [旅行期間交通] 欄位](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. 在欄位解析為 JSON 格式之後，請新增**逗號**（```,```），後面接著數位```60``` ，以便將**流量持續時間流量**中的值轉換成分鐘。 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      您的運算式現在看起來就像下面這個範例︰

      ![完成運算式](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. 完成時，選擇 [確定]****。

   <!-- markdownlint-disable MD038 -->
   1. 在運算式解析之後，請將下列文字加上前置空格：``` minutes```
  
       您的 [內文]**** 欄位現在看起來就像下面這個範例︰

       ![完成的 [內文] 欄位](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. 儲存您的邏輯應用程式。

接下來，新增範圍，讓您可以群組特定動作，並評估其狀態。

## <a name="add-a-scope"></a>新增範圍

1. 如果您還沒開啟邏輯應用程式設計工具中的邏輯應用程式，請將它開啟。 

1. 在要使用的工作流程位置上新增範圍。 例如，若要在邏輯應用程式工作流程中的現有步驟之間新增範圍，請遵循下列步驟： 

   1. 將指標停留在要新增範圍的箭號上方。 
   選擇**加號**（**+**） >**新增動作**]。

      ![新增範圍](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. 在搜尋方塊中，輸入「範圍」作為篩選條件。 
   選取 [範圍]**** 動作。

## <a name="add-steps-to-scope"></a>將步驟新增至範圍

1. 現在可新增步驟，或拖曳您想要在範圍內執行的現有步驟。 針對此範例，拖曳以下動作至範圍中：
      
   * **取得路由**
   * **如果交通時間超過指定時間**，其中需同時包含 **true** 和 **false** 分支

   您的邏輯應用程式現在看起來就像下面這個範例︰

   ![新增的範圍](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. 在範圍下，新增會檢查範圍狀態的條件。 以下列描述為條件重新命名：**如果範圍失敗**

   ![新增條件來檢查範圍狀態](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. 在條件中新增這些運算式，以檢查範圍狀態是否等於「失敗」或「中止」。 

   1. 若要新增另一個資料列，請選擇 [新增]****。 

   1. 在每一列的左側方塊中按一下，即會顯示動態內容清單。 
   從動態內容清單中，選擇 [運算式]****。 在 [編輯] 方塊中，輸入此運算式，然後選擇 [確定]****： 
   
      `result('Scope')[0]['status']`

      ![新增會檢查範圍狀態的運算式](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. 針對這兩個資料列，選取 [等於]**** 作為運算子。 
   
   1. 針對比較值，在第一列中輸入 `Failed`。 
   在第二列中輸入 `Aborted`。 

      當您完成時，您的條件看起來就像下面這個範例︰

      ![新增會檢查範圍狀態的運算式](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      現在，設定條件的 `runAfter` 屬性，使得條件會檢查範圍狀態，並執行後面步驟中定義的比對動作。

   1. 在 [如果範圍失敗]**** 條件中，選擇**省略符號** (...) 按鈕，然後再選擇 [設定執行後續動作]****。

      ![設定 `runAfter` 屬性](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. 選取所有這些範圍狀態：**成功**、**失敗**、**略過**和**逾時**

      ![選取範圍狀態](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. 完成之後，選擇 [完成]****。 
   條件現在會顯示「資訊」圖示。

1. 在 [若為 true]**** 和 [若為 false]**** 的分支中，新增您想根據每個範圍狀態執行的動作，例如傳送電子郵件或訊息。

   ![新增要根據範圍狀態採取的動作](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. 儲存您的邏輯應用程式。

已完成的邏輯應用程式現在看起來就像此範例︰

![具有範圍的已完成邏輯應用程式](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>儲存您的工作

在設計工具的工具列上，選擇 [執行]****。 如果範圍內的所有動作都成功，您會取得「範圍成功」的訊息。 如果範圍內有任何動作失敗，您會取得「範圍失敗」的訊息。 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON 定義

如果您是在程式碼檢視中進行作業，您可以改為以邏輯應用程式的 JSON 定義來定義範圍結構。 例如，下列是前面邏輯應用程式中觸發程序和動作的 JSON 定義：

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交或票選功能與建議，請造訪 [Azure Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* [根據條件 (條件陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [根據不同的值 (Switch 陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [執行並重複步驟 (迴圈)](../logic-apps/logic-apps-control-flow-loops.md)
* [執行或合併平行步驟 (分支)](../logic-apps/logic-apps-control-flow-branches.md)
