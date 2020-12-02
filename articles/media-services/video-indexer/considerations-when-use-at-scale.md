---
title: 在大規模使用影片索引子時應考慮的事項-Azure
titleSuffix: Azure Media Services
description: 本主題說明在大規模使用影片索引子時要考慮的事項。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: b955c0f494b757fd29c400194ef8b11314a89a03
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483605"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>使用大規模影片索引子時應考慮的事項

當您使用 Azure 媒體服務影片索引子來編制影片的索引，而您的影片封存成長時，請考慮進行調整。 

本文將回答類似下列的問題：

* 是否有任何需要考慮的技術條件約束？
* 是否有智慧型且有效率的方法？
* 我可以防止花費在處理過程中花費多少錢嗎？

本文提供六個如何大規模使用影片索引子的最佳作法。

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>上傳影片時，請考慮使用 URL over 位元組陣列

影片索引子可讓您選擇從 URL 上傳影片，或直接將檔案當作位元組陣列來傳送，後者則包含某些條件約束。 如需詳細資訊，請參閱 [上傳考慮和限制) ](upload-index-videos.md#uploading-considerations-and-limitations)

首先，它有檔案大小限制。 使用 URL 時，位元組陣列檔案的大小限制為 2 GB，相較于 30 GB 的上傳大小限制。

其次，只要考慮一些可能會影響您效能的問題，因此您可以調整規模：

* 使用多部分傳送檔案表示網路上的高相依性 
* 服務可靠性、 
* 連接 
* 上傳速度、 
* 遺失萬維網中某處的封包。

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="使用大規模影片索引子的第一次考慮":::

當您使用 URL 上傳影片時，您只需要提供媒體檔案位置的路徑，而影片索引子會處理其餘 (請參閱 `videoUrl` [上傳影片](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) API) 中的欄位。

> [!TIP]
> 使用 `videoUrl` 上傳影片 API 的選擇性參數。

若要查看如何使用 URL 上傳影片的範例，請參閱 [此範例](upload-index-videos.md#code-sample)。 或者，您可以使用 [AzCopy](../../storage/common/storage-use-azcopy-v10.md) 快速且可靠的方式，將內容提供給儲存體帳戶，您可以使用 [SAS URL](../../storage/common/storage-sas-overview.md)將內容提交至影片索引子。

## <a name="increase-media-reserved-units-if-needed"></a>視需要增加媒體保留單元

通常在概念證明階段中，當您只是開始使用影片索引子時，您不需要大量的運算能力。 當您開始擁有較大的影片封存時，您需要編制索引，而且您想要讓程式具有適合您使用案例的步調，您需要擴大影片索引子的使用方式。 因此，如果目前的運算能力量不足，您應該考慮增加您使用的計算資源數目。

在 Azure 媒體服務中，當您想要增加計算能力和平行處理時，必須注意) 的媒體 [保留單元](../latest/concept-media-reserved-units.md) (ru。 Ru 是決定媒體處理工作之參數的計算單位。 Ru 數目會影響每個帳戶中可同時處理的媒體工作數目，而其類型會判斷處理的速度，而如果索引很複雜，則會需要一個以上的 RU。 當您的 ru 忙碌時，新的工作會保留在佇列中，直到有其他資源可用為止。

影片索引子會提供自動調整系統，在需要較少的處理時將 ru 旋轉，並在您的尖峰 (時間內將 ru 旋轉，以充分利用您的所有 ru) ，以有效率地操作並避免資源閒置一段時間。 您可以藉由開啟帳戶設定中 [的自動](manage-account-connected-to-azure.md#autoscale-reserved-units) 調整，或使用 [更新付費帳戶-Azure-Media Services API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update)來啟用此功能。

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="使用大規模影片索引子的第二個考慮":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="AMS 保留單元":::

為了將索引持續時間和低輸送量降至最低，我們建議您一開始會有10個類型 S3 的 ru。 稍後，如果您擴大以支援更多內容或更高的平行存取，而且您需要更多資源才能這麼做，您可以使用付費帳戶上 [的支援系統](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (僅) 來要求更多 ru 配置。

## <a name="respect-throttling"></a>考慮節流

影片索引子的設計是為了處理大規模的索引編制，當您想要充分利用它時，您也應該留意系統的功能，並據以設計您的整合。 您不想要傳送一批影片的上傳要求，只是要找出某些電影未上傳，而且您收到 HTTP 429 回應碼 () 的要求太多。 這可能是因為您送出的要求數超過 [每分鐘支援的電影限制](upload-index-videos.md#uploading-considerations-and-limitations)。 影片索引子 `retry-after` 會在 HTTP 回應中新增標頭，標頭會指定何時應嘗試下一次重試。 在嘗試下一個要求之前，請確定您已遵守此要求。

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="設計完美的整合，尊重節流":::

## <a name="use-callback-url"></a>使用回呼 URL

我們建議您不要在您傳送上傳要求的第二個時間內，從第二個傳送上傳要求的第二個情況下，持續輪詢要求的狀態，您可以新增 [回呼 URL](upload-index-videos.md#callbackurl)，並等候影片索引子更新 一旦您的上傳要求中有任何狀態變更，您就會收到您所指定 URL 的 POST 通知。

您可以新增回呼 URL 作為 [上傳影片 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload)的其中一個參數。 查看 [GitHub](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/)存放庫中的程式碼範例。 

針對回呼 URL，您也可以使用 Azure Functions （可由 HTTP 觸發並執行下列流程的無伺服器事件驅動平臺）。

### <a name="callback-url-definition"></a>回呼 URL 定義

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>為您使用正確的索引參數

進行大規模使用影片索引子的決策時，請看看如何使用正確的參數來滿足您的需求。 藉由定義不同的參數來思考您的使用案例，您可以節省成本，讓影片的編制索引程式更快。

在上傳和編制影片索引之前，請閱讀這份簡短的 [檔](upload-index-videos.md)、查看 [indexingPreset](upload-index-videos.md#indexingpreset) 和 [streamingPreset](upload-index-videos.md#streamingpreset) ，以深入瞭解您的選項。

例如，如果您不打算觀看影片，請不要將預設值設定為串流，如果您只需要音訊見解，請勿編制影片深入解析索引。

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>最佳解析度的索引，不是最高的解析

您可能會問：為您的影片編制索引所需的影片品質為何？ 

在許多情況下，在 HD (720P) 影片和4K 影片之間，編制索引效能幾乎沒有任何差異。 最後，您會取得幾乎相同可信度的相同見解。 您上傳的電影品質越高，表示檔案大小愈高，而這會導致上傳影片所需的計算能力和時間更高。

例如，針對臉部偵測功能，較高的解析度有助於解決許多小型但內容重要臉部的案例。 不過，這會在執行時間增加二次，並增加誤報的風險。

因此，我們建議您確認您的使用案例會得到正確的結果，並先在本機進行測試。 在720P 和4K 中上傳相同的影片，並比較您取得的見解。

## <a name="next-steps"></a>後續步驟

[檢查 API 所產生的 Azure 影片索引子輸出](video-indexer-output-json-v2.md)