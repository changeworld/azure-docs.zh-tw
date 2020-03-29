---
title: 語音語音集 - 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何將語音服務拼音字母映射到國際拼音字母表 （IPA），以及何時使用該設置。
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675334"
---
# <a name="speech-service-phonetic-sets"></a>語音服務語音集

語音服務定義語音字母表（簡稱"電話集"），由七種語言組成;`en-US`、、、、、、、、、、、、、、、、、、、、、、、、、、 `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` `zh-TW` 語音服務電話集通常映射到<a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">國際語音字母表 （IPA）。 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 語音服務電話集與[語音合成標記語言 （SSML）](speech-synthesis-markup.md)結合使用，作為文本到語音服務服務的一部分。 在本文中，您將瞭解如何映射這些電話組以及何時使用哪個電話組。

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>英語超分段

| 示例 1（為輔音啟動，母音的單詞首字母） | 示例 2（輔音的交音，母音的單詞中音核） | 示例 3（輔音 Coda，母音單詞"最終"） | 註解 |
|--|--|--|--|
| 漢堡 /b er **1** r - g ax r/ | 法拉費爾 /f 斧 - l aa **1** - f ax l/ | 吉他 /g ih - t aa **1** r/ | 語音服務電話集在應力音節的母音後放置壓力 |
| inopportune /ih **2** - n aa - p ax r - t uw 1 n/ | 異位 /d ih - s ih **2**- m 斧 - l eh 1 - r ax - t iy/ | 工作力 /w er 1 r k - f ao **2** r s/ | 語音服務電話集在子應力音節的母音後放置應力 |

### <a name="english-vowels"></a>英語母音

| `sapi` | `ipa` | 範例 1     | 範例 2 | 範例 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| 伊伊     | `i`   | **ea**t       | f**ee l**  | 瓦爾**伊**                  |
| Ih     | `ɪ`   | **i**f        | f**我**  |                             |
| 伊伊     | `eɪ`  | **a**te       | g**a**te  | d**ay**                     |
| 嗯     | `ɛ`   | **e**非常     | p**e**t   | m**eh** （最後罕見單詞） |
| ae     | `æ`   | **一個**c-ctive    | c**a**t   | n**啊**（最後是稀有的單詞） |
| aa     | `ɑ`   | **o**bstinate | **p o**py | r**啊**（最後是稀有的單詞） |
| ao     | `ɔ`   | **o**範圍    | c**au**se | Ut**啊**                    |
| 呃     | `ʊ`   | b**oo**k      |           |                             |
| ow     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| 微波     | `u`   | **U**貝爾      | b**oo**st | t**oo**                     |
| 啊     | `ʌ`   | **ncle**     | c**u**t   |                             |
| 哎     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| aw     | `aʊ`  | **ou**t       | **s ou**th | c**ow**                     |
| oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **余**馬      | **h u**人 | f**ew**                     |
| 斧頭     | `ə`   | **去**       | wom**a**n | **是一個**                    |

### <a name="english-r-colored-vowels"></a>英語 R 色母音

| `sapi` | `ipa` | 範例 1    | 範例 2      | 範例 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **耳朵**     | t**ir**amisu   | n**耳**   |
| eh r   | `ɛɹ`  | **飛機** | 應用程式**ar** | sc**ar**e  |
| 呃 r   | `ʊɹ`  |              |                | c**您的**e   |
| ay r   | `aɪɹ` | **艾裡**土地  | f**ir**eplace  | 喬 **·奧伊爾**  |
| aw r   | `aʊɹ` | **小時**s    | p**欠債**ful   | **是我們的**   |
| ao r   | `ɔɹ`  | **或**安吉   | m**或**al      | **槳**   |
| aa r   | `ɑɹ`  | **阿爾**蒂斯特   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **耳朵**    | b**ir**d       | **f 您的**    |
| ax r   | `ɚ`   |              | 所有**er**gy    | 蘇普**呃** |

### <a name="english-semivowels"></a>英語半音

| `sapi` | `ipa` | 範例 1           | 範例 2  | 範例 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith，** ** | 阿爾**w**ays |           |
| y      | `j`   | **y**ard， f**e**w   | 上**我**上  |           |

