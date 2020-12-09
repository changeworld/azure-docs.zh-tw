---
title: 透過審核工具（內容仲裁）使用內容審核
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
ms.openlocfilehash: 77d7b8a0bf4d7fe9a94a61ea3f2f4279246ffb69
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903882"
---
# <a name="create-human-reviews"></a>建立人工審核

在本指南中，您將瞭解如何在審核工具網站上設定 [評論](../review-api.md#reviews) 。 審核儲存和顯示內容，讓人力仲裁者進行評估。 仲裁者可以改變套用的標籤，並視需要套用自己的自訂標記。 當使用者完成審核時，會將結果傳送至指定的回呼端點，並從網站移除內容。

## <a name="prerequisites"></a>必要條件

- 在內容仲裁者 [審核工具](https://contentmoderator.cognitive.microsoft.com/) 網站上登入或建立帳戶。

## <a name="image-reviews"></a>影像檢閱

1. 移至 [ [審核] 工具](https://contentmoderator.cognitive.microsoft.com/)，選取 [ **試用** ] 索引標籤，並上傳一些要審核的影像。
1. 上傳的映射完成處理之後，請移至 [ **審核** ] 索引標籤並選取 [ **影像**]。

    ![顯示審核工具的 Chrome 瀏覽器，其中 [審核影像] 選項已反白顯示](images/review-images-1.png)

    影像會顯示自動審核程式所指派的任何標籤。 其他審核者看不到您透過審核工具提交的影像。

1. 您也可以選擇移動 **評論來顯示** 滑杆 (1) 來調整螢幕上顯示的影像數目。 按一下已標記或未 **標記****的按鈕** (2) ，據以排序影像。 按一下標記面板 (3) 來開啟或關閉。

    ![顯示含已標記影像供審核之審核工具的 Chrome 瀏覽器](images/review-images-2.png)

1. 若要查看影像的詳細資訊，請按一下縮圖中的省略號，然後選取 [ **視圖詳細資料**]。 您可以使用 [ **移至** ] 選項將影像指派給子小組 (請參閱 [小組] 區段，以深入瞭解子 [小組](./configure.md#manage-team-and-subteams)) 。

    ![[檢視詳細資料] 選項已反白顯示的影像](images/review-images-3.png)

1. 在詳細資料頁面上瀏覽影像審核資訊。

    ![仲裁詳細資料列在不同窗格的影像](images/review-images-4.png)

1. 在您視需要審查並更新標記指派後，請按 [下一步] 以提交您的審查。 在您提交之後，您大約有五秒的時間可按 [上一步] 按鈕，以返回上一個畫面並再次審查影像。 在這之後，影像不再存在於 [提交] 佇列中，而且無法再使用 [上一步] 按鈕。

## <a name="text-reviews"></a>文字檢閱

文字評論的功能與影像評論類似。 除了上傳內容，您只需要在文字中寫入或貼上文字 (最多1024個字元) 。 然後，內容仲裁會分析文字，並將標記 (套用至其他審核資訊，例如不雅內容和個人資料) 。 在 [文字審核] 中，您可以在提交評論之前切換套用的標記及/或套用自訂標記。

![審查工具的螢幕擷取畫面，其中顯示在 Chrome 瀏覽器視窗中加上旗標的文字](../images/reviewresults_text.png)

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何設定和使用內容仲裁 [審核工具](https://contentmoderator.cognitive.microsoft.com)的評論。 接下來，請參閱 [API 主控台指南](../try-review-api-review.md) 或 [.net SDK 快速入門](../client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) ，以瞭解如何以程式設計方式建立評論。