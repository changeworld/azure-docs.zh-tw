---
title: 管理和監視 Azure VM 備份
description: 瞭解如何使用 Azure 備份服務來管理和監視 Azure VM 備份。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: dd4e9dc199048b3faf3da0cadfdf60bdcb26c5bc
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989683"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>使用 Azure 備份服務管理 Azure VM 備份

本文說明如何管理使用[Azure 備份服務](backup-overview.md)備份的 Azure 虛擬機器（vm）。 本文也會摘要說明您可以在保存庫儀表板上找到的備份資訊。

在 Azure 入口網站中，復原服務保存庫儀表板會提供保存庫資訊的存取權，包括：

* 最新的備份，也就是最新的還原點。
* 備份原則。
* 所有備份快照集的總大小。
* 已啟用備份的 Vm 數目。

您可以使用 [儀表板]，並向下切入至個別 Vm 來管理備份。 若要開始機器備份，請在儀表板上開啟保存庫。

![具有滑杆的完整儀表板視圖](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>在儀表板上查看 Vm

若要在保存庫儀表板上查看 Vm：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表上，選取 **[流覽]** 。 在資源清單中輸入 **復原服務**。 當您輸入時，系統會根據您的輸入來篩選清單。 選取 [復原服務保存庫]。

    ![建立復原服務保存庫](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. 為了方便使用，請以滑鼠右鍵按一下保存庫，然後選取 [**釘選到儀表板**]。
4. 開啟保存庫儀表板。

    ![開啟保存庫儀表板和設定窗格](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. 在 [**備份專案**] 圖格上，選取 [ **Azure 虛擬機器**]。

    ![開啟 [備份專案] 圖格](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. 在 [**備份專案**] 窗格中，您可以查看受保護 vm 的清單。 在此範例中，保存庫會保護一部虛擬機器： demobackup。  

    ![查看 [備份專案] 窗格](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. 從保存庫專案的儀表板，修改備份原則、執行隨選備份、停止或繼續保護 Vm、刪除備份資料、查看還原點，以及執行還原。

    ![[備份專案] 儀表板和 [設定] 窗格](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>管理 VM 的備份原則

若要管理備份原則：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 開啟保存庫儀表板。
2. 在 [**備份專案**] 圖格上，選取 [ **Azure 虛擬機器**]。

    ![開啟 [備份專案] 圖格](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. 在 [**備份專案**] 窗格中，您可以使用最新的還原點時間來查看受保護的 vm 清單和上次備份狀態。

    ![查看 [備份專案] 窗格](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. 從保存庫專案的 [儀表板] 中，您可以選取備份原則。

   * 若要切換原則，請選取不同的原則，然後選取 [**儲存**]。 新原則時會立即套用至保存庫。

     ![選擇備份原則](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>執行隨選備份

設定虛擬機器的保護之後，您可以執行 VM 的隨選備份。 請記住下列詳細資料：

* 如果初始備份擱置中，隨選備份會在復原服務保存庫中建立 VM 的完整複本。
* 如果初始備份已完成，隨選備份只會將先前快照集的變更傳送到復原服務保存庫。 也就是，之後的備份一律是增量的。
* 隨選備份的保留範圍是您在觸發備份時所指定的保留值。

若要觸發隨選備份：

1. 在保存[庫專案儀表板](#view-vms-on-the-dashboard)的 [**受保護的專案**] 底下，選取 [**備份專案**]。

    ![[立即備份] 選項](./media/backup-azure-manage-vms/backup-now-button.png)

2. 在 [**備份管理類型**] 中，選取 [ **Azure 虛擬機器**]。 [**備份專案（Azure 虛擬機器）** ] 窗格隨即出現。
3. 選取 VM，然後選取 [**立即備份**] 以建立隨選備份。 [**立即備份**] 窗格隨即出現。
4. 在 [**保留備份**于] 欄位中，指定要保留備份的日期。

    ![立即備份行事曆](./media/backup-azure-manage-vms/backup-now-check.png)

5. 選取 **[確定]** 以執行備份作業。

若要追蹤作業的進度，請在保存庫儀表板上，選取 [**備份作業**] 圖格。

## <a name="stop-protecting-a-vm"></a>停止保護 VM

有兩種方式可停止保護 VM：

* **停止保護並保留備份資料**。 此選項會阻止所有未來的備份工作保護您的 VM;不過，Azure 備份服務會保留已備份的復原點。  您必須付費以將復原點保留在保存庫中（如需詳細資訊，請參閱[Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)）。 如有需要，您將能夠還原 VM。 如果您決定繼續 VM 保護，則可以使用 [*繼續備份*] 選項。
* **停止保護並刪除備份資料**。 此選項會阻止所有未來的備份工作保護您的 VM，並刪除所有復原點。 您將無法還原 VM，也無法使用 [*繼續備份*] 選項。

>[!NOTE]
>如果您刪除資料來源，但沒有停止備份，新的備份將會失敗。 舊的復原點將會根據原則過期，但最後一個復原點一律會保留，直到您停止備份並刪除資料為止。
>

### <a name="stop-protection-and-retain-backup-data"></a>停止保護並保留備份資料

若要停止保護並保留 VM 的資料：

1. 在保存[庫專案的儀表板](#view-vms-on-the-dashboard)上，選取 [**停止備份**]。
2. 選擇 [**保留備份資料**]，並視需要確認您的選擇。 如有需要，請新增批註。 如果您不確定專案的名稱，請將滑鼠暫留在驚嘆號上以查看名稱。

    ![保留備份資料](./media/backup-azure-manage-vms/retain-backup-data.png)

通知可讓您知道備份作業已停止。

### <a name="stop-protection-and-delete-backup-data"></a>停止保護並刪除備份資料

若要停止保護和刪除 VM 的資料：

1. 在保存[庫專案的儀表板](#view-vms-on-the-dashboard)上，選取 [**停止備份**]。
2. 選擇 [**刪除備份資料**]，並視需要確認您的選擇。 輸入備份專案的名稱，並視需要新增批註。

    ![刪除備份資料](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>繼續保護 VM

如果您在 [停止 VM 保護] 期間選擇 [[停止保護並保留備份資料](#stop-protection-and-retain-backup-data)] 選項，則可以使用 [**繼續備份**]。 如果您選擇 [[停止保護] 和 [刪除備份資料](#stop-protection-and-delete-backup-data)] 選項或[刪除備份資料](#delete-backup-data)，則無法使用此選項。

若要繼續保護 VM：

1. 在保存[庫專案的儀表板](#view-vms-on-the-dashboard)上，選取 [**繼續備份**]。

2. 請依照[管理備份原則](#manage-backup-policy-for-a-vm)中的步驟來指派 VM 的原則。 您不需要選擇 VM 的初始保護原則。
3. 將備份原則套用至 VM 之後，您會看到下列訊息：

    ![指出已成功保護 VM 的訊息](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>刪除備份資料

有兩種方式可刪除 VM 的備份資料：

* 從保存庫專案儀表板中，選取 [停止備份]，然後依照 [[停止保護和刪除備份資料](#stop-protection-and-delete-backup-data)] 選項的指示進行。

  ![選取 [停止備份]](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* 從保存庫專案儀表板中，選取 [刪除備份資料]。 如果您已在停止 VM 保護期間選擇[停止保護並保留備份資料](#stop-protection-and-retain-backup-data)選項，則會啟用此選項。

  ![選取 [刪除備份]](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * 在保存[庫專案儀表板](#view-vms-on-the-dashboard)上，選取 [**刪除備份資料**]。
  * 輸入備份專案的名稱，以確認您想要刪除復原點。

    ![刪除備份資料](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * 若要刪除專案的備份資料，請選取 [**刪除**]。 通知訊息可讓您知道備份資料已被刪除。

為了保護您的資料，Azure 備份包含虛刪除功能。 透過虛刪除，即使在 VM 的備份（所有復原點）遭到刪除之後，備份資料仍會保留14個額外的天數。 如需詳細資訊，請參閱虛[刪除檔](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)。

  > [!NOTE]
  > 當您刪除備份資料時，會刪除所有相關聯的復原點。 您無法選擇要刪除的特定復原點。

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>主要資料來源已不存在的備份專案

* 如果已刪除或移動為 Azure 備份設定的 Azure Vm，但未停止保護，則排定的備份作業和隨選（臨機操作）備份工作將會失敗，並出現錯誤 UserErrorVmNotFoundV2。 只有針對失敗的隨選備份作業（不會顯示失敗的排程工作），備份預先檢查才會顯示為 [重大]。
* 這些備份專案會在系統中保持作用中狀態，遵循使用者所設定的備份和保留原則。 這些 Azure Vm 的備份資料將會根據保留原則加以保留。 已過期的復原點（最後一個復原點除外）會根據備份原則中設定的保留範圍來清除。
* 建議使用者刪除主要資料來源已不存在的備份專案，以避免任何額外的成本，如果已不再需要刪除資源的備份專案/資料，因為最後一個復原點會永久保留，而使用者則依據適用的備份定價收費。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[從 VM 的設定備份 Azure vm](backup-azure-vms-first-look-arm.md)。
* 瞭解如何[還原 vm](backup-azure-arm-restore-vms.md)。
* 瞭解如何[監視 AZURE VM 備份](backup-azure-monitor-vms.md)。
