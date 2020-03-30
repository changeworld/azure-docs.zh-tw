---
title: 監視 Azure 備份保護的工作負荷
description: 在本文中，瞭解使用 Azure 門戶的 Azure 備份工作負荷的監視和通知功能。
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: de5a82f5ad1d8113b27c07484f2f08f4cf97c759
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294929"
---
# <a name="monitoring-azure-backup-workloads"></a>監視 Azure 備份工作負荷

Azure 備份基於備份要求和基礎結構拓撲（本地備份與 Azure）提供多個備份解決方案。 任何備份使用者或管理員都應查看所有解決方案中發生的情況，並期望在重要方案中收到通知。 本文詳細介紹了 Azure 備份服務提供的監視和通知功能。

## <a name="backup-jobs-in-recovery-services-vault"></a>恢復服務保存庫中的備份作業

Azure 備份為 Azure 備份保護的工作負載提供內置監視和警報功能。 在"恢復服務"保存庫設置中，"**監視**"部分提供內置作業和警報。

![RS 保存庫內置監控](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

執行配置備份、備份、還原、刪除備份等操作時生成作業。

此處顯示了以下 Azure 備份解決方案中的作業：

- Azure VM 備份
- Azure 檔案備份
- Azure 工作負載備份，如 SQL 和 SAP HANA
- Azure 備份代理程式 (MAB)

不會顯示系統中心資料保護管理器 （SC-DPM）、Microsoft Azure 備份伺服器 （MABS） 中的作業。

> [!NOTE]
> Azure 工作負荷（如 Azure VM 中的 SQL 和 SAP HANA 備份）具有大量備份作業。 例如，記錄備份可以每 15 分鐘運行一次。 因此，對於此類資料庫工作負載，僅顯示使用者觸發的操作。 不顯示計畫的備份操作。

## <a name="backup-alerts-in-recovery-services-vault"></a>恢復服務保存庫中的備份警報

警報主要是通知使用者以便他們採取相關操作的情況。 "**備份警報"** 部分顯示 Azure 備份服務生成的警報。 這些警報由服務定義，使用者無法自訂創建任何警報。

### <a name="alert-scenarios"></a>警報方案

以下方案由服務定義為可警報方案。

- 備份/還原失敗
- 備份成功但有 Azure 備份代理程式 (MAB) 的警告
- 通過保留資料/停止資料保護停止保護

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>此處顯示了以下 Azure 備份解決方案中的警報

- Azure VM 備份
- Azure 檔案備份
- Azure 工作負載備份，如 SQL、SAP HANA
- Azure 備份代理程式 (MAB)

> [!NOTE]
> 此處不顯示來自系統中心資料保護管理器 （SC-DPM）、Microsoft Azure 備份伺服器 （MABS） 的警報。

### <a name="consolidated-alerts"></a>合併警報

對於 Azure 工作負載備份解決方案（如 SQL 和 SAP HANA），可以非常頻繁地生成記錄備份（根據策略，最多每 15 分鐘生成一次）。 因此，記錄備份失敗也可能非常頻繁（最多每 15 分鐘一次）。 在這種情況下，如果針對每次故障發生發出警報，最終使用者將不堪重負。 因此，將發送第一次事件的警報，如果後續失敗是由於同一根本原因，則不會生成進一步的警報。 第一個警報將更新為失敗計數。 但是，如果使用者停用了警報，則下一次發生將觸發另一個警報，這將被視為該事件的第一個警報。 這是 Azure 備份如何對 SQL 和 SAP HANA 備份執行警報整合。

### <a name="exceptions-when-an-alert-is-not-raised"></a>未引發警報時的異常

當未在發生故障時引發警報時，很少有例外情況。 其中包括：

- 使用者顯式取消正在運行的作業
- 作業失敗，因為另一個備份作業正在進行中（此處沒有任何操作，因為我們必須等待上一個作業完成）
- VM 備份作業失敗，因為備份的 Azure VM 不再存在
- [合併警報](#consolidated-alerts)

上述異常的設計基於以下理解，即這些操作的結果（主要是使用者觸發）會立即顯示在門戶/PS/CLI 用戶端上。 因此，使用者會立即知道，不需要通知。

### <a name="alert-types"></a>警示類型

根據警示嚴重性，警報可以定義三種類型：

- **嚴重**：原則上，任何備份或恢復失敗（計畫或使用者觸發）都將導致警報的生成，並顯示為嚴重警報以及破壞性操作，如刪除備份。
- **警告**：如果備份操作成功但警告很少，則它們被列為警告警報。
- **資訊：** 截至目前，Azure 備份服務不會生成任何資訊警報。

## <a name="notification-for-backup-alerts"></a>備份警報通知

> [!NOTE]
> 通知的配置只能通過 Azure 門戶完成。 不支援 PS/CLI/REST API/Azure 資源管理器範本支援。

引發警報後，將通知使用者。 Azure 備份通過電子郵件提供了內置通知機制。 可以指定單個電子郵件地址或通訊群組清單，以便生成警報時收到通知。 您還可以選擇是為每個警報收到通知，還是將它們分組到每小時摘要中，然後收到通知。

![RS Vault 內置電子郵件通知](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

配置通知後，您將收到歡迎或介紹性電子郵件。 這確認 Azure 備份可以在引發警報時向這些位址發送電子郵件。<br>

如果頻率設置為每小時摘要，並在一小時內引發並解決警報，則該頻率將不是即將進行的每小時摘要的一部分。

> [!NOTE]
>
> - 如果執行破壞性操作（如**使用刪除資料的停止保護**），則會發出警報，併發送電子郵件給訂閱擁有者、管理員和共同管理員，即使未為恢復服務保存庫配置通知也是如此。
> - 要為成功作業配置通知，請使用[日誌分析](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)。

## <a name="inactivating-alerts"></a>停用警報

要停用/解析活動警報，可以按一下與要停用的警報對應的清單項。 這將打開一個螢幕，顯示有關警報的詳細資訊，頂部有"停用"按鈕。 按一下此按鈕會將警報的狀態更改為"非活動"。 您還可以通過按右鍵與該警報對應的清單項並選擇"停用"來停用警報。

![RS Vault 警報停用](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>後續步驟

[使用 Azure 監視器監視 Azure 備份工作負荷](backup-azure-monitoring-use-azuremonitor.md)
