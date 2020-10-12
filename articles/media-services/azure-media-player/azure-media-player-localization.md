---
title: Azure 媒體播放機當地語系化
description: 非英文地區設定的使用者支援多種語言。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 8a459d1fba4353b4b3e092e83a759314cc455ead
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87087283"
---
# <a name="azure-media-player-localization"></a>Azure 媒體播放機當地語系化 #

多種語言支援可讓非英文地區設定的使用者以原生方式與播放程式互動。 Azure 媒體播放機會使用語言的全域字典來具現化，這會根據頁面語言將錯誤訊息當地語系化。 開發人員也可以使用強制設定的語言來建立播放機實例。 預設語言是英文 (en) 。

> [!NOTE]
> 這項功能仍在進行某些測試，因此可能會發生錯誤。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure 媒體播放機目前支援下列語言的對應語言代碼：

| Language            | 程式碼 | Language                | 程式碼   | Language                | 程式碼         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| 英文 {default}   | en   | 克羅埃西亞文                | hr     | 羅馬尼亞文                | ro           |
| 阿拉伯文              | ar   | 匈牙利文               | hu     | 斯洛伐克文                  | sk           |
| 保加利亞文           | bg   | 印尼文              | id     | 斯洛維尼亞文                 | sl           |
| 卡達隆尼亞文             | ca   | 冰島文               | 是     | 塞爾維亞文 (斯拉夫)      | sr-iov cyrl-cs   |
| 捷克文               | cs   | 義大利文                 | it     | 塞爾維亞文 (拉丁)         | sr-latn-rs   |
| 丹麥文              | da   | 日文                | ja     | 俄文                 | ru           |
| 德文              | de   | 哈薩克文                  | kk     | 瑞典文                 | sv           |
| 希臘文               | el   | 韓文                  | ko     | 泰文                    | th           |
| 西班牙文             | es   | 立陶宛文              | lt     | 他加祿文                 | Tl           |
| 愛沙尼亞文            | et   | 拉脫維亞文                 | lv     | 土耳其文                 | tr           |
| 巴斯克文              | eu   | 馬來西亞               | ms     | 烏克蘭文               | uk           |
| 波斯文               | fa   | 挪威文-BokmÃ¥ l     | nb     | 烏都文                    | ur           |
| 芬蘭文             | fi   | 荷蘭文                   | nl     | 越南文              | vi           |
| 法文              | fr   | 挪威文 - 耐諾斯克     | nn     | 簡體中文    | zh-hans      |
| 加利西亞文            | gl   | 波蘭文                  | pl     | 繁體中文   | zh-hant      |
| Hebrew              | he   | 葡萄牙文 - 巴西     | pt-br  |                         |              |
| Hindi               | hi   | 葡萄牙文 - 葡萄牙   | pt-pt  |                         |              |


> [!NOTE]
> 如果您不想要進行任何當地語系化，則必須將語言強制為英文

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)
