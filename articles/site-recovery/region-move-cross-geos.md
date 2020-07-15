---
title: 使用 Azure Site Recovery 在政府和公用區域之間移動 Azure VM
description: 使用 Azure Site Recovery，在 Azure Government 與公用區域之間移動 Azure VM。
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: d99a5feb344f970b10925b596726520b9dba9464
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134030"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>在 Azure Government 和公用區域之間移動 Azure VM 

您可以在 Azure Government 與公用區域之間移動 IaaS VM，以提高現有 VM 的可用性、改善管理能力，或為了控管理由，如[這裡](azure-to-azure-move-overview.md)所詳述。

除了針對商務持續性和災害復原 (BCDR) 使用 [Azure Site Recovery](site-recovery-overview.md) 服務來管理和協調內部部署電腦和 Azure VM 的災害復原之外，您也可以使用 Site Recovery 來管理將 Azure VM 移到次要區域。       

本教學課程說明如何使用 Azure Site Recovery 在 Azure Government 與公用區域之間移動 Azure VM。 同樣的做法可延伸至在非相同地理叢集內的區域組之間移動 VM。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 驗證必要條件
> * 準備來源 VM
> * 準備目標區域
> * 將資料複製到目標區域
> * 測試組態
> * 執行移動
> * 捨棄來源區域中的資源

