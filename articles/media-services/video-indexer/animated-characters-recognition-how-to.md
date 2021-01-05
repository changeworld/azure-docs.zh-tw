---
title: 使用影片索引子的動畫字元偵測如何
titleSuffix: Azure Media Services
description: 此 how to 示範如何搭配影片索引子使用動畫字元偵測。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: 1ee179efbe936c742f1eb51b998c10f9349c14fb
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763382"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>透過入口網站和 API 使用動畫字元偵測 (預覽)  

Azure 媒體服務影片索引子支援偵測、群組和辨識動畫內容中的字元，這項功能可透過 Azure 入口網站和透過 API 取得。 請參閱 [此總覽](animated-characters-recognition.md) 主題。

本文示範如何使用 Azure 入口網站和影片索引子 API 的動畫字元偵測。

## <a name="use-the-animated-character-detection-with-portal"></a>透過入口網站使用動畫字元偵測 

在試用帳戶中，自訂視覺整合由影片索引子管理，您可以開始建立和使用動畫字元模型。 如果使用試用帳戶，您可以略過下列 ( "Connect a 自訂視覺 account" ) 區段。

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>只將自訂視覺帳戶連接 (付費帳戶) 

如果您擁有影片索引子付費帳戶，您需要先連接自訂視覺帳戶。 如果您還沒有自訂視覺帳戶，請建立一個帳戶。 如需詳細資訊，請參閱 [自訂視覺](../../cognitive-services/custom-vision-service/overview.md)。

> [!NOTE]
> 這兩個帳戶都必須位於相同的區域中。 日本區域目前不支援自訂視覺整合。

具有自訂視覺帳戶存取權的付費帳戶可以在該處看到模型和標記的影像。 深入瞭解如何 [改善自訂視覺中的分類器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier)。 

請注意，模型的定型應該只透過影片索引子完成，而不是透過自訂視覺網站。 

#### <a name="connect-a-custom-vision-account-with-api"></a>使用 API 連接自訂視覺帳戶 

請遵循下列步驟，將您自訂視覺帳戶連接到影片索引子，或變更目前連線到影片索引子的自訂視覺帳戶：

