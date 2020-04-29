---
title: 常見問題集 - 備份 Azure VM 上的 SAP Hana 資料庫
description: 在本文中，您將找到使用 Azure 備份服務來備份 SAP Hana 資料庫的常見問題解答。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80155387"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常見問題-在 Azure Vm 上備份 SAP Hana 資料庫

本文會回答有關使用 Azure 備份服務來備份 SAP Hana 資料庫的常見問題。

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支援多少完整備份？

我們每天僅支援一次完整備份。 您不能在同一天觸發差異備份和完整備份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的備份作業是否會建立警示？

否。 成功的備份作業不會產生警示。 只有失敗的備份作業會傳送警示。 入口網站警示的詳細行為記載于[此處](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)。 不過，如果您有興趣取得成功作業的警示，您可以使用[Azure 監視器](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>我可以在 [備份作業] 功能表中看到已排程的備份工作嗎？

[備份作業] 功能表只會顯示特定的備份作業。 若為排程工作，請使用[Azure 監視器](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未來的資料庫會自動加入以進行備份嗎？

否，目前不支援這種情況。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果我從實例中刪除資料庫，備份會發生什麼事？

如果從 SAP Hana 實例卸載資料庫，仍然會嘗試資料庫備份。 這表示已刪除的資料庫會開始顯示為 [**備份專案**] 底下的 [狀況不良]，而且仍然受到保護。
停止保護此資料庫的正確方式，是在此資料庫上執行「**停止備份並刪除資料**」。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果我在保護資料庫之後變更其名稱，此行為會是什麼？

重新命名的資料庫會被視為新的資料庫。 因此，如果找不到資料庫，而且備份失敗，服務就會將這種情況視為相同。 重新命名的資料庫會顯示為新的資料庫，而且必須設定為要保護。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>備份 Azure VM 上 SAP Hana 資料庫的必要條件為何？

請參閱[必要條件](tutorial-backup-sap-hana-db.md#prerequisites)和[預先註冊腳本的執行](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>應該為 Azure 設定哪些許可權才能備份 SAP Hana 資料庫？

執行預先註冊腳本會設定必要的許可權，以允許 Azure 備份 SAP Hana 資料庫。 您可以在[這裡](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)找到更多預先註冊腳本的功能。

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>將 SAP Hana 從1.0 遷移至2.0 之後，備份是否可正常執行？

請參閱疑難排解指南的[這一節](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)。

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>是否可以針對虛擬 IP （負載平衡器）設定 Azure HANA 備份，而不是虛擬機器？

我們目前沒有針對虛擬 IP 設定解決方案的功能。 我們需要虛擬機器來執行解決方案。

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>我有 SAP Hana 系統複寫（HSR），該如何設定此安裝程式的備份？

HSR 的主要和次要節點會被視為兩個個別、不相關的 Vm。 您需要在主要節點上設定備份，而當故障轉換發生時，您需要在次要節點上設定備份（現在會成為主要節點）。 不會自動將備份「故障切換」至其他節點。

## <a name="restore"></a>還原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>為什麼我看不到我想要將資料庫還原到其中的 HANA 系統？

檢查是否符合還原至目標 SAP Hana 實例的所有必要條件。 如需詳細資訊，請參閱[必要條件-在 AZURE VM 中還原 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>為什麼我的資料庫的覆寫資料庫還原失敗？

請確定在還原時已選取 [**強制覆寫**] 選項。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>為什麼會看到「還原的來源和目標系統不相容」錯誤？

請參閱 SAP Hana 附注[1642148](https://launchpad.support.sap.com/#/notes/1642148) ，以查看目前支援哪些還原類型。

## <a name="next-steps"></a>後續步驟

瞭解如何備份在 Azure Vm 上執行的[SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。
