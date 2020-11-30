---
title: 使用審核工具的影片仲裁-內容仲裁
titleSuffix: Azure Cognitive Services
description: 使用機器輔助的影片仲裁和審核工具來仲裁不適當的內容
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 079d5ea0259a436100b81489043b71a81ec1a330
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327214"
---
# <a name="video-moderation-with-the-review-tool"></a>使用審核工具的影片仲裁

使用內容仲裁的電腦輔助 [影片審核](video-moderation-api.md) 和 [審核工具](Review-Tool-User-Guide/human-in-the-loop.md) ，以審核成人的影片和文字記錄 (明確的) 並猥褻 (暗示) 內容，以取得最適合您業務的結果。

## <a name="view-videos-under-review"></a>查看正在審核的影片

在儀表板上，選取影片內容類型內的任何審核佇列。 這將會開始審核，並開啟 [影片內容審核] 頁面。

> [!div class="mx-imgBorder"]
> ![在內容仲裁 (預覽) 上，滑杆會反白顯示並設定為4個評論。 [全部模糊] 和 [黑色] 和 [白色] 參數都已反白顯示，而且兩者都已設定。](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>評論計數

使用右上角的滑杆，設定您想要在頁面上顯示的評論數目。

### <a name="view-type"></a>檢視類型

您可以將不同的內容專案視為磚或詳細的觀點來查看。 **詳細資料** 視圖可讓您查看主要畫面格，以及所選影片的其他相關資訊。 

> [!NOTE]
> 影片審查服務只會識別和輸出可能完整 (良好) 的畫面，而不會定期輸出畫面。 這項功能可讓您有效率地產生框架層級的成人和猥褻分析。

**磚** 視圖會將每個影片顯示為單一磚。 選取影片畫面上方的展開按鈕，以放大影片並隱藏其他影片。

### <a name="content-obscuring-effects"></a>內容隱藏效果

使用 [ **全部模糊** ] 和 [ **黑色] 和 [白色** ] 開關來設定這些內容隱藏效果。 預設會開啟。 **在 [並排** 顯示] 中，您可以針對每一部影片個別切換效果。

## <a name="check-video-details"></a>檢查影片詳細資料

在 [ **詳細資料** ] 窗格中，右窗格會顯示數個索引標籤，為您提供影片的詳細資料。

* 選取 [ **附注** ] 索引標籤，將自訂附注新增至影片。
* 選取 [文字 **記錄** ] 索引標籤，以查看影片文字記錄 &mdash; ，此服務會自動解壓縮影片中任何語音的文字記錄。 當您選取某個文字區段時，影片播放程式會跳到影片的該部分。
* 選取 [ **中繼資料** ] 索引標籤以查看影片檔案中繼資料。
* 選取 [歷程 **記錄** ] 索引標籤，以查看評論的歷程記錄，例如建立和修改的時間。

> [!div class="mx-imgBorder"]
> ![右側窗格會反白顯示，並選取 [附注] 索引標籤。 有一個測試區域加上附注。](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>套用仲裁標記

影片評論的主要工作是在影片或影片的部分上套用或移除審核標記。

### <a name="bulk-tagging"></a>大量標記

**大量** 標籤工具列可讓您一次將標記新增至多個選取的影片。 選取一或多個影片，然後選取您想要套用的標籤，然後按一下 [ **提交**]。 

> [!div class="mx-imgBorder"]
> ![[大量標記] 窗格中的 [+] 按鈕會反白顯示。](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>主要畫面格標記

您也可以將仲裁標記新增至特定的主要畫面格。 從主要畫面格磚窗格中選取畫面格，然後選取 [主要畫面格 **標記 +** ] 以套用所需的標記。

> [!NOTE]
> 如果服務無法將主要畫面格解壓縮，主要畫面格磚窗格將 **不會顯示任何可用的畫面** ，而選取主要畫面格的選項將會呈現灰色。在此情況下，您只能使用 [ **影片標記 +** ] 按鈕) ，將標籤套用至影片作為整體 (。

> [!div class="mx-imgBorder"]
> ![[磚] 窗格、[影片播放]、[主要畫面格標籤] 窗格和 [影片標籤] 窗格都會顯示。 主要畫面格標記 + 和影片標記 + 按鈕會反白顯示。](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>召開審核

[影片] 窗格底部的 [ **按住** ] 按鈕可讓您進行保留審核，以便您可以在稍後取出並完成。 您可以針對需要查閱另一個目前無法使用之小組成員或管理員的評論，來進行這項檢查。 

您可以按一下畫面頂端的 [ **按住** ] 按鈕來觀看影片。 [保留] 窗格會出現在右側。 從這裡，您可以選取多個保留的評論，並將它們釋放回佇列，或設定其到期時間。 經過預先設定的時間之後，就會將保留的評論釋回佇列中。 選取 [ **儲存** ] 以從目前選取的到期時間開始計數。

> [!div class="mx-imgBorder"]
> ![在 [影片] 窗格中，會反白顯示 [保存] 按鈕。 在窗格底部，會反白顯示 [按住 Time] 下拉式方塊，以及 [發行] 和 [儲存] 按鈕。](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>提交評論

套用標記之後，請選取影片窗格底部的 [ **提交** ] 按鈕。 如果您已標記多部影片，您可以在單一評論或個別評論下提交。

## <a name="limbo-state"></a>Limbo 狀態

提交評論之後，影片會移至 **Limbo** 狀態，您可以選取畫面頂端的 [ **Limbo** ] 按鈕來加以查看。 影片會保持 Limbo 狀態一段預先設定的時間， (您可以在底部) 的功能表中變更，或直到再次進行審核或手動提交為止。

影片從 limbo 到期後，他們的評論會標示為完成。

## <a name="next-steps"></a>後續步驟

- 開始使用[影片審查快速入門](video-moderation-api.md)。
- 了解如何從已審核的輸出為您的人力審查者產生[影片審查](video-reviews-quickstart-dotnet.md)。
- 將[影片文字記錄審核](video-transcript-reviews-quickstart-dotnet.md)新增至您的影片審核。
- 查看有關如何開發[完整影片審查解決方案](video-transcript-moderation-review-tutorial-dotnet.md)的詳細教學課程。