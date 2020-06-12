---
title: 常見問題集 - 備份 Azure VM 上的 SAP Hana 資料庫
description: 在本文中，您可以針對使用 Azure 備份服務來備份 SAP Hana 資料庫時的常見問題，找到相關解答。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 56f98dddb00eb3ffc87eb27da73066de807a1ee1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701021"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常見問題 - 備份 Azure VM 上的 SAP Hana 資料庫

本文將解答使用 Azure 備份服務來備份 SAP Hana 資料庫時的常見問題。

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支援多少次完整備份？

我們每天僅支援一次完整備份。 您無法在同一天觸發差異備份和完整備份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的備份作業是否會建立警示？

否。 成功的備份作業不會產生警示。 只有失敗的備份作業會傳送警示。 [這裡](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)詳載了入口網站警示的行為。 不過，如果您希望成功的作業也要有警示，您可以使用 [Azure 監視器](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>是否可以在 [備份作業] 功能表中看到已排程的備份作業？

[備份作業] 功能表只會顯示隨選備份作業。 針對排程的作業，請使用 [Azure 監視器](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未來的資料庫會自動加入以進行備份嗎？

否，目前無此支援。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果我從執行個體中刪除資料庫，備份將有何情況？

如果從 SAP Hana 執行個體中捨棄資料庫，系統仍會嘗試進行資料庫備份。 這表示已刪除的資料庫會開始在 [備份項目] 下方顯示為狀況不良，且仍受到保護。
若要停止保護此資料庫，正確方式是在此資料庫上執行**停止以刪除的資料備份**。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果我在資料庫受到保護後變更其名稱，將有何行為？

重新命名的資料庫會被視為新的資料庫。 因此，服務會將這種情況視同找不到資料庫，且讓備份失敗。 重新命名的資料庫會顯示為新的資料庫，且必須進行保護設定。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>備份 Azure VM 上的 SAP Hana 資料庫的必要條件為何？

請參閱[必要條件](tutorial-backup-sap-hana-db.md#prerequisites)和[預先註冊指令碼的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)小節。

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>應設定哪些權限，Azure 才能備份 SAP Hana 資料庫？

執行預先註冊指令碼，可設定讓 Azure 備份 SAP Hana 資料庫的必要權限。 您可以在[這裡](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)找到預先註冊指令碼的其他功能。

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>將 SAP Hana 從 SDC 遷移至 MDC 之後，備份是否可正常運作？

請參閱疑難排解指南的[這一節](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid)。

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>是否可以針對虛擬 IP (負載平衡器) 設定 Azure Hana 備份，而非針對虛擬機器進行設定？

我們目前未提供針對虛擬 IP 設定解決方案的功能。 我們需要以虛擬機器執行解決方案。

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>我有 SAP Hana 系統複寫 (HSR)，在此設定下該如何設定備份？

HSR 的主要和次要節點會被視為兩個獨立且不相關的 VM。 您必須在主要節點上設定備份，而當容錯移轉發生時，您需要在次要節點上設定備份 (此時會成為主要節點)。 備份不會自動「容錯移轉」至其他節點。

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>如何將隨選備份移至本機檔案系統，而非 Azure 保存庫？

1. 等候目前執行中的備份在所需的資料庫上完成作業 (從 Studio 查看是否已完成)
1. 停用記錄備份，並使用下列步驟，將所需 DB 的目錄備份設定為 [檔案系統]：
1. 按兩下 [SYSTEMDB] -> [設定] -> [選取資料庫] -> [篩選 (記錄)]
    1. 將 enable_auto_log_backup 設定為 [否]
    1. 將 log_backup_using_backint 設定為 [False]
1. 在所需的資料庫上進行隨選備份，並等候備份和目錄備份完成。
1. 還原為先前的設定，讓備份流向 Azure 保存庫：
    1. 將 enable_auto_log_backup 設定為 [是]
    1. 將 log_backup_using_backint 設定為 [True]

## <a name="restore"></a>還原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>為何我看不到我想要將資料庫還原到其中的 Hana 系統？

請檢查是否符合還原至目標 SAP Hana 執行個體的所有必要條件。 如需詳細資訊，請參閱[必要條件 - 還原 Azure VM 中的 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>資料庫的覆寫 DB 還原為何會失敗？

請確定在還原時已選取 [強制覆寫] 選項。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>為何我會看到「還原的來源與目標系統不相容」錯誤？

請參閱 SAP Hana Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)，以查看目前支援的還原類型。

## <a name="next-steps"></a>後續步驟

了解如何[備份在 Azure VM 上執行的 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。
