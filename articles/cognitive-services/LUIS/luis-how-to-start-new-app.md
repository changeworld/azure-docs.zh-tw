---
title: 建立新的應用程式-LUIS
titleSuffix: Azure Cognitive Services
description: 在 Language Understanding (LUIS) 網頁上建立及管理應用程式。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2020
ms.openlocfilehash: 2dd06a7b4c8e6296cda747d17fd3d5be5db0af6b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018883"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>在 LUIS 入口網站中建立新的 LUIS 應用程式
有好幾種方法可建立 LUIS 應用程式。 您可以在 LUIS 入口網站中建立 LUIS 應用程式，或透過 LUIS 撰寫 [API](developer-reference-resource.md)。

## <a name="using-the-luis-portal"></a>使用 LUIS 入口網站

您可以透過數種方式在入口網站中建立新的應用程式：

* 從空的應用程式著手，並建立意圖、語句和實體。
* 從空的應用程式著手，並新增[預建網域](./howto-add-prebuilt-models.md)。
* 從 `.lu` `.json` 已經包含意圖、語句和實體的或檔案匯入 LUIS 應用程式。

## <a name="using-the-authoring-apis"></a>使用撰寫 API
您可使用撰寫 API 以數種方式建立新的應用程式：

* [新增應用程式](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) -以空的應用程式開始，並建立意圖、語句和實體。
* [新增預建的應用程式](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) -以預建網域開頭，包括意圖、語句和實體。


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>


[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>在 LUIS 中建立新的應用程式

1. 在 **我的應用程式** ] 頁面上選取您的 **訂** 用帳戶，然後  **撰寫資源** 然後 **+ 建立**。 

> [!div class="mx-imgBorder"]
> ![LUIS 應用程式清單](./media/create-app-in-portal.png)

1. 在對話方塊中，輸入應用程式的名稱，例如 `Pizza Tutorial` 。

    ![建立新的應用程式對話方塊](./media/create-pizza-tutorial-app-in-portal.png)

1. 選擇您的應用程式文化特性，然後選取 [ **完成**]。 描述和預測資源此時是選擇性的。 您可以隨時在入口網站的 [ **管理** ] 區段中設定。

    > [!NOTE]
    > 建立應用程式之後便無法變更文化特性 (Culture)。

    建立應用程式之後，LUIS 入口網站會顯示 **Intents** `None` 已為您建立之意圖的意圖清單。 您現在有空白應用程式。

    > [!div class="mx-imgBorder"]
    > ![未建立意圖的意圖清單（不含範例語句）。](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available-on-my-apps-page"></a>我的應用程式頁面上可用的其他動作

內容工具列會提供其他動作：

* 將應用程式重新命名
* 使用或從檔案匯入 `.lu``.json`
* 將應用程式匯出為適用于 `.lu` LUIS 容器的[LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) 、 `.json` 或 (的 (`.zip`) [LUIS container](luis-container-howto.md)
* 匯入容器端點記錄，以檢查端點語句
* 以離線分析的形式匯出端點記錄檔 `.csv`
* 刪除應用程式

## <a name="next-steps"></a>下一步

如果您的應用程式設計包含意圖偵測，請 [建立新的意圖](luis-how-to-add-intents.md)，並新增範例語句。 如果您的應用程式設計只是資料解壓縮，請將範例語句新增至 [無] 意圖，然後 [建立實體](./luis-how-to-add-entities.md)，並使用這些實體來標示範例語句。