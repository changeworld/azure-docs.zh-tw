---
title: 匯入應用程式步驟
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422736"
---
1. 在[預覽 LUIS 入口網站](https://preview.luis.ai)中，從 [我的應用程式]  頁面選取 [+ 新增對話應用程式]  ，然後選取 [匯入為 JSON]  。 尋找上一個步驟中已儲存的 JSON 檔案。 您不需要變更應用程式的名稱。 選取 [完成] 

1. 從 [管理]  區段的 [版本]  索引標籤上選取 `0.1` 版本，然後選取 [複製]  以複製版本，並為其指定 10 個字元的新 `ml-entity` 名稱，然後選取 [完成]  以結束複製程序。 因為版本名稱會作為 URL 路由的一部分，所以此名稱不能包含任何在 URL 中無效的字元。

    > [!TIP]
    > 在修改應用程式之前，複製到新版本是最佳做法。 完成版本變更後，請將版本匯出為 .json 或 .lu 檔案，然後將該檔案簽入您的原始檔控制系統中。

1. 依序選取 [建置]  和 [意圖]  來查看意圖，也就是 LUIS 應用程式的主要構成要素。

    ![從 [版本] 頁面變更為 [意圖] 頁面。](../media/tutorial-machine-learned-entity/new-version-imported-app.png)