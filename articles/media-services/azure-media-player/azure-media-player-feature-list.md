---
title: Azure 媒體播放機功能清單
description: Azure 媒體播放機的功能參考。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 88048c3328114f17b30859efb41bb9f059b71439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296360"
---
# <a name="feature-list"></a>功能清單 #
以下是已測試的功能和不支援的功能清單：

| 功能 | 測試 | 部分測試 | 未經 | 支援 | 注意 |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| **播放**                                |        |                  |          |             |                                                                                                                      |
| 基本隨選播放                | X      |                  |          |             | 僅支援來自 Azure 媒體服務的資料流程                                                                      |
| 基本即時播放                     | X      |                  |          |             | 僅支援來自 Azure 媒體服務的資料流程                                                                      |
| AES                                     | X      |                  |          |             | 支援 Azure 媒體服務金鑰傳遞服務                                                                   |
| 多重 DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | 支援 Azure 媒體服務金鑰傳遞服務                                                                   |
| Widevine                                |        | X                |          |             | 支援資訊清單中所述的 Widevine PSSH 方塊                                                                    |
| FairPlay                                |        | X                |          |             | 支援 Azure 媒體服務金鑰傳遞服務                                                                   |
| **技術人員**                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                   | X      |                  |          |             |                                                                                                                      |
| Flash (FlashSS)                 | X      |                  |          |             | 本技術並非所有功能都可使用。                                                                         |
| Silverlight Fallback SilverlightSS      | X      |                  |          |             | 本技術並非所有功能都可使用。                                                                         |
| 原生 HLS 傳遞 (Html5)          |        | X                |          |             | 由於平臺限制，這種技術並非所有功能都可以使用。                                            |
| **功能**                                |        |                  |          |             |                                                                                                                      |
| API 支援                             | X      |                  |          |             | 查看已知問題清單                                                                                                |
| 基本 UI                                | X      |                  |          |                                                                                                                                    |
| 透過 JavaScript 初始化       | X      |                  |          |             |                                                                                                                      |
| 透過影片標記初始化        |        | X                |          |             |                                                                                                                      |
| 以時間為基礎的區段定址         | X      |                  |          |             |                                                                                                                      |
| 區段定址-以索引為基礎        |        |                  |          | X           |                                                                                                                      |
| 區段定址-以位元組為基礎         |        |                  |          | X           |                                                                                                                      |
| Azure 媒體服務 URL 重寫       |        | X                |          |             |                                                                                                                      |
| 協助工具-字幕和子字幕  | X      |                 |          |             |  WebVTT (隨選) 、CEA 708 (隨選和即時) 和 IMSC1 (隨選和即時)                                                        |
| 協助工具-熱鍵                 | X      |                  |          |             |                                                                                                                      |
| 協助工具-高對比           |        | X                |          |             |                                                                                                                      |
| 協助工具-Tab 鍵焦點               |        | X                |          |             |                                                                                                                      |
| 錯誤訊息                         |        | X                |          |             | Techs 之間的錯誤訊息不一致                                                                         |
| 事件觸發                        | X      |                  |          |             |                                                                                                                      |
| 診斷                             |        | X                |          |             | 診斷資訊只適用于 AzureHtml5JS 技術，部分可在 SilverlightSS 技術上使用。 |
| 可自訂的技術順序                 |        | X                |          |             |                                                                                                                      |
| 啟發學習法-基本                      | X      |                  |          |             |                                                                                                                      |
| 啟發學習法-自訂              |        |                  | X        |             | 自訂僅適用于 AzureHtml5JS 技術。                                                          |
| 間斷                         | X      |                  |          |             |                                                                                                                      |
| 選取位元速率                          | X      |                  |          |             | 此 API 僅適用于 AzureHtml5JS 和 FlashSS techs。                                                    |
| 多音訊串流                      |        | X                |          |             | AzureHtml5JS 和 FlashSS techs 支援程式設計音訊參數，並可透過) Safari 中 AzureHtml5JS、FlashSS 和原生 Html5 (上的 UI 選取來取得。  大部分的平臺都需要相同的編解碼器私用資料來切換音訊串流 (相同的編解碼器、通道、取樣率等 ) 。 |
| UI 當地語系化                         |        | X                |          |             |                                                                                                                      |
| 多重實例播放                 |        |                  |          | X           | 這種情況可能適用于某些 techs，但目前不受支援且尚未測試。 您也可以使用 iframe 來使用 |
| Ads 支援                             |        | X                |          |             | AMP 支援在 AzureHtml5JS 技術中插入來自大型 ad 伺服器的最早和後續滾動線性廣告，以供 VOD |
| 分析                               |        | X                |          |             | AMP 提供接聽分析和診斷事件的能力，以傳送至您選擇的分析後端。  由於平臺限制，所有的事件和屬性都不會跨 techs 使用。                                                                            |
| 自訂外觀                            |        |                  | X        |             | 您可以藉由在 AMP 中將控制項設定為 false，並使用您自己的 HTML 和 CSS 來達成此案例。           |
| 搜尋列清理                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| 僅限音訊                              | X      |                  |          |           | 在 AzureHtml5JS 中支援。 如果平臺支援，則漸進式的 MP3 播放可以搭配 HTML5 技術使用。                                                                                                        |
| 僅影片                              | X      |                  |          |           | 在 AzureHtml5JS 中支援。                                                                                                        |
| 多重期間簡報               |        |                  |          | X                                                                                                                                  |
| 多個相機角度                  |        |                  |          | X           |                                                                                                                      |
| 播放速度                          |        | X                |          |             | 由於 Chrome 中的部分 bug，大部分案例都支援播放速度，但行動案例除外                 |

## <a name="next-steps"></a>後續步驟 ##
- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)