### <a name="english-aspirated-oral-stops"></a>英語吸氣口服停止

| `sapi` | `ipa` | 範例 1 | 範例 2   | 範例 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**ut   | 哈**普**恩  | fla**p**   |
| b      | `b`   | **b**ig   | 數位**b**er  | 克拉**b**   |
| t      | `t`   | **t**alk  | 卡皮**特**阿爾 | sough**t** |
| d      | `d`   | **d**ig   | 運行**d**om  | ro**d**    |
| K      | `k`   | **c**ut   | 斯拉**克**爾 | 伊拉**克**   |
| g      | `g`   | **g**o    | a**g**o     | 德拉**g**   |

### <a name="english-nasal-stops"></a>英國鼻停止

| `sapi` | `ipa` | 範例 1        | 範例 2  | 範例 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **我在**，粉碎   | 卡**m**時代 | 羅**姆**    |
| n      | `n`   | **n**o，**s n**ow | t**n**t   | 雞**鳴 n** |
| 議員     | `ŋ`   |                  | 利**n**k   | s**ing**    |

### <a name="english-fricatives"></a>英語裝飾

| `sapi` | `ipa` | 範例 1   | 範例 2        | 範例 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**ork    | le**f**t         | 哈爾**夫**   |
| v      | `v`   | **v**阿盧   | e**v**ent        | lo**v**e   |
| th     | `θ`   | **th**在    | empa**th y**      | 蒙**特**  |
| Dh     | `ð`   | **th**    | 莫爾**th**       | 斯莫**th** |
| s      | `s`   | **s**     | ri**s**k         | 事實**s**  |
| z      | `z`   | **z**ap     | 布**s**y         | 兒童**s**   |
| sh     | `ʃ`   | **sh** e    | 縮寫**ti** | 魯**什**   |
| zh     | `ʒ`   | **J**acques | 認罪**尿**     | 加拉**g**e |
| h      | `h`   | **h**埃爾普    | en**h**安切      | **a-h**a！  |

### <a name="english-affricates"></a>英語親和力

| `sapi` | `ipa` | 範例 1 | 範例 2    | 範例 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**in  | 福**圖**烏雷   | 阿塔**奇** |
| Jh     | `dʒ`  | **j**oy   | 奧裡**g**inal | 猩**猩 g**e |

### <a name="english-approximants"></a>英語近似值

| `sapi` | `ipa` | 範例 1          | 範例 2  | 範例 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id， g**l**廣告  | pa**l**王牌 | 奇**爾** |
| r      | `ɹ`   | **r**ed，**b r ing** | bo**rr**ow | 塔**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>法國超段

然而，語音服務電話集在強調音節的母音之後造成壓力;`fr-FR`語音服務電話集不支援 IPA 子壓力 ""。""。""。""。""。""。""。""。""。""。""。""。""。""。""。""。""。""。"表示""。"表示""。"表示" 如果需要 IPA 子應力，應直接使用 IPA。

### <a name="french-vowels"></a>法語母音

| `sapi` | `ipa` | 範例 1     | 範例 2       | 範例 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **一個**rbre     | p**a**tte       | ir**a**   |
| aa     | `ɑ`   |               | p **=** te        | p**a**s   |
| aa =   | `ɑ̃`  | **恩**幻想    | enf**en**t      | t**em**ps |
| 斧頭     | `ə`   |               | p**e**tite      | l**e**    |
| 嗯     | `ɛ`   | **e**lle      | **p e**rdu       | _t**ai**t |
| 歐盟     | `ø`   | **_u**fs      | cr**eu**ser     | qu**eu**  |
| 伊伊     | `e`   | _mu           | 克雷丁          | *       |
| 嗯 |   | `ɛ̃`  | **im**波特 | p**ein**ture    | 墊**在** |
| 伊伊     | `i`   | **i**dée      | 寵物**i**te      | **我**   |
| Oe     | `œ`   | **_u**f       | p**eu**r        |           |
| 哦     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| 哦 ~   | `ɔ̃`  | **在**澤      | r 在 deur**上**     | b**上**   |
| ow     | `o`   | **奧**迪特爾  | b**au**政變    | p *****    |
| oe |   | `œ̃ ` | **聯合國**        | l**un**di       | br**un**  |
| 微波     | `u`   | **烏**·拉格   | intr**ou**可 | **ou**    |
| 烏伊     | `y`   | **u**ne       | p**u**尼       | _l**u**   |

