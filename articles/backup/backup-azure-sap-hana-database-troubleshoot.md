---
title: 針對 SAP Hana 資料庫備份錯誤進行疑難排解
description: 說明如何針對使用 Azure 備份備份 SAP Hana 資料庫時可能發生的常見錯誤進行疑難排解。
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 8872cfe87df9b8d0553d777f72fe7102d08dea4d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382479"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>針對 Azure 上 SAP Hana 資料庫的備份進行疑難排解

本文提供在 Azure 虛擬機器上備份 SAP Hana 資料庫的疑難排解資訊。 如需目前支援的 SAP Hana 備份案例的詳細資訊，請參閱[案例支援](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="prerequisites-and-permissions"></a>必要條件和許可權

設定備份之前，請參閱[必要條件](tutorial-backup-sap-hana-db.md#prerequisites)和[預先註冊腳本的執行](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

## <a name="common-user-errors"></a>一般使用者錯誤

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **錯誤訊息**      | <span style="font-weight:normal">Azure 備份沒有執行備份所需的角色許可權</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **可能的原因**    | 角色可能已遭到覆寫。                          |
| **建議的動作** | 若要解決此問題，請從 [**探索資料庫**] 窗格執行腳本，或在[這裡](https://aka.ms/scriptforpermsonhana)下載。 或者，將「SAP_INTERNAL_HANA_SUPPORT」角色新增至工作負載備份使用者（AZUREWLBACKUPHANAUSER）。 |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| 錯誤訊息      | <span style="font-weight:normal">無法連接到 HANA 系統</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP Hana 實例可能已關閉。<br/>不會設定 Azure 備份與 HANA 資料庫互動所需的許可權。 |
| **建議的動作** | 檢查 SAP Hana 資料庫是否已啟動。 如果資料庫已啟動且正在執行，請檢查是否已設定所有必要的許可權。 如果遺漏任何許可權，請執行[preregistration 腳本](https://aka.ms/scriptforpermsonhana)來新增遺失的許可權。 |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| 錯誤訊息      | <span style="font-weight:normal">指定的 SAP Hana 實例無效或找不到</span>  |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 單一 Azure VM 上的多個 SAP Hana 實例無法備份。 |
| **建議的動作** | 在您要備份的 SAP Hana 實例上執行[preregistration 腳本](https://aka.ms/scriptforpermsonhana)。 如果問題仍然持續發生，請洽詢 Microsoft 支援服務。 |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| 錯誤訊息      | <span style="font-weight:normal">不支援指定的 SAP Hana 作業</span>              |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 適用于 SAP Hana 的 Azure 備份不支援在 SAP Hana 原生用戶端上執行的增量備份和動作（Studio/駕駛中心/DBA 環境） |
| **建議的動作** | 如需詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| 錯誤訊息      | <span style="font-weight:normal">此 SAP Hana 資料庫不支援要求的備份類型</span>  |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | Azure 備份不支援使用快照集進行增量備份和備份 |
| **建議的動作** | 如需詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| 錯誤訊息      | <span style="font-weight:normal">備份記錄鏈已中斷</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 記錄備份目的地可能已從 backint 更新至檔案系統，或可能已變更 backint 可執行檔 |
| **建議的動作** | 觸發完整備份以解決問題                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| 錯誤訊息      | <span style="font-weight:normal">還原的來源和目標系統不相容</span>    |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 還原的目標系統與來源不相容 |
| **建議的動作** | 請參閱 SAP 附注[1642148](https://launchpad.support.sap.com/#/notes/1642148) ，以瞭解目前支援的還原類型 |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| 錯誤訊息      | <span style="font-weight:normal">已偵測到 MDC 升級 SDC</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP Hana 實例已從 SDC 升級至 MDC。 更新之後，備份將會失敗。 |
| **建議的動作** | 遵循[從 SAP Hana 1.0 升級到2.0 一節](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)中所列的步驟來解決問題 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 錯誤訊息      | <span style="font-weight:normal">偵測到不正確 backint 設定</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 針對 Azure 備份不正確地指定了支援參數 |
| **建議的動作** | 檢查是否已設定下列（backint）參數：<br/>\* [catalog_backup_using_backint： true]<br/>\* [enable_accumulated_catalog_backup： false]<br/>\* [parallel_data_backup_backint_channels： 1]<br/>\* [log_backup_timeout_s：900）]<br/>\* [backint_response_timeout： 7200]<br/>如果主機中有以 backint 為基礎的參數，請將它們移除。 如果參數不存在於主機層級，但已在資料庫層級手動修改，請將它們還原為適當的值，如先前所述。 或者，執行 [[停止保護] 並保留](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database)Azure 入口網站中的備份資料，然後選取 [**繼續備份**]。 |

## <a name="restore-checks"></a>還原檢查

### <a name="single-container-database-sdc-restore"></a>單一容器資料庫（SDC）還原

將 HANA 的單一容器資料庫（SDC）還原至另一部 SDC 機器時，請注意輸入。 資料庫名稱應該以小寫形式提供，並在括弧中附加 "sdc"。 HANA 實例會以大寫顯示。

假設已備份 SDC HANA 實例 "H21"。 [備份專案] 頁面會將備份專案名稱顯示為 **"h21 （sdc）"** 。 如果您嘗試將此資料庫還原到另一個目標 SDC （例如 H11），則必須提供下列輸入。

![還原的 SDC 資料庫名稱](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

請注意下列幾點：

- 根據預設，還原的資料庫名稱會填入備份專案名稱。 在此情況下，h21 （sdc）。
- 選取目標為 H11 時，不會自動變更已還原的資料庫名稱。 **它應該會編輯為 h11 （sdc）** 。 關於 SDC，還原的資料庫名稱將會是以小寫字母括住的目標實例識別碼，而 ' SDC ' 會附加在方括弧中。
- 由於 SDC 只能有單一資料庫，因此您也需要按一下核取方塊，以允許以復原點資料覆寫現有的資料庫資料。
- Linux 會區分大小寫。 因此請小心保留大小寫。

### <a name="multiple-container-database-mdc-restore"></a>多個容器資料庫（MDC）還原

在 HANA 的多個容器資料庫中，標準設定為 SYSTEMDB + 1 或更多租使用者 Db。 還原整個 SAP Hana 實例表示還原 SYSTEMDB 和租使用者 Db。 其中一個還原會先 SYSTEMDB，然後再繼續進行租使用者資料庫。 系統資料庫基本上是指覆寫所選目標上的系統資訊。 這項還原也會覆寫目標實例中的 BackInt 相關資訊。 因此，在系統資料庫還原到目標實例之後，再次執行預先註冊腳本。 只有之後，後續的租使用者資料庫還原才會成功。

## <a name="upgrading-from-sap-hana-10-to-20"></a>從 SAP Hana 1.0 升級至2。0

如果您要保護 SAP Hana 1.0 資料庫，並想要升級至2.0，請執行下列步驟：

- 使用 [保留舊的 SDC 資料庫的資料] 來[停止保護](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)。
- 執行升級。 完成後，HANA 現在會有系統資料庫和租使用者資料庫的 MDC
- 重新執行[預先註冊腳本](https://aka.ms/scriptforpermsonhana)，並提供正確的詳細資料（sid 和 mdc）。
- 在 Azure 入口網站中重新登錄相同電腦的延伸模組（備份 > 視圖詳細資料-> 選取相關的 Azure VM-> 重新註冊）。
- 針對相同的 VM 按一下 [重新探索 Db]。 此動作應該會顯示步驟2中具有正確詳細資料的新資料庫（SYSTEMDB 和租使用者 DB，而非 SDC）。
- 設定這些新資料庫的備份。

## <a name="upgrading-without-an-sid-change"></a>在沒有 SID 變更的情況下升級

升級為不會造成 SID 變更的 OS 或 SAP Hana，可以如下所述處理：

- 使用保留資料庫資料的[停止保護](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 執行升級。
- 重新執行[預先註冊腳本](https://aka.ms/scriptforpermsonhana)。 通常，我們看到升級程式會移除必要的角色。 執行預先註冊腳本將有助於驗證所有必要的角色。
- 再次[繼續保護](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)資料庫

## <a name="next-steps"></a>後續步驟

- 查看有關在 Azure Vm 上備份 SAP Hana 資料庫的[常見問題](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm)
