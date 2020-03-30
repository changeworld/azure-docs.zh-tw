---
title: Azure 事件網格 - 故障排除指南
description: 本文提供了錯誤代碼、錯誤訊息、說明和建議操作的清單。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645067"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>排除 Azure 事件網格錯誤
此故障排除指南為您提供了 Azure 事件網格錯誤代碼的清單、錯誤訊息、說明以及收到這些錯誤時應執行的操作。 

## <a name="error-code-400"></a>錯誤碼︰400
| 錯誤碼 | 錯誤訊息 | 描述 | 建議 |
| ---------- | ------------- | ----------- | -------------- | 
| HTTPStatusCode.壞請求<br/>400 | 主題名稱的長度必須介於 3 到 50 個字元之間。 | 自訂主題名稱長度應介於 3 到 50 個字元之間。 主題名稱中只允許字母數位字母、數位和"-"字元。 此外，名稱不應以以下保留詞開頭： <ul><li>Microsoft</li><li>EventGrid</li><li>系統</li></ul> | 選擇符合主題名稱要求的不同主題名稱。 |
| HTTPStatusCode.壞請求<br/>400 | 功能變數名稱的長度必須介於 3 到 50 個字元之間。 | 功能變數名稱長度應介於 3 到 50 個字元之間。 主題名稱中只允許字母數位字母、數位和"-"字元。 此外，名稱不應以以下保留詞開頭：<ul><li>Microsoft</li><li>EventGrid</li><li>系統</li> | 選擇符合功能變數名稱要求的不同功能變數名稱。 |
| HTTPStatusCode.壞請求<br/>400 | 不正確過期時間。 | 事件訂閱的過期時間決定了事件訂閱何時停用。 此值應為將來有效的 DateTime 值。| 確保事件訂閱的到期日期為有效的 DateTime 格式，並且設置為將來。 |

## <a name="error-code-409"></a>錯誤代碼： 409
| 錯誤碼 | 錯誤訊息 | 描述 | 建議的動作 |
| ---------- | ------------- | ----------- | -------------- | 
| HTTPStatusCode.衝突 <br/>409 | 具有指定名稱的主題已存在。 選擇其他主題名稱。   | 自訂主題名稱在單個 Azure 區域中應是唯一的，以確保正確的發佈操作。 同一名稱可用於不同的 Azure 區域。 | 為主題選擇其他名稱。 |
| HTTPStatusCode.衝突 <br/> 409 | 已存在已指定的域。 選擇其他功能變數名稱。 | 功能變數名稱在單個 Azure 區域中應是唯一的，以確保正確的發佈操作。 同一名稱可用於不同的 Azure 區域。 | 為域選擇其他名稱。 |
| HTTPStatusCode.衝突<br/>409 | 已達到配額限制。 有關這些限制的詳細資訊，請參閱 Azure[事件網格限制](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)。  | 每個 Azure 訂閱對可以使用的 Azure 事件網格資源的數量有限制。 已經超出部分或全部配額，無法再創建資源。 |    檢查當前資源使用方式，並刪除任何不需要的資源。 如果仍需要增加配額，請發送電子郵件以[aeg@microsoft.com](mailto:aeg@microsoft.com)提供所需資源的確切數量。 |


## <a name="next-steps"></a>後續步驟
如果您需要更多説明，請在[堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-eventgrid)中發佈您的問題或打開[支援票證](https://azure.microsoft.com/support/options/)。 
