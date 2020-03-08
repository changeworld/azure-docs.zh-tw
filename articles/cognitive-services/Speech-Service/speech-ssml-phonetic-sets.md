---
title: 語音拼音集合-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解語音服務拼音字母如何對應至國際語音字母（.IPA），以及使用哪一組的時機。
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675334"
---
# <a name="speech-service-phonetic-sets"></a>語音服務拼音設定

語音服務會定義以七種語言組成的語音字母（簡稱「電話組」）;`en-US`、`fr-FR`、`de-DE`、`es-ES`、`ja-JP`、`zh-CN`和 `zh-TW`。 語音服務電話集通常會對應到<a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">國際語音字母（.ipa） <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。 語音服務電話組會與[語音合成標記語言（SSML）](speech-synthesis-markup.md)搭配使用，做為文字轉換語音服務供應專案的一部分。 在本文中，您將瞭解這些電話集合的對應方式，以及使用哪一個電話組的時機。

# <a name="en-us"></a>[en-us](#tab/en-US)

### <a name="english-suprasegmentals"></a>英文 suprasegmentals

| 範例1（適用于輔音的萌芽，為母音的單字初始） | 範例2（適用于輔音的 Intervocalic、適用于母音的單字字詞系統核心） | 範例3（適用于輔音的 Coda，適用于母音的單字 final） | 註解 |
|--|--|--|--|
| 漢堡一起享用/b er **1** r-g ax r/ | falafel/f ax-l aa **1** -f ax l/ | 吉他/g ih-t aa **1** r/ | 語音服務電話集合在高負荷音節的母音之後放置壓力 |
| 不適當/ih **2** -n aa-p ax r-t uw 1 n/ | dissimilarity/d ih-s ih **2**-m ax-l eh 1-r ax-t iy/ | 工作人力/w er 1 r k-f ao **2** r s/ | 語音服務電話設定將壓力放在子功能音節的母音之後 |

### <a name="english-vowels"></a>英文母音

| `sapi` | `ipa` | 範例 1     | 範例 2 | 範例 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **ea**t       | f**ee**l  | vall**安永**                  |
| ih     | `ɪ`   | **i**f        | f**i**ll  |                             |
| 安永     | `eɪ`  | **a**te       | g**a**te  | d**ay**                     |
| 吧     | `ɛ`   | **e**非常     | p**e**t   | m**eh** （罕見字 finally） |
| ae     | `æ`   | **現用**    | c**a**t   | n**ah** （罕見字 finally） |
| aa     | `ɑ`   | **o**bstinate | p**o**ppy | r**ah** （罕見字 finally） |
| ao     | `ɔ`   | **o**範圍    | c**au**se | 未通過的**ah**                    |
| 噢     | `ʊ`   | b**oo**k      |           |                             |
| 允許     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| uw     | `u`   | **U**ber      | b**oo**st | t**oo**                     |
| 啊     | `ʌ`   | **u**ncle     | c**u**t   |                             |
| ay     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| aw     | `aʊ`  | **ou**t       | s**ou**第一 | c**允許**                     |
| oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **Yu**ma      | h**u**man | f**new**                     |
| 限於     | `ə`   | **a**go       | wom**n** | 是                    |

### <a name="english-r-colored-vowels"></a>英文 R-彩色母音

| `sapi` | `ipa` | 範例 1    | 範例 2      | 範例 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **ear**     | t**ir**amisu   | n**耳**   |
| eh r   | `ɛɹ`  | **空氣**平面 | 應用程式**ar**ently | sc**ar**e  |
| 糟糕 r   | `ʊɹ`  |              |                | c**您的**e   |
| ay r   | `aɪɹ` | **即刻到期 i**土地  | f**ir**取代  | ch**oir**  |
| aw r   | `aʊɹ` | **小時**s    | p**ower**規格   | s   |
| ao r   | `ɔɹ`  | **或**範圍   | m**或**al      | s**oar**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **ear**第一    | b**ir**d       | f    |
| ax r   | `ɚ`   |              | 所有的**er**gy    | 抑制**er** |

### <a name="english-semivowels"></a>英文 Semivowels

| `sapi` | `ipa` | 範例 1           | 範例 2  | 範例 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**，s**ue**de | al**w**ays |           |
| y      | `j`   | **y**ard，f**e**w   | 開啟**時**  |           |

