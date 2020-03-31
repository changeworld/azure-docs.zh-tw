---
title: 訪問稽核記錄 - Azure 門戶 - MySQL 的 Azure 資料庫
description: 本文介紹如何從 Azure 門戶配置和訪問 MySQL Azure 資料庫中的稽核記錄。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 188ef3a1b9777c37f8557a69e19887638a973611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062533"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>在 Azure 門戶中為 MySQL 配置和訪問 Azure 資料庫的稽核記錄

可以從 Azure 門戶[為 MySQL 稽核記錄](concepts-audit-logs.md)和診斷設置配置 Azure 資料庫。

> [!IMPORTANT]
> 稽核記錄功能當前處於預覽狀態。

## <a name="prerequisites"></a>Prerequisites

若要逐步執行本作法指南，您需要︰

- [MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>配置稽核記錄記錄

啟用和配置稽核記錄記錄。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 選取適用於 MySQL 的 Azure 資料庫伺服器。

1. 在邊欄中的 **"設置"** 部分下，選擇 **"伺服器"參數**。
    ![伺服器參數](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. 將**audit_log_enabled**參數更新為"打開"。
    ![啟用稽核記錄](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. 通過更新**audit_log_events**參數選擇要記錄[的事件種類](concepts-audit-logs.md#configure-audit-logging)。
    ![稽核記錄事件](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. 通過更新**audit_log_exclude_users**參數，添加任何 MySQL 使用者，以便從日誌記錄中排除。 通過提供 MySQL 使用者名指定使用者。
    ![稽核記錄排除使用者](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 變更參數之後，您可以按一下 [儲存]****。 或者，也可以 [捨棄]**** 您的變更。
    ![儲存](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

1. 在側邊欄中的 **"監視**"部分下，選擇 **"診斷設置**"。

1. 按一下"+ 添加診斷設置"![添加診斷設置](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. 提供診斷設置名稱。

1. 指定要發送稽核記錄的資料接收器（存儲帳戶、事件中心和/或日誌分析工作區）。

1. 選擇"MySqlAuditLogs"作為日誌類型。
![配置診斷設置](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 將資料接收器配置為將稽核記錄管道到一起後，可以按一下"**保存**"。
![保存診斷設置](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 通過在配置的資料接收器中流覽稽核記錄來訪問稽核記錄。 日誌的顯示可能需要長達 10 分鐘。

## <a name="next-steps"></a>後續步驟

- 瞭解有關 MySQL Azure 資料庫中[的稽核記錄](concepts-audit-logs.md)的詳細資訊。