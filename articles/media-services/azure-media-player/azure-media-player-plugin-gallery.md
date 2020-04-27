---
title: Azure 媒體播放器外掛程式庫
description: 本文包含可供 Azure 媒體播放器使用的外掛程式清單。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 206200f3ba0757cd738439b58c8b94874cf5a938
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727495"
---
# <a name="azure-media-player-plugin-gallery"></a>Azure 媒體播放器外掛程式庫 #

## <a name="plugins"></a>外掛程式 ##

| 外掛程式名稱                         | 示範 URL                    | 原始程式碼                | 描述    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| 其他功能                 | | | |
| **新功能！** AMP360Video                | [示範](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | 此外掛程式可讓您在桌面或 VR 相容裝置的 AMP 中，將 360 影片視覺化。 您可以在[這裡](https://doc\.babylonjs\.com/extensions/amp360video)取得完整文件： |
|  原件提示                         | [示範](http://www.skymedia.tv/asset/sprite)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Azure 媒體播放器 (AMP) 外掛程式，用於呈現從 Azure 媒體服務 (AMS) 媒體編碼器標準 (MES) 產生的影片縮圖影像原件。 |
| 診斷重疊                 | [示範](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | 此外掛程式會顯示：所有金鑰參數、影片統計資料、影片播放生命週期中的所有事件，以及 DRM 保護資訊 (如金鑰識別碼、授權取得 URL) (如果受保護)。                                                                                                                                                                      |
| 畫面播放速率和時間碼計算機 | [示範](http://mconvertitest001.blob.core.windows.net/public/example.html)                        | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | 此外掛程式會根據第一個 MPEG-DASH 影片片段的 `tfhd`/`trun` MP4 方塊來計算影片的畫面播放速率、剖析來自 MPEG-DASH 用戶端資訊清單的時間刻度值，也會提供方法讓您從播放器產生指定絕對時間的時間碼 (以及在給定時間碼的情況下提供播放器絕對時間) |
| <strike>播放速度</strike>                      | [示範](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | 此外掛程式可讓觀看者控制影片的速度。 *請注意，這項功能會自動在版本 AMP v2.0.0+ 中提供，但預設為停用。* 若要了解如何啟用這項功能，請參閱[這裡](https://github.com/Azure-Samples/azure-media-player-samples)的範例 |
| 暫留時間提示                      | [示範](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | 在滑鼠停留於進度列上時顯示時間提示，以進行時間精確的搜尋。 *注意：此外掛程式已整合到 AMP*，但如果您有興趣了解其程式設計方式，也可以看一下。                                                                                                                       |
| 標題重疊                       | [示範](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin/")                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | 在螢幕上重疊可設定的影片標題 |
| 時間軸標記                    | [示範](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | 此外掛程式會接受時間陣列，並在進度列上於這些時間重疊小型標記。 |
| 分析                           | | | |
| Application Insights                | [部落格文章](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | 此外掛程式會追蹤您的播放器計量，並將其移植到 Power BI，以便能用圖形以直覺化的方式呈現觀看者的播放器體驗。 |
| Google Analytics (分析)                    | N/A                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | 適用於 Azure 媒體播放器的 Google Analytics (分析) 外掛程式 |
| 診斷                         | | | |
| 診斷輸出                  | [示範](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | 此外掛程式會從您的播放器輸出診斷陣列，以查看其運作方式，請移至示範連結並開啟您的 JavaScript 主控台。 |
| 輕鬆存取                      | | | |
| 放大                             | [示範](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | 此外掛程式會在播放器畫面上顯示可拖曳的放大比例，以便讓觀看者可以放大您的內容 |
| 即時字幕                       | [Azure 部落格文章](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/)、[SubPly 文章](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | N/A | *如需詳細資訊，請參閱文章。* 針對 Azure 媒體播放器的即時字幕建置外掛程式所設計的端對端工作流程，按一下最左邊的連結可前往 SubPly 的網站，並深入了解該解決方案 |
| 快速鍵                            | <strike>[示範](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | 快速鍵外掛程式可讓觀看者使用一般的外掛程式組合來控制播放器的各個層面，例如 F 代表全螢幕、M 代表靜音，以及用於控制進度列的方向鍵。 *注意：此外掛程式已整合到 AMP 中，但您也可以將其作為資源* |
| 社交                              | | | |
| 共用                               | [示範](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | 此外掛程式會在播放器的控制列中新增 [分享] 按鈕，讓您的觀看者可以透過 Facebook、Twitter 或 Linkedin 與朋友分享其觀看的影片。 |

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)