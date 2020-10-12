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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86232520"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>語音裝置 SDK 麥克風陣列建議

在本文中，您將瞭解如何為語音裝置 SDK 設計麥克風陣列。

語音裝置 SDK 最適合搭配已根據下列指導方針設計的麥克風陣列，包括麥克風幾何和元件選取。 本指南也提供整合和電氣考慮的指引。

## <a name="microphone-geometry"></a>麥克風幾何

建議將下列陣列幾何用於 Microsoft 音訊堆疊。 具有特定應用程式、使用者案例和裝置外型規格之相依性的多個麥克風，可改善音效來源的位置並拒絕環境雜訊。

| 麥克風 & 幾何 | 迴圈陣列 | 迴圈陣列 | 線性陣列 | 線性陣列 |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# 麥克風 | 7 | 4 | 4 | 2 |
| 幾何形狀 | 6個外部、1中心、半徑 = 42.5 mm、平均間距 | 3外部、1中心、半徑 = 42.5 mm、平均間距 | 長度 = 120 mm，間距 = 40 mm | 間距 = 40 mm |

麥克風頻道應根據上述陣列所描述的編號進行排序，從0增加。 Microsoft 音訊堆疊需要額外的音訊播放串流，才能執行回應取消。

## <a name="component-selection"></a>元件選取

您應該選取麥克風元件，以精確地重現無雜訊和失真的信號。

選取麥克風時的建議屬性如下：

| 參數 | 建議 |
| --------- | ----------- |
| 信 噪 比 | \>= 65 dB (1 kHz 信號 94 dBSPL，加權雜訊)  |
| 振幅相符 | ± 1 dB @ 1 kHz |
| 階段相符 | ±2° @ 1 kHz |
| 聲場超載點 (AOP)  | \>= 120 dBSPL (THD = 10% )  |
| 位元速度 | 最小24位 |
| 取樣率 | 最小 16 kHz\* |
| 頻率回應 | ± 3 dB、200-8000 Hz 浮動遮罩\* |
| 可靠性 | 儲存體溫度範圍-40 ° C 至70° C<br />操作溫度範圍-20 ° C 至55° C |

\*_高品質的通訊 (VoIP) 應用程式可能需要較高的取樣率或「更寬」頻率範圍_

良好的元件選取必須與良好的 electroacoustic 整合配對，以避免因而影響所用元件的效能。 唯一的使用案例也可能需要額外的需求 (例如：) 的操作溫度範圍。

## <a name="microphone-array-integration"></a>麥克風陣列整合

當麥克風陣列整合至裝置時，其效能會與元件規格不同。 在整合之後，請務必確定麥克風的相符程度。 因此，在任何固定增益或 EQ 之後測量的裝置效能都應該符合下列建議：

| 參數          | 建議                                        |
| ------------------ | -------------------------------------------------- |
| 信 噪 比                | \> 63 dB (1 kHz 信號 94 dBSPL，加權雜訊)  |
| 輸出敏感度 | -26 dBFS/Pa @ 1 kHz (建議的)                   |
| 振幅相符 | ± 2 dB、200-8000 Hz                                |
| THD%\*             | ≤1%、200-8000 Hz、94 dBSPL、5順序             |
| 頻率回應 | ± 6 dB、200-8000 Hz 浮動遮罩\*\*              |

\*\*_需要低扭曲喇叭來測量 THD (例如 Neumann KH120) _

\*\*_高品質的通訊 (VoIP) 應用程式可能需要「更寬」頻率範圍_

## <a name="speaker-integration-recommendations"></a>說話者整合建議

當包含喇叭的語音辨識裝置需要回應取消時，會針對說話者選取和整合提供其他建議。

| 參數 | 建議 |
| --------- | ----------- |
| 線性考慮 | 說話者參考後無非線性處理，否則需要以硬體為基礎的回送參考資料流 |
| 喇叭回送 | 透過 WASAPI、私用 Api、自訂 ALSA 外掛程式提供 (Linux) ，或透過固件通道提供 |
| THD% | 第三個 Octave 的區段最少第5個訂單，70 dBA 播放 @ 0.8 m ≤6.3%，315-500 Hz ≤5%，630-5000 Hz |
| Echo 與麥克風的結合 | \> -使用 ITU-T G. 122 附錄 b. 4 方法的 10 dB TCLw，標準化為 mic 層級<br />TCLw = TCLwmeasured \+ (測量層級目標輸出敏感度) <br />TCLw = TCLwmeasured \+ (測量層級- (-26) # A3 |

## <a name="integration-design-architecture"></a>整合設計架構

將麥克風整合至裝置時，需要下列架構指導方針：

| 參數 | 建議 |
| --------- | -------------- |
| Mic 埠相似性 | 陣列中所有麥克風埠的長度相同 |
| Mic 埠維度 | 埠大小Ø 0.8-1.0 mm。 埠長度/埠直徑 \< 2 |
| Mic 密封         | 密封在堆疊中一致執行的墊片。 \>針對泡沫墊片建議70% 壓縮率 |
| Mic 可靠性     | 網格應該用來防止在下一部移植麥克風的 PCB 和密封的襯墊/頂蓋之間進行灰塵和輸入 ()  |
| Mic 隔離       | 橡膠墊片和震動會透過結構分離，特別是為了因應整合式喇叭而隔離任何震動路徑 |
| 取樣時鐘      | 裝置音訊必須有低漂移的無抖動和下降 |
| 記錄功能   | 裝置必須能夠同時錄製個別的通道原始串流 |
| USB                 | 所有 USB 音訊輸入裝置都必須根據[USB 音訊裝置 Rev3 規格](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement)來設定描述項 |
| 麥克風幾何 | 驅動程式必須正確地執行 [麥克風陣列幾何描述](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) 項 |
| 可搜尋性     | 裝置不能有任何 & 或無法控制硬體、固件或協力廠商軟體為基礎的非線性音訊處理演算法 |
| Capture 格式      | 捕捉格式必須使用 16 kHz 的最小取樣率和建議的24位深度 |

## <a name="electrical-architecture-considerations"></a>電氣架構考慮

在適用的情況下，陣列可能會連接到 USB 主機 (例如執行 Microsoft 音訊堆疊) 的 SoC，以及語音服務或其他應用程式的介面。

像是 PDM 到 TDM 轉換的硬體元件應該確保重設取樣器內會保留麥克風的動態範圍和 SNR。

任何音訊 Mcu 中都應該支援高速 USB 音訊類別2.0，以提供最多七個通道的必要頻寬（以較高的取樣率和位深度為單位）。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [深入瞭解語音裝置 SDK](speech-devices-sdk.md)
