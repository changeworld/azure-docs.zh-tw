---
title: 備份和還原已加密的 Azure Vm
description: 說明如何使用 Azure 備份服務來備份和還原已加密的 Azure Vm。
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 0800a15b215b37ceb75abc0d6480331d642dc746
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85124498"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>備份和還原已加密的 Azure VM

本文說明如何使用[Azure 備份](backup-overview.md)服務來備份和還原具有加密磁片的 Windows 或 Linux Azure 虛擬機器（vm）。

如果您想要深入瞭解 Azure 備份在開始之前如何與 Azure Vm 互動，請參閱下列資源：

- [檢閱](backup-architecture.md#architecture-built-in-azure-vm-backup) Azure VM 備份架構。
- [深入瞭解](backup-azure-vms-introduction.md)Azure VM 備份和 Azure 備份延伸模組。

## <a name="encryption-support"></a>加密支援

Azure 備份支援使用 Azure 磁碟加密（ADE）加密其 OS/資料磁片的 Azure Vm 備份。 ADE 會使用 BitLocker 來加密 Windows Vm，以及適用于 Linux Vm 的 dm crypt 功能。 ADE 會與 Azure Key Vault 整合，以管理磁片加密金鑰和密碼。 Key Vault 金鑰加密金鑰（Kek）可以用來新增額外的安全性層級，在將加密秘密寫入 Key Vault 之前，先將其加密。

Azure 備份可以使用具有和不含 Azure AD 應用程式的 ADE 來備份和還原 Azure Vm，如下表摘要所示。

**VM 磁碟類型** | **ADE （BEK/dm-crypt）** | **ADE 和 KEK**
--- | --- | ---
**非受控** | 是 | 是
**受控**  | 是 | 是

- 深入瞭解[ADE](../security/azure-security-disk-encryption-overview.md)、 [Key Vault](../key-vault/general/overview.md)和[kek](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek)。
- 閱讀 Azure VM 磁片加密的[常見問題](../security/azure-security-disk-encryption-faq.md)。

### <a name="limitations"></a>限制

- 您可以在相同的訂用帳戶和區域內備份和還原已加密的 Vm。
- Azure 備份支援使用獨立金鑰加密的 Vm。 目前不支援任何屬於用來加密 VM 之憑證的金鑰。
- 您可以在與復原服務備份保存庫相同的訂用帳戶和區域中，備份及還原已加密的 Vm。
- 加密的 VM 無法在檔案/資料夾層級復原。 您必須復原整個 VM，以還原檔案和資料夾。
- 還原 VM 時，您無法使用已加密 Vm 的 [[取代現有 VM](backup-azure-arm-restore-vms.md#restore-options) ] 選項。 只有未加密的受控磁片才支援此選項。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

開始之前，請執行下列作業：

1. 請確定您有一或多個已啟用 ADE 的[Windows](../security/azure-security-disk-encryption-windows.md)或[Linux](../virtual-machines/linux/disk-encryption-overview.md) vm。
2. 審查 Azure VM 備份[的支援矩陣](backup-support-matrix-iaas.md)
3. [建立](backup-azure-arm-vms-prepare.md#create-a-vault)復原服務備份保存庫（如果您沒有的話）。
4. 如果您為已啟用備份的 Vm 啟用加密，您只需要提供備份許可權來存取 Key Vault，即可在不中斷的情況下繼續備份。 [深入瞭解](#provide-permissions)如何指派這些許可權。

此外，在某些情況下，您可能還需要做幾件事：

- **在 VM 上安裝 VM 代理程式**：為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 是從 Azure 市集映像建立，則代理程式已安裝且正在執行。 如果您建立自訂 VM，或遷移內部部署機器，您可能需要[手動安裝代理程式](backup-azure-arm-vms-prepare.md#install-the-vm-agent)。

## <a name="configure-a-backup-policy"></a>設定備份原則

1. 如果您尚未建立復原服務備份保存庫，請遵循[這些指示](backup-azure-arm-vms-prepare.md#create-a-vault)
2. 在入口網站中開啟保存庫，然後選取 [**消費者入門**] 區段中的 [**備份**]。

    ![備份刀鋒視窗](./media/backup-azure-vms-encryption/select-backup.png)

3. 在您的工作負載執行所在的**備份目標**  >  **？** 選取 [ **Azure**]。
4. 在 [您要備份什麼?] 中，選取 [虛擬機器] > [確定]。

      ![案例刀鋒視窗](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. 在 [**備份原則**] 中  >  ，**選擇 [備份原則**]，選取您想要與保存庫產生關聯的原則。 然後按一下 [確定] 。
    - 備份原則會指定備份的執行時間，以及儲存的時間長度。
    - 預設原則的詳細資料便會列在下拉式功能表之下。

    ![開啟 [案例] 刀鋒視窗](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. 如果您不想要使用預設原則，請選取 [**建立新**的]，然後[建立自訂原則](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。

7. 選擇您想要使用 [選取原則] 來備份的已加密 Vm，然後選取 **[確定]**。

      ![選取加密的 VM](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. 如果您使用 Azure Key Vault，在 [保存庫] 頁面上，您會看到一則訊息，指出 Azure 備份需要 Key Vault 中金鑰和密碼的唯讀存取權。

    - 如果您收到此訊息，則不需要採取任何動作。

        ![存取正常](./media/backup-azure-vms-encryption/access-ok.png)

    - 如果您收到此訊息，則需要設定許可權，如[下列](#provide-permissions)程式所述。

        ![存取警告](./media/backup-azure-vms-encryption/access-warning.png)

9. 按一下 [**啟用備份**] 以在保存庫中部署備份原則，並為選取的 vm 啟用備份。

## <a name="trigger-a-backup-job"></a>觸發備份作業

初始備份會根據排程執行，但也可以立即執行，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [備份項目] 中，按一下 [Azure 虛擬機器]。
3. 在 [備份項目] 清單中，按一下省略符號 (...)。
4. 按一下 [立即備份]。
5. 在 [立即備份] 中，使用行事曆控制項來選取復原點應該保留的最後一天。 然後按一下 [確定] 。
6. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的 VM 大小，建立初始備份可能需要花一點時間。

## <a name="provide-permissions"></a>提供許可權

Azure 備份需要備份金鑰和密碼的唯讀存取權，以及相關聯的 Vm。

- 您的 Key Vault 與 Azure 訂用帳戶的 Azure AD 租使用者相關聯。 如果您是**成員使用者**，Azure 備份會取得 Key Vault 的存取權，而不需要進一步的動作。
- 如果您是**來賓使用者**，您必須提供存取金鑰保存庫 Azure 備份的許可權。

若要設定許可權：

1. 在 [Azure 入口網站中，選取 [**所有服務**]，然後搜尋**金鑰保存庫**。
2. 選取與您要備份的加密 VM 相關聯的金鑰保存庫。
3. 選取 [**存取原則**] [  >  **加入新**的]。
4. 選取 [**選取主體**]，然後輸入**備份管理**。
5. 選取 [**備份管理服務**] [  >  **選取**]。

    ![備份服務選取項目](./media/backup-azure-vms-encryption/select-backup-service.png)

6. 在 [**新增存取原則**]  >  **[從範本設定（選擇性）**] 中，選取 [ **Azure 備份**]。
    - 該備份會在 [金鑰權限]**** 和 [祕密權限]**** 預先填入必要的權限。
    - 如果您的 VM 只使用**BEK**加密，請移除**金鑰許可權**的選擇，因為您只需要密碼的許可權。

    ![Azure 備份選取項目](./media/backup-azure-vms-encryption/select-backup-template.png)

7. 按一下 [確定]。 **備份管理服務**會新增至**存取原則**。

    ![存取原則](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. 按一下 [**儲存**] 以提供許可權給 Azure 備份。

## <a name="restore-an-encrypted-vm"></a>還原已加密的 VM

還原 VM 磁片只能還原已加密的 Vm，如下所述。 不支援**取代現有**和**還原 VM** 。

還原已加密的 Vm，如下所示：

1. [還原 VM 磁碟](backup-azure-arm-restore-vms.md#restore-disks)。
2. 執行下列其中一項動作，以重新建立虛擬機器實例：
    1. 使用還原作業期間所產生的範本來自訂 VM 設定，並觸發 VM 部署。 [深入了解](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)。
    2. 使用 PowerShell 從還原的磁片建立新的 VM。 [深入了解](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。
3. 若為 Linux Vm，請重新安裝 ADE 延伸模組，以便開啟並裝載資料磁片。

## <a name="next-steps"></a>後續步驟

如果您遇到任何問題，請參閱下列文章：

- 備份和還原已加密的 Azure Vm 時[常見的錯誤](backup-azure-vms-troubleshoot.md)。
- [AZURE VM 代理程式/備份擴充](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)功能的問題。
