---
title: 語言理解的預構建模型
titleSuffix: Azure Cognitive Services
description: LUIS 包括一組預構建模型，用於快速添加常見會話使用者方案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013539"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>為常見使用方案添加預構建模型 

LUIS 包括一組預構建模型，用於快速添加常見會話使用者方案。 這是一種快速簡便的方法，無需為這些功能設計模型，即可向會話用戶端應用程式添加功能。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>新增預建網域

1. 在 [My Apps] \(我的應用程式\)**** 頁面上，選取您的應用程式。 這會讓您的應用程式開啟至應用程式的 [Build] \(組建\)**** 區段。 

1. 從左側工具列中選擇**預構建域**。 

1. 找到要添加到應用的域，然後選擇 **"添加域**"按鈕。

    > [!div class="mx-imgBorder"]
    > ![新增 [Calendar] \(行事曆\) 預先建置定義域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>新增預先建置的意圖

1. 在 [My Apps] \(我的應用程式\)**** 頁面上，選取您的應用程式。 這會讓您的應用程式開啟至應用程式的 [Build] \(組建\)**** 區段。 

1. 在 **"意向"** 頁上，從意向清單上方的工具列**中選擇"添加預構建的域意圖**"。 

1. 從快顯對話方塊中選取 [Utilities.Cancel]**** 意圖。 

    > [!div class="mx-imgBorder"]
    > ![新增預先建置的意圖](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. 選取 [Done] \(完成\)**** 按鈕。

## <a name="add-a-prebuilt-entity"></a>新增預先建置的實體

1. 在 [我的應用程式]**** 頁面上按一下應用程式名稱來開啟應用程式，然後按一下左側的 [實體]****。 

1. 在 [實體]**** 頁面上，按一下 [先增預先建置的實體]****。

1. 在 **"添加預構建實體"** 對話方塊中，選擇預構建的實體。 

    > [!div class="mx-imgBorder"]
    > ![新增預先建置的實體對話方塊](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. 選擇 **"完成**"。 新增實體之後，您不需要進行應用程式定型。 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>發佈以從預測終結點查看預構建模型

查看預構建模型值的最簡單方法是從已發佈的終結點進行查詢。 

## <a name="entities-containing-a-prebuilt-entity-token"></a>包含預構建實體權杖的實體
 
如果具有受預構建實體約束的機器學習實體，則向機器學習實體添加子元件，然後添加預構建實體的約束。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用 REST API 從 .csv 生成模型](./luis-tutorial-node-import-utterances-csv.md)