1. 流覽至 [www.customvision.ai](https://www.customvision.ai) 並登入。
1. 複製定型和預測資源的索引鍵：

    > [!NOTE]
    > 若要提供所有的金鑰，您必須在自訂視覺中有兩個不同的資源，一個用於定型，另一個用於預測。
1. 提供其他資訊：

    * 端點 
    * 預測資源識別碼
1. 流覽並登入 [影片索引子](https://vi.microsoft.com/)。
1. 按一下頁面右上角的問號，然後選擇 [ **API 參考**]。
1. 按一下 [ **產品** ] 索引標籤，確認您已訂閱 API 管理。如果您有已連線的 API，您可以繼續進行下一個步驟，否則請訂閱。 
1. 在開發人員入口網站上，按一下 [ **完整 API 參考** ]，然後流覽至 [ **作業**]。  
1. 選取 **[連接自訂視覺帳戶] (預覽)** 然後按一下 [ **試試看**]。
1. 填寫必要欄位以及存取權杖，然後按一下 [ **傳送**]。 

    如需如何取得影片索引子存取權杖的詳細資訊，請移至 [開發人員入口網站](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)，並查看 [相關的檔](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)。  
1. 一旦呼叫傳回 200 OK 回應，您的帳戶就會連線。
1. 若要驗證您的連線，請流覽至 [影片索引子](https://vi.microsoft.com/)) 入口網站：
1. 按一下右上角的 [ **內容模型自訂** ] 按鈕。
1. 移至 [ **動畫字元** ] 索引標籤。
1. 當您在自訂視覺中按一下 [管理模型] 之後，就會傳送至您剛剛連線的自訂視覺帳戶。

> [!NOTE]
> 目前只支援透過影片索引子建立的模型。 透過自訂視覺建立的模型將無法使用。 此外，最佳作法是只透過影片索引子平臺編輯透過影片索引子所建立的模型，因為透過自訂視覺所做的變更可能會導致非預期的結果。

### <a name="create-an-animated-characters-model"></a>建立動畫字元模型

1. 瀏覽至[影片索引子](https://vi.microsoft.com/)網站並登入。
1. 若要在您的帳戶中自訂模型，請選取頁面左側的 [ **內容模型自訂** ] 按鈕。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="在影片索引子中自訂內容模型":::
1. 移至 [模型自訂] 區段中的 [ **動畫字元** ] 索引標籤。
1. 按一下 [ **加入模型**]。
1. 命名您的模型，然後按一下 enter 儲存名稱。

> [!NOTE]
> 最佳做法是為每個動畫數列各有一個自訂視覺模型。 

### <a name="index-a-video-with-an-animated-model"></a>使用動畫模型為影片編制索引

若為初始訓練，請上傳至少兩部影片。 每個都應該在預期良好的辨識模型之前，最好超過15分鐘。 如果您有較短的劇集，建議您在訓練之前，先上傳至少30分鐘的影片內容。 這可讓您合併來自不同場景和背景之相同字元的群組，因此會增加在您編制索引的下列集中字元偵測到字元的機會。 若要在多個影片上定型模型 (劇集) 您需要使用相同的動畫模型來編制它們的索引。 

1. 按一下 [上 **傳** ] 按鈕。
1. 選擇要從檔案或 URL) 上傳 (的影片。
1. 按一下 [ **Advanced options**]。
1. 在 [ **人員/動畫字元** ] 下，選擇 [ **動畫模型**]。
1. 如果您有一個模型，它會自動選擇，如果您有多個模型，您可以從下拉式功能表中選擇相關的模型。
1. 按一下 [上傳]。
1. 影片編制索引之後，您會在 [**見解**] 窗格的 [**動畫字元**] 區段中看到偵測到的字元。

標記和定型模型之前，所有的動畫字元將會命名為「未知 #X」。 定型模型之後，也會加以辨識。

### <a name="customize-the-animated-characters-models"></a>自訂動畫字元模型

1. 在影片索引子中為字元命名。

    1. 在建立模型的字元群組之後，建議您在自訂視覺中檢查這些群組。 
    1. 若要在您的影片中標記動畫字元，請移至 [ **見解**] 索引標籤   ，然後按一下視窗右上角的 [ **編輯**]   按鈕。 
    1. 在 [ **見解**   ] 窗格中，按一下任何偵測到的動畫字元，並將其名稱從「未知的 #X」變更為暫時名稱 (或先前指派給字元) 的名稱。 
    1. 輸入新名稱之後，按一下新名稱旁邊的核取圖示。 這會將新名稱儲存在影片索引子的模型中。 
1. 僅限付費帳戶：請參閱自訂視覺中的群組 

    > [!NOTE]
    > 具有自訂視覺帳戶存取權的付費帳戶可以在該處看到模型和標記的影像。 深入瞭解如何 [改善自訂視覺中的分類器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier)。 請務必注意，模型的定型只應透過影片索引子 (完成（如本 topid) 所述），而不是透過自訂視覺網站。 

    1. 移至影片索引子中的 [ **自訂模型** ] 頁面，然後選擇 [ **動畫字元** ] 索引標籤。 
    1. 按一下您正在處理之模型的 [編輯] 按鈕，以便在自訂視覺中進行管理。 
    1. 檢查每個字元群組： 

        * 如果群組包含不相關的影像，建議您在自訂視覺網站中刪除這些映射。 
        * 如果有屬於不同字元的影像，請按一下影像以變更這些特定影像上的標籤，並新增正確的標籤，並刪除錯誤的標記。 
        * 如果群組不正確，也就是包含多個字元的非字元影像或影像，您可以在自訂視覺網站或影片索引子深入解析中刪除。 
        * 群組演算法有時會將您的字元分割成不同的群組。 因此，建議您將屬於相同字元名稱的所有群組， (在影片索引子深入解析) 中，這會立即使所有這些群組在自訂視覺網站中顯示為開啟。 
    1. 調整群組之後，請確定您用來標記它的初始名稱會反映該群組中的字元。 
1. 將模型定型 

    1. 完成所有您想要的名稱編輯之後，您必須將模型定型。 
    1. 一旦將字元定型到模型之後，它就會被辨識為下一個使用該模型編制索引的影片。 
    1. 開啟 [自訂] 頁面，按一下 [ **動畫字元**] 索引標籤   ，然後按一下 [ **定型**] 按鈕以定型您的模型。 為了保持影片之間的連接 
    
索引子和模型不會將自訂視覺網站中的模型定型 (付費帳戶只能在影片索引子中存取自訂視覺網站) 。 定型之後，任何將使用該模型編制索引或重新建立索引的影片都會辨識定型的字元。 

## <a name="delete-an-animated-character-and-the-model"></a>刪除動畫字元和模型

1. 刪除動畫字元。

    1. 若要刪除影片深入解析中的動畫字元，請移至 [ **見解** ] 索引標籤，然後按一下視窗右上角的 [ **編輯** ] 按鈕。
    1. 選擇動畫字元，然後按一下其名稱下的 [ **刪除** ] 按鈕。

    > [!NOTE]
    > 這將會刪除此影片中的見解，但不會影響模型。
1. 刪除模型。

    1. 按一下頂端功能表上的 [ **內容模型自訂** ] 按鈕，然後移至 [ **動畫字元** ] 索引標籤。
    1. 按一下您想要刪除之模型右邊的省略號圖示，然後按一下 [刪除] 按鈕。
    
    * 付費帳戶：此模型將會從影片索引子中斷連線，而您將無法重新連接。
    * 試用帳戶：模型也會從海關視覺中刪除。 
    
        > [!NOTE]
        > 在試用帳戶中，您只能有一個可用的模型。 刪除之後，您就無法定型其他模型。

## <a name="use-the-animated-character-detection-with-api"></a>使用 API 的動畫字元偵測 

1. 連接自訂視覺帳戶。

    如果您擁有影片索引子付費帳戶，您需要先連接自訂視覺帳戶。 <br/>
    如果您還沒有自訂視覺帳戶，請建立一個帳戶。 如需詳細資訊，請參閱 [自訂視覺](../../cognitive-services/custom-vision-service/overview.md)。

    [使用 API 連接您的自訂視覺帳戶](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)。
1. 建立動畫字元模型。

    使用 [建立動畫模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) API。
1. 為影片編制索引或重新編制索引。

    使用 [重新編制索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) API。 
1. 自訂動畫字元模型。

    使用「 [定型動畫模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) API」。

### <a name="view-the-output"></a>檢視輸出

請參閱產生的 JSON 檔案中的動畫字元。

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>限制

* East-Asia 區域目前不支援「動畫識別」功能。
* 如果影片的品質很差，則可能無法正確識別出現在影片中的字元。
* 建議您針對每一組動畫字元使用模型 (例如，每個動畫系列) 。

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)