### <a name="french-consonants"></a>法語輔音

| `sapi` | `ipa` | 範例 1   | 範例 2     | 範例 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**_ te    | 哈 **·布**·伊勒   | ro**b**e                         |
| d      | `d`   | **d**ire    | 羅恩 **·德**·eur   | chau**d**e                       |
| f      | `f`   | **f**emme   | 蘇**伊夫**·ix埃   | 博**f**                          |
| g      | `g`   | **g**奧切  | ***g**阿萊     | 巴**古**e                        |
| 議員     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)**停車場** |
| Hy     | `ɥ`   | h**u**ile   | **n u**ire     |                                  |
| K      | `k`   | **c**藝術   | ***c**艾勒   | 是**c**                          |
| l      | `l`   | **l**ong    | l**l**ire     | 巴**l**                          |
| m      | `m`   | **姆**阿達姆  | 艾**姆**呃     | po**mm**e                        |
| n      | `n`   | **n**ous    | te**n**ir     | bo**nn**e                        |
| 新澤西州     | `ɲ`   |             |               | 佩 **·格恩**                       |
| p      | `p`   | **p**atte   | re**p**作為     | ca**p**                          |
| r      | `ʁ`   | **r**在     | 查 **·伊**奧特   | 森蒂**r**                       |
| s      | `s`   | **是我們的**  | **ss**ez     | pa**ss e**                        |
| sh     | `ʃ`   | **ch**安特 | 馬**奇**因   | 波**奇**e                        |
| t      | `t`   | **t**_ te    | **\t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | 在**v**輸入  | ré**v**e                         |
| w      | `w`   | **ou**i     | f**ou**ine    |                                  |
| y      | `j`   | **y**od     | p**i**_tiner  | 馬賽 **·伊耶**                    |
| z      | `z`   | _z _ro   | 萊 **·斯**翁納 | ro**s**e                         |
| zh     | `ʒ`   | **j**阿爾丁  | 男子**g**er    | 皮埃**格**                        |
|        | `n‿`  |             |               | **n 阿爾**佈雷                     |
|        | `t‿`  |             |               | 泉**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *僅適用于一些外來詞。*

> [!TIP]
> 語音`fr-FR`服務電話集不支援以下法語，`n‿`和`t‿`。 `z‿` 如果需要，應考慮直接使用 IPA。

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>德國超段

| 示例 1（為輔音啟動，母音的單詞首字母） | 示例 2（輔音的交音，母音的單詞中音核） | 示例 3（輔音 Coda，母音單詞"最終"） | 註解 |
|--|--|--|--|
| 安德斯 /a **1** n - d ax r s/ | 多普列克澤琴 /m uh l - t iy - p l iy - k a - ts y ow **1** ns - ts ay - c n/ | 生物 /b iy - ow - l ow - g iy **1**/ | 語音服務電話集在應力音節的母音後放置壓力 |
| Allgemeinwissen / **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgssfirma /a 1 p - f a l - = eh n t - z 哦**2** ax r - g ng s - f ih ax r - m a/ | 電腦學 /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | 語音服務電話集在子應力音節的母音後放置應力 |

### <a name="german-vowels"></a>德語母音

