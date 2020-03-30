---
title: 常見問題集 - 備份 Azure VM 上的 SAP Hana 資料庫
description: 在本文中，瞭解有關使用 Azure 備份服務備份 SAP HANA 資料庫的常見問題的解答。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155387"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常見問題 - 在 Azure VM 上備份 SAP HANA 資料庫

本文回答了有關使用 Azure 備份服務備份 SAP HANA 資料庫的常見問題。

## <a name="backup"></a>Backup 

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支援多少個完整備份？

我們每天僅支援一個完整備份。 不能在同一天觸發差異備份和完整備份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的備份作業是否會建立警示？

否。 成功的備份作業不會產生警示。 只有失敗的備份作業會傳送警示。 [此處](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)記錄了門戶警報的詳細行為。 但是，如果您有興趣對成功作業有警報，則可以使用[Azure 監視器](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>我可以在"備份作業"功能表中查看計畫的備份作業嗎？

"備份作業"功能表將僅顯示臨時備份作業。 對於計畫作業，請使用[Azure 監視器](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未來的資料庫會自動加入以進行備份嗎？

否，當前不支援此功能。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果我從實例中刪除資料庫，備份將會發生什麼情況？

如果從 SAP HANA 實例中刪除資料庫，則仍嘗試資料庫備份。 這意味著已刪除的資料庫在 **"備份專案"** 下開始顯示為不正常，並且仍然受到保護。
停止保護此資料庫的正確方法是使用此資料庫上**刪除資料的停止備份**。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果我在資料庫受到保護後更改它的名稱，那麼行為將是什麼？

重命名的資料庫被視為新資料庫。 因此，服務將視此情況為找不到資料庫且備份失敗。 重命名的資料庫將顯示為新資料庫，必須配置為保護。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>在 Azure VM 上備份 SAP HANA 資料庫的先決條件是什麼？

請參閱[先決條件](tutorial-backup-sap-hana-db.md#prerequisites)和[預註冊腳本執行哪些](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>應該為 Azure 設置哪些許可權才能備份 SAP HANA 資料庫？

運行預註冊腳本將設置必要的許可權，以允許 Azure 備份 SAP HANA 資料庫。 您可以在此處找到預註冊腳本所做的更多[內容](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)。

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>將 SAP HANA 從 1.0 遷移到 2.0 後，備份會起作用嗎？

請參閱故障排除指南的[這一部分](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)。

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>是否可以針對虛擬 IP（負載等化器）而不是虛擬機器設置 Azure HANA 備份？

目前，我們無法單獨針對虛擬 IP 設置解決方案。 我們需要一個虛擬機器來執行解決方案。

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>我有一個 SAP HANA 系統複製 （HSR），我應該如何配置此設置的備份？

HSR 的主節點和輔助節點將被視為兩個單獨的、不相關的 VM。 您需要在主節點上配置備份，當發生容錯移轉時，需要在輔助節點（現在成為主節點）上配置備份。 沒有自動"容錯移轉"備份到其他節點。

## <a name="restore"></a>還原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>為什麼我看不到希望還原到的 HANA 系統？

檢查是否滿足還原目標 SAP HANA 實例的所有先決條件。 有關詳細資訊，請參閱先決條件[- 在 Azure VM 中還原 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>為什麼覆蓋資料庫還原失敗？

確保在還原時選擇了 **"強制覆蓋**"選項。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>為什麼我看到"用於還原的源系統和目標系統不相容"錯誤？

請參閱 SAP HANA 注釋[1642148，](https://launchpad.support.sap.com/#/notes/1642148)瞭解當前支援哪些還原類型。

## <a name="next-steps"></a>後續步驟

瞭解如何備份在 Azure VM 上運行的[SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。
