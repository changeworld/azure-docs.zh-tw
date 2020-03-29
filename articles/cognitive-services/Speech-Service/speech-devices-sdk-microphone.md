---
title: 語音設備 SDK 麥克風陣列建議
titleSuffix: Azure Cognitive Services
description: 語音設備 SDK 麥克風陣列建議。 建議將這些陣列幾何體與 Microsoft 音訊堆疊一起使用。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168129"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>語音設備 SDK 麥克風陣列建議

在本文中，您將瞭解如何為語音設備 SDK 設計麥克風陣列。

語音設備 SDK 最適合根據以下準則（包括麥克風幾何形狀和元件選擇）設計的麥克風陣列。 還就集成和電氣注意事項提供了指導。

## <a name="microphone-geometry"></a>麥克風幾何形狀

建議使用以下陣列幾何體與 Microsoft 音訊堆疊一起使用。 隨著對特定應用程式、使用者方案和設備外形的依賴的麥克風數量增加，聲源的位置和環境雜訊的抑制得到了改進。

|     | 圓形陣列 |     | 線性陣列 |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#麥克風 | 7 | 4 | 4 | 2 |
| 幾何 | 6 外，1 中心，半徑 = 42.5 毫米，均勻間隔 | 3 外，1 中心，半徑 = 42.5 毫米，均勻間隔 | 長度 = 120 mm，間距 = 40 mm | 間距 = 40 mm |

麥克風通道應根據上述每個陣列的編號排序，從 0 增加到 0。 Microsoft 音訊堆疊將需要額外的音訊播放引用流來執行回聲取消。

## <a name="component-selection"></a>元件選擇

應選擇麥克風元件，以準確再現無雜訊和失真的信號。

選擇麥克風時，推薦的屬性是：

| 參數 | 建議 |
| --------- | ----------- |
| 信 噪 比 | \>• 65 dB（1 kHz 信號 94 dBSPL，A 加權雜訊） |
| 振幅匹配 | ± 1 dB = 1 kHz |
| 相位匹配 | ± 2° × 1 千赫 |
| 聲學超載點 （AOP） | \>• 120 dBSPL （THD = 10%） |
| 位元速度 | 最小 24 位 |
| 取樣率 | 最小 16 kHz\* |
| 頻率回應 | • 3 dB，200-8000 Hz 浮動蒙版\* |
| 可靠性 | 儲存溫度範圍 -40°C 至 70°C<br />工作溫度範圍 -20°C 至 55°C |

\*_對於高品質的通信 （VoIP） 應用，可能需要更高的取樣速率或"更寬"的頻率範圍_

良好的元件選擇必須與良好的電聲集成配對，以避免損害所用元件的性能。 獨特的用例可能還需要額外的要求（例如：工作溫度範圍）。

## <a name="microphone-array-integration"></a>麥克風陣列集成

集成到設備中時，麥克風陣列的性能將不同于元件規範。 請務必確保話筒在集成後匹配良好。 因此，在任何固定增益或 EQ 之後測量的設備性能應符合以下建議：

| 參數          | 建議                                        |
| ------------------ | -------------------------------------------------- |
| 信 噪 比                | \>63 dB （1 kHz 信號 94 dBSPL，A 加權雜訊） |
| 輸出靈敏度 | -26 dBFS/Pa = 1 kHz（推薦）                  |
| 振幅匹配 | ± 2 dB，200-8000 Hz                                |
| THD%\*             | • 1%，200-8000 Hz，94 dBSPL，第 5 階             |
| 頻率回應 | • 6 dB，200-8000 Hz 浮動蒙版\*\*              |

\*\*_測量 THD 需要低失真揚聲器（例如 Neumann KH120）_

\*\*_高品質通信 （VoIP） 應用可能需要"更寬"的頻率範圍_

## <a name="speaker-integration-recommendations"></a>揚聲器集成建議

由於包含揚聲器的語音辨識設備需要回聲消除，因此為揚聲器選擇和集成提供了其他建議。

| 參數 | 建議 |
| --------- | ----------- |
| 線性度注意事項 | 揚聲器引用後無需非線性處理，否則需要基於硬體的環回引用流 |
| 揚聲器環回 | 通過 WASAPI、專用 API、自訂 ALSA 外掛程式 （Linux） 提供或通過固件通道提供 |
| THD% | 第三個八度頻段最小 5 階，70 dBA 重播 = 0.8 m = 6.3%，315-500 Hz = 5%，630-5000 Hz |
| 回聲耦合到麥克風 | \>-10 dB TCLw 使用 ITU-T G.122 附件 B.4 方法，標準化為麥克風電平<br />TCLw = TCLw 測量\+（測量電平 - 目標輸出靈敏度）<br />TCLw = TCLw 測量\+（測量級別 - （-26）） |

## <a name="integration-design-architecture"></a>集成設計架構

將麥克風集成到設備中時，需要以下體系結構指南：

| 參數 | 建議 |
| --------- | -------------- |
| 麥克風埠相似性 | 所有麥克風埠在陣列中的長度相同 |
| 麥克風埠尺寸 | 埠尺寸 ±0.8-1.0 mm。 埠長度/ 埠\<直徑 2 |
| 麥克風密封         | 密封墊片在堆疊中均勻實現。 建議\>泡沫墊片的壓縮率為 70% |
| 麥克風可靠性     | 網格應用於防止灰塵和入口（底部移植麥克風的 PCB 和密封墊片/頂蓋之間） |
| 麥克風隔離       | 橡膠墊片和振動通過結構分離，特別是用於隔離由於集成揚聲器造成的任何振動路徑 |
| 採樣時鐘      | 設備音訊必須無抖動和低漂移的跌落 |
| 記錄功能   | 設備必須能夠同時記錄單個通道原始流 |
| USB                 | 所有 USB 音訊輸入裝置都必須根據[USB 音訊裝置 Rev3 規範](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement)設置描述項 |
| 麥克風幾何 | 驅動程式必須正確實現[麥克風陣列幾何描述項](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) |
| 可搜尋性     | 設備不得具有任何不可發現或無法控制的硬體、固件或基於協力廠商軟體的非線性音訊處理演算法。 |
| 捕獲格式      | 捕獲格式必須使用最小採樣速率為 16 kHz 和推薦的 24 位深度 |

## <a name="electrical-architecture-considerations"></a>電氣架構注意事項

在適用的情況下，陣列可以連接到 USB 主機（如運行 Microsoft 音訊堆疊的 SoC）和與語音服務或其他應用程式的介面。

硬體元件（如 PDM 到 TDM 轉換）應確保將麥克風的動態範圍和 SNR 保存在重新採樣器中。

任何音訊 MCU 都應支援高速 USB 音訊等級 2.0，以便為取樣速率和位深度高達 7 個通道提供必要的頻寬。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解有關語音設備 SDK 的更多內容](speech-devices-sdk.md)
