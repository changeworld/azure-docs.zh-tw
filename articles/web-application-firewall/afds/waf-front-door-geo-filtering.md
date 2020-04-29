---
title: Azure Front Service 的網域上的地區篩選
description: 在本文中，您會了解 Azure Front Door Service 的地區篩選原則
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: e287da94a71fccabddb90f3f5a3699f4c4cf22a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79472600"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door"></a>什麼是 Azure Front Door 的網域地區篩選？

根據預設，不論使用者是在什麼位置提出要求，Azure Front Door Service 都會回應使用者的要求。 不過，在某些情況下，您可以依國家/地區來限制 Web 應用程式的存取。 Front Door 上的 Web 應用程式防火牆 (WAF) 服務可讓您定義原則，針對端點上的特定路徑使用自訂存取規則來允許或封鎖來自指定國家/地區的存取。 

WAF 原則通常包含一組自訂規則。 規則會由比對條件、動作及優先順序組成。 在比對條件中，您可以定義比對變數、運算子和比對值。  對地區篩選規則來說，比對變數是 REMOTE_ADDR、運算子是 GeoMatch，值則是相關的兩個字母國家/地區代碼。 您可以結合 GeoMatch 條件和 REQUEST_URI 字串比對條件，來建立以路徑為基礎的地區篩選規則。

您可以藉由使用 [Azure PowerShell](waf-front-door-tutorial-geo-filtering.md) 或[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)，來針對 Front Door 設定地區篩選原則。

## <a name="country-code-reference"></a>國家/地區代碼參考

|國碼 (地區碼) | 國家/地區名稱 |
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
| GP | 哥德普洛|
| GR | 希臘|
| GT | 瓜地馬拉|
| GY | 蓋亞納|
| HK | 香港特別行政區|
| HN | 宏都拉斯|
| HR | 克羅埃西亞|
| HT | 海地|
| HU | 匈牙利|
| 識別碼 | 印尼|
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

- 了解 [Front Door 的應用程式層安全性](../../frontdoor/front-door-application-security.md)。
- 了解如何[建立 Front Door](../../frontdoor/quickstart-create-front-door.md)。
