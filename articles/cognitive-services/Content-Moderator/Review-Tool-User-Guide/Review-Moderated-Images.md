---
title: 通過審核工具使用內容審核 - 內容檢閱者
titleSuffix: Azure Cognitive Services
description: 了解審查工具如何讓人力審核者在入口網站審查影像。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73044119"
---
# <a name="create-human-reviews"></a>創建人工評論

在本指南中，您將瞭解如何在"審閱"工具網站上設置[評論](../review-api.md#reviews)。 評論存儲和顯示內容，供人工版主評估。 檢閱者可以更改應用的標記，並根據需要應用自己的自訂標記。 當使用者完成審核時，結果將發送到指定的回檔終結點，並且內容將從網站中刪除。

## <a name="prerequisites"></a>Prerequisites

- 在內容審閱人[審核工具](https://contentmoderator.cognitive.microsoft.com/)網站上登錄或創建帳戶。

## <a name="image-reviews"></a>影像檢閱

1. 轉到["審閱"工具](https://contentmoderator.cognitive.microsoft.com/)，選擇 **"試用"** 選項卡，並上傳一些圖片進行審閱。
1. 上傳的圖像完成處理後，轉到 **"審閱**"選項卡並選擇 **"圖像**"。

    ![顯示審核工具的 Chrome 瀏覽器，其中 [審核影像] 選項已反白顯示](images/review-images-1.png)

    圖像顯示與自動審核過程分配的任何標籤。 您通過"審閱"工具提交的圖像對其他檢閱者不可見。

1. 或者，將 **"審核"移動到顯示**滑塊 （1） 以調整螢幕上顯示的圖像數。 按一下**標記**或**未標記**的按鈕 （2） 對圖像進行排序。 按一下標籤面板 （3） 以將其打開或關閉。

    ![顯示含已標記影像供審核之審核工具的 Chrome 瀏覽器](images/review-images-2.png)

1. 要查看圖像的詳細資訊，請按一下縮略圖中的省略號並選擇 **"查看詳細資訊**"。 您可以使用 **"移動到"** 選項將圖像分配給子團隊（請參閱[團隊](./configure.md#manage-team-and-subteams)部分以瞭解有關子團隊詳細資訊）。

    ![[檢視詳細資料] 選項已反白顯示的影像](images/review-images-3.png)

1. 在詳細資料頁面上瀏覽影像審核資訊。

    ![仲裁詳細資料列在不同窗格的影像](images/review-images-4.png)

1. 在您視需要審查並更新標記指派後，請按 [下一步]**** 以提交您的審查。 在您提交之後，您大約有五秒的時間可按 [上一步]**** 按鈕，以返回上一個畫面並再次審查影像。 在這之後，影像不再存在於 [提交] 佇列中，而且無法再使用 [上一步]**** 按鈕。

## <a name="text-reviews"></a>文字檢閱

文本審閱的功能類似于圖像審閱。 您只需寫入或粘貼文本（最多 1，024 個字元），即可不上傳內容。 然後，內容審閱人分析文本並應用標記（除了其他審核資訊，如褻瀆和個人資料）。 在文本審閱中，您可以在提交審核之前切換應用的標記和/或應用自訂標記。

![審查工具的螢幕擷取畫面，其中顯示在 Chrome 瀏覽器視窗中加上旗標的文字](../images/reviewresults_text.png)

## <a name="next-steps"></a>後續步驟

在本指南中，您學習了如何設置和使用內容檢閱者[審閱工具](https://contentmoderator.cognitive.microsoft.com)的評論。 接下來，請參閱[REST API 指南](../try-review-api-review.md)或[.NET SDK 快速入門](../dotnet-sdk-quickstart.md)，瞭解如何以程式設計方式創建評論。