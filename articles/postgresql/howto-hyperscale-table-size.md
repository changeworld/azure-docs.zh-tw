---
title: 判斷資料表大小-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 如何在超大規模 (Citus) server 群組中找出實際的分散式資料表大小
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937578"
---
# <a name="determine-table-and-relation-size"></a>判斷資料表和關聯性大小

在於 postgresql 中尋找資料表大小的一般方式， `pg_total_relation_size` 在超大規模 (Citus) 上大幅報告分散式資料表的大小。
此函式會在超大規模 (Citus) 伺服器群組中，以顯示協調器節點上的資料表大小。  實際上，分散式資料表中的資料會存在於分區) （而不是協調器） (的背景工作節點上。 分散式資料表大小的真正量值會取得為分區大小的總和。 超大規模 (Citus) 提供協助程式函數來查詢此資訊。

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>函數</th>
<th>傳回</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name) </td>
<td><ul>
<li>資料表中實際資料的大小 (「<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">主要分支</a>」 ) 。</li>
<li>關聯可以是資料表或索引的名稱。</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name) </td>
<td><ul>
<li><p>citus_relation_size plus：</p>
<blockquote>
<ul>
<li><a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">可用空間對應</a>的大小</li>
<li><a href="https://www.postgresql.org/docs/current/static/storage-vm.html">可見度對應</a>的大小</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name) </td>
<td><ul>
<li><p>citus_table_size plus：</p>
<blockquote>
<ul>
<li>索引的大小</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

這些函式類似于三個標準于 postgresql [物件大小](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)函式，除非它們無法連接到節點，否則會發生錯誤。

## <a name="example"></a>範例

以下說明如何列出所有分散式資料表的大小：

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

輸出：

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [調整伺服器群組](howto-hyperscale-scale-grow.md) 以保存更多資料。
* 區分超大規模 (Citus) 伺服器群組中的 [資料表類型](concepts-hyperscale-nodes.md) 。
