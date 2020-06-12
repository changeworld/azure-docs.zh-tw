---
title: 針對 SAP Hana 資料庫備份錯誤進行疑難排解
description: 說明使用 Azure 備份來備份 SAP Hana 資料庫時，如何針對可能發生的常見錯誤進行疑難排解。
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 5c1ad55a86e80808b9055fd1b34a2d72209464a2
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697076"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>針對 Azure 上的 SAP Hana 資料庫備份進行疑難排解

本文提供在 Azure 虛擬機器上備份 SAP Hana 資料庫的疑難排解資訊。 若要深入了解我們目前支援的 SAP Hana 備份案例，請參閱[案例支援](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="prerequisites-and-permissions"></a>必要條件和權限

設定備份之前，請先參閱[必要條件](tutorial-backup-sap-hana-db.md#prerequisites)和[預先註冊指令碼的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)區段。

## <a name="common-user-errors"></a>一般使用者錯誤

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **錯誤訊息**      | <span style="font-weight:normal">Azure 備份沒有執行備份所需的角色權限</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **可能的原因**    | 角色可能已遭到覆寫。                          |
| **建議的動作** | 若要解決此問題，請從 [探索 DB] 窗格中執行指令碼，或從[此處](https://aka.ms/scriptforpermsonhana)下載。 或者，將 'SAP_INTERNAL_HANA_SUPPORT' 角色新增至工作負載備份使用者 (AZUREWLBACKUPHANAUSER)。 |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| 錯誤訊息      | <span style="font-weight:normal">無法連線到 Hana 系統</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP Hana 執行個體可能已關閉。<br/>未設定 Azure 備份與 HANA 資料庫互動時所需的權限。 |
| **建議的動作** | 檢查 SAP Hana 資料庫是否已啟動。 如果資料庫已啟動且正在執行，請檢查是否已設定所有必要的權限。 如果遺漏任何權限，請執行[預先註冊指令碼](https://aka.ms/scriptforpermsonhana)來新增遺失的權限。 |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| 錯誤訊息      | <span style="font-weight:normal">指定的 SAP Hana 執行個體無效或找不到</span>  |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 單一 Azure VM 上的多個 SAP Hana 執行個體無法備份。 |
| **建議的動作** | 在您要備份的 SAP Hana 執行個體上執行[預先註冊指令碼](https://aka.ms/scriptforpermsonhana)。 如果問題仍持續發生，請連絡 Microsoft 支援服務。 |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| 錯誤訊息      | <span style="font-weight:normal">不支援指定的 SAP HANA 作業</span>              |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 適用於 SAP Hana 的 Azure 備份不支援在 SAP Hana 原生用戶端 (Studio/ Cockpit/ DBA Cockpit) 上執行增量備份和動作 |
| **建議的動作** | 如需詳細資訊，請參閱[此處](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| 錯誤訊息      | <span style="font-weight:normal">此 SAP Hana 資料庫不支援要求的備份類型</span>  |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | Azure 備份不支援增量備份和使用快照集進行備份 |
| **建議的動作** | 如需詳細資訊，請參閱[此處](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| 錯誤訊息      | <span style="font-weight:normal">備份記錄鏈結中斷</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 記錄備份目的地可能已從 backint 更新為檔案系統，或 backint 可執行檔可能已變更 |
| **建議的動作** | 請觸發完整備份以解決問題                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| 錯誤訊息      | <span style="font-weight:normal">偵測到 SDC 至 MDC 的升級</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP Hana 執行個體已從 SDC 升級至 MDC。 更新之後，備份將會失敗。 |
| **建議的動作** | 依照 [SDC 至 MDC 的升級](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid)中所述步驟來解決問題 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 錯誤訊息      | <span style="font-weight:normal">偵測到無效的 backint 設定</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 對 Azure 備份指定了錯誤的支援參數 |
| **建議的動作** | 檢查是否已設定下列 (backint) 參數：<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>如果主機中有以 backint 為基礎的參數，請將這些參數移除。 如果參數不存在於主機層級，但已在資料庫層級上手動修改，請將這些參數還原為適當的值 (如先前所述)。 或者，從 Azure 入口網站執行[停止保護並保留備份資料](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database)，然後選取 [繼續備份]。 |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|錯誤訊息  |還原的來源與目標系統不相容  |
|---------|---------|
|可能的原因   | 針對還原而選取的來源與目標系統不相容        |
|建議的動作   |   請確定您的還原案例不在下列可能不相容的還原清單中： <br><br>   **案例 1：** 還原期間無法重新命名 SYSTEMDB。  <br><br> **案例 2︰** 來源 - SDC 和目標 - MDC：來源資料庫無法還原為目標上的 SYSTEMDB 或租用戶 DB。 <br><br> **案例 3：** 來源 - MDC 和目標 - SDC：來源資料庫 (SYSTEMDB 或租用戶 DB) 無法還原至目標。 <br><br>  如需詳細資訊，請參閱 [SAP 支援啟動列](https://launchpad.support.sap.com)中的 **1642148** 註解。 |

## <a name="restore-checks"></a>還原檢查

### <a name="single-container-database-sdc-restore"></a>單一容器資料庫 (SDC) 還原

將 Hana 的單一容器資料庫 (SDC) 還原至另一部 SDC 機器時，請留意輸入內容。 資料庫名稱應該以小寫形式提供，並在括弧中附加 "sdc"。 Hana 執行個體會以大寫顯示。

假設已備份 SDC Hana 執行個體 "H21"。 備份項目頁面會將備份項目名稱顯示為 **"h21(sdc)"** 。 如果您嘗試將此資料庫還原到另一個目標 SDC (例如 H11)，則必須提供下列輸入。

![還原的 SDC 資料庫名稱](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

請注意下列幾點：

- 根據預設，還原的資料庫名稱會以備份項目名稱填入。 在此案例中為 h21(sdc)。
- 將目標選取為 H11「不會」自動變更已還原的資料庫名稱。 **其應該會編輯為 h11(sdc)** 。 關於 SDC，還原的資料庫名稱將會是小寫字母的目標執行個體識別碼加上以括弧括住的 'sdc'。
- 由於 SDC 只能有單一資料庫，因此您也需要按一下核取方塊，才能允許以復原點資料覆寫現有的資料庫資料。
- Linux 會區分大小寫。 因此請小心保留大小寫。

### <a name="multiple-container-database-mdc-restore"></a>多個容器資料庫 (MDC) 還原

在 Hana 的多個容器資料庫中，標準設定為 SYSTEMDB + 1 或更多租用戶 DB。 還原整個 SAP Hana 執行個體表示還原 SYSTEMDB 和租用戶 DB。 資料庫會先還原 SYSTEMDB，然後再繼續處理租用戶 DB。 系統資料庫基本上是指覆寫所選目標上的系統資訊。 此還原也會覆寫目標執行個體中的 BackInt 相關資訊。 因此，在系統資料庫還原到目標執行個體之後，應再次執行預先註冊指令碼。 只有這麼做之後，後續的租用戶 DB 才能還原成功。

## <a name="back-up-a-replicated-vm"></a>備份已複寫的 VM

### <a name="scenario-1"></a>實例 1

已使用 Azure Site Recovery 或 Azure VM 備份來複寫原始 VM。 已建立新的 VM 來模擬舊的 VM。 也就是說，設定會完全相同。 (這是因為原始 VM 已刪除，而還原是從 VM 備份或 Azure Site Recovery 進行)。

此案例可能包含兩種可能情況。 了解如何在這兩種情況下備份已複寫的 VM：

1. 建立的新 VM 具有相同名稱，且與已刪除的 VM 位於相同資源群組和訂用帳戶中。

    - 此擴充已存在於 VM 上，但未讓任何服務看到
    - 執行預先註冊指令碼
    - 在 Azure 入口網站中，對同一部機器註冊擴充 ([備份] -> [檢視詳細資料] -> 選取相關的 Azure VM -> 重新註冊)
    - 已存在的已備份資料庫 (源自已刪除的 VM) 應可順利地開始備份

2. 建立的新 VM 具有下列其中一項：

    - 名稱與已刪除的 VM 不同
    - 名稱與已刪除的 VM 相同，但位於不同的資源群組或訂用帳戶中 (相較於已刪除的 VM)

    如果是這種情況，請執行下列步驟：

    - 此擴充已存在於 VM 上，但未讓任何服務看到
    - 執行預先註冊指令碼
    - 如果您探索並保護新的資料庫，就會開始在入口網站中看到重複的作用中資料庫。 若要避免這種情況，請[停止使用保留資料來保護舊資料庫](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)。 然後繼續進行其餘步驟。
    - 探索資料庫以啟用備份
    - 在這些資料庫上啟用備份
    - 已存在的已備份資料庫 (源自已刪除的 VM) 將繼續儲存在保存庫中 (根據原則保留其備份)

### <a name="scenario-2"></a>案例 2

已使用 Azure Site Recovery 或 Azure VM 備份來複寫原始 VM。 新 VM 是根據內容而建立的，用來作為範本。 這是具有新 SID 的新 VM。

請遵循下列步驟，在新的 VM 上啟用備份：

- 擴充已存在於 VM 上，但未讓任何服務看到
- 執行預先註冊指令碼。 視新 VM 的 SID 而定，可能會有兩種情況：
  - 原始 VM 和新 VM 的 SID 相同。 預先註冊指令碼將會順利執行。
  - 原始 VM 和新 VM 的 SID 不同。 預先註冊指令碼將會失敗。 請連絡支援人員以取得此案例的協助。
- 探索您想要備份的資料庫
- 在這些資料庫上啟用備份

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>相同 VM 上的 SDC 版本升級或 MDC 版本升級

若要在升級至 OS、SDC 版本變更或 MDC 版本變更時不會造成 SID 變更，可以依照下列方式處理：

- 請確認目前 [Azure 備份支援](sap-hana-backup-support-matrix.md#scenario-support)新的 OS 版本、SDC 或 MDC 版本
- [停止使用保留資料](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)來保護資料庫
- 執行升級或更新
- 重新執行預先註冊指令碼。 通常，升級程序會移除必要角色。 執行預先註冊指令碼有助於驗證所有必要角色
- 恢復資料庫的保護功能

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>在不變更 SID 的情況下進行 SDC 至 MDC 的升級

若要在從 SDC 升級至 MDC 時，不造成 SID 變更，可以依照下列方式處理：

- 確定目前 [Azure 備份](sap-hana-backup-support-matrix.md#scenario-support)支援新的 MDC 版本
- [停止使用保留資料來保護](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)舊 SDC 資料庫
- 執行升級。 完成之後，Hana 系統現在就是具有系統 DB 和租用戶 DB 的 MDC
- 重新執行[預先註冊指令碼](https://aka.ms/scriptforpermsonhana)
- 在 Azure 入口網站中，對同一部機器註冊擴充 ([備份] -> [檢視詳細資料] -> 選取相關的 Azure VM -> 重新註冊)
- 針對相同 VM，按一下 [重新探索 DB]。 此動作應會將步驟 3 中的新 DB 顯示為 SYSTEMDB 和租用戶 DB，而不是 SDC
- 較舊的 SDC 資料庫會繼續存在於保存庫中，並根據原則來保留舊的備份資料
- 設定這些資料庫的備份

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>在變更 SID 的情況下進行 SDC 至 MDC 的升級

若要在從 SDC 升級至 MDC 時，造成 SID 變更，可以依照下列方式處理：

- 確定目前 [Azure 備份](sap-hana-backup-support-matrix.md#scenario-support)支援新的 MDC 版本
- **停止使用保留資料來保護**舊 SDC 資料庫
- 執行升級。 完成之後，Hana 系統現在就是具有系統 DB 和租用戶 DB 的 MDC
- 重新執行[預先註冊指令碼](https://aka.ms/scriptforpermsonhana)，並提供正確的詳細資料 (新的 SID 和 MDC)。 由於 SID 的變更，您可能會遇到無法成功執行指令碼的問題。 如果您遇到問題，請連絡 Azure 備份支援。
- 在 Azure 入口網站中，對同一部機器註冊擴充 ([備份] -> [檢視詳細資料] -> 選取相關的 Azure VM -> 重新註冊)
- 針對相同 VM，按一下 [重新探索 DB]。 此動作應會將步驟 3 中的新 DB 顯示為 SYSTEMDB 和租用戶 DB，而不是 SDC
- 較舊的 SDC 資料庫會繼續存在於保存庫中，並根據原則來保留舊的備份資料
- 設定這些資料庫的備份

## <a name="re-registration-failures"></a>重新註冊失敗

在觸發重新註冊作業之前，請先檢查下列一個或多個徵兆：

- 所有作業 (例如備份、還原和設定備份) 都在 VM 上失敗，並出現下列其中一個錯誤代碼：**WorkloadExtensionNotReachable、UserErrorWorkloadExtensionNotInstalled、WorkloadExtensionNotPresent、WorkloadExtensionDidntDequeueMsg**。
- 如果備份項目的 [備份狀態] 區域顯示 [無法連線]，請排除可能會導致相同狀態的所有其他原因：

  - 缺少在 VM 上執行備份相關作業的權限
  - VM 已關閉，因此無法進行備份
  - 網路問題

這些徵兆可能是因下列一個或多個原因而發生：

- 已從入口網站刪除或卸載擴充。
- VM 已透過就地磁碟還原來還原到原本狀態。
- VM 已關閉一段較長的時間，因此其中的擴充設定已過期。
- VM 已刪除，已建立另一個相同名稱的 VM，並且與已刪除的 VM 位於相同資源群組中。

針對上述情況，我們建議您在 VM 上觸發重新註冊作業。

## <a name="next-steps"></a>後續步驟

- 如需在 Azure VM 上備份 SAP Hana 資料庫的相關資訊，請參閱[常見問題](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) 。
