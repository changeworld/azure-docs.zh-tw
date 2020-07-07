---
title: 使用影片索引子網站自訂語言模型
titleSuffix: Azure Media Services
description: 瞭解如何使用影片索引子網站自訂語言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80128088"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>使用影片索引器網站自訂語言模型

影片索引器可讓您上傳適應文字 (也就是從您希望引擎適應其詞彙的網域中取得的文字) 來建立自訂語言模型，以自訂語音辨識。 一旦您將模型定型之後，也會辨識出現在調整文字中的新字。

如需自訂語言模型的詳細總覽和最佳作法，請參閱[使用影片索引子自訂語言模型](customize-language-model-overview.md)。

您可以使用影片索引器網站，在您的帳戶中建立和編輯自訂語言模型，如本主題中所述。 您也可以使用 API，如[使用 API 自訂語言模型](customize-language-model-with-api.md)中所述。

## <a name="create-a-language-model"></a>建立語言模型

1. 移至[影片索引子](https://www.videoindexer.ai/)網站並登入。
2. 若要在您的帳戶中自訂模型，請選取頁面右上角的 [**內容模型自訂**] 按鈕。

   ![在影片索引子中自訂內容模型](./media/content-model-customization/content-model-customization.png)

3. 選取 [語言]**** 索引標籤。

    您會看到支援的語言清單。

    ![影片索引子中的語言模型清單](./media/customize-language-model/customize-language-model.png)

4. 在您想要的語言底下，選取 [**新增模型**]。
5. 輸入語言模型的名稱，然後按 Enter 鍵。

    此步驟會建立模型，並提供選項來將文字檔上傳至模型。

6. 若要新增文字檔，請選取 [**新增**檔案]。 您的檔案瀏覽器將會開啟。

7. 瀏覽至文字檔案並加以選取。 您可以將多個文字檔案新增至語言模型。

    您也可以選取語言模型右側的 **...** 按鈕，然後選取 [**新增**檔案]，來新增文字檔。

8. 當您完成上傳文字檔之後，請選取綠色的 [**定型**] 選項。

    ![在影片索引子中訓練語言模型](./media/customize-language-model/train-model.png)

定型程序需要數分鐘的時間。 定型完成後，您會在模型旁邊看到 [已定型]****。 您可以從模型預覽、下載和刪除檔案。

![影片索引子中的定型語言模型](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>在新的影片上使用語言模型

若要在新的影片上使用您的語言模型，請執行下列其中一個動作：

* 選取頁面頂端的 [**上傳**] 按鈕。

    ![上傳按鈕影片索引子](./media/customize-language-model/upload.png)

* 將音訊或影片檔案放入圓形中，或流覽您的檔案。

    ![上傳媒體檔案影片索引子](./media/customize-language-model/upload2.png)

系統會提供您選取**影片來源語言**的選項。 選取下拉式清單，然後選取您從清單中建立的語言模型。 它應該是語言模型的語言，並在括號中提供您賦予的名稱。

選取頁面底部的 [**上傳**] 選項，您的新影片會使用您的語言模型編制索引。

### <a name="using-a-language-model-to-reindex"></a>使用語言模型重新編製索引

若要使用您的語言模型為您收藏的視訊重新編製索引，請移至 [[影片索引器](https://www.videoindexer.ai/)] 首頁上的 [帳戶視訊]****，然後將滑鼠停駐在您想要重新編製索引之視訊的名稱上。

您會看到編輯影片、刪除影片，以及為您的影片重新編制索引的選項。 選取重新編制影片索引的選項。

![使用影片索引子重新編制索引](./media/customize-language-model/reindex1.png)

您可以選擇選取要用來重新編制影片索引的**影片來來源語言**。 選取下拉式清單，然後選取您從清單中建立的語言模型。 它應該是語言模型的語言，並在括號中提供您賦予的名稱。

![選擇影片來源語言-使用影片索引子重新編制影片索引](./media/customize-language-model/reindex.png)

選取 [**重新編制索引**] 按鈕，將會使用您的語言模型重新建立索引您的影片。

## <a name="edit-a-language-model"></a>編輯語言模型

若要編輯語言模型，您可以變更其名稱、在其中新增檔案，以及從中刪除檔案。

如果您從語言模型新增或刪除檔案，就必須選取綠色的 [**定型**] 選項，再次定型模型。

### <a name="rename-the-language-model"></a>重新命名語言模型

您可以選取語言模型右側的省略號（**...**）按鈕，然後選取 [**重新命名**]，以變更語言模型的名稱。

輸入新的名稱，然後按 Enter 鍵。

### <a name="add-files"></a>新增檔案

若要新增文字檔，請選取 [**新增**檔案]。 您的檔案瀏覽器將會開啟。

瀏覽至文字檔案並加以選取。 您可以將多個文字檔案新增至語言模型。

您也可以選取語言模型右邊的省略號（**...**）按鈕，然後選取 [**新增**檔案]，來新增文字檔。

### <a name="delete-files"></a>刪除檔案

若要從語言模型中刪除檔案，請選取文字檔右側的省略號（**...**）按鈕，然後選取 [**刪除**]。 此時會出現一個新視窗，告訴您刪除無法復原。 在新視窗中選取 [**刪除**] 選項。

此動作會從語言模型中完全移除檔案。

## <a name="delete-a-language-model"></a>刪除語言模型

若要從您的帳戶中刪除語言模型，請選取語言模型右側的省略號（**...**）按鈕，然後選取 [**刪除**]。

此時會出現一個新視窗，告訴您刪除無法復原。 在新視窗中選取 [**刪除**] 選項。

此動作會從帳戶中完全移除語言模型。 任何使用已刪除之語言模型的影片都會保留相同的索引，直到您重新編制影片索引為止。 如果您將影片重新編制索引，您可以將新的語言模型指派給影片。 否則，影片索引子會使用其預設模型來重新編制影片索引。

## <a name="customize-language-models-by-correcting-transcripts"></a>藉由修正文字記錄來自訂語言模型

影片索引子支援根據使用者對影片轉譯進行的實際更正，自動自訂語言模型。

1. 若要更正文字記錄，請從您的帳戶影片開啟您想要編輯的影片。 選取 [**時間軸**] 索引標籤。

    ![自訂語言模型時間軸索引標籤-影片索引子](./media/customize-language-model/timeline.png)

1. 選取鉛筆圖示以編輯轉譯的文字記錄。

    ![自訂語言模型編輯轉譯-影片索引子](./media/customize-language-model/edits.png)

    影片索引子會在您的影片轉譯中捕捉所有已更正的程式程式碼，並自動將其新增至稱為「從文字記錄編輯」的文字檔。 這些編輯用來重新定型用來為這部影片編制索引的特定語言模型。
    
    如果您在為這部影片編制索引時未指定語言模型，這段影片的所有編輯將會儲存在影片偵測到的語言內名為「帳戶採用原音」的預設語言模型中。
    
    如果對同一行進行了多項編輯，則只會使用最後一個版本的已更正行來更新語言模型。  
    
    > [!NOTE]
    > 只有文字更正會用於自訂。 不包含實際單字（例如，標點符號或空格）的更正。
    
1. 您將會在內容模型自訂頁面的 [語言] 索引標籤中看到文字記錄更正。

    ![自訂語言模型-影片索引子](./media/customize-language-model/customize.png)

   若要查看每個語言模型的「從文字記錄編輯」檔案，請選取它以開啟它。

    ![從文字記錄編輯-影片索引子](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>後續步驟

[使用 Api 自訂語言模型](customize-language-model-with-api.md)
