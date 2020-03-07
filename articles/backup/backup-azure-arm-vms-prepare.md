---
title: 備份復原服務保存庫中的 Azure VM
description: 說明如何使用 Azure 備份在復原服務保存庫中備份 Azure Vm
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363702"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>備份復原服務保存庫中的 Azure VM

本文說明如何使用[Azure 備份](backup-overview.md)服務來備份復原服務保存庫中的 Azure vm。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 準備 Azure VM。
> * 建立保存庫。
> * 探索 Vm 並設定備份原則。
> * 啟用 Azure Vm 的備份。
> * 執行初始備份。

> [!NOTE]
> 本文說明如何設定保存庫，並選取要備份的 Vm。 這有助於您備份多個 VM。 或者，您可以直接從 VM 設定來[備份單一 AZURE vm](backup-azure-vms-first-look-arm.md) 。

## <a name="before-you-start"></a>開始之前

* 請[參閱](backup-architecture.md#architecture-built-in-azure-vm-backup)Azure VM 備份架構。
* [深入了解](backup-azure-vms-introduction.md) Azure VM 備份和備份擴充功能。
* 設定備份之前，請先[參閱支援矩陣](backup-support-matrix-iaas.md)。

此外，在某些情況下，您可能還需要做幾件事：

* 在**vm 上安裝 vm 代理程式**： Azure 備份會藉由將擴充功能安裝到在電腦上執行的 azure vm 代理程式來備份 Azure vm。 如果您的 VM 是從 Azure marketplace 映射建立的，則代理程式已安裝且正在執行。 如果您建立自訂 VM，或遷移內部部署機器，您可能需要[手動安裝代理程式](#install-the-vm-agent)。

## <a name="create-a-vault"></a>建立保存庫

 保存庫會儲存在一段時間內建立的備份和復原點，並儲存與備份的機器相關聯的備份原則。 請依照下列方式建立保存庫：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [搜尋] 中，輸入**Recovery Services**。 在 [**服務**] 下，按一下 [復原**服務保存庫**]。

     ![搜尋復原服務保存庫](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. 在 [復原**服務保存庫**] 功能表中，按一下 [ **+ 新增**]。

     ![建立復原服務保存庫的步驟 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. 在 [復原**服務保存庫**] 中，輸入易記名稱來識別保存庫。
    * 必須是 Azure 訂用帳戶中唯一的名稱。
    * 它可以包含 2 到 50 個字元。
    * 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。
5. 選取要在其中建立保存庫的 Azure 訂用帳戶、資源群組和地理區域。 然後，按一下 [建立]。
    * 建立保存庫可能需要一些時間。
    * 請監視入口網站右上方區域中的狀態通知。

保存庫建立之後，它會出現在 [復原服務保存庫] 清單中。 如果您沒有看到保存庫，請選取 [重新整理]。

![備份保存庫的清單](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Azure 備份現在允許自訂由 Azure 備份服務所建立的資源組名。 如需詳細資訊，請參閱[虛擬機器的 Azure 備份資源群組](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)。

### <a name="modify-storage-replication"></a>修改儲存體複寫

根據預設，保存庫會使用[異地多餘儲存體（GRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

* 如果保存庫是您的主要備份機制，建議使用 GRS。
* 您可以使用[本機-多餘儲存體（LRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)來取得較便宜的選項。

修改儲存體複寫類型，如下所示：

1. 在新保存庫中，按一下 [**設定**] 區段中的 [**屬性**]。
2. 在 [**屬性**] 的 [**備份**設定] 底下，按一下 [**更新**]。
3. 選取 [儲存體] 複寫類型，然後**按一下 [儲存]** 。

      ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > 設定保存庫並包含備份專案之後，即無法修改儲存體複寫類型。 如果您想要這樣做，您必須重新建立保存庫。

## <a name="apply-a-backup-policy"></a>套用備份原則

設定保存庫的備份原則。

1. 在保存庫中，按一下 [**總覽**] 區段中的 [ **+ 備份**]。

   ![備份按鈕](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. 在 [**備份目標**] > **您的工作負載在何處執行？** 選取 [ **Azure**]。 在 [**您要備份什麼？** ] 中，選取 [**虛擬機器** **] >  [確定]** 。 這會在保存庫中註冊 VM 擴充功能。

   ![備份和備份目標窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. 在 [備份原則] 中，選取要與保存庫產生關聯的原則。
    * 預設原則會每天備份一次 VM。 每日備份會保留30天。 立即修復快照集會保留兩天。
    * 如果您不想要使用預設原則，請選取 [**建立新**的]，然後建立自訂原則，如下一個程式所述。

      ![預設備份原則](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. 在 [**選取虛擬機器**] 中，選取您想要使用原則備份的 vm。 然後按一下 **[確定]** 。

   * 系統會驗證選取的 Vm。
   * 您只能選取與保存庫位於相同區域中的 VM。
   * VM 只能在單一保存庫中備份。

     ![[選取虛擬機器] 窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. 在 [**備份**] 中，按一下 [**啟用備份**]。 這會將原則部署到保存庫和 VM，並在執行於 Azure VM 的 VM 代理程式上安裝備份擴充功能。

     ![[啟用備份] 按鈕](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

啟用備份後：

* 無論 VM 是否在執行，備份服務都會安裝備份擴充功能。
* 初始備份會根據您的備份排程執行。
* 當備份執行時，請注意：
  * 執行的 VM 具有最大的機會來捕獲應用程式一致的復原點。
  * 不過，即使 VM 已關閉，也會進行備份。 這類 VM 稱為離線 VM。 在此情況下，復原點將會是損毀一致。
* 不需要明確的輸出連線來允許 Azure Vm 的備份。

### <a name="create-a-custom-policy"></a>建立自訂原則

如果您選擇建立新的備份原則，請填寫原則設定。

1. 在 [**原則名稱**] 中，指定有意義的名稱。
2. 在 [**備份排程**] 中，指定應採用備份的時間。 您可以針對 Azure Vm 進行每日或每週備份。
3. 在 [**立即還原**] 中，指定您想要在本機保留快照集以進行立即還原的時間長度。
    * 當您還原時，備份的 VM 磁片會從儲存體複製到復原儲存位置。 使用「立即還原」，您可以利用在備份作業期間所建立的本機儲存快照集，而不需要等待備份資料傳輸到保存庫。
    * 您可以保留快照集，以便在一到五天之間進行立即還原。 預設設定為兩天。
4. 在 [**保留範圍**] 中，指定您想要保留每日或每週備份點的時間長度。
5. 在 [**保留每月備份點**] 中，指定是否要保留每日或每週備份的每月備份。
6. 按一下 [確定] 以儲存原則。

    ![新增備份原則](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure 備份在進行 Azure VM 備份時不支援依據日光節約變更而自動調整時鐘。 當發生時間變更時，請視需要手動修改備份原則。

## <a name="trigger-the-initial-backup"></a>觸發初始備份

初始備份會根據排程執行，但您可以立即執行，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [**備份專案**] 中，按一下 [ **Azure 虛擬機器**]。
3. 在 [**備份專案**] 清單中，按一下省略號（...）。
4. 按一下 [立即備份]。
5. 在 [**立即備份**] 中，使用行事曆控制項選取復原點應保留的最後一天。 然後按一下 **[確定]** 。
6. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的 VM 大小，建立初始備份可能需要花一點時間。

## <a name="verify-backup-job-status"></a>確認備份作業狀態

每個 VM 備份的備份作業詳細資料包含兩個階段：**快照**集階段，後面接著 [將**資料傳送至保存庫**] 階段。<br/>
快照集階段會保證與磁片一併儲存的復原點可用性，以進行**立即還原**，而且最多可有五天的時間，視使用者所設定的快照集保留而定。 將資料傳輸至保存庫會在保存庫中建立復原點以進行長期保留。 只有在快照集階段完成之後，才會開始將資料傳輸到保存庫。

  ![備份作業狀態](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

後端有兩**個子**工作，一個用於前端備份作業，可以從 [**備份作業**詳細資料] 分頁中檢查，如下所示：

  ![備份作業狀態](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

將**資料傳輸至保存庫**階段可能需要數天的時間才能完成，視磁片大小、每個磁片的變換和其他數個因素而定。

作業狀態會根據下列案例而有所不同：

**快照式** | **將資料傳輸到保存庫** | **作業狀態**
--- | --- | ---
已完成 | 進行中 | 進行中
已完成 | 略過 | 已完成
已完成 | 已完成 | 已完成
已完成 | 失敗 | 已完成，但出現警告
失敗 | 失敗 | 失敗

現在有了這項功能，針對相同的 VM，兩個備份可以平行執行，但是在任一階段（快照集，將資料傳送到保存庫）中，只有一個子工作可以執行。 因此，在某些情況下，進行中的備份作業會導致下一天的備份失敗，並透過這項分離功能來避免。 如果前一天的備份作業正在進行中，則在將**資料傳輸到保存庫**時，會略過後續日的備份。
在保存庫中建立的增量復原點將會從保存庫中建立的最後一個復原點來捕獲所有變換。 使用者不會產生任何成本影響。

## <a name="optional-steps"></a>選擇性步驟

### <a name="install-the-vm-agent"></a>安裝 VM 代理程式

為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 是從 Azure Marketplace 映射建立的，則代理程式已安裝且正在執行。 如果您建立自訂 VM，或遷移內部部署機器，您可能需要手動安裝代理程式，如下表中摘要說明。

**VM** | **詳細資料**
--- | ---
**Windows** | 1.[下載並安裝](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理程式 MSI 檔案。<br/><br/> 2. 在機器上以系統管理員許可權安裝。<br/><br/> 3. 確認安裝。 在 VM 的  *C:\WindowsAzure\Packages*  中，以滑鼠右鍵按一下  **WaAppAgent**  > **屬性**。 在 [**詳細資料**] 索引標籤上，**產品版本**應為2.6.1198.718 或更高。<br/><br/> 如果您要更新代理程式，請確定沒有任何備份作業正在執行，然後再[重新安裝代理程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
**Linux** | 從散發套件存放庫使用 RPM 或 DEB 套件進行安裝。 這是安裝和升級 Azure Linux 代理程式的慣用方法。 所有[認可的散發套件提供者](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。 代理程式可從 [GitHub](https://github.com/Azure/WALinuxAgent) 取得，但不建議從該處安裝。<br/><br/> 如果您要更新代理程式，請確定沒有任何備份作業正在執行，並更新二進位檔。

>[!NOTE]
> **Azure 備份現在支援使用 Azure 虛擬機器備份解決方案進行選擇性磁片備份和還原。**
>
>目前，Azure 備份支援使用虛擬機器備份解決方案，同時備份 VM 中的所有磁片（作業系統和資料）。 使用 [排除磁片] 功能時，您可以選擇從 VM 中的多個資料磁片備份一或數個。 這為您的備份和還原需求提供有效率且符合成本效益的解決方案。 每個復原點都包含備份作業中包含的磁片資料，這可讓您在還原作業期間，擁有從指定復原點還原的磁片子集。 這適用于從快照集和保存庫還原兩者。
>
>**若要註冊預覽版，請在 AskAzureBackupTeam@microsoft.com 寫信給我們**

## <a name="next-steps"></a>後續步驟

* 針對[AZURE vm 代理](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)程式或[azure vm 備份](backup-azure-vms-troubleshoot.md)的任何問題進行疑難排解。
* [還原](backup-azure-arm-restore-vms.md)Azure Vm。
