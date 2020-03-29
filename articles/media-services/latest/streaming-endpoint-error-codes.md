---
title: Azure 媒體服務打包和原點錯誤 |微軟文檔
description: 本主題介紹可能從 Azure 媒體服務流終結點 （Orgin） 服務收到的錯誤。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: ebcda6026f79bc88df91471d8be88316ba57bfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "65411380"
---
# <a name="streaming-endpoint-origin-errors"></a>流式處理終結點（原點）錯誤 

本主題介紹可能從 Azure 媒體服務[流終結點服務](streaming-endpoint-concept.md)收到的錯誤。

## <a name="400-bad-request"></a>400 不正確的要求

請求包含無效資訊，並且由於以下原因之一而拒絕這些錯誤代碼：

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL 語法或格式錯誤。 示例包括無效類型、無效片段或無效軌道的請求。 |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|請求在 URL 中沒有加密標記。 CMAF 請求需要在 URL 中使用加密標記。 配置了多個加密類型的其他協定也需要加密標記才能消除歧義。 |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|存儲請求以完成請求失敗，但請求錯誤。 |

## <a name="403-forbidden"></a>403 禁止

基於下列原因之一不允許此要求：

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|存儲請求以完成請求失敗，但身份驗證失敗。 如果存儲金鑰已旋轉且服務無法同步存儲金鑰，則可能發生此情況。 <br/><br/>通過在 Azure 門戶中訪問[説明 + 支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，請與 Azure 支援聯繫。|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |存儲操作錯誤，由於帳戶許可權不足，訪問失敗。 |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |存儲請求以完成請求失敗，因為存儲帳戶已禁用。 |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |存儲操作錯誤，訪問失敗，由於泛型錯誤。 |
|MPE_OUTPUT_FORMAT_BLOCKED |0 x80890207 |由於流式處理策略中的配置，輸出格式被阻止。 |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |內容需要加密，輸出格式需要傳遞策略。 |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |加密未在傳遞策略設置中設置。 |

## <a name="404-not-found"></a>404 找不到

作業嘗試在已經不存在的資源上執行。 例如，資源可能已經被刪除。

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0 x80890209 |找不到請求的軌道。 |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |找不到請求的資源。 |
|MPE_UNAUTHORIZED |0x80890244 |訪問是未經授權的。 |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |找不到請求的時間戳記。 |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |找不到請求的動態清單篩選器。 |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |請求的片段索引超出有效範圍。 |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |找不到即時媒體項目來獲取 moov 緩衝區。 |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |無法在請求的特定軌道的時間找到片段。<br/><br/>可能是碎片不在存儲中。 嘗試可能具有片段的演示文稿的不同層。 |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |無法在清單中找到請求的位元速率的媒體項目。 <br/><br/>可能是玩家要求某個位元速率的視頻軌道不在清單中。|
|MPE_METADATA_NOT_FOUND |0 x80890257 |無法在清單中找到某些中繼資料，或無法從存儲中找到重資料庫。 |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |存儲操作錯誤，找不到資源。 |

## <a name="409-conflict"></a>409 衝突

為`PUT`或`POST`操作上的資源提供的 ID 已被現有資源獲取。 使用資源的另一個 ID 解決此問題。

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900E  |存儲操作錯誤，衝突錯誤。  |

## <a name="410"></a>410

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|對於即時流式處理，當將強制 EndTimestamp 設置為 true 的篩選器時，開始時間戳或結束時間戳記在當前 DVR 視窗之外。|

## <a name="412-precondition-failure"></a>412 先決條件失敗

該操作指定了與伺服器中可用的版本不同的 eTag，即一個樂觀的併發錯誤。 讀取最新版的資源及更新要求上的 eTag 之後，重試要求。

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0 x80890200 |請求的片段未就緒。|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|存儲操作錯誤，先決條件失敗。|

## <a name="415-unsupported-media-type"></a>415 不支援的媒體類型

用戶端發送的有效負載格式不受支援。

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| 不應對已加密的內容應用加密。|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |加密對於輸入格式無效。|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| 傳遞策略類型無效。|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |原始設置可以由多種輸出格式共用。|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|不支援媒體格式或類型。 例如，媒體服務不支援超過 64 的品質級別計數。 在 FLV 視頻標籤中，媒體服務不支援具有多個 SPS 和多個 PPS 的視頻幀。|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0 x80890218| 不支援請求的資產的輸入格式。 媒體服務支援平滑（即時）、MP4 （VoD） 和漸進式下載格式。|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|不支援請求的輸出格式。 媒體服務支援平滑、DASH（CSF、CMAF）、HLS（v3、v4、CMAF）和漸進式下載格式。|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|遇到不支援的加密類型。|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|輸出格式不支援請求的媒體類型。 支援的類型是視頻、音訊或"SUBT"字幕。|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|源資產介質的編碼採用與輸出格式不相容的媒體格式。|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0 x80890210|源資產的編碼與輸出格式不相容的視頻格式。 支援 H.264、AVC、H.265（HEVC、hev1 或 hvc1）。|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0 x80890211|源資產的編碼具有與輸出格式不相容的音訊格式。 支援的音訊格式有 AAC、E-AC3 （DD+）、杜比 DTS。|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|源保護的資產無法轉換為輸出格式。|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|輸出格式不支援保護格式。|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0 x80890219|輸入格式不支援保護格式。|
|MPE_INVALID_VIDEO_NAL_UNIT|0 x80890231|無效視頻 NAL 單元，例如，只有樣本中的第一個 NAL 可以是 AUD。|
|MPE_INVALID_NALU_SIZE|0 x80890260|不正確 NAL 單位大小。|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|不正確 NAL 單位長度值。|
|MPE_FILTER_INVALID|0x80890236|不正確動態清單篩選器。|
|MPE_FILTER_VERSION_INVALID|0 x80890237|無效或不支援的篩選器版本。|
|MPE_FILTER_TYPE_INVALID|0 x80890238|不正確篩選器類型。|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0 x80890239|無效範圍由篩選器指定。|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|無效軌道屬性由篩選器指定。|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|不正確表示視窗長度由篩選器指定。|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|不正確即時返回由篩選器指定。|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|舊篩選器中僅支援一個 absTimeInHNS 元素。|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|應用篩選器後，根本沒有更多的流。|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|即時返回超出 DVR 視窗。|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|即時返回比演示視窗更大。|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|超過 10 （10） 個允許的最大預設篩選器。|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|組合要求篩選器中不允許多個第一視頻品質運算子。|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0 x80890249|第一品質位元速率屬性的數量必須為 1 （1）。|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS 段持續時間必須小於 DVR 視窗的三分之一，而 HLS 必須關閉。|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|片段持續時間必須小於或等於大約 20 秒，或者輸入品質級別不隨時間對齊。|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|特定于 DTS 的錯誤，在 DTS 框解析期間應出現在 DTSSpecficBox 中時，找不到保留框。|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS 特定錯誤，在 DTS 框解析期間，DTSSpecficBox 中找不到通道。|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS 特異性錯誤，DTSSpecficBox 中的樣本類型不匹配。|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|設置特定于 DTS 的錯誤，設置多資產，但 DTSH 樣本類型不匹配。|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|特定于 DTS 的錯誤，核心流大小無效。|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|特定于 DTS 的錯誤，示例解析度無效。|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|特定于 DTS 的錯誤，子流擴展索引無效。|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|特定于 DTS 的錯誤，子流塊號無效。|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|特定于 DTS 的錯誤，採樣頻率無效。|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|特定于 DTS 的錯誤，子流擴展中的參考時鐘代碼無效。|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|特定于 DTS 的錯誤，重新映射設置的揚聲器數量無效。|

有關加密文章和示例，請參閱：

- [概念：內容保護](content-protection-overview.md)
- [概念：內容關鍵策略](content-key-policy-concept.md)
- [概念：流式處理策略](streaming-policy-concept.md)
- [示例：使用 AES 加密進行保護](protect-with-aes128.md)
- [示例：使用 DRM 進行保護](protect-with-drm.md)

有關篩選器指南，請參閱：

- [概念：動態清單](filters-dynamic-manifest-overview.md)
- [概念：篩檢程式](filters-concept.md)
- [示例：使用 REST API 創建篩選器](filters-dynamic-manifest-rest-howto.md)
- [示例：使用 .NET 創建篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [示例：使用 CLI 創建篩選器](filters-dynamic-manifest-cli-howto.md)

有關即時文章和示例，請參閱：

- [概念：即時流式處理概述](live-streaming-overview.md)
- [概念：即時活動和即時輸出](live-events-outputs-concept.md)
- [示例：即時流式處理教程](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 範圍不適宜

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|存儲操作錯誤，返回 HTTP 416 錯誤，範圍無效。|

## <a name="500-internal-server-error"></a>500 內部伺服器錯誤

在處理要求時，媒體服務遇到錯誤，而導致無法繼續處理。  

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|從 winHTTP 錯誤代碼 ERROR_WINHTTP_TIMEOUT （0x00002eee2） 接收和翻譯。|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|從 winHTTP 錯誤代碼 ERROR_WINHTTP_CONNECTION_ERROR （0x00002efe） 接收和翻譯。|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|從 winHTTP 錯誤代碼 ERROR_WINHTTP_NAME_NOT_RESOLVED （0x00002ee7） 接收和翻譯。|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|存儲操作錯誤，一般內部錯誤之一的HTTP 500錯誤。|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|存儲操作錯誤，常規操作超時 HTTP 500 錯誤之一。|
|MPE_STORAGE_FAILURE|0x808900F2|存儲操作錯誤，其他 HTTP 500 錯誤，而不是內部錯誤或操作超時。|

## <a name="503-service-unavailable"></a>503 服務無法使用

伺服器目前無法接收要求。 此錯誤可能是由於對服務的大量要求所造成。 媒體服務節流機制會針對向服務發出過多要求的應用程式限制資源使用量。

> [!NOTE]
> 請檢查錯誤訊息和錯誤代碼字串，以取得您得到 503 錯誤原因的詳細資訊。 此錯誤不一定表示節流。
> 

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|存儲操作錯誤，收到 HTTP 伺服器忙錯誤 503。|

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>另請參閱

- [編碼錯誤碼](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Azure 媒體服務概念](concepts-overview.md)
- [配額和限制](limits-quotas-constraints.md)

## <a name="next-steps"></a>後續步驟

[示例：使用 .NET 訪問錯誤代碼和來自 ApiException 的消息](configure-connect-dotnet-howto.md#connect-to-the-net-client)
