---
title: 存取 audit 記錄檔-Azure 入口網站-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何從 Azure 入口網站設定和存取適用於 MySQL 的 Azure 資料庫中的 audit 記錄。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: c406fa6b49e800912edb5738b4d60596d828fc94
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570499"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>在 Azure 入口網站中設定和存取適用於 MySQL 的 Azure 資料庫的 audit 記錄

您可以從 Azure 入口網站設定適用於 MySQL 的 Azure 資料庫的 [ [審核記錄](concepts-audit-logs.md) ] 和 [診斷] 設定。

## <a name="prerequisites"></a>必要條件

若要逐步執行本作法指南，您需要︰

- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>設定 audit 記錄

>[!IMPORTANT]
> 建議您只針對您的審核目的記錄所需的事件種類和使用者，以確保伺服器的效能不會受到嚴重的影響。

啟用及設定 audit 記錄。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取適用於 MySQL 的 Azure 資料庫伺服器。

1. 在 [ **設定** ] 區段的側邊欄中，選取 [ **伺服器參數**]。
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="伺服器參數":::

1. 將 **audit_log_enabled** 參數更新為 ON。
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="伺服器參數":::

1. 藉由更新**audit_log_events**參數，選取要記錄的[事件種類](concepts-audit-logs.md#configure-audit-logging)。
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="伺服器參數":::

1. 藉由更新 **audit_log_exclude_users** 和 **audit_log_include_users** 參數，新增要在記錄中包含或排除的任何 MySQL 使用者。 提供使用者的 MySQL 使用者名稱來指定使用者。
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="伺服器參數":::

1. 變更參數之後，您可以按一下 [儲存]****。 或者，也可以 [捨棄]**** 您的變更。
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="伺服器參數":::

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

1. 在提要欄位的 [ **監視** ] 區段下，選取 [ **診斷設定**]。

1. 按一下 [+ 新增診斷設定] :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="伺服器參數" 作為記錄類型。
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="伺服器參數":::

1. 當您設定好將審核記錄傳送至的資料接收器之後，您可以按一下 [ **儲存**]。
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="伺服器參數":::

1. 藉由在您設定的資料接收器中探索來存取審核記錄。 最多可能需要10分鐘的時間才會顯示記錄。

## <a name="next-steps"></a>後續步驟

- 深入瞭解適用於 MySQL 的 Azure 資料庫中的[audit 記錄](concepts-audit-logs.md)
- 瞭解如何在[Azure CLI](howto-configure-audit-logs-cli.md)中設定 audit 記錄