---
title: Azure Web 應用程式防火牆 (WAF) 地區比對自訂規則
description: 本文概述Azure 應用程式閘道上的 Web 應用程式防火牆 (WAF) 地區比對自訂規則。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: bdb115cf6d0f0aa2c55f6143015a323df6faa114
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744292"
---
# <a name="geomatch-custom-rules-preview"></a>地區比對自訂規則 (預覽)

自訂規則可讓您建立量身打造的規則，以符合您應用程式和安全性原則的確切需求。 現在，您可以依國家/地區限制對您 Web 應用程式的存取。 如同所有的自訂規則，此邏輯可以與其他規則合併，以符合您應用程式的需求。

若要建立地區篩選自訂規則，只要在 [比對類型] 選取「地理位置」，然後選取應用程式要允許/封鎖的單一國家/地區或多個國家/地區。 如需詳細資訊，請參閱[如何在 Powershell 中建立自訂規則](configure-waf-custom-rules.md)，以及更多自訂規則的範例 (waf-rules.md)。

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="countryregion-codes"></a>國碼/地區碼

如果您使用地區比對運算子，選取器可以是下列任何一個兩位數字的國家/地區碼。 

|國碼/地區碼 | 國家/地區名稱 |
| ----- | ----- |
| AD | 安道爾 |
| AE | 阿拉伯聯合大公國|
| AF | 阿富汗|
| AG | 安地卡及巴布達|
| AL | 阿爾巴尼亞|
| AM | 亞美尼亞|
| AO | 安哥拉|
| AR | 阿根廷|
| AS | 美屬薩摩亞|
| AT | 奧地利|
| AU | 澳大利亞|
| AZ | 亞塞拜然|
| BA | 波士尼亞赫塞哥維納|
| BB | 巴貝多|
| BD | 孟加拉|
| BE | 比利時|
| BF | 布吉納法索|
| BG | 保加利亞|
| BH | 巴林|
| BI | 蒲隆地|
| BJ | 貝南|
| BL | 聖巴瑟米|
| BN | 汶萊達魯薩蘭|
| BO | 玻利維亞|
| BR | 巴西|
| BS | 巴哈馬|
| BT | 不丹|
| BW | 波札那|
| BY | 白俄羅斯|
| BZ | 貝里斯|
| CA | Canada|
| CD | 剛果民主共和國|
| CF | 中非共和國|
| CH | 瑞士|
| CI | 象牙海岸|
| CL | 智利|
| CM | 喀麥隆|
| CN | 中國|
| CO | 哥倫比亞|
| CR | 哥斯大黎加|
| CU | 古巴|
| CV | 維德角|
| CY | 賽浦路斯|
| CZ | 捷克共和國|
| DE | 德國|
| DK | 丹麥|
| DO | 多明尼加共和國|
| DZ | 阿爾及利亞|
| EC | 厄瓜多|
| EE | 愛沙尼亞|
| EG | 埃及|
| ES | 西班牙|
| ET | 衣索比亞|
| FI | 芬蘭|
| FJ | 斐濟|
| FM | 密克羅尼西亞聯邦|
| FR | 法國|
| GB | United Kingdom|
| GE | 喬治亞|
| GF | 法屬圭亞那|
| GH | 迦納|
| GN | 幾內亞|
| GP | 哥德洛普|
| GR | 希臘|
| GT | 瓜地馬拉|
| GY | 蓋亞那|
| HK | 香港特別行政區|
| HN | 宏都拉斯|
| HR | 克羅埃西亞|
| HT | 海地|
| HU | 匈牙利|
| ID | 印尼|
| IE | 愛爾蘭|
| IL | 以色列|
| IN | 印度|
| IQ | 伊拉克|
| IR | 伊朗伊斯蘭共和國|
| IS | 冰島|
| IT | 義大利|
| JM | 牙買加|
| JO | 約旦|
| JP | 日本|
| KE | 肯亞|
| KG | 吉爾吉斯|
| KH | 柬埔寨|
| KI | 吉里巴斯|
| KN | 聖克里斯多福及尼維斯|
| KP | 朝鮮民主主義人民共和國|
| KR | 大韓民國|
| KW | 科威特|
| KY | 開曼群島|
| KZ | 哈薩克|
| LA | 寮人民民主共和國|
| LB | 黎巴嫩|
| LI | 列支敦斯登|
| LK | 斯里蘭卡|
| LR | 賴比瑞亞|
| LS | 賴索托|
| LT | 立陶宛|
| LU | 盧森堡|
| LV | 拉脫維亞|
| LY | 利比亞 |
| MA | 摩洛哥|
| MD | 摩爾多瓦共和國|
| MG | 馬達加斯加|
| MK | 北馬其頓|
| ML | 馬利|
| MM | 緬甸|
| MN | 蒙古|
| MO | 澳門特別行政區|
| MQ | 馬丁尼克|
| MR | 茅利塔尼亞|
| MT | 馬爾他|
| MV | 馬爾地夫|
| MW | 馬拉威|
| MX | 墨西哥|
| MY | 馬來西亞|
| MZ | 莫三比克|
| NA | 納米比亞|
| NE | 尼日|
| NG | 奈及利亞|
| NI | 尼加拉瓜|
| NL | 荷蘭|
| 否 | 挪威|
| NP | 尼泊爾|
| NR | 諾魯|
| NZ | 紐西蘭|
| OM | 阿曼|
| PA | 巴拿馬|
| PE | 祕魯|
| PH | 菲律賓|
| PK | 巴基斯坦|
| PL | 波蘭|
| PR | 波多黎各|
| PT | 葡萄牙|
| PW | 帛琉|
| PY | 巴拉圭|
| QA | 卡達|
| RE | 留尼旺|
| RO | 羅馬尼亞|
| RS | 塞爾維亞|
| RU | 俄羅斯聯邦|
| RW | 盧安達|
| SA | 沙烏地阿拉伯|
| SD | 蘇丹|
| SE | 瑞典|
| SG | 新加坡|
| SI | 斯洛維尼亞|
| SK | 斯洛伐克|
| SN | 塞內加爾|
| SO | 索馬利亞|
| SR | 蘇利南|
| SS | 南蘇丹|
| SV | 薩爾瓦多|
| SY | 敘利亞阿拉伯共和國|
| SZ | 史瓦濟蘭|
| TC | 土克斯及開科斯群島|
| TG | 多哥|
| 二四 | 泰國|
| TN | 突尼西亞|
| TR | 土耳其|
| TT | 千里達及托巴哥|
| TW | 台灣|
| TZ | 坦尚尼亞聯合共和國|
| UA | 烏克蘭|
| UG | 烏干達|
| US | 美國|
| UY | 烏拉圭|
| UZ | 烏茲別克|
| VC | 聖文森及格瑞那丁|
| VE | 委內瑞拉|
| VG | 英屬維京群島|
| VI | 美屬維京群島|
| VN | 越南|
| ZA | 南非|
| ZM | 尚比亞|
| ZW | 辛巴威|

## <a name="next-steps"></a>後續步驟

瞭解自訂規則之後，[建立您自己的自訂規則](create-custom-waf-rules.md)。
