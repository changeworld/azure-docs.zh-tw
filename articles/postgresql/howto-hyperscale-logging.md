---
title: 記錄-超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 如何存取資料庫記錄以進行適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096506"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫中的記錄檔超大規模資料庫（Citus）

于 postgresql 記錄可在超大規模資料庫（Citus）伺服器群組的每個節點上使用。 您可以將記錄傳送至存放伺服器或分析服務。 記錄可用來識別、疑難排解和修復設定錯誤和效能較佳。

## <a name="accessing-logs"></a>存取記錄

若要存取超大規模資料庫（Citus）協調器或背景工作節點的于 postgresql 記錄，請開啟 Azure 入口網站中的節點：

![節點清單](media/howto-hyperscale-logging/choose-node.png)

針對選取的節點，開啟 [**診斷設定**]，然後按一下 [ **+ 新增診斷設定**]。

![[新增診斷設定] 按鈕](media/howto-hyperscale-logging/diagnostic-settings.png)

為新的診斷設定選擇 [名稱]，然後選取 [ **PostgreSQLLogs** ] 方塊。  選擇應該接收記錄的目的地。

![選擇于 postgresql 記錄](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>後續步驟

- [開始使用 log analytics 查詢](/azure/azure-monitor/log-query/get-started-portal)
- 瞭解[Azure 事件中樞](/azure/event-hubs/event-hubs-about)
