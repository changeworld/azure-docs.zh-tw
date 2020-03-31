---
title: 使用視頻索引子編輯器創建專案
titleSuffix: Azure Media Services
description: 本主題演示如何使用視頻索引子編輯器創建專案。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839173"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>使用視頻索引子編輯器創建專案

視頻索引子網站，使您能夠使用視頻的深入見解：找到正確的媒體內容，找到您感興趣的部件，並使用結果創建一個全新的專案。 創建後，可以從視頻索引子呈現和下載專案，並在您自己的編輯應用程式或下游工作流中使用。

您可能會發現此功能有用的一些方案包括： 

* 為預告片創建影片亮點。
* 在新聞播報中使用視頻的舊剪輯。
* 為社交媒體創建較短的內容。

本文演示如何從頭開始創建專案，以及如何從帳戶中的視頻創建專案。

## <a name="create-new-project-and-manage-videos"></a>創建新專案並管理視頻

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
1. 選擇"**專案**"選項卡。如果您以前創建過專案，您將在此處看到所有其他專案。
1. 按一下 **"創建新專案**"。  

    ![新增專案](./media/video-indexer-view-edit/new-project.png)
1. 按一下鉛筆圖示，為專案指定名稱。 將"無標題專案"的文本替換為專案名稱，然後按一下檢查。

    ![新增專案](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>向專案添加視頻

> [!NOTE]
> 目前，專案可能僅包含以相同語言索引的視頻。 選擇一種語言的視頻後，您無法在帳戶中添加不同語言的視頻。

1. 通過選擇 **"添加視頻**"，添加您要在此專案中使用的視頻。

    您將看到帳戶中的所有視頻以及一個搜索框，上面寫著"搜索文本、關鍵字或視覺內容"。 搜索在腳本和 OCR 中具有指定人員、標籤、品牌、關鍵字或出現的視頻。
    
    例如，在下圖中，我們正在尋找提及"GitHub"的視頻。
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    您可以通過選擇 **"篩選結果**"來進一步篩選結果。 您可以篩選以顯示包含特定人員的視頻，或指定您只想查看以特定語言或具有特定擁有者的視頻結果。 <br/> 您還可以指定查詢的範圍。 例如，如果要在 OCR 中搜索"GitHub"，請選擇 **"可視文本**"。

    ![Filter](./media/video-indexer-view-edit/visual-text.png)

    您可以將多個篩選器分層到查詢中。 **+** /使用**-** 按鈕添加/刪除篩選器。 使用**清除篩選器**刪除所有篩選器。
1. 要添加視頻，請選擇它們，然後選擇"**添加**"。
1. 現在，您將看到您選擇的所有視頻。 這些是您將從中選擇專案剪輯的視頻。

    您可以通過拖放或挑選清單功能表按鈕並選擇 **"向下移動**"或 **"向上移動**"來重新排列視頻的順序。 從清單功能表中，您還可以從此專案中刪除視頻。 

    ![重新 排列](./media/video-indexer-view-edit/rearrange.png)
    
    您可以選擇"添加視頻"，隨時向此專案**添加更多視頻**。 您還可以向專案添加同一視頻的多個匹配項。 如果要顯示一個視頻的剪輯，然後顯示另一個視頻的剪輯，然後顯示第一個視頻的另一個剪輯，則可能需要執行此操作。 

### <a name="select-clips-to-use-in-your-project"></a>選擇要在專案中使用的剪輯

如果按一下每個視頻右側的向下箭頭，您將根據時間戳記（視訊短片）打開視頻中的見解。 

1. 選擇 **"查看見解"** 可自訂要查看的見解以及不想查看的見解。 

    ![檢視見解](./media/video-indexer-view-edit/insights.png)
1. 要為特定剪輯創建查詢，請使用顯示"以腳本、可視文本、人和標籤搜索"的搜索框。
1. 通過選擇 **"篩選器"選項**，添加篩選器以進一步指定要查找的場景的詳細資訊。

    ![篩選選項](./media/video-indexer-view-edit/filter-options.png)

    例如，您可能希望查看在多諾萬·布朗在螢幕上時提及 GitHub 的剪輯。 為此，您需要添加一個"包含"篩選器，該篩選器以"人員"為見解類型。 然後，您需要在篩選器的搜索框中鍵入"多諾萬布朗"。
    
    ![包含](./media/video-indexer-view-edit/include.png)
    
    如果您希望在多諾萬·布朗_不_在螢幕上時提及 GitHub 的剪輯，只需使用下拉清單將"包含"篩選器更改為"排除"篩選器即可。 

1. 通過選擇要添加的片段向專案添加剪輯。 您可以通過再次按一下分段來取消選擇此剪輯。
    
    通過按一下視頻旁邊的清單功能表選項並**選擇"選擇所有段**"來添加視頻的所有段。 

    ![全部添加](./media/video-indexer-view-edit/add-all.png)

    您可以通過選擇"清除"選擇來清除所有選擇。

> [!TIP]
> 在選擇和訂購剪輯時，您可以在頁面右側的播放機中預覽視頻。 

![預覽](./media/video-indexer-view-edit/preview.png)

請記住，當您通過選擇 **"保存專案"** 進行更改時保存專案。 

### <a name="render-and-download-the-project"></a>渲染和下載專案

> [!NOTE]
> 對於視頻索引子付費帳戶，渲染專案具有編碼成本。 視頻索引子試用帳戶限制為 5 小時的渲染時間。

1. 完成後，請確保已保存專案。 您現在可以呈現此專案。 選擇**渲染和下載**。 

    ![儲存](./media/video-indexer-view-edit/save.png)

    將有一個快顯視窗，告訴您視頻索引子將呈現一個檔，然後下載連結將發送到您的電子郵件。 選取 [繼續]。 
    
    您還將看到一條通知，指出專案正在頁面頂部呈現。 呈現完成後，您將看到一個新的通知，表明專案已成功呈現。 按一下通知以下載專案。 它將以 mp4 格式下載專案。

    ![渲染完成](./media/video-indexer-view-edit/rendering-done.png)

1. 可以從"**專案"** 選項卡訪問保存的專案。 

    如果選擇此專案，您將看到此專案的所有見解和時程表。 如果選擇 **"視頻編輯器**"，則可以繼續編輯此專案。 編輯包括添加或刪除視頻和剪輯或重命名專案。

    ![視頻編輯器](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>從視頻創建專案

您可以直接從帳戶中的視頻創建新專案。 

1. 轉到視頻索引子網站的 **"庫**"選項卡。
1. 打開要用於創建專案的視頻。 在見解和時間表頁上，選擇 **"視頻編輯器"** 按鈕。

    這將帶您到用於創建新專案的同一頁面。 與新專案不同，您會看到以前開始編輯的視頻的時間戳記見解段。

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)

