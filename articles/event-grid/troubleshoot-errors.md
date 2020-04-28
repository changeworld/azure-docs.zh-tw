---
title: Azure 事件方格-疑難排解指南
description: 本文提供錯誤碼、錯誤訊息、描述和建議動作的清單。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645067"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>針對 Azure 事件方格錯誤進行疑難排解
本疑難排解指南提供您在收到這些錯誤時，應採取的 Azure 事件方格錯誤碼、錯誤訊息、描述和建議動作的清單。 

## <a name="error-code-400"></a>錯誤碼︰400
| 錯誤碼 | 錯誤訊息 | 描述 | 建議 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | 主題名稱的長度必須介於3到50個字元之間。 | 自訂主題名稱長度應介於3到50個字元的長度。 主題名稱只允許英數位元、數位和 '-' 字元。 此外，名稱不應以下列保留字開頭： <ul><li>Microsoft</li><li>EventGrid</li><li>系統</li></ul> | 選擇遵循主題名稱需求的其他主題名稱。 |
| HttpStatusCode. BadRequest<br/>400 | 功能變數名稱的長度必須介於3到50個字元之間。 | 功能變數名稱長度應介於3到50個字元的長度。 主題名稱只允許英數位元、數位和 '-' 字元。 此外，名稱不應以下列保留字開頭：<ul><li>Microsoft</li><li>EventGrid</li><li>系統</li> | 請選擇符合功能變數名稱需求的其他功能變數名稱。 |
| HttpStatusCode. BadRequest<br/>400 | 不正確到期時間。 | 事件訂閱的到期時間會決定何時會淘汰事件訂閱。 此值未來應為有效的日期時間值。| 請確定事件訂閱的到期時間是有效的日期時間格式，而且設定為未來的。 |

## <a name="error-code-409"></a>錯誤碼：409
| 錯誤碼 | 錯誤訊息 | 描述 | 建議的動作 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode。衝突 <br/>409 | 已存在具有指定名稱的主題。 選擇不同的主題名稱。   | 自訂主題名稱在單一 Azure 區域中必須是唯一的，才能確保正確的發佈作業。 相同的名稱可以在不同的 Azure 區域中使用。 | 為主題選擇不同的名稱。 |
| HttpStatusCode。衝突 <br/> 409 | 具有指定的網域已經存在。 請選擇不同的功能變數名稱。 | 功能變數名稱在單一 Azure 區域中必須是唯一的，才能確保正確的發佈作業。 相同的名稱可以在不同的 Azure 區域中使用。 | 請為網域選擇不同的名稱。 |
| HttpStatusCode。衝突<br/>409 | 已達到配額限制。 如需這些限制的詳細資訊，請參閱[Azure 事件方格限制](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)。  | 每個 Azure 訂用帳戶都有可使用的 Azure 事件方格資源數目限制。 已超過此配額的部分或全部，因此無法建立更多資源。 |    檢查您目前的資源使用量，並刪除任何不需要的資源。 如果您仍然需要增加配額，請將電子郵件傳送至[aeg@microsoft.com](mailto:aeg@microsoft.com) ，並提供所需的確切資源數。 |


## <a name="next-steps"></a>後續步驟
如果您需要更多協助，請在[Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-eventgrid)中張貼您的問題，或開啟[支援票證](https://azure.microsoft.com/support/options/)。 
