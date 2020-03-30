---
title: 使用視頻索引子進行動畫角色檢測
titleSuffix: Azure Media Services
description: 本主題演示如何將動畫角色檢測與視頻索引子一起使用。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989904"
---
# <a name="animated-character-detection-preview"></a>動畫角色檢測（預覽）

Azure 媒體服務視頻索引子支援通過與[認知服務自訂視覺](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)集成來檢測、分組和識別動畫內容中的字元。 此功能可通過門戶和 API 獲得。

使用特定動畫模型上傳動畫視頻後，視頻索引子提取關鍵幀、檢測這些幀中的動畫角色、對相似角色進行分組以及選擇最佳示例。 然後，它將分組字元發送到自訂視覺，根據所訓練的模型標識字元。 

在開始訓練模型之前，將無名地檢測到字元。 添加名稱並訓練模型時，視頻索引子將識別字元並相應地命名它們。

## <a name="flow-diagram"></a>流程圖

下圖演示了動畫角色檢測過程的流程。

![流程圖](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>帳戶

根據視頻索引子帳戶的類型，可以使用不同的功能集。 有關如何將帳戶連接到 Azure 的資訊，請參閱[創建連接到 Azure 的視頻索引子帳戶](connect-to-azure.md)。

* 試用帳戶：視頻索引子使用內部自訂視覺帳戶創建模型並將其連接到您的視頻索引子帳戶。 
* 付費帳戶：將自訂視覺帳戶連接到視頻索引子帳戶（如果您還沒有帳戶，則需要先創建一個帳戶）。

### <a name="trial-vs-paid"></a>試用 vs. 已付款

|功能|試用版|付費|
|---|---|---|
|自訂願景帳戶|由視頻索引子在幕後管理。 |您的自訂視覺帳戶已連接到視頻索引子。|
|動畫模型數|一個|每個帳戶最多 100 個型號（自訂視覺限制）。|
|定型模型|視頻索引子為新字元訓練模型，以補充現有字元的示例。|當帳戶擁有者準備好進行更改時，他們將訓練模型。|
|自訂視覺的高級選項|無法訪問自訂視覺門戶。|您可以在自訂視覺門戶中自行調整模型。|

## <a name="use-the-animated-character-detection-with-portal"></a>將動畫角色檢測及閘戶一起使用 

本節介紹開始使用動畫角色檢測模型所需的步驟。 

由於在試用帳戶中，自訂視覺集成由視頻索引子管理，因此您可以開始創建和使用動畫角色模型，並跳過以下部分（"連接自訂視覺帳戶"）。

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>連接自訂願景帳戶（僅限付費帳戶）

如果您擁有視頻索引子付費帳戶，則需要首先連接自訂視覺帳戶。 如果您還沒有自訂視覺帳戶，請創建一個帳戶。 有關詳細資訊，請參閱[自訂願景](../../cognitive-services/custom-vision-service/home.md)。

> [!NOTE]
> 兩個帳戶需要位於同一區域。 日本區域目前不支援自訂願景集成。

#### <a name="connect-a-custom-vision-account-with-api"></a>使用 API 連接自訂視覺帳戶 

按照以下步驟將自訂視覺帳戶連接到視頻索引子，或更改當前連接到視頻索引子的自訂視覺帳戶：

1. 流覽到[www.customvision.ai](https://www.customvision.ai)並登錄。
1. 複製以下鍵： 

    * 培訓金鑰（針對培訓資源）
    * 預測鍵（用於預測資源）
    * 端點 
    * 預測資源識別碼
    
    > [!NOTE]
    > 要提供所有金鑰，您需要在自訂視覺中具有兩個單獨的資源，一個用於培訓，一個用於預測。
1. 流覽並登錄到[視頻索引子](https://vi.microsoft.com/)。
1. 按一下頁面右上角的問號，然後選擇**API 參考**。
1. 通過按一下 **"產品"** 選項卡，確保您已訂閱 API 管理。如果連接了 API，則可以繼續執行下一步，否則，請訂閱。 
1. 在開發人員門戶上，按一下 **"完整的 API 引用"** 並流覽到**操作**。  
1. 選擇 **"連接自訂視覺帳戶（預覽）"，** 然後按一下"**試用"。**
1. 填寫所需的欄位以及訪問權杖，然後按一下"**發送**"。 

    有關如何獲取視頻索引子訪問權杖的詳細資訊，請訪問[開發人員門戶](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)，並查看[相關文檔](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)。  
1. 一旦呼叫返回 200 OK 回應，您的帳戶將連接。
1. 要通過流覽到[視頻索引子](https://vi.microsoft.com/)（ 門戶來驗證您的連接：
1. 按一下右上角**的內容模型自訂**按鈕。
1. 轉到 **"動畫角色"** 選項卡。
1. 按一下"自訂視覺"中的"管理模型"*後，您將被轉移到您剛剛連接的自訂視覺帳戶。

> [!NOTE]
> 目前，僅支援通過視頻索引子創建的模型。 通過自訂視覺創建的模型將不可用。 此外，最佳做法是僅通過視頻索引子平臺編輯通過視頻索引子創建的模型，因為通過自訂視覺所做的更改可能會導致意外的結果。

### <a name="create-an-animated-characters-model"></a>創建動畫角色模型

1. 瀏覽至[影片索引子](https://vi.microsoft.com/)網站並登入。
1. 按一下頁面右上角的內容模型自訂按鈕。

    ![內容模型自訂](./media/animated-characters-recognition/content-model-customization.png)
1. 轉到模型自訂部分中的 **"動畫角色"** 選項卡。
1. 按一下 **"添加模型**"。
1. 命名模型，然後按一下 Enter 以保存名稱。

> [!NOTE]
> 最佳做法是為每個動畫系列創建一個自訂視覺模型。 

### <a name="index-a-video-with-an-animated-model"></a>使用動畫模型為視頻編制索引

1. 按一下頂部功能表中的 **"上傳**"按鈕。
1. 選擇要上傳的視頻（從檔或 URL）。
1. 按一下 **"高級"選項**。
1. 在 **"人/動畫角色**"下選擇**動畫模型**。
1. 如果您有一個模型，它將自動選擇，如果您有多個模型，則可以從下拉式功能表中選擇相關的模型。
1. 點擊上傳。
1. 為視頻編制索引後，您將在 **"見解"** 窗格中的 **"動畫角色**"部分中看到檢測到的字元。

> [!NOTE] 
> 在標記和培訓模型之前，所有動畫角色都將命名為"未知#X"。 訓練模型後，它們也將被識別。

### <a name="customize-the-animated-characters-models"></a>自訂動畫角色模型

1. 標記並訓練模型。

    1. 通過編輯檢測到的字元的名稱來標記其檢測到的字元。 一旦將角色訓練到模型中，它將被識別為使用該模型索引的下一個視頻。 
    1. 要在視頻中標記動畫角色，請轉到 **"見解"** 選項卡，然後按一下視窗右上角的 **"編輯"** 按鈕。
    1. 在 **"見解"** 窗格中，按一下檢測到的任何動畫角色，並將其名稱從"未知#X"（或以前分配給該角色的名稱）更改。
    1. 輸入新名稱之後，按一下新名稱旁邊的核取圖示。 這將在視頻索引子中保存模型中的新名稱。
    1. 完成所有所需名稱的編輯後，需要訓練模型。

        打開自訂頁面並按一下 **"動畫角色"** 選項卡，然後按一下 **"訓練"** 按鈕以訓練模型。
         
        如果您有付費帳戶，則可以按一下 **"客戶願景"連結中的"管理模型**"（如下所示）。 然後，您將被轉發到自訂**視覺**中的模型頁面。
 
        ![內容模型自訂](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. 經過培訓，任何將用該模型編制索引或重新編制索引的視頻都將識別經過訓練的角色。 
    有權訪問其自訂視覺帳戶的付費帳戶可以看到模型和標記的圖像。 詳細瞭解在[自訂視覺中改進分類器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier)。

1. 刪除動畫角色。

    1. 要刪除視頻見解中的動畫角色，請轉到 **"見解"** 選項卡，然後按一下視窗右上角的 **"編輯"** 按鈕。
    1. 選擇動畫角色，然後按一下其名稱下的 **"刪除**"按鈕。

    > [!NOTE]
    > 這將從此視頻中刪除見解，但不會影響模型。

1. 刪除模型。

    1. 按一下頂部功能表上**的內容模型自訂**按鈕，然後轉到 **"動畫字元"** 選項卡。
    1. 按一下要刪除的模型右側的省略號圖示，然後按一下刪除按鈕。
    
    * 付費帳戶：模型將與視頻索引子斷開連接，您將無法重新連接它。
    * 試驗帳戶：該模型也將從海關願景中刪除。 
    
        > [!NOTE]
        > 在試用帳戶中，您只能使用一個模型。 刪除後，無法訓練其他模型。

## <a name="use-the-animated-character-detection-with-api"></a>將動畫字元檢測與 API 一起使用 

1. 連接自訂視覺帳戶。

    如果您擁有視頻索引子付費帳戶，則需要首先連接自訂視覺帳戶。 <br/>
    如果您還沒有自訂視覺帳戶，請創建一個帳戶。 有關詳細資訊，請參閱[自訂願景](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)。

    [使用 API 連接自訂視覺帳戶](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)。
1. 創建動畫角色模型。

    使用[創建動畫模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag)API。
1. 索引或重新索引視頻。

    使用[重新索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)API。 
1. 自訂動畫角色模型。

    使用[訓練動畫模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag)API。

### <a name="view-the-output"></a>檢視輸出

查看生成的 JSON 檔中的動畫字元。

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

* 目前，東亞地區不支援"動畫識別"功能。
* 如果視頻品質較差，則可能無法正確識別視頻中看似小或遠的字元。
* 建議使用每組動畫角色（例如，動畫系列）的模型。

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)
