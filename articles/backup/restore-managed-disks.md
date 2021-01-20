---
title: 還原 Azure 受控磁碟
description: 瞭解如何從 Azure 入口網站還原 Azure 受控磁碟。
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 848a7476b1c5095d4e4d3156d4c7ce33da777090
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611129"
---
# <a name="restore-azure-managed-disks-in-preview"></a>還原預覽版中的 Azure 受控磁碟 () 

>[!IMPORTANT]
>Azure 磁片備份處於預覽狀態，不含服務等級協定，因此不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 如需區域可用性，請參閱 [支援矩陣](disk-backup-support-matrix.md)。
>
>[填寫此表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以註冊預覽。

本文說明如何從 Azure 備份所建立的還原點還原 [Azure 受控磁碟](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) 。

目前，Original-Location 復原 (OLR) 選項，藉由取代不支援備份的現有來源磁片來進行還原。 您可以從復原點還原，以在與來源磁片相同的資源群組中建立新的磁片，以從中取得備份或任何其他資源群組。 這就是所謂的 Alternate-Location 復原 (ALR) ，這有助於保持來源磁片和還原 (新的) 磁片。

在本文中，您將學會如何：

- 還原以建立新的磁片

- 追蹤還原作業狀態

## <a name="restore-to-create-a-new-disk"></a>還原以建立新的磁片

備份保存庫會使用受控識別來存取其他 Azure 資源。 若要從備份還原，備份保存庫的受控識別需要在磁片還原的資源群組上有一組許可權。

備份保存庫會使用系統指派的受控識別，限制為每個資源一個，並系結到此資源的生命週期。 您可以使用 Azure 角色型存取控制 (Azure RBAC) ，將許可權授與受控識別。 受控識別是特殊類型的服務主體，只可搭配 Azure 資源使用。 深入瞭解 [受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

執行還原作業需要下列先決條件：

1. 將 **磁片還原操作員** 角色指派給資源群組上的備份保存庫的受控識別，此資源群組中的磁片將由 Azure 備份服務還原。

    >[!NOTE]
    > 您可以選擇與來源磁片相同的資源群組，從該處取得備份或相同或不同訂用帳戶內的任何其他資源群組。

    1. 移至要將磁片還原至其中的資源群組。 例如，資源群組為 *TargetRG*。

    1. 移至 [**存取控制] (IAM)** ，然後選取 [**新增角色指派**]

    1. 在右側的內容窗格中，選取 [**角色**] 下拉式清單中的 [**磁片還原操作員**]。 選取備份保存庫的受控識別，並 **儲存**。

        >[!TIP]
        >輸入備份保存庫的名稱，以選取保存庫的受控識別。

        ![選取磁片還原操作員角色](./media/restore-managed-disks/disk-restore-operator-role.png)

1. 確認備份保存庫的受控識別在要復原磁碟的資源群組上有一組正確的角色指派。

    1. 移至 **備份保存庫-> 身分識別**，然後選取 [ **Azure 角色指派**]

        ![選取 Azure 角色指派](./media/restore-managed-disks/azure-role-assignments.png)

    1. 確認角色、資源名稱和資源類型是否正確顯示。

        ![確認角色、資源名稱和資源類型](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >當角色指派在入口網站上正確反映時，可能需要大約15分鐘的時間，才會在備份保存庫的受控識別上套用許可權。
    >
    >在排程備份或隨選備份作業期間，Azure 備份會將磁片增量快照集儲存在設定磁片備份期間所提供的快照集資源群組中。 Azure 備份在還原作業期間使用這些增量快照集。 如果快照集已從快照集資源群組中刪除或移動，或在快照集資源群組上撤銷備份保存庫角色指派，則還原作業會失敗。

1. 如果要還原的磁片使用 [客戶管理的金鑰進行加密 (CMK)](https://docs.microsoft.com/azure/virtual-machines/disks-enable-customer-managed-keys-portal)或使用 [以平臺管理的金鑰和客戶管理的金鑰進行雙重加密](https://docs.microsoft.com/azure/virtual-machines/disks-enable-double-encryption-at-rest-portal)，然後在 **磁片加密集** 資源上，將「**讀取** 者」角色許可權指派給備份保存庫的受控識別。

一旦符合必要條件，請遵循下列步驟來執行還原作業。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，移至 [ **備份中心**]。 在 [**管理**] 區段底下選取 [**備份實例**]。 從備份實例清單中，選取您要執行還原操作的磁片備份實例。

    ![備份實例的清單](./media/restore-managed-disks/backup-instances.png)

    或者，您可以從用來設定磁片備份的備份保存庫執行此操作。

1. 在 [ **備份實例** ] 畫面中，選取您要用來執行還原操作的還原點，然後選取 [ **還原**]。

    ![選取還原點](./media/restore-managed-disks/select-restore-point.png)

1. 在 **還原** 工作流程中，檢查 **基本概念** 並 **選取 [復原點** ] 索引標籤資訊，然後選取 **[下一步：還原參數**]。

    ![檢查基本概念並選取復原點資訊](./media/restore-managed-disks/review-information.png)

1. 在 [ **還原參數** ] 索引標籤中，選取您要還原備份的 **目標訂** 用帳戶和 **目標資源群組** 。 提供要還原之磁片的名稱。 選取 **[下一步：檢查 + 還原]**。

    ![還原參數](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >使用磁片備份解決方案 Azure 備份備份的磁片也可以透過使用 Azure VM 備份解決方案與復原服務保存庫進行備份 Azure 備份。 如果您已設定此磁片所連接之 Azure VM 的保護，您也可以使用 Azure VM 還原作業。 您可以選擇從對應的 Azure VM 備份實例的復原點還原 VM，或磁片和檔案或資料夾。 如需詳細資訊，請參閱 [AZURE VM 備份](https://docs.microsoft.com/azure/backup/about-azure-vm-restore)。

1. 驗證成功之後，請選取 [ **還原** ] 以啟動還原作業。

    ![起始還原作業](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > 驗證可能需要幾分鐘才能完成，您才能觸發還原作業。 如果有下列情況，驗證可能會失敗：
    >
    > - 在 **還原的磁片名稱** 中提供的相同名稱的磁片已經存在於 **目標資源群組** 中
    > - 備份保存庫的受控識別在 **目標資源群組** 上沒有有效的角色指派
    > - 備份保存庫的受控識別角色指派會在儲存增量快照集的 **快照集資源群組** 上撤銷
    > - 如果從快照集資源群組刪除或移動增量快照集

Restore 會在還原作業期間提供的目標資源群組中，從選取的復原點建立新的磁片。 若要在現有的虛擬機器上使用還原的磁片，您必須執行更多步驟：

- 如果還原的磁片是資料磁片，您可以將現有的磁片連接至虛擬機器。 如果還原的磁片是 OS 磁片，您可以從 [**設定**] 區段中的 [**虛擬機器**] 窗格->**磁片**] 功能表下的 AZURE 入口網站，交換虛擬機器的 OS 磁片。

    ![交換 OS 磁片](./media/restore-managed-disks/swap-os-disks.png)

- 針對 Windows 虛擬機器，如果還原的磁片是資料磁片，請遵循指示從虛擬機器卸 [離原始資料磁片](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-the-portal) 。 然後 [將還原的磁片連接](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) 至虛擬機器。 遵循指示，將虛擬機器 [的 OS 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/os-disk-swap) 與還原的磁片交換。

- 針對 Linux 虛擬機器，如果還原的磁片是資料磁片，請遵循指示從虛擬機器卸 [離原始資料磁片](https://docs.microsoft.com/azure/virtual-machines/linux/detach-disk#detach-a-data-disk-using-the-portal) 。 然後 [將還原的磁片連接](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#attach-an-existing-disk) 至虛擬機器。 遵循指示，將虛擬機器 [的 OS 磁片](https://docs.microsoft.com/azure/virtual-machines/linux/os-disk-swap) 與還原的磁片交換。

建議您在順利完成還原作業之後，從 **目標資源群組** 上的備份保存庫的受控識別撤銷 **磁片還原操作員** 角色指派。

## <a name="track-a-restore-operation"></a>追蹤還原作業

在觸發還原作業之後，備份服務會建立用於追蹤的作業。 Azure 備份會在入口網站中顯示作業的相關通知。 若要查看還原作業進度：

1. 移至 [ **備份實例** ] 畫面。 它會顯示過去七天內具有作業和狀態的作業儀表板。

    ![作業儀表板](./media/restore-managed-disks/jobs-dashboard.png)

1. 若要查看還原作業的狀態，請選取 [ **全部查看** ]，顯示此備份實例的持續和過去工作。

    ![選取全視圖](./media/restore-managed-disks/view-all.png)

1. 檢查備份和還原作業的清單及其狀態。 從作業清單中選取作業，以查看作業詳細資料。

    ![作業清單](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>後續步驟

- [Azure 磁片備份常見問題](disk-backup-faq.md)
