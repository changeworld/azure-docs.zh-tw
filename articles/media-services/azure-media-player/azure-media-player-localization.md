---
title: Azure 媒體播放機當地語系化
description: 非英文地區設定之使用者的多語言支援。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ca4dc888af414ede270118eff72652f098d3306c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779037"
---
# <a name="localization"></a>當地語系化 #

多種語言支援可讓非英文地區設定的使用者以原生方式與播放程式互動。 Azure 媒體播放機會使用語言的全域字典來具現化，這會根據頁面語言將錯誤訊息當地語系化。 開發人員也可以建立具有強制 set 語言的 player 實例。 預設語言為英文（en）。

> [!NOTE]
> 這項功能仍在進行一些測試，因此受限於 bug。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure 媒體播放機目前支援下列語言及其對應的語言代碼：

| Language            | 程式碼 | Language                | 程式碼   | Language                | 程式碼         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| 英文 {預設}   | en   | 克羅埃西亞文                | hr     | 羅馬尼亞文                | ro           |
| 阿拉伯文              | ar   | 匈牙利文               | hu     | 斯洛伐克文                  | sk           |
| 保加利亞文           | bg   | 印尼文              | id     | 斯洛維尼亞文                 | sl           |
| 卡達隆尼亞文             | ca   | 冰島文               | 是     | 塞爾維亞文 (斯拉夫)      | cyrl-cs   |
| 捷克文               | cs   | 義大利文                 | it     | 塞爾維亞文 (拉丁)         | sr-latn-rs   |
| 丹麥文              | da   | 日文                | ja     | 俄文                 | ru           |
| 德文              | de   | 哈薩克文                  | kk     | 瑞典文                 | sv           |
| 希臘文               | el   | 韓文                  | ko     | 泰文                    | th           |
| 西班牙文             | es   | 立陶宛文              | lt     | 他加祿文                 | tl           |
| 愛沙尼亞文            | et   | 拉脫維亞文                 | lv     | 土耳其文                 | tr           |
| 巴斯克文              | eu   | 馬來西亞               | ms     | 烏克蘭文               | uk           |
| 波斯文               | fa   | 挪威文-BokmÃ¥ l     | nb     | 烏都文                    | ur           |
| 芬蘭文             | fi   | 荷蘭文                   | nl     | 越南文              | vi           |
| 法文              | fr   | 挪威文 - 耐諾斯克     | nn     | 中文-簡體    | zh-hans      |
| 加利西亞文            | gl   | 波蘭文                  | pl     | 中文-繁體   | zh-hant      |
| Hebrew              | he   | 葡萄牙文 - 巴西     | pt-br  |                         |              |
| Hindi               | hi   | 葡萄牙文 - 葡萄牙   | pt-pt  |                         |              |


> [!NOTE]
> 如果您不想要進行任何當地語系化，您必須將語言強制為英文

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)
