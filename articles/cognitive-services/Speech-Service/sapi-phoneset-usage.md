---
title: SAPI 手機設定使用方式-語音服務
titleSuffix: Azure Cognitive Services
description: 適用于 SAPI 的詳細電話使用方式檔。
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: jiajzhan
ms.openlocfilehash: c14636e46a09bf4e7528dd732548c1a09dc10f92
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228654"
---
# <a name="sapi-phone-set-usage"></a>SAPI 電話設定使用方式

Microsoft 定義了適用于七種語言（en-us、fr-fr、de、es、ja-jp、zh-CN、zh-幼圓）的 SAPI 手機組。 「SAPI 電話組」相當容易使用，特別是針對原生說話者。 例如，Ja-jp 使用假名作為電話，zh-CN 使用拼音做為電話組。 但有時候功能不夠強大，例如，fr-fr 的 SAPI 電話組無法代表法文聯絡人。 

## <a name="en-us"></a>zh-TW

| SAPI  | .IPA  | example1 （適用于輔音的萌芽，適用于母音的單字初始） | example2 （適用于輔音的 intervocalic、用於母音的單字字詞系統核心） | example3 （適用于輔音的 coda，適用于母音的單字 final） | 註解                                                     |
| ---- | ---- | ---------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ |
| SUPRASEGMENTALS|      |                                                            |                                                              |                                                         |                                                              |
| 1                    | 今天    | 漢堡一起享用/b er **1** r-g ax r/                                | falafel/f ax-l aa **1** -f ax l/                           | 吉他/g ih-t aa **1** r/                               | 在負荷過高的音節的母音之後，SAPI 手機設定 put 壓力 |
| 2                    | ˌ    | 不適當/ih **2** -n aa-p ax r-t uw 1 n/              | dissimilarity/d ih-s ih **2**-m ax-l eh 1-r ax-t iy/ | 工作人力/w er 1 r k-f ao **2** r s/                     | Substressed 音節的母音之後的 SAPI 電話集合 put 壓力 |
| -                    | 。    |                                                            |                                                              |                                                         |                                                              |
| 母音               |      |                                                            |                                                              |                                                         |                                                              |
| iy                   | i    | **ea**t                                                        | f**ee**l                                                         | vall**安永**                                                  |                                                              |
| ih                   | 怎樣    | **i**f                                                         | f**i**ll                                                         |                                                         |                                                              |
| 安永                   | eɪ   | **a**te                                                        | g**a**te                                                         | d**ay**                                                     |                                                              |
| 吧                   | ɛ    | **e**非常                                                      | p**e**t                                                          | m**eh**（罕見字 finally）                               |                                                              |
| ae                   | æ    | **現用**                                                     | c**a**t                                                          | n**ah**（罕見字 finally）                               |                                                              |
| aa                   | ɑ    | **o**bstinate                                                  | p**o**ppy                                                        | r**ah**（罕見字 finally）                               |                                                              |
| ao                   | ɔ    | **o**範圍                                                     | c**au**se                                                        | 未通過的**ah**                                                    |                                                              |
| 噢                   | ʊ    |                                                            | b**oo**k                                                         |                                                         |                                                              |
| 允許                   | oʊ   | **o**ld                                                        | cl**o**ne                                                        | g**o**                                                      |                                                              |
| uw                   | u    | **U**ber                                                       | b**oo**st                                                        | t**oo**                                                     |                                                              |
| 啊                   | ʌ    | **u**ncle                                                      | c**u**t                                                          |                                                         |                                                              |
| ay                   | aɪ   | **i**ce                                                        | b**i**te                                                         | fl**y**                                                     |                                                              |
| aw                   | aʊ   | **ou**t                                                        | s**ou**第一                                                        | c**允許**                                                     |                                                              |
| oy                   | ɔɪ   | **oi**l                                                        | j**oi**n                                                         | t**oy**                                                     |                                                              |
| y uw                 | ju   | **Yu**ma                                                       | h**u**man                                                        | f**new**                                                     |                                                              |
| 限於                   | ə    | **a**go                                                        | wom**n**                                                        | 是                                                    |                                                              |
| R-彩色母音|      |                                                            |                                                              |                                                         |                                                              |
| ih r                 | ɪɹ   | **ear**                                                       | t**ir**amisu                                                     | n**耳**                                                    |                                                              |
| eh r                 | ɛɹ   | **空氣**平面                                                   | 應用程式**ar**ently                                                   | sc**ar**e                                                   |                                                              |
| 糟糕 r                 | ʊɹ   |                                                            |                                                              | c**您的**e                                                    |                                                              |
| ay r                 | aɪɹ  | **即刻到期 i**土地                                                    | f**ir**取代                                                    | ch**oir**                                                   |                                                              |
| aw r                 | aʊɹ  | **小時**s                                                      | p**ower**規格                                                     | s                                                    |                                                              |
| ao r                 | ɔɹ   | **或**範圍                                                     | m**或**al                                                        | s**oar**                                                    |                                                              |
| aa r                 | ɑɹ   | **ar**tist                                                     | st**ar**t                                                        | c**ar**                                                     |                                                              |
| er r                 | ɝ    | **ear**第一                                                      | b**ir**d                                                         | f                                                     |                                                              |
| ax r                 | ɚ    |                                                            | 所有的**er**gy                                                      | 抑制**er**                                                  |                                                              |
| SEMIVOWELS|      |                                                            |                                                              |                                                         |                                                              |
| w                    | w    | **w**，s**ue**de                                                | al**w**ays                                                       |                                                         |                                                              |
| y                    | j    | **y**ard，f**e**w                                                  | 開啟**時**                                                        |                                                         |                                                              |
| ASPIRATED 口頭停止 |      |                                                            |                                                              |                                                         |                                                              |
| p                    | p    | **p**                                                        | ha**pp**en                                                       | fla**p**                                                    |                                                              |
| b                    | b    | **b**ig                                                        | num**b**er                                                       | cra**b**                                                    |                                                              |
| t                    | t    | **t**alk                                                       | capi**t**al                                                      | sough**t**                                                  |                                                              |
| d                    | d    | **d**ig                                                        | 已執行**d**om                                                       | ro**d**                                                     |                                                              |
| k                    | k    | **c**未通過                                                        | sla**ck**er                                                      | Ira**q**                                                    |                                                              |
| g                    | g    | **g**o                                                         | **g**o                                                          | dra**g**                                                    |                                                              |
| NASAL 停止          |      |                                                            |                                                              |                                                         |                                                              |
| m                    | m    | **m**at、smash                                                 | ca**m**紀元                                                       | roo**m**                                                    |                                                              |
| n                    | n    | **n**o、s**n**允許                                                   | te**n**t                                                         | chicke**n**                                                 |                                                              |
| ng                   | ŋ    |                                                            | li**n**k                                                         | s**ing**                                                    |                                                              |
| FRICATIVES|      |                                                            |                                                              |                                                         |                                                              |
| f                    | f    | **f**ork                                                       | le**f**t                                                         | hal**f**                                                    |                                                              |
| v                    | v    | **v**alue                                                      | e**v**ent                                                        | lo**v**e                                                    |                                                              |
| th                   | 微分    | **第**                                                       | empa**第**y                                                      | **第**一週一                                                   |                                                              |
| 不對                   | -    | **第**en                                                       | **第**一次 er                                                       | smoo**th**   |                                                              |
| s                    | s    | **s**it                                                        | ri**s**k                                                         | 事實**s**                                                   |                                                              |
| z                    | z    | **z**ap                                                        | bu**s**y                                                         | 小孩                                                    |                                                              |
| sh                   | ʃ    | **sh** e                                                        | abbrevia**ti**開啟于                                                 | ru**sh**                                                    |                                                              |
| zh                   | ʒ    | **J**acques                                                    | **s**u                                                     | gara**g**e                                                  |                                                              |
| h                    | h    | **h**elp                                                       | en**h**ance                                                      | a-**h**a！                                                   |                                                              |
| AFFRICATES           |      |                                                            |                                                              |                                                         |                                                              |
| 頻道                   | tʃ   | 中的**ch**                                                       | fu**t**u                                                       | atta**ch**                                                  |                                                              |
| jh                   | dʒ   | **j**oy                                                        | ori**g**inal                                                     | oran**g**e                                                  |                                                              |
| APPROXIMANTS|      |                                                            |                                                              |                                                         |                                                              |
| l                    | l    | **l**識別碼、g**l**ad                                                  | pa**l**ace                                                       | 卡**ll**                                                   |                                                              |
| r                    | ɹ    | **r**ed、b**r**ing                                                 | bo**rr**允許                                                       | ta**r**                                                     |                                                              |

