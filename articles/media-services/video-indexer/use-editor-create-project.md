---
title: 使用影片索引子編輯器來建立專案並新增影片剪輯
titleSuffix: Azure Media Services
description: 本主題將示範如何使用影片索引子編輯器來建立專案和新增影片剪輯。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 3a3ac3f2db4e23f03f83a98bee0aceaddef9f889
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433418"
---
# <a name="add-video-clips-to-your-projects"></a>將影片剪輯新增至專案

影片索引子網站可讓您使用影片的深入見解：尋找正確的媒體內容、找出您感興趣的元件，以及使用結果來建立全新的專案。 

一旦建立之後，就可以從影片索引子轉譯和下載專案，並將其用於您自己的編輯應用程式或下游工作流程。

在某些情況下，您可能會發現這項功能很有用： 

* 正在建立尾端的電影反白顯示。
* 使用新聞轉換中的舊影片剪輯。
* 正在建立較短的社交媒體內容。

本文說明如何建立專案，並將選取的剪輯從影片新增至專案。 

## <a name="create-new-project-and-manage-videos"></a>建立新的專案並管理影片

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
1. 選取 [ **專案** ] 索引標籤。如果您先前已建立專案，您會在這裡看到所有其他專案。
1. 按一下 [ **建立新專案**]。  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="建立新專案":::
1. 按一下鉛筆圖示，為您的專案命名。 將顯示為「未命名專案」的文字取代為您的專案名稱，然後按一下核取記號。

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="新的專案":::
    
### <a name="add-videos-to-the-project"></a>將影片新增至專案

> [!NOTE]
> 目前，專案可能只包含以相同語言編制索引的影片。 </br>一旦您選取一種語言的影片之後，就無法在您的帳戶中新增不同語言的影片，其他語言的影片將呈現灰色/停用狀態。

1. 藉由選取 [ **新增** 影片]，在此專案中新增您想要使用的影片。

    您將會在帳戶中看到所有影片，並在搜尋方塊中顯示「搜尋文字、關鍵字或視覺內容」。 您可以在文字記錄和 OCR 中搜尋具有指定的人員、標籤、品牌、關鍵字或出現次數的影片。
    
    例如，在下圖中，我們尋找的影片只會在文字記錄中提及「自訂視覺」 (如果您要) 篩選搜尋結果，請使用 **篩選** 。
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="螢幕擷取畫面顯示搜尋提及自訂視覺的影片":::
1. 按一下 [ **新增** ]，將影片新增至專案。
1. 現在，您會看到所有您選擇的影片。 這些是您要從中選取專案剪輯的影片。

    您可以藉由拖放或選取 [清單] 功能表按鈕，然後選取 [ **下移** ] 或 [ **上移**]，來重新排列影片的順序。 在 [清單] 功能表中，您也可以從這個專案中移除影片。 
    
    您可以選取 [ **新增** 影片]，隨時將其他影片新增至此專案。 您也可以在專案中加入多個出現的相同影片。 如果您想要從某個影片顯示剪輯，然後從另一個影片中顯示剪輯，然後從第一個影片中顯示另一個剪輯，您可能會想要這樣做。 

### <a name="select-clips-to-use-in-your-project"></a>選取要在專案中使用的剪輯

如果您按一下每個影片右邊的向下箭號，您將會根據影片) 的時間戳記 (剪輯來開啟影片中的見解。 

1. 若要建立特定剪輯的查詢，請使用搜尋方塊，其會顯示「在文字記錄、視覺文字、人員和標籤中搜尋」。
1. 選取 [ **View Insights** ] 可自訂您想要查看的深入解析，以及您不想要查看的資訊。 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="螢幕擷取畫面顯示搜尋試用認知服務的影片":::
1. 藉由選取 [ **篩選選項**]，新增篩選以進一步指定您要尋找的場景詳細資料。

    您可以新增多個篩選準則。 
1. 當您滿意結果之後，請選取您想要新增的區段，以選取要新增至此專案的剪輯。 您可以按一下區段，取消選取此剪輯。
    
    將影片的所有區段新增 (或，然後按一下影片旁的 [清單] 功能表選項，然後選取 [全 **選**]，在搜尋) 之後所傳回的所有區段。 

當您選取和排序剪輯時，可以在頁面右側的播放程式中預覽影片。 

> [!IMPORTANT]
> 請記得在頁面頂端選取 [ **儲存專案** ]，以儲存您的專案。 

### <a name="render-and-download-the-project"></a>轉譯和下載專案

> [!NOTE]
> 針對影片索引子付費帳戶，轉譯您的專案具有編碼成本。 影片索引子試用帳戶僅限5小時的轉譯。

1. 完成之後，請確定您的專案已儲存。 您現在可以轉譯這個專案。 按一下 [轉譯]，快顯視窗隨即 **出現，告訴** 您影片索引子會轉譯檔案，然後將下載連結傳送至您的電子郵件。 選取 [繼續]。 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="螢幕擷取畫面顯示影片索引子，並提供可轉譯和下載專案的選項":::
    
    您也會在頁面頂端看到正在轉譯專案的通知。 完成轉譯之後，您會看到新的通知，指出已成功轉譯專案。 按一下通知以下載專案。 它會以的格式下載專案。
1. 您可以從 [ **專案** ] 索引標籤存取儲存的專案。 

    如果您選取此專案，您會看到此專案的所有見解和時程表。 如果您選取 [ **影片編輯器**]，就可以繼續編輯此專案。 編輯包括新增或移除影片和剪輯，或重新命名專案。
    
## <a name="create-a-project-from-your-video"></a>從您的影片建立專案

您可以直接從您帳戶中的影片建立新專案。 

1. 移至影片索引子網站的 [連結 **庫** ] 索引標籤。
1. 開啟您想要用來建立專案的影片。 在 [見解與時間軸] 頁面上，選取 [ **影片編輯器** ] 按鈕。

    這會帶您前往用來建立新專案的相同頁面。 不同于新的專案，您會看到影片的時間戳記深入解析區段，也就是您先前開始編輯的部分。

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)

