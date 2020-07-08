---
title: 常見問題集 - 備份 Azure VM 上的 SAP Hana 資料庫
description: 在本文中，您可以針對使用 Azure 備份服務來備份 SAP Hana 資料庫時的常見問題，找到相關解答。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: ddc4af9a164de3a822e8aebd6c0a4db769ec62a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262577"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常見問題 - 備份 Azure VM 上的 SAP Hana 資料庫

本文將解答使用 Azure 備份服務來備份 SAP Hana 資料庫時的常見問題。

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支援多少次完整備份？

我們每天僅支援一次完整備份。 您不能在同一天觸發差異備份和完整備份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的備份作業是否會建立警示？

否。 成功的備份作業不會產生警示。 只有失敗的備份作業會傳送警示。 [這裡](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)詳載了入口網站警示的行為。 不過，如果您有興趣取得成功作業的警示，您可以使用[Azure 監視器](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>是否可以在 [備份作業] 功能表中看到已排程的備份作業？

[備份作業] 功能表只會顯示隨選備份作業。 針對排程的作業，請使用 [Azure 監視器](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未來的資料庫會自動加入以進行備份嗎？

否，目前不支援這種情況。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果我從執行個體中刪除資料庫，備份將有何情況？

如果從 SAP Hana 執行個體中捨棄資料庫，系統仍會嘗試進行資料庫備份。 這表示已刪除的資料庫會開始在 [備份項目] 下方顯示為狀況不良，且仍受到保護。
若要停止保護此資料庫，正確方式是在此資料庫上執行**停止以刪除的資料備份**。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果我在資料庫受到保護後變更其名稱，將有何行為？

重新命名的資料庫會被視為新的資料庫。 因此，如果找不到資料庫，而且備份失敗，服務就會將這種情況視為如此。 重新命名的資料庫會顯示為新的資料庫，且必須進行保護設定。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>備份 Azure VM 上的 SAP Hana 資料庫的必要條件為何？

請參閱[必要條件](tutorial-backup-sap-hana-db.md#prerequisites)和[預先註冊指令碼的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)小節。

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>應設定哪些許可權，才能讓 Azure 備份 SAP Hana 資料庫？

執行預先註冊指令碼，可設定讓 Azure 備份 SAP Hana 資料庫的必要權限。 您可以在[這裡](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)找到預先註冊指令碼的其他功能。

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>將 SAP Hana 從 SDC 遷移至 MDC 之後，備份是否可正常運作？

請參閱疑難排解指南的[這一節](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid)。

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>是否可以針對虛擬 IP (負載平衡器) 設定 Azure Hana 備份，而非針對虛擬機器進行設定？

目前我們沒有針對虛擬 IP 設定解決方案的功能。 我們需要以虛擬機器執行解決方案。

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

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>如何搭配使用 SAP Hana 備份與 HANA 複寫設定？

目前，Azure 備份沒有了解 HSR 設定的能力。 這表示 HSR 的主要和次要節點會被視為兩個不相關的個別 Vm。 首先，您必須在主要節點上設定備份。 當故障轉換發生時，必須在次要節點上設定備份（現在會成為主要節點）。 不會自動將備份故障切換到其他節點。

若要在任何指定的時間點備份來自作用中（主要）節點的資料，您可以將**保護切換**至次要節點，這在容錯回復後現在會變成主要節點。

若要執行此**交換器保護**，請遵循下列步驟：

- 在主要複本上[停止保護](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)（含保留資料）
- 在次要節點上執行[預先註冊腳本](https://aka.ms/scriptforpermsonhana)
- 探索次要節點上[的資料庫](tutorial-backup-sap-hana-db.md#discover-the-databases)，並[設定](tutorial-backup-sap-hana-db.md#configure-backup)其上的備份

這些步驟必須在每次故障切換之後手動執行。 除了 Azure 入口網站以外，您還可以透過命令列/HTTP REST 來執行這些步驟。 若要將這些步驟自動化，您可以使用 Azure runbook。

以下是如何執行**交換器保護**的詳細範例：

在此範例中，您在 HSR 設定中有兩個節點-Node 1 （主要）和節點2（次要）。  在節點1上設定備份。 如上所述，尚未嘗試在節點2上設定備份。

當第一次容錯移轉發生時，節點2會變成主要複本。 如此一來，

1. 使用 [保留資料] 選項停止保護節點1（前一個主要）。
1. 在節點2上執行預先註冊腳本（現在是主要複本）。
1. 探索節點2上的資料庫、指派備份原則，並設定備份。

然後在節點2上觸發第一次完整備份，然後在完成之後，記錄備份就會啟動。

當下一個故障轉換發生時，節點1會重新成為主要，而節點2會變成次要。 現在重複此程式：

1. 使用 [保留資料] 選項停止保護節點2。
1. 在節點1上執行預先註冊腳本（已再次成為主要複本）
1. 然後使用必要的原則繼續執行節點1上的[備份](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)（因為先前在節點1上的備份已停止）。

然後，系統會再次在節點1上觸發完整備份，而完成後，就會開機記錄備份。

## <a name="restore"></a>還原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>為何我看不到我想要將資料庫還原到其中的 Hana 系統？

請檢查是否符合還原至目標 SAP Hana 執行個體的所有必要條件。 如需詳細資訊，請參閱[必要條件 - 還原 Azure VM 中的 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>資料庫的覆寫 DB 還原為何會失敗？

請確定在還原時已選取 [強制覆寫] 選項。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>為何我會看到「還原的來源與目標系統不相容」錯誤？

請參閱 SAP Hana Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)，以查看目前支援的還原類型。

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>我可以使用在 SLES 上執行之資料庫的備份來還原到 RHEL HANA 系統，反之亦然？

是，您可以使用在 SLES 上執行的 HANA 資料庫所觸發的串流備份，將它還原到 RHEL HANA 系統，反之亦然。 也就是，使用串流備份可以進行跨 OS 還原。 不過，您必須確定您想要還原的 HANA 系統，以及用於還原的 HANA 系統，都可以根據 SAP 進行還原，這兩者都是相容的。 請參閱 SAP Hana 附注[1642148](https://launchpad.support.sap.com/#/notes/1642148) ，查看哪些還原類型相容。

## <a name="next-steps"></a>後續步驟

了解如何[備份在 Azure VM 上執行的 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。
