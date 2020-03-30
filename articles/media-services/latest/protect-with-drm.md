---
title: Azure 媒體服務 DRM 加密和許可證交付服務
titleSuffix: Azure Media Services
description: 瞭解如何使用 DRM 動態加密和許可證交付服務來交付使用 Microsoft PlayReady、Google Widevine 或 Apple FairPlay 許可證加密的流。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 14ba5f270138db22a76fd697b264046e22577427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086727"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>教程：使用 DRM 動態加密和許可證交付服務

> [!NOTE]
> 雖然本教學課程使用 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 範例，但是 [REST API](https://docs.microsoft.com/rest/api/media/liveevents)、[CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) 或其他受支援 [SDK](media-services-apis-overview.md#sdks) 的一般步驟都相同。

您可以使用 Azure 媒體服務，傳遞以 Microsoft PlayReady、Google Widevine 或 Apple FairPlay 授權加密的串流。 有關深入解釋，請參閱[使用動態加密的內容保護](content-protection-overview.md)。

媒體服務還提供提供 PlayReady、Widevine 和 FairPlay DRM 許可證的服務。 當使用者請求受 DRM 保護的內容時，播放機應用會從媒體服務許可證服務請求許可證。 如果播放機應用已授權，媒體服務許可證服務會向該播放機頒發許可證。 授權包含解密金鑰，可被用戶端播放器用來解密和串流處理內容。

本文以[使用 DRM 進行加密](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM)範例為基礎。

本文中說明的範例會產生下列結果：

![在 Azure 媒體播放機中帶有 DRM 保護視頻的 AMS](./media/protect-with-drm/ams_player.png)

本教學課程說明如何：

> [!div class="checklist"]
> * 創建編碼轉換。
> * 設定用來驗證權杖的簽署金鑰。
> * 設置內容金鑰策略的要求。
> * 使用指定的流式處理策略創建流式處理器。
> * 創建用於播放檔的 URL。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

需要有下列項目，才能完成教學課程：

* 請檢閱[內容保護概觀](content-protection-overview.md)文章。
* 查看[具有存取控制的設計多 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)。
* 安裝 Visual Studio Code 或 Visual Studio。
* 建立新的 Azure 媒體服務帳戶，如[此快速入門](create-account-cli-quickstart.md)所述。
* 藉由遵循[存取 API](access-api-cli-how-to.md) 以取得使用媒體服務 API 所需的認證
* 在應用設定檔（appsettings.json）中設置適當的值。

## <a name="download-code"></a>下載程式碼

使用以下命令將包含完整 .NET 範例 (在本文中討論) 的 GitHub 存放庫複製到您的電腦：

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
「使用 DRM 進行加密」範例位於 [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) 資料夾中。

> [!NOTE]
> 每次運行應用時，該示例都會創建唯一的資源。 通常，您將重用現有資源（如轉換和策略（如果現有資源具有所需的配置）。

## <a name="start-using-media-services-apis-with-net-sdk"></a>開始搭配使用媒體服務 API 與 .NET SDK

要開始使用具有 .NET 的媒體服務 API，請創建一個**AzureMediaServicesClient**物件。 若要建立物件，您需要提供必要的認證，讓用戶端使用 Azure AD 連線至 Azure。 在您於本文一開始複製的程式碼中，**GetCredentialsAsync** 函式會根據本機設定檔中提供的認證建立 ServiceClientCredentials 物件。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>建立輸出資產  

輸出[資產](assets-concept.md)存儲編碼作業的結果。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>取得或建立編碼轉換

建立新的[轉換](transforms-jobs-concept.md)執行個體時，您需要指定想要其產生的輸出是什麼。 所需的參數是一個`transformOutput`物件，如下代碼所示。 每個 TransformOutput 都會包含 **Preset (預設)**。 Preset 會描述影片和/或音訊處理作業的逐步指示，以產生所需的 TransformOutput。 本文中所述的範例會使用稱為 **AdaptiveStreaming** 的內建 Preset。 預設根據輸入解析度和位元速率將輸入視頻編碼為自動生成的位元速率階梯（位元速率解析度對），並生成 ISO MP4 檔，其中具有 H.264 視頻和 AAC 音訊，對應于每個位元速率解析度對。 

在建立新的**轉換**之前，您應該先使用 **Get** 方法檢查是否已有轉換存在，如後續程式碼所示。  在媒體服務 v3 中，如果實體不存在，對實體執行的 **Get** 方法會傳回 **null** (檢查名稱時不區分大小寫)。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>提交作業

如同前面所述，**轉換**物件是配方，而[作業](transforms-jobs-concept.md)則是實際要求媒體服務，將**轉換**套用至指定的輸入影片或音訊內容。 **作業**會指定輸入影片的位置、輸出的位置等資訊。

在本教程中，我們基於直接從[HTTP 源 URL](job-input-from-http-how-to.md)引入的檔創建作業的輸入。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>請等待作業完成

此作業需要一些時間才能完成。 作業完成時，您會想要收到通知。 下面的代碼示例演示如何輪詢服務以訪問**作業**的狀態。 對生產應用程式而言，輪詢不是建議的最佳做法，因為可能會發生延遲。 如果過度使用帳戶，輪詢可能會進行節流處理。 開發人員應改為使用事件方格。 請參閱[將事件路由至自訂 Web 端點](job-state-events-cli-how-to.md)。

**作業**通常會經歷下列狀態：**已排程**、**已排入佇列**、**正在處理**、**已完成** (最後一個狀態)。 如果作業發生錯誤，您會收到**錯誤**狀態。 如果正在取消作業，您會收到**正在取消**的狀態，以及完成時的**已取消**狀態。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>創建內容金鑰策略

內容金鑰可提供資產的安全存取。 使用 DRM 加密內容時，需要創建[內容金鑰策略](content-key-policy-concept.md)。 該策略配置內容金鑰如何傳遞到最終用戶端。 內容鍵與流式處理器相關聯。 媒體服務也提供加密金鑰傳遞服務，可將加密金鑰和授權傳遞給已授權的使用者。

您需要在**內容金鑰策略**上設置要求（限制），必須滿足這些要求（限制）才能使用指定的配置傳遞金鑰。 在此範例中，我們會設定下列組態和需求：

* 組態

    設定 [PlayReady](playready-license-template-overview.md) 和 [Widevine](widevine-license-template-overview.md) 授權，使其可由媒體服務授權傳遞服務來傳遞。 即使此示例應用未配置[FairPlay](fairplay-license-overview.md)許可證，它包含可用於配置公平播放的方法。 您可以將 FairPlay 配置添加為另一個選項。

* 限制

    應用在策略上設置 JSON Web 權杖 （JWT） 權杖類型限制。

播放程式要求串流時，媒體服務便會使用指定的金鑰動態加密您的內容。 為了將串流解密，播放程式將向金鑰傳遞服務要求金鑰。 為了判斷使用者是否有權取得金鑰，服務會評估您為金鑰指定的內容金鑰原則。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>建立串流定位器

編碼完成且設定好內容金鑰原則後，下一個步驟是要讓用戶端可播放輸出資產中的視訊。 通過兩個步驟提供視頻：

1. 創建[流式處理器](streaming-locators-concept.md)。
2. 建置用戶端可使用的串流 URL。

創建**流式處理器**的過程稱為發佈。 預設情況下，**流式處理器在**進行 API 呼叫後立即有效。 除非配置可選的開始和結束時間，否則它將一直持續到刪除為止。

創建**流式處理器**時，需要指定所需的`StreamingPolicyName`。 在本教程中，我們使用預定義的流式處理策略之一，它告訴 Azure 媒體服務如何發佈用於流式處理的內容。 在此範例中，我們將 StreamingLocator.StreamingPolicyName 設定為 "Predefined_MultiDrmCencStreaming" 原則。 應用 PlayReady 和 Widevine 加密，並根據配置的 DRM 許可證將金鑰傳遞到播放用戶端。 如果您也想要使用 CBCS (FairPlay) 為串流加密，請使用 "Predefined_MultiDrmStreaming"。

> [!IMPORTANT]
> 使用自訂的[串流原則](streaming-policy-concept.md)時，您應該為媒體服務帳戶設計一組受限的這類原則，並且在需要相同的加密選項和通訊協定時，對 StreamingLocators 重新使用這些原則。 媒體服務帳戶有 StreamingPolicy 項目的數量配額。 不應為每個流式處理器創建新的流式處理策略。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>取得測試權杖

在本教學課程中，我們會指定內容金鑰原則具有權杖限制。 權杖限制原則必須伴隨 Security Token Service (STS) 所發出的權杖。 媒體服務支援[JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)格式的權杖，這就是我們在示例中配置的權杖。

ContentKeyIdentifierClaim 用於 ContentKeyPolicy，表示向金鑰傳遞服務提出的權杖必須具有 ContentKey 的識別碼。 在示例中，在創建流式處理器時，我們不會指定內容金鑰，系統會為我們創建隨機金鑰。 要生成測試權杖，我們必須獲取 ContentKeyId 才能放入 ContentKey識別碼聲明。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>構建流式處理 URL

建立了 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) 之後，現在您就可以取得串流 URL。 要生成 URL，您需要串聯[流終結點](https://docs.microsoft.com/rest/api/media/streamingendpoints)主機名稱和**流式處理器**路徑。 此範例會使用預設的** **串流端點**。 初次建立媒體服務帳戶時，此預設的** **串流端點**會處於停止狀態，因此您需要呼叫 **Start**。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

運行應用時，您將看到以下螢幕：

![使用 DRM 保護](./media/protect-with-drm/playready_encrypted_url.png)

您可以開啟瀏覽器並貼上產生的 URL，以啟動 Azure 媒體播放器示範頁面，其中已為您填入 URL 和權杖。

## <a name="clean-up-resources-in-your-media-services-account"></a>清除媒體服務帳戶中的資源

通常，除了計畫重用的物件之外，應清理所有內容（通常，您將重用轉換、流式處理器等）。 如果您想要在實驗之後有乾淨的帳戶，請刪除您不打算重複使用的資源。 例如，下列程式碼會刪除作業：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>清除資源

如果您不再需要資源群組中的任何資源 (包含您為教學課程建立的媒體服務和儲存體帳戶)，請將稍早建立的資源群組刪除。

執行下列 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>其他注意事項

* Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

簽出

> [!div class="nextstepaction"]
> [使用 AES-128 保護](protect-with-aes128.md)
