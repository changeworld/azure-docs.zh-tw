---
title: Azure 媒體服務 DRM 加密和授權傳遞服務
titleSuffix: Azure Media Services
description: 瞭解如何使用 DRM 動態加密和授權傳遞服務來傳遞以 Microsoft PlayReady、Google Widevine 或 Apple FairPlay 授權加密的串流。
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
ms.date: 07/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8ab8a3ce0718cac3135bfdac67088d36fcd4f184
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87060621"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>教學課程：使用 DRM 動態加密與授權傳遞服務

> [!NOTE]
> 雖然本教學課程使用 [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 範例，但是 [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event?view=azure-cli-latest) 或其他受支援 [SDK](media-services-apis-overview.md#sdks) 的一般步驟都相同。

您可以使用 Azure 媒體服務，傳遞以 Microsoft PlayReady、Google Widevine 或 Apple FairPlay 授權加密的串流。 如需深入說明，請參閱[使用動態加密進行內容保護](content-protection-overview.md)。

媒體服務也提供傳遞 PlayReady、Widevine 和 FairPlay DRM 授權的服務。 當使用者要求受 DRM 保護的內容時，播放機應用程式會向媒體服務授權服務要求授權。 若播放機應用程式已獲授權，媒體服務授權服務就會發出授權給播放機。 授權包含解密金鑰，可被用戶端播放器用來解密和串流處理內容。

本文以[使用 DRM 進行加密](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM)範例為基礎。

本文中說明的範例會產生下列結果：

![Azure 媒體播放機中具有 DRM 保護影片的 AMS](./media/protect-with-drm/ams_player.png)

本教學課程說明如何：

> [!div class="checklist"]
> * 建立編碼轉換。
> * 設定用來驗證權杖的簽署金鑰。
> * 設定內容金鑰原則的需求。
> * 建立具有指定串流原則的 StreamingLocator。
> * 建立用來播放檔案的 URL。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先決條件

需要有下列項目，才能完成教學課程：

* 請檢閱[內容保護概觀](content-protection-overview.md)文章。
* 請參閱[使用存取控制設計多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)。
* 安裝 Visual Studio Code 或 Visual Studio。
* 建立新的 Azure 媒體服務帳戶，如[此快速入門](./create-account-howto.md)所述。
* 藉由遵循[存取 API](./access-api-howto.md) 以取得使用媒體服務 API 所需的認證
* 在應用程式佈建檔中設定適當的值（appsettings.js開啟）。

## <a name="download-code"></a>下載程式碼

使用以下命令將包含完整 .NET 範例 (在本文中討論) 的 GitHub 存放庫複製到您的電腦：

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
「使用 DRM 進行加密」範例位於 [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) 資料夾中。

> [!NOTE]
> 此範例會在您每次執行應用程式時建立唯一的資源。 一般而言，您會重複使用現有的資源，例如轉換和原則（如果現有資源具有必要的設定）。

## <a name="start-using-media-services-apis-with-net-sdk"></a>開始搭配使用媒體服務 API 與 .NET SDK

若要開始搭配使用媒體服務 Api 與 .NET，請建立**AzureMediaServicesClient**物件。 若要建立物件，您需要提供必要的認證，讓用戶端使用 Azure AD 連線至 Azure。 在您於本文一開始複製的程式碼中，**GetCredentialsAsync** 函式會根據本機設定檔中提供的認證建立 ServiceClientCredentials 物件。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>建立輸出資產  

輸出[資產](assets-concept.md)會儲存您的編碼作業結果。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>取得或建立編碼轉換

建立新的[轉換](transforms-jobs-concept.md)執行個體時，您需要指定想要其產生的輸出是什麼。 必要的參數是 `transformOutput` 物件，如下列程式碼所示。 每個 TransformOutput 都會包含 **Preset (預設)** 。 Preset 會描述影片和/或音訊處理作業的逐步指示，以產生所需的 TransformOutput。 本文中所述的範例會使用稱為 **AdaptiveStreaming** 的內建 Preset。 預設會根據輸入解析度和位元速率，將輸入影片編碼成自動產生的位元速率階梯（位元速率解析組），並使用 h.264 video 和 AAC 音訊（對應于每個位元速率解析組）來產生 ISO 的已執行檔。 

在建立新的**轉換**之前，您應該先使用 **Get** 方法檢查是否已有轉換存在，如後續程式碼所示。  在媒體服務 v3 中，如果實體不存在，對實體執行的 **Get** 方法會傳回 **null** (檢查名稱時不區分大小寫)。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>提交作業

如同前面所述，**轉換**物件是配方，而[作業](transforms-jobs-concept.md)則是實際要求媒體服務，將**轉換**套用至指定的輸入影片或音訊內容。 **作業**會指定輸入影片的位置、輸出的位置等資訊。

在本教學課程中，我們會根據直接從[HTTPs 來源 URL](job-input-from-http-how-to.md)內嵌的檔案，建立作業的輸入。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>請等待作業完成

此作業需要一些時間才能完成。 作業完成時，您會想要收到通知。 下列程式碼範例說明如何輪詢服務以取得**作業**狀態。 對生產應用程式而言，輪詢不是建議的最佳做法，因為可能會發生延遲。 如果過度使用帳戶，輪詢可能會進行節流處理。 開發人員應改為使用事件方格。 請參閱[將事件路由至自訂 Web 端點](job-state-events-cli-how-to.md)。

**作業**通常會經歷下列狀態：**已排程**、**已排入佇列**、**正在處理**、**已完成** (最後一個狀態)。 如果作業發生錯誤，您會收到**錯誤**狀態。 如果正在取消作業，您會收到**正在取消**的狀態，以及完成時的**已取消**狀態。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>建立內容金鑰原則

內容金鑰可提供資產的安全存取。 使用 DRM 加密內容時，您必須建立[內容金鑰原則](content-key-policy-concept.md)。 原則會設定如何將內容金鑰傳遞給終端用戶端。 內容金鑰與串流定位器相關聯。 媒體服務也提供加密金鑰傳遞服務，可將加密金鑰和授權傳遞給已授權的使用者。

您必須在**內容金鑰原則**上設定需要符合的需求（限制），才能使用指定的設定來傳遞金鑰。 在此範例中，我們會設定下列組態和需求：

* 組態

    設定 [PlayReady](playready-license-template-overview.md) 和 [Widevine](widevine-license-template-overview.md) 授權，使其可由媒體服務授權傳遞服務來傳遞。 雖然此範例應用程式不會設定[FairPlay](fairplay-license-overview.md)授權，但它包含可用來設定 FairPlay 的方法。 您可以新增 FairPlay 設定作為另一個選項。

* 限制

    應用程式會設定原則的 JSON Web 權杖（JWT）權杖類型限制。

播放程式要求串流時，媒體服務便會使用指定的金鑰動態加密您的內容。 為了將串流解密，播放程式將向金鑰傳遞服務要求金鑰。 為了判斷使用者是否有權取得金鑰，服務會評估您為金鑰指定的內容金鑰原則。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>建立串流定位器

編碼完成且設定好內容金鑰原則後，下一個步驟是要讓用戶端可播放輸出資產中的視訊。 您可以使用兩個步驟來提供影片：

1. 建立[串流定位器](streaming-locators-concept.md)。
2. 建置用戶端可使用的串流 URL。

建立**串流定位器**的程式稱為「發行」。 根據預設，**串流定位器**會在您進行 API 呼叫之後立即生效。 除非您設定選擇性的開始和結束時間，否則它會持續到刪除為止。

建立**串流定位器**時，您需要指定所需的 `StreamingPolicyName` 。 在本教學課程中，我們會使用其中一個預先定義的串流原則，告訴 Azure 媒體服務如何發佈內容以進行串流處理。 在此範例中，我們將 StreamingLocator.StreamingPolicyName 設定為 "Predefined_MultiDrmCencStreaming" 原則。 會套用 PlayReady 和 Widevine 加密，並根據所設定的 DRM 授權將金鑰傳遞至播放用戶端。 如果您也想要使用 CBCS (FairPlay) 為串流加密，請使用 "Predefined_MultiDrmStreaming"。

> [!IMPORTANT]
> 使用自訂的[串流原則](streaming-policy-concept.md)時，您應該為媒體服務帳戶設計一組受限的這類原則，並且在需要相同的加密選項和通訊協定時，對 StreamingLocators 重新使用這些原則。 媒體服務帳戶有 StreamingPolicy 項目的數量配額。 您不應該為每個 StreamingLocator 建立新的 StreamingPolicy。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>取得測試權杖

在本教學課程中，我們會指定內容金鑰原則具有權杖限制。 權杖限制原則必須伴隨 Security Token Service (STS) 所發出的權杖。 媒體服務支援[JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3)格式的權杖，這就是我們在範例中設定的內容。

ContentKeyIdentifierClaim 用於 ContentKeyPolicy，表示向金鑰傳遞服務提出的權杖必須具有 ContentKey 的識別碼。 在範例中，我們不會在建立串流定位器時指定內容金鑰，系統會為我們建立隨機的索引鍵。 若要產生測試權杖，我們必須取得要放入 ContentKeyIdentifierClaim 宣告中的 ContentKeyId。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>建立串流 URL

建立了 [StreamingLocator](/rest/api/media/streaminglocators) 之後，現在您就可以取得串流 URL。 若要建立 URL，您需要串連[StreamingEndpoint](/rest/api/media/streamingendpoints)主機名稱和**串流定位器**路徑。 此範例會使用預設的**串流端點**。 初次建立媒體服務帳戶時，此預設的**串流端點**會處於停止狀態，因此您需要呼叫 **Start**。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

當您執行應用程式時，您會看到下列畫面：

![使用 DRM 保護](./media/protect-with-drm/playready_encrypted_url.png)

您可以開啟瀏覽器並貼上產生的 URL，以啟動 Azure 媒體播放器示範頁面，其中已為您填入 URL 和權杖。

## <a name="clean-up-resources-in-your-media-services-account"></a>清除媒體服務帳戶中的資源

一般來說，您應該清除您打算重複使用之物件以外的所有專案（您通常會重複使用轉換、Streaminglocator 等等）。 如果您想要在實驗之後有乾淨的帳戶，請刪除您不打算重複使用的資源。 例如，下列程式碼會刪除作業、已建立的資產和內容金鑰原則：

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
