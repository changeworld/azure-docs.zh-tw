---
title: Azure 媒體播放器當地語系化
description: 對非英語區域設置的使用者提供多種語言支援。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727173"
---
# <a name="localization"></a>當地語系化 #

多種語言支援允許非英語區域設置的使用者與玩家進行本機交互。 Azure 媒體播放器將實例化為通用語言詞典,該詞典將根據頁面語言當地語系化錯誤消息。 開發人員還可以使用強制集語言創建播放機實例。 默認語言為英語(en)。

> [!NOTE]
> 此功能仍在經過一些測試,因此會受到錯誤的影響。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure 媒體播放器目前支援具有相應語言代碼的以下語言:

| Language            | 程式碼 | Language                | 程式碼   | Language                | 程式碼         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| 英語 [預設]   | en   | 克羅埃西亞文                | hr     | 羅馬尼亞文                | ro           |
| 阿拉伯文              | ar   | 匈牙利文               | hu     | 斯洛伐克文                  | sk           |
| 保加利亞文           | bg   | 印尼文              | id     | 斯洛維尼亞文                 | sl           |
| 卡達隆尼亞文             | 約   | 冰島文               | is     | 塞爾維亞文 (斯拉夫)      | sr-cyrl-cs   |
| 捷克文               | cs   | 義大利文                 | it     | 塞爾維亞文 (拉丁)         | sr-latn-rs   |
| 丹麥文              | da   | 日文                | ja     | 俄文                 | ru           |
| 德文              | de   | 哈薩克文                  | Kk     | 瑞典文                 | sv           |
| 希臘文               | el   | 韓文                  | ko     | 泰文                    | th           |
| 西班牙文             | es   | 立陶宛文              | lt     | 他加祿文                 | Tl           |
| 愛沙尼亞文            | et   | 拉脫維亞文                 | lv     | 土耳其文                 | tr           |
| 巴斯克文              | 歐盟   | 馬來西亞               | ms     | 烏克蘭文               | uk           |
| 波斯文               | 發   | 挪威文 - 博克°l     | nb     | 烏都文                    | 你           |
| 芬蘭文             | fi   | 荷蘭文                   | nl     | 越南文              | VI.           |
| 法文              | fr   | 挪威文 - 耐諾斯克     | nn     | 中文 - 簡體字    | zh-hans      |
| 加利西亞文            | gl   | 波蘭文                  | pl     | 中文 - 傳統   | zh-hant      |
| Hebrew              | he   | 葡萄牙文 - 巴西     | pt-br  |                         |              |
| Hindi               | hu   | 葡萄牙文 - 葡萄牙   | pt-pt  |                         |              |


> [!NOTE]
> 如果您不希望進行任何當地語系化,則必須強制該語言為英文

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)