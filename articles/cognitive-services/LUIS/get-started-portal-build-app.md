---
title: 快速入門：在 LUIS 入口網站中建立新的應用程式
description: 在本快速入門中，您會建立應用程式、意圖和實體的基本部分，以及在 LUIS 入口網站中使用範例語句進行測試。
ms.topic: quickstart
ms.date: 03/24/2020
ms.openlocfilehash: f0c8f0c77f832e049dfc494f82e90edb61a8cb2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80244609"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>快速入門：在 LUIS 入口網站中建立新的應用程式

在本快速入門中，您會在 LUIS 入口網站中建立新的應用程式。 受先，建立應用程式、**意圖**和**實體**的基本部分。 然後，藉由在互動式測試面板中提供範例使用者語句進行測試，以取得預測意圖。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>建立應用程式

1. 從內容工具列中選取 [+ 新增對話應用程式]  ，然後選取 [新增對話應用程式]  。

    > [!div class="mx-imgBorder"]
    > [![在 LUIS 入口網站中建立新的應用程式](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. 在快顯視窗中，使用下列設定進行應用程式設定，然後選取 [完成]  。

   |設定名稱| 值 | 目的|
   |--|--|--|
   |名稱|`myEnglishApp`|唯一的 LUIS 應用程式名稱<br>required|
   |文化特性|**英文**|使用者語句的語言 **en-us**<br>required|
   |說明 (選擇性)|`App made with LUIS Portal`|應用程式的描述<br>選用|
   |預測資源 (選擇性) |-  |請勿選取。 LUIS 提供入門金鑰，可免費用於製作 1000 個預測端點要求。 |

   ![輸入新的應用程式設定](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>建議意圖

建立 LUIS 應用程式之後，需要建立意圖。 意圖是對使用者的文字進行分類的方式之一。 例如，人力資源應用程式可能會有兩個函式。 功能是幫助人達成以下目標：

 1. 尋找和應徵工作
 1. 找到應徵工作的表單

應用程式的兩個不同「目的」  均符合下列意圖：

|Intent|使用者的文字範例<br>稱為_語句_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

若要建立意圖，請完成下列步驟：

1. 應用程式建立後，您會在 [建置]  區段的 [意圖]  頁面上。 選取 [建立]  。

   [![選取建立以建立新的意圖](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. 輸入意圖名稱 `FindForm`，然後選取 [完成]  。

## <a name="add-an-example-utterance"></a>新增範例語句

建立意圖之後，您可以新增範例語句。 範例語句是使用者在聊天機器人或其他用戶端應用程式中輸入的文字。 這些會將使用者文字的意圖對應到 LUIS 意圖。

至於此範例應用程式的 `FindForm` 意圖，範例語句會納入表單號碼。 用戶端應用程式需要表單號碼才能滿足使用者的要求，因此必須將表單號碼包含在表達中。

> [!div class="mx-imgBorder"]
> [![輸入 FindForm 意圖的範例語句](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

請將下列 15 個範例語句新增至 `FindForm` 意圖。

|#|範例語句|
|--|--|
|1|尋找 hrf-123456|
|2|人力資源表單 hrf-234591 在哪裡？|
|3|hrf-345623 在哪裡|
|4|可以將 hrf-345794 傳送給我嗎|
|5|應徵內部工作需要 hrf-234695 嗎？|
|6|我的經理需要知道我要應徵 hrf-234091 的工作嗎|
|7|hrf-234918 應傳送至何處？ 送到時我會收到電子郵件回應嗎？|
|8|hrf-234555|
|9|何時更新了 hrf-234987？|
|10|應徵工程師職位應使用 hrf-876345 表單嗎|
|11|我的公開徵才提交的是新版的 hrf-765234 嗎？|
|12|應徵國際職位應使用 hrf-234234 嗎？|
|13|hrf-234598 有拼字錯誤|
|14|有新的需求應編輯 hrf-234567 嗎|
|15|hrf-123456、hrf-123123、hrf-234567|

依據設計，這些範例語句有下列方面的不同：

* 語句長度
* 標點符號
* 用字
* 動詞時態
* 文字順序



## <a name="create-a-regular-expression-entity"></a>建立規則運算式實體

若要在執行階段預測回應中傳回表單號碼，表單必須標示為實體。 由於表單號碼文字高度結構化，因此可以使用規則運算式實體來標示。 請依照下列步驟建立此實體：

1. 從左側的功能表中選取 [實體]  。

1. 選取 [實體]  頁面上的 [建立]  。

1. 輸入名稱 `Human Resources Form Number`，選取 [Regex]  實體類型，然後選取 [下一步]  。

   ![建立規則運算式實體](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. 輸入規則運算式 (**RegEx**) 運算式 `hrf-[0-9]{6}`。 這個實體會比對出常值字元 `hrf-`，且僅允許 6 個數字，然後選取 [建立]  。

   ![輸入實體的規則運算式](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>將範例語句新增至 None 意圖

**None** 意圖是後援意圖，不應保留為空白。 您每為應用程式的其他意圖新增 10 個範例語句，此意圖就應含有一個語句。

**None** 意圖的範例語句應在您的用戶端應用程式定義域以外。

1. 從左功能表中選取 [意圖]  ，然後從 [意圖] 清單中選取 [None]  。

1. 請將下列範例語句新增至意圖：

   |None 意圖範例語句|
   |--|
   |狗一直叫很煩人|
   |幫我訂披薩|
   |海裡面的企鵝|

   在此應用程式中，這些範例語句不在定義域內。 如果您的定義域包含動物、食物或海洋，請對 [None]  意圖使用不同的範例語句。

## <a name="train-the-app"></a>進行應用程式定型

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>查看範例語句中的規則運算式實體

1. 從左側功能表中選取 [意圖]  ，以確認實體位於 **FindForm** 意圖中。 然後選取 [FindForm]  意圖。

   實體會標示它在範例語句中的顯示之處。 如果您想要查看原始文字，請從工具列切換 [實體檢視]  ，而不是使用實體名稱。

   > [!div class="mx-imgBorder"]
   > [![標示了實體的所有範例語句](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>使用互動式測試窗格測試新的應用程式

使用 LUIS 入口網站中的互動式 [測試]  窗格，驗證實體擷取自應用程式尚未看過新表達。

1. 從右上方的功能表中選取 [測試]  。

1. 新增表達，然後按 Enter 鍵：

   ```Is there a form named hrf-234098```

    選取 [檢查]  以查看實體預測。

   > [!div class="mx-imgBorder"]
   > ![對測試窗格中的新語句進行測試](./media/get-started-portal-build-app/test-new-utterance.png)

   最高預測意圖是信賴度超過 90% (0.977) 的 **FindForm**。 對於**人力資源表單號碼**實體擷取到 hrf-234098 的值。

## <a name="clean-up-resources"></a>清除資源

在完成本快速入門後，若您不繼續進行下一個快速入門，請從頂端導覽功能表中選取 [我的應用程式]  。 然後，從清單中選取應用程式左側的核取方塊，再從清單上方的內容工具列中選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [2.部署應用程式](get-started-portal-deploy-app.md)