> [!IMPORTANT]
> 本教學課程說明如何在 Azure Government 與公用區域之間，或在 Azure VM 的一般災害復原解決方案不支援的區域組之間移動 Azure VM。 假使，您的來源和目標區域組均[受支援](./azure-to-azure-support-matrix.md#region-support)，請參閱此[文件](azure-to-azure-tutorial-migrate.md)中的移動相關資訊。 如果您的需求是要將可用性設定組中的 VM 移到不同區域中固定區域的 VM，藉此改善可用性，請參閱[此處](move-azure-VMs-AVset-Azone.md)的教學課程。

> [!IMPORTANT]
> 不建議使用這個方法來設定不支援區域組之間的 DR，因為區域組已定義成記住資料延遲 (這對 DR 案例很重要)。

## <a name="verify-prerequisites"></a>驗證必要條件

> [!NOTE]
> 請確定您了解此案例的[架構和元件](physical-azure-architecture.md)。 此架構將用來移動 Azure VM，其做法是**將 VM 視為實體伺服器**。

- 檢閱所有元件的[支援需求](vmware-physical-secondary-support-matrix.md)。
- 請確定您想要複寫的伺服器符合 [Azure VM 需求](vmware-physical-secondary-support-matrix.md#replicated-vm-support)。
- 準備帳戶以自動在您要複寫的每個伺服器上安裝行動服務。

- 請注意，在容錯移轉至 Azure 中的目標區域之後，您無法直接執行容錯回復到來源區域。 您必須再次設定複寫回目標。

### <a name="verify-azure-account-permissions"></a>確認 Azure 帳戶權限

請確定您的 Azure 帳戶具有權限將 VM 複寫至 Azure。

- 檢閱您將機器複寫至 Azure 所需的[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。
- 驗證並修改[角色型存取](../role-based-access-control/role-assignments-portal.md)權限。 

### <a name="set-up-an-azure-network"></a>設定 Azure 網路

設定目標 [Azure 網路](../virtual-network/quick-create-portal.md)。

- 在容錯移轉之後建立的 Azure VM 會置於這個網路。
- 此網路應位於與復原服務保存庫相同的區域


### <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

設定 [Azure 儲存體帳戶](../storage/common/storage-account-create.md)。

- Site Recovery 會將內部部署機器複寫至 Azure 儲存體。 容錯移轉發生後，會從儲存體建立 Azure VM。
- 儲存體帳戶與復原服務保存庫必須位於相同的區域。


## <a name="prepare-the-source-vms"></a>準備來源 VM

### <a name="prepare-an-account-for-mobility-service-installation"></a>準備一個用來安裝行動服務的帳戶

行動服務必須安裝在您要複寫的每部伺服器上。 當您啟用伺服器的複寫時，Site Recovery 會自動安裝此服務。 若要自動安裝，您必須準備一個可供 Site Recovery 用來存取伺服器的帳戶。

- 您可以使用網域或本機帳戶
- 若是 Windows VM，如果您不使用網域帳戶，請停用本機電腦上的遠端使用者存取控制。 若要這樣做，請在登錄的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，新增 DWORD 項目 **LocalAccountTokenFilterPolicy**，值為 1。
- 若要從 CLI 新增登錄項目以停用設定，請輸入：``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- 在 Linux 上，帳戶應該是來源 Linux 伺服器上的根使用者。


## <a name="prepare-the-target-region"></a>準備目標區域

1. 確認您的 Azure 訂用帳戶允許您在用於災害復原的目標區域中建立 VM。 請連絡支援人員啟用所需的配額。

2. 確定您的訂用帳戶具有足夠的資源，可支援大小與來源 VM 相符的 VM。 如果您使用 Site Recovery 將資料複製到目標，它將會為目標 VM 挑選相同的大小或最接近的大小。

3. 務必為來源網路面配置中識別的每個元件建立目標資源。 務必要確定在完全移轉至目標區域後，您的 VM 具備您在來源中擁有的所有功能和特性。

    > [!NOTE]
    > 當您啟用來源 VM 的複寫，Azure Site Recovery 會自動探索和建立虛擬網路，您也可以在啟用複寫的使用者流程中，預先建立網路並指派給 VM。 但任何其他資源 (如下所述) 都必須在目標區域中手動建立。

     請參閱下列文件，以根據來源 VM 組態，建立您最常用的相關網路資源。

    - [網路安全性群組](../virtual-network/manage-network-security-group.md)
    - [負載平衡器](../load-balancer/index.yml)
    - [公用 IP](../virtual-network/virtual-network-public-ip-address.md)
    
    如需其他網路元件的資訊，請參閱網路[文件](../index.yml?pivot=products&panel=network)。

4. 如果您想要先測試組態再執行對目標區域的最終完全移轉，請在目標區域中手動[建立非生產網路](../virtual-network/quick-create-portal.md)。 這對生產環境的干擾最少，建議使用此方法。

## <a name="copy-data-to-the-target-region"></a>將資料複製到目標區域
下列步驟將說明如何使用 Azure Site Recovery 將資料複製到目標區域。

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>在來源區域以外的任何區域中建立保存庫。

1. 登入 [Azure 入口網站](https://portal.azure.com) > [復原服務]  。
2. 按一下 [建立資源]   > [管理工具]   > [備份和 Site Recovery]  。
3. 在 [名稱]  中，指定易記名稱 [ContosoVMVault]  。 如果您有多個 訂用帳戶，請選取適當的訂用帳戶。
4. 建立資源群組 **ContosoRG**。
5. 指定 Azure 區域。 若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 在復原服務保存庫中，按一下 [概觀]   > [ConsotoVMVault]   > [+複寫]  。
7. 選取 [至 Azure]   > [未虛擬化/其他]  。

### <a name="set-up-the-configuration-server-to-discover-vms"></a>安裝設定伺服器來探索 VM。


安裝設定伺服器、註冊在保存庫及探索 VM。

1. 按一下 [Site Recovery]   > [準備基礎結構]   > [來源]  。
2. 如果您沒有設定伺服器，請按一下 [+設定伺服器]  。
3. 在 [新增伺服器]  中，檢查 [設定伺服器]  是否出現在 [伺服器類型]  中。
4. 下載 Site Recovery 統一安裝的安裝檔案。
5. 下載保存庫註冊金鑰。 您會在執行統一安裝時用到此金鑰。 該金鑰在產生後會維持 5 天有效。

   ![設定來源](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>在保存庫中註冊設定伺服器

開始之前，請執行下列動作： 

#### <a name="verify-time-accuracy"></a>驗證時間精確度
在組態伺服器機器上，確定系統時鐘會與[時間伺服器](/windows-server/networking/windows-time-service/windows-time-service-top)同步。 應該相符。 如果快慢誤差 15 分鐘，安裝可能會失敗。

#### <a name="verify-connectivity"></a>驗證連線能力
請確定主機可以根據您的環境，存取這些 URL： 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

以 IP 位址為基礎的防火牆規則應該允許透過 HTTPS (443) 連接埠，對以上所列的所有 Azure URL 進行通訊。 為簡化並限制 IP 範圍，建議您執行 URL 篩選。

- **商用 IP**：允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。 允許訂用帳戶 Azure 區域的 IP 位址範圍，以支援 AAD、 備份、複寫和儲存體 URL。  
- **政府機關 IP**：允許 [Azure Government 資料中心 IP 範圍](https://www.microsoft.com/en-us/download/details.aspx?id=57063)以及用於所有 USGov 區域 (維吉尼亞州、德州、亞歷桑那州和愛荷華州) 的 HTTPS (443) 連接埠，以支援 AAD、備份、複寫和儲存體 URL。  

#### <a name="run-setup"></a>執行安裝程式
以本機系統管理員身分執行整合安裝程式，以安裝設定伺服器。 根據預設，處理序伺服器與主要目標伺服器也會安裝在設定伺服器上。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

註冊完成後，設定伺服器會顯示在保存庫的 [設定]   > [伺服器]  頁面上。

### <a name="configure-target-settings-for-replication"></a>設定複寫的目標設定

選取並確認目標資源。

1. 按一下 [準備基礎結構]   > [目標]  ，然後選取您要使用的 Azure 訂用帳戶。
2. 指定目標部署模型。
3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

   ![目標](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>建立複寫原則

1. 若要建立新的複寫原則，請按一下 [Site Recovery 基礎結構]   > [複寫原則]   > [+複寫原則]  。
2. 在 [建立複寫原則]  中，指定原則名稱。
3. 在 [RPO 閾值]  中，指定復原點目標 (RPO) 限制。 這個值指定資料復原點的建立頻率。 連續複寫超過此限制時會產生警示。
4. 在 [復原點保留]  中，指定每個復原點的保留週期長度 (以小時為單位)。 複寫的 VM 可以還原至一個週期內的任何時間點。 複寫至進階儲存體的電腦支援最長保留 24 小時，標準儲存體則是 72 小時。
5. 在 [應用程式一致快照頻率]  中，指定建立包含應用程式一致快照之復原點的頻率 (以分鐘為單位)。 按一下 [確定]  以建立原則。

    ![複寫原則](./media/physical-azure-disaster-recovery/replication-policy.png)


此原則會自動與設定伺服器產生關聯。 依預設會自動建立容錯回復的比對原則。 例如，如果複寫原則是 **rep-policy**，則建立的容錯回復原則是 **rep-policy-failback**。 從 Azure 起始容錯回復時才會使用此原則。

### <a name="enable-replication"></a>啟用複寫

- 啟用複寫時，Site Recovery 會安裝行動服務。
- 啟用伺服器的複寫時，可能需要 15 分鐘或更久的時間，變更才會生效並顯示在入口網站中。

1. 按一下 [複寫應用程式]   > [來源]  。
2. 在 [來源]  中，選取設定伺服器。
3. 在 [機器類型]  中，選取 [實體機器]  。
4. 選取處理序伺服器 (設定伺服器)。 然後按一下 [確定]  。
5. 在 [目標]  中，選取您想要在容錯移轉後，在其中建立 Azure VM 的訂用帳戶和資源群組。 選擇您想要在 Azure (傳統或資源管理) 中使用的部署模型。
6. 選取您要用來複寫資料的 Azure 儲存體帳戶。 
7. 選取 Azure VM 在容錯移轉後啟動時所要建立的 Azure 網路和子網路。
8. 選取 [立即設定選取的機器]  ，將網路設定套用至您選取要進行保護的所有機器。 選取 [稍後設定]  以選取每部機器的 Azure 網路。 
9. 在 [實體機器]  中，按一下 [+實體機器]  。 指定名稱和 IP 位址。 選擇您想要複寫之機器的作業系統。 需要經過幾分鐘才會發現並列出伺服器。 

   > [!WARNING]
   > 您必須輸入所要移動的 Azure VM 的 IP 位址

10. 在 [名稱]   >  [設定屬性]  中，選取處理序伺服器將用來在機器上自動安裝行動服務的帳戶。
11. 在 [複寫設定]   >  [進行複寫設定]  中，確認已選取正確的複寫原則。 
12. 按一下 [啟用複寫]  。 您可以在 [設定] >  [作業] >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。 執行 [完成保護]  作業之後，機器即準備好進行容錯移轉。


若要監視您新增的伺服器，您可以在 [設定伺服器]   > [上次連絡時間]  中查看上次探索伺服器的時間。 若要新增機器而不等候已排定的探索時間，請醒目提示設定伺服器 (不要按一下)，然後按一下 [重新整理]  。

## <a name="test-the-configuration"></a>測試組態


1. 瀏覽至保存庫，在 [設定]   > [複寫的項目]  中按一下您要移至目標區域的虛擬機器，然後按一下 [+測試容錯移轉]  圖示。
2. 在 [測試容錯移轉]  中，選取要用於容錯移轉的復原點：

   - **最近處理**：將 VM 容錯移轉到 Site Recovery 服務所處理的最新復原點。 隨即顯示時間戳記。 使用此選項時，無須花費時間處理資料，因此它會提供低 RTO (復原時間目標)。
   - **最近的應用程式一致**：此選項會將所有 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。
   - **自訂**：選取任何復原點。

3. 選取您要移入 Azure VM 的目標 Azure 虛擬網路，以測試組態。 

   > [!IMPORTANT]
   > 建議您使用個別的 Azure VM 網路來測試容錯移轉，而不要使用您啟用複寫時所設定且最終要移入 VM 的生產網路。

4. 若要開始測試移動，請按一下 [確定]  。 若要追蹤進度，請按一下 VM 開啟其內容。 或者，您也可以按一下保存庫名稱中的 [測試容錯移轉]  作業 > [設定]   > [作業]   > [Site Recovery 作業]  。
5. 容錯移轉完成之後，複本 Azure VM 會出現在 Azure 入口網站> [虛擬機器]  中。 請確定 VM 正在執行中、大小適中，並已連線到適當的網路。
6. 如果您想要刪除測試移動過程中建立的 VM，請在複寫的項目上按一下 [清除測試容錯移轉]  。 在 [記事]  中，記錄並儲存與測試相關聯的任何觀察。

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>執行移至目標區域的作業，並加以確認。

1. 瀏覽至保存庫，在 [設定]   > [複寫的項目]  中按一下虛擬機器，然後按一下 [容錯移轉]  。
2. 在 [容錯移轉]  中，選取 [最新]  。 
3. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)  。 Site Recovery 嘗試先關閉來源 VM，再觸發容錯移轉。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業]  頁面上追蹤容錯移轉進度。 
4. 作業完成後，請確認 VM 如預期出現在目標 Azure 區域中。
5. 在 [複寫的項目]  中，以滑鼠右鍵按一下 VM > [認可]  。 如此，移至目標區域的程序即告完成。 等待認可作業完成。

## <a name="discard-the-resource-in-the-source-region"></a>捨棄來源區域中的資源 

- 瀏覽至 VM。  按一下 [停用複寫]  。  這會停止複製 VM 資料的程序。  

   > [!IMPORTANT]
   > 務必執行此步驟，以避免產生 ASR 複寫費用。

如果您不打算重複使用任何來源資源，請繼續進行下一組步驟。

1. 繼續刪除您在[準備來源 VM](#prepare-the-source-vms) 的步驟 4 中列出的來源區域中包含的所有相關網路資源 
2. 刪除來源區域中對應的儲存體帳戶。



## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure VM 移到不同的 Azure 區域。 您現在可以設定已移動 VM 的災害復原。

> [!div class="nextstepaction"]
> [在移轉之後設定災害復原](azure-to-azure-quickstart.md)
