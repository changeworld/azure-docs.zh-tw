---
title: 使用 Azure 入口網站還原 Vm
description: 使用 Azure 入口網站從復原點還原 Azure 虛擬機器
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 00b0f7313ba77037d90dcdb8ed04e5f61e335c55
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023324"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>如何在 Azure 入口網站中還原 Azure VM 資料

本文說明如何從儲存在 [Azure 備份](backup-overview.md)復原服務保存庫的復原點還原 Azure VM 資料。

## <a name="restore-options"></a>還原選項

Azure 備份提供數種方法來還原 VM。

**還原選項** | **詳細資料**
--- | ---
**建立新的 VM** | 從還原點快速建立及啟動基本 VM 並加以執行。<br/><br/> 您可指定 VM 的名稱，選取要放置 VM 的資源群組和虛擬網路 (VNet)，以及為已還原的 VM 指定儲存體帳戶。 建立新 VM 的區域必須與來源 VM 相同。
**還原磁碟** | 還原 VM 磁碟，以便後續用於建立新 VM。<br/><br/> Azure 備份提供一個範本，協助您自訂和建立 VM。 <br/><br> 還原作業會產生範本，您可以下載並使用該範本來指定自訂 VM 設定，並建立 VM。<br/><br/> 磁碟會複製到所指定的資源群組。<br/><br/> 或者，您可以將磁碟連結至現有 VM，或使用 PowerShell 建立新的 VM。<br/><br/> 此選項十分適用於自訂 VM、新增備份時沒有的組態設定，或新增必須使用範本或 PowerShell 來配置的設定。
**取代現有的** | 您可以還原磁碟，然後使用該磁碟來取代現有 VM 上的磁碟。<br/><br/> 目前的 VM 必須存在。 如果已刪除，則無法使用此選項。<br/><br/> Azure 備份會在取代磁碟前，取得現有 VM 的快照集，並儲存於所指定的暫存位置， 然後使用所選還原點來取代連線到 VM 的現有磁碟。<br/><br/> 快照集會複製到保存庫，並根據保留原則加以保留。 <br/><br/> 完成取代磁碟作業之後，原始磁碟會保留在資源群組中。 如果不需要，您可以選擇手動刪除原始磁片。 <br/><br/>不加密的受控 Vm 支援 Replace existing，包括[使用自訂映射建立](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)的 vm。 傳統 Vm 不支援。<br/><br/> 如果還原點中的磁碟數目多於或少於目前的 VM，則還原點中的磁碟數目只會反映該 VM 組態。<br><br> 與資源連結的 VM (例如[使用者指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 或 [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)) 不支援取代現有的項目，因為備份用戶端應用程式在執行還原時，無權使用這些資源。
**跨區域 (次要區域)** | 跨區域還原可用來還原次要區域 (即 [Azure 配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions) (機器翻譯)) 中的 Azure VM。<br><br> 如果備份是在次要區域中完成，即可將所有 Azure VM 還原至選取的復原點。<br><br> 這項功能適用於下列選項：<br> <li> [建立 VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> <li> [還原磁碟](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) (機器翻譯) <br><br> 目前不支援[取代現有磁碟](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) (機器翻譯) 選項。<br><br> 權限<br> 次要地區的還原作業可由備份管理員和應用程式管理員執行。

> [!NOTE]
> 您也可以復原 Azure VM 上的特定檔案和資料夾。 [深入了解](backup-azure-restore-files-from-vm.md)。

## <a name="storage-accounts"></a>儲存體帳戶

儲存體帳戶的一些詳細資料：

- **建立 vm**：當您建立新的 vm 時，vm 將會放在您指定的儲存體帳戶中。
- **復原磁碟**：當您復原磁碟時，磁片會複製到您指定的儲存體帳戶。 還原作業會產生範本，供您下載並用來指定自訂 VM 設定。 此範本會放在指定的儲存體帳戶中。
- **取代磁片**：當您更換現有 vm 上的磁片時，Azure 備份會在更換磁片之前，取得現有 vm 的快照集。 快照集會儲存在您指定的預備位置（儲存體帳戶）中。 此儲存體帳戶是用來在還原過程中暫時儲存快照集，我們建議您建立新的帳戶來執行此作業，之後就可以輕鬆地移除。
- **儲存體帳戶位置**：儲存體帳戶必須位於與保存庫相同的區域中。 只會顯示這些帳戶。 如果位置中沒有儲存體帳戶，您必須建立一個。
- **儲存體類型**：不支援 Blob 儲存體。
- **儲存體冗余**：不支援區域冗余儲存體（ZRS）。 帳戶名稱後面會以括弧括住帳戶的複寫和重複資訊。
- **Premium 儲存體**：
  - 還原非 premium Vm 時，不支援 premium 儲存體帳戶。
  - 還原受控 Vm 時，不支援使用網路規則設定的 premium 儲存體帳戶。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

若要還原 VM （建立新的 VM），請確定您具有還原 VM 作業的正確角色型存取控制（RBAC）[許可權](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)。

如果您沒有許可權，您可以[復原磁碟](#restore-disks)，然後在復原磁碟之後，使用在還原作業中產生的[範本](#use-templates-to-customize-a-restored-vm)來建立新的 VM。

## <a name="select-a-restore-point"></a>選取還原點

1. 在與您想要還原的 VM 相關聯的保存庫中，按一下 [**備份專案**] [  >  **Azure 虛擬機器**]。
2. 建立 VM。 根據預設，VM 儀表板上會顯示過去 30 天內的復原點。 您可以根據日期、時間範圍和不同類型的快照集一致性來顯示或篩選出超過 30 天的復原點。
3. 若要還原 VM，請按一下 [還原 VM]****。

    ![還原點](./media/backup-azure-arm-restore-vms/restore-point.png)

4. 選取要用於復原的還原點。

## <a name="choose-a-vm-restore-configuration"></a>選擇 VM 還原組態

1. 在 [還原設定]**** 中，選取還原選項：
    - **建立新**的：如果您想要建立新的 VM，請使用此選項。 您可以使用簡單的設定來建立 VM，或還原磁碟並建立自訂 VM。
    - **取代現有**的：如果您想要取代現有 VM 上的磁片，請使用此選項。

        ![還原組態精靈](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. 為您所選的還原選項指定設定。

## <a name="create-a-vm"></a>建立 VM

其中一個[還原選項](#restore-options)可讓您從還原點快速建立具有基本設定的 VM。

1. 在 [**還原**設定] [  >  **建立新**  >  的**還原類型**] 中，選取 [**建立虛擬機器**]。
2. 在 [**虛擬機器名稱**] 中，指定訂用帳戶中不存在的 VM。
3. 在 [資源群組]**** 中，選取新 VM 的現有資源群組，或使用全域的唯一名稱來建立新的資源群組。 如果您指派已經存在的名稱，Azure 就會為群組指派與 VM 相同的名稱。
4. 在 [虛擬網路]**** 中，選取將放置 VM 的 VNet。 與訂用帳戶相關聯的所有 VNet 均會顯示。 選取子網路。 預設會選取第一個子網路。
5. 在 [**儲存位置**] 中，指定 VM 的儲存體帳戶。 [深入了解](#storage-accounts)。

    ![還原組態精靈](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. 在 [還原設定]**** 中，選取 [確定]****。 在 [還原]**** 中，按一下 [還原]**** 以觸發還原作業。

## <a name="restore-disks"></a>還原磁碟

其中一個[還原選項](#restore-options)可讓您從還原點建立磁碟。 然後，您可以使用該磁碟來執行下列其中一項操作：

- 使用還原作業期間所產生的範本來自訂設定，並觸發 VM 部署。 您會編輯預設範本設定，並提交範本以進行 VM 部署。
- [將還原的磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)連結至現有 VM。
- 使用 PowerShell 從還原的磁片[建立新的 VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) 。

1. 在 [**還原**設定] [  >  **建立新**  >  的**還原類型**] 中，選取 [**復原磁碟**]。
2. 在 [資源群組]**** 中，選取已還原磁碟的現有資源群組，或使用全域的唯一名稱來建立新的資源群組。
3. 在 [儲存體帳戶]**** 中，指定要將 VHD 複製到其中的帳戶。 [深入了解](#storage-accounts)。

    ![已完成復原設定](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. 在 [還原設定]**** 中，選取 [確定]****。 在 [還原]**** 中，按一下 [還原]**** 以觸發還原作業。

當您的虛擬機器使用受控磁片，並選取 [**建立虛擬機器**] 選項時，Azure 備份不會使用指定的儲存體帳戶。 在**復原磁碟**和**立即還原**的案例中，儲存體帳戶僅用於儲存範本。 受控磁片會建立在指定的資源群組中。
當您的虛擬機器使用非受控磁片時，它們會以 blob 的形式還原至儲存體帳戶。

### <a name="use-templates-to-customize-a-restored-vm"></a>使用範本自訂還原的 VM

還原磁碟後，使用還原作業期間所產生的範本來自訂和建立新 VM：

1. 開啟相關作業的**還原作業詳細資料**。

2. 在 [還原作業詳細資料]**** 中，選取 [部署範本]**** 來起始範本部署。

    ![還原作業向下鑽研](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. 若要自訂範本中提供的 VM 設定，請按一下 [編輯範本]****。 如果您想要新增更多自訂，請按一下 [編輯參數]****。
    - [深入了解](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)自訂範本中的部署資源。
    - [深入了解](../azure-resource-manager/templates/template-syntax.md)編寫範本。

   ![載入範本部署](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. 輸入 VM 的自訂值，接受 [條款和條件]****，然後按一下 [購買]****。

   ![提交範本部署](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>取代現有的磁碟

其中一個[還原選項](#restore-options)可讓您使用選取的還原點取代現有 VM 磁碟。 [檢閱](#restore-options)所有還原選項。

1. 在 [還原設定]**** 中，按一下 [取代現有的]****。
2. 在 [還原類型]**** 中，選取 [取代磁碟]****。 這是將用來取代現有 VM 磁碟的還原點。
3. 在 [**預備位置**] 中，指定在還原過程中應該儲存目前受控磁片的快照集。 [深入了解](#storage-accounts)。

   ![還原組態精靈取代現有的](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>跨區域還原

做為其中一個[還原選項](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)，跨區域還原（CRR）可讓您在次要區域（也就是 azure 配對的區域）中還原 azure vm。

若要在預覽期間上架功能，請閱讀[開始之前一節](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore)。

若要查看是否已啟用 CRR，請依照[設定跨區域還原](backup-create-rs-vault.md#configure-cross-region-restore)中的指示進行。

### <a name="view-backup-items-in-secondary-region"></a>在次要區域中查看備份專案

如果已啟用 [CRR]，您可以在次要區域中查看備份專案。

1. 在入口網站中，移至 [復原**服務保存庫**] [  >  **備份專案**]
2. 按一下 [**次要地區**] 以查看次要地區中的專案。

![次要區域中的虛擬機器](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![選取次要地區](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>在次要區域中還原

次要區域還原使用者體驗將類似于主要區域還原使用者體驗。 在 [還原設定] 分頁中設定詳細資料以設定還原時，系統會提示您只提供次要區域參數。

![選擇要還原的 VM](./media/backup-azure-arm-restore-vms/sec-restore.png)

![選取還原點](./media/backup-azure-arm-restore-vms/sec-rp.png)

![還原設定](./media/backup-azure-arm-restore-vms/rest-config.png)

>[!NOTE]
>次要區域中的虛擬網路必須以唯一的方式指派，而且無法用於該資源群組中的任何其他 Vm。

![觸發程式還原進行中通知](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- 若要還原和建立 VM，請參閱[建立 vm](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm)。
- 若要以磁片的形式還原，請參閱[復原磁碟](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks)。

>[!NOTE]
>在觸發還原並于資料傳輸階段之後，就無法取消還原作業。

### <a name="monitoring-secondary-region-restore-jobs"></a>監視次要區域還原作業

1. 在入口網站中，移至 [復原**服務保存庫**] [  >  **備份作業**]
2. 按一下 [**次要地區**] 以查看次要地區中的專案。

![已篩選的備份作業](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>還原具有特殊設定的 Vm

有幾個可能需要還原 VM 的常見案例。

**案例** | **指引**
--- | ---
**使用 Hybrid Use Benefit 還原 VM** | 如果 Windows VM 使用 [Hybrid Use Benefit (HUB) 授權](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，請使用提供的範本 (將**授權類型**設定為 **Windows_Server**) 或 PowerShell 來還原磁碟並建立新 VM。  此設定也可以在建立 VM 之後套用。
**在 Azure 資料中心發生災害時還原 VM** | 如果保存庫使用 GRS 和主要資料中心來因應 VM 的停機狀況，則 Azure 備份可支援將備份的 VM 還原至配對的資料中心。 您可以在配對的資料中心內選取儲存體帳戶，然後依正常程序進行還原。 Azure 備份會使用配對區域中的計算服務來建立還原的 VM。 [深入了解](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)資料中心復原。<br><br> 如果保存庫使用 GRS，您可以選擇 [[跨區域還原](#cross-region-restore)] 這項新功能。 這可讓您在完整或部分中斷的情況下還原到第二個區域，甚至完全沒有中斷的情況。
**還原單一網域中的單一網域控制站 VM** | 像任何其他 VM 一樣地還原 VM。 請注意：<br/><br/> 從 Active Directory 的觀點來看，Azure VM 就像任何其他 VM 一樣。<br/><br/> 我們也提供了目錄服務還原模式 (DSRM)，因此，您可以進行所有的 Active Directory 復原案例。 [深入了解](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)虛擬化網域控制站的備份與還原考量。
**還原單一網域中的多個網域控制站 VM** | 如果可以透過網路連線到相同網域中的其他網域控制站，則可以像任何 VM 一樣還原網域控制站。 如果該網域控制站是網域內剩餘的最後一個網域控制站，或者您是在隔離的網路中進行復原，請使用[樹系復原](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)。
**還原單一樹系中的多個網域** | 我們建議使用[樹系復原](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)。
**裸機還原** | Azure VM 與內部部署 Hypervisor 的主要差異是，Azure 沒有提供 VM 主控台。 在某些情況下，您必須使用主控台，例如使用裸機復原 (BMR) 類型的備份進行復原。 不過，從保存庫來還原 VM 可完整取代 BMR。
**還原具有特殊網路組態的 VM** | 特殊網路組態包含使用內部或外部負載平衡、使用多個 NIC 或多個保留 IP 位址的 VM。 您可以使用[還原磁碟選項](#restore-disks)來還原這些 VM。 此選項會將 Vhd 複製到指定的儲存體帳戶，然後您可以根據您的設定，建立具有[內部](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)或[外部](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell)負載平衡器、[多個 NIC](../virtual-machines/windows/multiple-nics.md)或[多個保留 IP 位址](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)的 VM。
**NIC/子網上的網路安全性群組（NSG）** | Azure VM 備份支援在 vnet、子網和 NIC 層級備份和還原 NSG 資訊。
**區域釘選的 Vm** | 如果您備份的 Azure VM 已釘選到區域（使用 Azure 備份），則您可以在其釘選所在的相同區域中進行還原。 [深入了解](https://docs.microsoft.com/azure/availability-zones/az-overview)
**還原任何可用性設定組中的 VM** | 從入口網站還原 VM 時，沒有選擇可用性設定組的選項。 還原的 VM 不會有可用性設定值組。 如果您使用 [復原磁碟] 選項，則當您使用提供的範本或 PowerShell 從磁片建立 VM 時，可以[指定可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)。
**還原特殊的 Vm，例如 SQL Vm** | 如果您使用 Azure VM 備份來備份 SQL VM，然後在復原磁碟後使用 [還原 VM] 選項或建立 VM，則必須向 SQL 提供者註冊新建立的 VM，如[這裡](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-vm-resource-provider-register?tabs=azure-cli%2Cbash)所述。 這會將已還原的 VM 轉換成 SQL VM。

## <a name="track-the-restore-operation"></a>追蹤還原作業

在觸發還原作業之後，備份服務會建立用於追蹤的作業。 Azure 備份會在入口網站中顯示作業的相關通知。 如果看不到它們，請選取 [**通知**] 符號，然後選取 [**查看所有作業**] 以查看還原程式狀態。

![已觸發還原](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 透過以下方式來追蹤還原：

1. 若要檢視作業的運作，請按一下 [通知] 超連結。 或者，在保存庫中按一下 [備份作業]****，然後按一下相關的 VM。

    ![保存庫中的 VM 清單](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. 若要監視還原進度，請按一下狀態為 [進行中]**** 的任何還原作業。 這會顯示進度列，其中顯示還原進度的相關資訊：

    - **預估的還原時間：一**開始會提供完成還原作業所花費的時間。 作業進行時，所花費的時間會減少，並在還原作業完成時到達零。
    - **還原的百分比**。 顯示已完成的還原作業百分比。
    - 已**傳輸的位元組數**：如果您要藉由建立新的 VM 來進行還原，它會顯示已傳輸的位元組數與要傳輸的位元組總數。

## <a name="post-restore-steps"></a>還原後的步驟

還原 VM 之後有些要注意的事項：

- 系統會安裝出現在備份組態期間的擴充功能，但不會加以啟用。 如果您發現問題，請重新安裝擴充功能。
- 如果備份的 VM 具有靜態 IP 位址，則還原的 VM 會有動態 IP 位址，這是為了避免發生衝突。 您可以[將靜態 IP 位址新增至已還原的 VM](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)。
- 還原的 VM 不會有可用性設定值組。 如果您使用 [復原磁碟] 選項，則當您使用提供的範本或 PowerShell 從磁片建立 VM 時，可以[指定可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)。
- 如果您使用 cloud-init 型 Linux 散發套件 (例如 Ubuntu)，基於安全理由，還原後會封鎖密碼。 請在還原的 VM 上使用 VMAccess 擴充功能[重設密碼](../virtual-machines/linux/reset-password.md)。 我們建議您在這些散發套件中使用 SSH 金鑰，如此一來，您就不需要在還原後重設密碼。
- 如果您因為 VM 與網域控制站的關聯性中斷而無法存取 VM，請遵循下列步驟來啟動 VM：
  - 將 OS 磁片做為資料磁片連結到復原的 VM。
  - 如果找不到 Azure 代理程式，請遵循此[連結](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)，手動安裝 VM 代理程式。
  - 在 VM 上啟用序列主控台存取，以允許對 VM 的命令列存取

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - 重建 VM 時，請使用 Azure 入口網站來重設本機系統管理員帳戶和密碼
  - 使用序列主控台存取和 CMD 從網域退出後 VM

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- 一旦 VM 脫離並重新啟動之後，您就可以使用本機系統管理員認證成功地 RDP 至 VM，並成功將 VM 重新加入網域。

## <a name="backing-up-restored-vms"></a>備份已還原的 VM

- 如果您將 VM 還原至相同的資源群組，並使用與原始備份 VM 相同的名稱，則會在還原後於 VM 上繼續進行備份。
- 如果您將 VM 還原到不同的資源群組，或為還原的 VM 指定了不同的名稱，則您必須為還原的 VM 設定備份。

## <a name="next-steps"></a>後續步驟

- 如果您在還原過程中遇到困難，[請檢閱](backup-azure-vms-troubleshoot.md#restore)常見問題和錯誤。
- 還原 VM 之後，深入了解[管理虛擬機器](backup-azure-manage-vms.md)
