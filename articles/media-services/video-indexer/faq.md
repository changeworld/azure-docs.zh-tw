---
title: 關於影片索引器的常見問題集 - Azure
titleSuffix: Azure Media Services
description: 本文提供 Azure 媒體服務影片索引子的常見問題解答。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: dc57978dd881532cab59150dec921df9ffa958c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767208"
---
# <a name="video-indexer-frequently-asked-questions"></a>影片索引子的常見問題

本文將回答有關影片索引器的常見問題集。

## <a name="general-questions"></a>一般問題

### <a name="what-is-video-indexer"></a>什麼是影片索引子？

影片索引器是包含在 Microsoft Azure 媒體服務中的人工智慧服務。 影片索引器提供多個機器學習模型的協調流程，可讓您輕鬆從影片中擷取深入見解。 為了提供進階且準確的見解，影片索引器會運用影片的多個頻道：音訊、語音及視訊。 影片索引器的見解可以用於許多方面，例如改善內容的可測知性和可存取性、創造新的營收商機，或建置能使用這些見解的新體驗。 影片索引器提供 Web 型介面，以針對您帳戶中的模型進行測試、設定及自訂。 開發人員可以使用以 REST 為基礎的 API，來將影片索引器整合到生產環境系統中。 

### <a name="what-can-i-do-with-video-indexer"></a>影片索引器有何用途？

Video Indexer 可以對媒體檔案執行的一些作業包括：

* 識別並擷取語音，以及識別說話者。
* 識別並擷取影片中的螢幕上文字。
* 在影片檔案中偵測物件。
* 從音訊曲目和影片中的螢幕上文字識別品牌（例如： Microsoft）。
* 根據名人的資料庫和使用者定義的臉部資料庫偵測並辨識臉部。
* 擷取所討論的主題，但不一定要在音訊和視訊內容中明確提及。
* 從音訊曲目建立隱藏式輔助字幕或字幕。

如需詳細資訊和更多 Video Indexer 功能，請參閱[概觀](video-indexer-overview.md)。

### <a name="how-do-i-get-started-with-video-indexer"></a>如何開始使用影片索引器？

