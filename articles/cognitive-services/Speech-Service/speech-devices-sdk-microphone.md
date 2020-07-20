---
title: 語音裝置 SDK 麥克風陣列建議
titleSuffix: Azure Cognitive Services
description: 語音裝置 SDK 麥克風陣列建議。 建議將這些陣列幾何用於 Microsoft 音訊堆疊。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: eace63effdbd62d8f08395aa16683627b475a963
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232520"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>語音裝置 SDK 麥克風陣列建議

在本文中，您將瞭解如何設計「語音裝置 SDK」的麥克風陣列。

語音裝置 SDK 最適合使用已根據下列指導方針設計的麥克風陣列，包括麥克風幾何和元件選取。 同時也提供整合和電氣考慮的指導方針。

## <a name="microphone-geometry"></a>麥克風幾何

建議將下列陣列幾何用於 Microsoft 音訊堆疊。 具有特定應用程式、使用者案例和裝置外型規格之相依性的更多麥克風，因而改善了音效來源的位置和環境雜訊的拒絕。

| 麥克風 & Geometry | 圓形陣列 | 圓形陣列 | 線性陣列 | 線性陣列 |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#麥克風 | 7 | 4 | 4 | 2 |
| 幾何 | 6個外部，1個中心，半徑 = 42.5 mm，平均間距 | 3個外部，1個中心，半徑 = 42.5 mm，平均間距 | 長度 = 120 mm，間距 = 40 mm | 間距 = 40 mm |

您應該根據上述每個陣列所描述的編號來排序麥克風頻道，從0增加。 Microsoft 音訊堆疊需要音訊播放的額外參考串流，才能執行 echo 取消作業。

## <a name="component-selection"></a>元件選取

應選取麥克風元件，以精確地重現無雜訊和失真的信號。

選取麥克風時的建議屬性如下：

| 參數 | 建議 |
| --------- | ----------- |
| SNR | \>= 65 dB (1 kHz 信號 94 dBSPL，) 加權雜訊 |
| 波幅比對 | ± 1 dB @ 1 kHz |
| 階段對應 | ±2° @ 1 kHz |
| 聲場超載點 (AOP)  | \>= 120 dBSPL (THD = 10% )  |
| 位元速度 | 最小24位 |
| 取樣率 | 最小 16 kHz\* |
| 頻率回應 | ± 3 dB，200-8000 Hz 浮動遮罩\* |
| 可靠性 | 儲存溫度範圍-40 ° C 至70° C<br />操作溫度範圍-20 ° C 到55° C |

\*_ (VoIP) 應用程式的高品質通訊可能需要較高的取樣率或「更寬」頻率範圍_

良好的元件選取範圍必須與良好的 electroacoustic 整合配對，才能避免損害所使用元件的效能。 唯一的使用案例可能也需要額外的需求 (例如：) 的操作溫度範圍。

## <a name="microphone-array-integration"></a>麥克風陣列整合

整合到裝置時，麥克風陣列的效能會與元件規格不同。 在整合之後，請務必確定麥克風的相符程度很重要。 因此，在任何固定增益或 EQ 之後測量的裝置效能應該符合下列建議：

| 參數          | 建議                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \>63 dB (1 kHz 信號 94 dBSPL，) 加權雜訊 |
| 輸出敏感度 | -26 dBFS/Pa @ 1 kHz (建議)                   |
| 波幅比對 | ± 2 dB，200-8000 Hz                                |
| THD%\*             | ≤1%，200-8000 Hz，94 dBSPL，第5順序             |
| 頻率回應 | ±6資料庫，200-8000 Hz 浮動遮罩\*\*              |

\*\*_需要低扭曲喇叭來測量 THD (例如 Neumann KH120) _

\*\*_ (VoIP) 應用程式的高品質通訊可能需要「更寬」頻率範圍_

## <a name="speaker-integration-recommendations"></a>演講者整合建議

當包含喇叭的語音辨識裝置需要回應取消時，會針對說話者選取和整合提供其他建議。

| 參數 | 建議 |
| --------- | ----------- |
| 線性考慮 | 說話者參考後不進行非線性處理，否則需要以硬體為基礎的回送參考資料流 |
| 喇叭回送 | 透過 WASAPI、私用 Api、自訂 ALSA 外掛程式 (Linux) ，或透過固件通道提供 |
| THD% | 第三個 Octave 區段最少5個訂單，70 dBA 播放 @ 0.8 m ≤6.3%，315-500 Hz ≤5%，630-5000 Hz |
| 回應與麥克風的結合 | \>-使用 ITU-T G. 122 附錄 B. 4 方法，正規化為 mic 層級的10個資料庫 TCLw<br />TCLw = TCLwmeasured \+ (測量的層級目標輸出敏感度) <br />TCLw = TCLwmeasured \+ (測量層級 (-26) # A3 |

## <a name="integration-design-architecture"></a>整合設計架構

將麥克風整合到裝置時，需要下列架構指導方針：

| 參數 | 建議 |
| --------- | -------------- |
| Mic 埠相似性 | 陣列中所有麥克風埠的長度都相同 |
| Mic 埠維度 | 埠大小Ø 0.8-1.0 mm。 埠長度/埠直徑 \< 2 |
| Mic 密封         | 在堆疊中以一致方式實作為的密封墊片。 \>為泡沫塑膠墊片建議70% 的壓縮比率 |
| Mic 可靠性     | 網格應該用來防止在下一個移植麥克風的 PCB 和密封襯墊/上蓋之間進行灰塵和輸入 ()  |
| Mic 隔離       | 透過結構的橡膠墊片和震動分離，特別是為了隔離任何震動路徑，因為整合式喇叭 |
| 取樣時鐘      | 裝置音訊必須沒有抖動，而且有低漂移的下降 |
| 記錄功能   | 裝置必須能夠同時記錄個別通道的原始資料流程 |
| USB                 | 所有 USB 音訊輸入裝置都必須根據[USB 音訊裝置 Rev3 規格](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement)設定描述項 |
| 麥克風幾何 | 驅動程式必須正確地執行[麥克風陣列幾何描述](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry)項 |
| 可搜尋性     | 裝置不得將任何 & 或無法控制硬體、固件或以協力廠商軟體為基礎的非線性音訊處理演算法提供給裝置 |
| Capture 格式      | 捕捉格式必須使用 16 kHz 的最低取樣率和建議的24位深度 |

## <a name="electrical-architecture-considerations"></a>電力架構考慮

如果適用的話，陣列可能會連接到 USB 主機 (例如執行 Microsoft 音訊堆疊) 的 SoC，以及語音服務或其他應用程式的介面。

硬體元件（例如 PDM 到 TDM 轉換）應確保在重新取樣器中保留麥克風的動態範圍和 SNR。

在任何音訊 Mcu 中，都應該支援高速 USB 音訊類別2.0，以提供最多七個頻道的必要頻寬（以較高的取樣率和位深度表示）。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入瞭解語音裝置 SDK](speech-devices-sdk.md)
