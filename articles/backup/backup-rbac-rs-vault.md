---
title: 使用基於角色的存取控制管理備份
description: 使用角色型存取控制來管理復原服務保存庫中的備份管理作業存取權。
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e2e32ac6981635e3b9885119fdf397783ac32cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273198"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>使用角色型存取控制來管理 Azure 備份復原點

Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更細緻的存取權管理。 RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。

> [!IMPORTANT]
> Azure 備份提供的角色僅限於可在 Azure 門戶中或通過 REST API 或恢復服務保存庫 PowerShell 或 CLI Cmdlet 執行的操作。 在 Azure 備份代理程式用戶端 UI、System Center Data Protection Manager UI 或 Azure 備份伺服器 UI 中執行的動作則非這些角色所能控制。

Azure 備份提供了三個內置角色來控制備份管理操作。 深入了解 [Azure RBAC 內建角色](../role-based-access-control/built-in-roles.md)

* [備份參與者](../role-based-access-control/built-in-roles.md#backup-contributor)- 此角色具有創建和管理備份的擁有權限，但刪除恢復服務保存庫和授予其他人存取權限除外。 您可以將此角色想做是管理備份的系統管理員，其可執行每一種備份管理作業。
* [備份操作員](../role-based-access-control/built-in-roles.md#backup-operator) - 此角色擁有參與者的所有權限，但用來移除備份和管理備份原則的權限除外。 此角色相當於參與者，但無法執行破壞性作業，例如停止備份並刪除資料，或移除內部部署資源的註冊。
* [備份讀取者](../role-based-access-control/built-in-roles.md#backup-reader) - 此角色擁有用來檢視所有備份管理作業的權限。 您可以將此角色想做是監視者。

如果要定義自己的角色以進行更多控制，請參閱如何在 Azure RBAC 中[構建自訂角色](../role-based-access-control/custom-roles.md)。

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>將備份的內建角色對應至備份管理動作

下表會擷取備份管理動作與執行該作業所需的最小對應 RBAC 角色。

| 管理作業 | 所需的最小 RBAC 角色 | 所需的範圍 |
| --- | --- | --- |
| 建立復原服務保存庫 | 備份參與者 | 包含保存庫的資源群組 |
| 啟用 Azure VM 的備份 | 備份操作員 | 包含保存庫的資源群組 |
| | 虛擬機器參與者 | VM 資源 |
| VM 的隨選備份 | 備份操作員 | 復原服務保存庫 |
| 還原 VM | 備份操作員 | 復原服務保存庫 |
| | 參與者 | 將部署 VM 的資源群組 |
| | 虛擬機器參與者 | 已備份的源 VM |
| 還原非受控磁碟 VM 備份 | 備份操作員 | 復原服務保存庫 |
| | 虛擬機器參與者 | 已備份的源 VM |
| | 儲存體帳戶參與者 | 要還原磁碟的儲存體帳戶資源 |
| 從 VM 備份還原受控磁碟 | 備份操作員 | 復原服務保存庫 |
| | 虛擬機器參與者 | 已備份的源 VM |
| | 儲存體帳戶參與者 | 在還原過程中選取，用來在將保存庫的資料轉換至受控磁碟之前，先保留資料的暫時儲存體帳戶 |
| | 參與者 | 要將受控磁碟還原到其中的資源群組 |
| 從 VM 備份還原個別檔案 | 備份操作員 | 復原服務保存庫 |
| | 虛擬機器參與者 | 已備份的源 VM |
| 建立 Azure VM 備份的備份原則 | 備份參與者 | 復原服務保存庫 |
| 修改 Azure VM 備份的備份原則 | 備份參與者 | 復原服務保存庫 |
| 刪除 Azure VM 備份的備份原則 | 備份參與者 | 復原服務保存庫 |
| 在 VM 備份上停止備份 (保留資料或刪除資料) | 備份參與者 | 復原服務保存庫 |
| 註冊內部部署 Windows Server/用戶端/SCDPM 或 Azure 備份伺服器 | 備份操作員 | 復原服務保存庫 |
| 刪除已註冊的內部部署 Windows Server/用戶端/SCDPM 或 Azure 備份伺服器 | 備份參與者 | 復原服務保存庫 |

> [!IMPORTANT]
> 如果您指定 VM 資源範圍內的 VM 參與者，並按一下 VM 設定中的 [備份]，它就會開啟 [啟用備份] 畫面 (即使已在呼叫時將 VM 備份)，以確認備份狀態只能在訂用帳戶層級才能運作。 若要避免這個問題，請移至保存庫並開啟 VM 的備份項目檢視，或在訂用帳戶層級指定 VM 參與者角色。

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Azure 檔共用備份的最低角色要求

下表捕獲執行 Azure 檔共用操作所需的備份管理操作和相應角色。

| 管理作業 | 需要的角色 | 資源 |
| --- | --- | --- |
| 啟用 Azure 檔共用的備份 | 備份參與者 |復原服務保存庫 |
| |儲存體帳戶 | 參與者存儲帳戶資源 |
| VM 的隨選備份 | 備份操作員 | 復原服務保存庫 |
| 還原檔共用 | 備份操作員 | 復原服務保存庫 |
| | 儲存體帳戶參與者 | 存在還原源和目的檔案共用的存儲帳戶資源 |
| 還原單個檔 | 備份操作員 | 復原服務保存庫 |
| |儲存體帳戶參與者|存在還原源和目的檔案共用的存儲帳戶資源 |
| 停止保護 |備份參與者 | 復原服務保存庫 |
| 從保存庫取消註冊存儲帳戶 |備份參與者 | 復原服務保存庫 |
| |儲存體帳戶參與者 | 儲存體帳戶資源|

## <a name="next-steps"></a>後續步驟

* [角色型存取控制](../role-based-access-control/role-assignments-portal.md)：開始在 Azure 入口網站中使用 RBAC。
* 了解如何使用下列各項管理存取權：
  * [電源外殼](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [角色型存取控制疑難排解](../role-based-access-control/troubleshooting.md)︰取得修正常見問題的建議。
