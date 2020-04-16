---
title: Azure 事件網格中的系統主題
description: 在 Azure 事件網格中描述系統主題。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393159"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure 事件網格中的系統主題
在為 Azure 事件源建立第一個事件訂閱時,Azure 事件網格服務將創建系統主題。 目前,事件網格不會為 2020 年 3 月 15 日之前創建的主題源創建系統主題。 對於在此日期或之後創建的所有主題源,事件網格會自動創建系統主題。 本文介紹 Azure 事件網格中的**系統主題**。

> [!NOTE]
> 此功能當前未為 Azure 政府雲啟用。 

## <a name="overview"></a>概觀
當您為 Azure 事件來源(如 Azure 儲存帳戶)創建第一個事件訂閱時,訂閱的預配過程將創建**Microsoft**類型的其他資源。 刪除 Azure 事件來源的最後一個事件訂閱時,系統主題將自動刪除。

系統主題不適用於自定義主題方案,即事件網格主題和事件網格域。 

## <a name="location"></a>Location
對於位於特定區域/位置中的 Azure 事件源,系統主題將創建與 Azure 事件來源相同的位置。 例如,如果為美國東部的 Azure Blob 儲存創建事件訂閱,則系統主題將在美國東部創建。 對於全域 Azure 事件來源(如 Azure 訂閱、資源組或 Azure 地圖),事件網格將在**全域**位置創建系統主題。 

## <a name="resource-group"></a>資源群組 
通常,系統主題是在 Azure 事件源位於的同一資源組中創建的。 對於在 Azure 訂閱範圍內創建的事件訂閱,系統主題將在資源組 **「默認事件網格**」下創建。 如果資源組不存在,Azure 事件網格會在創建系統主題之前創建它。 

您試著刪除有儲存帳戶的資源群組時,您將在受影響的資源清單中看到系統主題。  

![刪除資源群組](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [自訂主題](custom-topics.md)
- [網域](event-domains.md)