| `sapi` | `ipa`     | 範例 1                             | 範例 2     | 範例 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **一個**啤酒                              | 馬斯特**a**b   | 舍姆**a**                         |
| a      | `a`       | **一個**失敗                            | B**a**ch      | 阿加思**a**                         |
| 哦     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| 嗯：    | `ɛː`      | **·** 赫利希基特                       | B **+** r       | [<sup>1</sup>](#de-v-1)法西 **·艾** |
| 嗯     | `ɛ`       | **·** 恩德恩                            | Proz**e**nt   | 艾米格達爾 **·艾**                      |
| 斧頭     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | 弗拉格**e**                          |
| 伊伊     | `iː`      | **我**跑了                              | abb**ie gt**   | 相對主義者**ie**            |
| Ih     | `ɪ`       | **我**年                            | **s i**ngen    | 木材**y**                          |
| 歐盟     | `øː`      | ***** 森                              | abl **+** 斯滕  | 瑪律姆 *****                          |
| ow     | `o`, `oː` | **o**hne                              | 巴爾克**o**n    | 特雷普特**ow**                        |
| Oe     | `œ`       | ***** ffnung                           | 貝夫 **·** 勒德恩 |                                    |
| 伊伊     | `e`, `eː` | **E**貝爾哈德                          | abf**e**gt    | b                                  |
| 微波     | `uː`      | **U**do                               | H**u**t       | Akk**u**                           |
| 呃     | `ʊ`       | **尤**恩特謝德斯                     | b**u**nt      |                                    |
| Ue     | `yː`      | **•** 貝爾穆特                           | pfl **@** gt    | 男士 **|**                           |
| 烏伊     | `ʏ`       | **•** ppig                             | S**y**莖    |                                    |

<a id="de-v-1"></a>
**1** *僅在外國血統的詞語中，例如：法西**艾**。*<br>
<a id="de-v-2"></a>
**2** *字內僅在外來詞中，如**點**綴。可音節最初在：'v**e**rstauen。*

### <a name="german-diphthong"></a>德國迪普通

| `sapi` | `ipa`       | 範例 1    | 範例 2          | 範例 3 |
|--------|-------------|--------------|--------------------|-----------|
| 哎     | `ai`        | **伊·** 恩薩姆   | 恩塔本**吉格** | 阿卜特 **·伊** |
| aw     | `au`        | **奧**·奧·埃恩    | 阿巴**奧**斯特        | 聖**奧**  |
| oy     | `ɔy`, `ɔʏ̯` | **歐**·菲裡 | tr **_u**mt         | 施**尤** |

### <a name="german-semivowels"></a>德國半母音

| `sapi` | `ipa` | 範例 1 | 範例 2    | 範例 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | 阿比恩德 **·埃爾**恩 | 鎖**er** |

### <a name="german-consonants"></a>德國輔音

| `sapi` | `ipa` | 範例 1 | 範例 2 | 範例 3 |
|--|--|--|--|--|
| b | `b` | **B**安克 |  | [<sup>1</sup>](#de-c-1)普**b** |  |
| c | `ç` | **查韋斯** | 梅格利**赫**斯特 | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **德**安肯 | [<sup>3</sup>](#de-c-3)倫**d**l | [<sup>4</sup>](#de-c-4)克勞**德** |  |
| Jh | `ʤ` | **J**eff | 傑馬納**g**t | [<sup>5</sup>](#de-c-5)陳**g**e |
| f | `f` | **弗**·阿赫特道爾 | 憤怒**ff**slustig | 阿布布魯赫雷**夫** |  |
| g | `g` | **g**ut |  | [<sup>6</sup>](#de-c-6)格雷**g** |  |
| h | `h` | **H·** 烏桑博 |  |  |  |
| y | `j` | **J**od | 雷克特**我**上 | 胡**伊** |  |
| K | `k` | **K**奧馬 | 阿斯佩**k**t | 弗萊克**克** |  |
| l | `l` | **l**au | \hne**l**n | 祖維**l** |  |
| m | `m` | **M**ut | **一個 m**t | 勒姆**m** |  |
| n | `n` | **n**un | **n**d | Huh**n** |  |
| 議員 | `ŋ` | [<sup>7</sup>](#de-c-7)**吳**恩 | 施瓦**恩克** | R**ing** |  |
| p | `p` | **P**阿特納 | 阿布魯**p**t | 提**普** |  |
| 普夫 | `pf` | **Pf**erd | 水壩**pf**t | 到**pf** |  |
| r | `ʀ`, `r`, `ʁ` | **瑞**塞 | 克努**rr**t | 哈**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**塔卡托 | bi**s**t | 米**斯** |  |
| sh | `ʃ` | **舒**萊 | mi**sch**t | 拉皮**施** |  |
| t | `t` | **T**raum | S**t**raée | Mu**t** |  |
| ts | `ts` | **Z**ug | Ar**z**t | 維特**茲** |  |
| ch | `tʃ` | **奇**·埃欽 | 阿夫格普**茨**特 | 聯邦**tsch** |  |
| v | `v` | **w**因肯 | Q**u**alle | [<sup>9</sup>](#de-c-9)格**勞烏** |  |
| x | [<sup>10</sup>](#de-c-10)`x`，[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12)巴**赫**·拉赫 | 馬**赫**特·梅格利**赫**斯特 | 施馬**奇**'i**ch** |
| z | `z` | **向上** |  |  |  |
| zh | `ʒ` | **G**enre | **B·雷**津斯基 | 埃德維**g**e |

<a id="de-c-1"></a>
**1** *僅在外國血統的詞語中，例如：Pu**b**。*<br>
<a id="de-c-2"></a>
**2** *"e"和"i"之後的軟"ch"*<br>
<a id="de-c-3"></a>
**3** *僅在外國血統的詞語中，例如：Len**d**l。*<br>
<a id="de-c-4"></a>
**4** *僅在外國血統的詞語中，例如：Clau**d**e。*<br>
<a id="de-c-5"></a>
**5** *只用外國血統的詞，如：陳**g**e。*<br>
<a id="de-c-6"></a>
**6** *字終端僅在外國來源的單詞，如Gre**g**。*<br>
<a id="de-c-7"></a>
**7** *僅在外國血統的詞語中，如 **：Ng**uyen。*<br>
<a id="de-c-8"></a>
**8** *僅在外國血統的單詞中，如 **：S**taccato。*<br>
<a id="de-c-9"></a>
**9** *只用外國血統的詞語，如：Gr**oo**ve。*<br>
<a id="de-c-10"></a>
**10** *IPA`x`是所有非正面母音（a，aa，哦，哦，嗯，uw和diphthong aw）之後，是一個硬的"ch"。*<br>
<a id="de-c-11"></a>
**11** *IPA`ç`是一個軟的'ch'後前母音 （ih， iy， eh， ae， uy， ue， eu 也在 diphthongs ay， oy） 和輔音*<br>
<a id="de-c-12"></a>
**12** *單詞最初隻在外來詞中，例如 **：J**uan。音節最初也用諸如：巴**切**拉赫這樣的詞。*<br>

### <a name="german-oral-consonants"></a>德國口服輔音

| `sapi` | `ipa` | 範例 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | 比奇利奇 /b 斧 - = 1 x t - l ih c/ |

> [!NOTE]
> 我們需要在兩個不同的母音之間\]添加一個_gs電話，除了兩個母音是一個真正的二母音。 這個口腔輔音是一個腺停止，欲瞭解更多資訊，見<a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">格洛塔停止。 <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>西班牙文母音

| `sapi` | `ipa` | 範例 1    | 範例 2     | 範例 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **一個**lto     | c**ntar**    | cas**a**     |
| i      | `i`   | **伊**·貝裡卡  | av**i**spa    | 稅**i**     |
| e      | `e`   | **埃**勒凡特 | 在**e**nto    | 埃萊凡特**e** |
| o      | `o`   | **o**卡索    | 恩克 **·奧**恩特拉 | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | 胡安爾**u**   |

### <a name="spanish-consonants"></a>西班牙輔音

| `sapi` | `ipa`      | 範例 1  | 範例 2      | 範例 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**奧巴布 |                | am**b**        |
|        | `β`        |            | 寶**b**ab     | 保巴**b**     |
| ch     | `tʃ`       | **查韋斯** | co**ch**e      | 馬拉克**奇** |
| d      | `d`        | **d**edo   |                | 波特蘭**d**   |
|        | `ð`        |            | **德·德**奧       | 韋爾**達 d**     |
| f      | `f`        | **f**_cil  | ele**f**ante   | 普**f**        |
| g      | `g`        | **g**安加  |                | 多平**g**     |
|        | `ɣ`        |            | a**g**ua       | 圖加爾**g**     |
| j      | `j`        | **i**odo   | 卡爾 **·伊**·恩特   | re**y**        |
| Jj     | `j.j` `jj` |            | vi**ll**a      |                |
| K      | `k`        | **c**奧切  | 波**c**a       | 蒂蒂尼**c**    |
| l      | `l`        | **l**_piz  | a**l**a        | 線**l**     |
| ll     | `ʎ`        | **ll**ave  | 德薩羅 **·洛奧** |                |
| m      | `m`        | **m**順序 | 一**個 m**ar       | 阿爾布**姆**      |
| n      | `n`        | **n**ada   | ce**n**a       | 拉特內**n**      |
| 新澤西州     | `ɲ`        | **_aía**   | 阿拉 **·阿**佐    |                |
| p      | `p`        | **p**奧卡   | 到**p**o       | 斯托**普**       |
| r      | `ɾ`        |            | ca**r**a       | 阿布裡**r**      |
| Rr     | `r`        | **r**adio  | co**rr**e      | 普**rr**       |
| s      | `s`        | **s**aco   | va**s**o       | 佩洛**s**      |
| t      | `t`        | **t**Oldo  | a**t**ar       | disque**t**    |
| th     | `θ`        | **茲**埃布拉  | a**z**ul       | 萊皮**茲**      |
| w      | `w`        | **h u**eso  | ag**u**a       | 瓜**烏**       |
| x      | `x`        | **j**奧塔   | a**j**o        | 雷洛**j**      |

> [!TIP]
> 語音`es-ES`服務電話集不支援以下西班牙文 IPA`β`和`ð`。 `ɣ` 如果需要，應考慮直接使用 IPA。

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

的`zh-CN`語音服務電話集基於本機電話<a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">拼音<span class="docon docon-navigate-external x-hidden-focus"></span></a>集。

### <a name="tone"></a>色調

| 拼音 | `sapi` | 字元示例 |
|-------------|--------|-------------------|
| mé          | ma 1  | ·                 |
| mé          | ma 2  | •                 |
| mmm          | 馬 3  | *                 |
| mé          | 馬 4  | ·                 |
| ma          | 馬 5  | ·                 |

#### <a name="example"></a>範例

| 字元 | 語音服務                |
|-----------|-------------------------------|
| •      | zu 3 - 志 1 - 關 1 - xi 5 |
| 累进        | 雷 3 -津 4                 |
| ·       | Xi 1 - Zhai 2 - 翔 4      |

# <a name="zh-tw"></a>[日-TW](#tab/zh-TW)

語音`zh-TW`服務電話集基於本機電話<a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo<span class="docon docon-navigate-external x-hidden-focus"></span></a>集。

### <a name="tone"></a>色調

| 語音服務音 | 波波莫沃語調 | 示例（單詞） | 語音服務電話 | 博波莫沃 | 拼音 （*） |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ·                   | empty         | ·              | ·                   | ·       | 日恩        |
| ·                   | ·             | ·              | ·                   | ·      | ché         |
| ·                   | ·             | *              | ·                   | ·      | d=          |
| ·                   | ·             | ·              | ·                   | ·      | 旺昂        |
| ˙                   | ˙             | ·             | [ ]               | [ ]  | y 翁子    |

#### <a name="example"></a>範例

| 字元 | `sapi`   |
|-----------|----------|
| *         | ·      |
| ·        | ·   |
| ·        | · |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

的`ja-JP`語音服務電話集基於本機電話<a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana<span class="docon docon-navigate-external x-hidden-focus"></span></a>集。

### <a name="stress"></a>應力

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`主應力 |
| `+`    | `ˌ`子應力  |

#### <a name="example"></a>範例

| 字元 | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| ·        | *    | 戈瓦塞吉   |
| ·       | ショュ'ウ?ャ | [joj_w_ja |
| ·       | [ ]  | 薩吉特基卡 |

***