---
title: Azure 媒體服務的封裝和來源錯誤 |Microsoft Docs
description: 本主題說明您可能會從 Azure 媒體服務串流端點（Orgin）服務收到的錯誤。
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
ms.openlocfilehash: 7d3a85e6fcc5b9d1c5ca1511cd7edd05ff5d9ae4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582691"
---
# <a name="streaming-endpoint-origin-errors"></a>串流端點（原點）錯誤 

本主題說明您可能會從 Azure 媒體服務[串流端點服務](streaming-endpoint-concept.md)收到的錯誤。

## <a name="400-bad-request"></a>400 不正確的要求

此要求包含不正確資訊，而且會因為下列其中一個原因而遭到拒絕：

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL 語法或格式錯誤。 範例包括無效類型的要求、不正確片段或不正確追蹤。 |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|此要求在 URL 中沒有加密標記。 CMAF 要求需要 URL 中的加密標記。 其他使用一種以上的加密類型設定的通訊協定，也需要加密標記來進行混淆。 |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|要求儲存體以滿足要求失敗，發生錯誤的要求錯誤。 |

## <a name="403-forbidden"></a>403 禁止

基於下列原因之一不允許此要求：

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|要求儲存體以滿足要求失敗，發生驗證失敗。 如果儲存體金鑰已輪替，且服務無法同步儲存體金鑰，就會發生這種情況。 <br/><br/>請前往 Azure 入口網站中的 [說明[+ 支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)] 來聯絡 Azure 支援。|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |儲存體操作錯誤，存取權因帳戶許可權不足而失敗。 |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |因為儲存體帳戶已停用，導致要求完成儲存體的要求失敗。 |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |儲存體操作錯誤，存取因一般錯誤而失敗。 |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |輸出格式因 StreamingPolicy 中的設定而遭到封鎖。 |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |內容需要加密，輸出格式需要傳遞原則。 |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |未在傳遞原則設定中設定加密。 |

## <a name="404-not-found"></a>404 找不到

作業嘗試在已經不存在的資源上執行。 例如，資源可能已經被刪除。

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |找不到要求的追蹤。 |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |找不到要求的資源。 |
|MPE_UNAUTHORIZED |0x80890244 |存取未經授權。 |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |找不到要求的時間戳記。 |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |找不到要求的動態資訊清單篩選。 |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |要求的片段索引超出有效的範圍。 |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |找不到要取得 moov 緩衝區的即時媒體專案。 |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |在要求的時間找不到特定追蹤的片段。<br/><br/>這可能是因為片段不在儲存體中。 嘗試另一層可能有片段的簡報。 |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |在資訊清單中找不到所要求位元速率的媒體專案。 <br/><br/>可能是播放程式要求提供不在資訊清單中之特定位元速率的影片追蹤。|
|MPE_METADATA_NOT_FOUND |0x80890257 |在資訊清單中找不到特定的中繼資料，或找不到存放裝置的重訂基底。 |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |儲存體操作錯誤，找不到資源。 |

## <a name="409-conflict"></a>409 衝突

在`PUT`或`POST`作業上為資源提供的識別碼已由現有的資源所使用。 請為資源使用另一個識別碼來解決此問題。

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |儲存體操作錯誤，衝突錯誤。  |

## <a name="410"></a>410

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|針對即時串流，當 forceEndTimestamp 設定為 true 的篩選準則時，開始或結束時間戳記會在目前的 DVR 視窗外。|

## <a name="412-precondition-failure"></a>412 先決條件失敗

作業指定的 eTag 與伺服器上可用的版本不同，也就是開放式平行存取錯誤。 讀取最新版的資源及更新要求上的 eTag 之後，重試要求。

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |要求的片段尚未就緒。|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|儲存體作業錯誤，前置條件失敗。|

## <a name="415-unsupported-media-type"></a>415不支援的媒體類型