## <a name="fr-fr"></a>fr-FR

| SAPI            | .IPA                   | 範例1（適用于輔音的萌芽，為母音的單字初始） | 範例2（適用于輔音的 intervocalic、適用于母音的單字字詞系統核心） | 範例3（適用于輔音的 coda，適用于母音的單字 final） | 註解                                                      |
| --------------- | --------------------- | -------------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------- | ------------------------------------------------------------ |
| SUPRASEGMENTALS |                       |                                                          |                                                              |                                                       |                                                              |
| 1               | 今天（主要壓力）    |                                                          |                                                              |                                                       | 在負荷過高的音節的母音之後，SAPI 手機設定 put 壓力 |
|                 | ˌ (substress)         |                                                          |                                                              |                                                       | fr-FR SAPI phone set 不支援代表 .IPA substress ' ˌ '，如有需要，應直接使用 .IPA |
| -               | 。 （音節界限） |                                                          |                                                              |                                                       |                                                              |
| 母音          |                       |                                                          |                                                              |                                                       |                                                              |
| a               | a                     | **rbre**                                                    | p**a**tte                                                        | ir**a**                                                   |                                                              |
| aa              | ɑ                     |                                                          | p **-** te                                                         | p**a**s                                                   |                                                              |
| aa ~            | ɑ̃                     | **en**fant                                                   | enf**en**t                                                       | t**em**ps                                                 |                                                              |
| 限於              | ə                     |                                                          | p**e**tite                                                       | l**e**                                                    |                                                              |
| 吧              | ɛ                     | **e**lle                                                     | p**e**rdu                                                        | ét**ai**t                                                 |                                                              |
| eu              | ø                     | **œu**fs                                                     | cr**eu**ser                                                      | qu**eu**                                                  |                                                              |
| 安永              | e                     | ému                                                      | crétin                                                       | ôté                                                   |                                                              |
| eh ~            | ɛ̃                     | **im**portant                                                | p**ein**true                                                     | **中的**材料                                                 |                                                              |
| iy              | i                     | **我**dée                                                     | 寵物**我**                                                       | 我**是**                                                   |                                                              |
| oe              | –                     | **œu**f                                                      | p**eu**r                                                         |                                                       |                                                              |
| 糟糕              | ɔ                     | **o**bstacle                                                 | c**o**rps                                                        |                                                       |                                                              |
| 哦 ~            | ɔ̃                     | **on**ze                                                     | deur**上的**r                                                      | b**開啟**                                                   |                                                              |
| 允許              | o                     | **au**diteur                                                 | b**eau**coup                                                     | p**ô**                                                    |                                                              |
| oe ~            | œ̃                     | **取消**                                                       | l**un**di                                                        | br**un**                                                  |                                                              |
| uw              | u                     | **ou**trage                                                  | intr**ou**vable                                                  | **/ou**                                                    |                                                              |
| uy              | y                     | **u**ne                                                      | p**u**nir                                                        | él**u**                                                   |                                                              |
| 子音      |                       |                                                          |                                                              |                                                       |                                                              |
| b               | b                     | **b**ête                                                     | ha**b**ille                                                      | ro**b**e                                                  |                                                              |
| d               | d                     | **d**即刻到期 i                                                     | ron**d**歐元                                                      | chau**d**e                                                |                                                              |
| f               | f                     | **f**emme                                                    | su**ff**ixe                                                      | bo**f**                                                   |                                                              |
| g               | g                     | **g**auche                                                   | 日**g**ale                                                        | ba**gu**e                                                 |                                                              |
| ng              | ŋ                     |                                                          |                                                              | 公園                                              | 僅適用于部分外部文字                                 |
| hy              | ɥ                     | h**u**ile                                                    | n**u**即刻到期 i                                                        |                                                       |                                                              |
| k               | k                     | **c**arte                                                    | 日**c**aille                                                      | 是**c**                                                   |                                                              |
| l               | l                     | **l**ong                                                     | 日**l**即刻到期 i                                                        | ba**l**                                                   |                                                              |
| m               | m                     | **m**adame                                                   | ai**m**er                                                        | po**mm**e                                                 |                                                              |
| n               | n                     | **n**個 ou                                                     | te**n**ir                                                        | bo**nn**e                                                 |                                                              |
| 新澤西              | ɲ                     |                                                          |                                                              | pei**gn**e                                                |                                                              |
| p               | p                     | **p**atte                                                    | 將**p**重新做為                                                        | ca**p**                                                   |                                                              |
| r               | ɹ                     | **r**于                                                      | cha**r**iot                                                      | senti**r**                                                |                                                              |
| s               | s                     | **s**ourir                                                   | **ss**ez                                                        | pa**ss**e                                                 |                                                              |
| sh              | ʃ                     | **ch**anter                                                  | ma**ch**ine                                                      | po**ch**e                                                 |                                                              |
| t               | t                     | **t**ête                                                     | ô**t**er                                                         | ne**t**                                                   |                                                              |
| v               | v                     | **v**ent                                                     | 在**v**enter 中                                                     | rê**v**e                                                  |                                                              |
| w               | w                     | **ou**i                                                      | f**ou**ine                                                       |                                                       |                                                              |
| y               | j                     | **y**od                                                      | p**i**étiner                                                     | Marse**ille **                                            |                                                              |
| z               | z                     | \* * z * * éro                                                     | rai**s**onner                                                    | ro**s**e                                                  |                                                              |
| zh              | ʒ                     | **j**ardin                                                   | man**g**er                                                       | piè**g**e                                                 |                                                              |
|                 | 位                    |                                                          |                                                              | u**n** arbre                                              | fr-FR SAPI phone set 不支援代表法文 liasion "n ‿"，如有需要，應直接使用 .IPA |
|                 | 而已                    |                                                          |                                                              | quan**d**                                                 | fr-FR SAPI phone set 不支援代表法文 liasion "t ‿"，如有需要，應直接使用 .IPA |
|                 | z                    |                                                          |                                                              | di**x**                                                   | fr-FR SAPI phone set 不支援代表法文 liasion "z ‿" （如有需要），應直接使用 .IPA |


