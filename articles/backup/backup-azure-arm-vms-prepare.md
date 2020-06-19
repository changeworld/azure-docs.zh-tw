---
title: 備份復原服務保存庫中的 Azure VM
description: 說明如何使用 Azure 備份將 Azure VM 備份到備份復原服務保存庫
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: cba042efb08f121d4cd9fa5693edd69c827f1465
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727007"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>備份復原服務保存庫中的 Azure VM

本文說明如何使用 [Azure 備份](backup-overview.md)服務，將 Azure VM 備份到備份復原服務保存庫。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 準備 Azure VM。
> * 建立保存庫。
> * 探索 VM 並設定備份原則。
> * 啟用 Azure VM 的備份。
> * 執行初始備份。

> [!NOTE]
> 本文說明如何設定保存庫，並選取要備份的 VM。 這有助於您備份多個 VM。 或者，您可以直接根據 VM 設定來[備份單一 Azure VM](backup-azure-vms-first-look-arm.md)。

## <a name="before-you-start"></a>開始之前

* [檢閱](backup-architecture.md#architecture-built-in-azure-vm-backup) Azure VM 備份架構。
* [深入了解](backup-azure-vms-introduction.md) Azure VM 備份和備份擴充功能。
* 設定備份之前，請[檢閱支援矩陣](backup-support-matrix-iaas.md)。

此外，在某些情況下，您可能還需要做幾件事：

* **在 VM 上安裝 VM 代理程式**：為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 是從 Azure 市集映像建立，則代理程式已安裝且正在執行。 如果您建立自訂 VM，或遷移內部部署機器，您可能需要[手動安裝代理程式](#install-the-vm-agent)。

## <a name="create-a-vault"></a>建立保存庫

 保存庫會儲存在一段時間內建立的備份和復原點，並儲存與備份的機器相關聯的備份原則。 請依照下列方式建立保存庫：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [搜尋] 中，輸入**復原服務**。 在 [服務] 下，按一下 [復原服務保存庫]。

     ![搜尋復原服務保存庫](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. 在 [復原服務保存庫] 功能表中，按一下 [+新增]。

     ![建立復原服務保存庫的步驟 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. 在 [復原服務保存庫] 中，輸入易記名稱來識別保存庫。
    * 必須是 Azure 訂用帳戶中唯一的名稱。
    * 它可以包含 2 到 50 個字元。
    * 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。
5. 選取要在其中建立保存庫的 Azure 訂用帳戶、資源群組和地理區域。 接著，按一下 [建立]。
    * 建立保存庫可能需要一些時間。
    * 請監視入口網站右上方區域中的狀態通知。

保存庫建立之後，就會出現在 [復原服務保存庫] 清單中。 如果您沒有看到保存庫，請選取 [重新整理]。

![備份保存庫的清單](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Azure 備份現在允許自訂由 Azure 備份服務所建立的資源群組名稱。 如需詳細資訊，請參閱[虛擬機器的 Azure 備份資源群組](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)。

### <a name="modify-storage-replication"></a>修改儲存體複寫

根據預設，保存庫會使用[異地備援儲存體 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

* 如果保存庫是您的主要備份機制，則建議使用 GRS。
* 您可以使用[本地備援儲存體 (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)，這是較便宜的選項。

修改儲存體複寫類型，如下所示：

1. 在新的保存庫中，按一下 [設定] 區段中的 [屬性]。
2. 在 [屬性] 的 [備份設定] 下，按一下 [更新]。
3. 選取儲存體複寫類型，然後按一下 [儲存]。

      ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > 當保存庫設定完成且包含備份項目之後，您就無法修改儲存體複寫類型。 如果想要修改，則必須重新建立保存庫。

## <a name="apply-a-backup-policy"></a>套用備份原則

設定保存庫的備份原則。

1. 在保存庫中，按一下 [概觀] 區段中的 [+備份]。

   ![備份按鈕](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. 在 [備份目標] > [您的工作負載在何處執行?] 中，選取 [Azure]。 在 [您要備份什麼?] 中，選取 [虛擬機器] >  [確定]。 這會在保存庫中註冊 VM 擴充功能。

   ![備份和備份目標窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. 在 [備份原則] 中，選取要與保存庫產生關聯的原則。
    * 預設原則每天備份一次 VM。 每日備份保留 30 天。 立即復原快照集保留兩天。
    * 如果您不要使用預設原則，請選取 [建立新項目]，然後建立自訂原則，如下一個程序所述。

      ![預設備份原則](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. 在 [選取虛擬機器] 中，選取您要使用原則來備份的 VM。 然後按一下 [確定] 。

   * 選取的 VM 會經過驗證。
   * 您只能選取與保存庫位於相同區域中的 VM。
   * VM 只能在單一保存庫中備份。

     ![[選取虛擬機器] 窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > 只有與保存庫位於相同區域和訂用帳戶中的 VM，才可供設定備份。

5. 在 [備份] 中，按一下 [啟用備份]。 這會將原則部署到保存庫和 VM，並在執行於 Azure VM 的 VM 代理程式上安裝備份擴充功能。

     ![[啟用備份] 按鈕](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

啟用備份後：

* 無論 VM 是否在執行，備份服務都會安裝備份擴充功能。
* 初始備份會根據備份排程執行。
* 當備份執行時，請注意：
  * 執行中的 VM 最有機會捕捉到應用程式一致的復原點。
  * 不過，即使 VM 已關閉，還是會備份。 這種 VM 稱為離線 VM。 在此情況下，復原點是「當機時保持一致」。
* 不需要明確的輸出連線能力，也允許備份 Azure VM。

### <a name="create-a-custom-policy"></a>建立自訂原則

如果您選擇建立新的備份原則，請填寫原則設定。

1. 在 [原則名稱] 中，指定有意義的名稱。
2. 在 [備份排程] 中，指定應該何時備份。 您可以每日或每週備份 Azure VM。
3. 在 [立即還原] 中，指定快照集要在本機保留多久，以供立即還原。
    * 還原時，已備份的 VM 磁碟會從儲存體經由網路，複製到復原儲存位置。 透過立即還原，您可以利用備份作業期間所建立儲存在本機的快照集，而不需要等待備份資料傳輸到保存庫。
    * 可供立即還原的快照集可以保留一到五天。 預設值是兩天。
4. 在 [保留期間] 中，指定每日或每週備份點要保留多久。
5. 在 [每月備份點保留期] 中，指定是否要保留每日或每週備份的每月備份。
6. 按一下 [ **確定** ] 儲存原則。

    ![新增備份原則](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure 備份在進行 Azure VM 備份時不支援依據日光節約變更而自動調整時鐘。 當時間變更時，請視需要手動修改備份原則。

## <a name="trigger-the-initial-backup"></a>觸發初始備份

初始備份會根據排程執行，但也可以立即執行，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [備份項目] 中，按一下 [Azure 虛擬機器]。
3. 在 [備份項目] 清單中，按一下省略符號 (...)。
4. 按一下 [立即備份]。
5. 在 [立即備份] 中，使用行事曆控制項來選取復原點應該保留的最後一天。 然後按一下 [確定] 。
6. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的 VM 大小，建立初始備份可能需要花一點時間。

## <a name="verify-backup-job-status"></a>驗證備份作業狀態

每個 VM 備份的備份作業詳細資料包含兩個階段：**快照集** 階段，接著**將資料傳輸至保存庫**階段。<br/>
快照集階段保證隨磁碟一起儲存的復原點可供**立即還原**，而且最多保留五天，視使用者所設定的快照集保留期而定。 「將資料傳輸至保存庫」在保存庫中建立復原點以長期保留。 只有在快照集階段完成之後，才會開始「將資料傳輸至保存庫」。

  ![備份作業狀態](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

有兩個**子工作**在後端執行，一個用於前端備份作業，您可以從 [備份作業] 詳細資料刀鋒視窗中查看，如下所示：

  ![備份作業狀態](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

**將資料傳輸至保存庫**階段可能需要很多天才能完成，視磁碟大小、每個磁碟的變換和其他幾個因素而定。

作業狀態根據下列情況而有所不同：

**快照式** | **將資料傳輸至保存庫** | **作業狀態**
--- | --- | ---
Completed | 進行中 | 進行中
Completed | 已略過 | Completed
Completed | Completed | Completed
Completed | 失敗 | 已完成但有警告
失敗 | 失敗 | 失敗

現在透過這項功能，對於相同的 VM，可以平行執行兩個備份，但在任一階段 (快照集、將資料傳輸至保存庫)，只有一個子工作可以執行。 因此，透過這項分離功能，將可避免進行中的備份作業導致次日的備份失敗。 接下來一天的備份可以完成快照集，而如果前一天的備份作業處於進行中狀態，則會略過**將資料傳輸至保存庫**。
在保存庫中建立的增量復原點，將會從保存庫中建立的最後一個復原點來捕捉所有變換。 使用者的成本不受影響。

## <a name="optional-steps"></a>選擇性步驟

### <a name="install-the-vm-agent"></a>安裝 VM 代理程式

為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 是從 Azure Marketplace 映像建立，則代理程式已安裝且正在執行。 如果您建立自訂 VM，或遷移內部部署機器，您可能需要手動安裝代理程式，如下表概述。

**VM** | **詳細資料**
--- | ---
**Windows** | 1.[下載並安裝](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理程式 MSI 檔案。<br/><br/> 2.以機器的系統管理員權限安裝。<br/><br/> 3.驗證安裝。 在 VM 的 *C:\WindowsAzure\Packages* 中，以滑鼠右鍵按一下 **WaAppAgent.exe** > [內容]。 在 [詳細資料] 索引標籤上，[產品版本] 應該為 2.6.1198.718 或更高版本。<br/><br/> 如果您要更新代理程式，請確定沒有任何備份作業正在執行，然後[重新安裝代理程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
**Linux** | 從散發套件中存放庫，使用 RPM 或 DEB 套件進行安裝。 這是安裝和升級 Azure Linux 代理程式的慣用方法。 所有[認可的散發套件提供者](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。 代理程式可從 [GitHub](https://github.com/Azure/WALinuxAgent) 取得，但不建議從該處安裝。<br/><br/> 如果您要更新代理程式，請確定沒有任何備份作業正在執行，然後更新二進位檔。

>[!NOTE]
> **Azure 備份現在支援使用 Azure 虛擬機器備份解決方案進行選擇性磁碟備份和還原。**
>
>目前，Azure 備份支援使用虛擬機器備份解決方案來同時備份 VM 中的所有磁碟 (作業系統與資料)。 使用排除磁碟功能時，您可以從 VM 的諸多資料磁碟中，選擇備份一個或多個磁碟。 如此可提供有效率且符合成本效益的解決方案，以滿足您的備份和還原需求。 每個復原點都有備份作業所含磁碟的資料，這進一步可讓您在還原作業期間，從給定的復原點還原磁碟子集。 從快照集和保存庫還原時都是如此。
>
>**若要註冊預覽版，請將電子郵件寄到 AskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>後續步驟

* 對 [Azure VM 代理程式](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)或 [Azure VM 備份](backup-azure-vms-troubleshoot.md)的任何問題進行疑難排解。
* [還原](backup-azure-arm-restore-vms.md) Azure VM。
