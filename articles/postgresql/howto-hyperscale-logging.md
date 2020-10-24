---
title: 記錄-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: '如何存取適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus 的資料庫記錄) '
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: fac0db7f8c94113ff87d1c0a3091e4c0cbd5ed2b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489841"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>記錄適用於 PostgreSQL 的 Azure 資料庫-超大規模 (Citus) 

于 postgresql 記錄可在超大規模 (Citus) server 群組的每個節點上使用。 您可以將記錄傳送至儲存體伺服器或分析服務。 這些記錄可用來識別、疑難排解和修復設定錯誤和次佳效能。

## <a name="accessing-logs"></a>存取記錄

若要存取超大規模 (Citus) 協調器或背景工作節點的于 postgresql 記錄，請在 Azure 入口網站中開啟節點：

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="節點清單":::

針對選取的節點開啟 [ **診斷設定**]，然後按一下 [ **+ 新增診斷設定**]。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="節點清單":::

挑選新診斷設定的名稱，然後選取 [ **PostgreSQLLogs** ] 方塊。  選擇要接收記錄 () 的目的地。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="節點清單":::

## <a name="next-steps"></a>後續步驟

- [開始使用 log analytics 查詢](../azure-monitor/log-query/get-started-portal.md)
- 瞭解 [Azure 事件中樞](../event-hubs/event-hubs-about.md)