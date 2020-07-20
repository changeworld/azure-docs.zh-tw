---
title: 透過審核工具使用內容審查-內容仲裁
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73044119"
---
# <a name="create-human-reviews"></a>建立人力審查

在本指南中，您將瞭解如何在審核工具網站上設定[評論](../review-api.md#reviews)。 審查儲存和顯示內容，供人力仲裁者評估。 仲裁者可以改變套用的標記，並視需要套用自己的自訂標記。 當使用者完成審查時，會將結果傳送至指定的回呼端點，並將內容從網站中移除。

## <a name="prerequisites"></a>先決條件

- 在內容仲裁[審查工具](https://contentmoderator.cognitive.microsoft.com/)網站上登入或建立帳戶。

## <a name="image-reviews"></a>影像檢閱

1. 移至[審核工具](https://contentmoderator.cognitive.microsoft.com/)，選取 [**嘗試**] 索引標籤，並上傳一些影像進行審核。
1. 一旦上傳的影像完成處理，請移至 [**審核**] 索引標籤，然後選取 [**映射**]。

    ![顯示審核工具的 Chrome 瀏覽器，其中 [審核影像] 選項已反白顯示](images/review-images-1.png)

    影像會與自動審核程式所指派的任何標籤一起顯示。 其他審核者看不到您透過審核工具提交的影像。

1. 或者，移動**評論以顯示**滑杆（1）來調整螢幕上顯示的影像數目。 按一下已加上標籤**或未****標記**的按鈕（2），以據以排序影像。 按一下標記面板（3）以開啟或關閉它。

    ![顯示含已標記影像供審核之審核工具的 Chrome 瀏覽器](images/review-images-2.png)

1. 若要查看影像的詳細資訊，請按一下縮圖中的省略號，然後選取 [**查看詳細資料**]。 您可以使用 [**移至**] 選項將影像指派給子小組（如需深入瞭解子小組，請參閱[團隊](./configure.md#manage-team-and-subteams)一節）。

    ![[檢視詳細資料] 選項已反白顯示的影像](images/review-images-3.png)

1. 在詳細資料頁面上瀏覽影像審核資訊。

    ![仲裁詳細資料列在不同窗格的影像](images/review-images-4.png)

1. 在您視需要審查並更新標記指派後，請按 [下一步]**** 以提交您的審查。 在您提交之後，您大約有五秒的時間可按 [上一步]**** 按鈕，以返回上一個畫面並再次審查影像。 在這之後，影像不再存在於 [提交] 佇列中，而且無法再使用 [上一步]**** 按鈕。

## <a name="text-reviews"></a>文字檢閱

文字審核功能類似于影像評論。 您不需要上傳內容，只要以文字撰寫或貼上（最多1024個字元）。 然後，內容仲裁會分析文字並套用標記（除了其他審核資訊，例如不雅內容和個人資料）。 在文字審查中，您可以在提交審查之前切換套用的標記和/或套用自訂標記。

![審查工具的螢幕擷取畫面，其中顯示在 Chrome 瀏覽器視窗中加上旗標的文字](../images/reviewresults_text.png)

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何從內容仲裁[審查工具](https://contentmoderator.cognitive.microsoft.com)設定及使用審核。 接下來，請參閱[REST API 指南](../try-review-api-review.md)或[.net SDK 快速入門](../dotnet-sdk-quickstart.md)，以瞭解如何以程式設計方式建立評論。