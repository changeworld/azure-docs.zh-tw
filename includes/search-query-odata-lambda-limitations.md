---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80272850"
---
| 資料類型 | Lambda 運算式中允許的功能`any` | Lambda 運算式中允許的功能`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | 除了和以外的所有專案 `search.ismatch``search.ismatchscoring` | 相同 |
| `Collection(Edm.String)` | 與或的比較 `eq``search.in` <br/><br/> 結合子運算式與`or` | 與或的比較 `ne``not search.in()` <br/><br/> 結合子運算式與`and` |
| `Collection(Edm.Boolean)` | 與或的比較 `eq``ne` | 相同 |
| `Collection(Edm.GeographyPoint)` | 搭配 `geo.distance` `lt` 或使用`le` <br/><br/> `geo.intersects` <br/><br/> 結合子運算式與`or` | 搭配 `geo.distance` `gt` 或使用`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> 結合子運算式與`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | 使用 `eq` 、、、、 `ne` `lt` `gt` `le` 或的比較`ge` <br/><br/> 使用來合併與其他子運算式的比較`or` <br/><br/> 結合 `ne` 使用以外的其他子運算式的比較`and` <br/><br/> `and` `or` 在[Disjunctive 標準格式（DNF）](https://en.wikipedia.org/wiki/Disjunctive_normal_form)中使用和組合的運算式 | 使用 `eq` 、、、、 `ne` `lt` `gt` `le` 或的比較`ge` <br/><br/> 使用來合併與其他子運算式的比較`and` <br/><br/> 結合 `eq` 使用以外的其他子運算式的比較`or` <br/><br/> `and` `or` 在[組成標準格式（my.cnf）](https://en.wikipedia.org/wiki/Conjunctive_normal_form)中使用和組合的運算式 |
