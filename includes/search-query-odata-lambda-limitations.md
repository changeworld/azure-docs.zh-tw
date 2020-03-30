---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272850"
---
| 資料類型 | lambda 運算式中允許的功能，`any` | lambda 運算式中允許的功能，`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | 除`search.ismatch`和`search.ismatchscoring` | 相同 |
| `Collection(Edm.String)` | 與`eq`或 的比較`search.in` <br/><br/> 將子運算式與`or` | 與`ne`或 的比較`not search.in()` <br/><br/> 將子運算式與`and` |
| `Collection(Edm.Boolean)` | 與`eq`或 的比較`ne` | 相同 |
| `Collection(Edm.GeographyPoint)` | 使用`geo.distance``lt`或`le` <br/><br/> `geo.intersects` <br/><br/> 將子運算式與`or` | 使用`geo.distance``gt`或`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> 將子運算式與`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | 使用`eq`、 `ne` `lt`、 `gt` `le`、 、 、`ge` <br/><br/> 使用 其他子運算式將比較組合`or` <br/><br/> 將比較與`ne`使用的其他子運算式合併`and` <br/><br/> 使用`and`和`or`以[分離法態形式 （DNF）](https://en.wikipedia.org/wiki/Disjunctive_normal_form)的組合的組合的運算式 | 使用`eq`、 `ne` `lt`、 `gt` `le`、 、 、`ge` <br/><br/> 使用 其他子運算式將比較組合`and` <br/><br/> 將比較與`eq`使用的其他子運算式合併`or` <br/><br/> 使用 和 在`and``or`[組合法態形式 （CNF）](https://en.wikipedia.org/wiki/Conjunctive_normal_form)的組合中的運算式 |
