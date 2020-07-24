---
title: Azure 媒體播放機功能清單
description: Azure 媒體播放機的功能參考。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 41e090b9e1d4c091bd3972afd296c5751e6b8c58
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082710"
---
# <a name="feature-list"></a>功能清單 #
以下是已測試的功能和不支援的功能清單：

| 功能 | 方 | 部分測試 | 經過 | 不支援 | 注意 |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| 播放                                |        |                  |          |             |                                                                                                                      |
| 基本隨選播放                | X      |                  |          |             | 僅支援來自 Azure 媒體服務的資料流程                                                                      |
| 基本即時播放                     | X      |                  |          |             | 僅支援來自 Azure 媒體服務的資料流程                                                                      |
| AES                                     | X      |                  |          |             | 支援 Azure 媒體服務金鑰傳遞服務                                                                   |
| 多重 DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | 支援 Azure 媒體服務金鑰傳遞服務                                                                   |
| Widevine                                |        | X                |          |             | 支援資訊清單中所述的 Widevine PSSH 方塊                                                                    |
| FairPlay                                |        | X                |          |             | 支援 Azure 媒體服務金鑰傳遞服務                                                                   |
| Techs                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME （AzureHtml5JS）                  | X      |                  |          |             |                                                                                                                      |
| Flash Fallback （FlashSS）                | X      |                  |          |             | 並非所有功能都可在此技術上取得。                                                                         |
| Silverlight Fallback SilverlightSS      | X      |                  |          |             | 並非所有功能都可在此技術上取得。                                                                         |
| 原生 HLS 傳遞（Html5）         |        | X                |          |             | 由於平臺限制，這種技術並非所有功能都可供使用。                                            |
| 功能                                |        |                  |          |             |                                                                                                                      |
| API 支援                             | X      |                  |          |             | 查看已知問題清單                                                                                                |
| 基本 UI                                | X      |                  |          |                                                                                                                                    |
| 透過 JavaScript 初始化       | X      |                  |          |             |                                                                                                                      |
| 透過影片標記初始化        |        | X                |          |             |                                                                                                                      |
| 區段定址-以時間為基礎         | X      |                  |          |             |                                                                                                                      |
| 區段定址-以索引為基礎        |        |                  |          | X           |                                                                                                                      |
| 區段定址-以位元組為基礎         |        |                  |          | X           |                                                                                                                      |
| Azure 媒體服務 URL 重寫工具       |        | X                |          |             |                                                                                                                      |
| 協助工具-標題和子字幕  |        | X                |          |             |  支援隨選、即時 CEA 708 部分測試的 WebVTT                                                       |
| 協助工具-快速鍵                 | X      |                  |          |             |                                                                                                                      |
| 協助工具-高對比           |        | X                |          |             |                                                                                                                      |
| 協助工具-索引標籤焦點               |        | X                |          |             |                                                                                                                      |
| 錯誤訊息                         |        | X                |          |             | 跨 techs 的錯誤訊息不一致                                                                         |
| 觸發事件                        | X      |                  |          |             |                                                                                                                      |
| 診斷                             |        | X                |          |             | 診斷資訊僅適用于 AzureHtml5JS 技術，並在 SilverlightSS 技術上部分提供。 |
| 可自訂的技術順序                 |        | X                |          |             |                                                                                                                      |
| 啟發學習法-基本                      | X      |                  |          |             |                                                                                                                      |
| 啟發學習法-自訂              |        |                  | X        |             | 自訂僅適用于 AzureHtml5JS 技術。                                                          |
| 不連續                         | X      |                  |          |             |                                                                                                                      |
| 選取位元速率                          | X      |                  |          |             | 此 API 僅適用于 AzureHtml5JS 和 FlashSS techs。                                                    |
| 多音訊串流                      |        | X                |          |             | AzureHtml5JS 和 FlashSS techs 支援程式設計音訊交換器，並可透過 AzureHtml5JS、FlashSS 和原生 Html5 （Safari）中的 UI 選取來取得。  大部分的平臺都需要相同的編解碼器私用資料，才能切換音訊串流（相同的編解碼器、通道、取樣率等等）。 |
| UI 當地語系化                         |        | X                |          |             |                                                                                                                      |
| 多重實例播放                 |        |                  |          | X           | 此案例可能適用于某些 techs，但目前不受支援且未經過測試。 您也可以使用 iframe 讓此作業正常執行 |
| Ads 支援                             |        | x                |          |             | AMP 支援在 AzureHtml5JS 技術中插入來自大型相容 ad 伺服器的前置和後置線性廣告，以用於 VOD |
| 分析                               |        | X                |          |             | AMP 提供接聽分析和診斷事件的功能，以便傳送至您選擇的分析後端。  由於平臺限制，所有事件和屬性在 techs 中都無法使用。                                                                            |
| 自訂外觀                            |        |                  | X        |             | 使用您自己的 HTML 和 CSS，將 AMP 中的設定控制項轉換為 false。           |
| 搜尋列清理                      |        |                  |          | X           |                                                                                                                      |
| 訣竅-播放                              |        |                  |          | X           |                                                                                                                      |
| 僅限音訊                              |        |                  |          | X           | 在某些 techs 中可用於彈性資料流程，但目前不受支援，而且無法在 AzureHtml5JS 中運作。 如果平臺支援，則漸進式 MP3 播放可以與 HTML5 技術搭配使用。                                                                                                        |
| 僅限影片                              |        |                  |          | X           | 在某些 techs 中可用於彈性資料流程，但目前不受支援，而且無法在 AzureHtml5JS 中運作。      |
| 多週期簡報               |        |                  |          | X                                                                                                                                  |
| 多張攝影機角度                  |        |                  |          | X           |                                                                                                                      |
| 播放速度                          |        | X                |          |             | 大部分的情況下都支援播放速度，但由於 Chrome 中的部分 bug，行動案例除外                 |

## <a name="next-steps"></a>後續步驟 ##
- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)