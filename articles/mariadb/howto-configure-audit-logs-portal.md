---
title: 存取 audit 記錄-Azure 入口網站-適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何從 Azure 入口網站設定和存取適用於 MariaDB 的 Azure 資料庫中的 audit 記錄。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: 30ca62305c325943e17ee574873cfbbbaee97f77
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113032"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>設定和存取 Azure 入口網站中的 audit 記錄

您可以從 Azure 入口網站設定[適用於 MariaDB 的 Azure 資料庫的審核記錄](concepts-audit-logs.md)和診斷設定。

## <a name="prerequisites"></a>必要條件

若要逐步執行本作法指南，您需要︰

- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>設定審核記錄

>[!IMPORTANT]
> 建議您只記錄您的審核目的所需的事件種類和使用者，以確保伺服器的效能不會受到嚴重影響。

啟用和設定 audit 記錄。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取適用於 MariaDB 的 Azure 資料庫伺服器。

1. 在提要欄位的 [**設定**] 區段底下，選取 [**伺服器參數**]。
    ![伺服器參數](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. 將**audit_log_enabled**參數更新為 ON。
    ![啟用 audit 記錄](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. 藉由更新**audit_log_events**參數來選取要記錄的[事件種類](concepts-audit-logs.md#configure-audit-logging)。
    ![Audit 記錄事件](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. 藉由更新**audit_log_exclude_users**參數，新增要排除在記錄之外的任何適用于 mariadb 使用者。 藉由提供適用于 mariadb 使用者名稱來指定使用者。
    ![Audit log 排除使用者](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 變更參數之後，您可以按一下 [儲存]****。 或者，也可以 [捨棄]**** 您的變更。
    ![儲存](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

1. 在提要欄位的 [**監視**] 區段下，選取 [**診斷設定**]。

1. 按一下 [+ 新增診斷設定] [ ![ 新增診斷設定]](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. 提供診斷設定名稱。

1. 指定要傳送 audit 記錄（儲存體帳戶、事件中樞和/或 Log Analytics 工作區）的資料接收。

1. 選取 "MySqlAuditLogs" 作為記錄類型。
![設定診斷設定](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 當您設定資料接收以管道傳送 audit 記錄檔之後，您可以按一下 [**儲存**]。
![儲存診斷設定](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 藉由在您設定的資料接收器中探索來存取 audit 記錄。 最多可能需要10分鐘的時間，記錄才會出現。

## <a name="next-steps"></a>後續步驟

- 深入瞭解適用於 MariaDB 的 Azure 資料庫中的[audit 記錄](concepts-audit-logs.md)
- 瞭解如何在[Azure CLI](howto-configure-audit-logs-cli.md)中設定 audit 記錄