---
title: 從 Azure 資料總管刪除資料
description: 本文說明 Azure 資料總管中的刪除案例，包括清除、捨棄範圍和以保留為基礎的刪除。
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 681cfd71d2666630b192935d66ba32eaf16c92de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204608"
---
# <a name="delete-data-from-azure-data-explorer"></a>從 Azure 資料總管刪除資料

Azure 資料總管支援本文中所述的各種刪除案例。 

## <a name="delete-data-using-the-retention-policy"></a>使用保留原則刪除資料

Azure 資料總管會根據[保留原則](/azure/kusto/management/retentionpolicy)自動刪除資料。 這個方法是刪除資料最有效率且不容易的方式。 在資料庫或資料表層級設定保留原則。

假設有一個保留期設為 90 天的資料庫或資料表。 如果只需要60天的資料，請刪除較舊的資料，如下所示：

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>藉由捨棄範圍來刪除資料

[範圍（資料分區）](/azure/kusto/management/extents-overview)是儲存資料的內部結構。 每個範圍最多可以保存數百萬筆記錄。 您可以使用[drop 範圍命令](/azure/kusto/management/extents-commands#drop-extents)，個別或以群組方式刪除範圍。 

### <a name="examples"></a>範例

您可以刪除資料表中的所有資料列，或只刪除特定的範圍。

* 刪除資料表中的所有資料列：

    ```kusto
    .drop extents from TestTable
    ```

* 刪除特定範圍：

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>使用清除來刪除個別資料列

[資料清除](/azure/kusto/management/data-purge)可用於刪除個人列。 刪除作業不會立即進行，而且需要大量的系統資源。 因此，只有在合規性案例中才會建議您這麼做。  

