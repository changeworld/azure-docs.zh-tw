---
title: 快速入門：在 LUIS 入口網站中建立新的應用程式
description: 在本快速入門中，您會建立應用程式、意圖和實體的基本部分，以及在 LUIS 入口網站中使用範例語句進行測試。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/30/2020
ms.openlocfilehash: a5443dce9fc8bc028232f66f45b537a46858f35a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436669"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>快速入門：在 LUIS 入口網站中建立新的應用程式

在本快速入門中，您會在 LUIS 入口網站中建立新的應用程式。 受先，建立應用程式、**意圖** 和 **實體** 的基本部分。 然後，藉由在互動式測試面板中提供範例使用者語句進行測試，以取得預測意圖。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>建立應用程式

若要建立應用程式，請選取 [+ 新增應用程式]。 

在顯示的視窗中，輸入下列資訊：

|名稱  |描述  |
|---------|---------|
|名稱     | 應用程式的名稱。 例如 "home automation"。 必要。        |
|文化特性     | 您的應用程式了解及讀出的語言。 必要。   |
|描述 | 應用程式的描述。 選擇性。
|預測資源 | 將接收查詢的預測資源。 選擇性。 |

選取 [完成]  。

>[!NOTE]
>建立應用程式之後便無法變更文化特性 (Culture)。


## <a name="create-intents"></a>建議意圖

建立 LUIS 應用程式之後，需要建立意圖。 意圖是對使用者的文字進行分類的方式之一。 例如，人力資源應用程式可能會有兩個函式。 功能是幫助人達成以下目標：

 1. 尋找和應徵工作
 1. 找到應徵工作的表單

應用程式的兩個不同「目的」均符合下列意圖：

|Intent|使用者的文字範例<br>稱為 _語句_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

若要建立意圖，請完成下列步驟：

1. 應用程式建立後，請確定您位於 [建置] 區段的 [意圖] 頁面上。 選取 [建立]。

   [![選取 [建立] 以建立新意圖的螢幕擷取畫面](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. 輸入意圖名稱 `FindForm`，然後選取 [完成]。

## <a name="add-an-example-utterance"></a>新增範例語句

建立意圖之後，您可以新增範例語句。 範例語句是使用者在聊天機器人或其他用戶端應用程式中輸入的文字。 這些會將使用者文字的意圖對應到 LUIS 意圖。 至於此範例應用程式的 `FindForm` 意圖，範例語句會納入表單號碼。 用戶端應用程式需要表單號碼才能滿足使用者的要求，因此必須將表單號碼包含在表達中。

在 `FindForm` 的 [意圖] 頁面上，在 [範例語句] 底下新增下列範例語句。 

|#|範例語句|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

依據設計，這些範例語句有下列方面的不同：

* 語句長度
* [標點符號](luis-reference-application-settings.md#punctuation-normalization)
* 用字
* 動詞時態
* 文字順序


## <a name="create-a-regular-expression-entity"></a>建立規則運算式實體

若要在執行階段預測回應中傳回表單號碼，必須以實體的形式擷取表單號碼。 由於表單號碼文字高度結構化，因此您可以使用規則運算式實體。 請依照下列步驟建立規則運算式實體：

1. 從左側的功能表中選取 [實體]。

1. 選取 [實體] 頁面上的 [建立]。

1. 輸入名稱 `FormNumber`，選取 [Regex] 實體類型。

1. 在 [RegEx] 欄位中，輸入規則運算式 `hrf-[0-9]{6}`。 這個實體會比對出常值字元 `hrf-` (僅允許六個數字)，然後選取 [建立]。

    > [!div class="mx-imgBorder"]
    > ![建立規則運算式實體的螢幕擷取畫面](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    此實體會擷取與任何意圖中的規則運算式相符的任何文字。

## <a name="add-example-utterances-to-the-none-intent"></a>將範例語句新增至 None 意圖

**None** 意圖是後援意圖，不應保留為空白。 您每為應用程式的其他意圖新增 10 個範例語句，此意圖就應含有一個語句。

**None** 意圖的範例語句應在您的用戶端應用程式定義域以外。

1. 從左功能表中選取 [意圖]，然後從 [意圖] 清單中選取 [None]。

1. 請將下列範例語句新增至意圖：

   |None 意圖範例語句|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   在此應用程式中，這些範例語句不在定義域內。 如果您的定義域包含動物或海洋，請對 [None] 意圖使用不同的範例語句。

## <a name="train-the-app"></a>進行應用程式定型

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>查看範例語句中的規則運算式實體

1. 從左側功能表中選取 [意圖]，以確認實體位於 **FindForm** 意圖中。 然後選取 [FindForm] 意圖。

   實體會標示它在範例語句中的顯示之處。

   > [!div class="mx-imgBorder"]
   > [![標示了實體的所有範例語句的螢幕擷取畫面](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>使用互動式測試窗格測試新的應用程式

使用 LUIS 入口網站中的互動式 [測試] 窗格，驗證實體擷取自應用程式尚未看過新表達。

1. 從右上方的功能表中選取 [測試]。

1. 新增表達，然後按 Enter 鍵：

   ```Is there a form named hrf-234098```

    選取 [檢查] 以查看實體預測。

   > [!div class="mx-imgBorder"]
   > ![對測試窗格中的新語句進行測試的螢幕擷取畫面](./media/get-started-portal-build-app/test-new-utterance.png)

   最高預測意圖是信賴度超過 90% (0.977) 的 **FindForm**。 **FormNumber** 實體的擷取值為 hrf-234098。

## <a name="clean-up-resources"></a>清除資源

在完成本快速入門後，若您不繼續進行下一個快速入門，請從頂端導覽功能表中選取 [我的應用程式]。 然後，從清單中選取應用程式左側的核取方塊，再從清單上方的內容工具列中選取 [刪除]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [2.部署應用程式](get-started-portal-deploy-app.md)