用戶端所傳送的裝載格式為不支援的格式。

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| 不應對已經加密的內容套用加密。|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |加密對輸入格式無效。|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| 傳遞原則類型無效。|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |原始設定可由多個輸出格式共用。|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|不支援媒體格式或類型。 例如，媒體服務不支援超過64的品質等級計數。 在 FLV video 標記中，媒體服務不支援具有多個 SPS 和多個 PPS 的影片畫面。|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| 不支援所要求之資產的輸入格式。 媒體服務支援平滑（即時）、（VoD）和漸進式下載格式。|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|不支援要求的輸出格式。 媒體服務支援平滑、虛線（CSF、CMAF）、HLS （v3、v4、CMAF）和漸進式下載格式。|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|發現不支援的加密類型。|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|輸出格式不支援要求的媒體類型。 支援的類型為 video、audio 或 "SUBT" 子標題。|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|來源資產媒體使用與輸出格式不相容的媒體格式進行編碼。|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|來源資產使用與輸出格式不相容的影片格式進行編碼。 支援 h.264、AVC、H. 265 （HEVC、hev1 或 hvc1）。|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|來源資產使用與輸出格式不相容的音訊格式進行編碼。 支援的音訊格式為 AAC、E AC3 （DD +）、杜比 DTS。|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|來源受保護的資產無法轉換成輸出格式。|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|輸出格式不支援保護格式。|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|輸入格式不支援保護格式。|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|不正確影片 NAL 單位，例如，只有範例中的第一個 NAL 可以是 AUD。|
|MPE_INVALID_NALU_SIZE|0x80890260|不正確 NAL 單位大小。|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|NAL 單位長度值無效。|
|MPE_FILTER_INVALID|0x80890236|不正確動態資訊清單篩選。|
|MPE_FILTER_VERSION_INVALID|0x80890237|無效或不支援的篩選版本。|
|MPE_FILTER_TYPE_INVALID|0x80890238|不正確篩選類型。|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|篩選準則指定了不正確範圍。|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|篩選準則指定了不正確 track 屬性。|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|篩選器指定了不正確呈現視窗長度。|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|篩選準則指定了不正確即時備份。|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|舊版篩選器僅支援一個 absTimeInHNS 元素。|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|套用篩選之後，完全沒有其他資料流程。|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|即時備份已超出 DVR 視窗。|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|即時後關閉大於展示視窗。|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|超過10（10）個允許的預設篩選器上限。|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|合併的要求篩選器中不允許有多個第一個視頻品質運算子。|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|第一個品質位元速率屬性的數目必須是一（1）。|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS 區段持續時間必須小於 DVR 視窗的三分之一，然後再 HLS 一次。|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|片段持續時間必須小於或等於大約20秒，否則輸入品質層級不會對齊。|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS 特定的錯誤，找不到 ReservedBox，但在 DTS 方塊剖析期間應該會出現在 DTSSpecficBox 中。|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS 特定的錯誤，在 DTS 方塊剖析期間，在 DTSSpecficBox 中找不到任何通道。|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS 特定的錯誤，DTSSpecficBox 中的範例類型不符。|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS 特定的錯誤，已設定多資產，但 DTSH 範例類型不符。|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS 特定的錯誤，核心資料流程大小無效。|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS 特定的錯誤，範例解析無效。|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS 特定的錯誤，子資料流程延伸模組索引無效。|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS 特定的錯誤，子資料流程區塊號碼無效。|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS 特定的錯誤，取樣頻率無效。|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS 特定的錯誤，子資料流程延伸中的參考時鐘程式碼無效。|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS 特定的錯誤，說話者重新對應集的數目無效。|

如需加密文章和範例，請參閱：

- [概念：內容保護](content-protection-overview.md)
- [概念：內容金鑰原則](content-key-policy-concept.md)
- [概念：串流原則](streaming-policy-concept.md)
- [範例：使用 AES 加密保護](protect-with-aes128.md)
- [範例：使用 DRM 保護](protect-with-drm.md)

如需篩選指導方針，請參閱：

- [概念：動態資訊清單](filters-dynamic-manifest-overview.md)
- [概念：篩選](filters-concept.md)
- [範例：使用 REST Api 建立篩選器](filters-dynamic-manifest-rest-howto.md)
- [範例：使用 .NET 建立篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [範例：使用 CLI 建立篩選器](filters-dynamic-manifest-cli-howto.md)

如需即時文章和範例，請參閱：

- [概念：即時串流總覽](live-streaming-overview.md)
- [概念：即時事件和即時輸出](live-events-outputs-concept.md)
- [範例：即時串流教學課程](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416範圍未達成

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|儲存體操作錯誤，傳回 HTTP 416 錯誤，不正確範圍。|

## <a name="500-internal-server-error"></a>500 內部伺服器錯誤

在處理要求時，媒體服務遇到錯誤，而導致無法繼續處理。  

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|從 ERROR_WINHTTP_TIMEOUT 的 WinHTTP 錯誤碼（0x00002ee2）接收和轉譯。|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|從 ERROR_WINHTTP_CONNECTION_ERROR 的 WinHTTP 錯誤碼（0x00002efe）接收和轉譯。|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|從 ERROR_WINHTTP_NAME_NOT_RESOLVED 的 WinHTTP 錯誤碼（0x00002ee7）接收和轉譯。|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|儲存體作業錯誤，其中一個 HTTP 500 錯誤的一般 InternalError。|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|儲存體作業錯誤，其中一個 HTTP 500 錯誤的一般 OperationTimedOut。|
|MPE_STORAGE_FAILURE|0x808900F2|儲存體作業錯誤，其他 HTTP 500 錯誤，超過 InternalError 或 OperationTimedOut。|

## <a name="503-service-unavailable"></a>503 服務無法使用

伺服器目前無法接收要求。 此錯誤可能是由於對服務的大量要求所造成。 媒體服務節流機制會針對向服務發出過多要求的應用程式限制資源使用量。

> [!NOTE]
> 請檢查錯誤訊息和錯誤代碼字串，以取得您得到 503 錯誤原因的詳細資訊。 此錯誤不一定表示節流。
> 

|錯誤碼|十六進位值 |錯誤描述|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|儲存體操作錯誤，收到 HTTP 伺服器忙碌錯誤503。|

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>請參閱

- [編碼錯誤碼](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Azure 媒體服務概念](concepts-overview.md)
- [配額和限制](limits-quotas-constraints.md)

## <a name="next-steps"></a>後續步驟

[範例：使用 .NET 從 ApiException 存取 ErrorCode 和 Message](configure-connect-dotnet-howto.md#connect-to-the-net-client)
