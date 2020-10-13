---
title: Language Understanding 的預建模型
titleSuffix: Azure Cognitive Services
description: LUIS 包含一組預先建立的模型，可讓您快速新增常見的對話式使用者案例。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2080139732d71aa5df9a9d4ad13fcc4545128dff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541912"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>針對常見的使用案例新增預先建立的模型

LUIS 包含一組預先建立的模型，可讓您快速新增常見的對話式使用者案例。 這是一種快速又簡單的方式，可讓您在對話用戶端應用程式中新增功能，而不需要設計這些功能的模型。

## <a name="add-a-prebuilt-domain"></a>新增預建網域

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。

1. 從左側工具列中選取 [ **預建網域** ]。

1. 尋找您要新增到應用程式的網域，然後選取 [ **新增網域** ] 按鈕。

    > [!div class="mx-imgBorder"]
    > ![新增 [Calendar] \(行事曆\) 預先建置定義域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>新增預先建置的意圖

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。

1. 在 [ **意圖** ] 頁面上，從 [意圖] 清單上方的工具列中選取 [ **新增預建的定義域意圖** ]。

1. 從快顯對話方塊中選取意圖。

    > [!div class="mx-imgBorder"]
    > ![新增預先建置的意圖](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. 選取 [Done] \(完成\)**** 按鈕。

## <a name="add-a-prebuilt-entity"></a>新增預先建置的實體
1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 選取左側的 [ **實體** ]。

1. 在 [ **實體** ] 頁面上，選取 [ **新增預建實體**]。

1. 在 [ **加入預建實體** ] 對話方塊中，選取預建實體。

    > [!div class="mx-imgBorder"]
    > ![新增預先建置的實體對話方塊](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. 選取 [完成]。 新增實體之後，您不需要進行應用程式定型。

## <a name="add-a-prebuilt-domain-entity"></a>新增預建網域實體
1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 選取左側的 [ **實體** ]。

1. 在 [ **實體** ] 頁面上，選取 [ **新增預建網域實體**]。

1. 在 [ **新增預先建立的領域模型** ] 對話方塊中，選取 [預建網域] 實體。

1. 選取 [完成]。 新增實體之後，您不需要進行應用程式定型。

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>發行以從預測端點查看預先建立的模型

若要查看預建模型的值，最簡單的方式就是從已發佈的端點查詢。

## <a name="entities-containing-a-prebuilt-entity-token"></a>包含預建實體權杖的實體

如果您的機器學習實體需要預先建立之實體的必要功能，請將列新增至機器學習實體，然後新增預先建立之實體的 _必要_ 功能。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [從 .csv 使用 REST Api 建立模型](./luis-tutorial-node-import-utterances-csv.md)