## <a name="de-de"></a>de-DE

| SAPI   | .IPA     | 範例1（適用于輔音的萌芽，為母音的單字初始）  | 範例2（適用于輔音的 intervocalic、適用于母音的單字字詞系統核心） | 範例3（適用于輔音的 coda，適用于母音的單字 final）     | 註解                                                      |
| --------------- | --------------------- | -------------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------- | ------------------------------------------------------------ |
| SUPRASEGMENTALS |         |                                                              |                                                              |                                                              |                                                              |
| 1               | 今天       | anders/a **1** n-1 ax r s/                                | Multiplikationszeichen/m 噢 l-t iy-p l iy-k a-ts y 允許**1** n s-ts ay-c n/ | Biologie/b iy-允許-l 允許-g iy **1**/                     | 在負荷過高的音節的母音之後，SAPI 手機設定 put 壓力 |
| 2               | ˌ       | Allgemeinwissen/a **2** l-g ax-m ay 1 n-v ih-s n/   | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z 哦**2** ax r-g 啊 ng s-f ih ax r-m a/ | Computertomographie/k 哦 m-p y uw 1-t ax r-t 允許-m 允許-g r a-f iy **2**/ | Substressed 音節的母音之後的 SAPI 電話集合 put 壓力 |
| -               | 。       |                                                              |                                                              |                                                              |                                                              |
| 母音          |         |                                                              |                                                              |                                                              |                                                              |
| 為             | ː      | **Ber**                                        | Maßst**a**b                             | 架構**a**                                      |                                                              |
| a               | a       | **Bfall**                                        | B**a**ch                                               | Agath**a**                                    |                                                              |
| 糟糕              | ɔ       | **O**sten                                          | Pf**o**sten                                     |                                                              |                                                              |
| 吧            | ɛː      | **Ä**hnlichkeit                      | B**ä**r                                           | 僅限於外部來源的單字，例如： Fasci**ae**      |                                                              |
| 吧              | ɛ       | **ä**ndern                                    | Proz**e**nt                                | Amygdal**ae**                         |                                                              |
| 限於              | ə       | Intially 只在外部來源的單字中，例如 ppointment 音節-**一**開始為： ' v**e**rstauen      | Aach**e**n                                       | 片段**e**                                      |                                                              |
| iy              | 我ː      | **我**執行                                         | abb**ie**gt                                    | Relativitätstheor**ie**     |                                                              |
| ih              | 怎樣       | **我**nnung                                       | s**i**ngen                                     | 木材**y**                                         |                                                              |
| eu              | øː      | **Ö**sen                                             | abl**ö**sten                               | Malm**ö**                                        |                                                              |
| 允許              | o、o ː   | **o**hne                                            | Balk**o**n                                     | Trept**允許**                                   |                                                              |
| oe              | –       | **Ö**ffnung                                    | bef**ö**rdern                     |                                                              |                                                              |
| 安永              | e、e ː   | **E**berhard                            | .abf**e**gt                                     | b                                                    |                                                              |
| uw              | u ː      | **U**do                                             | H**u**t                                                | Akk**u**                                             |                                                              |
| 噢              | ʊ       | **U**nterschiedes              | b**u**nt                                             |                                                              |                                                              |
| 專色              | y ː      | **Ü**bermut                              | default.pfl**ü**gt                                        | 男性**ü**                                          |                                                              |
| uy              | ʏ       | **ü**ppig                                         | S**y**莖                                    |                                                              |                                                              |
| DIPHTHONG       |         |                                                              |                                                              |                                                              |                                                              |
| ay              | 音      | **ei**nsam                                     | Unabhängigk**ei**t      | Abt**ei**                                          |                                                              |
| aw              | 澳大利亞      | **au**ßen                                            | abb**au**st                                  | St**au**                                              |                                                              |
| oy              | ɔy, ɔʏ̯  | **Eu**phorie                                 | tr**äu**mt                                         | .sch**eu**                                               |                                                              |
| SEMIVOWEL       |         |                                                              |                                                              |                                                              |                                                              |
| ax r            | ɐ       |                                                              | abänd**er**n                          | 鎖定**er**                                     |                                                              |
| 字母       |         |                                                              |                                                              |                                                              |                                                              |
| b               | b       | **B**ank                                             |                                                              | ' 僅限於外部來源的單字，例如： Pu**b**     |                                                              |
| c               | ç       | **Ch**emie                                        | mögli**ch**st                             | i**ch**                                                  | ' e ' 和 ' i ' 之後的軟 ' ch '                                 |
| d               | d       | **d**anken                                       | 僅限於外部來源的單字，例如： Len**d**l      | 僅限於外部來源的單字，例如： Clau**d**e      |                                                              |
| jh              | ʤ       | **J**eff                                                | gemana**g**t                        | 僅限於外部來源的單字，例如： Chan**g**e      | 僅限於外部來源的單字                             |
| f               | f       | **F**ahrtdauer                          | angri**ff**slustig         | abbruchrei**f**                      |                                                              |
| g               | g       | **g**                                                |                                                              | 僅在外部來源的單字中 terminally，例如 Gre**g**     |                                                              |
| h               | h       | **H**ausanbau                           |                                                              |                                                              |                                                              |
| y               | j       | **J**od                                                | Reakt**i**                           | hu**i**                                                |                                                              |
| k               | k       | **K**oma-uri                                          | Aspe**k**t                                     | Flec**k**                                            |                                                              |
| l               | l       | **l**au                                                  | ähne**l**n                                       | zuvie**l**                                     |                                                              |
| m               | m       | **M**個                                                | **M**t                                                 | Leh**m**                                              |                                                              |
| n               | n       | **n**取消                                                | u**n**d                                                | 那是**n**                                               |                                                              |
| ng              | ŋ       | 僅限於外部來源的單字，例如： **Ng**uyen      | Schwa**nk.bin**                                       | R**ing**                                              |                                                              |
| p               | p       | **P**夥伴                                | abru**p**t                                     | Ti**p**                                                |                                                              |
| pf              | pf      | **Pf**erd                                       | dam**pf**t                                         | 至**pf**                                              |                                                              |
| r               | ʀ、r、ʁ | **R**eise                                         | knu**rr**t                                      | Haa**r**                                              |                                                              |
| s               | s       |  僅限於外部來源的單字，例如： **S**taccato      | bi**s**t                                             | mie**s**                                               |                                                              |
| sh              | ʃ       | **.Sch**規則 u)                                      | mi **.sch**t                                          | lappi **.sch**                                    |                                                              |
| t               | t       | **T**raum                                            | S**t**raße                                  | Mu**t**                                                |                                                              |
| ts              | ts      | **Z**ug                                               | Ar**z**t                                           | Wit**z**                                              |                                                              |
| 頻道              | tʃ      | **Tsch**echien                               | aufgepu**tsch**t                     | bundesdeu**tsch**                   |                                                              |
| v               | v       | **w**inken                                      | 問**u**全部                                       | 僅限於外部來源的單字，例如： Groove      |                                                              |
| x               | x、ç     | Word-一開始只會在外部來源的單字中，例如： **J**uan 音節-一開始也會在類似以下的文字： Ba**ch**erach  | Ma**ch**t mögli**ch**st  | 結構**ch** ' i**ch**             | .IPA [x]：在所有非前面的母音（a、aa、哦、允許、啊、uw 和 diphthong aw）之後，硬式「ch」。  .IPA [ç]：前面母音後面的軟性 ' ch ' （ih、iy、eh、ae、uy、ue、oe、歐盟也在 diphthongs ay、oy）和子音中 |
| z               | z       | **s**uper                                       |                                                              |                                                              |                                                              |
| zh              | ʒ       | **G**enre                                      | B**重新**ezinski                      | Edvi**g**e                                     | 僅限於外部來源的單字                             |
| 口頭輔音  |         |                                                              |                                                              |                                                              |                                                              |
| ^               | ʔ       | beachtlich/b ax-^ a 1 x t-l ih c/      |                                                              |                                                              | 這表示是以喉塞音停止，如需詳細資訊，請按一下[這裡](http://en.wikipedia.org/wiki/Glottal_stop)。 我們需要在兩個不同的母音之間新增 [gs\] 電話，但這兩個母音是一種正版 diphthong。 |


## <a name="es-es"></a>es-ES

| SAPI            | .IPA            | 範例1（適用于輔音的萌芽，為母音的單字初始） | 範例2（適用于輔音的 intervocalic、適用于母音的單字字詞系統核心） | 範例3（適用于輔音的 coda，適用于母音的單字 final） | 註解                                                     |
| --------------- | -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| SUPRASEGMENTALS |                |                                                              |                                                              |                                                              |                                                              |
| -               | 。 音節   |                                                              |                                                              |                                                              |                                                              |
| 1               | 今天（mainstress） |                                                              |                                                              |                                                              | 在負荷過高的音節的母音之後，SAPI 手機設定 put 壓力 |
| 2               | ˌ (substress)  |                                                              |                                                              |                                                              | Substressed 音節的母音之後的 SAPI 電話集合 put 壓力 |
| 母音          |                |                                                              |                                                              |                                                              |                                                              |
| a               | a              | **lto**                          | c**a**ntar                           | ca**a**                                                        |                                                              |
| i               | i   | **我**bérica                                                      | av**i**spa                                                       | 稅金**i**                                                         |                                                              |
| e               | e              | **e**lefante                                                     | 在**e**n                                                       | elefant**e**                                                     |                                                              |
| o               | o              | **o**caso                                                        | enc**o**ntrar                                                    | ocasenc**o**                                                        |                                                              |
| u               | u              | **u**sted                                                        | p**u**nta                                                        | Juanl**u**                                                       |                                                              |
| 子音      |                |                                                              |                                                              |                                                              |                                                              |
| b               | b              | **b**aobab                                                       |                                                              | am**b**                                                          |                                                              |
|                 | β              |                                                              | bao**b**ab                                                       | baoba**b**                                                       | es SAPI 電話組不支援代表 .IPA ' β '，如有需要，應直接使用 .IPA |
| 頻道              | tʃ             | **ch**eque                                                       | 共**ch**e                                                        | Marraque**ch**                                                   |                                                              |
| d               | d              | **d**艾鬥文                                                         |                                                              | portlan**d**                                                     |                                                              |
|                 | -              |                                                              | de**d**o                                                         | verda**d**                                                       | es SAPI 電話組不支援代表 .IPA '-' （如有需要），應直接使用 .IPA |
| f               | f              | **f**ácil                                                        | ele**f**加緊                                                     | pu**f**                                                          |                                                              |
| g               | g              | **g**anga                                                        |                                                              | dópin**g**                                                       |                                                              |
|                 | ɣ              |                                                              | **g**ua                                                         | tuare**g**                                                       | es SAPI 電話組不支援代表 .IPA ' ɣ '，如有需要，應直接使用 .IPA |
| j               | j              | **我**odo                                                         | cal**i**ente                                                     | 重做**y**                                                          |                                                              |
| jj              | j-phone/jj         |                                                              | vi**ll**a                                                        |                                                              |                                                              |
| k               | k              | **c**oche                                                        | bo**c**a                                                         | titáni**c**                                                      |                                                              |
| l               | l              | **l**ápiz                                                        | a**l**a                                                          | corde**l**                                                       |                                                              |
| ll              | ʎ              | **ll**                                                        | desarro**ll**o                                                   |                                                              |                                                              |
| m               | m              | **m**順序                                                       | **m**ar                                                         | álbu**m**                                                        |                                                              |
| n               | n              | **n**ada                                                         | ce**n**a                                                         | rató**n**                                                        |                                                              |
| 新澤西              | ɲ              | **-** aña                                                         | ara **-** azo                                                      |                                                              |                                                              |
| p               | p              | **p**oca                                                         | 至**p**o                                                         | 停止**p**                                                         |                                                              |
| r               | ɾ              |                                                              | ca**r**a                                                         | abri**r**                                                        |                                                              |
| rr              | r              | **r**adio                                                        | co**rr**e                                                        | pu**rr**                                                         |                                                              |
| s               | s              | **s**aco                                                         | va**s**o                                                         | pelo**s**                                                        |                                                              |
| t               | t              | **t**oldo                                                        | a**t**ar                                                         | disque**t**                                                      |                                                              |
| th              | 微分              | **z**ebra                                                        | **z**ul                                                         | lápi**z**                                                        |                                                              |
| w               | w              | h**u**eso                                                        | ag**u**a                                                         | gua**u**                                                         |                                         |
| x               | x              | **j**ota                                                         | **j**o                                                          | relo**j**                                                        |                                                              |


## <a name="zh-cn"></a>zh-CN

Zh 的 TTS SAPI 手機設定-CN 是根據原生電話設定拼音。 針對原生說話者，建議您使用 SAPI （拼音），這很容易使用，而不是 .IPA。 

**語氣**

MS TTS zh-CN SAPI 使用數位： 1 2 3 4 5 來標注音調。

| 使用色調的拼音範例 | SAPI  | 字元範例 |
| ------------------------- | ----- | ------------------ |
| mā                        | ma 1 | 媽                 |
| 則                        | ma 2 | 麻                 |
| mǎ                        | ma 3 | 馬                 |
| mà                        | ma 4 | 罵                 |
| 碩士                        | ma 5 | 嘛                 |

**範例**

| SAPI 範例 |                               |
| ------------- | ----------------------------- |
| 字元     | SAPI                          |
| 組織關係      | zu 3-zhi 1-guan 1-xi 5 |
| 累進          | lei 3-金4                 |
| 西宅巷        | xi 1-zhai 2-xiang 4      |

## <a name="zh-tw"></a>zh-TW

Zh-臺灣的 SAPI 手機設定是以原生電話設定拼音為基礎。 針對原生說話者，建議您使用 SAPI （注音），這很容易使用，而不是 .IPA。 

**語氣**

| SAPI 音調 | 注音音調 | 範例（word） | SAPI 手機 | 符號   | 拼音（拼音） |
| ---------- | ------------- | ------------------ | ----------- | ---------- | -------------------- |
| ˉ          | empty         | 偵                 | ㄓㄣˉ       | ㄓㄣ       | zhēn                 |
| ˊ          | ˊ             | 察                 | ㄔㄚˊ       | ㄔㄚˊ      | chá                  |
| ˇ          | ˇ             | 打                 | ㄉㄚˇ       | ㄉㄚˇ      | dǎ                   |
| ˋ          | ˋ             | 望                 | ㄨㄤˋ       | ㄨㄤˋ      | wàng                 |
| ˙          | ˙             | 影子               | 一ㄥˇㄗ̇  | 一ㄥˇㄗ̇ | yǐng zi             |

**範例**

| SAPI 範例 |                |
| ------------- | -------------- |
| 字元     | SAPI           |
| 狗            | ㄍㄡˇ          |
| 然後          | ㄖㄢˊㄏㄡˋ     |
| 剪掉          | ㄐㄧㄢˇㄉㄧㄠˋ |

## <a name="ja-jp"></a>ja-JP

Ja-jp 的 SAPI 手機組是以日文原生電話集（假名）為基礎。 對於原生說話者，建議您使用 SAPI （假名），這是很容易使用的，而不是 .IPA。 

**過重**

| 過重 |                 |
| ------ | --------------- |
| SAPI   | .IPA             |
| 今天      | 今天（mainstress） |
| +      | ˌ (substress)  |

**範例**

| SAPI 範例 |              |             |
| ------------- | ------------ | ----------- |
| 字元     | SAPI         | .IPA         |
| 合成          | ゴ'ウセ      | go 今天 wɯseji   |
| 擁有者        | ショュ'ウ?ャ | ɕjojɯ今天 wɯɕja |
| 最適化        | サィテキカ +  | sajitecikaˌ |