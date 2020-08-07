---
title: 使用影片索引子網站自訂人員模型
titleSuffix: Azure Media Services
description: 瞭解如何使用影片索引子網站自訂人員模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/06/2020
ms.author: kumud
ms.openlocfilehash: 6894eb783b990ebe98f9ebe5eb2fe112a59309a4
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904491"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>使用影片索引器網站自訂人員模型

影片索引子支援影片內容的名人辨識。 名人辨識功能能根據常被要求的資料來源 (例如 IMDB、維基百科以及 LinkedIn 最有影響力的人)，涵蓋大約 1 百萬個人臉。 如需詳細的總覽，請參閱[在影片索引子中自訂人員模型](customize-person-model-overview.md)。

您可以使用影片索引器網站，編輯在視訊中偵測到的人臉，如本主題中所述。 您也可以使用 API，如[使用 Api 自訂人員模型](customize-person-model-with-api.md)中所述。

## <a name="central-management-of-person-models-in-your-account"></a>集中管理您帳戶中的人員模型

1. 若要在您的帳戶中查看、編輯和刪除人員模型，請流覽至影片索引子網站並登入。

2. 選取頁面右上角的 [內容模型自訂] 按鈕。

    ![內容模型自訂](./media/customize-face-model/content-model-customization.png)