### <a name="english-aspirated-oral-stops"></a>英文 aspirated 口頭停止

| `sapi` | `ipa` | 範例 1 | 範例 2   | 範例 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**   | ha**pp**en  | fla**p**   |
| b      | `b`   | **b**ig   | num**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| d      | `d`   | **d**ig   | 已執行**d**om  | ro**d**    |
| k      | `k`   | **c**未通過   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | **g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>英文 Nasal 停止

| `sapi` | `ipa` | 範例 1        | 範例 2  | 範例 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**at、smash   | ca**m**紀元 | roo**m**    |
| n      | `n`   | **n**o、s**n**允許 | te**n**t   | chicke**n** |
| ng     | `ŋ`   |                  | li**n**k   | s**ing**    |

### <a name="english-fricatives"></a>英文 fricatives

| `sapi` | `ipa` | 範例 1   | 範例 2        | 範例 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**ork    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | lo**v**e   |
| th     | `θ`   | **第**    | empa**第**y      | **第**一週一  |
| 不對     | `ð`   | **第**en    | **第**一次 er       | smoo**th** |
| s      | `s`   | **s**it     | ri**s**k         | 事實**s**  |
| z      | `z`   | **z**ap     | bu**s**y         | 小孩   |
| sh     | `ʃ`   | **sh** e    | abbrevia**ti**開啟于 | ru**sh**   |
| zh     | `ʒ`   | **J**acques | **s**u     | gara**g**e |
| h      | `h`   | **h**elp    | en**h**ance      | a-**h**a！  |

### <a name="english-affricates"></a>英文 affricates

| `sapi` | `ipa` | 範例 1 | 範例 2    | 範例 3  |
|--------|-------|-----------|--------------|------------|
| 頻道     | `tʃ`  | 中的**ch**  | fu**t**u   | atta**ch** |
| jh     | `dʒ`  | **j**oy   | ori**g**inal | oran**g**e |

### <a name="english-approximants"></a>英文 approximants

| `sapi` | `ipa` | 範例 1          | 範例 2  | 範例 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**識別碼、g**l**ad  | pa**l**ace | 卡**ll** |
| r      | `ɹ`   | **r**ed、b**r**ing | bo**rr**允許 | ta**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>法文 suprasegmentals

不過，語音服務電話集會將壓力放在大量音節的母音之後;`fr-FR` 語音服務電話集不支援 .IPA substress ' ˌ '。 如果需要 .IPA substress，您應該直接使用 .IPA。

### <a name="french-vowels"></a>法文母音

| `sapi` | `ipa` | 範例 1     | 範例 2       | 範例 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **rbre**     | p**a**tte       | ir**a**   |
| aa     | `ɑ`   |               | p **-** te        | p**a**s   |
| aa ~   | `ɑ̃`  | **en**fant    | enf**en**t      | t**em**ps |
| 限於     | `ə`   |               | p**e**tite      | l**e**    |
| 吧     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| eu     | `ø`   | **œu**fs      | cr**eu**ser     | qu**eu**  |
| 安永     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im**portant | p**ein**true    | **中的**材料 |
| iy     | `i`   | **我**dée      | 寵物**我**      | 我**是**   |
| oe     | `œ`   | **œu**f       | p**eu**r        |           |
| 糟糕     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| 哦 ~   | `ɔ̃`  | **on**ze      | deur**上的**r     | b**開啟**   |
| 允許     | `o`   | **au**diteur  | b**eau**coup    | p**ô**    |
| oe ~   | `œ̃ ` | **取消**        | l**un**di       | br**un**  |
| uw     | `u`   | **ou**trage   | intr**ou**vable | **/ou**    |
| uy     | `y`   | **u**ne       | p**u**nir       | él**u**   |

### <a name="french-consonants"></a>法文子音

