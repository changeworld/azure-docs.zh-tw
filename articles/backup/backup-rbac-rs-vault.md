---
title: 使用 Azure 角色型存取控制來管理備份
description: 使用 Azure 角色型存取控制來管理復原服務保存庫中的備份管理作業存取權。
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 0dd8d08c4ee79082f47929cf7d453f3f4bbd60ee
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090874"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>使用 Azure 角色型存取控制來管理 Azure 備份復原點

Azure 角色型存取控制 (Azure RBAC) 可讓您對 Azure 進行更細緻的存取管理。 Azure RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。

> [!IMPORTANT]
> Azure 備份提供的角色僅限於可在 Azure 入口網站或透過 REST API 或復原服務保存庫 PowerShell 或 CLI Cmdlet 執行的動作。 在 Azure 備份代理程式用戶端 UI 或 System center Data Protection Manager UI 或 Azure 備份伺服器 UI 中執行的動作不是這些角色的控制權。

Azure 備份提供三個內建角色來控制備份管理作業。 深入瞭解 [Azure 內建角色](../role-based-access-control/built-in-roles.md)

* [備份參與者](../role-based-access-control/built-in-roles.md#backup-contributor) -除了刪除復原服務保存庫並將存取權授與其他人之外，此角色具有建立及管理備份的擁有權限。 您可以將此角色想做是管理備份的系統管理員，其可執行每一種備份管理作業。
* [備份操作員](../role-based-access-control/built-in-roles.md#backup-operator) - 此角色擁有參與者的所有權限，但用來移除備份和管理備份原則的權限除外。 此角色相當於參與者，但無法執行破壞性作業，例如停止備份並刪除資料，或移除內部部署資源的註冊。
* [備份讀取者](../role-based-access-control/built-in-roles.md#backup-reader) - 此角色擁有用來檢視所有備份管理作業的權限。 您可以將此角色想做是監視者。

如果您想要定義您自己的角色以進行更多的控制，請參閱如何在 Azure RBAC 中 [建立自訂角色](../role-based-access-control/custom-roles.md) 。

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>將備份的內建角色對應至備份管理動作

下表將會捕獲執行該作業所需的備份管理動作和對應的最低 Azure 角色。

| 管理作業 | 需要最小的 Azure 角色 | 所需的範圍 |
| --- | --- | --- |
| 建立復原服務保存庫 | 備份參與者 | 包含保存庫的資源群組 |
| 啟用 Azure VM 的備份 | 備份操作員 | 包含保存庫的資源群組 |
| | 虛擬機器參與者 | VM 資源 |
| VM 的隨選備份 | 備份操作員 | 復原服務保存庫 |
| 還原 VM | 備份操作員 | 復原服務保存庫 |
| | 參與者 | 將部署 VM 的資源群組 |
| | 虛擬機器參與者 | 已備份的來源 VM |
| 還原非受控磁碟 VM 備份 | 備份操作員 | 復原服務保存庫 |
| | 虛擬機器參與者 | 已備份的來源 VM |
| | 儲存體帳戶參與者 | 要還原磁碟的儲存體帳戶資源 |
| 從 VM 備份還原受控磁碟 | 備份操作員 | 復原服務保存庫 |
| | 虛擬機器參與者 | 已備份的來源 VM |
| | 儲存體帳戶參與者 | 在還原過程中選取，用來在將保存庫的資料轉換至受控磁碟之前，先保留資料的暫時儲存體帳戶 |
| | 參與者 | 要將受控磁碟還原到其中的資源群組 |
| 從 VM 備份還原個別檔案 | 備份操作員 | 復原服務保存庫 |
| | 虛擬機器參與者 | 已備份的來源 VM |
| 建立 Azure VM 備份的備份原則 | 備份參與者 | 復原服務保存庫 |
| 修改 Azure VM 備份的備份原則 | 備份參與者 | 復原服務保存庫 |
| 刪除 Azure VM 備份的備份原則 | 備份參與者 | 復原服務保存庫 |
| 在 VM 備份上停止備份 (保留資料或刪除資料) | 備份參與者 | 復原服務保存庫 |
| 註冊內部部署 Windows Server/用戶端/SCDPM 或 Azure 備份伺服器 | 備份操作員 | 復原服務保存庫 |
| 刪除已註冊的內部部署 Windows Server/用戶端/SCDPM 或 Azure 備份伺服器 | 備份參與者 | 復原服務保存庫 |

> [!IMPORTANT]
> 如果您在 VM 資源範圍指定 VM 參與者，並選取 [ **備份** ] 作為 vm 設定的一部分，則會開啟 [ **啟用備份** ] 畫面，即使 vm 已備份也一樣。 這是因為只在訂用帳戶層級上驗證備份狀態的呼叫才會生效。 若要避免這種情況，請移至保存庫並開啟 VM 的備份專案視圖，或在訂用帳戶層級指定 VM 參與者角色。

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Azure 檔案共用備份的最小角色需求

下表將會捕獲執行 Azure 檔案共用作業所需的備份管理動作和對應角色。

| 管理作業 | 需要角色 | 資源 |
| --- | --- | --- |
| 啟用 Azure 檔案共用的備份 | 備份參與者 |復原服務保存庫 |
| |儲存體帳戶 | 參與者儲存體帳戶資源 |
| VM 的隨選備份 | 備份操作員 | 復原服務保存庫 |
| 還原檔案共用 | 備份操作員 | 復原服務保存庫 |
| | 儲存體帳戶參與者 | 存在還原來源和目標檔案共用的儲存體帳戶資源 |
| 還原個別檔案 | 備份操作員 | 復原服務保存庫 |
| |儲存體帳戶參與者|存在還原來源和目標檔案共用的儲存體帳戶資源 |
| 停止保護 |備份參與者 | 復原服務保存庫 |
| 從保存庫取消註冊儲存體帳戶 |備份參與者 | 復原服務保存庫 |
| |儲存體帳戶參與者 | 儲存體帳戶資源|

## <a name="next-steps"></a>後續步驟

* [Azure 角色型存取控制 (AZURE rbac) ](../role-based-access-control/role-assignments-portal.md)：開始使用 Azure 入口網站中的 azure rbac。
* 了解如何使用下列各項管理存取權：
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Azure 角色型存取控制疑難排解](../role-based-access-control/troubleshooting.md)：取得修正常見問題的建議。
