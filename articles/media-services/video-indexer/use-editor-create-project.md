---
title: 使用影片索引子編輯器來建立專案
titleSuffix: Azure Media Services
description: 本主題將示範如何使用影片索引子編輯器來建立專案。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 28186a7dcced47a42b3249f1f74b13e969b41978
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041360"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>使用影片索引子編輯器來建立專案

影片索引子網站可讓您使用影片的深入見解：尋找正確的媒體內容、找出您感興趣的元件，以及使用結果來建立全新的專案。 一旦建立之後，就可以從影片索引子轉譯和下載專案，並將其用於您自己的編輯應用程式或下游工作流程。

在某些情況下，您可能會發現這項功能很有用： 

* 正在建立尾端的電影反白顯示。
* 使用新聞轉換中的舊影片剪輯。
* 正在建立較短的社交媒體內容。

本文說明如何從頭開始建立專案，以及如何從您帳戶中的影片建立專案。

## <a name="create-new-project-and-manage-videos"></a>建立新的專案並管理影片

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
1. 選取 [ **專案** ] 索引標籤。如果您先前已建立專案，您會在這裡看到所有其他專案。
1. 按一下 [ **建立新專案** ]。  

    ![顯示 [影片索引子] 頁面的螢幕擷取畫面，其中已選取 [專案] 索引標籤。](./media/video-indexer-view-edit/new-project.png)