| `sapi` | `ipa` | 範例 1   | 範例 2     | 範例 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**ille   | ro**b**e                         |
| d      | `d`   | **d**即刻到期 i    | ron**d**歐元   | chau**d**e                       |
| f      | `f`   | **f**emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g**auche  | 日**g**ale     | ba**gu**e                        |
| ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)公園 |
| hy     | `ɥ`   | h**u**ile   | n**u**即刻到期 i     |                                  |
| k      | `k`   | **c**arte   | 日**c**aille   | 是**c**                          |
| l      | `l`   | **l**ong    | 日**l**即刻到期 i     | ba**l**                          |
| m      | `m`   | **m**adame  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **n**個 ou    | te**n**ir     | bo**nn**e                        |
| 新澤西     | `ɲ`   |             |               | pei**gn**e                       |
| p      | `p`   | **p**atte   | 將**p**重新做為     | ca**p**                          |
| r      | `ʁ`   | **r**于     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir  | **ss**ez     | pa**ss**e                        |
| sh     | `ʃ`   | **ch**anter | ma**ch**ine   | po**ch**e                        |
| t      | `t`   | **t**ête    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | 在**v**enter 中  | rê**v**e                         |
| w      | `w`   | **ou**i     | f**ou**ine    |                                  |
| y      | `j`   | **y**od     | p**i**étiner  | Marse**ille**                    |
| z      | `z`   | \* * z * * éro   | rai**s**onner | ro**s**e                         |
| zh     | `ʒ`   | **j**ardin  | man**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | quan**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *僅適用于部分外部文字。*

> [!TIP]
> `fr-FR` 語音服務電話組不支援下列法文 liasions、`n‿`、`t‿`和 `z‿`。 如果需要，您應該考慮直接使用 .IPA。

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>德文 suprasegmentals

| 範例1（適用于輔音的萌芽，為母音的單字初始） | 範例2（適用于輔音的 Intervocalic、適用于母音的單字字詞系統核心） | 範例3（適用于輔音的 Coda，適用于母音的單字 final） | 註解 |
|--|--|--|--|
| anders/a **1** n-1 ax r s/ | Multiplikationszeichen/m 噢 l-t iy-p l iy-k a-ts y 允許**1** n s-ts ay-c n/ | Biologie/b iy-允許-l 允許-g iy **1**/ | 語音服務電話集合在高負荷音節的母音之後放置壓力 |
| Allgemeinwissen/a **2** l-g ax-m ay 1 n-v ih-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z 哦**2** ax r-g 啊 ng s-f ih ax r-m a/ | Computertomographie/k 哦 m-p y uw 1-t ax r-t 允許-m 允許-g r a-f iy **2**/ | 語音服務電話設定將壓力放在子功能音節的母音之後 |

### <a name="german-vowels"></a>德文母音

