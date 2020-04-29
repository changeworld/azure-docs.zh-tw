---
title: Azure 事件方格中的系統主題
description: 說明 Azure 事件方格中的系統主題。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393159"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure 事件方格中的系統主題
當您建立 Azure 事件來源的第一個事件訂用帳戶時，Azure 事件方格服務會建立系統主題。 目前，事件方格不會針對在 Mar、15、2020之前建立的主題來源建立系統主題。 針對您在此日期或之後建立的所有主題來源，事件方格會自動建立系統主題。 本文說明 Azure 事件方格中的**系統主題**。

> [!NOTE]
> 此功能目前未針對 Azure Government 雲端啟用。 

## <a name="overview"></a>總覽
當您建立 Azure 事件來源的第一個事件訂用帳戶（例如 Azure 儲存體帳戶）時，訂用帳戶的布建程式會建立**EventGrid/systemTopics**類型的其他資源。 刪除 Azure 事件來源的最後一個事件訂用帳戶時，系統主題會自動刪除。

系統主題不適用於自訂主題案例，也就是「事件方格」主題和「事件方格」網域。 

## <a name="location"></a>位置
針對特定區域/位置中的 Azure 事件來源，系統主題會建立在與 Azure 事件來源相同的位置中。 例如，如果您為美國東部的 Azure blob 儲存體建立事件訂用帳戶，系統主題會建立在美國東部。 對於全域 Azure 事件來源（例如 Azure 訂用帳戶、資源群組或 Azure 地圖服務），事件方格會在**全域**位置中建立系統主題。 

## <a name="resource-group"></a>資源群組 
一般而言，系統主題會建立在 Azure 事件來源所在的相同資源群組中。 針對在 Azure 訂用帳戶範圍建立的事件訂閱，系統主題會建立在資源群組**預設-EventGrid**之下。 如果資源群組不存在，Azure 事件方格會先建立它，再建立系統主題。 

當您嘗試使用儲存體帳戶刪除資源群組時，您會在受影響的資源清單中看到 [系統] 主題。  

![刪除資源群組](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [自訂主題](custom-topics.md)
- [網域](event-domains.md)