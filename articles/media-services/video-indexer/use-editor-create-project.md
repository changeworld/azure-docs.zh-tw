---
title: 使用影片索引子編輯器來建立專案
titleSuffix: Azure Media Services
description: 本主題示範如何使用影片索引子編輯器來建立專案。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "73839173"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>使用影片索引子編輯器來建立專案

影片索引子網站，可讓您使用影片的深入解析來：尋找正確的媒體內容、找出您感興趣的部分，然後使用結果來建立全新的專案。 建立之後，您可以從影片索引子轉譯和下載專案，並在您自己的編輯應用程式或下游工作流程中使用。

在某些情況下，您可能會發現這項功能很有用： 

* 建立尾端影片重點。
* 在新聞轉換中使用影片的舊剪輯。
* 建立較短的社交媒體內容。

本文說明如何從頭開始建立專案，以及如何從您的帳戶中的影片建立專案。

## <a name="create-new-project-and-manage-videos"></a>建立新專案和管理影片

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
1. 選取 [**專案**] 索引標籤。如果您先前已建立專案，您會在這裡看到所有其他專案。
1. 按一下 [**建立新專案**]。  

    ![新增專案](./media/video-indexer-view-edit/new-project.png)
1. 按一下鉛筆圖示，為您的專案命名。 將顯示為 [未命名專案] 的文字取代為您的專案名稱，然後按一下 [檢查]。

    ![新增專案](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>將影片新增至專案

> [!NOTE]
> 目前，專案可能只包含以相同語言編制索引的影片。 一旦您選取一種語言的影片，就無法在您的帳戶中新增以不同語言顯示的影片。

1. 選取 [**新增**影片]，以在此專案中新增您想要使用的影片。

    您會看到您帳戶中的所有影片，以及顯示「搜尋文字、關鍵字或視覺內容」的搜尋方塊。 搜尋文字記錄和 OCR 中具有指定之人員、標籤、品牌、關鍵字或出現專案的影片。
    
    例如，在下圖中，我們要尋找提及「GitHub」的影片。
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    您可以藉由選取 [**篩選結果**] 來進一步篩選您的結果。 您可以篩選以顯示有特定人員的影片，或指定您只想要查看屬於特定語言或具有特定擁有者的影片結果。 <br/> 您也可以指定查詢的範圍。 例如，如果您想要在 OCR 中搜尋 "GitHub"，請選取 [**視覺文字**]。

    ![Filter](./media/video-indexer-view-edit/visual-text.png)

    您可以將多個篩選器分層至您的查詢。 使用 **+** / **-** 按鈕來新增/移除篩選。 使用 [**清除篩選**] 來移除所有篩選。
1. 若要新增影片，請選取影片，然後選取 [**新增**]。
1. 現在，您會看到所有您選擇的影片。 這些是您要從中選取專案剪輯的影片。

    您可以藉由拖放或選取 [清單] 功能表按鈕，**然後選取 [下移]** 或 [**上移**]，來重新排列影片的順序。 從 [清單] 功能表中，您也可以從這個專案中移除影片。 

    ![重新](./media/video-indexer-view-edit/rearrange.png)
    
    您可以選取 [**新增**影片]，隨時在此專案中新增更多影片。 您也可以在專案中新增多個相同影片的出現次數。 如果您想要在一段影片中顯示剪輯，然後再從第一部影片播放另一個剪輯，則您可能會想要這麼做。 

### <a name="select-clips-to-use-in-your-project"></a>選取要在專案中使用的剪輯

如果您按一下每個影片右邊的向下箭號，就會根據時間戳記（影片的剪輯）來開啟影片中的深入解析。 

1. 選取 [ **View Insights** ] 以自訂您想要查看的深入解析，以及您不想要查看的資訊。 

    ![檢視見解](./media/video-indexer-view-edit/insights.png)
1. 若要建立特定剪輯的查詢，請使用搜尋方塊，此方塊會顯示「在文字記錄、視覺文字、人員和標籤中搜尋」。
1. 藉由選取 [**篩選選項**] 來新增篩選，以進一步指定您所尋找之場景的詳細資料。

    ![篩選選項](./media/video-indexer-view-edit/filter-options.png)

    例如，您可能會想要查看在螢幕上 Donovan 棕色時提及 GitHub 的剪輯。 為此，您需要新增「包含」篩選，其中包含「人員」做為深入解析的類型。 接著，您必須在篩選的搜尋方塊中輸入 "Donovan 棕色"。
    
    ![包含](./media/video-indexer-view-edit/include.png)
    
    如果您想_要在螢幕上 Donovan_棕色時提及 GitHub 的剪輯，您只需要使用下拉式清單將「包含」篩選器變更為「排除」篩選。 

1. 選取您要新增的區段，以將剪輯新增至您的專案。 您可以重新選取此剪輯，方法是再次按一下該區段。
    
    按一下影片旁的 [清單] 功能表選項，然後選取 [**選取所有區段**]，以新增影片的所有區段。 

    ![全部新增](./media/video-indexer-view-edit/add-all.png)

    您可以選取 [清除選取範圍] 來清除所有選取專案。

> [!TIP]
> 當您選取並排序剪輯時，可以在頁面右側的播放程式中預覽影片。 

![預覽](./media/video-indexer-view-edit/preview.png)

當您選取 [**儲存專案**] 進行變更時，請記得儲存您的專案。 

### <a name="render-and-download-the-project"></a>轉譯和下載專案

> [!NOTE]
> 針對影片索引子付費帳戶，轉譯您的專案具有編碼成本。 影片索引子試用帳戶限制為5小時的轉譯。

1. 完成後，請確定您的專案已儲存。 您現在可以呈現這個專案。 選取 [轉譯**和下載**]。 

    ![儲存](./media/video-indexer-view-edit/save.png)

    將會出現一個快顯視窗，告訴您影片索引子會轉譯檔案，然後下載連結就會傳送至您的電子郵件。 選取 [繼續]。 
    
    您也會看到在頁面頂端呈現專案的通知。 轉譯完成後，您會看到新的通知，指出已成功呈現專案。 按一下通知以下載專案。 它會以有設定的格式下載專案。

    ![轉譯完成](./media/video-indexer-view-edit/rendering-done.png)

1. 您可以從 [**專案**] 索引標籤存取已儲存的專案。 

    如果您選取這個專案，您會看到此專案的所有深入解析和時間軸。 如果您選取 [**影片編輯器**]，您可以繼續對此專案進行編輯。 編輯包括新增或移除影片和剪輯，或重新命名專案。

    ![影片編輯器](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>從您的影片建立專案

您可以直接從您帳戶中的影片建立新專案。 

1. 移至影片索引子網站的 [**媒體**櫃] 索引標籤。
1. 開啟您想要用來建立專案的影片。 在 [insights 和時間軸] 頁面上，選取 [**影片編輯器**] 按鈕。

    這會帶您前往用來建立新專案的相同頁面。 不同于新專案，您會看到影片的時間戳記深入解析區段，您先前已開始編輯。

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)

