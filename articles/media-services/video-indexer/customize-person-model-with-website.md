---
title: 使用視頻索引子網站自訂人員模型
titleSuffix: Azure Media Services
description: 瞭解如何使用視頻索引子網站自訂人員模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499968"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>使用影片索引器網站自訂人員模型

視頻索引子支援名人對視頻內容的識別。 名人辨識功能能根據常被要求的資料來源 (例如 IMDB、維基百科以及 LinkedIn 最有影響力的人)，涵蓋大約 1 百萬個人臉。 有關詳細概述，請參閱[在視頻索引子中自訂人員模型](customize-person-model-overview.md)。

您可以使用影片索引器網站，編輯在視訊中偵測到的人臉，如本主題中所述。 您還可以使用 API，如[使用 API 自訂人員模型](customize-person-model-with-api.md)中所述。

## <a name="central-management-of-person-models-in-your-account"></a>帳戶中人員模型的集中管理

1. 要查看、編輯和刪除帳戶中的"人"模型，請流覽到視頻索引子網站並登錄。

2. 選擇頁面右上角的內容模型自訂按鈕。

    ![內容模型自訂](./media/customize-face-model/content-model-customization.png)

3. 選擇"人員"選項卡。

    您將在帳戶中看到"預設人員"模型。 預設人員模型包含您可能編輯或更改的視頻見解中的任何面孔，而您在索引期間未為其指定自訂"人"模型。

    如果您創建了其他 Person 模型，它們也會在此頁上列出。

    ![內容模型自訂](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>建立新的人員模型

1. 選擇 **"添加模型"** 按鈕。

    ![添加新的人員模型](./media/customize-face-model/add-new-person.png)

2. 輸入模型的名稱，然後選擇名稱旁邊的檢查按鈕。

    ![添加新的人員模型](./media/customize-face-model/add-new-person2.png)

    您已經創建了一個新的人員模型。 您現在可以向新的"人"模型添加面。

3. 挑選清單功能表按鈕，然後選擇 **"添加人員**"。

    ![添加新的人員模型](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>將新人添加到人員模型

> [!NOTE]
> 視頻索引子允許您在人員模型中添加多個同名人員。 但是，建議您為模型中的每個人提供唯一的名稱，以確保可用性和清晰度。

1. 要向"人"模型添加新面，請選擇要向其添加面的"人"模型旁邊的清單功能表按鈕。

1. 從功能表中選擇**和添加人員**。

    ![向人添加新面孔](./media/customize-face-model/add-new-face.png)

    快顯視窗將提示您填寫此人的詳細資訊。 鍵入人員的姓名並選擇檢查按鈕。

    ![向人添加新面孔](./media/customize-face-model/add-new-face2.png)

    然後，您可以從檔資源管理器中進行選擇，也可以拖放人臉圖像。 視頻索引子將採用所有標準圖像檔案類型（例如：JPG、PNG 等）。

    視頻索引子可以使用添加到此新面孔的人員模型，檢測此人在將來視頻中的發生情況，以及您已索引的當前視頻。 識別當前視頻中的人員可能需要一些時間才能生效，因為這是一個批次處理過程。

## <a name="rename-a-person-model"></a>重命名人員模型

您可以重命名帳戶中的任意人員模型，包括"預設人員"模型。 即使您重命名預設的"人"模型，它仍將作為帳戶中的預設人員模型。

1. 選擇要重命名的"人員"模型旁邊的清單功能表按鈕。
2. 從功能表中選取 [重新命名]****。

    ![重命名人員模型](./media/customize-face-model/rename-person.png)

3. 選擇模型的當前名稱，並在新名稱中鍵入。

    ![重命名人員模型](./media/customize-face-model/rename-person2.png)

4. 選擇要重命名模型的檢查按鈕。

## <a name="delete-a-person-model"></a>刪除人員模型

您可以刪除您在帳戶中創建的任意人員模型。 但是，您無法刪除預設人員模型。

1. 從功能表中選取 [刪除]****。

    ![刪除人員模型](./media/customize-face-model/delete-person.png)

    將顯示一個快顯視窗，並通知您此操作將刪除"人"模型及其包含的所有人員檔。 無法撤銷此操作。

    ![刪除人員模型](./media/customize-face-model/delete-person2.png)

1. 如果確定，請選擇"刪除"。

> [!NOTE]
> 使用此（現已刪除）人員模型編制索引的現有視頻不支援您更新視頻中顯示的人臉名稱的能力。 只有在使用其他"人"模型重新索引這些視頻中的人臉名稱後，才能編輯這些視頻中的人臉名稱。 如果不指定"人"模型，則重新編制索引，將使用預設模型。

## <a name="manage-existing-people-in-a-person-model"></a>在人員模型中管理現有人員

要查看任何"人"模型的內容，請選擇"人"模型名稱旁邊的箭頭。 下拉清單顯示該特定人物模型中的所有人員。 如果選擇每個人旁邊的清單功能表按鈕，您將看到管理、重命名和刪除選項。  

![向人添加新面孔](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>重命名人員

1. 要重命名"人"模型中的人員，請挑選清單功能表按鈕，然後從清單功能表中選擇 **"重命名**"。
1. 選擇此人的當前姓名並鍵入新名稱。
1. 選擇檢查按鈕，該人員將被重命名。

### <a name="delete-a-person"></a>刪除某人

1. 要從"人人"模型中刪除人員，請挑選清單功能表按鈕，然後從清單功能表中選擇 **"刪除**"。
1. 快顯視窗將告訴您將刪除此人，並且無法撤銷此操作。
1. 再次選擇 **"刪除**"，這將從"人員"模型中刪除此人。

### <a name="manage-a-person"></a>管理人員

如果選擇 **"管理**"，您將看到正在訓練此人員模型的所有面。 這些人臉來自使用此人員模型的視頻或手動上傳的圖像中該人員的發生率。

您可以通過選擇 **"添加圖像**"向人添加更多面。

您可以使用管理窗格重命名人員並從人員模型中刪除該人員。

## <a name="use-a-person-model-to-index-a-video"></a>使用人員模型對視頻進行索引

您可以在視頻上傳期間分配"人"模型，使用"人"模型為新視頻編制索引。

要在新視頻上使用您的人員模型，請執行以下步驟：

1. 選擇頁面頂部的 **"上傳**"按鈕。

    ![上傳人員模型](./media/customize-face-model/upload.png)

1. 將視頻檔放在圓圈中或流覽檔。
1. 選擇 **"高級"選項**箭頭。

    ![上傳人員模型](./media/customize-face-model/upload2.png)

1. 選擇下拉清單並選擇您創建的"人"模型。

    ![上傳人員模型](./media/customize-face-model/upload3.png)

1. 選擇頁面底部的 **"上傳**"選項，將使用"人"模型對新視頻進行索引。

如果在上傳過程中未指定"人"模型，視頻索引子將使用帳戶中的"預設人員"模型對視頻進行索引。

## <a name="use-a-person-model-to-reindex-a-video"></a>使用人員模型重新索引視頻

要使用 Person 模型重新索引集合中的視頻，請轉到視頻索引子主頁上的帳戶視頻，並懸停在要重新索引的視頻名稱上。

您將看到用於編輯、刪除和重新索引視頻的選項。

1. 選擇重新索引視頻的選項。

    ![使用人員模型重新索引視頻](./media/customize-face-model/reindex.png)

    您現在可以選擇"人員"模型來重新索引視頻。
1. 選擇下拉清單並選擇要使用的人員模型。

    ![使用人員模型重新索引視頻](./media/customize-face-model/reindex2.png)

1. 選擇 **"重新索引**"按鈕，您的視頻將使用"人"模型重新編制索引。

對視頻中檢測到和識別的剛剛重新編制索引的人臉進行的任何新編輯都將保存在用於重新索引視頻的 Person 模型中。

## <a name="managing-people-in-your-videos"></a>管理視頻中的人

您可以通過編輯和刪除人臉來管理檢測到的人臉以及您索引的視頻中識別的人。

刪除面會從視頻的見解中刪除特定面。

編輯人臉會重命名視頻中檢測到並可能識別的人臉。 編輯視頻中的人臉時，該名稱將保存為在上傳和索引期間分配給視頻的人員模型中的人員條目。

如果在上傳過程中未為視頻分配"人員"模型，則您的編輯將保存在帳戶的"預設人員"模型中。

### <a name="edit-a-face"></a>編輯人臉

> [!NOTE]
> 如果 Person 模型具有兩個或多個具有相同名稱的人員，則您將無法在使用該 Person 模型的視頻中標記該名稱。 您只能在視頻索引子中內容模型自訂頁的"人員"選項卡中對共用該名稱的使用者進行更改。 因此，建議您為 Person 模型中的每個人提供唯一名稱。

1. 瀏覽至影片索引子網站並登入。
1. 在您的帳戶中搜尋您要檢視和編輯的視訊。
1. 要編輯視頻中的人臉，請轉到"見解"選項卡，然後選擇視窗右上角的鉛筆圖示。

    ![編輯視頻中的人臉](./media/customize-face-model/edit-face.png)

1. 選擇任何檢測到的面，並從"未知#X"（或以前分配給面的名稱）更改其名稱。
1. 鍵入新名稱後，選擇新名稱旁邊的檢查圖示。 此操作將保存新名稱，並在其他當前視頻和您上傳的未來視頻中識別和命名此人臉的所有事件。 您其他目前視訊中的臉部辨識可能需要一些時間才會生效，因為這是批次處理。

如果為視頻正在使用的"人"模型中現有人員的姓名命名，則從該人員的此視頻中檢測到的人臉圖像將與模型中已有的人臉圖像合併。 如果使用新名稱命名人臉，則在視頻正在使用的"人"模型中創建新的"人"條目。

![編輯視頻中的人臉](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>刪除人臉

要刪除視頻中檢測到的人臉，請轉到"見解"窗格並選擇窗格右上角的鉛筆圖示。 選擇面名下方的 **"刪除**"選項。 此操作從視頻中刪除檢測到的面。 在出現該人臉的其他視頻中，仍會檢測到此人的面部，但您可以在這些視頻編制索引後從這些視頻中刪除該人臉。

此人（如果已命名）還將繼續存在用於索引從中刪除人臉的視頻的 Person 模型中，除非您從"人"模型中專門刪除此人。

![刪除視頻中的面部](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>後續步驟

[使用 API 自訂人員模型](customize-person-model-with-api.md)