3. 選取 [人員] 索引標籤。

    您會在帳戶中看到預設人員模型。 預設的人員模型會保留您在編制索引期間未指定自訂人員模型的影片深入解析中，您可能已編輯或變更的任何臉部。

    如果您已建立其他人員模型，則也會列在此頁面上。

    ![內容模型自訂](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>建立新的人員模型

1. 選取 [ **+ 新增模型**] 按鈕。

    ![加入新的人員模型](./media/customize-face-model/add-new-person.png)

2. 輸入模型的名稱，然後選取名稱旁的核取按鈕。

    ![加入新的人員模型](./media/customize-face-model/add-new-person2.png)

    您已建立新的人員模型。 您現在可以將臉部新增至新的人員模型。

3. 選取 [清單] 功能表按鈕，然後選擇 [ **+ 新增人員**]。

    ![加入新的人員模型](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>將新人員新增至人員模型

> [!NOTE]
> 影片索引子可讓您在人員模型中新增多個名稱相同的人員。 不過，建議您為模型中的每個人員提供唯一的名稱，以提供可用性和清晰度。

1. 若要將新臉部新增至人員模型，請選取您想要新增臉部之人員模型旁的 [清單] 功能表按鈕。

1. 從功能表中選取 [ **+ 新增人員**]。

    ![將新臉部新增至人員](./media/customize-face-model/add-new-face.png)

    快顯視窗會提示您填寫該人員的詳細資料。 輸入人員的名稱，然後選取 [檢查] 按鈕。

    ![將新臉部新增至人員](./media/customize-face-model/add-new-face2.png)

    然後您可以從 [檔案瀏覽器] 中選擇，或拖放臉部影像。 影片索引子會採用所有標準影像檔案類型 (例如： JPG、PNG 和更多) 。

    影片索引子可以使用您在其中新增新臉部的人員模型，在您建立索引的後續影片中偵測到此人員的出現次數，以及您已編制索引的目前影片。 您目前影片中的人員辨識可能需要一些時間才會生效，因為這是批次程式。

## <a name="rename-a-person-model"></a>重新命名人員模型

您可以重新命名帳戶中的任何人員模型，包括預設人員模型。 即使您將預設人員模型重新命名，它仍會作為您帳戶中的預設人員模型。

1. 選取您要重新命名之人員模型旁的 [清單] 功能表按鈕。
2. 從功能表中選取 [重新命名]****。

    ![重新命名人員模型](./media/customize-face-model/rename-person.png)

3. 選取模型的目前名稱，並輸入您的新名稱。

    ![重新命名人員模型](./media/customize-face-model/rename-person2.png)

4. 選取要重新命名之模型的核取按鈕。

## <a name="delete-a-person-model"></a>刪除人員模型

您可以刪除您在帳戶中建立的任何人員模型。 不過，您無法刪除預設人員模型。

1. 從功能表中選取 [刪除]。

    ![刪除人員模型](./media/customize-face-model/delete-person.png)

    隨即會顯示一個快顯視窗，通知您此動作將刪除人員模型及其包含的所有人員和檔案。 此動作無法復原。

    ![刪除人員模型](./media/customize-face-model/delete-person2.png)

1. 如果您確定，請再次選取 [刪除]。

> [!NOTE]
> 使用此 (編制索引的現有影片現在已刪除，) Person 模型不支援您更新影片中所顯示之臉部名稱的功能。 只有在您使用其他人員模型重新編制其索引之後，才能夠編輯這些影片中的臉部名稱。 如果您在未指定人員模型的情況下重新編制索引，則會使用預設模型。

## <a name="manage-existing-people-in-a-person-model"></a>管理人員模型中的現有人員

若要查看任何人員模型的內容，請選取人員模型名稱旁邊的箭號。 下拉式選單會顯示該特定人員模型中的所有人員。 如果您選取每個人員旁的 [清單] 功能表按鈕，您會看到 [管理]、[重新命名] 和 [刪除] 選項。  

![將新臉部新增至人員](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>重新命名人員

1. 若要重新命名人員模型中的人員，請選取 [清單] 功能表按鈕，然後從 [清單] 功能表中選擇 [**重新命名**]。
1. 選取人員的目前名稱，並輸入您的新名稱。
1. 選取 [核取] 按鈕，就會重新命名人員。

### <a name="delete-a-person"></a>刪除人員

1. 若要從您的人員模型中刪除人員，請選取 [清單] 功能表按鈕，然後從 [清單] 功能表中選擇 [**刪除**]。
1. 快顯視窗會告訴您，此動作將會刪除該人員，且此動作無法復原。
1. 再次選取 [**刪除**]，這將會移除人員模型中的人員。

### <a name="manage-a-person"></a>管理人員

如果您選取 [**管理**]，就會看到該人員**的詳細資料**視窗，其中包含要從中定型此 person 模型的所有臉部。 這些臉部來自于使用此人員模型的影片，或從您手動上傳的影像中出現的人員。

> [!TIP]
> 您可以按一下人員的名稱，或按一下 [**管理**]，以前往該**人員的詳細資料**視窗，如上所示。

#### <a name="add-a-face"></a>新增臉部

您可以藉由選取 [**新增影像**]，將更多人臉新增至人員。

#### <a name="delete-a-face"></a>刪除人臉

選取您想要刪除的映射，然後按一下 [**刪除**]。

#### <a name="rename-and-delete-the-person"></a>重新命名及刪除人員 

您可以使用 [管理] 窗格來重新命名人員，以及刪除人員模型中的人員。

## <a name="use-a-person-model-to-index-a-video"></a>使用人員模型來編制影片索引

您可以使用人員模型，在影片上傳期間指派人員模型，為新的影片編制索引。

若要在新的影片上使用您的人員模型，請執行下列步驟：

1. 選取頁面頂端的 [**上傳**] 按鈕。

    ![上傳人員模型](./media/customize-face-model/upload.png)

1. 將您的影片檔案放在圓形中，或流覽您的檔案。
1. 選取 [ **Advanced options** ] 箭號。

    ![上傳人員模型](./media/customize-face-model/upload2.png)

1. 選取下拉式選單，然後選取您所建立的人員模型。

    ![上傳人員模型](./media/customize-face-model/upload3.png)

1. 選取頁面底部的 [**上傳**] 選項，您的新影片會使用您的人員模型編制索引。

如果您未在上傳期間指定人員模型，影片索引子會使用您帳戶中的預設人員模型來為影片編制索引。

## <a name="use-a-person-model-to-reindex-a-video"></a>使用人員模型來重新編制影片索引

若要使用人員模型將集合中的影片重新編制索引，請移至影片索引子首頁上的帳戶影片，並將滑鼠停留在您想要重新編制索引的影片名稱上。

您會看到編輯、刪除及重新編制影片索引的選項。

1. 選取重新編制影片索引的選項。

    ![使用人員模型重新編制影片索引](./media/customize-face-model/reindex.png)

    您現在可以選取要用來重新編制影片索引的人員模型。
1. 選取下拉式選單，然後選取您想要使用的人員模型。

    ![使用人員模型重新編制影片索引](./media/customize-face-model/reindex2.png)

1. 選取 [重新**編制索引**] 按鈕，將會使用您的人員模型重新建立索引您的影片。

在您剛重新建立索引的影片中偵測到並辨識的臉部，將會儲存在您用來重新編制影片索引的人員模型中。

## <a name="managing-people-in-your-videos"></a>管理影片中的人員

您可以藉由編輯和刪除臉部來管理偵測到的臉部，以及在您索引的影片中辨識出的人。

刪除臉部會從影片的深入解析中移除特定的臉部。

編輯臉部會重新命名在您的影片中偵測到並可能被辨識的臉部。 當您編輯影片中的臉部時，該名稱會儲存為人員模型中，在上傳和編制索引期間指派給影片的人員專案。

如果您未在上傳期間將人員模型指派給影片，您的編輯會儲存在帳戶的預設人員模型中。

### <a name="edit-a-face"></a>編輯人臉

> [!NOTE]
> 如果人員模型有兩個或多個名稱相同的不同人員，您將無法在使用該人員模型的影片內標記該名稱。 在影片索引子中，您只能在內容模型自訂頁面的 [人員] 索引標籤中，對共用該名稱的人員進行變更。 基於這個理由，建議您為人員模型中的每個人提供唯一的名稱。

1. 瀏覽至影片索引子網站並登入。
1. 在您的帳戶中搜尋您要檢視和編輯的視訊。
1. 若要編輯影片中的臉部，請移至 [深入解析] 索引標籤，然後選取視窗右上角的鉛筆圖示。

    ![編輯影片中的臉部](./media/customize-face-model/edit-face.png)

1. 選取任何偵測到的臉部，並將其名稱從「未知的 #X」 (或先前指派給臉部) 的名稱。
1. 輸入新名稱之後，請選取新名稱旁的核取圖示。 此動作會儲存新名稱，並在您其他目前的影片和您上傳的未來影片中，辨識並命名此臉部的所有出現專案。 您其他目前視訊中的臉部辨識可能需要一些時間才會生效，因為這是批次處理。

如果您使用影片所用的人員模型中現有人員的名稱來命名臉部，則從該人員的這部影片偵測到的臉部影像會與模型中已存在的內容合併。 如果您將臉部命名為新的名稱，則會在影片所使用的人員模型中建立新的 Person 專案。

![編輯影片中的臉部](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>刪除人臉

若要刪除影片中偵測到的臉部，請移至 [深入解析] 窗格，然後選取窗格右上角的鉛筆圖示。 選取臉部名稱底下的 [**刪除**] 選項。 此動作會從影片中移除偵測到的臉部。 該人的臉部仍然會在出現的其他影片中偵測到，但您也可以在建立索引之後，從這些影片中刪除臉部。

如果使用者已命名，則也會繼續存在於用來為您刪除臉部的影片編制索引的人員模型中，除非您明確地從人員模型中刪除該人員。

![刪除您影片中的臉部](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>後續步驟

[使用 API 自訂人員模型](customize-person-model-with-api.md)
