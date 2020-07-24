---
title: 使用 AES 加密影片-128
titleSuffix: Azure Media Services
description: 瞭解如何使用 AES 128 位加密來加密影片，以及如何在 Azure 媒體服務中使用金鑰傳遞服務。
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
ms.date: 07/09/2020
ms.author: juliako
ms.openlocfilehash: 45bb8637d37c9c3789a962c9f5ac42227d547637
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022815"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>教學課程：使用 AES-128 加密影片並使用金鑰傳遞服務

> [!NOTE]
> 雖然教學課程使用 [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 範例，但是 [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event?view=azure-cli-latest) 或其他受支援 [SDK](media-services-apis-overview.md#sdks) 的一般步驟都相同。

您可以利用 128 位元加密金鑰，使用媒體服務提供 HTTP 即時串流 (HLS)、MPEG-DASH，和透過 AES 加密的 Smooth Streaming。 媒體服務也提供加密金鑰傳遞服務，將加密金鑰傳遞至授權的使用者。 如果您想要媒體服務來動態加密您的影片，您可以將加密金鑰與串流定位器建立關聯，同時設定內容金鑰原則。 當播放程式要求串流時，媒體服務會使用指定的金鑰，以 AES-128 動態加密您的內容。 為了將串流解密，播放程式將向金鑰傳遞服務要求金鑰。 為了判斷使用者是否有權取得金鑰，服務會評估您為金鑰指定的內容金鑰原則。

您可以使用多種加密類型 (AES-128、PlayReady、Widevine、FairPlay) 來加密每項資產。 請參閱[串流通訊協定和加密類型](content-protection-overview.md#streaming-protocols-and-encryption-types)，以查看哪些組合可行。 另請參閱[如何使用 DRM 保護](protect-with-drm.md)。

這篇文章的輸出包含 Azure 媒體播放機的 URL、資訊清單 URL，以及播放內容所需的 AES 權杖。 此範例會將 JSON Web 權杖（JWT）權杖的到期時間設定為1小時。 您可以開啟瀏覽器並貼上產生的 URL，以啟動 Azure 媒體播放器示範頁面，其中已為您填入 URL 和權杖，格式如下：```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```。

本教學課程說明如何：

> [!div class="checklist"]
> * 下載本文中所述的[EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES)範例。
> * 開始搭配使用媒體服務 Api 與 .NET SDK。
> * 建立輸出資產。
> * 建立編碼轉換。
> * 提交工作。
> * 等候作業完成。
> * 建立內容金鑰原則
> * 設定原則以使用 JWT 權杖限制。
> * 建立串流定位器。
> * 設定串流定位器以 AES （ClearKey）加密影片。
> * 取得測試 token。
> * 建立串流 URL。
> * 清除資源。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

需要有下列項目，才能完成教學課程。

* 請檢閱[內容保護概觀](content-protection-overview.md)文章。
* 安裝 Visual Studio Code 或 Visual Studio。
* [建立媒體服務帳戶](./create-account-howto.md)。
* 藉由遵循[存取 api](./access-api-howto.md)，取得使用媒體服務 api 所需的認證。

## <a name="download-code"></a>下載程式碼

使用以下命令將包含完整 .NET 範例 (在本文中討論) 的 GitHub 存放庫複製到您的電腦：

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

「使用 AES-128 加密」範例位於 [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) 資料夾中。

> [!NOTE]
> 此範例會在您每次執行應用程式時建立唯一的資源。 一般而言，您會重複使用現有的資源，例如轉換和原則（如果現有資源具有必要的設定）。

## <a name="start-using-media-services-apis-with-net-sdk"></a>開始搭配使用媒體服務 API 與 .NET SDK

若要開始搭配使用媒體服務 Api 與 .NET，請建立**AzureMediaServicesClient**物件。 若要建立物件，您需要提供必要的認證，讓用戶端使用 Azure AD 連線至 Azure。 在您於本文一開始複製的程式碼中，**GetCredentialsAsync** 函式會根據本機設定檔中提供的認證建立 ServiceClientCredentials 物件。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>建立輸出資產  

輸出[資產](/rest/api/media/assets)會儲存您的編碼作業結果。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>取得或建立編碼轉換

建立新的[轉換](/rest/api/media/transforms)執行個體時，您需要指定想要其產生的輸出是什麼。 必要的參數是 **TransformOutput** 物件，如下列程式碼所示。 每個 **TransformOutput** 都會包含 **Preset (預設)** 。 **Preset** 會描述影片和/或音訊處理作業的逐步指示，以產生所需的 **TransformOutput**。 本文中所述的範例會使用稱為 **AdaptiveStreaming** 的內建 Preset。 預設會根據輸入解析度和位元速率，將輸入影片編碼成自動產生的位元速率階梯（位元速率解析組），然後使用 h.264 video 和 AAC 音訊（對應于每個位元速率解析組）來產生 ISO 的已執行檔。

在建立新的[轉換](/rest/api/media/transforms)之前，請先使用**Get**方法檢查其中一個是否已存在，如下列程式碼所示。 在媒體服務 v3 中，如果實體不存在，對實體執行的 **Get** 方法會傳回 **null** (檢查名稱時不區分大小寫)。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>提交作業

如同前面所述，[轉換](/rest/api/media/transforms)物件是配方，而[作業](/rest/api/media/jobs)則是實際要求媒體服務，將**轉換**套用至指定的輸入影片或音訊內容。 **作業**會指定輸入影片的位置、輸出的位置等資訊。

在本教學課程中，我們會根據直接從[HTTPs 來源 URL](job-input-from-http-how-to.md)內嵌的檔案，建立作業的輸入。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>請等待作業完成

此作業需要一些時間才能完成。 作業完成時，您會想要收到通知。 下列程式碼範例說明如何輪詢服務以取得[作業](/rest/api/media/jobs)狀態。 對生產應用程式而言，輪詢不是建議的最佳做法，因為可能會發生延遲。 如果過度使用帳戶，輪詢可能會進行節流處理。 開發人員應改為使用事件方格。 如需詳細資訊，請參閱[將事件路由至自訂 Web 端點](job-state-events-cli-how-to.md)。

**作業**通常會經歷下列狀態：**已排程**、**已排入佇列**、**正在處理**、**已完成** (最後一個狀態)。 如果作業發生錯誤，您會收到**錯誤**狀態。 如果正在取消作業，您會收到**正在取消**的狀態，以及完成時的**已取消**狀態。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>建立內容金鑰原則

內容金鑰可提供資產的安全存取。 您必須建立**內容金鑰原則**，以設定將內容金鑰傳遞給終端用戶端的方式。 內容金鑰與**串流定位器**相關聯。 媒體服務也提供加密金鑰傳遞服務，將加密金鑰傳遞至授權的使用者。

當播放程式要求串流時，媒體服務會使用指定的金鑰動態加密您的內容（在此案例中，是使用 AES 加密）。為了解密資料流程，player 會向金鑰傳遞服務要求金鑰。 為了判斷使用者是否有權取得金鑰，服務會評估您為金鑰指定的內容金鑰原則。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>建立串流定位器

編碼完成且設定好內容金鑰原則後，下一個步驟是要讓用戶端可播放輸出資產中的視訊。 您可以使用兩個步驟來提供影片：

1. 建立[串流定位器](/rest/api/media/streaminglocators)。
2. 建置用戶端可使用的串流 URL。

建立**串流定位器**的程式稱為「發行」。 根據預設，**串流定位器**會在您進行 API 呼叫之後立即生效。 除非您設定選擇性的開始和結束時間，否則它會持續到刪除為止。

建立[串流定位器](/rest/api/media/streaminglocators)時，您必須指定所需的**StreamingPolicyName**。 在本教學課程中，我們會使用其中一個 PredefinedStreamingPolicies，告訴 Azure 媒體服務如何發佈內容以進行串流處理。 在此範例中，會套用 AES 信封加密（此加密也稱為 ClearKey 加密，因為金鑰會透過 HTTPS 傳遞至播放用戶端，而不是 DRM 授權）。

> [!IMPORTANT]
> 使用自訂的 [StreamingPolicy](/rest/api/media/streamingpolicies) 時，您應該為媒體服務帳戶設計一組受限的這類原則，並且在需要相同的加密選項和通訊協定時，對 StreamingLocators 重新使用這些原則。 媒體服務帳戶有 StreamingPolicy 項目的數量配額。 您不應該為每個串流定位器建立新的 StreamingPolicy。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>取得測試權杖

在本教學課程中，我們會指定內容金鑰原則具有權杖限制。 權杖限制原則必須伴隨 Security Token Service (STS) 所發出的權杖。 媒體服務支援[JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3)格式的權杖，這就是我們在範例中設定的內容。

ContentKeyIdentifierClaim 用於**內容金鑰原則**中，這表示向金鑰傳遞服務出示的權杖必須具有內容金鑰的識別碼。 在範例中，我們不會在建立串流定位器時指定內容金鑰，系統會為我們建立一個隨機的索引鍵。 若要產生測試權杖，我們必須取得要放入 ContentKeyIdentifierClaim 宣告中的 ContentKeyId。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>建置 DASH 串流 URL

現在已建立[串流定位器](/rest/api/media/streaminglocators)，您可以取得串流 url。 若要建立 URL，您需要串連[StreamingEndpoint](/rest/api/media/streamingendpoints)主機名稱和**串流定位器**路徑。 此範例會使用預設的**串流端點**。 初次建立媒體服務帳戶時，此預設的**串流端點**會處於停止狀態，因此您需要呼叫 **Start**。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>清除媒體服務帳戶中的資源

一般來說，您應該清除您打算重複使用之物件以外的所有專案（通常您會重複使用轉換、串流定位器等等）。 如果您想要在實驗之後有乾淨的帳戶，請刪除您不打算重複使用的資源。 例如，下列程式碼會刪除作業、已建立的資產和內容金鑰原則：

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

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [使用 DRM 保護](protect-with-drm.md)
