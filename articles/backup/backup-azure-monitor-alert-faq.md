---
title: 監視警示和報告常見問題
description: 在本文中，您可以找到 Azure 備份監視警示和 Azure 備份報告的常見問題解答。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 28645311328a2e2054851af6bacf07927eaa88aa
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761112"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure 備份監視警示-常見問題

本文將回答有關 Azure 備份監視和報告的常見問題。

## <a name="configure-azure-backup-reports"></a>設定 Azure 備份報告

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>如何? 檢查報表資料是否已開始流入 Log Analytics (LA) 工作區？

流覽至您已設定的 LA 工作區，流覽至 [ **記錄** 檔] 功能表項目，然後執行查詢 CoreAzureBackup |花1個。 如果您看到傳回的記錄，表示資料已開始流入工作區。 初始資料推送最多可能需要24小時的時間。

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>將資料推送至 LA 工作區的頻率為何？

保存庫中的診斷資料會抽出至 Log Analytics 工作區，並且有一些延遲。 每個事件都會在從復原服務保存庫推送之後的 20 到 30 分鐘抵達 Log Analytics 工作區。 以下是延遲的進一步詳細資料：

* 在所有解決方案中，備份服務的內建警示會在建立後立即推送。 因此，這些通常會在 20 到 30 分鐘後出現在 Log Analytics 工作區中。
* 在所有解決方案中，隨選備份作業和還原作業會在完成時立即推送。
* 對於 SQL 備份以外的所有解決方案，排程的備份作業會在完成時立即推送。
* 針對 SQL 備份，因為記錄備份可能每隔 15 分鐘發生一次，所以所有已完成的排程備份作業 (包括記錄) 的資訊每隔 6 小時會進行批次處理和推送。
* 在所有解決方案中，其他資訊 (例如，備份項目、原則、復原點、儲存體等等) 會一天至少一次推送。
* 備份設定的變更 (例如變更原則或編輯原則) 會觸發所有相關備份資訊的推送。

### <a name="how-long-can-i-retain-reporting-data"></a>我可以保留報告資料的時間長度？

建立 LA 工作區之後，您可以選擇保留最多2年的資料。 根據預設，LA 工作區會保留31天的資料。

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>我在設定 LA 工作區後，是否會在報告中看到所有的資料？

 設定診斷設定之後所產生的所有資料都會推送至 LA 工作區，而且可以在報表中使用。 系統不會推送進行中的作業來提供報告。 作業完成或失敗之後，就會傳送至報表。

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>我是否可以跨保存庫和訂用帳戶檢視報告？

是的，您可以跨保存庫和訂用帳戶以及區域來查看報告。 您的資料可能位於單一 LA 工作區或一組 LA 工作區中。

### <a name="can-i-view-reports-across-tenants"></a>我可以跨租使用者查看報表嗎？

如果您是具有客戶訂用帳戶或 LA 工作區之委派存取權的 [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) 使用者，您可以使用備份報表來跨所有租使用者查看資料。

## <a name="recovery-services-vault"></a>復原服務保存庫

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure 備份代理程式作業狀態需要多久時間才會反映在入口網站中？

Azure 入口網站最多可能需要 15 分鐘，才會反映 Azure 備份代理程式作業狀態。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>當備份作業失敗時，需要多久的時間才會引發警示？

在 Azure 備份失敗的 20 分鐘內就會引發警示。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>是否會有已設定通知但不會傳送電子郵件的情況？

是。 在下列情況下，不會傳送通知：

* 如果通知設為每小時，而且在一小時內引發警示並加以解決
* 作業遭到取消時
* 如果第二項備份作業失敗，因為原始備份作業正在進行中

## <a name="next-steps"></a>後續步驟

閱讀其他常見問題集：

* Azure VM 的相關[常見問題](backup-azure-vm-backup-faq.md)。
* 「Azure 備份」代理程式的相關[常見問題](backup-azure-file-folder-backup-faq.md)
