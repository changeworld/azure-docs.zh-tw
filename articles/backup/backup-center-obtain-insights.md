---
title: 使用備份中心取得見解
description: 瞭解如何使用備份中心來分析歷程趨勢，並取得備份的更深入見解。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c0f687ee123abe2f95ad0d23a6fe302f4a5c22e1
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173745"
---
# <a name="obtain-insights-using-backup-center"></a>使用備份中心取得見解

為了分析歷史趨勢並取得更深入的備份見解，「備份中心」提供了用來 [備份報表](configure-reports.md)的介面，其使用 [Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md) 和 [Azure 活頁簿](../azure-monitor/platform/workbooks-overview.md)。 備份報表提供下列功能：

- 配置和預測已取用的雲端儲存體。

- 稽核備份和還原。

- 找出不同細微性層級的主要趨勢。

- 取得您的備份成本優化機會的可見度和見解。

## <a name="supported-scenarios"></a>支援的案例

- 適用於 PostgreSQL 的 Azure 資料庫 server 備份目前無法使用備份報告。

- 如需支援和不支援案例的詳細清單，請參閱 [支援矩陣](backup-center-support-matrix.md) 。

## <a name="get-started"></a>開始使用

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>設定您的保存庫以將資料傳送至 Log Analytics 工作區

[瞭解如何為您的保存庫設定大規模的診斷設定](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>在備份中心入口網站中查看備份報表

在 [備份中心] 中選取 [ **備份報告** ] 功能表項目，就會開啟報告。 選擇一或多個 Log Analytics 工作區，以查看並分析備份上的重要資訊。

![備份中心的備份報告](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

可用的視圖如下：

1. **摘要** -使用此索引標籤可取得備份資產的概要說明。 [深入了解](./configure-reports.md#summary)

1. **備份專案** -使用此索引標籤可查看在備份專案層級使用之雲端儲存體的資訊和趨勢。 [深入了解](./configure-reports.md#backup-items)

1. **使用** 方式-使用此索引標籤來查看您備份的重要帳單參數。 [深入了解](./configure-reports.md#usage)

1. **作業** -使用此索引標籤可查看長期執行的工作趨勢，例如每日失敗的作業數目，以及作業失敗的最大原因。 [深入了解](./configure-reports.md#jobs)

1. **原則** -使用此索引標籤可查看所有作用中原則的相關資訊，例如相關聯的專案數目，以及在指定原則下備份之專案所耗用的雲端儲存體總數。 [深入了解](./configure-reports.md#policies)

1. **優化** -使用此索引標籤可讓您查看備份的潛在成本優化機會。 [深入了解](./configure-reports.md#optimize)

## <a name="next-steps"></a>後續步驟

- [監視和操作備份](backup-center-monitor-operate.md)
- [管理您的備份資產](backup-center-govern-environment.md)
- [使用備份中心執行動作](backup-center-actions.md)