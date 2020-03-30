---
title: 使用視頻索引子網站自訂語言模型
titleSuffix: Azure Media Services
description: 瞭解如何使用視頻索引子網站自訂語言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128088"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>使用影片索引器網站自訂語言模型

影片索引器可讓您上傳適應文字 (也就是從您希望引擎適應其詞彙的網域中取得的文字) 來建立自訂語言模型，以自訂語音辨識。 一旦您將模型定型之後，也會辨識出現在調整文字中的新字。

有關自訂語言模型的詳細概述和最佳實踐，請參閱[使用視頻索引子自訂語言模型](customize-language-model-overview.md)。

您可以使用影片索引器網站，在您的帳戶中建立和編輯自訂語言模型，如本主題中所述。 您也可以使用 API，如[使用 API 自訂語言模型](customize-language-model-with-api.md)中所述。

## <a name="create-a-language-model"></a>建立語言模型

1. 轉到[視頻索引子](https://www.videoindexer.ai/)網站並登錄。
2. 要自訂帳戶中的模型，請選擇頁面右上角**的內容模型自訂**按鈕。

   ![在視頻索引子中自訂內容模型](./media/content-model-customization/content-model-customization.png)

3. 選取 [語言]**** 索引標籤。

    您會看到支援的語言清單。

    ![視頻索引子中的語言模型清單](./media/customize-language-model/customize-language-model.png)

4. 在所需的語言下，選擇 **"添加模型**"。
5. 輸入語言模型的名稱，然後按 Enter 鍵。

    此步驟創建模型，並給出將文字檔上載到模型的選項。

6. 要添加文字檔，請選擇"**添加檔**"。 您的檔資源管理器將打開。

7. 瀏覽至文字檔案並加以選取。 您可以將多個文字檔案新增至語言模型。

    您還可以通過選擇語言模型右側的 **...** 按鈕並選擇 **"添加檔**"來添加文字檔。

8. 上傳完文字檔後，選擇綠色 **"訓練"** 選項。

    ![視頻索引子中的訓練語言模型](./media/customize-language-model/train-model.png)

定型程序需要數分鐘的時間。 定型完成後，您會在模型旁邊看到 [已定型]****。 您可以從模型預覽、下載和刪除檔案。

![視頻索引子中訓練的語言模型](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>在新的影片上使用語言模型

要在新視頻上使用語言模型，請執行以下操作之一：

* 選擇頁面頂部的 **"上傳**"按鈕。

    ![上傳按鈕 視頻索引子](./media/customize-language-model/upload.png)

* 將音訊或視頻檔放入圓圈或流覽檔。

    ![上傳媒體檔案 視頻索引子](./media/customize-language-model/upload2.png)

您可以選擇**視頻來源語言**。 選擇下拉清單並選擇從清單中創建的語言模型。 它應該是語言模型的語言，並在括號中提供您賦予的名稱。

選擇頁面底部的 **"上傳**"選項，將使用語言模型對新視頻進行索引。

### <a name="using-a-language-model-to-reindex"></a>使用語言模型重新編製索引

若要使用您的語言模型為您收藏的視訊重新編製索引，請移至 [[影片索引器](https://www.videoindexer.ai/)] 首頁上的 [帳戶視訊]****，然後將滑鼠停駐在您想要重新編製索引之視訊的名稱上。

您將看到編輯視頻、刪除視頻以及重新索引視頻的選項。 選擇重新索引視頻的選項。

![使用視頻索引子重新索引](./media/customize-language-model/reindex1.png)

您可以選擇**視頻來源語言**以重新索引視頻。 選擇下拉清單並選擇從清單中創建的語言模型。 它應該是語言模型的語言，並在括號中提供您賦予的名稱。

![選擇視頻來源語言 — 使用視頻索引子重新索引視頻](./media/customize-language-model/reindex.png)

選擇 **"重新索引**"按鈕，您的視頻將使用語言模型重新編制索引。

## <a name="edit-a-language-model"></a>編輯語言模型

若要編輯語言模型，您可以變更其名稱、在其中新增檔案，以及從中刪除檔案。

如果從"語言"模型添加或刪除檔，則必須通過選擇綠色 **"訓練"** 選項再次訓練模型。

### <a name="rename-the-language-model"></a>重新命名語言模型

您可以通過選擇語言模型右側的省略號 **（...）** 按鈕並選擇重命名來更改語言模型**的名稱**。

輸入新的名稱，然後按 Enter 鍵。

### <a name="add-files"></a>新增檔案

要添加文字檔，請選擇"**添加檔**"。 您的檔資源管理器將打開。

瀏覽至文字檔案並加以選取。 您可以將多個文字檔案新增至語言模型。

您還可以通過選擇語言模型右側的省略號 **（...）** 按鈕並選擇 **"添加檔**"來添加文字檔。

### <a name="delete-files"></a>刪除檔案

要從語言模型中刪除檔，請選擇文字檔右側的省略號 **（...）** 按鈕，然後選擇 **"刪除**"。 彈出一個新視窗，告訴您無法撤銷刪除。 在新視窗中選擇 **"刪除**"選項。

此動作會從語言模型中完全移除檔案。

## <a name="delete-a-language-model"></a>刪除語言模型

要從帳戶中刪除語言模型，請選擇"語言模型"右側的省略號 **（...）** 按鈕，然後選擇 **"刪除**"。

彈出一個新視窗，告訴您無法撤銷刪除。 在新視窗中選擇 **"刪除**"選項。

此動作會從帳戶中完全移除語言模型。 使用已刪除的語言模型的任何視頻都將保留相同的索引，直到重新索引視頻。 如果重新對視頻進行索引，則可以為視頻分配新的語言模型。 否則，視頻索引子將使用其預設模型重新索引視頻。

## <a name="customize-language-models-by-correcting-transcripts"></a>通過更正成績單自訂語言模型

視頻索引子支援根據使用者對其視頻轉錄的實際更正自動自訂語言模型。

1. 要更正腳本，請打開要從帳戶視頻編輯的視頻。 選擇 **"時間軸"** 選項卡。

    ![自訂語言模型時間表選項卡_視頻索引子](./media/customize-language-model/timeline.png)

1. 選擇鉛筆圖示以編輯轉錄的抄本。

    ![自訂語言模型編輯轉錄器_視頻索引子](./media/customize-language-model/edits.png)

    視頻索引子捕獲您在視頻轉錄中更正的所有行，並自動將它們添加到名為"從腳本編輯"的文字檔中。 這些編輯用於重新訓練用於索引此視頻的特定語言模型。
    
    如果在索引此視頻時未指定語言模型，則此視頻的所有編輯都將存儲在視頻檢測到的語言中稱為"帳戶適應"的預設語言模型中。
    
    如果對同一行進行了多次編輯，則僅使用已更正行的最後一個版本來更新語言模型。  
    
    > [!NOTE]
    > 僅用於自訂的文本更正。 不包括不涉及實際單詞（例如，標點符號或空格）的更正。
    
1. 在"內容模型自訂"頁的"語言"選項卡中將顯示腳本更正。

    ![自訂語言模型—視頻索引子](./media/customize-language-model/customize.png)

   要查看每個語言模型的"從腳本編輯"檔，請選擇該檔以將其打開。

    ![從腳本編輯 — 視頻索引子](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>後續步驟

[使用 API 自訂語言模型](customize-language-model-with-api.md)