1. 按一下鉛筆圖示，為您的專案命名。 將顯示為「未命名專案」的文字取代為您的專案名稱，然後按一下核取記號。

    ![新增專案](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>將影片新增至專案

> [!NOTE]
> 目前，專案可能只包含以相同語言編制索引的影片。 當您選取一種語言的影片之後，就無法在您的帳戶中新增不同語言的影片。

1. 藉由選取 [ **新增** 影片]，在此專案中新增您想要使用的影片。

    您將會在帳戶中看到所有影片，並在搜尋方塊中顯示「搜尋文字、關鍵字或視覺內容」。 搜尋文字記錄和 OCR 中有指定人員、標籤、品牌、關鍵字或出現次數的影片。
    
    例如，在下圖中，我們要尋找提及「GitHub」的影片。
    
    ![螢幕擷取畫面顯示使用兩個結果來搜尋提及 GitHub 的影片。](./media/video-indexer-view-edit/github.png)

    您可以藉由選取 [ **篩選結果** ] 來進一步篩選結果。 您可以篩選以顯示有特定人員的影片，或指定您只想要查看屬於特定語言或具有特定擁有者的影片結果。 <br/> 您也可以指定查詢的範圍。 例如，如果您想要在 OCR 中搜尋 "GitHub"，請選取 [ **視覺效果文字** ]。

    ![篩選](./media/video-indexer-view-edit/visual-text.png)

    您可以將多個篩選準則分層到您的查詢。 使用 **+** / **-** 按鈕來新增/移除篩選。 使用 **清除篩選** 來移除所有篩選。
1. 若要新增影片，請選取影片，然後選取 [ **新增** ]。
1. 現在，您會看到所有您選擇的影片。 這些是您要從中選取專案剪輯的影片。

    您可以藉由拖放或選取 [清單] 功能表按鈕，然後選取 [ **下移** ] 或 [ **上移** ]，來重新排列影片的順序。 在 [清單] 功能表中，您也可以從這個專案中移除影片。 

    ![螢幕擷取畫面顯示影片索引子，其中包含要移除的影片、清除選取專案或下移的內容功能表。](./media/video-indexer-view-edit/rearrange.png)
    
    您可以 **選擇新增影片** ，隨時將其他影片新增至此專案。 您也可以在專案中加入多個出現的相同影片。 如果您想要從某個影片顯示剪輯，然後從另一個影片中顯示剪輯，然後從第一個影片中顯示另一個剪輯，您可能會想要這樣做。 

### <a name="select-clips-to-use-in-your-project"></a>選取要在專案中使用的剪輯

如果您按一下每個影片右邊的向下箭號，您將會根據影片) 的時間戳記 (剪輯來開啟影片中的見解。 

1. 選取 [ **View Insights** ] 可自訂您想要查看的深入解析，以及您不想要查看的資訊。 

    ![檢視見解](./media/video-indexer-view-edit/insights.png)
1. 若要建立特定剪輯的查詢，請使用搜尋方塊，其會顯示「在文字記錄、視覺文字、人員和標籤中搜尋」。
1. 藉由選取 [ **篩選選項** ]，新增篩選以進一步指定您要尋找的場景詳細資料。

    ![篩選選項](./media/video-indexer-view-edit/filter-options.png)

    例如，您可能想要查看在螢幕上 Donovan 棕色時提及 GitHub 的剪輯。 若要這樣做，您需要新增「包含」篩選準則，該篩選具有「人員」做為深入解析的類型。 然後，您必須在篩選準則的搜尋方塊中輸入「Donovan 棕色」。
    
    ![螢幕擷取畫面會顯示影片索引子，其中包含針對 [包含] 篩選所選取的人員。](./media/video-indexer-view-edit/include.png)
    
    如果您想要在 Donovan 棕色 _不_ 在畫面上時提及 GitHub 的剪輯，您只要使用下拉式清單，將「包含」篩選準則變更為「排除」篩選。 

1. 選取您要新增的區段，以將剪輯加入至您的專案。 您可以按一下區段，取消選取此剪輯。
    
    按一下影片旁的 [清單] 功能表選項，然後選取 [ **選取所有區段** ]，以新增影片的所有區段。 

    ![全部新增](./media/video-indexer-view-edit/add-all.png)

    您可以選取 [清除選取範圍]，清除所有選取專案。

> [!TIP]
> 當您選取和排序剪輯時，可以在頁面右側的播放程式中預覽影片。 

![螢幕擷取畫面會顯示影片索引子，並在視窗右側顯示影片的預覽。](./media/video-indexer-view-edit/preview.png)

當您選取 [ **儲存專案** ] 進行變更時，請記得儲存您的專案。 

### <a name="render-and-download-the-project"></a>轉譯和下載專案

> [!NOTE]
> 針對影片索引子付費帳戶，轉譯您的專案具有編碼成本。 影片索引子試用帳戶僅限5小時的轉譯。

1. 完成之後，請確定您的專案已儲存。 您現在可以轉譯這個專案。 選取 [轉譯 **和下載** ]。 

    ![螢幕擷取畫面顯示影片索引子，並提供可轉譯和下載專案的選項。](./media/video-indexer-view-edit/save.png)

    將會出現一個快顯視窗，告訴您影片索引子會轉譯檔案，然後下載連結會傳送至您的電子郵件。 選取 [繼續]。 
    
    您也會在頁面頂端看到正在轉譯專案的通知。 完成轉譯之後，您會看到新的通知，指出已成功轉譯專案。 按一下通知以下載專案。 它會以的格式下載專案。

    ![轉譯完成](./media/video-indexer-view-edit/rendering-done.png)

1. 您可以從 [ **專案** ] 索引標籤存取儲存的專案。 

    如果您選取此專案，您會看到此專案的所有見解和時程表。 如果您選取 [ **影片編輯器** ]，就可以繼續編輯此專案。 編輯包括新增或移除影片和剪輯，或重新命名專案。

    ![影片編輯器](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>從您的影片建立專案

您可以直接從您帳戶中的影片建立新專案。 

1. 移至影片索引子網站的 [連結 **庫** ] 索引標籤。
1. 開啟您想要用來建立專案的影片。 在 [見解與時間軸] 頁面上，選取 [ **影片編輯器** ] 按鈕。

    這會帶您前往用來建立新專案的相同頁面。 不同于新的專案，您會看到影片的時間戳記深入解析區段，也就是您先前開始編輯的部分。

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)

