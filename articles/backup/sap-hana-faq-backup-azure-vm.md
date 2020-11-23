---
title: 常見問題集 - 備份 Azure VM 上的 SAP Hana 資料庫
description: 在本文中，您可以針對使用 Azure 備份服務來備份 SAP Hana 資料庫時的常見問題，找到相關解答。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: bf662600bafcd18b00c8f8d3b673fc3f9c110aca
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95400202"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常見問題 - 備份 Azure VM 上的 SAP Hana 資料庫

本文將解答使用 Azure 備份服務來備份 SAP Hana 資料庫時的常見問題。

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支援多少次完整備份？

我們每天僅支援一次完整備份。 您無法在同一天觸發差異備份和完整備份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的備份作業是否會建立警示？

否。 成功的備份作業不會產生警示。 只有失敗的備份作業會傳送警示。 [這裡](./backup-azure-monitoring-built-in-monitor.md)詳載了入口網站警示的行為。 但是，如果您對成功的工作有興趣，就可以使用 [Azure 監視器](./backup-azure-monitoring-use-azuremonitor.md)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>是否可以在 [備份作業] 功能表中看到已排程的備份作業？

[備份作業] 功能表只會顯示隨選備份作業。 針對排程的作業，請使用 [Azure 監視器](./backup-azure-monitoring-use-azuremonitor.md)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未來的資料庫會自動加入以進行備份嗎？

否，目前不支援這種情況。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果我從執行個體中刪除資料庫，備份將有何情況？

如果從 SAP Hana 執行個體中捨棄資料庫，系統仍會嘗試進行資料庫備份。 這表示已刪除的資料庫會開始在 [備份項目] 下方顯示為狀況不良，且仍受到保護。
若要停止保護此資料庫，正確方式是在此資料庫上執行 **停止以刪除的資料備份**。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果我在資料庫受到保護後變更其名稱，將有何行為？

重新命名的資料庫會被視為新的資料庫。 因此，此服務會將這種情況視為無法找到資料庫，而且將會使備份失敗。 重新命名的資料庫會顯示為新的資料庫，且必須進行保護設定。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>備份 Azure VM 上的 SAP Hana 資料庫的必要條件為何？

