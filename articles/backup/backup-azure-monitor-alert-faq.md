---
title: 監視警報和報告常見問題解答
description: 在本文中，瞭解有關 Azure 備份監視警報和 Azure 備份報告的常見問題的解答。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989564"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure 備份監視警報 - 常見問題解答

本文回答了有關 Azure 備份監視和報告的常見問題。

## <a name="configure-azure-backup-reports"></a>設定 Azure 備份報告

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>如何檢查報告資料是否已開始流入日誌分析 （LA） 工作區？

導航到已配置的 LA 工作區，導航到**日誌**功能表項目，並執行查詢 CoreAzure 備份 |採取 1。 如果您看到正在返回的記錄，則意味著資料已開始流入工作區。 初始資料推送可能需要長達 24 小時。

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>資料推送到 LA 工作區的頻率是多少？

從保存庫的診斷資料被泵送到日誌分析工作區，並帶有一些滯後性。 每個事件在從恢復服務保存庫推送 20 到 30 分鐘後到達日誌分析工作區。 以下是有關延遲的進一步詳細資訊：

* 在所有解決方案中，備份服務的內置警報在創建後立即推送。 因此，它們通常在 20 到 30 分鐘後顯示在日誌分析工作區中。
* 在所有解決方案中，按需備份作業和恢復作業一旦完成，就會推送。
* 對於除 SQL 備份之外的所有解決方案，計畫備份作業在完成後立即推送。
* 對於 SQL 備份，由於記錄備份可以每 15 分鐘進行一次，因此每 6 小時批次處理並推送所有已完成的計畫備份作業（包括日誌）的資訊。
* 在所有解決方案中，其他資訊（如備份項、策略、復原點、存儲等）每天至少推送一次。
* 備份配置的更改（如更改策略或編輯策略）會觸發所有相關備份資訊的推送。

### <a name="how-long-can-i-retain-reporting-data"></a>我可以保留報告資料多長時間？

創建 LA 工作區後，您可以選擇將資料保留最多 2 年。 預設情況下，LA 工作區將資料保留 31 天。

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>配置 LA 工作區後，我是否會在報表中看到所有資料？

 配置診斷設置後生成的所有資料將推送到 LA 工作區，並在報表中可用。 系統不會推送進行中的作業來提供報告。 作業完成或失敗後，它將發送到報表。

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>我是否可以跨保存庫和訂用帳戶檢視報告？

可以，您可以查看跨保存庫和訂閱以及區域的報告。 您的資料可能駐留在單個 LA 工作區或一組 LA 工作區中。

### <a name="can-i-view-reports-across-tenants"></a>我可以查看跨租戶的報告嗎？

如果您是 Azure[燈塔](https://azure.microsoft.com/services/azure-lighthouse/)使用者，具有對客戶訂閱或 LA 工作區的委派存取權限，則可以使用備份報表查看所有租戶中的資料。

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure 備份代理程式作業狀態需要多久時間才會反映在入口網站中？

Azure 入口網站最多可能需要 15 分鐘，才會反映 Azure 備份代理程式作業狀態。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>當備份作業失敗時，需要多久的時間才會引發警示？

在 Azure 備份失敗的 20 分鐘內就會引發警示。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>是否會有已設定通知但不會傳送電子郵件的情況？

是。 在下列情況下，不會傳送通知。

* 如果通知設為每小時，而且在一小時內引發警示並加以解決
* 作業遭到取消時
* 如果第二項備份作業失敗，因為原始備份作業正在進行中

## <a name="recovery-services-vault"></a>復原服務保存庫

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure 備份代理程式作業狀態需要多久時間才會反映在入口網站中？

Azure 入口網站最多可能需要 15 分鐘，才會反映 Azure 備份代理程式作業狀態。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>當備份作業失敗時，需要多久的時間才會引發警示？

在 Azure 備份失敗的 20 分鐘內就會引發警示。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>是否會有已設定通知但不會傳送電子郵件的情況？

是。 在以下情況下，不會發送通知：

* 如果通知設為每小時，而且在一小時內引發警示並加以解決
* 作業遭到取消時
* 如果第二項備份作業失敗，因為原始備份作業正在進行中

## <a name="next-steps"></a>後續步驟

閱讀其他常見問題集：

* Azure VM 的相關[常見問題](backup-azure-vm-backup-faq.md)。
* 「Azure 備份」代理程式的相關[常見問題](backup-azure-file-folder-backup-faq.md)
