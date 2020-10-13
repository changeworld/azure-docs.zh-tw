---
title: 設定 audit logs-Azure 入口網站-適用於 MySQL 的 Azure 資料庫-彈性伺服器
description: 本文說明如何從 Azure 入口網站設定和存取適用於 MySQL 的 Azure 資料庫彈性伺服器中的 audit 記錄。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: cadd384bc7d3222cbc5d1179271f7b4624c9224a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565813"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>使用 Azure 入口網站為適用於 MySQL 的 Azure 資料庫彈性的伺服器設定及存取 audit 記錄

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

您可以從 Azure 入口網站設定適用於 MySQL 的 Azure 資料庫彈性的伺服器 [審核記錄](concepts-audit-logs.md) 和診斷設定。

## <a name="prerequisites"></a>必要條件
本文中的步驟需要您擁有有 [彈性的伺服器](quickstart-create-server-portal.md)。

## <a name="configure-audit-logging"></a>設定 audit 記錄

>[!IMPORTANT]
> 建議您只針對您的審核目的記錄所需的事件種類和使用者，以確保伺服器的效能不會受到嚴重的影響。

啟用及設定 audit 記錄。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取您的彈性伺服器。

1. 在 [ **設定** ] 區段的側邊欄中，選取 [ **伺服器參數**]。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/server-parameters.png" alt-text="伺服器參數":::

1. 將 **audit_log_enabled** 參數更新為 ON。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-enabled.png" alt-text="伺服器參數":::

1. 藉由更新**audit_log_events**參數，選取要記錄的[事件種類](concepts-audit-logs.md#configure-audit-logging)。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-events.png" alt-text="伺服器參數":::

1. 藉由更新 **audit_log_exclude_users** 和 **audit_log_include_users** 參數，新增要在記錄中包含或排除的任何 MySQL 使用者。 提供使用者的 MySQL 使用者名稱來指定使用者。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="伺服器參數":::

1. 變更參數之後，您可以按一下 [儲存]****。 或者，也可以 [捨棄]**** 您的變更。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-parameters.png" alt-text="伺服器參數":::

## <a name="set-up-diagnostics"></a>設定診斷

Audit 記錄檔與 Azure 監視器診斷設定整合，可讓您使用管線將記錄傳送至 Azure 監視器記錄、事件中樞或 Azure 儲存體。

1. 在提要欄位的 [ **監視** ] 區段下，選取 [ **診斷設定**]。

1. 按一下 [+ 新增診斷設定]  :::image type="content" source="./media/how-to-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="伺服器參數":::

1. 提供診斷設定名稱。

1. 指定要將審核記錄傳送 (儲存體帳戶、事件中樞和/或 Log Analytics 工作區) 的目的地。

1. 選取 [ **MySqlAuditLogs** ] 做為記錄類型。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="伺服器參數":::

1. 當您設定好將審核記錄傳送至的資料接收器之後，您可以按一下 [ **儲存**]。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="伺服器參數":::

1. 藉由在您設定的資料接收器中探索來存取審核記錄。 最多可能需要10分鐘的時間才會顯示記錄。

如果您使用管線將審核記錄傳送至 Azure 監視器記錄 (Log Analytics) ，請參閱您可以用來進行分析的一些 [範例查詢](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) 。  

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [audit 記錄](concepts-audit-logs.md)
- 瞭解 [慢速查詢記錄](concepts-slow-query-logs.md)
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->