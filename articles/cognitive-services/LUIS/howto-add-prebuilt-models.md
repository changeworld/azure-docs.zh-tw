---
title: Language Understanding 的預建模型
titleSuffix: Azure Cognitive Services
description: LUIS 包含一組預先建立的模型，可快速新增常見的交談式使用者案例。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 653882db4b62d7731123faf7b177da44dbd74e3f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584989"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>為一般使用案例新增預先建立的模型

LUIS 包含一組預先建立的模型，可快速新增常見的交談式使用者案例。 這是快速又簡單的方法，可將功能加入對話式用戶端應用程式，而不需要設計這些功能的模型。

## <a name="add-a-prebuilt-domain"></a>新增預建網域

1. 登入[LUIS 入口網站](https://www.luis.ai)，並選取您的**訂**用帳戶和**撰寫資源**，以查看指派給該撰寫資源的應用程式。
1. 在**我的應用程式**] 頁面上選取您的應用程式名稱，以開啟它。

1. 從左側工具列中選取 [**預建網域**]。

1. 尋找您要新增至應用程式的網域，然後選取 [**新增網域**] 按鈕。

    > [!div class="mx-imgBorder"]
    > ![新增 [Calendar] \(行事曆\) 預先建置定義域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>新增預先建置的意圖

1. 登入[LUIS 入口網站](https://www.luis.ai)，並選取您的**訂**用帳戶和**撰寫資源**，以查看指派給該撰寫資源的應用程式。
1. 在**我的應用程式**] 頁面上選取您的應用程式名稱，以開啟它。

1. 在 [**意圖**] 頁面上，從 [意圖] 清單上方的工具列中選取 [**新增預先建立的定義域意圖**]。

1. 從快顯對話方塊中選取 [Utilities.Cancel]**** 意圖。

    > [!div class="mx-imgBorder"]
    > ![新增預先建置的意圖](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. 選取 [Done] \(完成\)**** 按鈕。

## <a name="add-a-prebuilt-entity"></a>新增預先建置的實體
1. 登入[LUIS 入口網站](https://www.luis.ai)，並選取您的**訂**用帳戶和**撰寫資源**，以查看指派給該撰寫資源的應用程式。
1. 在**我的應用程式**] 頁面上選取您的應用程式名稱，以開啟它。
1. 選取左側的 [**實體**]。

1. 在 [**實體**] 頁面上，選取 [**新增預建實體**]。

1. 在 [**新增預先**建立的實體] 對話方塊中，選取預先建立的實體。

    > [!div class="mx-imgBorder"]
    > ![新增預先建置的實體對話方塊](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. 選取 [完成]  。 新增實體之後，您不需要進行應用程式定型。

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>發行以從預測端點中查看預先建立的模型

若要查看預建模型的值，最簡單的方式是從已發行的端點進行查詢。

## <a name="entities-containing-a-prebuilt-entity-token"></a>包含預建實體 token 的實體

如果您的機器學習實體需要預建實體的必要功能，請將子元件新增至機器學習的實體，然後新增預建實體的_必要_功能。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用 REST Api 從 .csv 建立模型](./luis-tutorial-node-import-utterances-csv.md)