請參閱[必要條件](tutorial-backup-sap-hana-db.md#prerequisites)和[預先註冊指令碼的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)小節。

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>應設定哪些許可權，Azure 才能備份 SAP Hana 資料庫？

執行預先註冊指令碼，可設定讓 Azure 備份 SAP Hana 資料庫的必要權限。 您可以在 [這裡](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)找到預先註冊腳本的用途。

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>將 SAP Hana 從 SDC 遷移至 MDC 之後，備份是否可正常運作？

請參閱疑難排解指南的[這一節](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid)。

### <a name="what-should-be-done-while-upgrading-within-the-same-version"></a>在相同版本中進行升級時，應該怎麼做？

請參閱疑難排解指南中的 [這一節](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm) 。

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>是否可以針對虛擬 IP (負載平衡器) 設定 Azure Hana 備份，而非針對虛擬機器進行設定？

目前我們不能單獨針對虛擬 IP 設定解決方案。 我們需要以虛擬機器執行解決方案。

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>如何將隨選備份移至本機檔案系統，而非 Azure 保存庫？

1. 等候目前正在執行的備份在所需的資料庫上完成， (從 studio 檢查完成) 。
1. 停用記錄備份，並使用下列步驟，將所需 DB 的目錄備份設定為 [檔案系統]：
1. 按兩下 [SYSTEMDB] -> [設定] -> [選取資料庫] -> [篩選 (記錄)]
    1. 將 enable_auto_log_backup 設定為 [ **否**]。
    1. 將 catalog_backup_using_backint 設定為 **false**。
1. 針對所需的資料庫， (完整/差異/增量) 進行隨選備份，並等候備份和目錄備份完成。
1. 如果您也想要將記錄備份移至檔案系統，請將 enable_auto_log_backup 設定為 **[是]**。
1. 還原為先前的設定，讓備份流向 Azure 保存庫：
    1. 將 enable_auto_log_backup 設定為 **[是]**。
    1. 將 catalog_backup_using_backint 設定為 **true**。

>[!NOTE]
>將備份移至本機檔案系統，並再次切換回 Azure 保存庫，可能會導致保存庫中記錄備份的記錄鏈中斷。 這將會觸發完整備份，一旦成功完成，就會開始備份記錄。

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>如何使用 SAP Hana 備份搭配我的 HANA 複寫設定？

目前，Azure 備份沒有了解 HSR 設定的功能。 這表示 HSR 的主要和次要節點將被視為兩個個別、不相關的 Vm。 您需要先在主要節點上設定備份。 當發生容錯移轉時，必須在次要節點上設定備份， (現在會變成主要節點) 。 備份不會自動容錯移轉至另一個節點。

若要在任何指定的時間點備份 active (主要) 節點的資料，您可以將 **保護切換** 至次要節點，此節點現在會在容錯移轉之後變成主要節點。

若要執行此 **切換保護**，請遵循下列步驟：

- 在主要複本上使用保留資料) [停止保護](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (
- 在次要節點上執行[預先註冊腳本](https://aka.ms/scriptforpermsonhana)
- 探索次要節點上[的資料庫](tutorial-backup-sap-hana-db.md#discover-the-databases)，並在其上[設定備份](tutorial-backup-sap-hana-db.md#configure-backup)

在每次容錯移轉之後，必須手動執行這些步驟。 除了 Azure 入口網站之外，您還可以透過命令列/HTTP REST 來執行這些步驟。 若要將這些步驟自動化，您可以使用 Azure runbook。

以下是必須如何執行 **交換器保護** 的詳細範例：

在此範例中，您有兩個節點-節點 1 (主要) 和節點 2 (次要) 在 HSR 設定中。  在節點1上設定備份。 如前所述，請不要嘗試在節點2上設定備份。

發生第一次容錯移轉時，節點2會變成主要複本。 如此一來，

1. 使用 [保留資料] 選項停止保護節點 1 (先前的主要) 。
1. 在節點2上執行預先註冊腳本 (這現在是主要) 。
1. 探索節點2上的資料庫、指派備份原則，以及設定備份。

然後，在節點2上觸發第一次完整備份，完成之後，就會開始記錄備份。

當發生下一個容錯移轉時，節點1會再次變成主節點，而節點2會變成次要。 現在重複此程式：

1. 使用 [保留資料] 選項停止保護節點2。
1. 在節點1上執行預先註冊的腳本，該腳本會再次成為主要 () 
1. 然後使用必要的原則 (繼續執行節點1上的 [備份](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) ，因為先前在節點 1) 上已停止備份。

然後，系統會再次在節點1上觸發完整備份，然後在完成後，記錄備份便會啟動。

## <a name="restore"></a>還原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>為何我看不到我想要將資料庫還原到其中的 Hana 系統？

請檢查是否符合還原至目標 SAP Hana 執行個體的所有必要條件。 如需詳細資訊，請參閱[必要條件 - 還原 Azure VM 中的 SAP Hana 資料庫](./sap-hana-db-restore.md#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>資料庫的覆寫 DB 還原為何會失敗？

請確定在還原時已選取 [強制覆寫] 選項。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>為何我會看到「還原的來源與目標系統不相容」錯誤？

請參閱 SAP Hana Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)，以查看目前支援的還原類型。

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>我可以使用在 SLES 上執行的資料庫備份來還原至 RHEL HANA 系統，反之亦然？

是的，您可以使用在 SLES 上執行的 HANA 資料庫上觸發的串流備份，將其還原至 RHEL HANA 系統，反之亦然。 也就是說，您可以使用串流備份來還原跨作業系統。 不過，您必須確定您想要還原的 HANA 系統，以及用於還原的 HANA 系統，都是根據 SAP 進行還原的相容性。 請參閱 SAP Hana 附注 [1642148](https://launchpad.support.sap.com/#/notes/1642148) ，以查看哪些還原類型相容。

## <a name="policy"></a>原則

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>SAP Hana 備份建立新原則時，可使用不同的選項

建立原則之前，您應該先清楚 RPO 和 RTO 的需求，以及其相關的成本含意。

RPO (復原點目標) 指出使用者/客戶可接受的資料遺失量。 這取決於記錄備份頻率。 更頻繁的記錄備份表示 RPO 較低，Azure 備份服務所支援的最小值為15分鐘。 因此，記錄備份頻率可能是15分鐘或更高。

RTO (復原時間-目標) 指出資料在資料遺失案例之後，應還原到最後一個可用時間點的速度。 這取決於 HANA 所採用的復原策略，這通常取決於還原所需的檔案數目。 這也會影響成本，下表則有助於瞭解所有案例及其含意。

|備份原則  |復原時間目標 (RTO)  |成本  |
|---------|---------|---------|
|每日完整 + 記錄     |   最快，因為我們只需要一份完整複製 + 所需的記錄來進行時間點還原      |    Costliest 選項，因為每天都會進行完整複製，因此會在後端累積更多資料，直到保留時間為止   |
|每週完整 + 每日差異 + 記錄     |   比上述選項慢，但速度比下一個選項更慢，因為我們需要一個完整複製 + 一個差異複本 + 記錄以進行時間點還原      |    成本較低的選項，因為每日差異通常小於 full，且一周只會執行一次完整複製      |
|每週完整 + 每日增量 + 記錄     |  最慢，因為我們需要一個完整複本 + ' n ' 個增量 + 記錄以進行時間點恢復       |     最便宜的選項，因為每日增量將會小於差異，而且只會每週執行一次完整複製    |

> [!NOTE]
> 上述選項是最常見的選項，但不是唯一的選項。 例如，您可以使用每週完整備份 + 差異兩次以上的記錄。

因此，您可以根據 RPO 和 RTO 目標和成本考慮來選取原則變異。

### <a name="impact-of-modifying-a-policy"></a>修改原則的影響

在判斷將備份專案原則從原則 1 (P1) 切換至原則 2 (P2) 或編輯原則 1 (P1) 的影響時，應該牢記一些原則。

- 所有變更也適用于追溯。 最新的備份原則也會套用至稍早所建立的復原點。 例如，假設每日完整保留期為30天，並根據目前作用中的原則來採用10個復原點。 如果每日完整保留期變更為10天，則先前的時間點的到期時間也會重新計算為開始時間 + 10 天，並在過期時刪除。
- 變更範圍也包括備份的日期、備份類型和保留期。 例如：如果在星期日將原則從每日完整變更為每週完整，則所有先前未在星期日執行的完整備份都會標示為刪除。
- 在子系為使用中/尚未過期之前，不會刪除父系。 每個備份類型都有根據目前作用中原則的到期時間。 但在後續的 ' 差異 '、「增量」和「記錄」中，會將完整備份類型視為父代。 「差異」和「記錄」不是其他任何人的家長。 「累加」可以是後續「增量」的父系。 即使 ' parent ' 標示為要刪除，如果子 ' 差異 ' 或 ' logs ' 尚未過期，就不會實際刪除。 例如，如果在星期日將原則從每日完整變更為每週完整，則所有先前未在星期日的完整備份都會標示為刪除。 但在先前每日拍攝的記錄到期之前，它們都不會被刪除。 換句話說，它們會根據最新的記錄持續時間進行保留。 記錄到期後，就會刪除記錄和這些完整的備份。

使用這些原則，您可以閱讀下表以瞭解原則變更的含意。

|舊原則/新原則  |每日完整備份 + 記錄  | 每週完整備份 + 每日差異 + 記錄  |每週完整 + 每日增量 + 記錄  |
|---------|---------|---------|---------|
|每日完整備份 + 記錄     |   -      |    先前未在一周相同日的完整備份會標示為要刪除，但會保留到記錄保留期限之前     |    先前未在一周相同日的完整備份會標示為要刪除，但會保留到記錄保留期限之前     |
|每週完整備份 + 每日差異 + 記錄     |   先前的每週完整保留期會根據最新的原則重新計算。 會立即刪除先前的差異      |    -     |    會立即刪除先前的差異     |
|每週完整 + 每日增量 + 記錄     |     先前的每週完整保留期會根據最新的原則重新計算。 會立即刪除先前的增量    |     會立即刪除先前的增量    |    -     |

## <a name="next-steps"></a>後續步驟

了解如何[備份在 Azure VM 上執行的 SAP Hana 資料庫](./backup-azure-sap-hana-database.md)。
