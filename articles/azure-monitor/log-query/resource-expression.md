---
title: Azure 監視器日誌查詢中的資源（） 運算式 |微軟文檔
description: 資源運算式用於以資源為中心的 Azure 監視器日誌查詢中，以便從多個資源檢索資料。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665694"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>Azure 監視器日誌查詢中的資源（） 運算式

該`resource`運算式用於 Azure 監視器查詢[，該查詢範圍限定為資源](scope.md#query-scope)，以便從其他資源檢索資料。 


## <a name="syntax"></a>語法

`resource(`*識別碼*`)`

## <a name="arguments"></a>引數

- *識別碼*：資源的資源識別碼。

| 識別碼 | 描述 | 範例
|:---|:---|:---|
| 資源 | 包括資源的資料。 | 資源（"/訂閱/xxxxxx-xxx-xxx-xxxxxx-xxxxxxxxxxxx/資源組/我的資源組/供應商/微軟.計算/虛擬機器/myvm"） |
| 資源組或訂閱 | 包括資源及其包含的所有資源的資料。  | 資源（"/訂閱/xxxxxxxxx-xxx-xxx-xxxxxxxxxxxxxxx/資源組/我的資源組） |


## <a name="notes"></a>注意

* 您必須具有對資源的讀取存取許可權。


## <a name="examples"></a>範例

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>後續步驟

- 有關查詢範圍的詳細資訊，請參閱[Azure 監視器日誌分析中的日誌查詢範圍和時間範圍](scope.md)。
- 存取 [Kusto 查詢語言](/azure/kusto/query/)的完整文件。
