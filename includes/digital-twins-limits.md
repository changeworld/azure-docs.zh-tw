---
author: baanders
description: 包含 Azure 數位 Twins 限制的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 3035bd71a91f7cad6fb951d74081b77d8445a81f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133788"
---
### <a name="functional-limits"></a>功能限制

下表列出 Azure 數位 Twins 在目前預覽中的功能限制。

| 區域 | 功能 | 預設限制 | 可? |
| --- | --- | --- | --- |
| Azure 資源 | 區域中每個訂用帳戶的 Azure 數位 Twins 實例數目 | 10 | 是 |
| 數位 twins | Azure 數位 Twins 實例中的 twins 數目 | 200,000 | 是 |
| 路由 | 單一 Azure 數位 Twins 實例的端點數目 | 6 | 否 |
| 路由 | 單一 Azure 數位 Twins 實例的路由數目 | 6 | 是 |
| 模型 | 單一 Azure 數位 Twins 實例內的模型數目 | 10,000 | 是 |
| 模型 | 可在單一 API 呼叫中上傳的模型數目 | 250 | 否 |
| 模型 | 在單一頁面中傳回的專案數 | 100 | 否 |
| 查詢 | 在單一頁面中傳回的專案數 | 100 | 否 |
| 查詢 | `AND`  /  `OR` 查詢中的運算式數目 | 50 | 是 |
| 查詢 | 子句中的陣列專案數目 `IN`  /  `NOT IN` | 50 | 是 |
| 查詢 | 查詢中的字元數 | 8,000 | 是 |
| 查詢 | `JOINS`查詢中的數目 | 1 | 是 |

### <a name="rate-limits"></a>速率限制

下表反映不同 Api 的速率限制。

| API | 功能 | 預設限制 | 可? |
| --- | --- | --- | --- |
| 模型 API | 每秒要求數目 | 100 | 是 |
| 數位 Twins API | 每秒要求數目 | 1,000 | 是 |
| 查詢 API | 每秒要求數目 | 500 | 是 |
| 查詢 API | 每秒查詢單位數 | 4,000 | 是 |
| 事件路由 API | 每秒要求數目 | 100 | 是 |

### <a name="other-limits"></a>其他限制

適用于 Azure 數位 Twins 模型之 DTDL 檔中的資料類型和欄位限制，可以在其 GitHub 中的規格檔中找到：[*數位 Twins 定義語言（DTDL）-第2版*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。
 
如需在預覽期間撰寫查詢的查詢延遲詳細資料和其他指導方針，請參閱[*如何：查詢*](../articles/digital-twins/how-to-query-graph.md)對應項圖形。