| `sapi` | `ipa`     | 範例 1                             | 範例 2     | 範例 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **Ber**                              | Maßst**a**b   | 架構**a**                         |
| a      | `a`       | **Bfall**                            | B**a**ch      | Agath**a**                         |
| 糟糕     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| 吧    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1</sup>](#de-v-1)Fasci**ae** |
| 吧     | `ɛ`       | **ä**ndern                            | Proz**e**nt   | Amygdal**ae**                      |
| 限於     | `ə`       | [<sup>2</sup>](#de-v-2)' v**e**rstauen | Aach**e**n    | 片段**e**                          |
| iy     | `iː`      | **我**執行                              | abb**ie**gt   | Relativitätstheor**ie**            |
| ih     | `ɪ`       | **我**nnung                            | s**i**ngen    | 木材**y**                          |
| eu     | `øː`      | **Ö**sen                              | abl**ö**sten  | Malm**ö**                          |
| 允許     | `o`, `oː` | **o**hne                              | Balk**o**n    | Trept**允許**                        |
| oe     | `œ`       | **Ö**ffnung                           | bef**ö**rdern |                                    |
| 安永     | `e`, `eː` | **E**berhard                          | .abf**e**gt    | b                                  |
| uw     | `uː`      | **U**do                               | H**u**t       | Akk**u**                           |
| 噢     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| 專色     | `yː`      | **Ü**bermut                           | default.pfl**ü**gt    | 男性**ü**                           |
| uy     | `ʏ`       | **ü**ppig                             | S**y**莖    |                                    |

<a id="de-v-1"></a> *只在外部來源的單字中 
1，例如： Fasci**ae**。*<br>
<a id="de-v-2"></a>
**2** *個單字 intially，僅限外部來源的單字，例如 ppointment。音節-一開始： ' v**e**rstauen。*

### <a name="german-diphthong"></a>德文 diphthong

| `sapi` | `ipa`       | 範例 1    | 範例 2          | 範例 3 |
|--------|-------------|--------------|--------------------|-----------|
| ay     | `ai`        | **ei**nsam   | Unabhängigk**ei**t | Abt**ei** |
| aw     | `au`        | **au**ßen    | abb**au**st        | St**au**  |
| oy     | `ɔy`, `ɔʏ̯` | **Eu**phorie | tr**äu**mt         | .sch**eu** |

### <a name="german-semivowels"></a>德文 semivowels

| `sapi` | `ipa` | 範例 1 | 範例 2    | 範例 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd**er**n | 鎖定**er** |

### <a name="german-consonants"></a>德文子音

| `sapi` | `ipa` | 範例 1 | 範例 2 | 範例 3 |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1</sup>](#de-c-1)Pu**b** |  |
| c | `ç` | **Ch**emie | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3</sup>](#de-c-3)Len**d**l | [<sup>4</sup>](#de-c-4)Clau**d**e |  |
| jh | `ʤ` | **J**eff | gemana**g**t | [<sup>5</sup>](#de-c-5)Chan**g**e |
| f | `f` | **F**ahrtdauer | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g** |  | [<sup>6</sup>](#de-c-6)Gre**g** |  |
| h | `h` | **H**ausanbau |  |  |  |
| y | `j` | **J**od | Reakt**i** | hu**i** |  |
| k | `k` | **K**oma-uri | Aspe**k**t | Flec**k** |  |
| l | `l` | **l**au | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**個 | **M**t | Leh**m** |  |
| n | `n` | **n**取消 | u**n**d | 那是**n** |  |
| ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng**uyen | Schwa**nk.bin** | R**ing** |  |
| p | `p` | **P**夥伴 | abru**p**t | Ti**p** |  |
| pf | `pf` | **Pf**erd | dam**pf**t | 至**pf** |  |
| r | `ʀ`、`r`、`ʁ` | **R**eise | knu**rr**t | Haa**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| sh | `ʃ` | **.Sch**規則 u) | mi **.sch**t | lappi **.sch** |  |
| t | `t` | **T**raum | S**t**raße | Mu**t** |  |
| ts | `ts` | **Z**ug | Ar**z**t | Wit**z** |  |
| 頻道 | `tʃ` | **Tsch**echien | aufgepu**tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | 問**u**全部 | [<sup>9</sup>](#de-c-9)Gr**oo**ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`，[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12)Ba**ch**erach | Ma**ch**t mögli**ch**st | 結構**ch** ' i**ch** |
| z | `z` | **s**uper |  |  |  |
| zh | `ʒ` | **G**enre | B**重新**ezinski | Edvi**g**e |

<a id="de-c-1"></a> *只在外部來源的單字中 
1，例如： Pu**b**。*<br>
<a id="de-c-2"></a> *在 "e" 和 "i" 之後 
2 個軟性 "ch"*<br>
<a id="de-c-3"></a> *只有在外部來源的單字中 
3，例如： Len**d**l。*<br>
<a id="de-c-4"></a> *僅在外部來源的單字中 
4，例如： Clau**d**e。*<br>
<a id="de-c-5"></a>*只有外部來源的單字（例如： Chan**g**e）才*
**5** 。<br>
<a id="de-c-6"></a>
**6** *單字-僅 terminally 在外部來源的單字，例如 Gre**g**。*<br>
<a id="de-c-7"></a> *僅在外部來源的單字中 
7，例如： **Ng**uyen。*<br>
<a id="de-c-8"></a>
**8** *僅適用于外部來源的單字，例如： **S**taccato。*<br>
<a id="de-c-9"></a>
**9** *僅用於外部來源的單字，例如： Gr**oo**ve。*<br>
<a id="de-c-10"></a>
**10** *.ipa `x` 在所有非 front 母音（a、aa、哦、允許、啊、uw 和 diphthong aw）之後，都是硬式*的「ch」。<br>
<a id="de-c-11"></a>
**11** *.ipa `ç` 是前母音後面的「ch」軟性（ih、iy、eh、ae、uy、ue、oe、歐盟也在 diphthongs ay、oy）和子音*<br>
<a id="de-c-12"></a>
**12** *單字-一開始只會在外部來源的單字中，例如： **J**uan。音節-一開始也會在像是： Ba**ch**erach 的文字中。*<br>

### <a name="german-oral-consonants"></a>德文口頭子音

| `sapi` | `ipa` | 範例 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b ax-^ a 1 x t-l ih c/ |

> [!NOTE]
> 我們需要在兩個不同的母音之間新增 [gs\] 電話，但這兩個母音是一種正版 diphthong。 這個口頭輔音是一種喉塞音符。如需詳細資訊，請參閱 <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">的喉塞音符 <span class="docon docon-navigate-external x-hidden-focus"></a></a>。

# <a name="es-es"></a>[es](#tab/es-ES)

### <a name="spanish-vowels"></a>西班牙文母音

| `sapi` | `ipa` | 範例 1    | 範例 2     | 範例 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **lto**     | c**a**ntar    | ca**a**     |
| i      | `i`   | **我**bérica  | av**i**spa    | 稅金**i**     |
| e      | `e`   | **e**lefante | 在**e**n    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>西班牙文子音

| `sapi` | `ipa`      | 範例 1  | 範例 2      | 範例 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | am**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| 頻道     | `tʃ`       | **ch**eque | 共**ch**e      | Marraque**ch** |
| d      | `d`        | **d**艾鬥文   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | verda**d**     |
| f      | `f`        | **f**ácil  | ele**f**加緊   | pu**f**        |
| g      | `g`        | **g**anga  |                | dópin**g**     |
|        | `ɣ`        |            | **g**ua       | tuare**g**     |
| j      | `j`        | **我**odo   | cal**i**ente   | 重做**y**        |
| jj     | `j.j` `jj` |            | vi**ll**a      |                |
| k      | `k`        | **c**oche  | bo**c**a       | titáni**c**    |
| l      | `l`        | **l**ápiz  | a**l**a        | corde**l**     |
| ll     | `ʎ`        | **ll**  | desarro**ll**o |                |
| m      | `m`        | **m**順序 | **m**ar       | álbu**m**      |
| n      | `n`        | **n**ada   | ce**n**a       | rató**n**      |
| 新澤西     | `ɲ`        | **-** aña   | ara **-** azo    |                |
| p      | `p`        | **p**oca   | 至**p**o       | 停止**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| rr     | `r`        | **r**adio  | co**rr**e      | pu**rr**       |
| s      | `s`        | **s**aco   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | a**t**ar       | disque**t**    |
| th     | `θ`        | **z**ebra  | **z**ul       | lápi**z**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua**u**       |
| x      | `x`        | **j**ota   | **j**o        | relo**j**      |

> [!TIP]
> `es-ES` 語音服務電話組不支援下列西班牙文 .IPA、`β`、`ð`和 `ɣ`。 如果需要，您應該考慮直接使用 .IPA。

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

`zh-CN` 的語音服務電話集合是以原生電話<a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">拼音<span class="docon docon-navigate-external x-hidden-focus"></span> </a>集為基礎。

### <a name="tone"></a>色調

| 拼音音調 | `sapi` | 字元範例 |
|-------------|--------|-------------------|
| mā          | ma 1  | 媽                 |
| 則          | ma 2  | 麻                 |
| mǎ          | ma 3  | 馬                 |
| mà          | ma 4  | 罵                 |
| 碩士          | ma 5  | 嘛                 |

#### <a name="example"></a>範例

| 字元 | 語音服務                |
|-----------|-------------------------------|
| 組織關係      | zu 3-zhi 1-guan 1-xi 5 |
| 累進        | lei 3-金4                 |
| 西宅巷       | xi 1-zhai 2-xiang 4      |

# <a name="zh-tw"></a>[zh-幼圓](#tab/zh-TW)

`zh-TW` 的語音服務電話集合是以原生電話<a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">注音<span class="docon docon-navigate-external x-hidden-focus"></span> </a>集為基礎。

### <a name="tone"></a>色調

| 語音服務音調 | 注音音調 | 範例（word） | 語音服務電話 | 符號 | 拼音（拼音） |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | empty         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇㄗ̇               | 一ㄥˇㄗ̇  | yǐng zi    |

#### <a name="example"></a>範例

| 字元 | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然後        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

`ja-JP` 的語音服務電話集合是以原生電話<a href="https://en.wikipedia.org/wiki/Kana" target="_blank">假名<span class="docon docon-navigate-external x-hidden-focus"></span> </a>組為基礎。

### <a name="stress"></a>過重

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` substress  |

#### <a name="example"></a>範例

| 字元 | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | go 今天 wɯseji   |
| 擁有者       | ショュ'ウ?ャ | ɕjojɯ今天 wɯɕja |
| 最適化       | サィテキカ +  | sajitecikaˌ |

***