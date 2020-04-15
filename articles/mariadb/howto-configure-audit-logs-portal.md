---
title: 存取稽核紀錄 ─ Azure 門戶 - MariaDB 的 Azure 資料庫
description: 本文介紹如何從 Azure 門戶配置和訪問 MariaDB Azure 資料庫中的審核日誌。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 506bf076c955beb5c5e57811bbdb42bfedb8cbe3
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382959"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>在 Azure 門戶中設定和存取稽核紀錄

可以從 Azure 門戶[為 MariaDB 審核日誌](concepts-audit-logs.md)和診斷設置配置 Azure 資料庫。

> [!IMPORTANT]
> 審核日誌功能當前處於預覽狀態。

## <a name="prerequisites"></a>Prerequisites

若要逐步執行本作法指南，您需要︰

- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>設定稽核記錄記錄

啟用和配置審核日誌記錄。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取適用於 MariaDB 的 Azure 資料庫伺服器。

1. 在邊列中的 **「設定」** 部份下,選擇 **「伺服器」參數**。
    ![伺服器參數](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. 將**audit_log_enabled**參數更新為"打開"
    ![開啟稽核紀錄](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. 透過更新**audit_log_events**參數選擇要記錄[的事件型態](concepts-audit-logs.md#configure-audit-logging)。
    ![稽核紀錄事件](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. 通過更新**audit_log_exclude_users**參數,添加要從日誌記錄中排除的任何 MariaDB 使用者。 通過提供 MariaDB 使用者名指定使用者。
    ![稽核紀錄排除使用者](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 變更參數之後，您可以按一下 [儲存]****。 或者，也可以 [捨棄]**** 您的變更。
    ![儲存](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

1. 在側邊欄中的 **「監視**」部分下,選擇 **「診斷設置**」。

1. 點選「+ 新增診斷設定![」 新增診斷設定](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. 提供診斷設置名稱。

1. 指定要發送審核日誌的數據接收器(存儲帳戶、事件中心和/或日誌分析工作區)。

1. 選擇"MySqlAuditLogs"作為日誌類型。
![設定診斷設定](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 將資料接收器設定為將審核日誌管道到一起後,可以按下「**保存**」。
![儲存診斷設定](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 通過在配置的數據接收器中瀏覽審核日誌來訪問審核日誌。 日誌的顯示可能需要長達 10 分鐘。

## <a name="next-steps"></a>後續步驟

- 瞭解有關 MariaDB Azure 資料庫中[的稽核紀錄](concepts-audit-logs.md)的詳細資訊
- 瞭解如何在[Azure CLI](howto-configure-audit-logs-cli.md)中設定稽核紀錄