影片索引器包含免費的試用供應項目，可在 Web 型介面中提供 600 分鐘的試用時間，透過 API 則可取得 2,400 分鐘的試用時間。 您可以[登入影片索引器 Web 型介面](https://www.videoindexer.ai/) \(英文\)，並使用任何 Web 身分識別來試用它，而不需要設定 Azure 訂用帳戶。 遵循[這個簡單的簡介實驗室](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)，更瞭解如何使用影片索引子。

若要大規模編製視訊和音訊檔的索引，您可以將影片索引器連接至付費的 Microsoft Azure 訂用帳戶。 您可以在[定價](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)頁面上找到定價的詳細資訊。

您可以在[開始使用](video-indexer-get-started.md)中找到開始使用的詳細資訊。

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>需要編碼技能才能使用影片索引器嗎？

您可以在**無需撰寫程式碼**的情況下，使用影片索引器 Web 型介面來評估、設定及管理您的帳戶。  當您準備好開發更複雜的應用程式時，便可以使用[影片索引器 API](https://api-portal.videoindexer.ai/) \(英文\) 來將影片索引器整合到您的應用程式、網站，或是[使用無伺服器技術 (例如 Azure Logic Apps 或 Azure Functions) 的自訂工作流程](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) \(英文\) 之中。

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>需要機器學習技能才能使用影片索引器嗎？

否，影片索引器能提供將多個機器學習模型整合為單一管線的能力。 透過影片索引器對視訊或音訊檔案進行索引編製，能將完整的見解集合擷取到單一的共用時間軸上；客戶本身並不需要具備機器學習技能或演算法的相關知識。

### <a name="what-media-formats-does-video-indexer-support"></a>影片索引器支援哪些媒體格式？

影片索引器支援最常見的媒體格式。 如需詳細資料，請參閱 [Azure 媒體編碼器的標準格式](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats)清單。

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>如何? 將媒體檔案上傳到影片索引子中，有哪些限制？

在影片索引器 Web 入口網站中，您可以使用 [檔案上傳] 對話方塊來上傳媒體檔案，或透過指向直接裝載來源檔案的 URL 來上傳 (請參閱[範例](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4))。 所有使用 iFrame 或內嵌程式碼裝載媒體內容的 URL 都將無法運作 (請參閱[範例](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11) \(英文\))。 

如需詳細資訊，請參閱此[操作指南](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos)。

#### <a name="limitations"></a>限制

* 影片的名稱不能超過80個字元。
* 如果您使用位元組陣列上傳影片，則影片大小限制為2GB （使用 URL 時則為30GB）。 

如需完整清單，請參閱[上傳考慮與限制](upload-index-videos.md#uploading-considerations-and-limitations)。

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>影片索引器從媒體擷取見解需要多長的時間？

使用影片索引器 API 和影片索引器 Web 型介面，編製視訊或音訊檔案索引所需的時間量會取決於多個參數，例如檔案的長度與品質、在檔案中找到的見解數目、可用的[保留單元](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview)數目，以及是否已啟用[串流端點](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview)。 我們建議您以自己的內容來執行數個測試檔案，並採用平均值以進一步了解。

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>是否可以建立自訂工作流程以將影片索引器的程序自動化？

是，您可以將影片索引器整合到無伺服器技術之中，例如 Logic Apps、Flow，以及 [Azure Functions](https://azure.microsoft.com/services/functions/)。 您可以在[這裡](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) \(英文\) 找到適用於影片索引器的 [Logic App](https://azure.microsoft.com/services/logic-apps/) 和 [Flow](https://flow.microsoft.com/en-us/) 連接器的詳細資料。 您可以在[影片索引子範例](https://github.com/Azure-Samples/media-services-video-indexer)存放庫中看到由合作夥伴完成的一些自動化專案。

### <a name="in-which-azure-regions-is-video-indexer-available"></a>哪些 Azure 區域提供影片索引器？

您可以在[區域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)頁面上查看哪些 Azure 區域提供影片索引器。

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>我可以針對我的特定使用案例自訂影片索引子模型嗎？ 

是。 在影片索引子中，您可以自訂一些可用的模型，以更符合您的需求。 

例如，我們的人員模型支援名人辨識的現成1000000臉部，但您也可以將其定型，以辨識不在該資料庫中的其他臉部。 

如需詳細資訊，請參閱自訂[人員](customize-person-model-overview.md)、[品牌](customize-brands-model-overview.md)和[語言](customize-language-model-overview.md)模型的相關文章。 

###  <a name="can-i-edit-the-videos-in-my-library"></a>我可以編輯媒體櫃中的影片嗎？

是。 按下文件庫顯示的 [**編輯影片**] 按鈕，或從播放者顯示的 [**在編輯器中開啟**] 按鈕，進入 [**專案**] 索引標籤。您可以建立新的專案，並從媒體櫃新增更多影片來一起編輯，一旦完成後，您就可以轉譯您的影片並下載。 

如果您想要取得新影片的深入解析，請使用影片索引子為其編制索引，它會顯示在您的程式庫中與其深入解析。

### <a name="what-is-the-sla-for-video-indexer"></a>影片索引器的 SLA 為何？

Azure 媒體服務的 SLA 涵蓋影片索引器，並可以在 [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) 頁面上找到。 SLA 僅適用於影片索引器付費帳戶，且不適用於免費試用。

## <a name="privacy-questions"></a>隱私權問題

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>是否會儲存由影片索引器編製索引的音訊和視訊檔案？

是，除非您使用影片索引器網站或 API 從影片索引器刪除檔案，否則將會儲存您的視訊和音訊檔案。 針對免費試用，您編製索引的視訊和音訊檔案會儲存在美國東部的 Azure 區域中。 否則，您的視訊和音訊檔案都將儲存在您 Azure 訂用帳戶的儲存體帳戶中。

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>我可以刪除儲存在影片索引器入口網站中的檔案嗎？

是，您隨時都可以刪除您的視訊和音訊檔案，以及由影片索引器從它們所擷取的任何中繼資料和見解。 當您從影片索引器刪除檔案時，該檔案和其中繼資料與見解都會從影片索引器中永久移除。 不過，如果您已在 Azure 儲存體中實作屬於自己的備份解決方案，那些檔案將會保留在您的 Azure 儲存體中。

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>我是否可以控制自己影片索引器帳戶的使用者存取？

是，只有帳戶系統管理員可以邀請及取消邀請人員存取其帳戶，以及將編輯權限或唯讀存取權限指派給人員。

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>有誰可以存取我那些已經編製索引和/或由影片索引器儲存的視訊和音訊檔案，以及所擷取的中繼資料和見解？

針對您將隱私權設定設為公用的視訊或音訊檔案，所有擁有該視訊或音訊內容及其見解之連結的人員都可以存取它。 針對您將隱私權設定設為私人的視訊或音訊檔案，只有被邀請至視訊或音訊內容帳戶的使用者才可以存取它。 您內容的隱私權設定也會套用到影片索引器所擷取的中繼資料和見解上。 您會在上傳視訊或音訊檔案時指派隱私權設定。 您也可以在編製索引後變更隱私權設定。

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Microsoft 對於我那些已經編製索引和/或由影片索引器儲存的視訊和音訊檔案，以及所擷取的中繼資料和見解有哪些存取權？

根據 [Azure 線上服務條款](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) \(英文\) (OST)，您完全擁有自己的內容，且 Microsoft 僅會根據 OST 和 Microsoft 隱私權聲明存取您的內容，以及影片索引器從您內容中擷取的中繼資料和見解。

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>我在自己的影片索引子帳戶中建置的自訂模型是否可供其他帳戶使用？

 否，您在自己的帳戶中建立的自訂模型不可供任何其他帳戶使用。 影片索引子目前可讓您在自己的帳戶中建置自訂[品牌](customize-brands-model-overview.md)、[語言](customize-language-model-overview.md)和[人員](customize-person-model-overview.md)模型。 這些模型只能在您建立模型的帳戶中使用。
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>由影片索引器編製索引的內容是否保留在我要使用影片索引器的 Azure 區域內？

是，內容和其見解都會保留在 Azure 區域內，除非您在 Azure 訂用帳戶中具有使用多個 Azure 區域的手動設定。 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>影片索引子的隱私權原則為何？

影片索引器在 [Microsoft 隱私權聲明](https://privacy.microsoft.com/privacystatement)的涵蓋範圍內。 隱私權聲明會說明 Microsoft 處理的個人資料、Microsoft 處理該資料的方式，以及 Microsoft 處理該資料的目的。 若要深入了解隱私權，請參閱 [Microsoft 信任中心](https://www.microsoft.com/trustcenter)。

### <a name="what-certifications-does-video-indexer-have"></a>影片索引器有哪些認證？

影片索引器目前有 SOC 認證。 若要檢閱影片索引器的認證，請參閱 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure)。

### <a name="what-is-the-difference-between-private-and-public-videos"></a>私用和公用影片之間有何差異？ 

當影片上傳到影片索引子時，您可以從兩種隱私權設定中進行選擇： [私人] 和 [公用]。 所有人都可以存取公用影片，包括匿名和無法識別的使用者。 私人帳戶僅限於帳戶成員。 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>我嘗試將影片上傳為公用，並標示為不適當或冒犯的內容，這代表什麼意思？ 

將影片上傳到影片索引子時，會透過演算法和模型來進行自動內容分析，以確保不會公開任何不當的內容。 如果找到的影片可疑于包含明確內容，就無法將其設定為公用。 不過，帳戶成員仍然可以將其當做私人影片存取（觀看、下載深入解析和已解壓縮的成品，以及執行其他可供帳戶成員使用的作業）。   

為了設定公用存取的影片，您可以： 

* 建立您自己的介面層（例如應用程式或網站），並使用它來與影片索引子服務互動。 如此一來，影片會在入口網站中保持私用，而您的使用者可以透過您的介面與它互動。 例如，您仍然可以取得深入解析，或允許在自己的介面中觀看影片。 
* 要求內容的人工審核，這會導致移除限制，假設內容不是明確的。 

    如果您的使用者直接使用影片索引子網站做為介面層，以及用於公用（未經驗證）的查看，就可以探索此選項。 

## <a name="api-questions"></a>API 問題

### <a name="what-apis-does-video-indexer-offer"></a>影片索引器提供哪些 API？

影片索引器的 API 可供進行編製索引、擷取中繼資料、資產管理、轉譯、內嵌、自訂模型等。 如需使用影片索引器 API 的詳細資訊，請參閱[影片索引器開發人員入口網站](https://api-portal.videoindexer.ai/) \(英文\)。

### <a name="what-client-sdks-does-video-indexer-offer"></a>影片索引器提供哪些用戶端 SDK？

目前沒有提供任何用戶端 SDK。 影片索引器小組正在努力建置 SDK 功能，並預計在近日推出。

### <a name="how-do-i-get-started-with-video-indexers-api"></a>如何開始使用影片索引器的 API？

請遵循[教學課程：開始使用影片索引器 API](video-indexer-use-apis.md)。

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>影片索引器 API 和 Azure 媒體服務 v3 API 之間有何差異？

目前由影片索引器 API 和 Azure 媒體服務 v3 API 所提供的功能有部分重疊。 您可以在[這裡](compare-video-indexer-with-media-services-presets.md)找到如何比較這兩個服務的詳細資訊。

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>API 存取權杖是什麼？為什麼我需要它？

影片索引器 API 包含授權 API 和作業 API。 授權 API 包含讓您存取權杖的呼叫。 作業 API 的每次呼叫皆應與存取權杖相關聯，以符合呼叫的授權範圍。

基於安全性考量，需要有存取權杖才能使用影片索引器 API。 這可確保任何呼叫都來自您或具有您帳戶存取權限的人員。 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>帳戶存取權杖、使用者存取權杖，以及影片存取權杖之間的差異為何？

* 帳戶層級 – 帳戶層級的存取權杖可讓您在帳戶層級或影片層級上執行作業。 例如，上傳影片、列出所有影片、取得影片見解等。
* 使用者層級 - 使用者層級的存取權杖可讓您在使用者層級上執行作業。 例如，取得相關聯的帳戶。
* 影片層級 – 影片層級的存取權杖可讓您在特定影片上執行作業。 例如，取得影片深入解析、下載字幕、取得介面控件等等。

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>我必須多久取得一次新的存取權杖？ 存取權杖何時到期？

存取權杖每隔一小時到期，因此您需要每隔一小時產生一個新的存取權杖。 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>影片索引子開發人員入口網站的登入選項有哪些？

您可以使用 Azure AD、Microsoft 帳戶、Google 帳戶或 Facebook 帳戶來登入。 

當您使用身分識別提供者註冊您的電子郵件帳戶之後，就無法將此電子郵件帳戶與其他身分識別提供者搭配使用。

## <a name="billing-questions"></a>計費問題

### <a name="how-much-does-video-indexer-cost"></a>影片索引器的費用是多少？

影片索引器會使用簡單的隨用隨付定價模型，其以您編製索引之內容輸入的持續時間為基礎。 對於編碼、資料流、儲存體、網路使用量和媒體保留單元，可能須支付額外的費用。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)頁面。

### <a name="when-am-i-billed-for-using-video-indexer"></a>使用影片索引子的計費時間為何？

傳送要編制索引的影片時，使用者會將索引定義為影片分析、音訊分析或兩者。 這會決定要向哪個 Sku 收費。 如果在處理期間發生嚴重層級錯誤，將會傳回錯誤碼作為回應。 在這種情況下，不會產生任何費用。  嚴重錯誤可能是因為我們的程式碼中的 bug，或服務所擁有的內部相依性嚴重失敗所造成。 錯誤的識別或深入解析解壓縮不會視為關鍵，而且會傳迴響應。 在傳回有效（非錯誤碼）回應的任何情況下，都會進行計費。
 
### <a name="does-video-indexer-offer-a-free-trial"></a>影片索引器是否提供免費試用版？

是，影片索引器提供免費試用，其能提供完整的服務和 API 功能。 Web 型介面使用者可以享有 600 分鐘的視訊配額，而 API 使用者則能享有 2,400 分鐘的配額。 

## <a name="next-steps"></a>後續步驟

[概觀](video-indexer-overview.md)
