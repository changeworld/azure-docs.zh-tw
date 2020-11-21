---
title: 音訊內容建立-語音服務
titleSuffix: Azure Cognitive Services
description: 建立音訊內容是一種線上工具，可讓您針對應用程式和產品自訂和微調 Microsoft 的文字到語音轉換輸出。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7d4d1acac591bfe6ce40efcf6166357d43718363
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024954"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>使用音訊內容建立工具改進合成

建立[音訊內容](https://aka.ms/audiocontentcreation)是一種容易使用且功能強大的工具，可讓您針對各種案例（例如有聲書;、新聞廣播、影片旁白和聊天機器人）建立高度自然的音訊內容。 藉由建立音訊內容，您可以微調文字轉換語音的語音，並以有效率且低成本的方式設計自訂音訊體驗。

此工具是以 [語音合成標記語言 (SSML) ](speech-synthesis-markup.md)為基礎。 它可讓您即時調整文字轉換語音的輸出屬性，或進行批次合成，例如語音字元、語音樣式、說話速度、發音和韻律。

您可以輕鬆地在接近50種不同語言（包括最先進的神經 TTS 語音）和您的自訂語音（如果有的話）的情況下，輕鬆存取超過150的預先建立語音。 

請參閱建立音訊內容的 [影片教學](https://www.youtube.com/watch?v=O1wIJ7mts_w) 課程。

## <a name="how-to-get-started"></a>如何開始？

音訊內容的建立是免費的工具，但您會支付所取用的 Azure 語音服務。 若要使用此工具，您必須使用 Azure 帳戶登入，並建立語音資源。 針對每個 Azure 帳戶，您有每月免費的語音配額，包括500000個字元用於類神經 TTS 語音 (每月) 、5000000個字元用於標準和自訂語音 (每月) ，以及1個自訂語音端點主機服務 (每個月) 。 每月分配數量通常已足夠給大約3-5 人的小型內容小組。 以下是如何建立 Azure 帳戶和取得語音資源的步驟。 

### <a name="step-1---create-an-azure-account"></a>步驟 1-建立 Azure 帳戶

若要使用音訊內容建立，您需要有 [Microsoft 帳戶](https://account.microsoft.com/account) 和 [Azure 帳戶](https://azure.microsoft.com/free/ai/)。 遵循這些指示來 [設定帳戶](./overview.md#try-the-speech-service-for-free)。 

[Azure 入口網站](https://portal.azure.com/) 是您管理 Azure 帳戶的集中位置。 您可以建立語音資源、管理產品存取，以及監視從簡單 web 應用程式到複雜雲端部署的所有專案。 

### <a name="step-2---create-a-speech-resource"></a>步驟 2-建立語音資源

註冊 Azure 帳戶之後，您必須在您的 Azure 帳戶下建立語音資源以存取語音服務。 查看 [如何建立語音資源](./overview.md#create-the-azure-resource)的指示。 

部署新的語音資源需要幾分鐘的時間。 部署完成後，您就可以開始進行音訊內容的建立旅程。 

 >[!NOTE]
   > 如果您打算使用類神經語音，請務必在 [支援類神經語音的區域](regions.md#standard-and-neural-voices)中建立您的資源。
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>步驟 3-使用您的 Azure 帳戶和語音資源登入音訊內容建立

1. 取得 Azure 帳戶和語音資源之後，您可以按一下 [**開始** 使用] 來登入 [音訊內容建立](https://aka.ms/audiocontentcreation)。
2. **語音資源** 頁面將會顯示給您。 選取您要處理的語音資源。 按一下 [ **移至 Studio** ] 開始建立音訊。 您也可以按一下 [ **建立新** 的]，在這裡建立新的語音資源。 當您下一次登入音訊內容建立工具時，我們會直接將您連結到目前語音資源下的音訊工作檔案。 
3. 您可以隨時使用 [ **設定** ] 選項（位於頂端導覽）來修改您的語音資源。

## <a name="how-to-use-the-tool"></a>如何使用此工具？

下圖顯示微調文字到語音轉換輸出所需要的步驟。 您可以使用下列連結來深入瞭解每個步驟。

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="微調文字轉語音輸出所需步驟的圖表":::


1. 選擇您想要處理的語音資源。
2. 使用純文字或 SSML 腳本[建立音訊微調](#create-an-audio-tuning-file)檔。 將您的內容輸入或上傳到音訊內容建立。
3. 選擇您的腳本內容的語音和語言。 音訊內容的建立包含所有的 [Microsoft 文字轉換語音](language-support.md#text-to-speech)語音。 您可以使用標準、類神經或您自己的自訂語音。
   >[!NOTE]
   > 閘道存取可供自訂神經語音使用，可讓您建立與自然發音語音類似的高定義語音。 如需其他詳細資訊，請參閱管制 [流程](./text-to-speech.md)。

4. 按一下 [ **播放** ] 圖示 (三角形) 預覽預設合成輸出。 然後藉由調整發音、斷路、音調、速率、聲調、聲音樣式等，來改善輸出。 如需完整的選項清單，請參閱 [語音合成標記語言](speech-synthesis-markup.md)。 以下 [影片](https://www.youtube.com/watch?v=O1wIJ7mts_w) 示範如何在建立音訊內容時微調語音輸出。 
5. 儲存並 [匯出您的微調音訊](#export-tuned-audio)。 當您將微調播放軌儲存在系統中時，您可以繼續工作並逐一查看輸出。 當您滿意輸出時，可以使用匯出功能建立音訊建立工作。 您可以觀察匯出工作的狀態，並下載輸出以搭配您的應用程式和產品使用。

## <a name="create-an-audio-tuning-file"></a>建立音訊微調檔

有兩種方式可讓您將內容帶入音訊內容建立工具。

**選項 1：**

1. 按一下右上方的 [ **新增** 檔案] 圖示，以建立新的音訊微調檔。
2. 在編輯視窗中輸入或貼上您的內容。 每個檔案的字元上限為20000。 如果腳本的長度超過20000個字元，您可以使用選項2自動將您的內容分割成多個檔案。 
3. 別忘了儲存。

**選項 2：**

1. 按一下 **[上傳** ] 以匯入一或多個文字檔。 支援純文字和 SSML。 如果您的腳本檔案超過20000個字元，請依段落、字元或正則運算式來分割檔案。 
3. 當您上傳文字檔時，請確定該檔案符合這些需求。

   | 屬性 | 值/附注 |
   |----------|---------------|
   | 檔案格式 | 純文字 (.txt)<br/> SSML 文字 ( .txt) <br/> 不支援壓縮檔案 |
   | 編碼格式 | UTF-8 |
   | 檔案名稱 | 每個檔案都必須有唯一的名稱。 不支援重複的專案。 |
   | 文字長度 | 文字檔的字元限制為20000。 如果您的檔案超過限制，請使用工具中的指示將檔案分割。 |
   | SSML 限制 | 每個 SSML 檔案只能包含單一的 SSML 片段。 |

**純文字範例**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**SSML 文字範例**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>匯出微調音訊

在您審核音訊輸出並對調整和調整感到滿意之後，就可以匯出音訊。

1. 按一下 [ **匯出** ] 以建立音訊建立工作。 建議 **匯出至音訊媒體** 櫃，因為它支援長音訊輸出和完整音訊輸出體驗。 您也可以直接將音訊下載到本機磁片，但只有前10分鐘可用。
2. 選擇您調整音訊的輸出格式。 以下提供支援的格式清單和取樣率。
3. 您可以在 [ **匯出** 工作] 索引標籤上查看工作的狀態。如果工作失敗，請參閱詳細資訊頁面以取得完整的報告。
4. 當工作完成時，您可以在 [ **音訊媒體** 櫃] 索引標籤上下載音訊。
5. 按一下 [下載] 。 現在您已準備好在您的應用程式或產品中使用自訂的微調音訊。

**支援的音訊格式**

| 格式 | 16 kHz 取樣率 | 24 kHz 取樣率 |
|--------|--------------------|--------------------|
| wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | 音訊-16khz-128kbitrate-mono-mp3 | 音訊-24khz-160kbitrate-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>如何新增/移除音訊內容建立使用者？

如果有一位以上的使用者想要使用音訊內容建立，您可以與使用者共用您的 Azure 帳戶和密碼，或將 Azure 訂用帳戶和語音資源的存取權授與使用者。 如果您將使用者新增至 Azure 訂用帳戶，使用者可以存取 Azure 訂用帳戶下的所有資源。 但是，如果您只將使用者新增至語音資源，使用者將只能存取語音資源，且無法存取此 Azure 訂用帳戶下的其他資源。 具有語音資源存取權的使用者可以使用音訊內容建立。

### <a name="add-users-to-a-speech-resource"></a>將使用者新增至語音資源

遵循下列步驟將使用者新增至語音資源，讓他們可以使用音訊內容建立。

1. 搜尋 [Azure 入口網站](https://portal.azure.com/)中的 **認知服務**，選取您要新增使用者的語音資源。
2. 按一下 [存取控制 (IAM)]。 按一下 [角色指派] 索引標籤以檢視此訂用帳戶的所有角色指派。
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="[角色指派] 索引標籤":::
1. 按一下 [新增] > [新增角色指派]，以開啟 [新增角色指派] 窗格。 在 [角色] 下拉式清單中，選取 [ **認知服務] 使用者** 角色。 如果您想要授與使用者此語音資源的擁有權，您可以選取 [ **擁有** 者] 角色。
1. 在清單中，選取使用者。 如果您在清單中看不到使用者，您可以在 [選取] 方塊中輸入，以在目錄中搜尋顯示名稱和電子郵件地址。 如果使用者不在此目錄中，您可以輸入 Azure active directory) 所信任的使用者 [Microsoft 帳戶](https://account.microsoft.com/account) (。
1. 按一下 [儲存] 以指派角色。 幾分鐘後，就會在語音資源範圍將認知服務使用者角色指派給使用者。

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="[新增角色] 對話方塊":::

1. 您新增的使用者將會收到邀請電子郵件。 按一下 [**接受邀請**  >  **接受] 以加入 Azure** 後，他們就可以使用 [音訊內容建立](https://aka.ms/audiocontentcreation)。

相同語音資源中的使用者將會在音訊內容建立 studio 中看到彼此的工作。 如果您想要讓每位使用者在音訊內容建立中具有唯一和私用的工作場所，請為每位使用者 [建立新的語音資源](#step-2---create-a-speech-resource) ，並為每位使用者提供語音資源的唯一存取權。 

### <a name="remove-users-from-a-speech-resource"></a>從語音資源移除使用者
1. 搜尋 Azure 入口網站中的 **認知服務** ，選取您要從中移除使用者的語音資源。
2. 按一下 [存取控制 (IAM)]。 按一下 [ **角色指派** ] 索引標籤，以查看此語音資源的所有角色指派。
3. 選取您要移除的使用者，然後按一下 **Remove**  >  **[移除確定]**。
    :::image source="media/audio-content-creation/remove-user.png" alt-text="[移除] 按鈕":::

### <a name="enable-users-to-grant-access"></a>讓使用者授與存取權
如果您想要讓其中一個使用者授與其他使用者的存取權，您必須為使用者提供語音資源的「擁有者」角色，並將使用者設定為「Azure 目錄讀者」。 
1. 將使用者新增為語音資源的擁有者。 瞭解 [如何將使用者新增至語音資源](#add-users-to-a-speech-resource)。
    :::image source="media/audio-content-creation/add-role.png" alt-text="角色擁有者欄位":::
1. 選取左上方的折迭功能表。 按一下 [ **Azure Active Directory**]，然後按一下 [ **使用者**]。
1. 搜尋使用者的 Microsoft 帳戶，然後移至使用者的詳細資料頁面。 按一下 [ **指派的角色**]。
1. 按一下 [**新增指派**  ->  **目錄讀取器**]。

## <a name="see-also"></a>另請參閱

* [長音訊 API](./long-audio-api.md)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com) \(英文\)
