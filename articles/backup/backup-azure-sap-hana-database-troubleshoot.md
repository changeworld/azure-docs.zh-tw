---
title: 排除 SAP HANA 資料庫備份錯誤
description: 介紹如何使用 Azure 備份備份 SAP HANA 資料庫時可能出現的常見錯誤。
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459305"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>在 Azure 上對 SAP HANA 資料庫進行故障排除備份

本文提供用於在 Azure 虛擬機器上備份 SAP HANA 資料庫的故障排除資訊。 有關當前支援的 SAP HANA 備份方案的詳細資訊，請參閱[方案支援](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="prerequisites-and-permissions"></a>先決條件和許可權

在配置備份之前，請參閱[先決條件](tutorial-backup-sap-hana-db.md#prerequisites)和[預註冊腳本執行](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)哪些部分。

## <a name="common-user-errors"></a>常見使用者錯誤

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **錯誤訊息**      | <span style="font-weight:normal">Azure 備份沒有執行備份所需的角色許可權</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **可能的原因**    | 角色可能已被覆蓋。                          |
| **建議的動作** | 要解決此問題，請從 **"發現資料庫"** 窗格運行腳本，或[在此處](https://aka.ms/scriptforpermsonhana)下載該腳本。 或者，將"SAP_INTERNAL_HANA_SUPPORT"角色添加到工作負載備份使用者（AZUREWLBACKUPHANAUSER）。 |

### <a name="usererrorinopeninghanaodbcconnection"></a>使用者錯誤在打開哈納奧德布連接

| 錯誤訊息      | <span style="font-weight:normal">無法連接到 HANA 系統</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP HANA 實例可能已關閉。<br/>未設置 Azure 備份與 HANA 資料庫交互所需的許可權。 |
| **建議的動作** | 檢查 SAP HANA 資料庫是否已啟動。 如果資料庫已啟動並運行，請檢查是否設置了所有必需的許可權。 如果缺少任何許可權，則運行[預註冊腳本](https://aka.ms/scriptforpermsonhana)以添加缺少的許可權。 |

### <a name="usererrorhanainstancenameinvalid"></a>使用者錯誤"實例名稱無效"

| 錯誤訊息      | <span style="font-weight:normal">指定的 SAP HANA 實例無效或找不到</span>  |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 無法備份單個 Azure VM 上的多個 SAP HANA 實例。 |
| **建議的動作** | 在要備份的 SAP HANA 實例上運行[預註冊腳本](https://aka.ms/scriptforpermsonhana)。 如果問題仍然存在，請與 Microsoft 支援部門聯繫。 |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| 錯誤訊息      | <span style="font-weight:normal">不支援指定的 SAP HANA 操作</span>              |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP HANA 的 Azure 備份不支援對 SAP HANA 本機用戶端（工作室/駕駛艙/DBA 駕駛艙）執行的增量備份和操作 |
| **建議的動作** | 有關詳細資訊，請參閱[此處](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>使用者錯誤哈納波多不支援備份類型

| 錯誤訊息      | <span style="font-weight:normal">此 SAP HANA 資料庫不支援請求的備份類型</span>  |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | Azure 備份不支援使用快照的增量備份和備份 |
| **建議的動作** | 有關詳細資訊，請參閱[此處](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanalsnvalidationfailure"></a>使用者錯誤HANALS驗證失敗

| 錯誤訊息      | <span style="font-weight:normal">備份日誌鏈已斷開</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 記錄備份目標可能已由回音更新到檔案系統，或者返回檔可執行檔可能已更改 |
| **建議的動作** | 觸發完整備份以解決此問題                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>使用者錯誤可協調SrcTargetsystsems用於還原

| 錯誤訊息      | <span style="font-weight:normal">用於還原的源系統和目標系統不相容</span>    |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 用於還原的目標系統與源不相容 |
| **建議的動作** | 請參閱 SAP 說明[1642148，](https://launchpad.support.sap.com/#/notes/1642148)瞭解當前支援的還原類型 |

### <a name="usererrorsdctomdcupgradedetected"></a>檢測到使用者錯誤DCtoMDC升級

| 錯誤訊息      | <span style="font-weight:normal">檢測到 SDC 到 MDC 升級</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP HANA 實例已從 SDC 升級到 MDC。 更新後備份將失敗。 |
| **建議的動作** | 按照 SAP [HANA 1.0 到 2.0 部分](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)中列出的步驟解決問題 |

### <a name="usererrorinvalidbackintconfiguration"></a>使用者錯誤不正確備份配置

| 錯誤訊息      | <span style="font-weight:normal">檢測到不正確後臺配置</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 為 Azure 備份正確指定支援參數 |
| **建議的動作** | 檢查是否設置了以下（背面）參數：<br/>\*[catalog_backup_using_backint：真]<br/>\*[enable_accumulated_catalog_backup：假]<br/>\*[parallel_data_backup_backint_channels：1]<br/>\*[log_backup_timeout_s：900）]<br/>\*[backint_response_timeout：7200]<br/>如果 HOST 中存在基於背面的參數，請刪除它們。 如果參數在 HOST 級別不存在，但在資料庫級別已手動修改，請將其還原為前面所述的適當值。 或者，運行[停止保護並從](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database)Azure 門戶保留備份資料，然後選擇 **"恢復備份**"。 |

## <a name="restore-checks"></a>還原檢查

### <a name="single-container-database-sdc-restore"></a>單個容器資料庫 （SDC） 還原

在將 HANA 的單個容器資料庫 （SDC） 還原到另一台 SDC 電腦時，請處理輸入。 資料庫名稱應使用小寫字母命名，並在括弧中附加"sdc"。 HANA 實例將以大寫字母顯示。

假設 SDC HANA 實例"H21"已備份。 備份項頁將顯示備份項名稱為 **"h21（sdc）"。** 如果嘗試將此資料庫還原到另一個目標 SDC（例如 H11），則需要提供以下輸入。

![已還原的 SDC 資料庫名稱](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

請注意下列幾點：

- 預設情況下，還原的 db 名稱將填充備份項名稱。 在這種情況下，h21（sdc）。
- 選擇目標為 H11 不會自動更改還原的 db 名稱。 **應將其編輯為 h11（sdc）。** 關於 SDC，還原的 db 名稱將是帶有小寫字母和括弧中"sdc"的目標實例 ID。
- 由於 SDC 只能有單個資料庫，因此還需要按一下該核取方塊以允許使用復原點資料覆蓋現有資料庫資料。
- Linux 區分大小寫。 因此，請小心保留案件。

### <a name="multiple-container-database-mdc-restore"></a>多個容器資料庫 （MDC） 還原

在 HANA 的多個容器資料庫中，標準配置為 SYSTEMDB = 1 或更多租戶 GB。 還原整個 SAP HANA 實例意味著還原 SYSTEMDB 和租戶 DB。 一個還原 SYSTEMDB，然後繼續為租戶資料庫。 系統 DB 實質是指覆蓋所選目標的系統資訊。 此還原還覆蓋目標實例中的 BackInt 相關資訊。 因此，在系統 DB 還原到目標實例後，請再次運行預註冊腳本。 只有這樣，後續租戶資料庫還原才會成功。

## <a name="upgrading-from-sap-hana-10-to-20"></a>從 SAP HANA 1.0 升級到 2.0

如果要保護 SAP HANA 1.0 資料庫並希望升級到 2.0，則執行以下步驟：

- 使用舊 SDC 資料庫保留資料[停止保護](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)。
- 執行升級。 完成後，HANA 現在為 MDC，具有系統資料庫和租戶資料庫。
- 重新運行[預註冊腳本](https://aka.ms/scriptforpermsonhana)，包含 （sid 和 mdc） 的正確詳細資訊。
- 在 Azure 門戶中重新註冊同一電腦的擴展（備份->視圖詳細資訊 ->選擇相關的 Azure VM->重新註冊）。
- 按一下"重新發現同一 VM 的 DB"。 此操作應在步驟 2 中顯示具有正確詳細資訊的新 DB（SYSTEMDB 和租戶資料庫，而不是 SDC）。
- 為這些新資料庫配置備份。

## <a name="upgrading-without-an-sid-change"></a>升級時無需更改 SID

不會導致 SID 更改的作業系統或 SAP HANA 的升級可以按照以下方式進行處理：

- 使用資料庫保留資料[停止保護](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 執行升級。
- 重新運行[預註冊腳本](https://aka.ms/scriptforpermsonhana)。 通常，我們已經看到升級過程刪除了必要的角色。 運行預註冊腳本將有助於驗證所有必需的角色。
- 再次[恢復資料庫保護](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)

## <a name="re-registration-failures"></a>重新註冊失敗

在觸發重新註冊操作之前，請檢查以下一個或多個症狀：

- 所有操作（如備份、還原和配置備份）在 VM 上都失敗，並且具有以下錯誤代碼之一：**工作負載擴展不可訪問、使用者錯誤工作負載擴展未安裝、工作負載擴展不存在、工作負載擴展未執行佇列 Msg**。
- 如果備份項的 **"備份狀態**"區域顯示 **"無法訪問"，** 則排除可能導致相同狀態的所有其他原因：

  - 缺乏在 VM 上執行與備份相關的操作的許可權
  - VM 已關閉，因此無法進行備份
  - 網路問題

這些症狀可能出現以下一個或多個原因：

- 擴展已從門戶中刪除或卸載。
- 通過就地磁片還原及時還原 VM。
- VM 已關閉很長一段時間，因此其上的擴展配置已過期。
- 已刪除 VM，並且使用與已刪除的 VM 相同的名稱並在同一資源組中創建了另一個 VM。

在前面的方案中，我們建議您在 VM 上觸發重新註冊操作。

## <a name="next-steps"></a>後續步驟

- 查看有關在 Azure VM 上備份 SAP HANA 資料庫[的常見問題](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm)。
