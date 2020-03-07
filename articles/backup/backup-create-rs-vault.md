---
title: 建立復原服務保存庫
description: 在本文中，您將瞭解如何建立復原服務保存庫，以儲存備份和復原點。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: eb7dcb4038260b1edac57a2163a1c0eae29ee452
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363695"
---
# <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一個實體，可儲存一段時間以來建立的備份和復原點。 復原服務保存庫也包含與受保護虛擬機器相關聯的備份原則。

若要建立復原服務保存庫：

1. 在 [Azure 入口網站](https://portal.azure.com/)中登入您的訂用帳戶。

2. 在左側功能表上，選取 [所有服務]。

    ![選取所有服務](./media/backup-create-rs-vault/click-all-services.png)

3. 在 [所有服務] 對話方塊中，輸入 **Recovery Services**。 資源清單會根據您的輸入進行篩選。 在資源清單中，選取 [復原服務保存庫]。

    ![輸入並選擇復原服務保存庫](./media/backup-create-rs-vault/all-services.png)

    隨即會在訂用帳戶中出現 [復原服務保存庫] 清單。

4. 在 [復原服務保存庫] 儀表板上，選取 [新增]。

    ![新增復原服務保存庫](./media/backup-create-rs-vault/add-button-create-vault.png)

    [復原服務保存庫] 對話方塊隨即開啟。 提供 [名稱]、[訂用帳戶]、[資源群組] 和 [位置] 的值。

    ![設定復原服務保存庫](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **名稱**：輸入可識別保存庫的易記名稱。 該名稱必須是 Azure 訂用帳戶中唯一的名稱。 指定的名稱至少要有兩個字元，但不能超過 50 個字元。 名稱開頭必須是字母，且只能包含字母、數字和連字號。
   - **訂用帳戶**：選擇要使用的訂用帳戶。 如果您是唯一一個訂用帳戶的成員，就會看到該名稱。 如果您不確定要使用哪個訂用帳戶，請使用預設 (建議) 的訂用帳戶。 只有在您的公司或學校帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。
   - **資源群組**：使用現有資源群組，或建立新的群組。 若要查看您訂用帳戶中可用的資源群組清單，請選取 [使用現有的]﹐然後從下拉式清單方塊中選取資源。 若要建立新的資源群組，請選取 [新建]，然後輸入名稱。 如需資源群組的完整資訊，請參閱 [Azure Resource Manager 概觀](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
   - **位置**：選取保存庫的地理區域。 若要要建立保存庫來保護虛擬機器，則保存庫**必須**與虛擬機器位於相同區域。

      > [!IMPORTANT]
      > 如果不確定您 VM 的位置，請關閉對話方塊。 移至入口網站中的虛擬機器清單。 如果您在數個區域中有虛擬機器，請在每個區域中建立復原服務保存庫。 請在第一個位置建立保存庫，然後再建立另一個位置的保存庫。 不需要指定用來儲存備份資料的儲存體帳戶。 復原服務保存庫和 Azure 備份服務會自動處理該事宜。
      >
      >

5. 當您準備好建立復原服務保存庫時，選取 [建立]。

    ![建立復原服務保存庫](./media/backup-create-rs-vault/click-create-button.png)

    建立復原服務保存庫可能需要一些時間。 請監視入口網站右上角 [通知] 區域中的狀態通知。 保存庫建立之後，就可以在 [復原服務保存庫] 的清單中看到。 如果您沒有看到保存庫，請選取 [重新整理]。

     ![重新整理備份保存庫的清單](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>設定儲存體備援

Azure 備份會自動處理保存庫的儲存體。 您必須指定該儲存體的複寫方式。

1. 從 [復原服務保存庫] 刀鋒視窗，按一下 [新增保存庫]。 在 [**設定**] 區段下，按一下 [**屬性**]。
2. 在 [**屬性**] 的 [**備份**設定] 底下，按一下 [**更新**]。

3. 選取 [儲存體] 複寫類型，然後**按一下 [儲存]** 。

     ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - 如果您使用 Azure 做為主要的備份儲存體端點，我們建議您繼續使用預設的**異地多餘**設定。
   - 如果您未使用 Azure 做為主要的備份儲存體端點，則選擇 [本地備援]，以減少 Azure 儲存體成本。
   - 深入瞭解[地理](../storage/common/storage-redundancy-grs.md)和[本機](../storage/common/storage-redundancy-lrs.md)冗余。

> [!NOTE]
> 您必須先變更復原服務保存庫的**儲存體複寫類型**（本機-多餘/異地），才能在保存庫中進行備份。 一旦您設定備份，[修改] 選項就會停用，而且您無法變更**儲存體複寫類型**。

## <a name="set-cross-region-restore"></a>設定跨區域還原

做為其中一個還原選項，跨區域還原（CRR）可讓您在次要區域（也就是[azure 配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)）中還原 azure vm。 此選項可讓您：

- 在有審核或合規性需求時進行演練
- 如果主要區域發生損毀，請還原 VM 或其磁片。

若要選擇這項功能，請從 [**備份**設定] 分頁選取 [**啟用跨區域還原**]。

在此程式中，會有價格含意，因為它是在儲存體層級。

>[!NOTE]
>開始之前：
>
>- 如需支援的 managed 類型和區域清單，請參閱[支援矩陣](backup-support-matrix.md#cross-region-restore)。
>- 「跨區域還原」（CRR）功能目前僅適用于下欄區域：美國中西部、美國西部2、澳大利亞東部和澳大利亞東南部。 
>- CRR 是適用于任何 GRS 保存庫的保存庫層級加入宣告功能（預設為關閉）。
>- 請使用下列命令，將您的訂用帳戶上架此功能：<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- 如果您在公開有限預覽期間上架此功能，審核核准電子郵件將會包含定價原則詳細資料。
>- 加入宣告之後，最多可能需要48小時的時間，才能在次要區域中使用備份專案。
>- 目前只有備份管理類型才支援 CRR-ARM Azure VM （不支援傳統的 Azure VM）。  當其他管理類型支援 CRR 時，將會**自動**註冊。

### <a name="configure-cross-region-restore"></a>設定跨區域還原

以 GRS 冗余建立的保存庫包含設定跨區域還原功能的選項。 每個 GRS 保存庫都會有橫幅，這會連結到檔。 若要設定保存庫的 CRR，請移至 [備份設定] 分頁，其中包含啟用此功能的選項。

 ![備份設定橫幅](./media/backup-azure-arm-restore-vms/banner.png)

1. 在入口網站中，移至 [復原服務保存庫] > [設定] > [內容]。
2. 按一下 [**在此保存庫中啟用跨區域還原**] 以啟用功能。

   ![在此保存庫中按一下 [啟用跨區域還原] 之前](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![在此保存庫中按一下 [啟用跨區域還原] 之後](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

瞭解如何[在次要區域中查看備份專案](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region)。

瞭解如何[在次要區域中還原](backup-azure-arm-restore-vms.md#restore-in-secondary-region)。

瞭解如何[監視次要區域還原作業](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)。

## <a name="modifying-default-settings"></a>修改預設設定

我們強烈建議您在保存庫中設定備份之前，先檢查**儲存體複寫類型**和**安全性設定**的預設值。

- **儲存體複寫類型**預設會設定為 [**異地冗余**]。 一旦您設定備份之後，[修改] 選項就會停用。 請遵循下列[步驟](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)來審查和修改設定。

- 預設會在新建立的保存庫上**啟用**虛**刪除**，以防止意外或惡意刪除的備份資料。 請遵循下列[步驟](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete)來審查和修改設定。

## <a name="next-steps"></a>後續步驟

[深入瞭解](backup-azure-recovery-services-vault-overview.md)復原服務保存庫。
[深入瞭解](backup-azure-delete-vault.md)刪除復原服務保存庫。
