---
title: 備份和恢復加密的 Azure VM
description: 介紹如何使用 Azure 備份服務備份和還原加密的 Azure VM。
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: a3976cc83f749b1abe00cef3f5bf867ffbc30ab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206685"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>備份和還原加密的 Azure VM

本文介紹如何使用[Azure 備份](backup-overview.md)服務使用加密磁片備份和還原 Windows 或 Linux Azure 虛擬機器 （VM）。

如果要在開始之前瞭解有關 Azure 備份如何與 Azure VM 交互的詳細資訊，請查看以下資源：

- [查看](backup-architecture.md#architecture-built-in-azure-vm-backup)Azure VM 備份體系結構。
- [瞭解](backup-azure-vms-introduction.md)Azure VM 備份和 Azure 備份擴展。

## <a name="encryption-support"></a>加密支援

Azure 備份支援使用 Azure 磁片加密 （ADE） 加密其作業系統/資料磁片的 Azure VM 的備份。 ADE 使用 BitLocker 對 Windows VM 進行加密，以及 Linux VM 的 dm-crypt 功能。 ADE 與 Azure 金鑰保存庫集成，以管理磁片加密金鑰和機密。 金鑰保存庫金鑰加密金鑰 （KEK） 可用於添加額外的安全層，在將加密機密寫入金鑰保存庫之前對其進行加密。

Azure 備份可以使用 ADE 備份和還原 Azure VM，無論是否使用 Azure AD 應用，如下表中總結的。

**VM 磁碟類型** | **ADE （BEK/dm-crypt）** | **ADE 和 KEK**
--- | --- | ---
**非 託管** | 是 | 是
**管理**  | 是 | 是

- 瞭解有關[ADE、](../security/azure-security-disk-encryption-overview.md)[金鑰保存庫](../key-vault/key-vault-overview.md)和[KEK](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek)的更多。
- 閱讀 Azure VM 磁片加密[的常見問題解答](../security/azure-security-disk-encryption-faq.md)。

### <a name="limitations"></a>限制

- 您可以在同一訂閱和地區中備份和還原加密 VM。
- Azure 備份支援使用獨立金鑰加密的 VM。 當前不支援作為用於加密 VM 的證書的一部分的任何金鑰。
- 您可以在與恢復服務備份保存庫相同的訂閱和地區中備份和還原加密 VM。
- 加密的 VM 無法在檔案/資料夾層級復原。 您需要恢復整個 VM 以還原檔和資料夾。
- 還原 VM 時，不能將[替換現有 VM](backup-azure-arm-restore-vms.md#restore-options)選項用於加密 VM。 此選項僅支援未加密的託管磁片。

## <a name="before-you-start"></a>開始之前

開始之前，請執行下列作業：

1. 確保啟用了一個或多個啟用了 ADE 的[Windows](../security/azure-security-disk-encryption-windows.md)或[Linux](../virtual-machines/linux/disk-encryption-overview.md) VM。
2. 查看 Azure VM[備份的支援矩陣](backup-support-matrix-iaas.md)
3. 如果沒有恢復服務備份保存庫，則[創建](backup-azure-arm-vms-prepare.md#create-a-vault)恢復服務備份保存庫。
4. 如果為已啟用備份的 VM 啟用加密，只需向備份提供訪問金鑰保存庫的許可權，以便備份可以繼續而不中斷。 [詳細瞭解](#provide-permissions)如何分配這些許可權。

此外，在某些情況下，您可能需要執行幾項操作：

- **在 VM 上安裝 VM 代理**：Azure 備份通過安裝電腦上運行的 Azure VM 代理的擴展來備份 Azure VM。 如果 VM 是從 Azure 應用商店映射創建的，則代理將安裝並運行。 如果創建自訂 VM 或遷移本地電腦，則可能需要[手動安裝代理](backup-azure-arm-vms-prepare.md#install-the-vm-agent)。

## <a name="configure-a-backup-policy"></a>設定備份原則

1. 如果您尚未創建恢復服務備份保存庫，請按照[以下說明操作](backup-azure-arm-vms-prepare.md#create-a-vault)
2. 打開門戶中的保存庫，並在"**入門"** 部分中選擇 **"備份**"。

    ![備份刀鋒視窗](./media/backup-azure-vms-encryption/select-backup.png)

3. 在**備份目標** > **中，工作負荷在何處運行？** 選擇**Azure**。
4. 在"**要備份什麼"中？** 選擇 **"虛擬機器** > **確定**"。

      ![案例刀鋒視窗](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. 在**備份策略** > **"選擇備份策略**"中，選擇要與保存庫關聯的策略。 然後按一下 **[確定]**。
    - 備份策略指定何時執行備份以及備份的存儲時間。
    - 預設原則的詳細資料便會列在下拉式功能表之下。

    ![開啟 [案例] 刀鋒視窗](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. 如果不想使用預設策略，請選擇 **"新建**"並[創建自訂策略](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。

7. 選擇要使用選擇策略備份的加密 VM，然後選擇 **"確定**"。

      ![選取加密的 VM](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. 如果使用 Azure 金鑰保存庫，則在保存庫頁上，您將看到一條消息，指出 Azure 備份需要唯讀訪問金鑰保存庫中的金鑰和機密。

    - 如果您收到此消息，則無需執行任何操作。

        ![訪問正常](./media/backup-azure-vms-encryption/access-ok.png)

    - 如果您收到此消息，則需要設置許可權，如下[所述過程](#provide-permissions)。

        ![訪問警告](./media/backup-azure-vms-encryption/access-warning.png)

9. 按一下"**啟用備份**"以在保存庫中部署備份策略，並為所選 VM 啟用備份。

## <a name="trigger-a-backup-job"></a>觸發備份作業

初始備份將按照計畫運行，但您可以立即按如下方式運行它：

1. 在保存庫功能表中，按一下 [備份項目]****。
2. 在**備份項**中，按一下**Azure 虛擬機器**。
3. 在 **"備份專案"** 清單中，按一下省略號 （...）。
4. 按一下 [立即備份]****。
5. 在 **"立即備份"** 中，使用日曆控制項選擇應保留復原點的最後一天。 然後按一下 **[確定]**。
6. 監視入口網站通知。 您可以在保存庫儀表板>**正在進行的備份作業** > **中**監視作業進度。 根據您的 VM 大小，建立初始備份可能需要花一點時間。

## <a name="provide-permissions"></a>提供許可權

Azure VM 需要唯讀訪問來備份金鑰和機密以及關聯的 VM。

- 金鑰保存庫與 Azure 訂閱的 Azure AD 租戶相關聯。 如果您是**成員使用者**，Azure 備份將獲取對金鑰保存庫的存取權限，而無需執行進一步操作。
- 如果您是**來賓使用者**，則必須為 Azure 備份提供訪問金鑰保存庫的許可權。

要設置許可權：

1. 在 Azure 門戶中，選擇 **"所有服務**"並搜索**金鑰保存庫**。
2. 選擇與要備份的加密 VM 關聯的金鑰保存庫。
3. 選擇**訪問策略** > **添加新**。
4. 選擇 **"選擇主體**"，然後鍵入 **"備份管理**"。
5. 選擇**備份管理服務** > **選擇**。

    ![備份服務選取項目](./media/backup-azure-vms-encryption/select-backup-service.png)

6. 在**添加訪問策略** > **從範本（可選）配置中，** 選擇**Azure 備份**。
    - 該備份會在 [金鑰權限]**** 和 [祕密權限]**** 預先填入必要的權限。
    - 如果您的 VM 僅使用**BEK**進行加密，請刪除**金鑰許可權**的選擇，因為您只需要機密許可權。

    ![Azure 備份選取項目](./media/backup-azure-vms-encryption/select-backup-template.png)

7. 按一下 [確定]****。 **備份管理服務**將添加到**訪問策略**中。

    ![存取原則](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. 按一下 **"保存**"以提供具有許可權的 Azure 備份。

## <a name="restore-an-encrypted-vm"></a>還原已加密的 VM

還原加密 VM 如下所示：

1. [還原 VM 磁碟](backup-azure-arm-restore-vms.md#restore-disks)。
2. 通過執行以下操作之一重新創建虛擬機器實例：
    1. 使用還原操作期間生成的範本自訂 VM 設置並觸發 VM 部署。 [深入了解](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)。
    2. 使用 PowerShell 從還原的磁片創建新 VM。 [深入了解](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。
3. 對於 Linux VM，請重新安裝 ADE 擴展，以便打開並裝載資料磁片。

## <a name="next-steps"></a>後續步驟

如果遇到任何問題，請查看以下文章：

- 備份和還原加密 Azure VM 時[的常見錯誤](backup-azure-vms-troubleshoot.md)。
- [Azure VM 代理/備份擴展](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)問題。
