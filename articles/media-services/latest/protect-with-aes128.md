---
title: 使用 AES-128 加密視頻
titleSuffix: Azure Media Services
description: 瞭解如何使用 AES 128 位加密加密視頻，以及如何在 Azure 媒體服務中使用金鑰傳遞服務。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974167"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>教程：使用 AES-128 加密視頻並使用金鑰交付服務

> [!NOTE]
> 雖然教學課程使用 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 範例，但是 [REST API](https://docs.microsoft.com/rest/api/media/liveevents)、[CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) 或其他受支援 [SDK](media-services-apis-overview.md#sdks) 的一般步驟都相同。

您可以利用 128 位元加密金鑰，使用媒體服務提供 HTTP 即時串流 (HLS)、MPEG-DASH，和透過 AES 加密的 Smooth Streaming。 媒體服務也提供加密金鑰傳遞服務，將加密金鑰傳遞至授權的使用者。 如果希望媒體服務動態加密視頻，請將加密金鑰與流式處理器相關聯，並配置內容金鑰策略。 當播放機請求流時，媒體服務使用指定的金鑰使用 AES-128 動態加密內容。 為了將串流解密，播放程式將向金鑰傳遞服務要求金鑰。 為了判斷使用者是否有權取得金鑰，服務會評估您為金鑰指定的內容金鑰原則。

您可以使用多種加密類型 (AES-128、PlayReady、Widevine、FairPlay) 來加密每項資產。 請參閱[串流通訊協定和加密類型](content-protection-overview.md#streaming-protocols-and-encryption-types)，以查看哪些組合可行。 此外，請參閱[如何使用 DRM 進行保護](protect-with-drm.md)。

本文示例的輸出包括 Azure 媒體播放機的 URL、清單 URL 和播放內容所需的 AES 權杖。 該示例將 JSON Web 權杖 （JWT） 權杖的過期設置為 1 小時。 您可以開啟瀏覽器並貼上產生的 URL，以啟動 Azure 媒體播放器示範頁面，其中已為您填入 URL 和權杖，格式如下：```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```。

本教學課程說明如何：

> [!div class="checklist"]
> * 下載本文中描述的[加密與AES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES)示例。
> * 開始使用具有 .NET SDK 的媒體服務 API。
> * 創建輸出資產。
> * 創建編碼轉換。
> * 提交作業。
> * 等待作業完成。
> * 創建內容金鑰策略
> * 將策略配置為使用 JWT 權杖限制。
> * 創建流式處理器。
> * 配置流式處理器以使用 AES（清除金鑰）加密視頻。
> * 獲取測試權杖。
> * 構建流式處理 URL。
> * 清除資源。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

需要有下列項目，才能完成教學課程。

* 請檢閱[內容保護概觀](content-protection-overview.md)文章。
* 安裝 Visual Studio Code 或 Visual Studio。
* [創建媒體服務帳戶](create-account-cli-quickstart.md)。
* 通過按照[訪問 API](access-api-cli-how-to.md)獲取使用媒體服務 API 所需的憑據。

## <a name="download-code"></a>下載程式碼

使用以下命令將包含完整 .NET 範例 (在本文中討論) 的 GitHub 存放庫複製到您的電腦：

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

「使用 AES-128 加密」範例位於 [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) 資料夾中。

> [!NOTE]
> 每次運行應用時，該示例都會創建唯一的資源。 通常，您將重用現有資源（如轉換和策略（如果現有資源具有所需的配置）。

## <a name="start-using-media-services-apis-with-net-sdk"></a>開始搭配使用媒體服務 API 與 .NET SDK

要開始使用具有 .NET 的媒體服務 API，請創建一個**AzureMediaServicesClient**物件。 若要建立物件，您需要提供必要的認證，讓用戶端使用 Azure AD 連線至 Azure。 在您於本文一開始複製的程式碼中，**GetCredentialsAsync** 函式會根據本機設定檔中提供的認證建立 ServiceClientCredentials 物件。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>建立輸出資產  

輸出[資產](https://docs.microsoft.com/rest/api/media/assets)存儲編碼作業的結果。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>取得或建立編碼轉換

建立新的[轉換](https://docs.microsoft.com/rest/api/media/transforms)執行個體時，您需要指定想要其產生的輸出是什麼。 必要的參數是 **TransformOutput** 物件，如下列程式碼所示。 每個 **TransformOutput** 都會包含 **Preset (預設)**。 **Preset** 會描述影片和/或音訊處理作業的逐步指示，以產生所需的 **TransformOutput**。 本文中所述的範例會使用稱為 **AdaptiveStreaming** 的內建 Preset。 預設根據輸入解析度和位元速率將輸入視頻編碼到自動生成的位元速率階梯（位元速率解析度對），然後生成 ISO MP4 檔，其中帶有 H.264 視頻和 AAC 音訊，對應于每個位元速率解析度對。

在創建新[的轉換](https://docs.microsoft.com/rest/api/media/transforms)之前，首先使用**Get**方法檢查是否存在，如下編所示。 在媒體服務 v3 中，如果實體不存在，對實體執行的 **Get** 方法會傳回 **null** (檢查名稱時不區分大小寫)。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>提交作業

如同前面所述，[轉換](https://docs.microsoft.com/rest/api/media/transforms)物件是配方，而[作業](https://docs.microsoft.com/rest/api/media/jobs)則是實際要求媒體服務，將**轉換**套用至指定的輸入影片或音訊內容。 **作業**會指定輸入影片的位置、輸出的位置等資訊。

在本教程中，我們基於直接從[HTTP 源 URL](job-input-from-http-how-to.md)引入的檔創建作業的輸入。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>請等待作業完成

此作業需要一些時間才能完成。 作業完成時，您會想要收到通知。 下面的代碼示例演示如何輪詢服務以訪問[作業](https://docs.microsoft.com/rest/api/media/jobs)的狀態。 對生產應用程式而言，輪詢不是建議的最佳做法，因為可能會發生延遲。 如果過度使用帳戶，輪詢可能會進行節流處理。 開發人員應改為使用事件方格。 如需詳細資訊，請參閱[將事件路由至自訂 Web 端點](job-state-events-cli-how-to.md)。

**作業**通常會經歷下列狀態：**已排程**、**已排入佇列**、**正在處理**、**已完成** (最後一個狀態)。 如果作業發生錯誤，您會收到**錯誤**狀態。 如果正在取消作業，您會收到**正在取消**的狀態，以及完成時的**已取消**狀態。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>創建內容金鑰策略

內容金鑰可提供資產的安全存取。 您需要創建**內容金鑰策略**，以配置內容金鑰如何傳遞到最終用戶端。 內容鍵與**流式處理器**相關聯。 媒體服務也提供加密金鑰傳遞服務，將加密金鑰傳遞至授權的使用者。

當播放機請求流時，媒體服務使用指定的金鑰動態加密內容（在這種情況下，通過使用 AES 加密）。要解密流，播放機從金鑰傳遞服務請求金鑰。 為了判斷使用者是否有權取得金鑰，服務會評估您為金鑰指定的內容金鑰原則。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>建立串流定位器

編碼完成且設定好內容金鑰原則後，下一個步驟是要讓用戶端可播放輸出資產中的視訊。 通過兩個步驟提供視頻：

1. 創建[流式處理器](https://docs.microsoft.com/rest/api/media/streaminglocators)。
2. 建置用戶端可使用的串流 URL。

創建**流式處理器**的過程稱為發佈。 預設情況下，**流式處理器在**進行 API 呼叫後立即有效。 除非配置可選的開始和結束時間，否則它將一直持續到刪除為止。

創建[流式處理器](https://docs.microsoft.com/rest/api/media/streaminglocators)時，您需要指定所需的**流式處理策略名稱**。 在本教程中，我們使用預定義的流式處理策略之一，它告訴 Azure 媒體服務如何發佈內容以進行流式處理。 在此示例中，應用 AES 信封加密（此加密也稱為 ClearKey 加密，因為金鑰通過 HTTPS 而不是 DRM 許可證傳遞到播放用戶端）。

> [!IMPORTANT]
> 使用自訂的 [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) 時，您應該為媒體服務帳戶設計一組受限的這類原則，並且在需要相同的加密選項和通訊協定時，對 StreamingLocators 重新使用這些原則。 媒體服務帳戶有 StreamingPolicy 項目的數量配額。 不應為每個流式處理器創建新的流式處理策略。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>取得測試權杖

在本教學課程中，我們會指定內容金鑰原則具有權杖限制。 權杖限制原則必須伴隨 Security Token Service (STS) 所發出的權杖。 媒體服務支援[JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)格式的權杖，這就是我們在示例中配置的權杖。

ContentKey識別碼聲明用於**內容金鑰策略**，這意味著呈現給金鑰傳遞服務的權杖必須具有內容金鑰的識別碼。 在示例中，在創建流定位器時，我們沒有指定內容金鑰，系統為我們創建了一個隨機金鑰。 要生成測試權杖，我們必須獲取 ContentKeyId 才能放入 ContentKey識別碼聲明。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>建置 DASH 串流 URL

現在，[已創建流式處理器，](https://docs.microsoft.com/rest/api/media/streaminglocators)您可以獲取流式處理 URL。 要生成 URL，您需要串聯[流終結點](https://docs.microsoft.com/rest/api/media/streamingendpoints)主機名稱和**流式處理器**路徑。 此範例會使用預設的** **串流端點**。 初次建立媒體服務帳戶時，此預設的** **串流端點**會處於停止狀態，因此您需要呼叫 **Start**。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>清除媒體服務帳戶中的資源

通常，除了計畫重用的物件之外，應清理所有內容（通常，您將重用轉換、流式處理器等）。 如果您想要在實驗之後有乾淨的帳戶，請刪除您不打算重複使用的資源。 例如，下列程式碼會刪除作業：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>清除資源

如果您不再需要資源群組中的任何資源 (包含您為教學課程建立的媒體服務和儲存體帳戶)，請將稍早建立的資源群組刪除。

執行下列 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="additional-notes"></a>其他注意事項

* Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 DRM 保護](protect-with-drm.md)
