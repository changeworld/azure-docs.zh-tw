---
title: Azure 媒體播放器協助工具
description: 深入了解 Azure 媒體播放器的協助工具設定。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727605"
---
# <a name="accessibility"></a>Accessibility #

Azure 媒體播放器可與螢幕助讀程式功能搭配運作，例如 Windows 朗讀程式和 Apple OSX/iOS VoiceOver。 UI 按鈕有可用的替代標籤，而且螢幕助讀程式可以在使用者瀏覽至這些替代標籤時念出這些標籤。 您可以在作業系統層級上進行其他設定。

## <a name="captions-and-subtitles"></a>原文字幕和翻譯字幕 ##

Azure 媒體播放器目前的隱藏式輔助字幕支援僅限於採用 WebVTT 格式的隨選事件，以及使用 CEA 708 的即時事件。 目前不支援 TTML 格式。 隱藏式輔助字幕和字幕可讓播放器服務更廣大的觀眾，包括聽障人士或想要以其他語言閱讀的人。 隱藏式輔助字幕也可增加影片效果、方便觀眾理解，以及讓觀眾可在安靜的環境 (例如工作場所) 觀看影片。  

## <a name="high-contrast-mode"></a>高對比模式 ##

Azure 媒體播放器中的預設 UI 符合大部分裝置/瀏覽器的高對比檢視模式規範。 您可以在作業系統層級上進行設定。

## <a name="mobility-options"></a>行動性選項 ##

### <a name="tabbing-focus"></a>透過 Tab 鍵移動焦點 ###

Azure 媒體播放器中可以使用一般 HTML 標準所提供的透過 Tab 鍵移動焦點功能。 若要啟用透過 Tab 鍵移動焦點的功能，您必須在 HTML `<video>` 中新增 `tabindex=0` (如果您了解定位順序在 HTML 中會如何受到影響，則也可以新增其他值)，例如：`<video ... tabindex=0>...</video>`。 在某些平台上，只有當控制項可見且平台支援這些功能時，才會顯示控制項的焦點。

一旦啟用透過 Tab 鍵移動焦點的功能，使用者就可以有效地瀏覽及控制影片播放器，而不需依賴其滑鼠。 每個捷徑功能表或可控制的元素均可藉由叫用 Tab 鍵來瀏覽至其中，再透過 Enter 鍵或空格鍵來加以選取。 在捷徑功能表上按 Enter 鍵或空格鍵會使其展開，以供使用者繼續用 Tab 鍵來瀏覽以選取功能表項目。 一旦您到達想要選取的項目內容，即可再次按 Enter 鍵或空格鍵來完成選取。

### <a name="hotkeys"></a>快速鍵 ###

Azure 媒體播放器支援透過鍵盤快速鍵進行控制。 在網頁瀏覽器中，控制基礎 video 元素的唯一方法就是將焦點放在播放器上。 焦點放在播放器之後，快速鍵就可以控制播放器的功能。  下表描述各種快速鍵和其相關聯的行為：

| 快速鍵              | 行為                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | 播放器會進入/結束全螢幕模式                                  |
| M/m                  | 播放器音量將靜音/取消靜音                                          |
| 向上鍵和向下鍵    | 播放器音量會增加/減少                                    |
| 向左鍵和向右鍵 | 影片進度會增加/減少                                  |
| 0,1,2,3,4,5,6,7,8,9  | 根據所按的按鍵，影片進度會變更為 0%\- 90% |
| 按一下的動作         | 影片將會播放/暫停                                                   |

## <a name="next-steps"></a>後續步驟

<!---Some context for the following links goes here--->
- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)